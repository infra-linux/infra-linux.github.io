---
layout: default
title: Squid
---

<h1 class="page-title">
        <svg class="title-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true"><path d="M16 3h5v5"/><path d="M4 20 21 3"/><path d="M21 16v5h-5"/><path d="m15 15 6 6"/><path d="m4 4 5 5"/>
  </svg>
  Squid

</h1>

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
