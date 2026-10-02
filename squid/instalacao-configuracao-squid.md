---
layout: default
title: Instalação e configuração básica do Squid no Rocky Linux 9
---

# Instalação e configuração básica do Squid no Rocky Linux 9
{:.no_toc}

Este tutorial mostra a instalação do Squid como proxy HTTP/HTTPS e uma configuração básica que libera o acesso apenas para a rede interna.

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---

## Instalar o Squid

```bash
dnf install -y squid
```

Verificar a versão instalada:

```bash
squid -v
```

---

## Fazer backup da configuração original

```bash
cp /etc/squid/squid.conf /etc/squid/squid.conf.bkp
```

---

## Configurar o Squid

Editar:

```bash
vi /etc/squid/squid.conf
```

### Definir a rede liberada

Adicione uma ACL com a rede interna que poderá usar o proxy. Ela deve ficar **antes** da linha `http_access deny all`:

```text
acl rede_interna src 192.0.2.0/24
http_access allow rede_interna
```

Substitua `192.0.2.0/24` pela rede utilizada no seu ambiente.

### Conferir a ordem das regras

As regras `http_access` são avaliadas de cima para baixo e a primeira que corresponder é aplicada. A ordem deve ficar assim:

```text
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports
http_access allow localhost manager
http_access deny manager

http_access allow rede_interna
http_access allow localhost
http_access deny all
```

### Definir a porta

A porta padrão do Squid é a `3128/TCP`:

```text
http_port 3128
```

### Definir o nome do servidor (opcional)

```text
visible_hostname proxy.exemplo.local
```

### Configurar o cache em disco (opcional)

Para habilitar o cache em disco, descomente ou adicione:

```text
cache_dir ufs /var/spool/squid 1000 16 256
```

Os valores indicam o diretório, o tamanho em MB, o número de diretórios de primeiro nível e o de segundo nível.

Se for usar o cache em disco, inicialize a estrutura de diretórios:

```bash
squid -z
```

---

## Validar a configuração

```bash
squid -k parse
```

Se houver erro de sintaxe, o comando mostra o arquivo e a linha com problema.

---

## Iniciar o serviço

```bash
systemctl enable --now squid
```

Verificar:

```bash
systemctl status squid
```

---

## Liberar o firewall

```bash
firewall-cmd --permanent --add-port=3128/tcp

firewall-cmd --reload
```

---

## SELinux

A porta `3128/TCP` já é permitida para o Squid pelo SELinux. Se você usar outra porta, como a `8080`, registre-a:

```bash
dnf install -y policycoreutils-python-utils

semanage port -a -t squid_port_t -p tcp 8080
```

Para conferir as portas liberadas:

```bash
semanage port -l | grep squid
```

---

## Testar o proxy

A partir de uma máquina da rede liberada:

```bash
curl -x http://IP_DO_SERVIDOR:3128 -I https://www.example.com
```

Uma resposta `HTTP/1.1 200 Connection established` seguida do cabeçalho do site indica que o proxy está funcionando.

---

## Configurar os clientes

### Linux

Para a sessão atual do terminal:

```bash
export http_proxy=http://IP_DO_SERVIDOR:3128
export https_proxy=http://IP_DO_SERVIDOR:3128
```

### Windows

Em **Configurações > Rede e Internet > Proxy**, habilite o proxy manual e informe:

```text
Endereço: IP_DO_SERVIDOR
Porta: 3128
```

---

## Verificações úteis

Acompanhar os acessos em tempo real:

```bash
tail -f /var/log/squid/access.log
```

Acompanhar os logs do serviço:

```bash
tail -f /var/log/squid/cache.log
```

Verificar a porta:

```bash
ss -tulpn | grep 3128
```

Aplicar alterações na configuração sem derrubar o serviço:

```bash
squid -k reconfigure
```

---

## Problemas comuns

| Sintoma | Causa provável | O que verificar |
|---|---|---|
| `TCP_DENIED/403` no `access.log` | A origem não está liberada por ACL | ACL `rede_interna` e a ordem das regras `http_access` |
| O cliente não conecta | Porta bloqueada | Firewall do servidor e regras de rede entre cliente e proxy |
| O serviço não inicia | Erro de sintaxe ou porta em uso | `squid -k parse` e `journalctl -u squid -n 50 --no-pager` |
| O serviço não sobe com porta diferente | SELinux bloqueando a porta | `semanage port -l \| grep squid` |