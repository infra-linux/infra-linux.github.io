---
layout: default
title: Troubleshooting Wiki da Empresa Exemplo
---

# Troubleshooting Completo – Wiki da Empresa Exemplo Indisponível (Docker, WikiJS, Nginx e Certificados)


## Sumário

1. [Objetivo](#objetivo)
2. [Ambiente](#ambiente)
3. [Sintoma Inicial](#sintoma-inicial)
4. [Etapa 1 – Validar DNS](#etapa-1-validar-dns)
5. [Conclusão](#conclusao)
6. [Etapa 2 – Validar conectividade HTTPS](#etapa-2-validar-conectividade-https)
7. [Conclusão](#conclusao)
8. [Etapa 3 – Acessar o servidor da Wiki](#etapa-3-acessar-o-servidor-da-wiki)
9. [Conclusão](#conclusao)
10. [Etapa 4 – Verificar portas abertas](#etapa-4-verificar-portas-abertas)
11. [Conclusão](#conclusao)
12. [Etapa 5 – Verificar serviços com falha](#etapa-5-verificar-servicos-com-falha)
13. [Conclusão](#conclusao)
14. [Etapa 6 – Verificar status do Docker](#etapa-6-verificar-status-do-docker)
15. [Conclusão](#conclusao)
16. [Etapa 7 – Verificar módulos do kernel](#etapa-7-verificar-modulos-do-kernel)
17. [Conclusão](#conclusao)
18. [Etapa 8 – Verificar kernel instalado](#etapa-8-verificar-kernel-instalado)
19. [Conclusão](#conclusao)
20. [Etapa 9 – Reconstruir dependências dos módulos](#etapa-9-reconstruir-dependencias-dos-modulos)
21. [Conclusão](#conclusao)
22. [Etapa 10 – Carregar módulos necessários](#etapa-10-carregar-modulos-necessarios)
23. [Conclusão](#conclusao)
24. [Etapa 11 – Reiniciar Docker](#etapa-11-reiniciar-docker)
25. [Conclusão](#conclusao)
26. [Etapa 12 – Verificar containers](#etapa-12-verificar-containers)
27. [Conclusão](#conclusao)
28. [Etapa 13 – Validar aplicação](#etapa-13-validar-aplicacao)
29. [Conclusão](#conclusao)

---
## Objetivo

Documentar o processo de análise, diagnóstico e correção da indisponibilidade da Wiki da Empresa Exemplo.

---

# Ambiente

Servidor:

```bash
srv-wiki01.corp.example.com
```

Aplicação:

```bash
WikiJS
```

Containers:

```bash
wikijs_nginx
wikijs
wikijs_db
```

Serviços:

```text
Nginx
WikiJS
PostgreSQL
Docker
```

---

# Sintoma Inicial

Usuários reportaram indisponibilidade da Wiki:

```text
https://wiki.corp.example.com
```

Teste realizado:

```bash
curl -I https://wiki.corp.example.com
```

Resultado:

```text
curl: (7) Failed to connect
```

Também foi observado:

```text
Connection refused
```

---

# Etapa 1 – Validar DNS

Executar:

```bash
nslookup wiki.corp.example.com
```

Resultado:

```text
Servidor: dns01.corp.example.com
Address: 192.0.2.53

Nome: wiki.corp.example.com
Address: 192.0.2.200
```

### Conclusão

DNS funcionando corretamente.

---

# Etapa 2 – Validar conectividade HTTPS

Executar:

```bash
curl -I https://wiki.corp.example.com
```

Resultado:

```text
curl: (7) Failed to connect
```

Executar:

```bash
telnet wiki.corp.example.com 443
```

Resultado:

```text
Connection refused
```

### Conclusão

A porta 443 não estava disponível.

---

# Etapa 3 – Acessar o servidor da Wiki

Conectar ao servidor:

```bash
ssh admin@srv-wiki01
```

Verificar acesso local:

```bash
curl -I https://localhost
```

Resultado:

```text
curl: (7) Failed to connect to localhost port 443
```

### Conclusão

O problema estava no próprio servidor.

---

# Etapa 4 – Verificar portas abertas

Executar:

```bash
ss -tulnp | grep :443
```

Resultado:

```text
Sem retorno
```

### Conclusão

Nenhum serviço escutando na porta 443.

---

# Etapa 5 – Verificar serviços com falha

Executar:

```bash
systemctl --failed
```

Resultado:

```text
docker.service failed
docker.socket failed
```

### Conclusão

O Docker estava indisponível.

---

# Etapa 6 – Verificar status do Docker

Executar:

```bash
systemctl status docker -l
```

Foi identificado erro relacionado à criação das regras de rede.

Também foi observado:

```bash
nft --version
```

Resultado:

```text
Unable to initialize Netlink socket
Protocol not supported
```

### Conclusão

Problema relacionado aos módulos de rede do kernel.

---

# Etapa 7 – Verificar módulos do kernel

Executar:

```bash
modprobe nf_tables
```

Resultado:

```text
FATAL: Module nf_tables not found
```

Também:

```bash
modprobe overlay
modprobe ip_tables
modprobe iptable_nat
modprobe br_netfilter
```

Resultado:

```text
Module not found
```

### Conclusão

Os módulos necessários para o funcionamento do Docker não estavam carregados.

---

# Etapa 8 – Verificar kernel instalado

Executar:

```bash
uname -r
```

Resultado:

```text
5.14.0-000.00.0.el9_0.x86_64
```

Verificar pacotes:

```bash
rpm -qa | grep kernel | sort
```

Resultado:

```text
kernel
kernel-core
kernel-modules
kernel-modules-core
```

### Conclusão

Os pacotes estavam instalados.

---

# Etapa 9 – Reconstruir dependências dos módulos

Executar:

```bash
depmod -a
```

Aguardar finalização.

Verificar retorno:

```bash
echo $?
```

Resultado:

```text
0
```

### Conclusão

Índices dos módulos reconstruídos.

---

# Etapa 10 – Carregar módulos necessários

Executar:

```bash
modprobe overlay
modprobe nf_tables
modprobe ip_tables
modprobe iptable_nat
modprobe br_netfilter
```

Validar:

```bash
echo $?
```

Resultado:

```text
0
```

### Conclusão

Módulos carregados com sucesso.

---

# Etapa 11 – Reiniciar Docker

Executar:

```bash
systemctl reset-failed docker
systemctl restart docker
```

Verificar:

```bash
systemctl status docker
```

Resultado:

```text
Active: active (running)
```

Também foi possível observar:

```text
docker-proxy
0.0.0.0:80
0.0.0.0:443
```

### Conclusão

Docker restaurado.

---

# Etapa 12 – Verificar containers

Executar:

```bash
docker ps
```

Resultado:

```text
wikijs_nginx
wikijs
wikijs_db
```

### Conclusão

Containers iniciados automaticamente.

---

# Etapa 13 – Validar aplicação

Executar:

```bash
curl -kI https://localhost
```

Resultado:

```text
HTTP/1.1 200 OK
```

Executar:

```bash
curl -kI https://wiki.corp.example.com
```

Resultado:

```text
HTTP/1.1 200 OK
```

### Conclusão

Wiki restaurada.

---
