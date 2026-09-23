---
layout: default
title: BIND — Encaminhamento Condicional de DNS para um Domínio Externo 
---

# BIND — Encaminhamento Condicional de DNS para um Domínio Externo
{:.no_toc}

> Guia prático e didático sobre como configurar um servidor DNS BIND para encaminhar consultas de um domínio específico para servidores DNS externos, mantendo o restante das consultas no fluxo normal do DNS corporativo.

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---

## 1. Objetivo

Este documento demonstra como configurar o **BIND (`named`)** para que consultas destinadas a um domínio específico sejam encaminhadas para servidores DNS externos.

### Exemplo utilizado

O objetivo foi fazer com que:

```text
www.sangfor.com
```

fosse resolvido utilizando os DNS públicos do Google:

```text
8.8.8.8
8.8.4.4
```

Enquanto os demais domínios continuassem utilizando a configuração normal do DNS da Infraero.

### Comportamento desejado

```text
                    DNS BIND Infraero
                         │
                         │
              ┌──────────┴──────────┐
              │                     │
        Outros domínios         sangfor.com
              │                     │
              ▼                     ▼
      Resolução normal        Google DNS
                              8.8.8.8
                              8.8.4.4
```

Isso é chamado de **Conditional Forwarding** ou **encaminhamento condicional**.

---

# 2. O que é Conditional Forwarding?

Normalmente, um servidor DNS recursivo pode resolver um domínio seguindo a hierarquia DNS:

```text
Root (.)
   │
   ▼
TLD (.com)
   │
   ▼
sangfor.com
   │
   ▼
Servidor autoritativo
```

Entretanto, podemos determinar que determinado domínio seja resolvido por servidores DNS específicos.

Por exemplo:

```text
sangfor.com → 8.8.8.8 / 8.8.4.4
```

Dessa forma, quando o BIND receber:

```text
www.sangfor.com
```

ele não tentará resolver diretamente a hierarquia DNS desse domínio.

Ele encaminhará a consulta para:

```text
8.8.8.8
8.8.4.4
```

---

# 3. Por que utilizar Conditional Forwarding?

Essa configuração é útil quando:

* um domínio externo não está sendo resolvido corretamente pelo DNS interno;
* existe uma falha de comunicação com os servidores autoritativos;
* existe uma necessidade específica de utilizar outro DNS para determinado domínio;
* determinados domínios precisam ser resolvidos por DNS externos;
* não queremos alterar o DNS global da organização.

Uma vantagem importante é que **somente o domínio especificado será encaminhado**.

Por exemplo:

```text
sangfor.com → Google DNS
```

mas:

```text
infraero.gov.br → DNS interno
google.com → resolução normal
github.com → resolução normal
cloudflare.com → resolução normal
```

---

# 4. Situação encontrada

No primeiro DNS:

```text
10.0.134.23
```

foi executado:

```bash
dig sangfor.com
```

O resultado foi:

```text
status: SERVFAIL
```

Isso indicava que o DNS interno não conseguia concluir a resolução.

---

# 5. Testando diretamente o Google DNS

Foi feita uma consulta diretamente ao Google:

```bash
dig www.sangfor.com @8.8.8.8
```

O resultado foi:

```text
status: NOERROR
```

E retornou:

```text
www.sangfor.com. CNAME www.sangfor.com.cdn.cloudflare.net.

www.sangfor.com.cdn.cloudflare.net. A 172.66.160.251
www.sangfor.com.cdn.cloudflare.net. A 104.20.41.193
```

Isso demonstrou que:

```text
Internet → Google DNS → sangfor.com
```

estava funcionando.

---

# 6. Investigando com +trace

Também foi executado:

```bash
dig www.sangfor.com +trace
```

A consulta conseguiu chegar à delegação de:

```text
sangfor.com
```

e encontrou:

```text
ns3.dnsv2.com
ns4.dnsv2.com
```

Porém o servidor apresentou:

```text
couldn't get address for 'ns3.dnsv2.com': not found
couldn't get address for 'ns4.dnsv2.com': not found
```

Consequentemente, a resolução não era concluída pelo DNS interno.

Isso confirmou que havia um problema no caminho de resolução utilizado pelo DNS interno.

---

# 7. Identificando o serviço DNS

Antes de alterar a configuração, foi verificado qual serviço DNS estava executando:

```bash
ps -ef | grep -E '[n]amed|[b]ind|[d]nsmasq|[u]nbound'
```

O resultado mostrou:

```text
/usr/sbin/named -u named -c /etc/named.conf -4
```

Isso confirmou que o servidor utilizava:

```text
BIND / named
```

e o arquivo principal era:

```text
/etc/named.conf
```

---

# 8. Verificando a configuração existente

Foi utilizado:

```bash
grep -nE '^(options|zone|forward|forwarders|include)' /etc/named.conf
```

No primeiro servidor foi encontrado:

```conf
include "/etc/named/conditional.conf";
```

Isso significava que o servidor já possuía um arquivo separado para encaminhamentos condicionais.

Foi então consultado:

```bash
cat /etc/named/conditional.conf
```

O arquivo já possuía uma configuração semelhante para:

```text
hikvision.com
```

```conf
zone "hikvision.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

Isso serviu como modelo para a nova configuração.

---

# 9. Configuração para sangfor.com

Foi adicionada a seguinte configuração:

```conf
// Encaminhamento condicional para sangfor.com
zone "sangfor.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

O arquivo ficou conceitualmente assim:

```conf
// Encaminhamento condicional para hikvision.com
zone "hikvision.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};

// Encaminhamento condicional para sangfor.com
zone "sangfor.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

---

# 10. Entendendo cada diretiva

## 10.1 zone

```conf
zone "sangfor.com" IN {
```

Define uma zona DNS para:

```text
sangfor.com
```

O BIND passa a tratar consultas desse domínio de maneira específica.

---

## 10.2 type forward

```conf
type forward;
```

Indica que o BIND deve encaminhar as consultas para outros servidores DNS.

---

## 10.3 forward only

```conf
forward only;
```

Essa diretiva é importante.

Ela significa:

> Para essa zona, encaminhe as consultas somente para os servidores definidos em `forwarders`.

Ou seja, se:

```text
8.8.8.8
8.8.4.4
```

não responderem, o BIND não tentará resolver o domínio diretamente através da hierarquia DNS.

---

## 10.4 forwarders

```conf
forwarders {
        8.8.8.8;
        8.8.4.4;
};
```

Define os servidores DNS para os quais as consultas serão encaminhadas.

Neste caso:

```text
8.8.8.8  → Google Public DNS
8.8.4.4  → Google Public DNS
```

---

# 11. Validando a configuração

Antes de recarregar o BIND, sempre validar a configuração.

Comando:

```bash
named-checkconf
```

Se não houver saída:

```text
[root@server ~]# named-checkconf
[root@server ~]#
```

isso indica que a configuração está sintaticamente válida.

---

# 12. Erro encontrado durante a configuração

Durante a edição inicial ocorreu:

```text
/etc/named/conditional.conf:9: unknown option 'i'
/etc/named/conditional.conf:9: unexpected token near '}'
```

Esse erro indicava que havia um caractere `i` indevido no arquivo.

A correção foi feita e o teste:

```bash
named-checkconf
```

passou sem erros.

### Regra importante

Nunca faça:

```bash
rndc reload
```

ou reinicie o `named` antes de validar:

```bash
named-checkconf
```

---

# 13. Recarregando o BIND

Depois da validação:

```bash
rndc reload
```

O resultado esperado é:

```text
server reload successful
```

O `reload` é preferível ao restart nesse cenário porque permite que o BIND recarregue sua configuração sem derrubar completamente o serviço.

---

# 14. Testando a resolução

Depois do reload:

```bash
dig www.sangfor.com
```

Também é recomendável especificar explicitamente o servidor:

```bash
dig www.sangfor.com @10.0.134.23
```

O resultado esperado é:

```text
status: NOERROR
```

E:

```text
SERVER: 10.0.134.23#53
```

---

# 15. Resultado obtido

Após a configuração, o DNS interno retornou:

```text
www.sangfor.com.  CNAME  www.sangfor.com.cdn.cloudflare.net.

www.sangfor.com.cdn.cloudflare.net. A 104.20.41.193
www.sangfor.com.cdn.cloudflare.net. A 172.66.160.251
```

O mais importante foi:

```text
status: NOERROR
```

Isso confirmou que o DNS interno passou a resolver corretamente o domínio.

---

# 16. Fluxo final da resolução

Antes:

```text
Cliente
   │
   ▼
DNS Infraero
10.0.134.23
   │
   ▼
Tentativa de resolução normal
   │
   ▼
Falha
   │
   ▼
SERVFAIL
```

Depois:

```text
Cliente
   │
   ▼
DNS Infraero
10.0.134.23
   │
   │ consulta www.sangfor.com
   ▼
Conditional Forwarder
   │
   ▼
8.8.8.8
ou
8.8.4.4
   │
   ▼
DNS público
   │
   ▼
sangfor.com
   │
   ▼
Resposta
   │
   ▼
DNS Infraero
   │
   ▼
Cliente
```

---

# 17. Segundo DNS

No segundo servidor:

```text
s-brsu13423
```

também foi identificado:

```text
/usr/sbin/named -u named -c /etc/named.conf -4
```

Portanto, também utiliza BIND.

Porém, havia uma diferença.

O `/etc/named.conf` não possuía:

```conf
include "/etc/named/conditional.conf";
```

Além disso:

```text
/etc/named/
```

estava vazio.

Nesse caso, foi necessário criar o arquivo:

```text
/etc/named/conditional.conf
```

com:

```conf
// Encaminhamento condicional para sangfor.com
zone "sangfor.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

Depois foi adicionada ao `/etc/named.conf`:

```conf
include "/etc/named/conditional.conf";
```

Por exemplo:

```conf
include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";
include "/etc/named/conditional.conf";
```

E novamente:

```bash
named-checkconf
```

deve ser executado antes do reload.

---

# 18. Procedimento resumido para reutilização

Quando precisar configurar outro domínio:

### 1. Verificar o BIND

```bash
ps -ef | grep -E '[n]amed|[b]ind|[d]nsmasq|[u]nbound'
```

### 2. Verificar o `named.conf`

```bash
grep -nE '^(options|zone|forward|forwarders|include)' /etc/named.conf
```

### 3. Verificar conditional.conf

```bash
cat /etc/named/conditional.conf
```

### 4. Criar ou editar

```bash
vim /etc/named/conditional.conf
```

### 5. Adicionar:

```conf
zone "DOMINIO" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

### 6. Se necessário, incluir no `named.conf`

```conf
include "/etc/named/conditional.conf";
```

### 7. Validar

```bash
named-checkconf
```

### 8. Recarregar

```bash
rndc reload
```

### 9. Testar

```bash
dig www.DOMINIO
```

Ou:

```bash
dig www.DOMINIO @IP_DO_DNS
```

---

# 19. Testes recomendados

Depois da alteração, faça pelo menos estes testes:

### Consulta pelo DNS interno

```bash
dig www.sangfor.com @10.0.134.23
```

### Consulta diretamente pelo Google

```bash
dig www.sangfor.com @8.8.8.8
```

### Verificar status

Procure:

```text
status: NOERROR
```

### Comparar as respostas

As respostas devem ser compatíveis, embora a ordem dos registros possa mudar.

Por exemplo:

```text
104.20.41.193
172.66.160.251
```

---

# 20. Verificando o caminho da consulta

O comando:

```bash
dig +trace www.sangfor.com
```

é útil para investigar a hierarquia DNS.

Porém, atenção:

`+trace` não representa necessariamente o mesmo caminho utilizado pelo seu BIND quando existe um:

```conf
type forward;
```

Para verificar o funcionamento do conditional forwarding, o teste mais importante é:

```bash
dig www.sangfor.com @IP_DO_DNS_INTERNO
```

---

# 21. `forward` x `forward only`

Existem duas opções comuns:

```conf
forward only;
```

e:

```conf
forward first;
```

## forward only

```conf
forward only;
```

O BIND utiliza somente os servidores definidos em:

```conf
forwarders
```

Se eles não responderem, a resolução falha.

## forward first

```conf
forward first;
```

O BIND tenta primeiro os servidores configurados em `forwarders`.

Se não conseguir obter uma resposta, pode tentar resolver a consulta por conta própria.

### Neste caso

Foi utilizado:

```conf
forward only;
```

porque o objetivo era determinar explicitamente que:

```text
sangfor.com
```

fosse resolvido através dos DNS definidos.

---

# 22. Não alterar o DNS global sem necessidade

Uma solução diferente seria configurar:

```conf
options {
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

Isso faria com que **diversos domínios** fossem encaminhados para o Google.

Não era esse o objetivo.

A configuração utilizada é mais específica:

```conf
zone "sangfor.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

Assim, somente:

```text
sangfor.com
```

é tratado dessa maneira.

---

# 23. Subdomínios

Uma zona:

```conf
zone "sangfor.com" IN {
```

abrange consultas como:

```text
sangfor.com
www.sangfor.com
portal.sangfor.com
mail.sangfor.com
qualquercoisa.sangfor.com
```

Portanto, não é necessário criar uma regra separada para:

```text
www.sangfor.com
```

se a intenção é encaminhar todo o domínio.

---

# 24. Cuidados em ambiente corporativo

Antes de aplicar esse tipo de alteração em produção:

1. Fazer backup do arquivo.

```bash
cp /etc/named/conditional.conf \
   /etc/named/conditional.conf.bak
```

Ou:

```bash
cp /etc/named.conf /etc/named.conf.bak
```

2. Validar a configuração:

```bash
named-checkconf
```

3. Utilizar:

```bash
rndc reload
```

em vez de reiniciar desnecessariamente o serviço.

4. Testar a resolução:

```bash
dig www.sangfor.com @IP_DO_DNS
```

5. Confirmar que outros domínios continuam funcionando.

Exemplo:

```bash
dig www.google.com @IP_DO_DNS
```

e:

```bash
dig algum-dominio-interno.infraero.gov.br @IP_DO_DNS
```

---

# 25. Comandos essenciais para memorizar

### Verificar processo

```bash
ps -ef | grep -E '[n]amed|[b]ind'
```

### Verificar configuração

```bash
named-checkconf
```

### Recarregar BIND

```bash
rndc reload
```

### Testar DNS específico

```bash
dig www.sangfor.com @IP_DO_DNS
```

### Testar Google DNS

```bash
dig www.sangfor.com @8.8.8.8
```

### Investigar hierarquia DNS

```bash
dig www.sangfor.com +trace
```

### Consultar configuração

```bash
cat /etc/named/conditional.conf
```

---

# 26. Checklist

Use este checklist quando precisar repetir o procedimento:

```text
[ ] Identificar o serviço DNS
[ ] Confirmar que é BIND/named
[ ] Verificar /etc/named.conf
[ ] Verificar se existe conditional.conf
[ ] Verificar se conditional.conf está incluído
[ ] Criar o arquivo, se necessário
[ ] Adicionar a zona
[ ] Definir type forward
[ ] Definir forward only
[ ] Definir os forwarders
[ ] Executar named-checkconf
[ ] Corrigir qualquer erro
[ ] Executar rndc reload
[ ] Testar com dig
[ ] Testar diretamente no DNS interno
[ ] Testar outros domínios
[ ] Confirmar funcionamento no navegador
```

---

# 27. Configuração final utilizada

A configuração principal estudada neste procedimento é:

```conf
zone "sangfor.com" IN {
        type forward;
        forward only;
        forwarders {
                8.8.8.8;
                8.8.4.4;
        };
};
```

Com isso:

```text
Consulta:
www.sangfor.com

        ↓

DNS BIND Infraero

        ↓

Conditional Forwarding

        ↓

8.8.8.8 / 8.8.4.4

        ↓

Resposta DNS

        ↓

Cliente
```

---

# 28. Conceito principal para guardar

O ponto mais importante deste procedimento é:

> **Conditional Forwarding permite definir que consultas destinadas a um domínio específico sejam encaminhadas para servidores DNS específicos, sem alterar a resolução dos demais domínios.**

No exemplo estudado:

```text
sangfor.com
      ↓
8.8.8.8
8.8.4.4
```

Enquanto o restante continua seguindo a configuração normal do DNS corporativo.
