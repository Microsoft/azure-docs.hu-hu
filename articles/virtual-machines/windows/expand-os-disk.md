---
title: Windows rendszerű virtuális gép operációsrendszer-meghajtójának kibontása az Azure-ban
description: A Resource Manager-alapú üzemi modellben Azure PowerShell használatával bontsa ki a virtuális gép operációsrendszer-meghajtójának méretét.
services: virtual-machines
documentationcenter: ''
author: kirpasingh
manager: roshar
editor: ''
tags: azure-resource-manager
ms.assetid: d9edfd9f-482f-4c0b-956c-0d2c2c30026c
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: kirpas
ms.subservice: disks
ms.openlocfilehash: af57bc396349286c3edcaefc8385ddbaec066226
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199463"
---
# <a name="how-to-expand-the-os-drive-of-a-virtual-machine"></a>Virtuális gép operációsrendszer-meghajtójának bővítése

Amikor új virtuális gépet (VM) hoz létre egy erőforráscsoporthoz egy rendszerkép [Azure piactéren](https://azure.microsoft.com/marketplace/)való üzembe helyezésével, az alapértelmezett operációsrendszer-meghajtó gyakran 127 GB (a lemezképek alapértelmezés szerint kisebb operációsrendszer-lemezzel rendelkeznek). Bár lehetséges, hogy adatlemezeket is hozzáadhat a virtuális géphez (ez a szám függ a választott SKU-tól), és javasoljuk, hogy az alkalmazások és a nagy CPU-igényű munkaterhelések telepítését a kiegészítő lemezeken gyakran az ügyfeleknek ki kell bontaniuk az operációsrendszer-meghajtót az adott forgatókönyvek támogatásához:

- Az operációsrendszer-meghajtón összetevőket telepítő örökölt alkalmazások támogatása.
- Fizikai számítógép vagy virtuális gép áttelepíthető a helyszíni rendszerből egy nagyobb operációsrendszer-meghajtóval.

> [!IMPORTANT]
> Egy Azure-beli virtuális gép operációs rendszerének vagy adatlemezének átméretezéséhez a virtuális gépet fel kell osztani.
>
> Egy meglévő lemez zsugorodása nem támogatott, és az adatvesztést okozhat.
> 
> A lemezek bővítése után [ki kell bővíteni a kötetet az operációs rendszeren belül](#expand-the-volume-within-the-os) , hogy kihasználhassa a nagyobb lemezt.

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Felügyelt lemez átméretezése a Azure Portal

1. A [Azure Portal](https://portal.azure.com)nyissa meg azt a virtuális gépet, amelyben ki szeretné bővíteni a lemezt. A virtuális gép felszabadításához válassza a **Leállítás** lehetőséget.
2. Ha a virtuális gép le van állítva, a bal oldali menüben a **Beállítások** területen válassza a **lemezek** elemet.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="A menü Beállítások szakaszában kiválasztott lemezek lehetőséget megjelenítő képernyőkép.":::

 
3. A **lemez neve** területen válassza ki az átméretezni kívánt lemezt.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Képernyőkép, amely a lemezeket tartalmazó panelt jeleníti meg a kijelölt lemezzel.":::

4. A **Beállítások** területen a bal oldali menüben válassza a **méret + teljesítmény** lehetőséget.

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Képernyőfelvétel: a menü Beállítások szakaszában kiválasztott méret és teljesítmény beállítás.":::

5. A **méret + teljesítmény** területen válassza ki a kívánt méretű lemezt.
   
   > [!WARNING]
   > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. Az operációsrendszer-lemezek esetében a maximálisan megengedett 2 048 GB. (Lehetséges, hogy a VHD-blobot ezen a méreten túl szeretné kibontani, de az operációs rendszer csak az első 2 048 GB területtel működik.)
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Képernyőfelvétel: a méret és a teljesítmény ablaktábla, ahol a kiválasztott lemez mérete látható.":::

6. Válassza a lap alján található **átméretezés** elemet.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Képernyőfelvétel: a méret és a teljesítmény ablaktábla, ahol az átméretezés gomb van kiválasztva.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Felügyelt lemez átméretezése a PowerShell használatával

Nyissa meg felügyeleti módban a PowerShell ISE vagy a PowerShell ablakot, és kövesse az alábbi lépéseket:

1. Jelentkezzen be Microsoft Azure-fiókjába erőforrás-kezelési módban, és válassza ki az előfizetését:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Adja meg az erőforráscsoport nevét és a virtuális gép nevét:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Szerezzen be egy hivatkozást a virtuális gépre:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Állítsa le a virtuális gépet a lemez átméretezése előtt:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Szerezze be a felügyelt operációsrendszer-lemezre mutató hivatkozást. Állítsa be a felügyelt operációsrendszer-lemez méretét a kívánt értékre, és frissítse a lemezt:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. Az operációsrendszer-lemezek esetében a maximálisan megengedett 2 048 GB. (Lehetséges, hogy a VHD-blobot ezen a méreten túl szeretné kibontani, de az operációs rendszer csak az első 2 048 GB területtel működik.)
    > 
         
6. A virtuális gép frissítése néhány másodpercig is eltarthat. Ha a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

Készen is van. Csatlakozzon RDP-kapcsolaton keresztül a virtuális géphez, nyissa meg a Számítógép-kezelés (vagy Lemezkezelés) elemet, és bővítse ki a meghajtót az újonnan kiosztott tárhellyel.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Nem felügyelt lemez átméretezése a PowerShell használatával

Nyissa meg felügyeleti módban a PowerShell ISE vagy a PowerShell ablakot, és kövesse az alábbi lépéseket:

1. Jelentkezzen be Microsoft Azure-fiókjába erőforrás-kezelési módban, és válassza ki az előfizetését:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

2. Adja meg az erőforráscsoport nevét és a virtuális gépek nevét:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

3. Szerezzen be egy hivatkozást a virtuális gépre:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

4. Állítsa le a virtuális gépet a lemez átméretezése előtt:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

5. Állítsa be a nem felügyelt operációsrendszer-lemez méretét a kívánt értékre, és frissítse a virtuális gépet:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > Az új méretnek nagyobbnak kell lennie a meglévő lemezméretnél. Az operációsrendszer-lemezek esetében a maximálisan megengedett 2 048 GB. (Lehetséges, hogy a VHD-blobot ezen a méreten túl szeretné kibontani, de az operációs rendszer csak az első 2 048 GB területtel fog tudni működni.)
    > 
    > 
   
6. A virtuális gép frissítése néhány másodpercig is eltarthat. Ha a parancs végrehajtása befejeződött, indítsa újra a virtuális gépet:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>OPERÁCIÓSRENDSZER-lemez parancsfájljai

Alább látható a teljes parancsfájl a felügyelt és a nem felügyelt lemezekre vonatkozó referenciához:


**Felügyelt lemezek**

```powershell
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

## <a name="resizing-data-disks"></a>Adatlemezek átméretezése

Ez a cikk elsősorban a virtuális gép operációsrendszer-lemezének kibővítésére összpontosít, de a szkript használható a virtuális géphez csatlakoztatott adatlemezek bővítésére is. A virtuális géphez csatolt első adatlemez bővítéséhez például cserélje ki a `StorageProfile``OSDisk` objektumát a `DataDisks` tömbre, és egy numerikus indexszel szerezzen be az első csatolt adatlemezre mutató hivatkozást az alább látható módon:

**Felügyelt lemez**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Nem felügyelt lemez**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Hasonlóképpen a virtuális géphez csatolt más adatlemezekre is hivatkozhat, ha a fenti ábrán látható módon vagy a lemez **Name (név** ) tulajdonságát használja:


**Felügyelt lemez**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Nem felügyelt lemez**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>A kötet kibontása az operációs rendszeren belül

Ha kibontotta a virtuális gép lemezét, be kell lépnie az operációs rendszerbe, és ki kell bontania a kötetet, hogy az kiterjedjen az új területre. A partíciók kibővítésének számos módja van. Ez a szakasz a virtuális gép RDP-kapcsolaton keresztüli csatlakoztatását ismerteti a partíció a **DiskPart** használatával történő kibontásához.

1. Nyisson meg egy RDP-kapcsolat a virtuális géppel.

2. Nyisson meg egy parancssort, és írja be a **DiskPart** parancsot.

3. A **DiskPart** parancssorba írja be a következőt: `list volume` . Jegyezze fel a kiterjeszteni kívánt kötetet.

4. A **DiskPart** parancssorba írja be a következőt: `select volume <volumenumber>` . Itt választhatja ki, hogy milyen kötetre kívánja kiterjeszteni a *kötetszám* , hanem az ugyanazon a lemezen lévő üres helyet.

5. A **DiskPart** parancssorba írja be a következőt: `extend [size=<size>]` . Ez kibővíti a kijelölt  kötetet megabájtban (MB).


## <a name="next-steps"></a>Következő lépések

A [Azure Portal](attach-managed-disk-portal.md)használatával is csatolhat lemezeket.
