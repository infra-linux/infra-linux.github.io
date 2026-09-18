---
layout: default
title: Instalação do Zabbix Server com PostgreSQL no Rocky Linux 9
---

# Instalação do Zabbix Server com PostgreSQL no Rocky Linux 9

Este tutorial mostra uma instalação básica do Zabbix Server utilizando PostgreSQL como banco de dados.


## Sumário

1. [Adicionar o repositório do Zabbix](#adicionar-o-repositorio-do-zabbix)
2. [Instalar os pacotes](#instalar-os-pacotes)
3. [Instalar PostgreSQL](#instalar-postgresql)
4. [Criar banco de dados](#criar-banco-de-dados)
5. [Importar o schema do Zabbix](#importar-o-schema-do-zabbix)
6. [Configurar o Zabbix Server](#configurar-o-zabbix-server)
7. [Iniciar serviços](#iniciar-servicos)
8. [Liberar firewall](#liberar-firewall)
9. [Acessar interface Web](#acessar-interface-web)
10. [Login padrão](#login-padrao)
11. [Verificações úteis](#verificacoes-uteis)
12. [Serviços principais](#servicos-principais)
13. [Instalar Zabbix Agent no Linux](#instalar-zabbix-agent-no-linux)
14. [Instalar Zabbix Agent no Windows](#instalar-zabbix-agent-no-windows)

---
## Adicionar o repositório do Zabbix

```bash
rpm -Uvh https://repo.zabbix.com/zabbix/7.0/rhel/9/x86_64/zabbix-release-latest-7.0.el9.noarch.rpm

dnf clean all
```

---

## Instalar os pacotes

```bash
dnf install -y \
zabbix-server-pgsql \
zabbix-web-pgsql \
zabbix-nginx-conf \
zabbix-sql-scripts \
zabbix-selinux-policy \
zabbix-agent
```

---

## Instalar PostgreSQL

```bash
dnf install -y postgresql-server
```

Inicializar o banco:

```bash
postgresql-setup --initdb
```

Habilitar o serviço:

```bash
systemctl enable --now postgresql
```

Verificar:

```bash
systemctl status postgresql
```

---

## Criar banco de dados

Acessar o PostgreSQL:

```bash
sudo -u postgres psql
```

Criar usuário:

```sql
CREATE USER zabbix WITH PASSWORD 'senha_forte';
```

Criar banco:

```sql
CREATE DATABASE zabbix OWNER zabbix;
```

Conceder permissões:

```sql
GRANT ALL PRIVILEGES ON DATABASE zabbix TO zabbix;
```

Sair:

```sql
\q
```

---

## Importar o schema do Zabbix

```bash
zcat /usr/share/zabbix/sql-scripts/postgresql/server.sql.gz | \
PGPASSWORD='senha_forte' psql -U zabbix -d zabbix
```

A importação pode levar alguns minutos.

---

## Configurar o Zabbix Server

Editar:

```bash
vi /etc/zabbix/zabbix_server.conf
```

Localizar:

```text
DBPassword=
```

Alterar:

```text
DBPassword=senha_forte
```

Salvar o arquivo.

---

## Iniciar serviços

```bash
systemctl enable --now zabbix-server
systemctl enable --now zabbix-agent
systemctl enable --now nginx
systemctl enable --now php-fpm
```

Verificar:

```bash
systemctl status zabbix-server
```

---

## Liberar firewall

```bash
firewall-cmd --permanent --add-service=http

firewall-cmd --reload
```

---

## Acessar interface Web

Abrir no navegador:

```text
http://IP_DO_SERVIDOR
```

Exemplo:

```text
http://192.0.2.35
```

---

## Login padrão

```text
Usuário: Admin
Senha: zabbix
```

---

## Verificações úteis

Verificar logs do servidor:

```bash
tail -f /var/log/zabbix/zabbix_server.log
```

Verificar porta:

```bash
ss -tulpn | grep 10051
```

Verificar banco:

```bash
sudo -u postgres psql
```

---

## Serviços principais

```bash
systemctl status zabbix-server
systemctl status zabbix-agent
systemctl status postgresql
systemctl status nginx
systemctl status php-fpm
```

---

## Instalar Zabbix Agent no Linux

Esta seção instala o Zabbix Agent 2 em um servidor Rocky Linux 9 ou outra distribuição compatível com pacotes RPM.

### Adicionar o repositório

Substitua `7.0` pela versão utilizada pelo seu Zabbix Server, se necessário:

```bash
rpm -Uvh https://repo.zabbix.com/zabbix/7.0/rhel/9/x86_64/zabbix-release-latest-7.0.el9.noarch.rpm
dnf clean all
```

### Instalar o agente

```bash
dnf install -y zabbix-agent2
```

### Configurar o agente

Edite o arquivo de configuração:

```bash
vi /etc/zabbix/zabbix_agent2.conf
```

Configure pelo menos os parâmetros abaixo. Use o endereço IP ou DNS do Zabbix Server:

```text
Server=IP_DO_ZABBIX_SERVER
ServerActive=IP_DO_ZABBIX_SERVER
Hostname=nome-do-host-linux
```

O valor de `Hostname` deve ser exatamente igual ao nome cadastrado no frontend do Zabbix.

### Iniciar e habilitar o serviço

```bash
systemctl enable --now zabbix-agent2
systemctl status zabbix-agent2
```

### Liberar a porta do agente

Para monitoramento passivo, libere a porta TCP `10050`:

```bash
firewall-cmd --permanent --add-port=10050/tcp
firewall-cmd --reload
```

### Validar a comunicação

No Zabbix Server, teste a porta do agente:

```bash
nc -zv IP_DO_HOST_LINUX 10050
```

Verifique também os logs no host monitorado:

```bash
journalctl -u zabbix-agent2 -n 50 --no-pager
```

No frontend do Zabbix, crie ou abra o host e confirme:

- nome do host igual ao parâmetro `Hostname`;
- interface do tipo Agent apontando para o IP do Linux;
- template compatível com a versão do agente;
- status do host como disponível.

---

## Instalar Zabbix Agent no Windows

Esta seção instala o Zabbix Agent 2 em uma máquina Windows utilizando o pacote MSI oficial.

### Baixar o instalador

Baixe o instalador correspondente à arquitetura do Windows na página oficial:

```text
https://www.zabbix.com/download_agents
```

Escolha:

- versão compatível com o Zabbix Server;
- Windows;
- arquitetura x86_64;
- Zabbix Agent 2;
- pacote MSI.

Salve o arquivo, por exemplo, em:

```text
C:\Temp\zabbix_agent2.msi
```

### Instalar pelo PowerShell

Abra o PowerShell como Administrador e execute:

```powershell
msiexec.exe /i C:\Temp\zabbix_agent2.msi /qn `
	SERVER=IP_DO_ZABBIX_SERVER `
	SERVERACTIVE=IP_DO_ZABBIX_SERVER `
	HOSTNAME=nome-do-host-windows `
	/l*v C:\Temp\zabbix-agent2-install.log
```

Se a instalação silenciosa não for desejada, abra o arquivo MSI pelo Explorer e informe os mesmos parâmetros no assistente.

### Conferir a configuração

O arquivo normalmente fica em:

```text
C:\Program Files\Zabbix Agent 2\zabbix_agent2.conf
```

Confira se os parâmetros principais estão corretos:

```text
Server=IP_DO_ZABBIX_SERVER
ServerActive=IP_DO_ZABBIX_SERVER
Hostname=nome-do-host-windows
```

### Verificar o serviço

No PowerShell como Administrador:

```powershell
Get-Service -Name "Zabbix Agent 2"
Start-Service -Name "Zabbix Agent 2"
Set-Service -Name "Zabbix Agent 2" -StartupType Automatic
```

### Liberar o firewall do Windows

Para permitir verificações passivas do Zabbix Server:

```powershell
New-NetFirewallRule `
	-DisplayName "Zabbix Agent 2" `
	-Direction Inbound `
	-Protocol TCP `
	-LocalPort 10050 `
	-Action Allow
```

### Validar a comunicação

No Zabbix Server, teste a porta do host Windows:

```bash
nc -zv IP_DO_HOST_WINDOWS 10050
```

No Windows, consulte eventos e logs do agente. O arquivo de log depende da configuração usada, mas pode ser configurado no `zabbix_agent2.conf` com:

```text
LogFile=C:\Program Files\Zabbix Agent 2\zabbix_agent2.log
```

Depois de alterar a configuração, reinicie o serviço:

```powershell
Restart-Service -Name "Zabbix Agent 2"
```

No frontend do Zabbix, cadastre o host Windows usando o mesmo valor definido em `Hostname`, informe o IP correto e associe um template Windows compatível.
