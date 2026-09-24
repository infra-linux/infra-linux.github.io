---
layout: default
title: BIND - Adicionar Domínio ao Encaminhamento Condicional
---

# BIND — Adicionar Domínio ao Encaminhamento Condicional
{:.no_toc}

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}


## Hosts DNS

**DNS 01**

```text
s-sesu13423
10.0.134.23
```

**DNS 02**

```text
s-brsu13423
10.8.134.23
```

---

## 1. Fazer backup

Antes de alterar:

```bash
cp /etc/named/conditional.conf /etc/named/conditional.conf.bak
```

---

## 2. Editar o arquivo

```bash
vim /etc/named/conditional.conf
```

Adicionar:

```conf
zone "DOMINIO.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

## 3. Validar e Recarregar BIND

```bash
named-checkconf
```

Sem retorno = configuração válida.

```bash
rndc reload
```

Resultado esperado:

```text
server reload successful
```

---

## 5. Testar

```bash
dig www.DOMINIO.com @IP_DO_DNS
```

Resultado esperado:

```text
status: NOERROR
```

Também pode comparar diretamente com o Google:

```bash
dig www.DOMINIO.com @8.8.8.8
```