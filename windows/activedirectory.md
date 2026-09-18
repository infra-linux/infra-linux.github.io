---
layout: default
title: Active Directory
---

# Active Directory - Comandos Úteis para Suporte e Infraestrutura


## Sumário

1. [Verificar informações de um usuário](#verificar-informacoes-de-um-usuario)
2. [Verificar grupos de um usuário](#verificar-grupos-de-um-usuario)
3. [Verificar membros de um grupo](#verificar-membros-de-um-grupo)
4. [Adicionar usuário a um grupo](#adicionar-usuario-a-um-grupo)
5. [Remover usuário de um grupo](#remover-usuario-de-um-grupo)
6. [Verificar se usuário está bloqueado](#verificar-se-usuario-esta-bloqueado)
7. [Desbloquear usuário](#desbloquear-usuario)
8. [Verificar data da última troca de senha](#verificar-data-da-ultima-troca-de-senha)
9. [Forçar troca de senha no próximo logon](#forcar-troca-de-senha-no-proximo-logon)
10. [Redefinir senha](#redefinir-senha)
11. [Verificar computadores no domínio](#verificar-computadores-no-dominio)
12. [Procurar computador específico](#procurar-computador-especifico)
13. [Verificar informações do computador](#verificar-informacoes-do-computador)
14. [Pesquisar usuário por nome](#pesquisar-usuario-por-nome)
15. [Pesquisar computador por nome](#pesquisar-computador-por-nome)
16. [Descobrir em qual OU está o usuário](#descobrir-em-qual-ou-esta-o-usuario)
17. [Verificar usuário logado na máquina local](#verificar-usuario-logado-na-maquina-local)
18. [Verificar grupos recebidos no logon](#verificar-grupos-recebidos-no-logon)
19. [Atualizar tickets Kerberos](#atualizar-tickets-kerberos)
20. [Verificar informações do domínio](#verificar-informacoes-do-dominio)
21. [Verificar controladores de domínio](#verificar-controladores-de-dominio)
22. [Verificar conectividade com o AD](#verificar-conectividade-com-o-ad)
23. [Verificar replicação do AD (Admins)](#verificar-replicacao-do-ad-admins)
24. [Comandos RSAT](#comandos-rsat)

---
## Verificar informações de um usuário

```powershell
Get-ADUser usuario.exemplo
```

Exibir propriedades adicionais:

```powershell
Get-ADUser usuario.exemplo -Properties *
```

Exibir nome, e-mail e departamento:

```powershell
Get-ADUser usuario.exemplo -Properties mail,department |
Select Name,SamAccountName,Mail,Department
```

---

## Verificar grupos de um usuário

```powershell
Get-ADPrincipalGroupMembership usuario.exemplo
```

Somente os nomes:

```powershell
Get-ADPrincipalGroupMembership usuario.exemplo |
Select Name
```

Ordenado:

```powershell
Get-ADPrincipalGroupMembership usuario.exemplo |
Sort Name |
Select Name
```

---

## Verificar membros de um grupo

```powershell
Get-ADGroupMember "GrupoAcessoPadrao"
```

Exibir apenas logins:

```powershell
Get-ADGroupMember "GrupoAcessoPadrao" |
Select SamAccountName
```

Procurar usuário dentro do grupo:

```powershell
Get-ADGroupMember "GrupoAcessoPadrao" |
Where-Object {$_.SamAccountName -eq "usuario.exemplo"}
```

---

## Adicionar usuário a um grupo

```powershell
Add-ADGroupMember `
-Identity "GrupoAcessoPadrao" `
-Members "usuario.exemplo"
```

---

## Remover usuário de um grupo

```powershell
Remove-ADGroupMember `
-Identity "GrupoAcessoPadrao" `
-Members "usuario.exemplo"
```

Sem confirmação:

```powershell
Remove-ADGroupMember `
-Identity "GrupoAcessoPadrao" `
-Members "usuario.exemplo" `
-Confirm:$false
```

---

## Verificar se usuário está bloqueado

```powershell
Get-ADUser usuario.exemplo -Properties LockedOut |
Select Name,LockedOut
```

---

## Desbloquear usuário

```powershell
Unlock-ADAccount usuario.exemplo
```

---

## Verificar data da última troca de senha

```powershell
Get-ADUser usuario.exemplo -Properties PasswordLastSet |
Select Name,PasswordLastSet
```

---

## Forçar troca de senha no próximo logon

```powershell
Set-ADUser `
-Identity "usuario.exemplo" `
-ChangePasswordAtLogon $true
```

---

## Redefinir senha

```powershell
Set-ADAccountPassword `
-Identity "usuario.exemplo" `
-Reset `
-NewPassword (ConvertTo-SecureString "SENHA_FORTE_DE_EXEMPLO" -AsPlainText -Force)
```

---

## Verificar computadores no domínio

```powershell
Get-ADComputer -Filter *
```

Listar somente nomes:

```powershell
Get-ADComputer -Filter * |
Select Name
```

---

## Procurar computador específico

```powershell
Get-ADComputer -Identity PC-EXEMPLO-01
```

---

## Verificar informações do computador

```powershell
Get-ADComputer PC-EXEMPLO-01 -Properties *
```

---

## Pesquisar usuário por nome

```powershell
Get-ADUser -Filter 'Name -like "*Usuario*"'
```

---

## Pesquisar computador por nome

```powershell
Get-ADComputer -Filter 'Name -like "*PC-EXEMPLO*"'
```

---

## Descobrir em qual OU está o usuário

```powershell
Get-ADUser usuario.exemplo |
Select DistinguishedName
```

Exemplo:

```text
CN=Usuario Exemplo,
OU=Colaboradores,
OU=Usuarios,
OU=Matriz,
DC=corp,
DC=example,
DC=com
```

---

## Verificar usuário logado na máquina local

```cmd
whoami
```

---

## Verificar grupos recebidos no logon

```cmd
whoami /groups
```

Filtrar por Proxy:

```cmd
whoami /groups | findstr /i Proxy
```

---

## Atualizar tickets Kerberos

Visualizar:

```cmd
klist
```

Limpar:

```cmd
klist purge
```

---

## Verificar informações do domínio

```cmd
systeminfo | findstr /B /C:"Domínio"
```

---

## Verificar controladores de domínio

```cmd
nltest /dclist:corp.example.com
```

---

## Verificar conectividade com o AD

```cmd
nltest /dsgetdc:corp.example.com
```

---

## Verificar replicação do AD (Admins)

```cmd
repadmin /replsummary
```

---

## Comandos RSAT

Verificar se o módulo AD está carregado:

```powershell
Get-Module ActiveDirectory
```

Importar:

```powershell
Import-Module ActiveDirectory
```
