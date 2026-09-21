---
layout: default
title: Ansible
---

# Ansible

## Sumário

1. [Introdução](#introducao)
2. [Conceitos principais](#conceitos-principais)
3. [Conteúdo](#conteudo)
4. [Fluxo recomendado de estudo](#fluxo-recomendado-de-estudo)

---

## Introdução

Um **Playbook Ansible** é um arquivo escrito em **YAML** que define, de forma organizada e automatizada, quais tarefas o Ansible deve executar em um ou mais servidores.

Enquanto o comando `ansible` executa uma ação pontual, o Playbook transforma várias ações em um procedimento documentado, repetível e automatizado.

Uma execução manual poderia ser:

```bash
ssh usuario@servidor
systemctl status servico
systemctl is-active servico
```

Com Ansible, essas verificações podem ser executadas de forma padronizada em vários servidores:

```bash
ansible-playbook -i inventory/hosts.ini playbooks/diagnostico.yml -k
```

O Playbook passa a funcionar como um procedimento operacional automatizado.

---

## Conceitos principais

Ansible é uma ferramenta de automação usada para:

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

O **Control Node** é o computador onde o Ansible está instalado. Os **Managed Nodes** são os servidores administrados.

---

## Conteúdo

* [Playbooks Ansible — guia completo](guia-pratico.md)

---
## Fluxo recomendado de estudo

```text
Inventário → Módulos → Playbooks → Validação → Automação recorrente
```
