---
layout: default
title: Kubernetes
---
# Kubernetes
{:.no_toc}

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---


## Introdução

O Kubernetes é uma plataforma de orquestração de containers utilizada para automatizar implantação, escalabilidade e gerenciamento de aplicações.

Atualmente é uma das principais tecnologias utilizadas em ambientes corporativos, nuvem e arquiteturas de microsserviços.

Permite executar aplicações de forma distribuída, resiliente e altamente disponível.

> Execute comandos `kubectl` somente após confirmar o contexto com `kubectl config current-context`. Em produção, valide namespace, recurso e impacto antes de aplicar ou remover manifestos.

---
## Conteúdo

<div class="wiki-topic-list">

  <a class="wiki-topic" href="{{ 'devops/kubernetes/kubeconfig.html' | relative_url }}">
    <span class="wiki-topic-title">KUBECONFIG</span>
    <span class="wiki-topic-description">Configuração de acesso e gerenciamento de clusters Kubernetes.</span>
  </a>

  <a class="wiki-topic" href="{{ 'devops/kubernetes/kubectl.html' | relative_url }}">
    <span class="wiki-topic-title">Kubectl</span>
    <span class="wiki-topic-description">Comandos essenciais para administrar e interagir com o Kubernetes.</span>
  </a>

  <a class="wiki-topic" href="{{ 'devops/kubernetes/instalacao-cluster-rocky-containerd-calico.html' | relative_url }}">
    <span class="wiki-topic-title">Instalação de Cluster Kubernetes com Rocky Linux, containerd e Calico</span>
    <span class="wiki-topic-description">Instalação completa de um cluster Kubernetes utilizando Rocky Linux, containerd e Calico.</span>
  </a>

  <a class="wiki-topic" href="{{ 'devops/kubernetes/tutorial-kubernetes-rocky-linux.html' | relative_url }}">
    <span class="wiki-topic-title">Instalação do Kubernetes (kubeadm) no Rocky Linux</span>
    <span class="wiki-topic-description">Instalação do Kubernetes com kubeadm em servidores Rocky Linux.</span>
  </a>

  <a class="wiki-topic" href="{{ 'devops/kubernetes/pods.html' | relative_url }}">
    <span class="wiki-topic-title">Pods</span>
    <span class="
---
## Fluxo recomendado de estudo

```text
KUBECONFIG --> Kubectl --> Instalação do Cluster --> Pods --> Deployments --> Services --> Ingress --> Troubleshooting
```

---
> Recomenda-se iniciar pelos tópicos KUBECONFIG, Kubectl e Pods antes de avançar para Deployments, Services, Ingress e Troubleshooting.
