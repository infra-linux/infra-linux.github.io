# Sessão: TCP/IP

## Sumário

1. [TCP/IP](#1-tcpip)
2. [Endereço IP](#2-endereço-ip)
   - IPv4
   - IP Público, IP Privado e Loopback
3. [Máscara de Rede](#3-máscara-de-rede)
4. [CIDR](#4-cidr)
5. [Rede, Host e Broadcast](#5-rede-host-e-broadcast)
6. [Sub-redes (Subnetting)](#6-sub-redes-subnetting)
7. [MAC Address](#7-mac-address)
8. [ARP](#8-arp)
9. [Gateway](#9-gateway)
10. [Rotas](#10-rotas)
11. [ICMP](#11-icmp)
12. [TCP e UDP](#12-tcp-e-udp)
13. [Portas](#13-portas)
14. [IPv6](#14-ipv6)

---

## Tópicos complementares (opcionais)

- **NAT** — sugerido logo após "IP Privado", explica como IPs privados acessam a internet.
- **DNS** — se o escopo for mais amplo, pode fechar a sessão como tradução de nomes para IP.
- **VLAN** — relevante para público de administração de redes corporativas, complementa "Rede, host e broadcast" e "Sub-redes".

---

## 1. TCP/IP

### O que é

TCP/IP (Transmission Control Protocol / Internet Protocol) é o conjunto de protocolos que serve como base para a comunicação de dados na internet e na grande maioria das redes locais atuais. Não se trata de um único protocolo, mas de uma **pilha (stack)** de protocolos que trabalham em camadas, cada uma responsável por uma parte específica do processo de comunicação entre dispositivos.

O nome "TCP/IP" vem dos dois protocolos mais importantes dessa pilha:

- **IP (Internet Protocol)**: responsável por endereçar e rotear os dados entre dispositivos em diferentes redes.
- **TCP (Transmission Control Protocol)**: responsável por garantir que os dados cheguem de forma completa, ordenada e confiável.

Antes de dois dispositivos poderem trocar informações — seja um navegador acessando um site, um servidor enviando e-mails ou uma câmera IP transmitindo vídeo — é preciso que exista um conjunto de regras comuns definindo como os dispositivos são identificados, como os dados são divididos e remontados, como garantir a entrega e como encontrar o caminho até o destino. O TCP/IP resolve todos esses pontos e se tornou o padrão de fato para redes de computadores desde os anos 1980.

### O modelo em camadas

O TCP/IP é organizado em quatro camadas (em contraste com as sete camadas do modelo OSI, que é mais teórico). Cada camada usa os serviços da camada abaixo dela e oferece serviços à camada acima. Os tópicos seguintes desta sessão vão aprofundar cada uma delas.

| Camada | Função | Exemplos de protocolos |
|---|---|---|
| Aplicação | Fornece serviços diretamente aos programas do usuário | HTTP, HTTPS, FTP, SMTP, DNS, SSH |
| Transporte | Garante (ou não) a entrega dos dados entre origem e destino | TCP, UDP |
| Internet (Rede) | Endereça e roteia os pacotes entre redes diferentes | IP (IPv4/IPv6), ICMP |
| Acesso à Rede (Enlace/Física) | Transmite os dados fisicamente no meio de rede | Ethernet, Wi-Fi (802.11), ARP |

### Como os dados trafegam: encapsulamento

Quando um dispositivo envia dados, cada camada adiciona suas próprias informações de controle (cabeçalhos) aos dados da camada superior. Esse processo é chamado de **encapsulamento**:

```
Dados da aplicação
      ↓
[Cabeçalho TCP/UDP] + Dados            → Segmento/Datagrama
      ↓
[Cabeçalho IP] + Segmento              → Pacote
      ↓
[Cabeçalho de Enlace] + Pacote         → Quadro (Frame)
      ↓
Transmissão física (bits)
```

No destino, o processo inverso ocorre (desencapsulamento), removendo cada cabeçalho camada por camada até que os dados originais cheguem à aplicação.

### Resumo

- TCP/IP é a pilha de protocolos que fundamenta a comunicação em redes modernas, incluindo a internet.
- É organizado em quatro camadas: Aplicação, Transporte, Internet e Acesso à Rede.
- IP cuida do endereçamento e roteamento; TCP garante entrega confiável dos dados.
- Os próximos tópicos desta sessão vão detalhar cada peça desse quebra-cabeça, começando pelo endereçamento IP.

## 2. Endereço IP

Um endereço IP é um identificador numérico atribuído a cada dispositivo conectado a uma rede que utiliza o protocolo IP. Ele funciona de forma semelhante a um endereço postal: permite que os dados saibam exatamente para onde ir e de onde voltar.

Todo dispositivo que se comunica em uma rede IP — computador, celular, servidor, roteador, impressora — precisa ter um endereço IP, único dentro daquele escopo de rede.

### IPv4

O IPv4 (Internet Protocol version 4) é a versão mais usada até hoje. Um endereço IPv4 é formado por **32 bits**, geralmente representados em **notação decimal com pontos**, divididos em 4 octetos (blocos de 8 bits cada):

```
192.168.0.1
```

Cada octeto vai de `0` a `255` (2⁸ = 256 valores possíveis), totalizando cerca de **4,3 bilhões de endereços únicos** possíveis — número que já se esgotou globalmente, o que motivou a criação do IPv6.

Em binário, o exemplo acima seria:

```
11000000.10101000.00000000.00000001
   192   .   168   .    0   .    1
```

### IP Público, IP Privado e Loopback

- **IP Público**: endereço visível e roteável na internet, único no mundo. É o IP que seu provedor de internet atribui à sua conexão para que ela seja alcançável externamente.

- **IP Privado**: endereço usado dentro de redes locais (LANs), não roteável diretamente na internet. Várias redes diferentes podem usar os mesmos endereços privados sem conflito, já que eles não saem do ambiente local. As faixas reservadas para uso privado (definidas na RFC 1918) são:

  | Faixa | Intervalo |
  |---|---|
  | Classe A | `10.0.0.0` – `10.255.255.255` |
  | Classe B | `172.16.0.0` – `172.31.255.255` |
  | Classe C | `192.168.0.0` – `192.168.255.255` |

  Para que dispositivos com IP privado acessem a internet, é necessário um processo de **NAT (Network Address Translation)**, geralmente feito pelo roteador, que traduz o IP privado para o IP público da rede.

- **Loopback**: endereço especial que um dispositivo usa para se referir a si mesmo. Na faixa `127.0.0.0/8`, sendo `127.0.0.1` o mais comum (também chamado de `localhost`). É muito usado para testar serviços rodando localmente, sem depender da rede física.

---

## 3. Máscara de Rede

A máscara de rede (subnet mask) define qual parte de um endereço IP identifica a **rede** e qual parte identifica o **host** (o dispositivo específico dentro daquela rede).

Ela também é representada em 32 bits, no mesmo formato do IPv4, onde:

- Bits em `1` → identificam a porção de **rede**.
- Bits em `0` → identificam a porção de **host**.

Exemplo clássico:

```
IP:      192.168.  0.  1
Máscara: 255.255.255.  0
```

Aqui, `255.255.255.0` (em binário `11111111.11111111.11111111.00000000`) indica que os três primeiros octetos identificam a rede (`192.168.0`) e o último octeto identifica o host dentro dessa rede (de `0` a `255`).

A máscara é o que permite ao dispositivo saber: *"esse outro IP está na minha rede local, ou preciso mandar esse pacote para o gateway?"*

---

## 4. CIDR

CIDR (Classless Inter-Domain Routing) é uma notação simplificada para representar a máscara de rede, indicando quantos bits são usados para a porção de rede, em vez de escrever a máscara por extenso.

Formato: `IP/quantidade-de-bits-de-rede`

Exemplo:

```
192.168.0.0/24
```

O `/24` significa que os primeiros 24 bits (3 octetos) são de rede, e os 8 bits restantes são de host — exatamente equivalente à máscara `255.255.255.0`.

Tabela de referência rápida:

| CIDR | Máscara | Hosts utilizáveis |
|---|---|---|
| /24 | 255.255.255.0 | 254 |
| /25 | 255.255.255.128 | 126 |
| /26 | 255.255.255.192 | 62 |
| /27 | 255.255.255.224 | 30 |
| /30 | 255.255.255.252 | 2 |

O CIDR surgiu para substituir o antigo sistema de classes fixas (A, B, C), permitindo criar redes de tamanhos mais flexíveis e usar o espaço de endereços de forma mais eficiente.

---

## 5. Rede, Host e Broadcast

Dentro de qualquer bloco de endereços IP definido por uma máscara/CIDR, três endereços têm papéis especiais:

- **Endereço de Rede**: identifica a rede como um todo, e não pode ser atribuído a nenhum dispositivo. É sempre o primeiro endereço do bloco, com todos os bits de host em `0`.

- **Endereço de Host**: os endereços "no meio" do bloco, que podem ser atribuídos livremente aos dispositivos.

- **Endereço de Broadcast**: usado para enviar uma mensagem a **todos** os dispositivos da rede simultaneamente. É sempre o último endereço do bloco, com todos os bits de host em `1`.

Exemplo com a rede `192.168.0.0/24`:

| Tipo | Endereço |
|---|---|
| Rede | 192.168.0.0 |
| Primeiro host utilizável | 192.168.0.1 |
| Último host utilizável | 192.168.0.254 |
| Broadcast | 192.168.0.255 |

Por isso, de um bloco `/24` (256 endereços), apenas 254 ficam disponíveis para hosts — os outros dois (rede e broadcast) são reservados.

---

## 6. Sub-redes (Subnetting)

Subnetting é o processo de dividir uma rede maior em redes menores (sub-redes), tornando o uso de endereços IP mais eficiente e organizando melhor o tráfego.

Por que dividir uma rede?

- **Organização**: separar setores, ambientes ou tipos de dispositivo (ex: rede de servidores, rede de usuários, rede de câmeras).
- **Segurança**: isolar tráfego entre grupos diferentes.
- **Redução de domínio de broadcast**: broadcasts ficam restritos à sub-rede, evitando tráfego desnecessário em toda a rede.
- **Uso eficiente de endereços**: evitar desperdiçar um bloco `/24` inteiro (254 hosts) em um link que precisa de apenas 2 endereços, por exemplo.

Exemplo prático: dividindo `192.168.0.0/24` em quatro sub-redes `/26`:

| Sub-rede | Faixa de hosts | Broadcast |
|---|---|---|
| 192.168.0.0/26 | .1 – .62 | 192.168.0.63 |
| 192.168.0.64/26 | .65 – .126 | 192.168.0.127 |
| 192.168.0.128/26 | .129 – .190 | 192.168.0.191 |
| 192.168.0.192/26 | .193 – .254 | 192.168.0.255 |

Cada sub-rede passa a ter seu próprio endereço de rede, faixa de hosts e broadcast — funcionando como redes independentes, mas ainda dentro do bloco original.

---

## 7. MAC Address

O MAC Address (Media Access Control) é um identificador físico, único, gravado na placa de rede (interface) de cada dispositivo pelo fabricante. Diferente do IP, que pode mudar conforme a rede, o MAC normalmente é fixo (embora possa ser alterado via software em alguns casos).

Formato: 48 bits, representados em hexadecimal, geralmente separados por dois pontos ou hífen:

```
00:1A:2B:3C:4D:5E
```

Os primeiros 24 bits identificam o fabricante (OUI — Organizationally Unique Identifier), e os 24 bits restantes identificam o dispositivo especificamente.

O MAC Address atua na **camada de Acesso à Rede** (camada de enlace) e é usado para a comunicação dentro do mesmo segmento de rede local — ao contrário do IP, que serve para comunicação entre redes diferentes.

---

## 8. ARP

ARP (Address Resolution Protocol) é o protocolo responsável por **traduzir um endereço IP em um endereço MAC** dentro de uma rede local.

Quando um dispositivo precisa enviar dados para outro na mesma rede, ele sabe o IP de destino, mas para efetivamente entregar o quadro na camada de enlace, precisa saber o MAC correspondente. O processo funciona assim:

1. O dispositivo envia um **broadcast ARP Request** perguntando: *"quem tem o IP X.X.X.X? me informe seu MAC."*
2. O dispositivo dono daquele IP responde com um **ARP Reply**, informando seu MAC Address.
3. O dispositivo de origem armazena essa informação em sua **tabela ARP** (cache), evitando repetir o processo a cada pacote.

Esse mecanismo é essencial para o funcionamento local da rede e ocorre de forma transparente, sem intervenção do usuário.

---

## 9. Gateway

O gateway (também chamado de default gateway ou porta de saída padrão) é o dispositivo — geralmente um roteador — responsável por encaminhar o tráfego entre a rede local e outras redes, incluindo a internet.

Quando um dispositivo precisa se comunicar com um IP que **não está na sua própria rede/sub-rede**, ele envia o pacote para o gateway configurado, que se encarrega de rotear esse tráfego até o destino (ou até o próximo salto no caminho).

Exemplo de configuração de rede em um host:

```
IP:      192.168.0.10
Máscara: 255.255.255.0
Gateway: 192.168.0.1
```

Aqui, `192.168.0.1` normalmente é o endereço do roteador da rede local, e todo tráfego destinado a fora da faixa `192.168.0.0/24` passa por ele.

---

## 10. Rotas

Uma rota define o caminho que o tráfego de rede deve seguir para chegar a um determinado destino. Roteadores (e também sistemas operacionais) mantêm uma **tabela de rotas**, consultada a cada pacote para decidir por onde encaminhá-lo.

Cada entrada de rota geralmente contém:

- **Rede de destino** (ex: `10.0.0.0/8`)
- **Próximo salto (next-hop)**: para qual dispositivo/interface o pacote deve ser enviado
- **Métrica**: um valor que indica a "preferência" da rota, usado quando existe mais de um caminho possível

Tipos comuns de rotas:

- **Rota estática**: configurada manualmente pelo administrador, fixa até ser alterada.
- **Rota dinâmica**: aprendida automaticamente através de protocolos de roteamento (ex: OSPF, BGP, RIP), que trocam informações entre roteadores sobre os melhores caminhos disponíveis.
- **Rota padrão (default route)**: usada quando nenhuma rota mais específica é encontrada para o destino — geralmente aponta para o gateway.

---

## 11. ICMP

ICMP (Internet Control Message Protocol) é um protocolo da camada de Internet usado para **enviar mensagens de controle e diagnóstico** sobre a comunicação de rede — não é usado para transportar dados de aplicações, mas sim para relatar erros e testar conectividade.

Duas ferramentas muito conhecidas são baseadas em ICMP:

- **Ping**: envia uma mensagem `ICMP Echo Request` a um destino e aguarda uma resposta `ICMP Echo Reply`, usado para testar se um host está acessível e medir a latência.

- **Traceroute** (ou `tracert` no Windows): usa mensagens ICMP (junto com manipulação do TTL — Time To Live dos pacotes) para descobrir o caminho, salto a salto, até um destino, ajudando a identificar onde um problema de rede está ocorrendo.

Outros usos do ICMP incluem notificar quando um destino está inalcançável (`Destination Unreachable`) ou quando o tempo de vida de um pacote expirou (`Time Exceeded`).

---

## 12. TCP e UDP

TCP e UDP são os dois principais protocolos da **camada de Transporte**, responsáveis por levar os dados entre aplicações de origem e destino — mas com filosofias bem diferentes.

### TCP (Transmission Control Protocol)

- **Orientado a conexão**: estabelece uma conexão antes de trocar dados, através do **handshake de três vias**:
  1. **SYN** — o cliente solicita a conexão.
  2. **SYN-ACK** — o servidor confirma e também solicita sincronização.
  3. **ACK** — o cliente confirma, e a conexão é estabelecida.
- **Confiável**: garante que os dados cheguem completos, na ordem correta, com confirmação de recebimento e retransmissão em caso de perda.
- **Mais "pesado"**: todo esse controle adiciona overhead, tornando o TCP mais lento que o UDP.
- **Usado em**: navegação web (HTTP/HTTPS), e-mail (SMTP), transferência de arquivos (FTP), acesso remoto (SSH) — qualquer cenário onde a integridade dos dados é essencial.

### UDP (User Datagram Protocol)

- **Não orientado a conexão**: envia os dados diretamente, sem estabelecer conexão prévia.
- **Sem garantias**: não confirma recebimento, não garante ordem, não retransmite pacotes perdidos.
- **Mais rápido e leve**: menor overhead, ideal para aplicações sensíveis a atraso.
- **Usado em**: streaming de vídeo/áudio, chamadas VoIP, jogos online, DNS — cenários onde velocidade importa mais que garantia total de entrega.

---

## 13. Portas

Uma porta é um número usado para identificar **qual serviço ou aplicação específica**, dentro de um dispositivo, deve receber determinado tráfego de rede. Enquanto o IP identifica o dispositivo, a porta identifica o processo dentro dele.

Portas vão de `0` a `65535`, divididas em faixas:

| Faixa | Uso |
|---|---|
| 0 – 1023 | Portas conhecidas (well-known), reservadas para serviços padronizados |
| 1024 – 49151 | Portas registradas, usadas por aplicações específicas |
| 49152 – 65535 | Portas dinâmicas/privadas, usadas temporariamente por conexões de saída |

Algumas portas conhecidas:

| Porta | Protocolo | Serviço |
|---|---|---|
| 20/21 | TCP | FTP |
| 22 | TCP | SSH |
| 25 | TCP | SMTP (e-mail) |
| 53 | TCP/UDP | DNS |
| 80 | TCP | HTTP |
| 443 | TCP | HTTPS |
| 3389 | TCP | RDP (Remote Desktop) |

A combinação **IP + Porta** forma um **socket**, que identifica de forma única uma comunicação específica — por isso é possível, por exemplo, rodar vários serviços diferentes no mesmo servidor (mesmo IP), cada um respondendo em uma porta distinta.

---

## 14. IPv6

O IPv6 (Internet Protocol version 6) foi criado para resolver a limitação de endereços do IPv4, já esgotados globalmente diante do crescimento de dispositivos conectados à internet.

Principais características:

- **128 bits** de endereçamento (contra 32 bits do IPv4), oferecendo um espaço praticamente inesgotável de endereços — cerca de 340 undecilhões de combinações possíveis.
- **Notação hexadecimal**, dividida em 8 grupos de 16 bits, separados por dois-pontos:

  ```
  2001:0db8:85a3:0000:0000:8a2e:0370:7334
  ```

- **Abreviação**: sequências de zeros podem ser simplificadas com `::` (uma única vez por endereço):

  ```
  2001:db8:85a3::8a2e:370:7334
  ```

Outras diferenças em relação ao IPv4:

- Não usa mais o conceito tradicional de broadcast — o IPv6 usa **multicast** e **anycast** para cenários semelhantes.
- Simplifica o cabeçalho do pacote, tornando o roteamento mais eficiente.
- Possui suporte nativo a configuração automática de endereços (SLAAC) e maior foco em segurança (IPsec previsto desde a especificação original).

A adoção do IPv6 ainda coexiste com o IPv4 na maioria das redes atuais, em um modelo chamado **dual stack**, até que a transição completa aconteça.