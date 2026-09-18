---

layout: default
title: KUBECONFIG
---

# KUBECONFIG - Guia Rápido


## Sumário

1. [Introdução](#introducao)
2. [Estrutura de um KUBECONFIG](#estrutura-de-um-kubeconfig)
3. [Definindo o KUBECONFIG](#definindo-o-kubeconfig)
4. [NKE](#nke)
5. [Desenvolvimento](#desenvolvimento)
6. [Homologação](#homologacao)
7. [Produção](#producao)
8. [Infraestrutura](#infraestrutura)
9. [RKE](#rke)
10. [Desenvolvimento](#desenvolvimento)
11. [Homologação](#homologacao)
12. [Produção](#producao)
13. [Verificando o contexto atual](#verificando-o-contexto-atual)
14. [Listando os contextos](#listando-os-contextos)
15. [Testando acesso ao cluster](#testando-acesso-ao-cluster)
16. [Comandos úteis](#comandos-uteis)
17. [Troubleshooting](#troubleshooting)
18. [Verificar KUBECONFIG carregado](#verificar-kubeconfig-carregado)
19. [Erro: Forbidden](#erro-forbidden)
20. [Erro: system:unauthenticated](#erro-systemunauthenticated)
21. [Causa](#causa)
22. [Caso real](#caso-real)
23. [Solução](#solucao)
24. [Referências](#referencias)

---
## Introdução

O KUBECONFIG é um arquivo utilizado pelo Kubernetes para armazenar informações de acesso aos clusters.

Ele contém dados como:

* Endereço da API do cluster
* Certificados
* Contextos
* Usuários
* Tokens de autenticação

Sem um KUBECONFIG válido, o `kubectl` não consegue se conectar ao cluster.

---

## Estrutura de um KUBECONFIG

Exemplo simplificado:

```yaml
apiVersion: v1
kind: Config

clusters:
- name: empresa-exemplo-hml
  cluster:
    server: https://cluster.corp.example.com

contexts:
- name: empresa-exemplo-hml
  context:
    cluster: empresa-exemplo-hml
    user: empresa-exemplo-hml

current-context: empresa-exemplo-hml
```

---

## Definindo o KUBECONFIG

### NKE

#### Desenvolvimento

```bash
export KUBECONFIG=/home/operador/empresa-exemplo-dev.yaml
```

#### Homologação

```bash
export KUBECONFIG=/home/operador/empresa-exemplo-hml.yaml
```

#### Produção

```bash
export KUBECONFIG=/home/operador/empresa-exemplo-prd.yaml
```

#### Infraestrutura

```bash
export KUBECONFIG=/home/operador/empresa-exemplo-infra.yaml
```

---

### RKE

#### Desenvolvimento

```bash
export KUBECONFIG=/home/operador/desenvolvimentorke.yaml
```

#### Homologação

```bash
export KUBECONFIG=/home/operador/homologacaorke.yaml
```

#### Produção

```bash
export KUBECONFIG=/home/operador/producaorke.yaml
```

---

## Verificando o contexto atual

```bash
kubectl config current-context
```

Exemplo:

```text
empresa-exemplo-hml
```

---

## Listando os contextos

```bash
kubectl config get-contexts
```

Exemplo:

```text
CURRENT   NAME
*         empresa-exemplo-hml
          empresa-exemplo-prd
          empresa-exemplo-dev
```

---

## Testando acesso ao cluster

Listar namespaces:

```bash
kubectl get ns
```

Listar nodes:

```bash
kubectl get nodes
```

Verificar versão:

```bash
kubectl version
```

---

## Comandos úteis

Listar pods de todos os namespaces:

```bash
kubectl get pods -A
```

Listar serviços:

```bash
kubectl get svc -A
```

Listar ingress:

```bash
kubectl get ingress -A
```

Descrever um pod:

```bash
kubectl describe pod <pod>
```

Visualizar logs:

```bash
kubectl logs <pod>
```

Acessar um container:

```bash
kubectl exec -it <pod> -- bash
```

---

## Troubleshooting

### Verificar KUBECONFIG carregado

```bash
echo $KUBECONFIG
```

---

### Erro: Forbidden

Exemplo:

```text
Error from server (Forbidden)
```

Possíveis causas:

* Token inválido
* Token expirado
* Permissões insuficientes
* Usuário sem acesso ao cluster

---

### Erro: system:unauthenticated

Exemplo:

```text
User "system:unauthenticated" cannot get resource
```

#### Causa

O cluster recebeu uma requisição sem autenticação válida.

#### Caso real

Durante um deploy via Jenkins foi retornado:

```text
User "system:unauthenticated" cannot get resource "clusters"
```

A causa foi uma credencial/token incorreto configurado no Jenkins.

#### Solução

* Gerar novo token no Rancher
* Atualizar a credencial utilizada pelo Jenkins
* Executar novamente o pipeline

---

## Referências

Documentação oficial Kubernetes:

https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

---

> Sempre valide o KUBECONFIG carregado antes de iniciar qualquer operação em homologação ou produção.
