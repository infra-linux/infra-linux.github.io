---
layout: default
title: Redes-DNS
---

# DNS


## Sumário

1. [1. O que é DNS?](#1-o-que-e-dns)
2. [Exemplo](#exemplo)
3. [2. O que acontece quando você acessa um site?](#2-o-que-acontece-quando-voce-acessa-um-site)
4. [3. Como descobrir qual DNS o Linux está usando?](#3-como-descobrir-qual-dns-o-linux-esta-usando)
5. [4. `dig` — principal ferramenta para diagnóstico DNS](#4-dig-principal-ferramenta-para-diagnostico-dns)
6. [5. Descobrir qual DNS respondeu](#5-descobrir-qual-dns-respondeu)
7. [6. Consultar um DNS específico](#6-consultar-um-dns-especifico)
8. [7. Exemplo real de resolução interna](#7-exemplo-real-de-resolucao-interna)
9. [8. Consultar registros específicos](#8-consultar-registros-especificos)
10. [9. DNS reverso](#9-dns-reverso)
11. [10. Principais tipos de registros DNS](#10-principais-tipos-de-registros-dns)
12. [11. CNAME](#11-cname)
13. [12. MX](#12-mx)
14. [13. NS](#13-ns)
15. [14. SOA](#14-soa)
16. [15. TTL](#15-ttl)
17. [16. Caches DNS](#16-caches-dns)
18. [17. DNS recursivo x autoritativo](#17-dns-recursivo-x-autoritativo)
19. [DNS recursivo](#dns-recursivo)
20. [DNS autoritativo](#dns-autoritativo)
21. [18. Testando DNS com `nslookup`](#18-testando-dns-com-nslookup)
22. [19. `resolvectl`](#19-resolvectl)
23. [20. DNS não significa conectividade](#20-dns-nao-significa-conectividade)
24. [21. Testando conectividade com `ping`](#21-testando-conectividade-com-ping)
25. [22. Testando uma porta TCP](#22-testando-uma-porta-tcp)
26. [23. Testando HTTP/HTTPS com `curl`](#23-testando-httphttps-com-curl)
27. [24. Entendendo `curl`](#24-entendendo-curl)
28. [25. Diagnóstico em camadas](#25-diagnostico-em-camadas)
29. [26. Diagnóstico de erro DNS](#26-diagnostico-de-erro-dns)
30. [`NXDOMAIN`](#nxdomain)
31. [`SERVFAIL`](#servfail)
32. [`REFUSED`](#refused)
33. [Timeout](#timeout)
34. [27. Comparando DNS interno e externo](#27-comparando-dns-interno-e-externo)
35. [28. Arquivo `/etc/hosts`](#28-arquivo-etchosts)
36. [29. Troubleshooting rápido de DNS](#29-troubleshooting-rapido-de-dns)
37. [1. Verifique a configuração DNS](#1-verifique-a-configuracao-dns)
38. [2. Resolva o nome](#2-resolva-o-nome)
39. [3. Consulte diretamente o DNS](#3-consulte-diretamente-o-dns)
40. [4. Teste o IP](#4-teste-o-ip)
41. [5. Teste a porta](#5-teste-a-porta)
42. [6. Teste a aplicação](#6-teste-a-aplicacao)
43. [30. Exemplo completo de diagnóstico](#30-exemplo-completo-de-diagnostico)
44. [DNS](#dns)
45. [Conectividade](#conectividade)
46. [Porta](#porta)
47. [HTTP](#http)
48. [31. Checklist de DNS](#31-checklist-de-dns)
49. [Comandos essenciais](#comandos-essenciais)
50. [Regra de ouro](#regra-de-ouro)

---
## 1. O que é DNS?

**DNS (Domain Name System)** é o sistema responsável por traduzir nomes de domínio em endereços IP.

Em vez de acessar:

```text
https://10.0.19.20
```

podemos acessar:

```text
https://pgd.infraero.gov.br
```

O DNS faz a associação:

```text
pgd.infraero.gov.br → 10.0.19.20
```

Essa tradução é chamada de **resolução de nomes**.

### Exemplo

```text
Cliente Linux
     │
     │ "Qual é o IP de pgd.infraero.gov.br?"
     ▼
Servidor DNS
     │
     │ "10.0.19.20"
     ▼
Cliente Linux
     │
     ▼
10.0.19.20
```

DNS normalmente utiliza:

* **UDP/53** — consultas DNS comuns;
* **TCP/53** — transferências de zona e consultas que precisam de TCP;
* **DoT (DNS over TLS)** — normalmente TCP/853;
* **DoH (DNS over HTTPS)** — normalmente HTTPS/443.

---

# 2. O que acontece quando você acessa um site?

Quando executamos:

```bash
curl https://pgd.infraero.gov.br
```

primeiro precisamos descobrir o IP de `pgd.infraero.gov.br`.

O sistema consulta o DNS configurado.

Por exemplo:

```text
pgd.infraero.gov.br
        ↓
DNS
        ↓
10.0.19.20
        ↓
TCP/443
        ↓
Servidor HTTPS
```

Por isso, **DNS e conectividade IP são coisas diferentes**.

Se o DNS funcionar, isso não significa que o servidor esteja acessível.

Podemos ter:

```text
DNS funcionando
       +
TCP bloqueado
       =
aplicação inacessível
```

---

# 3. Como descobrir qual DNS o Linux está usando?

Em sistemas Linux tradicionais, verifique:

```bash
cat /etc/resolv.conf
```

Exemplo:

```text
nameserver 10.0.27.23
search infraero.gov.br
```

Nesse caso:

```text
nameserver 10.0.27.23
```

significa que o sistema utiliza `10.0.27.23` como servidor DNS.

O parâmetro:

```text
search infraero.gov.br
```

define um domínio de pesquisa.

Por exemplo, dependendo da configuração, uma consulta para:

```bash
ping servidor01
```

pode resultar em uma tentativa de resolução de:

```text
servidor01.infraero.gov.br
```

---

# 4. `dig` — principal ferramenta para diagnóstico DNS

No Linux, uma das melhores ferramentas para investigar DNS é o `dig`.

Instalação no Debian/Ubuntu:

```bash
sudo apt install dnsutils
```

Uso básico:

```bash
dig google.com
```

Uma resposta pode apresentar:

```text
;; ANSWER SECTION:
google.com.    300    IN    A    142.250.x.x
```

O registro:

```text
A
```

representa um endereço IPv4.

---

# 5. Descobrir qual DNS respondeu

No final da saída do `dig` existe uma linha semelhante a:

```text
;; SERVER: 10.0.27.23#53(10.0.27.23)
```

Isso informa:

```text
DNS utilizado: 10.0.27.23
Porta:         53
Protocolo:     UDP
```

Isso é muito útil durante troubleshooting.

---

# 6. Consultar um DNS específico

Podemos escolher exatamente qual servidor DNS queremos consultar:

```bash
dig @10.0.27.23 pgd.infraero.gov.br
```

A sintaxe é:

```text
dig @DNS domínio
```

Exemplo:

```bash
dig @8.8.8.8 google.com
```

ou:

```bash
dig @1.1.1.1 google.com
```

Isso permite comparar servidores DNS diferentes.

---

# 7. Exemplo real de resolução interna

Em uma rede corporativa, podemos ter:

```bash
dig @10.0.27.23 pgd.infraero.gov.br
```

Resultado:

```text
;; ANSWER SECTION:
pgd.infraero.gov.br.    3600    IN    A    10.0.19.20
```

Isso significa:

```text
pgd.infraero.gov.br
        ↓
10.0.19.20
```

Observe que `10.0.19.20` é um endereço IP privado.

Portanto, esse nome pode existir somente no **DNS interno da organização**.

Um DNS público, como o Google ou Cloudflare, pode não conhecer esse registro.

---

# 8. Consultar registros específicos

Podemos especificar o tipo de registro:

```bash
dig google.com A
```

IPv4:

```bash
dig google.com A
```

IPv6:

```bash
dig google.com AAAA
```

Servidor de e-mail:

```bash
dig google.com MX
```

Servidores DNS autoritativos:

```bash
dig google.com NS
```

Informações de texto:

```bash
dig google.com TXT
```

DNS reverso:

```bash
dig -x 10.0.19.20
```

---

# 9. DNS reverso

A resolução normal é:

```text
nome → IP
```

Por exemplo:

```text
pgd.infraero.gov.br → 10.0.19.20
```

O DNS reverso faz o contrário:

```text
IP → nome
```

Com:

```bash
dig -x 10.0.19.20
```

podemos receber:

```text
20.19.0.10.in-addr.arpa.    PTR    pgd.infraero.gov.br.
```

O registro utilizado nesse caso é:

```text
PTR
```

Um IP pode inclusive possuir mais de um nome DNS.

Exemplo:

```text
10.0.19.20
├── pgd.infraero.gov.br
└── pgd.hml.infraero.gov.br
```

Isso é possível porque DNS permite múltiplos registros `PTR`, embora o uso de múltiplos PTR possa causar problemas dependendo da aplicação.

---

# 10. Principais tipos de registros DNS

| Registro | Função                       |
| -------- | ---------------------------- |
| A        | Nome → IPv4                  |
| AAAA     | Nome → IPv6                  |
| CNAME    | Alias de outro nome          |
| MX       | Servidores de e-mail         |
| NS       | Servidores DNS autoritativos |
| PTR      | IP → nome                    |
| TXT      | Informações textuais         |
| SOA      | Informações da zona DNS      |
| SRV      | Localização de serviços      |

---

# 11. CNAME

Um `CNAME` cria um alias.

Exemplo:

```text
www.exemplo.com → web.exemplo.com
```

Consulta:

```bash
dig www.exemplo.com CNAME
```

Pode retornar:

```text
www.exemplo.com.    IN    CNAME    web.exemplo.com.
```

---

# 12. MX

Registros `MX` indicam os servidores responsáveis pelo recebimento de e-mails.

Exemplo:

```bash
dig exemplo.com MX
```

Resultado hipotético:

```text
exemplo.com.    IN    MX    10 mail.exemplo.com.
```

O número:

```text
10
```

é a prioridade.

Quanto menor o valor, maior a prioridade.

---

# 13. NS

Mostra os servidores DNS responsáveis pela zona:

```bash
dig exemplo.com NS
```

Exemplo:

```text
exemplo.com.    IN    NS    ns1.exemplo.com.
exemplo.com.    IN    NS    ns2.exemplo.com.
```

---

# 14. SOA

O registro `SOA` contém informações importantes sobre a zona DNS:

```bash
dig exemplo.com SOA
```

Ele normalmente contém informações como:

* servidor DNS principal;
* responsável pela zona;
* número de série;
* intervalo de atualização;
* tempo de retry;
* expiração;
* TTL mínimo.

---

# 15. TTL

TTL significa **Time To Live**.

Exemplo:

```text
google.com.    300    IN    A    142.250.x.x
```

O valor:

```text
300
```

é o TTL em segundos.

Isso indica por quanto tempo uma resposta pode permanecer armazenada em cache.

Exemplo:

```text
300 segundos = 5 minutos
3600 segundos = 1 hora
86400 segundos = 24 horas
```

---

# 16. Caches DNS

O DNS utiliza cache para evitar consultas repetidas.

Exemplo:

```text
Cliente
   │
   ▼
DNS
   │
   ├── Cache? SIM
   │
   └── retorna imediatamente
```

Quando não existe no cache:

```text
Cliente
   │
   ▼
DNS local
   │
   ▼
DNS superior
   │
   ▼
Servidor autoritativo
```

Isso explica por que uma alteração DNS pode não aparecer imediatamente para todos os usuários.

---

# 17. DNS recursivo x autoritativo

### DNS recursivo

Recebe uma consulta do cliente e procura a resposta.

Exemplo:

```text
Cliente → DNS corporativo → Internet
```

### DNS autoritativo

É responsável pela informação oficial de uma zona.

Por exemplo:

```text
infraero.gov.br
```

pode possuir servidores autoritativos responsáveis pelos registros dessa zona.

Um servidor pode exercer funções diferentes dependendo da arquitetura.

---

# 18. Testando DNS com `nslookup`

Outra ferramenta bastante conhecida:

```bash
nslookup google.com
```

Também podemos especificar o DNS:

```bash
nslookup google.com 8.8.8.8
```

Embora `nslookup` seja útil, para troubleshooting mais detalhado normalmente é preferível utilizar:

```bash
dig
```

---

# 19. `resolvectl`

Em sistemas que utilizam `systemd-resolved`:

```bash
resolvectl status
```

pode mostrar:

```text
DNS Servers:
10.0.27.23
```

Também podemos consultar um domínio:

```bash
resolvectl query google.com
```

---

# 20. DNS não significa conectividade

Este é um dos conceitos mais importantes para troubleshooting.

Imagine:

```bash
dig pgd.infraero.gov.br
```

retornando:

```text
10.0.19.20
```

Isso prova que:

```text
DNS → funcionando
```

Mas ainda não sabemos se:

```text
10.0.19.20:443
```

está acessível.

Precisamos testar separadamente.

---

# 21. Testando conectividade com `ping`

```bash
ping -c 4 10.0.19.20
```

O resultado pode ser:

```text
4 packets transmitted, 4 received, 0% packet loss
```

Isso demonstra que o host responde a ICMP.

**Importante:** ausência de resposta ao ping não significa necessariamente que o servidor esteja fora do ar.

Firewalls podem bloquear ICMP.

---

# 22. Testando uma porta TCP

Use:

```bash
nc -vz 10.0.19.20 443
```

Se aparecer:

```text
Connection to 10.0.19.20 443 port [tcp/https] succeeded!
```

temos:

```text
DNS       → OK
IP        → alcançável
TCP/443   → acessível
```

Esse teste é muito mais específico que o `ping`.

---

# 23. Testando HTTP/HTTPS com `curl`

```bash
curl -I https://pgd.infraero.gov.br
```

Podemos receber:

```text
HTTP/1.1 200 OK
Server: Apache
Content-Type: text/html
```

Agora sabemos que:

```text
DNS
 ↓
IP
 ↓
TCP/443
 ↓
TLS
 ↓
HTTP
 ↓
Aplicação
```

estão funcionando até aquele ponto.

---

# 24. Entendendo `curl`

Consulta simples:

```bash
curl https://example.com
```

Mostrar apenas cabeçalhos:

```bash
curl -I https://example.com
```

Ignorar validação do certificado:

```bash
curl -k https://example.com
```

Mostrar detalhes da conexão:

```bash
curl -v https://example.com
```

Combinação muito útil para diagnóstico:

```bash
curl -vk https://example.com
```

---

# 25. Diagnóstico em camadas

Quando um sistema está indisponível, não comece assumindo que o problema é DNS.

Investigue em camadas:

```text
1. DNS
     ↓
2. Rota
     ↓
3. IP/ICMP
     ↓
4. TCP/porta
     ↓
5. TLS
     ↓
6. HTTP
     ↓
7. Aplicação
```

Exemplo:

```bash
dig pgd.infraero.gov.br
```

↓

```bash
ping -c 4 10.0.19.20
```

↓

```bash
nc -vz 10.0.19.20 443
```

↓

```bash
curl -vk https://pgd.infraero.gov.br
```

Cada teste elimina uma camada diferente do problema.

---

# 26. Diagnóstico de erro DNS

### `NXDOMAIN`

Exemplo:

```text
status: NXDOMAIN
```

Significa que o DNS informa que o nome **não existe** naquela zona/visão DNS.

Possíveis causas:

* domínio digitado errado;
* registro inexistente;
* consulta ao DNS errado;
* problema de DNS interno;
* split DNS.

---

### `SERVFAIL`

Exemplo:

```text
status: SERVFAIL
```

Significa que o servidor DNS não conseguiu fornecer uma resposta válida.

Possíveis causas:

* falha de resolução recursiva;
* problema de encaminhamento;
* DNS autoritativo indisponível;
* DNSSEC;
* timeout;
* configuração incorreta.

---

### `REFUSED`

```text
status: REFUSED
```

O servidor recebeu a consulta, mas recusou respondê-la.

Pode ocorrer por:

* ACL;
* política de segurança;
* recursão desabilitada;
* consulta não autorizada.

---

### Timeout

Se:

```bash
dig @10.0.27.23 exemplo.com
```

fica aguardando e termina com timeout, investigue:

```text
Cliente
   ↓
rota
   ↓
firewall
   ↓
10.0.27.23:53
   ↓
DNS
```

---

# 27. Comparando DNS interno e externo

Em redes corporativas, é muito útil comparar:

```bash
dig @10.0.27.23 pgd.infraero.gov.br
```

com:

```bash
dig @8.8.8.8 pgd.infraero.gov.br
```

Pode acontecer:

```text
DNS interno:
pgd.infraero.gov.br → 10.0.19.20

DNS público:
NXDOMAIN
```

Isso pode ser totalmente normal.

A empresa pode utilizar **DNS interno/split DNS**, onde determinados nomes só existem dentro da rede corporativa.

---

# 28. Arquivo `/etc/hosts`

Antes ou além do DNS, o Linux pode resolver nomes através de:

```bash
/etc/hosts
```

Veja:

```bash
cat /etc/hosts
```

Exemplo:

```text
10.0.19.20    pgd.infraero.gov.br
```

Nesse caso, o nome pode funcionar mesmo sem uma consulta DNS.

A ordem de resolução normalmente é definida em:

```bash
/etc/nsswitch.conf
```

Procure:

```bash
grep '^hosts:' /etc/nsswitch.conf
```

Exemplo:

```text
hosts: files dns
```

Nesse caso, o sistema consulta primeiro:

```text
/etc/hosts
```

e depois o DNS.

---

# 29. Troubleshooting rápido de DNS

Quando alguém disser:

> "O site não abre."

Não altere DNS imediatamente.

Faça:

### 1. Verifique a configuração DNS

```bash
cat /etc/resolv.conf
```

### 2. Resolva o nome

```bash
dig exemplo.com
```

### 3. Consulte diretamente o DNS

```bash
dig @10.0.27.23 exemplo.com
```

### 4. Teste o IP

```bash
ping -c 4 IP
```

### 5. Teste a porta

```bash
nc -vz IP 443
```

### 6. Teste a aplicação

```bash
curl -vk https://exemplo.com
```

---

# 30. Exemplo completo de diagnóstico

Suponha:

```text
https://pgd.infraero.gov.br
```

### DNS

```bash
dig pgd.infraero.gov.br
```

Resultado:

```text
pgd.infraero.gov.br. 3600 IN A 10.0.19.20
```

### Conectividade

```bash
ping -c 4 10.0.19.20
```

Resultado:

```text
0% packet loss
```

### Porta

```bash
nc -vz 10.0.19.20 443
```

Resultado:

```text
Connection succeeded
```

### HTTP

```bash
curl -I https://pgd.infraero.gov.br
```

Resultado:

```text
HTTP/1.1 200 OK
Server: Apache
```

Conclusão:

```text
DNS       → OK
Roteamento → OK
ICMP       → OK
TCP/443    → OK
HTTPS      → OK
Aplicação  → respondeu
```

---

# 31. Checklist de DNS

Quando houver problema de resolução:

```text
[ ] /etc/resolv.conf está correto?
[ ] O DNS responde?
[ ] A porta UDP/53 está acessível?
[ ] TCP/53 está acessível quando necessário?
[ ] O domínio existe?
[ ] O registro correto existe?
[ ] Estou consultando o DNS correto?
[ ] Existe DNS interno?
[ ] Existe split DNS?
[ ] Existe entrada em /etc/hosts?
[ ] Existe cache DNS?
[ ] O problema é realmente DNS?
```

## Comandos essenciais

```bash
cat /etc/resolv.conf
```

```bash
dig dominio.com
```

```bash
dig @DNS dominio.com
```

```bash
dig dominio.com A
```

```bash
dig dominio.com AAAA
```

```bash
dig dominio.com MX
```

```bash
dig dominio.com NS
```

```bash
dig -x IP
```

```bash
nslookup dominio.com
```

```bash
resolvectl status
```

```bash
ping -c 4 IP
```

```bash
nc -vz IP PORTA
```

```bash
curl -vk https://dominio.com
```

---

## Regra de ouro

**DNS resolve nomes. DNS não garante conectividade.**

Sempre pense em camadas:

```text
NOME
 ↓
DNS
 ↓
IP
 ↓
ROTA
 ↓
FIREWALL
 ↓
PORTA TCP/UDP
 ↓
TLS
 ↓
HTTP
 ↓
APLICAÇÃO
```

Quando você aprende a testar cada camada separadamente, deixa de simplesmente perguntar **"o servidor está fora?"** e passa a identificar exatamente **em qual etapa a comunicação está falhando**.
