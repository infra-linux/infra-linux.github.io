---

layout: default
title: Docker
---

# Docker

## Sumário

1. [Introdução](#introdução)
2. [Conceitos principais](#conceitos-principais)
3. [Comandos de consulta rápida](#comandos-de-consulta-rápida)
4. [Fluxo recomendado de estudo](#fluxo-recomendado-de-estudo)
5. [Boas práticas](#boas-práticas)

---

## Introdução

Docker é uma plataforma para criar, empacotar e executar aplicações em **containers**.

Ele ajuda a padronizar ambientes, reduzir diferenças entre desenvolvimento e produção e facilitar a distribuição de aplicações com suas dependências.

> **Atenção:** antes de remover containers, imagens ou volumes, confirme o ambiente e o recurso-alvo. Volumes podem conter dados persistentes e sua recuperação pode não ser simples.

---

## Conceitos principais

Esta seção reúne os principais conceitos e procedimentos relacionados ao Docker:

* [Imagens Docker](docker-image.md)
* [Containers](docker-container.md)
* Dockerfile
* Volumes
* Redes Docker
* Docker Compose
* Registries privados
* Troubleshooting de containers

---

## Comandos de consulta rápida

### Containers

```bash
docker ps
docker ps -a
docker start nome-do-container
docker stop nome-do-container
docker restart nome-do-container
docker rm nome-do-container
```

### Imagens

```bash
docker images
docker pull nome-da-imagem
docker rmi nome-da-imagem
```

### Logs e diagnóstico

```bash
docker logs nome-do-container
docker logs -f nome-do-container
docker inspect nome-do-container
docker stats
```

### Execução de comandos

```bash
docker exec -it nome-do-container /bin/sh
```

---

## Fluxo recomendado de estudo

A sequência recomendada para estudar Docker é:

```text
Imagens
   ↓
Containers
   ↓
Volumes
   ↓
Redes
   ↓
Docker Compose
   ↓
Registries
   ↓
Troubleshooting
```

### Progressão prática

1. Entender o que são **imagens Docker**.
2. Criar, iniciar, parar e remover **containers**.
3. Trabalhar com **Dockerfile** e construir imagens.
4. Entender persistência de dados com **volumes**.
5. Configurar comunicação entre containers com **redes Docker**.
6. Orquestrar aplicações com **Docker Compose**.
7. Trabalhar com **registries privados**.
8. Diagnosticar problemas em containers e serviços.

---

## Boas práticas

* Use tags de imagem fixas em ambientes de produção.
* Evite executar containers como `root` quando possível.
* Separe dados persistentes utilizando volumes.
* Documente portas, variáveis de ambiente, volumes e dependências.
* Utilize `.dockerignore` para evitar o envio de arquivos desnecessários durante o build.
* Mantenha as imagens o mais enxutas possível.
* Remova containers e imagens que não são mais utilizados.
* Evite armazenar credenciais diretamente em imagens ou arquivos versionados.
* Monitore logs, consumo de CPU, memória e armazenamento.
* Antes de executar comandos destrutivos, confirme o container, imagem ou volume alvo.

---

## Próximos passos

Comece pelo estudo de **imagens Docker** e, em seguida, avance para:

* [Containers](docker-container.md)

A sequência recomendada é seguir o fluxo apresentado nesta página.
