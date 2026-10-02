---
layout: default
title: Autenticação de usuários no Squid
---

# Autenticação de usuários no Squid
{:.no_toc}

Este tutorial mostra como exigir usuário e senha no Squid usando autenticação básica com arquivo local e, como alternativa, com LDAP.

Pré-requisito: Squid instalado e funcionando. Veja [Instalação e configuração básica]({{ 'squid/instalacao-configuracao-squid.html' | relative_url }}).

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---

## Como funciona

Quando a autenticação está ativa, o Squid responde `407 Proxy Authentication Required` ao cliente. O navegador pede usuário e senha, e o Squid valida as credenciais com um **helper** (programa auxiliar) antes de liberar o acesso.

> Na autenticação básica, usuário e senha trafegam apenas codificados em Base64, sem criptografia. Use-a em redes confiáveis ou combine com outros controles.

---

## Autenticação com arquivo local (NCSA)

### Instalar o utilitário htpasswd

```bash
dnf install -y httpd-tools
```

### Criar o arquivo de usuários

Criar o arquivo com o primeiro usuário (a opção `-c` cria o arquivo):

```bash
htpasswd -c /etc/squid/passwd usuario1
```

Adicionar outros usuários (sem `-c`, para não sobrescrever o arquivo):

```bash
htpasswd /etc/squid/passwd usuario2
```

Ajustar as permissões para que apenas o Squid consiga ler:

```bash
chown root:squid /etc/squid/passwd
chmod 640 /etc/squid/passwd
```

### Conferir o caminho do helper

```bash
ls /usr/lib64/squid/basic_ncsa_auth
```

### Configurar o Squid

Editar:

```bash
vi /etc/squid/squid.conf
```

Adicionar, antes das regras `http_access allow`:

```text
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy Pangolim
auth_param basic credentialsttl 2 hours

acl usuarios_autenticados proxy_auth REQUIRED
```

Na área de regras, libere apenas usuários autenticados:

```text
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports

http_access allow usuarios_autenticados

http_access deny all
```

Para exigir autenticação **e** rede de origem ao mesmo tempo:

```text
http_access allow rede_interna usuarios_autenticados
```

### Aplicar a configuração

```bash
squid -k parse
squid -k reconfigure
```

### Testar

Sem credenciais, o proxy deve responder `407`:

```bash
curl -x http://IP_DO_SERVIDOR:3128 -I https://www.example.com
```

Com credenciais:

```bash
curl -x http://IP_DO_SERVIDOR:3128 -U usuario1:SENHA -I https://www.example.com
```

---

## Liberar usuários específicos

Para restringir o acesso a uma lista de usuários, em vez de qualquer usuário válido:

```bash
vi /etc/squid/usuarios_liberados.txt
```

```text
usuario1
usuario2
```

No `squid.conf`:

```text
acl usuarios_liberados proxy_auth "/etc/squid/usuarios_liberados.txt"
http_access allow usuarios_liberados
```

---

## Autenticação com LDAP (alternativa)

Para validar os usuários em um diretório LDAP ou Active Directory, use o helper `basic_ldap_auth` no lugar do `basic_ncsa_auth`.

Guarde a senha da conta de consulta em um arquivo protegido:

```bash
echo -n 'SENHA_DA_CONTA' > /etc/squid/ldap_senha
chown root:squid /etc/squid/ldap_senha
chmod 640 /etc/squid/ldap_senha
```

Exemplo de configuração, ajustando domínio, servidor e contas ao seu ambiente:

```text
auth_param basic program /usr/lib64/squid/basic_ldap_auth -R \
  -b "dc=exemplo,dc=local" \
  -D "cn=svc-squid,ou=Servicos,dc=exemplo,dc=local" \
  -W /etc/squid/ldap_senha \
  -f "sAMAccountName=%s" \
  -h dc01.exemplo.local

auth_param basic children 5
auth_param basic realm Proxy Pangolim
auth_param basic credentialsttl 2 hours

acl usuarios_autenticados proxy_auth REQUIRED
```

| Parâmetro | Função |
|---|---|
| `-b` | Base de busca no diretório |
| `-D` | Conta usada para consultar o diretório |
| `-W` | Arquivo com a senha dessa conta |
| `-f` | Filtro de busca; `%s` é substituído pelo usuário digitado |
| `-h` | Servidor LDAP |
| `-R` | Não seguir referrals |

Para testar o helper fora do Squid, execute-o e digite `usuario senha` em uma linha:

```bash
/usr/lib64/squid/basic_ldap_auth -R -b "dc=exemplo,dc=local" -D "cn=svc-squid,ou=Servicos,dc=exemplo,dc=local" -W /etc/squid/ldap_senha -f "sAMAccountName=%s" -h dc01.exemplo.local
```

A resposta `OK` indica credenciais válidas e `ERR` indica falha.

---

## Verificações úteis

Ver o usuário registrado nos acessos (a coluna do usuário no `access.log` mostra o nome autenticado):

```bash
tail -f /var/log/squid/access.log
```

Procurar falhas de autenticação:

```bash
grep TCP_DENIED/407 /var/log/squid/access.log | tail
```

Ver erros do helper e do serviço:

```bash
tail -f /var/log/squid/cache.log
```

---

## Problemas comuns

| Sintoma | Causa provável | O que verificar |
|---|---|---|
| O navegador pede senha repetidamente | Usuário ou senha incorretos | `htpasswd` e teste com `curl -U` |
| Erro `helper ... crashed` no `cache.log` | Caminho do helper errado ou sem permissão no arquivo de senhas | Caminho do helper, dono e modo do arquivo |
| A autenticação não é solicitada | Existe um `allow` sem `proxy_auth` antes da regra autenticada | Ordem das linhas `http_access` |
| Alteração de senha não vale de imediato | O Squid mantém as credenciais em cache | Parâmetro `credentialsttl` ou `squid -k reconfigure` |
| LDAP retorna `ERR` | Base, filtro ou conta de consulta incorretos | Teste do helper pela linha de comando |