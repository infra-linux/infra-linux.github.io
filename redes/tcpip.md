---
layout: default
title: Redes-tcpip
---

# 🌐 TCP/IP

## Introdução

**TCP/IP** é o conjunto de protocolos utilizado para comunicação entre dispositivos em uma rede.

Ele é a base da comunicação em:

* servidores Linux e Windows;
* Internet;
* redes corporativas;
* containers;
* máquinas virtuais;
* Kubernetes;
* aplicações e serviços de rede.

O modelo TCP/IP organiza a comunicação em diferentes camadas, cada uma responsável por uma parte do processo.

---

## 1. O que é TCP/IP?

TCP/IP não é apenas um protocolo.

É um **conjunto de protocolos** que trabalham juntos para permitir a comunicação entre dispositivos.

Os dois principais são:

```text
TCP → Transmission Control Protocol
IP  → Internet Protocol
```

O IP é responsável principalmente pelo **endereçamento e encaminhamento dos pacotes**.

O TCP é responsável pela **comunicação confiável entre aplicações**.

Exemplo:

```text
Cliente
10.0.10.20
     │
     │ TCP
     │
     ▼
Servidor
10.0.20.30
```

---

# 2. Endereço IP

O endereço IP identifica uma interface de rede dentro de uma rede IP.

Exemplo:

```text
10.0.17.23
```

Podemos consultar os endereços configurados no Linux com:

```bash
ip addr
```

Ou de forma mais resumida:

```bash
ip -br addr
```

Exemplo:

```text
ens3    UP    10.0.27.72/24
```

Nesse caso:

```text
Interface → ens3
IP        → 10.0.27.72
Prefixo   → /24
```

---

# 3. IPv4

O IPv4 utiliza endereços de **32 bits**.

Exemplo:

```text
192.168.1.100
```

Um endereço IPv4 possui quatro octetos:

```text
192 . 168 . 1 . 100
 │     │    │    │
 └─────┴────┴────┴── 4 octetos
```

Cada octeto pode variar de:

```text
0 a 255
```

---

# 4. Máscara de rede

A máscara determina qual parte do endereço representa a **rede** e qual parte representa o **host**.

Exemplo:

```text
192.168.1.100/24
```

O `/24` significa que os primeiros 24 bits pertencem à rede.

A máscara equivalente é:

```text
255.255.255.0
```

Assim:

```text
Rede: 192.168.1.0
Host: 100
```

---

# 5. CIDR

CIDR é uma forma compacta de representar a máscara de rede.

Exemplos:

```text
/8
/16
/24
/25
/26
/27
/28
```

Alguns exemplos:

| CIDR  | Máscara         |
| ----- | --------------- |
| `/8`  | 255.0.0.0       |
| `/16` | 255.255.0.0     |
| `/24` | 255.255.255.0   |
| `/25` | 255.255.255.128 |
| `/26` | 255.255.255.192 |
| `/27` | 255.255.255.224 |
| `/28` | 255.255.255.240 |

---

# 6. Rede, host e broadcast

Considere:

```text
192.168.10.50/24
```

A rede é:

```text
192.168.10.0
```

O primeiro endereço normalmente representa a rede.

O último:

```text
192.168.10.255
```

é o broadcast da rede `/24`.

Os hosts ficam entre:

```text
192.168.10.1
até
192.168.10.254
```

Portanto:

```text
Rede       → 192.168.10.0
Hosts      → 192.168.10.1–254
Broadcast  → 192.168.10.255
```

---

# 7. Gateway

O **gateway padrão** é o dispositivo utilizado para alcançar outras redes.

Exemplo:

```text
Servidor
10.0.27.72
     │
     ▼
Gateway
10.0.16.1
     │
     ▼
Outras redes / Internet
```

Para consultar:

```bash
ip route
```

Exemplo:

```text
default via 10.0.16.1 dev ens3
```

Significa:

```text
default → qualquer destino sem rota específica
gateway → 10.0.16.1
interface → ens3
```

---

# 8. Rotas

Uma rota determina **por onde um pacote deve sair para chegar ao destino**.

Consulte:

```bash
ip route
```

Para descobrir especificamente como o Linux chegará a determinado IP:

```bash
ip route get 10.0.19.20
```

Exemplo:

```text
10.0.19.20 via 10.0.16.1 dev ens3 src 10.0.27.72
```

Podemos interpretar:

```text
Destino  → 10.0.19.20
Gateway  → 10.0.16.1
Interface → ens3
Origem   → 10.0.27.72
```

Esse é um dos comandos mais úteis para troubleshooting de rede.

---

# 9. TCP

O TCP trabalha na camada de transporte.

Ele estabelece uma conexão antes de transmitir os dados.

O processo inicial é:

```text
Cliente                  Servidor

   │──── SYN ────────────>│
   │<─── SYN/ACK ─────────│
   │──── ACK ─────────────>│
   │                       │
   │   conexão estabelecida│
```

Esse processo é chamado de:

**Three-Way Handshake.**

---

# 10. UDP

O UDP também trabalha na camada de transporte, mas não estabelece uma conexão como o TCP.

```text
Cliente
   │
   │──── Datagramas ─────> Servidor
```

Não existe garantia de:

* entrega;
* ordem;
* retransmissão.

Exemplos de serviços que utilizam UDP:

```text
DNS  → 53/UDP
NTP  → 123/UDP
SNMP → 161/UDP
```

---

# 11. TCP x UDP

| Característica    | TCP   | UDP   |
| ----------------- | ----- | ----- |
| Conexão           | Sim   | Não   |
| Handshake         | Sim   | Não   |
| Retransmissão     | Sim   | Não   |
| Ordenação         | Sim   | Não   |
| Controle de fluxo | Sim   | Não   |
| Overhead          | Maior | Menor |

---

# 12. Portas

Um IP identifica o host, enquanto a porta identifica o serviço.

Exemplo:

```text
10.0.19.20:443
```

Significa:

```text
IP    → 10.0.19.20
Porta → 443
```

Com protocolo:

```text
10.0.19.20:443/TCP
```

Portas conhecidas:

```text
22    SSH
53    DNS
80    HTTP
443   HTTPS
445   SMB
3389  RDP
5432  PostgreSQL
3306  MySQL
```

---

# 13. MAC Address

O endereço MAC identifica uma interface de rede na camada de enlace.

Exemplo:

```text
00:1A:2B:3C:4D:5E
```

Consultar no Linux:

```bash
ip link
```

Ou:

```bash
ip -br link
```

Exemplo:

```text
ens3    UP    00:50:56:AA:BB:CC
```

---

# 14. ARP

O **ARP (Address Resolution Protocol)** relaciona um endereço IPv4 a um endereço MAC dentro da rede local.

Exemplo:

```text
Quem possui 10.0.16.1?

10.0.16.1 → MAC xx:xx:xx:xx:xx:xx
```

Consultar a tabela ARP:

```bash
ip neigh
```

Exemplo:

```text
10.0.16.1 dev ens3 lladdr 00:11:22:33:44:55 REACHABLE
```

---

# 15. ICMP

O **ICMP** é utilizado principalmente para mensagens de controle e diagnóstico de rede.

O exemplo mais conhecido é o `ping`.

```bash
ping -c 4 10.0.16.1
```

O `ping` utiliza ICMP Echo Request e Echo Reply.

```text
Cliente ── Echo Request ──> Servidor
Cliente <── Echo Reply ──── Servidor
```

⚠️ **Importante:**

Um `ping` sem resposta não significa necessariamente que o servidor está offline.

O firewall pode bloquear ICMP enquanto permite:

```text
TCP/443
TCP/22
UDP/53
```

---

# 16. Loopback

O endereço de loopback permite que o computador se comunique consigo mesmo.

IPv4:

```text
127.0.0.1
```

Nome:

```text
localhost
```

Exemplo:

```bash
ping -c 4 127.0.0.1
```

Também podemos verificar:

```bash
ip addr show lo
```

---

# 17. IP privado

Algumas faixas IPv4 são reservadas para redes privadas.

### Classe privada `10.0.0.0/8`

```text
10.0.0.0 – 10.255.255.255
```

### `172.16.0.0/12`

```text
172.16.0.0 – 172.31.255.255
```

### `192.168.0.0/16`

```text
192.168.0.0 – 192.168.255.255
```

Esses endereços normalmente são utilizados em:

* redes corporativas;
* redes domésticas;
* VLANs;
* servidores;
* containers;
* máquinas virtuais.

---

# 18. IPv6

O IPv6 foi criado para substituir o IPv4 e utiliza endereços de **128 bits**.

Exemplo:

```text
2001:db8::1
```

Consultar:

```bash
ip -6 addr
```

Rotas IPv6:

```bash
ip -6 route
```

Loopback IPv6:

```text
::1
```

---

# 19. Modelo TCP/IP

Uma forma simples de visualizar o modelo TCP/IP:

```text
┌─────────────────────────────┐
│ Aplicação                   │
│ HTTP HTTPS DNS SSH SMTP     │
├─────────────────────────────┤
│ Transporte                  │
│ TCP / UDP                   │
├─────────────────────────────┤
│ Internet                    │
│ IP / ICMP                   │
├─────────────────────────────┤
│ Acesso à rede               │
│ Ethernet / Wi-Fi / ARP      │
└─────────────────────────────┘
```

Quando uma aplicação envia dados, eles passam por essas camadas.

---

# 20. Encapsulamento

Os dados são encapsulados conforme descem pelas camadas.

```text
Aplicação
   ↓
Segmento TCP / Datagrama UDP
   ↓
Pacote IP
   ↓
Quadro Ethernet
```

De forma simplificada:

```text
HTTP
 ↓
TCP
 ↓
IP
 ↓
Ethernet
```

No destino, ocorre o processo inverso.

---

# 21. Comandos essenciais no Linux

### Ver interfaces e IPs

```bash
ip addr
```

### Forma resumida

```bash
ip -br addr
```

### Ver interfaces

```bash
ip link
```

### Ver rotas

```bash
ip route
```

### Ver rota para um destino

```bash
ip route get 8.8.8.8
```

### Ver vizinhos/ARP

```bash
ip neigh
```

### Testar conectividade

```bash
ping -c 4 10.0.16.1
```

### Ver portas locais

```bash
ss -tulnp
```

### Testar porta TCP

```bash
nc -vz 10.0.19.20 443
```

### Ver caminho até um destino

```bash
traceroute 10.0.19.20
```

ou:

```bash
tracepath 10.0.19.20
```

---

# 22. Troubleshooting TCP/IP

Quando uma máquina não consegue acessar um serviço, siga uma sequência lógica:

```text
1. Interface
      ↓
2. IP
      ↓
3. Máscara/prefixo
      ↓
4. Gateway
      ↓
5. Rota
      ↓
6. DNS
      ↓
7. IP destino
      ↓
8. Porta
      ↓
9. Firewall
      ↓
10. Serviço
```

### Exemplo

Problema:

```text
Não consigo acessar https://servidor.infraero.gov.br
```

Primeiro:

```bash
ip -br addr
```

Depois:

```bash
ip route
```

Depois:

```bash
dig servidor.infraero.gov.br
```

Depois:

```bash
ip route get IP_DO_SERVIDOR
```

Depois:

```bash
nc -vz IP_DO_SERVIDOR 443
```

E finalmente:

```bash
curl -vk https://servidor.infraero.gov.br
```

Assim conseguimos separar **problema de interface, IP, rota, DNS, firewall, porta ou aplicação**.

---

# 🧠 Resumo

```text
IP       → identifica o destino
MAC      → identifica a interface na rede local
TCP      → transporte confiável
UDP      → transporte sem conexão
ICMP     → diagnóstico/controle
ARP      → IPv4 ↔ MAC
Porta    → identifica o serviço
Gateway  → saída para outras redes
Rota     → define o caminho
```

### Comandos que você deve dominar

```bash
ip addr
ip -br addr
ip route
ip route get DESTINO
ip neigh
ping
ss
nc
traceroute
tracepath
```

> 💡 **Regra de ouro do troubleshooting TCP/IP:**
> **Antes de investigar a aplicação, descubra quem é o destino, qual IP ele possui, qual rota será utilizada, qual porta está envolvida e qual protocolo está sendo usado.**