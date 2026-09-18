---
layout: default
title: Instalação de Aplicativos (.exe) no Windows
---

# Instalação de Aplicativos (.exe) no Windows


## Sumário

1. [Objetivo](#objetivo)
2. [1. Baixar o instalador](#1-baixar-o-instalador)
3. [2. Abrir PowerShell como Administrador](#2-abrir-powershell-como-administrador)
4. [3. Acessar a pasta do instalador](#3-acessar-a-pasta-do-instalador)
5. [4. Executar o instalador](#4-executar-o-instalador)
6. [5. Concluir a instalação](#5-concluir-a-instalacao)
7. [6. Validar a instalação](#6-validar-a-instalacao)
8. [7. Executar o aplicativo](#7-executar-o-aplicativo)
9. [Exemplo](#exemplo)
10. [Boas práticas](#boas-praticas)

---
## Objetivo

Procedimento padrão para instalação de aplicativos distribuídos em formato `.exe`.

---

## 1. Baixar o instalador

Baixar o arquivo `.exe` do site oficial do fabricante.

Exemplo:

```text
Downloads\programa.exe
```

---

## 2. Abrir PowerShell como Administrador

* Menu Iniciar
* Procurar por **PowerShell**
* Clicar em **Executar como Administrador**

---

## 3. Acessar a pasta do instalador

```powershell
cd $env:USERPROFILE\Downloads
```

Verificar o arquivo:

```powershell
dir *.exe
```

---

## 4. Executar o instalador

```powershell
.\programa.exe
```

ou

```powershell
Start-Process .\programa.exe -Verb RunAs
```

---

## 5. Concluir a instalação

Seguir o assistente:

* Next
* Accept License
* Install
* Finish

---

## 6. Validar a instalação

Verificar se o aplicativo foi instalado:

```powershell
Get-StartApps | findstr "nome"
```

ou pesquisar pelo programa no Menu Iniciar.

---

## 7. Executar o aplicativo

Abrir pelo Menu Iniciar ou executar diretamente:

```powershell
"C:\Program Files\Aplicativo\programa.exe"
```

---

## Exemplo

Instalação do Markdown Monster:

```powershell
cd $env:USERPROFILE\Downloads

Start-Process .\MarkdownMonsterSetup.exe -Verb RunAs
```

Após a instalação:

```text
Menu Iniciar → Markdown Monster
```

---

## Boas práticas

* Utilizar instaladores obtidos no site oficial.
* Executar sempre como Administrador.
* Manter os aplicativos atualizados.
* Preferir versões homologadas pela empresa quando aplicável.

