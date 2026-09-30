---
layout: default
title: Linux
description: Procedimentos práticos de Linux: DNS, NFS, LVM, certificados SSL e SSH.
---

<h1 class="page-title">
  <svg class="title-icon" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8" stroke-linecap="round" stroke-linejoin="round" aria-hidden="true">
    <polyline points="4 7 9 12 4 17"/>
    <line x1="11" y1="17" x2="20" y2="17"/>
  </svg>
  Linux
</h1>

---

## Introdução

O Linux é um sistema operacional de código aberto amplamente utilizado em servidores, ambientes corporativos, nuvem, containers e dispositivos embarcados.

Sua flexibilidade, estabilidade e segurança fazem dele uma das principais plataformas para administração de infraestrutura e aplicações.

---

## Conteúdo

Os procedimentos estão agrupados por tema. Cada página traz um passo a passo prático, com comandos e validação.

### Rede e DNS

<div class="wiki-topic-list">

  <a class="wiki-topic" href="{{ 'linux/configuracaodns.html' | relative_url }}">
    <span class="wiki-topic-title">DNS com BIND</span>
    <span class="wiki-topic-description">Forwarders corporativos e zona interna.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/ssh-acesso-via-nome.html' | relative_url }}">
    <span class="wiki-topic-title">Acesso a VMs por nome</span>
    <span class="wiki-topic-description">Acessar VMs do VirtualBox pelo nome, em vez do IP.</span>
  </a>

</div>

### Armazenamento

<div class="wiki-topic-list">

  <a class="wiki-topic" href="{{ 'linux/servidor-nfs-rocky-linux-9.html' | relative_url }}">
    <span class="wiki-topic-title">NFS — Servidor</span>
    <span class="wiki-topic-description">Instalação e configuração no Rocky Linux 9.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/cliente-nfs-rocky-linux-9.html' | relative_url }}">
    <span class="wiki-topic-title">NFS — Cliente Linux</span>
    <span class="wiki-topic-description">Montagem de compartilhamentos no Rocky Linux 9.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/cliente-nfs-windows-10-11.html' | relative_url }}">
    <span class="wiki-topic-title">NFS — Cliente Windows</span>
    <span class="wiki-topic-description">Acesso ao NFS a partir do Windows 10/11.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/troubleshooting-nfs.html' | relative_url }}">
    <span class="wiki-topic-title">NFS — Troubleshooting</span>
    <span class="wiki-topic-description">Diagnóstico de problemas em Rocky/Linux e Windows.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/expandir-lvm.html' | relative_url }}">
    <span class="wiki-topic-title">LVM — Adicionar novo disco</span>
    <span class="wiki-topic-description">Expandir volumes com um disco adicional.</span>
  </a>

</div>

### Segurança e acesso

<div class="wiki-topic-list">

  <a class="wiki-topic" href="{{ 'linux/ssh_key.html' | relative_url }}">
    <span class="wiki-topic-title">SSH — Chave pública</span>
    <span class="wiki-topic-description">Configurar autenticação por chave no Linux.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/certificadossl.html' | relative_url }}">
    <span class="wiki-topic-title">Certificado SSL — Harbor</span>
    <span class="wiki-topic-description">Atualização do certificado no Harbor com Docker.</span>
  </a>

  <a class="wiki-topic" href="{{ 'linux/atualizarSSLwiki.html' | relative_url }}">
    <span class="wiki-topic-title">Certificado SSL — WikiJS</span>
    <span class="wiki-topic-description">Atualização do certificado no WikiJS com Docker e Nginx.</span>
  </a>

</div>

---

> Esta seção reúne conceitos, comandos e procedimentos práticos relacionados ao Linux.