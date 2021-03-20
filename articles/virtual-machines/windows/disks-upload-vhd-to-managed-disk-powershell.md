---
title: VHD feltöltése az Azure-ba vagy lemez másolása régiók között – Azure PowerShell
description: Megtudhatja, hogyan tölthet fel egy virtuális merevlemezt egy Azure Managed Disk-lemezre, és hogyan másolhat a felügyelt lemezeket régiók között a közvetlen feltöltéssel Azure PowerShell használatával.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: c766c78705a1c1e40a9385360d35ac06a3db3a5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252237"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>VHD feltöltése az Azure-ba vagy egy felügyelt lemez másolása egy másik régióba – Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

- Töltse le a [AzCopy v10 legújabb verzióját](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Azure PowerShell modul telepítése](/powershell/azure/install-Az-ps).
- Ha a virtuális merevlemezt a helyszíni rendszerből kívánja feltölteni: az Azure-hoz [készült](prepare-for-upload-vhd-image.md)rögzített méretű VHD-t helyileg tárolják.
- Vagy egy felügyelt lemezt az Azure-ban, ha egy másolási műveletet kíván végrehajtani.

## <a name="getting-started"></a>Első lépések

Ha egy grafikus felhasználói felületen szeretné feltölteni a lemezeket, ezt Azure Storage Explorer használatával teheti meg. Részletekért lásd: [Azure Storage Explorer használata az Azure Managed Disks kezeléséhez](../disks-use-storage-explorer-managed-disks.md)

A virtuális merevlemez Azure-ba való feltöltéséhez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehoz egyet, néhány további információra van szüksége a lemezekről.

Az ilyen felügyelt lemez két egyedi állapottal rendelkezik:

- ReadToUpload, ami azt jelenti, hogy a lemez készen áll a feltöltésre, de nem jött létre [biztonságos hozzáférési aláírás](../../storage/common/storage-sas-overview.md) (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltés fogadására, és a SAS létrejött.

> [!NOTE]
> Ezen állapotok bármelyikében a felügyelt lemez a [standard szintű HDD díjszabása](https://azure.microsoft.com/pricing/details/managed-disks/)alapján kerül kiszámlázásra, a lemez tényleges típusától függetlenül. Egy P10 például egy S10-ként lesz kiszámlázva. Ez csak akkor lesz érvényes `revoke-access` , ha a felügyelt lemezre van meghívva, ami szükséges ahhoz, hogy csatlakoztatni lehessen a lemezt egy virtuális géphez.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

Ahhoz, hogy a feltöltéshez üres szabványos HDD-t hozzon létre, szüksége lesz a feltölteni kívánt VHD-fájl méretére (bájtban). A példában szereplő kód a következőt fogja használni: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Ez az érték a **-UploadSizeInBytes** paraméter megadásakor használatos.

Most a helyi rendszerhéjon hozzon létre egy üres szabványos HDD-t a feltöltéshez, és adja meg a **feltöltési** beállítást a **-CreateOption** paraméterben, valamint a **-UploadSizeInBytes** paramétert a [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) parancsmagban. Ezután hívja a [New-AzDisk-](/powershell/module/az.compute/new-azdisk) t a lemez létrehozásához.

Cserélje le `<yourdiskname>` , `<yourresourcegroupname>` , majd `<yourregion>` futtassa a következő parancsokat:

> [!TIP]
> Ha operációsrendszer-lemezt hoz létre, adja hozzá a következőt: `-HyperVGeneration '<yourGeneration>'` `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Ha prémium szintű SSD-t vagy standard SSD-t szeretne feltölteni, cserélje le a **Standard_LRSt** **Premium_LRS** vagy **StandardSSD_LRSre**. Az ultra-lemezek még nem támogatottak.

Most, hogy létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva, feltöltheti a virtuális merevlemezt. A virtuális merevlemezek lemezre való feltöltéséhez írható SAS szükséges, hogy a feltöltés céljának megfelelően hivatkozzon legyen rá.

Az üres felügyelt lemezről írható SAS létrehozásához cserélje le a `<yourdiskname>` és `<yourresourcegroupname>` a parancsot, majd használja a következő parancsokat:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>VHD feltöltése

Most, hogy rendelkezik egy SAS-vel az üres felügyelt lemezhez, használhatja azt a felügyelt lemez beállításához a feltöltési parancs céljának megfelelően.

A AzCopy v10 használatával töltse fel a helyi VHD-fájlt egy felügyelt lemezre úgy, hogy megadja a létrehozott SAS URI-t.

Ez a feltöltés azonos átviteli sebességgel rendelkezik, mint a [szabványos HDD](../disks-types.md#standard-hdd). Ha például egy olyan mérettel rendelkezik, amely megfelel az S4 értéknek, akkor akár 60 MiB/s sebességű átviteli sebességgel fog rendelkezni. Ha azonban egy olyan mérettel rendelkezik, amely megfelel a S70, akkor akár 500 MiB/s sebességű átviteli sebességgel fog rendelkezni.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Miután a feltöltés befejeződött, és többé nem kell további adatokra írnia a lemezt, vonja vissza a SAS-t. Az SAS visszavonása megváltoztatja a felügyelt lemez állapotát, és lehetővé teszi a lemez csatlakoztatását egy virtuális géphez.

Cserélje `<yourdiskname>` le `<yourresourcegroupname>` , majd futtassa a következő parancsot:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés emellett leegyszerűsíti a felügyelt lemezek másolásának folyamatát. Másolhatja ugyanazon a régión belül, vagy átmásolhatja a felügyelt lemezt egy másik régióba.

A következő szkript ezt elvégzi Önnek, a folyamat hasonló a korábban ismertetett lépésekhez, néhány különbséggel, mivel meglévő lemezzel dolgozik.

> [!IMPORTANT]
> Ha az Azure-ból felügyelt lemez mérete bájtban van megadva, akkor 512 eltolást kell hozzáadnia. Ennek az az oka, hogy az Azure kihagyja a láblécet a lemez méretének visszaadása során. Ha ezt nem teszi meg, a másolás sikertelen lesz. A következő szkript ezt már elvégezte Önnek.

Cserélje le a,,, `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` , `<yourOSTypeHere>` és `<yourTargetLocationHere>` (például a uswest2) értéket az értékekkel, majd futtassa a következő parancsfájlt a felügyelt lemez másolásához.

> [!TIP]
> Ha operációsrendszer-lemezt hoz létre, a következőt adja hozzá a-HyperVGeneration: <yourGeneration> `New-AzDiskConfig` .

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, csatlakoztathatja a lemezt egy virtuális géphez, és megkezdheti a használatát.

Ha szeretné megtudni, hogyan csatolhat adatlemezt egy virtuális géphez, tekintse meg a tárgyat ismertető cikket: [adatlemez csatlakoztatása egy Windows rendszerű virtuális géphez a PowerShell használatával](attach-disk-ps.md). A lemez operációsrendszer-lemezként való használatához tekintse meg [a Windows rendszerű virtuális gép létrehozása speciális lemezről](create-vm-specialized.md#create-the-new-vm)című témakört.
