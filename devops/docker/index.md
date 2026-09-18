---
layout: default
title: Docker
---

# Docker

## Sumário

1. [Introdução](#introducao)
2. [Conceitos principais](#conceitos-principais)
3. [Comandos de consulta rápida](#comandos-de-consulta-rapida)
4. [Fluxo recomendado de estudo](#fluxo-recomendado-de-estudo)
5. [Boas práticas](#boas-praticas)

---

## Introdução

Docker é uma plataforma para criar, empacotar e executar aplicações em containers.

Ele ajuda a padronizar ambientes, reduzir diferenças entre desenvolvimento e produção e facilitar a distribuição de aplicações com suas dependências.

> Antes de remover containers, imagens ou volumes, confirme o ambiente e o recurso-alvo. Volumes podem conter dados persistentes e nem sempre possuem recuperação simples.

---

## Conceitos principais

Esta seção reúne fundamentos e procedimentos sobre:

- containers;
- imagens;
- volumes;
- redes Docker;
- Docker Compose;
- registries privados;
- troubleshooting de containers.

---

## Comandos de consulta rápida

```bash
docker ps
docker ps -a
docker images
docker logs nome-do-container
docker exec -it nome-do-container /bin/sh
docker inspect nome-do-container
```

---

## Fluxo recomendado de estudo

```text
Imagens → Containers → Volumes → Redes → Compose → Registries
```

---

## Boas práticas

- Use tags fixas de imagem em produção.
- Evite executar containers como root quando possível.
- Separe dados persistentes em volumes.
- Documente portas, variáveis de ambiente e volumes.
- Use `.dockerignore` para reduzir o tamanho do build.
- Faça limpeza periódica de imagens e containers antigos.

---

Esta seção concentra procedimentos e anotações sobre containers, imagens, volumes e execução de aplicações com Docker.
