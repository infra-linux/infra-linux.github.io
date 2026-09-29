---
layout: default
title: Redes
---

<h1 class="page-title">
  <a href="{{ 'redes/index.html' | relative_url }}" data-label="Redes" aria-label="Redes">
    <svg class="title-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
      <rect x="16" y="16" width="6" height="6" rx="1"/>
      <rect x="2" y="16" width="6" height="6" rx="1"/>
      <rect x="9" y="2" width="6" height="6" rx="1"/>
      <path d="M5 16v-3a1 1 0 0 1 1-1h12a1 1 0 0 1 1 1v3"/>
      <path d="M12 12V8"/>
    </svg>
  </a>
  Redes
</h1>

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