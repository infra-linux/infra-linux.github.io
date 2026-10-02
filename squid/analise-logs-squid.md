---
layout: default
title: Análise de logs do Squid
---

# Análise de logs do Squid
{:.no_toc}

Este tutorial explica como ler os logs do Squid e extrair informações úteis, como clientes mais ativos, sites mais acessados, bloqueios e consumo de banda.

Pré-requisito: Squid instalado e funcionando. Veja [Instalação e configuração básica]({{ 'squid/instalacao-configuracao-squid.html' | relative_url }}).

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---

## Arquivos de log

Os logs ficam em `/var/log/squid/`:

| Arquivo | Conteúdo |
|---|---|
| `access.log` | Cada requisição atendida pelo proxy |
| `cache.log` | Mensagens do serviço, avisos e erros |
| `store.log` | Objetos armazenados e removidos do cache (pode estar desativado) |

Acompanhar em tempo real:

```bash
tail -f /var/log/squid/access.log
```

---

## Formato do access.log

O formato padrão (`squid`) registra uma linha por requisição, com campos separados por espaço:

```text
1759400000.123    250 192.0.2.10 TCP_MISS/200 15230 GET http://www.example.com/ usuario1 HIER_DIRECT/203.0.113.5 text/html
```

| Campo | Posição | Exemplo | Descrição |
|---|---|---|---|
| Horário | 1 | `1759400000.123` | Data e hora em formato epoch |
| Duração | 2 | `250` | Tempo da requisição em milissegundos |
| Cliente | 3 | `192.0.2.10` | IP de origem |
| Resultado/Status | 4 | `TCP_MISS/200` | Resultado no cache e código HTTP |
| Bytes | 5 | `15230` | Tamanho da resposta enviada ao cliente |
| Método | 6 | `GET` | Método HTTP |
| URL | 7 | `http://www.example.com/` | Endereço solicitado |
| Usuário | 8 | `usuario1` | Usuário autenticado (`-` quando não há) |
| Destino | 9 | `HIER_DIRECT/203.0.113.5` | Como e de onde o conteúdo foi obtido |
| Tipo | 10 | `text/html` | Tipo do conteúdo |

Em conexões HTTPS, o método é `CONNECT` e a URL aparece como `dominio:443`, pois o Squid não vê o conteúdo.

---

## Converter o horário

Converter um horário epoch para data legível:

```bash
date -d @1759400000
```

Acompanhar o log já com o horário convertido:

```bash
tail -f /var/log/squid/access.log | perl -pe 's/\d+\.\d+/localtime($&)/e'
```

---

## Principais códigos de resultado

| Código | Significado |
|---|---|
| `TCP_HIT` | Resposta entregue pelo cache |
| `TCP_MEM_HIT` | Resposta entregue pelo cache em memória |
| `TCP_MISS` | Conteúdo buscado no servidor de origem |
| `TCP_REFRESH_UNMODIFIED` | Cache revalidado, conteúdo não mudou |
| `TCP_TUNNEL` | Túnel `CONNECT` (HTTPS) |
| `TCP_DENIED` | Acesso negado por ACL ou falta de autenticação |

Os códigos HTTP mais comuns depois da barra:

| Status | Significado |
|---|---|
| `200` | Sucesso |
| `403` | Acesso negado por regra do Squid |
| `407` | Autenticação necessária |
| `502`/`503` | Falha ao conectar no destino |
| `504` | Tempo esgotado no destino |

---

## Consultas úteis

### Clientes mais ativos

```bash
awk '{print $3}' /var/log/squid/access.log | sort | uniq -c | sort -rn | head -10
```

### Sites mais acessados

```bash
awk '{print $7}' /var/log/squid/access.log \
  | sed -E 's#^[a-z]+://##; s#/.*##; s#:[0-9]+$##' \
  | sort | uniq -c | sort -rn | head -10
```

### Distribuição por resultado

```bash
awk '{print $4}' /var/log/squid/access.log | sort | uniq -c | sort -rn
```

### Acessos bloqueados

```bash
grep TCP_DENIED /var/log/squid/access.log | tail -20
```

### Bloqueios por cliente

```bash
grep TCP_DENIED /var/log/squid/access.log | awk '{print $3}' | sort | uniq -c | sort -rn | head
```

### Consumo de banda por cliente (em MB)

```bash
awk '{b[$3]+=$5} END {for (c in b) printf "%.1f MB %s\n", b[c]/1048576, c}' \
  /var/log/squid/access.log | sort -rn | head -10
```

### Consumo por usuário autenticado (em MB)

```bash
awk '$8 != "-" {b[$8]+=$5} END {for (u in b) printf "%.1f MB %s\n", b[u]/1048576, u}' \
  /var/log/squid/access.log | sort -rn | head -10
```

### Taxa de acerto do cache

```bash
awk '{ if ($4 ~ /HIT/) h++; t++ } END { printf "%.1f%% (%d de %d)\n", h*100/t, h, t }' \
  /var/log/squid/access.log
```

### Acessos de um cliente específico

```bash
grep '^[0-9.]* *[0-9]* 192.0.2.10 ' /var/log/squid/access.log | tail -20
```

### Acessos a um domínio

```bash
grep 'exemplo.com' /var/log/squid/access.log | tail -20
```

---

## Analisar o cache.log

Mostrar avisos e erros recentes:

```bash
grep -E 'WARNING|ERROR|FATAL' /var/log/squid/cache.log | tail -20
```

Para aumentar o detalhe do registro durante um diagnóstico, ajuste no `squid.conf`:

```text
debug_options ALL,1 28,3
```

O nível `28` refere-se às ACLs e ajuda a entender qual regra foi aplicada. Volte para `ALL,1` ao terminar, pois níveis altos aumentam muito o volume de log.

---

## Rotação de logs

No Rocky Linux, o pacote do Squid instala uma configuração do logrotate em `/etc/logrotate.d/squid`. Para conferir:

```bash
cat /etc/logrotate.d/squid
```

Rotacionar manualmente:

```bash
squid -k rotate
```

O número de arquivos mantidos é controlado pela diretiva `logfile_rotate` no `squid.conf`.

---

## Personalizar o formato do log

É possível definir um formato próprio, por exemplo com data legível:

```text
logformat leitura %tl %>a %un %Ss/%>Hs %<st %rm %ru
access_log daemon:/var/log/squid/access_leitura.log leitura
```

| Código | Valor registrado |
|---|---|
| `%tl` | Horário local legível |
| `%>a` | IP do cliente |
| `%un` | Usuário |
| `%Ss/%>Hs` | Resultado no cache e status HTTP |
| `%<st` | Bytes enviados ao cliente |
| `%rm` | Método |
| `%ru` | URL |

Depois de alterar, valide e recarregue:

```bash
squid -k parse
squid -k reconfigure
```

> Se mudar o formato do `access_log` principal, as consultas desta página que usam posição de campo (`$3`, `$7`) deixam de funcionar. Prefira manter o formato padrão no log principal e criar um segundo arquivo para o formato novo.

---

## Ferramentas de relatório

Para relatórios em HTML ou painéis, existem ferramentas que leem o `access.log`, como SARG, SquidAnalyzer e GoAccess. Parte delas não está nos repositórios padrão do Rocky Linux e pode exigir o repositório EPEL ou instalação manual. Verifique a disponibilidade antes de adotar uma delas.

---

## Problemas comuns

| Sintoma | Causa provável | O que verificar |
|---|---|---|
| `access.log` vazio | Nenhum cliente está usando o proxy ou o log está em outro caminho | Diretiva `access_log` e uso do proxy pelos clientes |
| Disco enchendo | Logs sem rotação | `/etc/logrotate.d/squid` e `logfile_rotate` |
| Consultas com resultados estranhos | Formato de log alterado | Formato do `access_log` e posição dos campos |
| Muitos `TCP_DENIED/407` | Clientes sem credenciais ou com senha incorreta | Configuração do proxy no cliente e autenticação |
| Horários ilegíveis | Formato epoch do log padrão | Converter com `date -d @` ou usar `logformat` |