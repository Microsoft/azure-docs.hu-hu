---
title: VHD feltöltése az Azure-ba vagy lemez másolása a régiók között – Azure CLI
description: Megtudhatja, hogyan tölthet fel egy virtuális merevlemezt egy Azure-beli felügyelt lemezre, és hogyan másolhat egy felügyelt lemezt régiók között az Azure CLI használatával, közvetlen feltöltéssel.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978985"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>VHD feltöltése az Azure-ba vagy egy felügyelt lemez másolása egy másik régióba – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

- Töltse le a [AzCopy v10 legújabb verzióját](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
- Ha a virtuális merevlemezt a helyszíni rendszerből kívánja feltölteni: az Azure-hoz [készült](../windows/prepare-for-upload-vhd-image.md)rögzített méretű VHD-t helyileg tárolják.
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

Ahhoz, hogy a feltöltéshez üres szabványos HDD-t hozzon létre, szüksége lesz a feltölteni kívánt VHD-fájl méretére (bájtban). Ehhez használhatja a vagy a lehetőséget is `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd` . Ez az érték a **--upload-size-Bytes** paraméter megadásakor használatos.

Hozzon létre egy üres szabványos HDD-t a feltöltéshez a **-– for-upload** paraméter és a **--upload-size-Bytes** paraméter megadásával a [lemez létrehozása](/cli/azure/disk#az-disk-create) parancsmagban:

Cserélje le a `<yourdiskname>` `<yourresourcegroupname>` értéket a `<yourregion>` értékre, és válassza ki a megfelelő értékeket. A `--upload-size-bytes` paraméter egy példát tartalmaz, és `34359738880` lecseréli az Önnek megfelelő értékre.

> [!TIP]
> Ha operációsrendszer-lemezt hoz létre, adja hozzá a--Hyper-v-Generation lehetőséget a következőhöz: <yourGeneration> `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Ha prémium szintű SSD-t vagy standard SSD-t szeretne feltölteni, cserélje le a **standard_lrst** **premium_LRS** vagy **standardssd_lrsre**. Az ultra-lemezek jelenleg nem támogatottak.

Most, hogy létrehozott egy üres felügyelt lemezt, amely a feltöltési folyamathoz van konfigurálva, feltöltheti a virtuális merevlemezt. A virtuális merevlemezek lemezre való feltöltéséhez írható SAS szükséges, hogy a feltöltés céljának megfelelően hivatkozzon legyen rá.

Az üres felügyelt lemezről írható SAS létrehozásához cserélje le a (z) és a (z) `<yourdiskname>` `<yourresourcegroupname>` parancsot a következő parancs használatára:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Minta visszaadott értéke:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD feltöltése

Most, hogy rendelkezik egy SAS-vel az üres felügyelt lemezhez, használhatja azt a felügyelt lemez beállításához a feltöltési parancs céljának megfelelően.

A AzCopy v10 használatával töltse fel a helyi VHD-fájlt egy felügyelt lemezre úgy, hogy megadja a létrehozott SAS URI-t.

Ez a feltöltés azonos átviteli sebességgel rendelkezik, mint a [szabványos HDD](../disks-types.md#standard-hdd). Ha például egy olyan mérettel rendelkezik, amely megfelel az S4 értéknek, akkor akár 60 MiB/s sebességű átviteli sebességgel fog rendelkezni. Ha azonban egy olyan mérettel rendelkezik, amely megfelel a S70, akkor akár 500 MiB/s sebességű átviteli sebességgel fog rendelkezni.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Miután a feltöltés befejeződött, és többé nem kell további adatokra írnia a lemezt, vonja vissza a SAS-t. Az SAS visszavonása megváltoztatja a felügyelt lemez állapotát, és lehetővé teszi a lemez csatlakoztatását egy virtuális géphez.

Cserélje le a és a (z) `<yourdiskname>` `<yourresourcegroupname>` parancsot, majd a következő parancs használatával tegye elérhetővé a lemezt:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés emellett leegyszerűsíti a felügyelt lemezek másolásának folyamatát. A másolást elvégezheti ugyanazon a régión vagy régión belül (egy másik régióba).

A következő szkript ezt elvégzi Önnek, a folyamat hasonló a korábban ismertetett lépésekhez, néhány különbséggel, mivel meglévő lemezzel dolgozik.

> [!IMPORTANT]
> Ha az Azure-ból felügyelt lemez mérete bájtban van megadva, akkor 512 eltolást kell hozzáadnia. Ennek az az oka, hogy az Azure kihagyja a láblécet a lemez méretének visszaadása során. Ha ezt nem teszi meg, a másolás sikertelen lesz. A következő szkript ezt már elvégezte Önnek.

Cserélje le a,,, `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` , és `<yourTargetLocationHere>` (például a uswest2) értéket az értékekkel, majd futtassa a következő parancsfájlt a felügyelt lemez másolásához.

> [!TIP]
> Ha operációsrendszer-lemezt hoz létre, adja hozzá a--Hyper-v-Generation lehetőséget a következőhöz: <yourGeneration> `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Következő lépések

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, csatlakoztathatja a lemezt [adatlemezként egy meglévő virtuális géphez](add-disk.md) , vagy [csatlakoztathatja a lemezt egy virtuális géphez egy operációsrendszer-lemezként](upload-vhd.md#create-the-vm)egy új virtuális gép létrehozásához.