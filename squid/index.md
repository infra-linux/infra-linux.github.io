---
layout: default
title: Squid
---
# Squid


## Sumário

1. [Introdução](#introducao)
2. [Conteúdo](#conteúdo)
3. [Arquivos importantes](#arquivos-importantes)
4. [Comandos de consulta rápida](#comandos-de-consulta-rapida)
5. [Boas práticas](#boas-praticas)

---


## Introdução

Squid é um servidor proxy utilizado para controlar, registrar e otimizar o acesso HTTP/HTTPS em ambientes corporativos.

Ele pode ser usado para aplicar ACLs, autenticação, filtros de acesso, cache e auditoria de navegação.

---
## Conteúdo

* instalação do Squid
* configuração de proxy
* ACLs
* autenticação
* logs
* liberação e bloqueio de destinos
* troubleshooting de acesso

---
## Arquivos importantes

```bash
/etc/squid/squid.conf
/var/log/squid/access.log
/var/log/squid/cache.log
```

---
## Comandos de consulta rápida

```bash
systemctl status squid
systemctl restart squid
squid -k parse
tail -f /var/log/squid/access.log
tail -f /var/log/squid/cache.log
```

---
## Boas práticas

* Valide a configuração com `squid -k parse` antes de reiniciar.
* Faça backup do `squid.conf` antes de alterações grandes.
* Documente ACLs e regras de exceção.
* Monitore logs durante testes de liberação.
* Separe regras por finalidade para facilitar manutenção.

---
> Esta seção centraliza procedimentos de configuração, análise de logs e troubleshooting do Squid Proxy.
