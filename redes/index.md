---
layout: default
title: Redes
---

<h1 class="page-title">
  <svg class="title-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
    <circle cx="12" cy="5" r="2"/>
    <circle cx="5" cy="19" r="2"/>
    <circle cx="19" cy="19" r="2"/>
    <line x1="12" y1="7" x2="5" y2="17"/>
    <line x1="12" y1="7" x2="19" y2="17"/>
  </svg>
  Redes

</h1>

---

## Introdução

Redes são a base da comunicação entre servidores, estações, aplicações, containers, clusters e serviços corporativos.

Esta seção reúne conceitos, comandos e procedimentos relacionados a conectividade, DNS, portas, rotas, proxy e troubleshooting de comunicação.

---
## Conteúdo

* [DNS](dns/index.md)
* [TCP/IP](tcpip.md)
* [Portas e Protocolos](portaseprotocolos.md)
* rotas
* firewall
* proxy
* testes de conectividade
* análise de indisponibilidade

---
## Comandos de consulta rápida

```bash
ping destino
nslookup dominio.com.br
tracert destino
netstat -ano
curl -vk https://dominio.com.br
telnet host porta
```

No Linux:

```bash
ip addr
ip route
ss -tulnp
dig dominio.com.br
traceroute destino
```

---
## Boas práticas

* Valide DNS antes de investigar aplicação.
* Confirme porta e protocolo usados pelo serviço.
* Teste conectividade a partir da origem correta.
* Documente IPs, VLANs, rotas e regras de firewall.
* Diferencie problema de rede, proxy, DNS e aplicação.

---
> Esta seção serve como base para diagnósticos de conectividade em ambientes Linux, Windows, containers e Kubernetes.