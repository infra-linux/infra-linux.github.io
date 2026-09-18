---
layout: default
title: DevOps — início rápido
---

# DevOps — início rápido

## Sumário

1. [Introdução](#introducao)
2. [Trilha de estudo](#trilha-de-estudo)
3. [Rotina recomendada](#rotina-recomendada)
4. [Boas práticas](#boas-praticas)

---

## Introdução

Esta seção apresenta uma trilha de aprendizagem para conectar versionamento, containers, orquestração, CI/CD e automação.

O objetivo é entender a relação entre as ferramentas antes de avançar para implementações mais complexas.

---

## Trilha de estudo

Siga esta sequência para compreender o fluxo de entrega de aplicações, do versionamento à operação em Kubernetes.

1. [Git — guia prático](../git/): branches, revisão e colaboração segura.
2. [Docker](../docker/): imagens, containers, volumes e redes.
3. [Kubernetes](../kubernetes/): KUBECONFIG, kubectl, Pods, Deployments, Services e Ingress.
4. [CI/CD](../cicd/): validação, criação de imagens e deploy.
5. [Ansible](../ansible/): automação de configurações repetitivas de infraestrutura.

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
