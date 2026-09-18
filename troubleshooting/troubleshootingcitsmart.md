---
layout: default
title: Troubleshooting Aplicação ITSM de Exemplo
---

# Troubleshooting – Indisponibilidade da Aplicação ITSM de Exemplo

---


## Sumário

1. [Solução de Contorno](#solucao-de-contorno)
2. [Sintoma](#sintoma)
3. [Verificar status dos containers](#verificar-status-dos-containers)
4. [Verificar consumo de recursos](#verificar-consumo-de-recursos)
5. [Verificar erros de memória](#verificar-erros-de-memoria)
6. [Identificar processo Java](#identificar-processo-java)
7. [Verificar utilização da Heap](#verificar-utilizacao-da-heap)
8. [Verificar parâmetros da JVM](#verificar-parametros-da-jvm)
9. [Coletar logs da aplicação](#coletar-logs-da-aplicacao)
10. [Verificar trilha de auditoria](#verificar-trilha-de-auditoria)
11. [Evidências encontradas no incidente de DD/MM/AAAA](#evidencias-encontradas-no-incidente-de-ddmmaaaa)
12. [Conclusão Preliminar](#conclusao-preliminar)

---
## Solução de Contorno

Reiniciar os serviços da aplicação ITSM de exemplo.

Observação: a reinicialização restabelece o funcionamento da aplicação, porém não corrige a causa raiz do problema.

---

## Sintoma

Usuários relatam indisponibilidade ou lentidão no acesso à aplicação ITSM de exemplo.

---

## Verificar status dos containers

```bash
docker ps
```

Verificar se todos os containers do ambiente estão em execução.

---

## Verificar consumo de recursos

```bash
docker stats --no-stream itsm-exemplo
```

Validar consumo de CPU e memória do container principal da aplicação.

---

## Verificar erros de memória

```bash
docker logs itsm-exemplo 2>&1 | grep -i "OutOfMemoryError"
```

Caso exista retorno semelhante ao abaixo:

```text
java.lang.OutOfMemoryError: Java heap space
```

A aplicação apresentou esgotamento da memória Java (Heap).

---

## Identificar processo Java

```bash
docker exec -it itsm-exemplo ps -ef | grep java
```

Anotar o PID do processo Java.

Exemplo:

```text
app <PID_JAVA> java ...
```

---

## Verificar utilização da Heap

```bash
docker exec -it itsm-exemplo jcmd <PID_JAVA> GC.heap_info
```

Exemplo:

```text
garbage-first heap total 8388608K
used 1048576K
```

---

## Verificar parâmetros da JVM

```bash
docker exec -it itsm-exemplo jcmd <PID_JAVA> VM.flags
```

Verificar principalmente os parâmetros:

```text
-XX:InitialHeapSize
-XX:MaxHeapSize
```

---

## Coletar logs da aplicação

Acessar:

```text
Sistema → Informações do Sistema → Download Log do JBoss
```

Salvar o arquivo para análise posterior.

---

## Verificar trilha de auditoria

Acessar:

```text
Sistema → Trilha de Auditoria → Auditoria de Dados
```

Filtrar pelo horário da indisponibilidade.

Exemplo:

```text
Data Inicial: DD/MM/AAAA 11:40
Data Final:   DD/MM/AAAA 11:50
```

---

## Evidências encontradas no incidente de DD/MM/AAAA

Foi identificado:

```text
java.lang.OutOfMemoryError: Java heap space
```

A stack trace apontou para:

```text
com.example.audit.service.impl.AuditServiceImpl
org.javers.core.JaversCore.compare
```

Após o erro foram observadas falhas de comunicação internas:

```text
ActiveMQConnectionTimedOutException
AMQ219014
Channel is closed
```

## Conclusão Preliminar

Os indícios apontam para falha relacionada ao módulo de auditoria da aplicação, resultando em esgotamento da memória Java (OutOfMemoryError) e consequente degradação dos serviços internos da aplicação.
