---
title: VHD feltöltése az Azure-ba vagy lemez másolása régiók között – Azure CLI
description: Megtudhatja, hogyan tölthet fel VHD-t egy Azure-beli felügyelt lemezre, és hogyan másolhatja át a felügyelt lemezeket régiók között az Azure CLI használatával közvetlen feltöltésen keresztül.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 285f0acd5097ce68cddee6f732b17944dffb0eba
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762568"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>VHD feltöltése az Azure-ba vagy felügyelt lemez másolása egy másik régióba – Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Előfeltételek

- Töltse le az [AzCopy 10-es verziójának legújabb verzióját.](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)
- [Telepítse az Azure CLI-t.](/cli/azure/install-azure-cli)
- Ha helyszíni virtuális merevlemezt kíván feltölteni: Az [Azure-hoz](../windows/prepare-for-upload-vhd-image.md)előkészített rögzített méretű, helyileg tárolt VHD.
- Vagy egy felügyelt lemezt az Azure-ban, ha másolási műveletet kíván végrehajtani.

## <a name="getting-started"></a>Első lépések

Ha grafikus felhasználói felületről szeretne lemezeket feltölteni, ezt az Azure Storage Explorer. Részletekért lásd: Az Azure Azure Storage Explorer felügyelt lemezek [kezelése a virtuális lemezekkel](../disks-use-storage-explorer-managed-disks.md)

A VHD Azure-ba való feltöltéshez létre kell hoznia egy üres felügyelt lemezt, amely ehhez a feltöltési folyamathoz van konfigurálva. Mielőtt létrehoz egy lemezt, érdemes tudni néhány további információt ezekről a lemezekről.

Az ilyen típusú felügyelt lemezek két egyedi államban vannak:

- A ReadToUpload azt jelenti, hogy a lemez készen áll a feltöltés fogadására, de nem lett létrehozva biztonságos [hozzáférésű](../../storage/common/storage-sas-overview.md) jogosultsága (SAS).
- ActiveUpload, ami azt jelenti, hogy a lemez készen áll a feltöltés fogadására, és az SAS létre lett hozva.

> [!NOTE]
> A felügyelt lemez számlázása mindkét esetben a standard [HDD](https://azure.microsoft.com/pricing/details/managed-disks/)díjszabás szerint, a lemez tényleges típusától függetlenül. A P10-et például S10-ként számlázjuk. Ez addig igaz, amíg meg nem hívjuk a felügyelt lemezen, amelyre a lemez virtuális géphez való csatlakoztatásához `revoke-access` van szükség.

## <a name="create-an-empty-managed-disk"></a>Üres felügyelt lemez létrehozása

Mielőtt létrehoz egy üres standard HDD-t a feltöltéshez, szüksége lesz a feltölteni kívánt VHD fájlméretére bájtban. Ezt a vagy a használatával `wc -c <yourFileName>.vhd` kaphatja `ls -al <yourFileName>.vhd` meg. Ez az érték az **--upload-size-bytes** paraméter megadásakor használatos.

Hozzon létre egy üres standard HDD-t a feltöltéshez a **--for-upload** paraméter és az **--upload-size-bytes** paraméter megadásával a [disk create](/cli/azure/disk#az_disk_create) parancsmagban:

Cserélje `<yourdiskname>` le a , és `<yourresourcegroupname>` `<yourregion>` értékeket a választott értékekre. A `--upload-size-bytes` paraméter egy példaértéket tartalmaz, `34359738880` és lecseréli egy Önnek megfelelő értékre.

> [!TIP]
> Operációsrendszer-lemez létrehozásakor adja hozzá a --hyper-v-generation <yourGeneration> adatokat a `az disk create` fájlhoz.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Ha prémium SSD-t vagy standard SSD-t  szeretne feltölteni, cserélje le a standard_lrs a **premium_LRS** vagy a **standardssd_lrs.** Az ultralemezek jelenleg nem támogatottak.

Most, hogy létrehozott egy üres felügyelt lemezt, amely konfigurálva van a feltöltési folyamathoz, feltölthet rá egy VHD-t. Ahhoz, hogy feltöltsön egy VHD-t a lemezre, írható SAS-re lesz szüksége, hogy a feltöltés célhelyeként hivatkozni tudja rá.

Az üres felügyelt lemez írható SAS-ének létrehozásához cserélje le a és a adatokat, `<yourdiskname>` majd használja a következő `<yourresourcegroupname>` parancsot:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Minta visszaadott érték:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD feltöltése

Most, hogy sas-t használ az üres felügyelt lemezhez, a használatával beállíthatja a felügyelt lemezt a feltöltési parancs célhelyének.

Az AzCopy 10-esével feltöltheti a helyi VHD-fájlt egy felügyelt lemezre a létrehozott SAS URI megadásával.

Ez a feltöltés ugyanolyan átviteli sebességet biztosít, mint a standard [HDD.](../disks-types.md#standard-hdd) Ha például az S4-nek megfelelő méretű, legfeljebb 60 MiB/s átviteli sebességet fog látni. Ha azonban az S70-nek megfelelő méretű, legfeljebb 500 MiB/s átviteli sebességet fog látni.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Ha a feltöltés befejeződött, és már nem kell további adatokat írnia a lemezre, vonja vissza az SAS-t. Az SAS visszaállítása megváltoztatja a felügyelt lemez állapotát, és lehetővé teszi a lemez virtuális géphez csatlakoztatását.

Cserélje `<yourdiskname>` le a és a adatokat, majd használja a következő parancsot a lemez `<yourresourcegroupname>` használhatósághoz:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Felügyelt lemez másolása

A közvetlen feltöltés a felügyelt lemezek másolásának folyamatát is leegyszerűsíti. Másolhatja ugyanannak a régiónak vagy régióközinek (egy másik régióba) belülre.

A következő szkript ezt önért fogja megtenni, a folyamat hasonló a korábban leírt lépésekhez, néhány eltéréssel, mivel egy meglévő lemezzel dolgozik.

> [!IMPORTANT]
> 512-es eltolást kell hozzáadnia, ha a lemezméretet egy felügyelt lemez bájtban adja meg az Azure-ból. Ennek az az oka, hogy az Azure kihagyja a láblécet a lemez méretének visszaadásakor. Ha ezt nem tegye meg, a másolás sikertelen lesz. A következő szkript már ezt teszi meg Önért.

Cserélje le a , , , és értékeket (például a `<sourceResourceGroupHere>` `<sourceDiskNameHere>` `<targetDiskNameHere>` `<targetResourceGroupHere>` uswest2 helyet) a saját értékeire, majd futtassa a következő szkriptet egy felügyelt lemez `<yourTargetLocationHere>` másolása érdekében.

> [!TIP]
> Operációsrendszer-lemez létrehozásakor adja hozzá a --hyper-v-generation adatokat a <yourGeneration> következő `az disk create` hez: .

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

Most, hogy sikeresen feltöltött egy virtuális merevlemezt egy felügyelt lemezre, [](add-disk.md) csatlakoztathatja a lemezt [](upload-vhd.md#create-the-vm)adatlemezként egy meglévő virtuális géphez, vagy csatlakoztathatja a lemezt egy virtuális géphez operációsrendszer-lemezként egy új virtuális gép létrehozásához.