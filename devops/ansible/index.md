---
layout: default
title: Ansible
---

# Ansible
{:.no_toc}
> Trilha de estudo: **Índice (você está aqui)** · [Inventários](inventarios.md) · [Módulos](modulos.md) · [Playbooks](playbooks.md)

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---

## Conteúdo

* [Inventários Ansible](inventarios.md)
* [Módulos Ansible](modulos.md)
* [Playbooks Ansible - Guia completo](playbooks.md)

---

## Introdução

Ansible é uma ferramenta de automação utilizada para administrar servidores, configurar sistemas, instalar pacotes, gerenciar serviços e executar tarefas repetitivas em vários hosts.

A comunicação normalmente ocorre por SSH e não exige a instalação de um agente nos servidores gerenciados.

O computador onde o Ansible está instalado é o **Control Node**. Os servidores administrados são os **Managed Nodes**.

---

## Conceitos principais

O Ansible pode ser usado para:

- administrar servidores;
- configurar sistemas;
- instalar pacotes;
- gerenciar serviços;
- distribuir arquivos;
- executar comandos e coletar informações;
- automatizar infraestrutura.

A comunicação normalmente ocorre por SSH e não exige a instalação de um agente nos servidores gerenciados.

```text
Control Node
	│
	│ SSH
	▼
Managed Nodes
```

Um Playbook é uma das principais formas de organizar e executar essas automações de maneira documentada e repetível.

---

## Fluxo recomendado de estudo

```text
Inventário → Módulos → Playbooks → Validação → Automação recorrente
```
