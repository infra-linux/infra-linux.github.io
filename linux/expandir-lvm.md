---
layout: default
title: LVM
---

# LVM
## Como criar um novo volume LVM

Este procedimento cria um novo volume LVM utilizando um disco já conectado ao servidor.

## Identificar o disco

```bash
lsblk
```

Exemplo:

![Saída do comando lsblk](../images/lsblk.png)

```text
sdb    10G disk
```

---

## Criar o Physical Volume (PV)

```bash
pvcreate /dev/sdb
```
![Saída do comando pvcreate](../images/pvcreate.png)


Verificar:

```bash
pvs
```
![Saída do comando pvs](../images/pvs.png)

---

## Criar o Volume Group (VG)

```bash
vgcreate dados-vg /dev/sdb
```
![Saída do comando vgcreate](../images/vgcreate.png)

Verificar:

```bash
vgs
```
![Saída do comando vgs](../images/vgs.png)
---

## Criar o Logical Volume (LV)

Adicionando 5G do dico disponível:

```bash
lvcreate -l 5 -n dados-lv dados-vg
```
![Saída do comando lvcreate](../images/lvcreate.png)

Verificar:

```bash
lvs
```
![Saída do comando lvs](../images/lvs.png)
---

## Criar o filesystem

XFS:

```bash
mkfs.xfs /dev/dados-vg/dados-lv
```

ou EXT4:

```bash
mkfs.ext4 /dev/dados-vg/dados-lv
```
![Saída do comando mkfs](../images/mkfs.png)

---

## Criar o ponto de montagem

```bash
mkdir /dados
```

---

## Montar o volume

```bash
mount /dev/dados-vg/dados-lv /dados
```

Validar:

```bash
df -h
```
![Saída do comando df-h](../images/df-h.png)
---

## Configurar montagem automática

Obter o UUID:

```bash
blkid
```

Editar:

```bash
vi /etc/fstab
```

Adicionar:

```text
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx /dados xfs defaults 0 0
```

Testar:

```bash
mount -a
```

---

## Estrutura criada

```text
Disco (/dev/sdb)
   ↓
PV
   ↓
VG (dados-vg)
   ↓
LV (dados-lv)
   ↓
Filesystem (XFS)
   ↓
/dados
```

---

## Resumo

```bash
pvcreate /dev/sdb

vgcreate dados-vg /dev/sdb

lvcreate -l 100%FREE -n dados-lv dados-vg

mkfs.xfs /dev/dados-vg/dados-lv

mkdir /dados

mount /dev/dados-vg/dados-lv /dados

blkid

vi /etc/fstab

mount -a
```
