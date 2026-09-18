---
layout: default
title: CI/CD
---

# CI/CD


## Sumário

1. [Introdução](#introducao)
2. [Conteúdo](#conteudo)
3. [Fluxo recomendado de estudo](#fluxo-recomendado-de-estudo)

---


## Introdução

CI/CD é um conjunto de práticas utilizadas para automatizar a integração, validação, entrega e implantação de aplicações.

A automação reduz tarefas manuais, aumenta a rastreabilidade das alterações e permite identificar problemas mais cedo no ciclo de desenvolvimento.

O fluxo fundamental pode ser representado por:

```text
Código → Build → Testes → Imagem → Deploy → Monitoramento
```

---
## Conteúdo

### Fundamentos

* [Fundamentos de CI/CD](fundamentos-cicd.md)

### Git e pipelines

* [Guia prático de Git](../git/guia-pratico.md)

### Build e testes

* [Fundamentos de CI/CD](fundamentos-cicd.md)

### Containers

* [Imagens Docker](../docker/docker-image.md)
* [Containers Docker](../docker/docker-container.md)

### Deploy

* [Kubernetes](../kubernetes/index.md)

### Ferramentas

* [Fundamentos de CI/CD](fundamentos-cicd.md)

### Operação

* [Monitoramento](../../monitoramento/index.md)
* [DevOps — início rápido](../inicio-rapido/index.md)

---
## Fluxo recomendado de estudo

```text
                    CI/CD
                      │
                      ▼
                    Git
                      │
                      ▼
                    Build
                      │
                      ▼
                   Testes
                      │
                      ▼
              Imagem / Artefato
                      │
                      ▼
                    Deploy
                      │
                      ▼
                 Kubernetes
                      │
                      ▼
             Monitoramento
```

A sequência recomendada é:

1. Entender os fundamentos de CI/CD.
2. Revisar Git e branches.
3. Entender build e artefatos.
4. Aprender testes automatizados.
5. Entender Docker e imagens.
6. Criar uma pipeline.
7. Realizar deploy.
8. Integrar com Kubernetes.
9. Implementar monitoramento.
10. Avançar para GitOps e estratégias de deploy.

```