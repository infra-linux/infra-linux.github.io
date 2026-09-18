---
layout: default
title: WatchGuard_Collection
---

# Coleta de Logs do WatchGuard NDR Collection Agent (Linux)

Este procedimento descreve como gerar o pacote oficial de diagnóstico do **WatchGuard NDR Collection Agent** em servidores Linux. O pacote é utilizado pelo suporte da WatchGuard para análise de falhas de comunicação, heartbeat, captura de fluxos e conectividade.

> **Importante**
>
> O script **não coleta logs de uma data específica**. Ele gera um pacote contendo o estado atual do Collection Agent, incluindo logs existentes, configurações, testes de conectividade e uma captura temporária de rede.

---


## Sumário

1. [1. Acessar o servidor](#1-acessar-o-servidor)
2. [2. Confirmar o host](#2-confirmar-o-host)
3. [3. Gerar o pacote de diagnóstico](#3-gerar-o-pacote-de-diagnostico)
4. [4. Localizar o pacote gerado](#4-localizar-o-pacote-gerado)
5. [5. Baixar o arquivo para o computador](#5-baixar-o-arquivo-para-o-computador)
6. [Verificações opcionais](#verificacoes-opcionais)
7. [Verificar os processos do Collection Agent](#verificar-os-processos-do-collection-agent)
8. [Verificar o Heartbeat](#verificar-o-heartbeat)
9. [Procurar erros de Heartbeat](#procurar-erros-de-heartbeat)
10. [Verificar os logs disponíveis](#verificar-os-logs-disponiveis)
11. [Envio ao suporte](#envio-ao-suporte)
12. [Checklist](#checklist)
13. [Referências](#referencias)

---
# 1. Acessar o servidor

Conecte-se via SSH ao servidor desejado.

```bash
ssh root@<hostname>
```

Exemplo:

```bash
ssh root@s-sdsn224.infraero.gov.br
```

---

# 2. Confirmar o host

Verifique se você está conectado ao servidor correto.

```bash
hostname -f
```

Exemplo:

```text
s-sdsn224.infraero.gov.br
```

---

# 3. Gerar o pacote de diagnóstico

Execute:

```bash
sudo /opt/collector/scripts/collectorDiagnostics.sh
```

O script executa automaticamente:

- Verificação do firewall
- Verificação dos serviços Nfcapd e Sfcapd
- Teste de conectividade com a WatchGuard
- Captura temporária de pacotes (`tcpdump`)
- Coleta dos logs do Collection Agent
- Geração do pacote oficial de diagnóstico

Exemplo de saída:

```text
UFW is disabled
Nfcapd is running
Sfcapd is running
Last upload occurred at Wed Jul 29 04:45:49 PM UTC 2026
Connectivity test to WatchGuard succeeded
Starting diagnostic data gathering...
```

Aguarde até o retorno do prompt antes de continuar.

---

# 4. Localizar o pacote gerado

Liste os arquivos mais recentes:

```bash
ls -lht /opt/collector/staging/ | head
```

O primeiro arquivo será semelhante a:

```text
WGC-1-xxxxxxxxxxxxxxxx-s-sdsn224-diagnostics-YYYYMMDDHHMM.lzo
```

Exemplo:

```text
WGC-1-16b7386a21134870b485-s-sdsn224-diagnostics-202607311843.lzo
```

Esse é o arquivo que deverá ser enviado ao suporte da WatchGuard.

---

# 5. Baixar o arquivo para o computador

No PowerShell do Windows execute:

```powershell
scp root@<hostname>:/opt/collector/staging/<arquivo>.lzo "$env:USERPROFILE\Downloads\"
```

Exemplo:

```powershell
scp root@s-sdsn224.infraero.gov.br:/opt/collector/staging/WGC-1-16b7386a21134870b485-s-sdsn224-diagnostics-202607311843.lzo "$env:USERPROFILE\Downloads\"
```

Após informar a senha, a cópia será iniciada.

Exemplo:

```text
100%
```

O arquivo ficará disponível em:

```text
C:\Users\<usuario>\Downloads\
```

---

# Verificações opcionais

## Verificar os processos do Collection Agent

```bash
ps -ef | grep -Ei 'nfcapd|sfcapd|ndr_' | grep -v grep
```

Exemplo:

```text
/opt/collector/bin/nfcapd
/opt/collector/bin/sfcapd
/opt/collector/bin/ndr_nf_aggregator
```

---

## Verificar o Heartbeat

```bash
tail -50 /opt/collector/logs/ndr_heartbeat.log
```

Resultado esperado:

```text
Sending Heartbeat
heartbeat upload status 0
```

O código **status 0** indica envio realizado com sucesso.

---

## Procurar erros de Heartbeat

```bash
grep -nEi 'status [^0]$|error|fail|timeout|unable|refused' \
/opt/collector/logs/ndr_heartbeat.log
```

Caso o comando não retorne nenhuma saída, significa que não foram encontrados erros no log de heartbeat.

---

## Verificar os logs disponíveis

```bash
ls -lht /opt/collector/logs/
```

Os principais arquivos são:

- `ndr_heartbeat.log`
- `ndr_nf_aggregator.log`
- `ndr_s3upload.log`
- `collection.log`
- `ndr_monitor_capture.log`
- `syslog_cross_check.log`

---

# Envio ao suporte

Anexe o arquivo `.lzo` gerado ao chamado ou e-mail enviado ao suporte da WatchGuard.

Exemplo:

```text
WGC-1-16b7386a21134870b485-s-sdsn224-diagnostics-202607311843.lzo
```

Cada servidor gera um pacote próprio. Caso mais de um Collection Agent apresente problemas, repita o procedimento em todos os hosts afetados.

---

# Checklist

- [ ] Conectar via SSH ao servidor.
- [ ] Confirmar o hostname.
- [ ] Executar `collectorDiagnostics.sh`.
- [ ] Localizar o arquivo em `/opt/collector/staging/`.
- [ ] Baixar o arquivo via `scp`.
- [ ] Anexar o arquivo `.lzo` ao chamado da WatchGuard.

---

# Referências

- WatchGuard NDR Collection Agent for Linux
- WatchGuard Support – Troubleshoot Collection Agent
- Documentação oficial do WatchGuard NDR