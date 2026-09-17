---
layout: default
title: TCP/IP
---

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
15. [NAT](#15-nat)
16. [DNS](#16-dns)
17. [VLAN](#17-vlan)

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
| Acesso à Rede (Enlace/Física) | Transmite quadros pelo meio físico e controla a entrega no enlace local | Ethernet, Wi-Fi (802.11) |

O modelo em camadas é uma forma de organizar responsabilidades, não uma separação rígida de equipamentos. Por exemplo, um roteador normalmente trabalha com IP, mas também precisa participar do enlace local e pode executar funções de camadas superiores, como firewall e NAT. O ARP é um protocolo auxiliar que relaciona a camada Internet à camada de enlace em redes IPv4; ele não é um protocolo de transporte.

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

### Exemplo prático: acessar um site

Ao acessar `https://www.exemplo.com`:

1. O DNS encontra o endereço IP associado ao nome.
2. A aplicação abre uma comunicação na porta `443` usando TCP ou, em versões modernas do HTTP, QUIC sobre UDP.
3. O IP verifica se o destino está na rede local. Se não estiver, entrega o pacote ao gateway.
4. O ARP descobre o MAC do gateway, caso ele ainda não esteja no cache.
5. Os dados são encapsulados em segmentos, pacotes e quadros até serem enviados pelo meio físico.

## 2. Endereço IP

Um endereço IP é um identificador numérico atribuído a cada dispositivo conectado a uma rede que utiliza o protocolo IP. Ele funciona de forma semelhante a um endereço postal: permite que os dados saibam exatamente para onde ir e de onde voltar.

Todo dispositivo que se comunica em uma rede IP — computador, celular, servidor, roteador, impressora — precisa ter um endereço IP, único dentro daquele escopo de rede.

### IPv4

O IPv4 (Internet Protocol version 4) é a versão mais usada até hoje. Um endereço IPv4 é formado por **32 bits**, geralmente representados em **notação decimal com pontos**, divididos em 4 octetos (blocos de 8 bits cada):

```
192.168.0.1
```

Cada octeto vai de `0` a `255` (2⁸ = 256 valores possíveis), totalizando cerca de **4,3 bilhões de endereços únicos** possíveis — número que já se esgotou globalmente, o que motivou a criação do IPv6.

O endereço, isoladamente, não informa o tamanho da rede. `192.168.0.10/24` e `192.168.0.10/16`, por exemplo, pertencem a redes diferentes porque a máscara muda a interpretação dos bits.

Em binário, o exemplo acima seria:

```
11000000.10101000.00000000.00000001
   192   .   168   .    0   .    1
```

### IP Público, IP Privado e Loopback

- **IP Público**: endereço visível e roteável na internet, único no mundo. É o IP que seu provedor de internet atribui à sua conexão. Um IP público pode estar protegido por firewall ou NAT e, portanto, não necessariamente aceita conexões iniciadas pela internet.

- **IP Privado**: endereço usado dentro de redes locais (LANs), não roteável diretamente na internet. Várias redes diferentes podem usar os mesmos endereços privados sem conflito, já que eles não saem do ambiente local. As faixas reservadas para uso privado (definidas na RFC 1918) são:

  | Faixa | Intervalo |
  |---|---|
  | Classe A | `10.0.0.0` – `10.255.255.255` |
  | Classe B | `172.16.0.0` – `172.31.255.255` |
  | Classe C | `192.168.0.0` – `192.168.255.255` |

  Para que dispositivos com IP privado acessem a internet, é necessário um processo de **NAT (Network Address Translation)**, geralmente feito pelo roteador, que traduz o IP privado para o IP público da rede.

- **Loopback**: endereço especial que um dispositivo usa para se referir a si mesmo. Na faixa `127.0.0.0/8`, sendo `127.0.0.1` o mais comum (também chamado de `localhost`). É muito usado para testar serviços rodando localmente, sem depender da rede física.

Outras faixas importantes são `169.254.0.0/16` (endereço automático local, normalmente usado quando o host não recebeu DHCP) e `0.0.0.0` (endereço não especificado, usado por exemplo para indicar "todas as interfaces" ao iniciar um serviço).

### Exemplo prático

```bash
# Linux: exibe os endereços configurados
ip address

# Windows PowerShell
Get-NetIPAddress -AddressFamily IPv4
```

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

### Exemplo prático: descobrir a rede de um host

Para `192.168.10.37` com máscara `255.255.255.0` (`/24`), os primeiros 24 bits são a rede. Portanto:

```text
Rede:      192.168.10.0
Host:      37
Broadcast: 192.168.10.255
```

Com máscara `/26` (`255.255.255.192`), o tamanho de cada bloco é 64. O endereço `192.168.10.37` fica no bloco `192.168.10.0/26`, com hosts de `.1` a `.62` e broadcast `.63`.

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

Para calcular rapidamente os hosts utilizáveis em uma sub-rede IPv4 comum, use $2^h - 2$, onde $h$ é o número de bits de host. O desconto de dois endereços é para rede e broadcast. Há exceções, como `/31`, usado em alguns enlaces ponto a ponto, e `/32`, que representa um único endereço.

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

Dividir uma rede em sub-redes não cria isolamento de segurança por si só. Para impedir que a rede de usuários acesse a rede de servidores, por exemplo, ainda é necessário usar roteamento controlado, ACLs ou firewall. Em redes com VLANs, normalmente cada VLAN recebe uma sub-rede própria e o roteador ou switch de camada 3 faz o roteamento entre elas.

### Exemplo prático: calcular sub-redes

Para dividir `192.168.10.0/24` em redes com no máximo 30 hosts, são necessários 5 bits de host, pois $2^5 - 2 = 30$. A máscara será `/27` (`255.255.255.224`) e o incremento entre redes será 32: `.0`, `.32`, `.64`, `.96` e assim por diante.

---

## 7. MAC Address

O MAC Address (Media Access Control) é um identificador físico, único, gravado na placa de rede (interface) de cada dispositivo pelo fabricante. Diferente do IP, que pode mudar conforme a rede, o MAC normalmente é fixo (embora possa ser alterado via software em alguns casos).

Formato: 48 bits, representados em hexadecimal, geralmente separados por dois pontos ou hífen:

```
00:1A:2B:3C:4D:5E
```

Os primeiros 24 bits identificam o fabricante (OUI — Organizationally Unique Identifier), e os 24 bits restantes identificam o dispositivo especificamente.

O MAC Address atua na **camada de Acesso à Rede** (camada de enlace) e é usado para a comunicação dentro do mesmo segmento de rede local — ao contrário do IP, que serve para comunicação entre redes diferentes.

Em redes modernas, o MAC pode ser alterado por software e alguns sistemas usam endereços aleatórios em redes Wi-Fi para reduzir rastreamento. Portanto, ele não deve ser tratado como uma identidade permanente ou como mecanismo de autenticação.

### Exemplo prático

```bash
# Linux
ip link

# Windows PowerShell
Get-NetAdapter | Select-Object Name, MacAddress, Status
```

---

## 8. ARP

ARP (Address Resolution Protocol) é o protocolo responsável por **traduzir um endereço IP em um endereço MAC** dentro de uma rede local.

Quando um dispositivo precisa enviar dados para outro na mesma rede, ele sabe o IP de destino, mas para efetivamente entregar o quadro na camada de enlace, precisa saber o MAC correspondente. O processo funciona assim:

1. O dispositivo envia um **broadcast ARP Request** perguntando: *"quem tem o IP X.X.X.X? me informe seu MAC."*
2. O dispositivo dono daquele IP responde com um **ARP Reply**, informando seu MAC Address.
3. O dispositivo de origem armazena essa informação em sua **tabela ARP** (cache), evitando repetir o processo a cada pacote.

Esse mecanismo é essencial para o funcionamento local da rede e ocorre de forma transparente, sem intervenção do usuário.

O ARP só é necessário para o próximo salto no enlace local. Se o destino estiver fora da sub-rede, o host não procura o MAC do servidor remoto: procura o MAC do gateway e envia o pacote IP original para ele.

### Exemplo prático

```bash
# Linux: consultar e limpar o cache ARP de uma interface
ip neigh show
sudo ip neigh flush all

# Windows
arp -a
```

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

O gateway precisa estar em uma rede diretamente conectada ao host. Configurar um gateway inexistente ou fora da sub-rede normalmente impede a comunicação externa, mesmo que o endereço IP e a máscara estejam corretos.

### Exemplo prático

```bash
# Linux: mostrar o gateway padrão
ip route show default

# Windows
Get-NetRoute -DestinationPrefix '0.0.0.0/0'
```

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

Quando várias rotas correspondem ao mesmo destino, a tabela normalmente prefere a rota com o prefixo mais específico (por exemplo, `/24` vence `/16`). Se a especificidade for igual, a métrica e as regras do sistema ajudam a escolher o caminho.

### Exemplo prático

```bash
# Linux: visualizar todas as rotas e a rota escolhida para um destino
ip route
ip route get 8.8.8.8

# Windows
route print
Test-NetConnection 8.8.8.8
```

---

## 11. ICMP

ICMP (Internet Control Message Protocol) é um protocolo da camada de Internet usado para **enviar mensagens de controle e diagnóstico** sobre a comunicação de rede — não é usado para transportar dados de aplicações, mas sim para relatar erros e testar conectividade.

Duas ferramentas muito conhecidas são baseadas em ICMP:

- **Ping**: envia uma mensagem `ICMP Echo Request` a um destino e aguarda uma resposta `ICMP Echo Reply`, usado para testar se um host está acessível e medir a latência.

- **Traceroute** (ou `tracert` no Windows): usa mensagens ICMP (junto com manipulação do TTL — Time To Live dos pacotes) para descobrir o caminho, salto a salto, até um destino, ajudando a identificar onde um problema de rede está ocorrendo.

Outros usos do ICMP incluem notificar quando um destino está inalcançável (`Destination Unreachable`) ou quando o tempo de vida de um pacote expirou (`Time Exceeded`). Um firewall pode bloquear ICMP sem que o host esteja desligado; por isso, a ausência de resposta ao ping não prova sozinha que o serviço ou o computador está indisponível.

O `traceroute` do Linux pode usar UDP, ICMP ou TCP, conforme as opções. O `tracert` do Windows usa ICMP Echo por padrão. Em ambos, respostas `Time Exceeded` revelam os saltos intermediários, quando os roteadores permitem esse diagnóstico.

### Exemplo prático

```bash
# Linux
ping -c 4 192.168.0.1
traceroute example.com

# Windows PowerShell
Test-Connection 192.168.0.1 -Count 4
tracert example.com
```

---

## 12. TCP e UDP

TCP e UDP são os dois principais protocolos da **camada de Transporte**, responsáveis por levar os dados entre aplicações de origem e destino — mas com filosofias bem diferentes.

### TCP (Transmission Control Protocol)

- **Orientado a conexão**: estabelece uma conexão antes de trocar dados, através do **handshake de três vias**:
  1. **SYN** — o cliente solicita a conexão.
  2. **SYN-ACK** — o servidor confirma e também solicita sincronização.
  3. **ACK** — o cliente confirma, e a conexão é estabelecida.
- **Confiável**: garante que os dados cheguem completos, na ordem correta, com confirmação de recebimento e retransmissão em caso de perda.
- **Mais controle**: cabeçalhos, confirmações, controle de fluxo e controle de congestionamento adicionam overhead. Isso não significa que TCP sempre tenha menor velocidade; a escolha depende da rede e da aplicação.
- **Usado em**: navegação web (HTTP/HTTPS), e-mail (SMTP), transferência de arquivos (FTP), acesso remoto (SSH) — qualquer cenário onde a integridade dos dados é essencial.

### UDP (User Datagram Protocol)

- **Não orientado a conexão**: envia os dados diretamente, sem estabelecer conexão prévia.
- **Sem garantias**: não confirma recebimento, não garante ordem, não retransmite pacotes perdidos.
- **Menor overhead**: pode reduzir latência e consumo de recursos, ideal para aplicações sensíveis a atraso, mas não é automaticamente mais rápido em todas as redes.
- **Usado em**: streaming de vídeo/áudio, chamadas VoIP, jogos online e DNS — cenários em que baixa latência ou mensagens pequenas podem ser mais importantes que retransmitir todos os dados. Aplicações modernas também podem implementar confiabilidade sobre UDP, como o QUIC.

TCP trata os dados como um fluxo contínuo de bytes; UDP preserva os limites de cada datagrama. Nenhum dos dois, sozinho, cifra os dados: a proteção costuma vir de protocolos como TLS, SSH ou IPsec.

### Exemplo prático: observar a conexão

```bash
# Linux: conexões TCP/UDP e processos associados
ss -tulpen

# Windows PowerShell
Get-NetTCPConnection
```

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

Em uma conexão TCP, o fluxo é identificado pelo conjunto origem (IP e porta) e destino (IP e porta), além do protocolo. A porta de origem costuma ser efêmera, enquanto o servidor escuta em uma porta conhecida. Uma porta aberta também pode estar acessível apenas na rede local, bloqueada pelo firewall ou sem nenhum serviço útil atrás dela.

### Exemplo prático: testar uma porta

```bash
# Linux, se o netcat estiver instalado
nc -vz servidor.exemplo 443

# Windows PowerShell
Test-NetConnection servidor.exemplo -Port 443
```

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

O IPv6 não elimina a necessidade de firewall. IPsec é suportado pelo protocolo, mas não significa que todo tráfego IPv6 seja automaticamente cifrado. Também não há ARP no IPv6: a descoberta de vizinhos usa ICMPv6 Neighbor Discovery.

Faixas importantes incluem `::1` (loopback), `fe80::/10` (link-local, obrigatório em interfaces IPv6) e `fc00::/7` (endereços locais únicos, equivalentes em propósito geral aos endereços privados). A faixa `2001:db8::/32` é reservada para documentação e exemplos, não para uso em produção.

### Exemplo prático

```bash
# Linux
ip -6 address
ping -6 -c 4 ::1

# Windows PowerShell
Get-NetIPAddress -AddressFamily IPv6
Test-Connection -TargetName ::1 -Count 4
```

A adoção do IPv6 ainda coexiste com o IPv4 na maioria das redes atuais, em um modelo chamado **dual stack**, até que a transição completa aconteça.

---

## 15. NAT

NAT (Network Address Translation) altera endereços IP, e às vezes portas, enquanto um pacote atravessa um roteador. O uso mais comum é permitir que vários dispositivos com endereços privados compartilhem um único IP público.

No cenário mais comum, chamado **PAT** ou NAT overload, o roteador registra uma associação como:

```text
192.168.0.10:51500 -> 203.0.113.20:40001
```

Quando a resposta retorna para `203.0.113.20:40001`, o roteador consulta essa associação e entrega o tráfego ao host privado correto. Isso permite conexões de saída, mas normalmente impede conexões iniciadas da internet para dentro da rede. Para publicar um serviço, pode ser necessário configurar redirecionamento de porta (port forwarding), firewall e DNS.

NAT não é um substituto para firewall. Ele pode dificultar conexões de entrada por causa do estado das traduções, mas a política de segurança deve ser definida explicitamente no firewall.

### Exemplo prático

```bash
# Linux: observar conexões e regras NAT (requer permissões e ferramentas instaladas)
sudo nft list ruleset

# Ver o IP público percebido por um serviço externo
curl https://api.ipify.org
```

---

## 16. DNS

DNS (Domain Name System) traduz nomes legíveis, como `www.exemplo.com`, em endereços IP. Ele também pode publicar outros dados, como servidores de e-mail e políticas de domínio.

O cliente consulta um resolvedor DNS, normalmente fornecido pelo roteador, provedor ou organização. Se o resolvedor não tiver a resposta em cache, ele consulta a hierarquia DNS: servidores raiz, servidores do domínio de topo (como `.com`) e o servidor autoritativo do domínio.

Registros comuns:

| Registro | Função |
|---|---|
| `A` | Nome para endereço IPv4 |
| `AAAA` | Nome para endereço IPv6 |
| `CNAME` | Alias para outro nome |
| `MX` | Servidor responsável por e-mail |
| `NS` | Servidores autoritativos do domínio |
| `TXT` | Texto e políticas, como SPF |

DNS não é criptografia. Consultas tradicionais podem ser observadas ou alteradas no caminho; mecanismos como DoT e DoH protegem o transporte entre cliente e resolvedor, sem transformar o DNS em um mecanismo geral de autenticação.

### Exemplo prático

```bash
# Linux e Windows com nslookup
nslookup example.com
nslookup -type=MX example.com

# Linux, quando o dig estiver instalado
dig example.com A
```

---

## 17. VLAN

VLAN (Virtual Local Area Network) divide logicamente uma rede Ethernet em vários domínios de broadcast, mesmo quando os dispositivos usam os mesmos switches físicos. Cada VLAN costuma representar uma rede IP diferente, como usuários, servidores, voz ou visitantes.

Em um link **access**, o quadro pertence a uma única VLAN. Em um link **trunk**, quadros de várias VLANs são identificados com uma tag 802.1Q para atravessar o enlace entre switches, roteadores ou hipervisores.

Dispositivos em VLANs diferentes não se comunicam apenas por estarem conectados ao mesmo switch. Para permitir comunicação entre elas, é necessário roteamento inter-VLAN, normalmente em um switch de camada 3 ou roteador, com ACLs ou firewall definindo o que é permitido.

### Exemplo prático de planejamento

```text
VLAN 10 - Usuários:   192.168.10.0/24
VLAN 20 - Servidores: 192.168.20.0/24
VLAN 30 - Visitantes: 192.168.30.0/24
```

O planejamento acima cria separação de broadcast e organização de endereços. Ele só cria isolamento de segurança quando o equipamento de camada 3 aplica regras impedindo, por exemplo, que visitantes acessem servidores.