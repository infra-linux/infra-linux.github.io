---
layout: default
title: ACLs e regras de acesso no Squid
---

# ACLs e regras de acesso no Squid
{:.no_toc}

Este tutorial explica como o Squid controla o acesso com ACLs e regras `http_access`, com exemplos de bloqueio de sites, restrição por horário e bloqueio de tipos de arquivo.

Pré-requisito: Squid instalado e funcionando. Veja [Instalação e configuração básica]({{ 'squid/instalacao-configuracao-squid.html' | relative_url }}).

<div class="toc-title">Sumário</div>
* Sumário:
{:toc}

---

## Como funciona

O controle de acesso do Squid tem duas partes:

- **ACL:** define *o que* será comparado, como origem, destino, porta, horário ou usuário.
- **Regra de acesso (`http_access`):** define *o que fazer* (`allow` ou `deny`) quando as ACLs correspondem.

Formato básico:

```text
acl nome_da_acl tipo valor
http_access allow|deny nome_da_acl
```

Quando uma regra tem mais de uma ACL, **todas** precisam corresponder (operador E):

```text
http_access allow rede_interna horario_comercial
```

---

## Ordem das regras

As regras `http_access` são avaliadas de cima para baixo e a **primeira que corresponder é aplicada**. Por isso:

- regras de bloqueio específicas ficam **antes** das regras de liberação;
- a regra `http_access deny all` deve ser a **última**.

Ordem recomendada:

```text
http_access deny !Safe_ports
http_access deny CONNECT !SSL_ports

# bloqueios
http_access deny sites_bloqueados
http_access deny arquivos_bloqueados

# liberações
http_access allow rede_interna horario_comercial
http_access allow localhost

http_access deny all
```

---

## Principais tipos de ACL

| Tipo | Compara | Exemplo |
|---|---|---|
| `src` | IP ou rede de origem | `acl rede_interna src 192.0.2.0/24` |
| `dst` | IP ou rede de destino | `acl rede_destino dst 198.51.100.0/24` |
| `dstdomain` | Domínio de destino | `acl sites_bloqueados dstdomain .exemplo.com` |
| `dstdom_regex` | Domínio por expressão regular | `acl dominios_regex dstdom_regex -i jogos` |
| `url_regex` | URL completa por expressão regular | `acl urls_regex url_regex -i exemplo\.com/video` |
| `urlpath_regex` | Caminho da URL por expressão regular | `acl arquivos_bloqueados urlpath_regex -i \.(exe\|msi)$` |
| `port` | Porta de destino | `acl Safe_ports port 80 443` |
| `method` | Método HTTP | `acl metodo_connect method CONNECT` |
| `time` | Dia da semana e horário | `acl horario_comercial time MTWHF 08:00-18:00` |
| `proxy_auth` | Usuário autenticado | `acl usuarios proxy_auth REQUIRED` |

O ponto no início de um domínio (`.exemplo.com`) inclui o domínio e todos os seus subdomínios.

Os dias da semana na ACL `time` usam as letras `S` (domingo), `M`, `T`, `W`, `H` (quinta), `F` e `A` (sábado).

---

## Liberar redes

Para várias redes, use um arquivo de lista:

```bash
vi /etc/squid/redes_liberadas.txt
```

```text
192.0.2.0/24
198.51.100.0/24
```

No `squid.conf`:

```text
acl redes_liberadas src "/etc/squid/redes_liberadas.txt"
http_access allow redes_liberadas
```

---

## Bloquear sites

Crie a lista de domínios:

```bash
vi /etc/squid/sites_bloqueados.txt
```

```text
.exemplo.com
.redesocial.exemplo
.jogos.exemplo
```

No `squid.conf`, antes das regras de liberação:

```text
acl sites_bloqueados dstdomain "/etc/squid/sites_bloqueados.txt"
http_access deny sites_bloqueados
```

Para liberar apenas alguns domínios dentro de um bloqueio maior, crie uma lista de exceções e coloque o `allow` **antes** do `deny`:

```text
acl sites_liberados dstdomain "/etc/squid/sites_liberados.txt"
http_access allow sites_liberados
http_access deny sites_bloqueados
```

---

## Restringir por horário

```text
acl horario_comercial time MTWHF 08:00-18:00
http_access allow rede_interna horario_comercial
```

Fora desse horário, a rede interna não corresponde a nenhuma regra `allow` e cai na regra `http_access deny all`.

---

## Bloquear tipos de arquivo

```text
acl arquivos_bloqueados urlpath_regex -i \.(exe|msi|iso)$
http_access deny arquivos_bloqueados
```

> Em conexões HTTPS, o Squid vê apenas o domínio e a porta (método `CONNECT`), e não o caminho da URL. O bloqueio por `urlpath_regex` e `url_regex` só funciona em HTTPS com inspeção de tráfego (SSL Bump), que não faz parte deste tutorial. Para HTTPS, prefira o bloqueio por `dstdomain`.

---

## Mensagem de bloqueio personalizada

Para mostrar uma página de erro específica quando uma ACL bloquear o acesso, use `deny_info`:

```text
deny_info ERR_ACCESS_DENIED sites_bloqueados
```

O Squid usa os modelos de página do diretório `/usr/share/squid/errors/`.

---

## Aplicar e testar

Validar a sintaxe:

```bash
squid -k parse
```

Aplicar sem derrubar o serviço:

```bash
squid -k reconfigure
```

Testar um site bloqueado a partir de um cliente da rede:

```bash
curl -x http://IP_DO_SERVIDOR:3128 -I https://www.exemplo.com
```

Confirmar no log que a regra foi aplicada:

```bash
grep TCP_DENIED /var/log/squid/access.log | tail
```

---

## Problemas comuns

| Sintoma | Causa provável | O que verificar |
|---|---|---|
| Site bloqueado continua abrindo | Uma regra `allow` aparece antes do `deny` | Ordem das linhas `http_access` |
| Tudo é bloqueado | Falta a regra `allow` para a rede ou ela está depois do `deny all` | ACL `src` e posição da regra |
| Lista de domínios ignorada | Arquivo ilegível pelo Squid ou caminho errado | Permissões e caminho do arquivo |
| Bloqueio de URL não funciona em HTTPS | O Squid não enxerga o caminho em HTTPS | Usar `dstdomain` |
| Alteração não surte efeito | A configuração não foi recarregada | `squid -k reconfigure` |