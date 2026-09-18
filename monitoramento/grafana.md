---
layout: default
title: Instalação do Grafana no Rocky Linux 9
---

# Instalação do Grafana no Rocky Linux 9


## Sumário

1. [Objetivo](#objetivo)
2. [Ambiente](#ambiente)
3. [Adicionando o repositório oficial](#adicionando-o-repositorio-oficial)
4. [Instalando o Grafana](#instalando-o-grafana)
5. [Habilitando o serviço](#habilitando-o-servico)
6. [Verificando a porta de escuta](#verificando-a-porta-de-escuta)
7. [Configurando o Firewall](#configurando-o-firewall)
8. [Obtendo o IP do servidor](#obtendo-o-ip-do-servidor)
9. [Acessando o Grafana](#acessando-o-grafana)
10. [Credenciais padrão](#credenciais-padrao)
11. [Comandos úteis](#comandos-uteis)
12. [Verificar status](#verificar-status)
13. [Reiniciar serviço](#reiniciar-servico)
14. [Parar serviço](#parar-servico)
15. [Iniciar serviço](#iniciar-servico)
16. [Ver logs](#ver-logs)
17. [Troubleshooting](#troubleshooting)
18. [Serviço não inicia](#servico-nao-inicia)
19. [Porta 3000 não responde](#porta-3000-nao-responde)
20. [Acesso recusado pelo navegador](#acesso-recusado-pelo-navegador)
21. [Resultado esperado](#resultado-esperado)

---
## Objetivo

Instalar o Grafana em um servidor Rocky Linux 9 para criação de dashboards e visualização de métricas.

---

## Ambiente

* Sistema Operacional: Rocky Linux 9
* Grafana: 13.x
* Porta padrão: 3000/TCP

---

## Adicionando o repositório oficial

Criar o arquivo de repositório:

```bash
cat > /etc/yum.repos.d/grafana.repo << 'EOF'
[grafana]
name=grafana
baseurl=https://rpm.grafana.com
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://rpm.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
EOF
```

Validar o repositório:

```bash
dnf repolist | grep grafana
```

Saída esperada:

```text
grafana              grafana
```

---

## Instalando o Grafana

Instalar o pacote:

```bash
dnf install grafana -y
```

Verificar a versão:

```bash
grafana-server -v
```

Exemplo:

```text
Version 13.0.2
```

---

## Habilitando o serviço

Iniciar o Grafana e configurar inicialização automática:

```bash
systemctl enable --now grafana-server
```

Verificar o status:

```bash
systemctl status grafana-server
```

Ou:

```bash
systemctl is-active grafana-server
```

Saída esperada:

```text
active
```

---

## Verificando a porta de escuta

O Grafana utiliza a porta TCP 3000 por padrão.

Verificar:

```bash
ss -tlnp | grep 3000
```

Exemplo:

```text
LISTEN 0 4096 *:3000 *:*
```

---

## Configurando o Firewall

Verificar se o Firewalld está ativo:

```bash
systemctl is-active firewalld
```

Liberar a porta 3000:

```bash
firewall-cmd --permanent --add-port=3000/tcp
```

Aplicar as alterações:

```bash
firewall-cmd --reload
```

Validar:

```bash
firewall-cmd --list-ports
```

Exemplo:

```text
3000/tcp
```

---

## Obtendo o IP do servidor

```bash
hostname -I
```

Ou:

```bash
ip -4 addr show
```

Exemplo:

```text
192.0.2.10
```

---

## Acessando o Grafana

Abrir no navegador:

```text
http://IP_DO_SERVIDOR:3000
```

Exemplo:

```text
http://192.0.2.10:3000
```

---

## Credenciais padrão

Primeiro acesso:

```text
Usuário: admin
Senha: admin
```

Após o login, o Grafana solicitará a alteração da senha.

---

## Comandos úteis

### Verificar status

```bash
systemctl status grafana-server
```

### Reiniciar serviço

```bash
systemctl restart grafana-server
```

### Parar serviço

```bash
systemctl stop grafana-server
```

### Iniciar serviço

```bash
systemctl start grafana-server
```

### Ver logs

```bash
journalctl -u grafana-server -f
```

---

## Troubleshooting

### Serviço não inicia

Verificar logs:

```bash
journalctl -xeu grafana-server
```

---

### Porta 3000 não responde

Verificar escuta:

```bash
ss -tlnp | grep 3000
```

Verificar firewall:

```bash
firewall-cmd --list-ports
```

---

### Acesso recusado pelo navegador

Verificar se o serviço está ativo:

```bash
systemctl is-active grafana-server
```

Verificar conectividade de rede:

```bash
ping IP_DO_SERVIDOR
```

---

## Resultado esperado

Ao final da instalação, o Grafana estará acessível através do navegador na porta 3000:

```text
http://IP_DO_SERVIDOR:3000
```

Pronto para integração com Zabbix, Prometheus, InfluxDB, PostgreSQL, MySQL e outras fontes de dados.
