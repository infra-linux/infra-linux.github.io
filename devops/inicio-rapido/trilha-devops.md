---
layout: default
title: DevOps — início rápido
---

# DevOps — início rápido

Siga esta trilha para entender o fluxo de entrega de aplicações, do versionamento à operação em Kubernetes.

1. [Git — guia prático](../git/): branches, revisão e colaboração segura.
2. [Docker](../docker/): imagens, containers, volumes e redes.
3. [Kubernetes](../kubernetes/): comece por KUBECONFIG e kubectl; depois avance para Pods, Deployments, Services e Ingress.
4. [CI/CD](../cicd/): conecte a validação, a criação de imagens e o deploy.
5. [Ansible](../ansible/): automatize configurações repetitivas de infraestrutura.

## Rotina recomendada

```text
Alteração em branch → revisão → validação automática → merge → build de imagem → deploy controlado → monitoramento
```

> Pratique primeiro em um ambiente de desenvolvimento. Antes de qualquer alteração em produção, valide o alvo, tenha backup e defina o rollback.
