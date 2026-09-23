---
layout: default
title: DevOps
---
# DevOps

## Sumário

1. [Introdução](#introducao)
2. [Conteúdo](#conteudo)
3. [Rotina recomendada](#rotina-recomendada)
4. [Boas práticas](#boas-praticas)

---

## Introdução

DevOps reúne práticas que aproximam o desenvolvimento de software e as operações de infraestrutura.

Esta seção apresenta versionamento, CI/CD, automação, containers, orquestração e observabilidade em um fluxo integrado de entrega e operação.

O objetivo é entender a relação entre as ferramentas antes de avançar para implementações mais complexas.

---

## Conteúdo

Siga esta sequência para compreender o fluxo de entrega de aplicações, do versionamento à operação em Kubernetes.

1. [Git](git/index.md): branches, revisão e colaboração segura.
2. [Docker](docker/index.md): imagens, containers, volumes e redes.
3. [Kubernetes](kubernetes/index.md): KUBECONFIG, kubectl, Pods, Deployments, Services e Ingress.
4. [CI/CD](cicd/index.md): validação, criação de imagens e deploy.
5. [Ansible](ansible/index.md): automação de configurações repetitivas de infraestrutura.
6. [Documentações](devops/documentacao/index.md)

O fluxo pode ser resumido assim:

```text
Git → Docker → Kubernetes → CI/CD → Ansible
```

---

## Rotina recomendada

Depois de compreender os fundamentos, o trabalho diário costuma seguir este fluxo:

```text
Alteração em branch
	↓
Revisão
	↓
Validação automática
	↓
Merge
	↓
Build de imagem
	↓
Deploy controlado
	↓
Monitoramento
```

---

## Boas práticas

- Pratique primeiro em um ambiente de desenvolvimento.
- Valide o alvo antes de executar alterações em produção.
- Mantenha backup e defina o procedimento de rollback.
- Use branches e Pull Requests para revisar alterações.
- Registre logs e monitore o resultado do deploy.

---

> Esta seção reúne conceitos, procedimentos, automações e troubleshooting relacionados ao universo DevOps.