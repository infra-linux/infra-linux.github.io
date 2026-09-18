---

layout: default
title: CI/CD
------------

# CI/CD

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

* [Git no CI/CD](git-no-cicd.md)

### Build e testes

* [Build e artefatos](build-e-artefatos.html)
* [Testes em pipelines](testes-em-pipelines.html)

### Containers

* [Docker e imagens no CI/CD](docker-cicd.html)

### Deploy

* [Deploy](deploy.html)
* [CI/CD com Kubernetes](cicd-kubernetes.html)

### Ferramentas

* [Jenkins](jenkins.html)
* [GitHub Actions](github-actions.html)

### Operação

* [Monitoramento e observabilidade](monitoramento-cicd.html)
* [GitOps](gitops.html)

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

**Minha recomendação:** não crie todos esses documentos agora. Comece com **`fundamentos-cicd.html`**, depois faça um tutorial prático de **GitHub Actions ou Jenkins**, e a partir dele evoluímos para Docker → Kubernetes → monitoramento.

Isso deixa o Ninja Linux organizado e evita transformar a seção CI/CD em uma página enorme difícil de estudar.
```