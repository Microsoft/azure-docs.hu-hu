---
title: Az operációs rendszer meghajtóját, egy Windows virtuális gép az Azure-ban |} A Microsoft Docs
description: Bontsa ki az operációs rendszer meghajtójának az Azure Powershell használatával a Resource Manager-alapú üzemi modellben a virtuális gép méretét.
services: virtual-machines-windows
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: bd863a8ddd9e2277b628673d2146efd8c458c319
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2019
ms.locfileid: "55979496"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Hogyan lehet az operációs rendszer meghajtóját, a virtuális gép

Amikor hoz létre egy új virtuális gépet (VM) egy erőforráscsoportban egy lemezkép központi telepítésével [Azure Marketplace-en](https://azure.microsoft.com/marketplace/), az operációs rendszer meghajtójának alapértelmezett mérete gyakran (néhány képet kell operációsrendszer-lemez kisebb méretű alapértelmezés szerint) 127 GB. Bár hozzáadhat adatlemezeket a virtuális géphez (ezeknek száma a választott termékváltozattól függ), és ajánlott ezekre a kiegészítő lemezekre telepíteni az alkalmazásokat és a processzorigényes számítási feladatokat, az ügyfeleknek gyakran bővíteniük kell az operációs rendszer meghajtóját bizonyos forgatókönyvek támogatásához, mint például a következők:

- Az operációs rendszer meghajtójára összetevőket telepítő, régebbi alkalmazások támogatása.
- Nagyobb operációsrendszer-meghajtóval rendelkező fizikai számítógép vagy virtuális gép migrálása a helyszínről.


> [!IMPORTANT]
> Egy Azure virtuális gépet az operációsrendszer-lemez átméretezése miatt, hogy indítsa újra.
>
> A lemezek növekszik, létre kell [bontsa ki az operációs rendszer belül a kötet](#expand-the-volume-within-the-os) kihasználásához a nagyobb lemez.
> 


[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]


## <a name="resize-a-managed-disk"></a>Felügyelt lemez átméretezése

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be a Microsoft Azure-fiókjába erőforrás-kezelő módban, és válassza ki az előfizetését a következőképpen:
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. Szerezzen be egy hivatkozást a felügyelt operációsrendszer-lemez. Állítsa be a felügyelt operációsrendszer-lemez méretét a kívánt értékre, és a lemez a következő frissítése:
   
   ```Powershell
   $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
   $disk.DiskSizeGB = 1023
   Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
   ```   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A maximális szám 2048 GB-os operációsrendszer-lemezek esetén. (Lehetséges bontsa ki a VHD-blob meghaladja ezt a méretet, de az operációs rendszer csak akkor képesek együttműködni a az első 2048 GB lemezterület.)
   > 
   > 
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```

Készen is van. Csatlakozzon RDP-kapcsolaton keresztül a virtuális géphez, nyissa meg a Számítógép-kezelés (vagy Lemezkezelés) elemet, és bővítse ki a meghajtót az újonnan kiosztott tárhellyel.

## <a name="resize-an-unmanaged-disk"></a>Nem felügyelt lemez átméretezése

Nyissa meg a Powershell ISE-t vagy PowerShell-ablakot rendszergazdai módban, és kövesse az alábbi lépéseket:

1. Jelentkezzen be a Microsoft Azure-fiókjába erőforrás-kezelő módban, és válassza ki az előfizetését a következőképpen:
   
   ```Powershell
   Connect-AzAccount
   Select-AzSubscription –SubscriptionName 'my-subscription-name'
   ```
2. Állítsa az erőforráscsoport és a virtuális gép nevét a következőre:
   
   ```Powershell
   $rgName = 'my-resource-group-name'
   $vmName = 'my-vm-name'
   ```
3. Szerezzen be egy hivatkozást a virtuális gépre a következő módon:
   
   ```Powershell
   $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
   ```
4. A lemez átméretezése előtt állítsa le a virtuális gépet a következő módon:
   
    ```Powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```
5. A nem felügyelt operációsrendszer-lemez méretét adja meg a kívánt értékre, és frissítse a virtuális Gépet a következő:
   
   ```Powershell
   $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
   Update-AzVM -ResourceGroupName $rgName -VM $vm
   ```
   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. A maximális szám 2048 GB-os operációsrendszer-lemezek esetén. (Lehetséges bontsa ki a VHD-blob meghaladja ezt a méretet, de az operációs rendszer csak akkor képesek együttműködni a az első 2048 GB lemezterület.)
   > 
   > 
   
6. A virtuális gép frissítése eltarthat néhány másodpercig. Miután a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet a következő módon:
   
   ```Powershell
   Start-AzVM -ResourceGroupName $rgName -Name $vmName
   ```


## <a name="scripts-for-os-disk"></a>Parancsfájlok operációsrendszer-lemez

Az alábbi, a felügyelt és a nem felügyelt lemezek teljes szkriptet:


**A felügyelt lemezek**

```Powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Nem felügyelt lemezek**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Adatok lemezek átméretezése

Ez a cikk irányul, elsősorban a virtuális gép operációsrendszer-lemez kibontása, de a parancsfájl a virtuális géphez csatolt adatlemezek bővítésére is használható. A virtuális géphez csatolt első adatlemez bővítéséhez például cserélje ki a `StorageProfile` `OSDisk` objektumát a `DataDisks` tömbre, és egy numerikus indexszel szerezzen be az első csatolt adatlemezre mutató hivatkozást az alább látható módon:

**Felügyelt lemez**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```


**Nem felügyelt lemez**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```



Hasonlóképpen hivatkozhat más virtuális Géphez, vagy egy index használatával, mint a fenti csatlakoztatott adatlemezek vagy a **neve** tulajdonság a lemez:


**Felügyelt lemez**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nem felügyelt lemez**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'}).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Bontsa ki a köteten található az operációs rendszer

Miután a lemezt a virtuális gép rendelkezik kibontva, bontsa ki a kötet kiterül és kitölti az új hely és az operációs rendszer kell. Többféleképpen is egy partíción bővítésére. Ez a szakasz ismertet a virtuális gép RDP-kapcsolatok segítségével bontsa ki a partíciós használatával csatlakoztatása **DiskPart**.

1. Nyissa meg a virtuális gép RDP-kapcsolatának.

2.  Nyisson meg egy parancssort, írja be **diskpart**.

2.  Jelenleg a **DISKPART** parancssorába írja be a `list volume`. Jegyezze fel a kötet bővítéséhez.

3.  Jelenleg a **DISKPART** parancssorába írja be a `select volume <volumenumber>`. Ez a kötet kiválasztja *volumenumber* , amelyek ugyanazon a lemezen összefüggő, üres területre bővítéséhez.

4.  Jelenleg a **DISKPART** parancssorába írja be a `extend [size=<size>]`. Ez kibővíti a kiválasztott kötet *mérete* megabájtban (MB).


## <a name="next-steps"></a>További lépések

A lemezek is hozzáadhat a [az Azure portal](attach-managed-disk-portal.md).
