---
layout: default
title: Kubernetes
---
# Kubernetes


## Sumário

1. [Introdução](#introducao)
2. [Conteúdo](#conteudo)
3. [Fluxo recomendado de estudo](#fluxo-recomendado-de-estudo)

---


## Introdução

O Kubernetes é uma plataforma de orquestração de containers utilizada para automatizar implantação, escalabilidade e gerenciamento de aplicações.

Atualmente é uma das principais tecnologias utilizadas em ambientes corporativos, nuvem e arquiteturas de microsserviços.

Permite executar aplicações de forma distribuída, resiliente e altamente disponível.

> Execute comandos `kubectl` somente após confirmar o contexto com `kubectl config current-context`. Em produção, valide namespace, recurso e impacto antes de aplicar ou remover manifestos.

---
## Conteúdo

* [KUBECONFIG](kubeconfig.md)
* [Kubectl](kubectl.md)
* [Instalação de Cluster Kubernetes com Rocky Linux, containerd e Calico](instalacao-cluster-rocky-containerd-calico.md)
* [Instalação do Kubernetes (kubeadm) no Rocky Linux](tutorial-kubernetes-rocky-linux.md)
* [Pods](pods.md)
* [Deployments](deployments.md)
* [Services](services.md)
* [Ingress](ingress.md)
* [Troubleshooting Kubernetes](troubleshooting.md)

---
## Fluxo recomendado de estudo

```text
KUBECONFIG --> Kubectl --> Instalação do Cluster --> Pods --> Deployments --> Services --> Ingress --> Troubleshooting
```

---
> Recomenda-se iniciar pelos tópicos KUBECONFIG, Kubectl e Pods antes de avançar para Deployments, Services, Ingress e Troubleshooting.
