---
layout: default
title: DevOps
---
# DevOps

## Sumário

1. [Introdução](#introdução)
2. [Conteúdo](#conteúdo)
3. [Conceitos e tecnologias](#conceitos-e-tecnologias)
4. [Fluxo de estudo](#fluxo-de-estudo)

---
## Introdução

DevOps é um conjunto de práticas e princípios que aproxima o desenvolvimento de software e as operações de infraestrutura.

A abordagem busca automatizar processos, melhorar a colaboração entre equipes e tornar a entrega e a operação de aplicações mais rápidas, confiáveis e rastreáveis.

Um fluxo simplificado pode ser representado por:

```text
Planejar
   ↓
Código
   ↓
Build
   ↓
Testes
   ↓
Entrega
   ↓
Deploy
   ↓
Monitoramento
   ↓
Feedback
   └──────────────→ Planejar
```

---
## Conteúdo

### Comece aqui

* [Início rápido em DevOps](inicio-rapido/index.md)

### CI/CD

* [Fundamentos de CI/CD](cicd/fundamentos-cicd.md)

### Controle de versão

* [Git](git/index.md)

### Automação

* [Ansible](ansible/index.md)

### Containers

- [Docker](docker/index.md)
  - [Imagens Docker](docker/docker-image.md)

### Orquestração

* [Kubernetes](kubernetes/index.md)

---
## Conceitos e tecnologias

Ao longo dos estudos, os seguintes conceitos e ferramentas serão abordados:

| Categoria                  | Tecnologias / Conceitos     |
| -------------------------- | --------------------------- |
| Controle de versão         | Git                         |
| CI/CD                      | Jenkins, Azure DevOps       |
| Automação                  | Ansible                     |
| Containers                 | Docker                      |
| Container Registry         | Harbor                      |
| Orquestração               | Kubernetes                  |
| Infraestrutura como código | Terraform / OpenTofu        |
| Observabilidade            | Zabbix, Prometheus, Grafana |
| GitOps                     | Argo CD / Flux              |

---
## Fluxo de estudo

Uma sequência recomendada para estudar DevOps é:

```text
Linux
  ↓
Git
  ↓
Shell
  ↓
Docker
  ↓
CI/CD
  ↓
Jenkins / GitHub Actions
  ↓
Registry
  ↓
Kubernetes
  ↓
Automação
  ↓
Infrastructure as Code
  ↓
GitOps
  ↓
Observabilidade
```

O objetivo não é apenas aprender ferramentas individualmente, mas entender como elas se integram em um fluxo de entrega e operação.

---
> Esta seção reúne conceitos, procedimentos, automações e troubleshooting relacionados ao universo DevOps.