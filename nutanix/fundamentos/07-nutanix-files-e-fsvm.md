---
layout: default
title: Nutanix Files e FSVM
---

# Nutanix Files e FSVM


## Sumário

1. [O que é Nutanix Files?](#o-que-e-nutanix-files)
2. [FSVM](#fsvm)
3. [CVM versus FSVM](#cvm-versus-fsvm)
4. [Fluxo de acesso](#fluxo-de-acesso)
5. [Compartilhamento SMB](#compartilhamento-smb)
6. [Export NFS](#export-nfs)
7. [Alta disponibilidade](#alta-disponibilidade)
8. [Relação com o Data Lens](#relacao-com-o-data-lens)
9. [Pontos importantes de administração](#pontos-importantes-de-administracao)
10. [Resumo](#resumo)

---
## O que é Nutanix Files?

O **Nutanix Files** é o serviço distribuído de armazenamento de arquivos da Nutanix.

Ele pode fornecer acesso por:

- SMB;
- NFS.

Exemplos:

```text
SMB:
\\files01\financeiro

NFS:
files01:/dados
```

## FSVM

O Files utiliza máquinas virtuais especializadas chamadas **File Server VMs**, ou FSVMs.

```text
Nutanix Files
├── FSVM 1
├── FSVM 2
└── FSVM 3
```

As FSVMs trabalham em conjunto para prestar o serviço de arquivos.

## CVM versus FSVM

| Componente | Função |
|---|---|
| CVM | Serviços de armazenamento e dados do cluster |
| FSVM | Serviço de arquivos SMB e NFS |
| VM comum | Aplicação, banco de dados ou outro sistema |

## Fluxo de acesso

```text
Usuário ou aplicação
        ↓ SMB/NFS
FSVM
        ↓
Armazenamento do Files
        ↓
CVM e AOS
        ↓
Discos do cluster
```

## Compartilhamento SMB

Um compartilhamento SMB costuma ser utilizado por clientes Windows.

```text
\\files01.empresa.local\rh
```

Ele pode integrar-se ao Active Directory para autenticação e autorização.

## Export NFS

Um export NFS costuma ser utilizado por clientes Linux ou Unix.

```text
files01.empresa.local:/dados
```

Exemplo de montagem:

```bash
mount files01.empresa.local:/dados /mnt/dados
```

## Alta disponibilidade

Como o serviço utiliza várias FSVMs, a carga pode ser distribuída e o ambiente pode continuar atendendo acessos dentro dos limites de sua configuração quando ocorre uma falha.

## Relação com o Data Lens

O Data Lens utiliza informações e eventos relacionados aos dados não estruturados para oferecer recursos como:

- Auditoria;
- Visibilidade;
- Identificação de anomalias;
- Análise de permissões;
- Investigação de riscos;
- Proteção contra ransomware.

## Pontos importantes de administração

- Saúde das FSVMs;
- DNS;
- NTP;
- Integração com Active Directory;
- Rede de clientes;
- Capacidade;
- Desempenho;
- Eventos de auditoria;
- Permissões;
- Alertas.

## Resumo

```text
Nutanix Files = serviço distribuído de arquivos
FSVM = máquina virtual que atende SMB/NFS
CVM = serviços de armazenamento do cluster
```
