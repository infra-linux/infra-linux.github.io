---
layout: default
title: VMware
---
# VMware
{:.no_toc}

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---


## Introdução

Esta seção reúne procedimentos, anotações e boas práticas para administração de ambientes VMware, com foco em ESXi, instalação de hypervisors, configuração inicial, storage local e suporte a hosts físicos.

---
## Conteúdo

* [Instalação do VMware ESXi 7.0.3 em Dell PowerEdge R830 via iDRAC](instalacao-esxi-7-dell-r830-idrac.md)

---
## Temas da seção

* VMware ESXi
* instalação de hypervisor
* iDRAC
* RAID em servidores Dell
* PERC H730
* datastore local
* configuração de rede de gerenciamento
* troubleshooting de boot e instalação

---
## Checklist de operação

* Validar acesso ao iDRAC antes da intervenção.
* Confirmar janela de manutenção e autorização para apagar discos.
* Conferir configuração RAID antes da instalação.
* Instalar o ESXi no volume correto.
* Desconectar a mídia virtual após a instalação.
* Validar boot do host pelo ESXi.
* Registrar dados de rede, hostname, datastore e credenciais conforme o padrão do ambiente.

---
> Esta seção centraliza procedimentos práticos para instalação, administração e suporte de hosts VMware ESXi.
