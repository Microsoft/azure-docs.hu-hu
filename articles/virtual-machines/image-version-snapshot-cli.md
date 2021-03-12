---
title: CLI – rendszerkép létrehozása pillanatképből vagy felügyelt lemezről megosztott képtárban
description: Megtudhatja, hogyan hozhat létre lemezképet egy pillanatképből vagy egy felügyelt lemezből egy megosztott rendszerkép-katalógusban az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c809edd3699d0b9827fe15da53d5d18b12cbe6e6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556961"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Rendszerkép létrehozása felügyelt lemezről vagy pillanatképből egy megosztott rendszerkép-katalógusban az Azure CLI használatával

Ha van meglévő pillanatképe vagy felügyelt lemeze, amelyet át szeretne telepíteni egy megosztott képkatalógusba, akkor közvetlenül a felügyelt lemezről vagy pillanatképből hozhat létre egy megosztott rendszerkép-katalógust. Az új rendszerkép tesztelése után törölheti a forrás felügyelt lemezt vagy pillanatképet. A rendszerképeket a [Azure PowerShell](image-version-snapshot-powershell.md)használatával is létrehozhatja egy felügyelt lemezről vagy pillanatképből egy megosztott Képtárban.

A képkatalógusban található lemezképek két összetevővel rendelkeznek, amelyeket a következő példában hozunk létre:
- A **rendszerkép definíciója** információt nyújt a rendszerképekről és az azok használatára vonatkozó követelményekről. Ez magában foglalja azt is, hogy a rendszerkép Windows vagy Linux, specializált vagy általánosított, kibocsátási megjegyzések, valamint minimális és maximális memória-követelmény. Ez egy adott típusú rendszerkép definíciója. 
- A **rendszerkép verziója** a virtuális gépek megosztott képtárat használó létrehozásához használatos. A környezethez szükség lehet a rendszerkép több verziójára. A virtuális gép létrehozásakor a rendszer a rendszerkép verzióját használja új lemezek létrehozásához a virtuális géphez. A rendszerkép verziója többször is használható.


## <a name="before-you-begin"></a>Előkészületek

A cikk elvégzéséhez pillanatkép vagy felügyelt lemez szükséges. 

Ha adatlemezt szeretne megadni, az adatlemez mérete nem lehet nagyobb 1 TB-nál.

A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.

## <a name="find-the-snapshot-or-managed-disk"></a>A pillanatkép vagy a felügyelt lemez megkeresése 

Az erőforráscsoporthoz elérhető Pillanatképek listáját az [az Snapshot List](/cli/azure/snapshot#az-snapshot-list)paranccsal tekintheti meg. 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

A pillanatképek helyett felügyelt lemezt is használhat. Felügyelt lemez beszerzéséhez használja [az az Disk List](/cli/azure/disk#az-disk-list)lehetőséget. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Miután megadta a pillanatkép vagy a felügyelt lemez AZONOSÍTÓját, és hozzárendeli azt egy nevű változóhoz, amelyet `$source` később felhasználhat.

Ugyanezt a folyamatot használhatja a rendszerképbe felvenni kívánt adatlemezek lekéréséhez. Rendelje hozzá a változóhoz, majd ezeket a változókat később, amikor létrehozza a rendszerkép verzióját.


## <a name="find-the-gallery"></a>A gyűjtemény megkeresése

A rendszerkép-definíció létrehozásához a rendszerkép-katalógusra vonatkozó információkra lesz szüksége.

A rendelkezésre álló rendszerkép-gyűjteményekkel kapcsolatos információk listázása az [az SIG List](/cli/azure/sig#az-sig-list)paranccsal. Jegyezze fel a katalógus nevét, hogy a katalógus melyik erőforráscsoportot használja a későbbiekben.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. A rendszer a rendszerképpel kapcsolatos információk kezelésére szolgál. A képdefiníciók nevei nagybetűket, kisbetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

A rendszerkép meghatározásakor győződjön meg arról, hogy a megfelelő információval rendelkezik. Ebben a példában feltételezzük, hogy a pillanatkép vagy a felügyelt lemez egy használatban lévő virtuális gépről származik, és nem lett általánosítva. Ha a felügyelt lemez vagy pillanatkép általánosított operációs rendszerből készült (a Sysprep futtatása után Windows vagy [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` vagy `-deprovision+user` Linux rendszeren), akkor módosítsa a alkalmazást `-OsState` `generalized` . 

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](./shared-image-galleries.md#image-definitions).

Hozzon létre egy rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal.

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy [speciális](./shared-image-galleries.md#generalized-and-specialized-images) Linux operációsrendszer-rendszerképhez van. Ha Windows operációs rendszert használó lemezképek definícióját szeretné létrehozni, használja a következőt: `--os-type Windows` . 

Ebben a példában a katalógus neve *MyGallery*, ez a *myGalleryRG* erőforráscsoport, a rendszerkép definíciójának neve pedig *mImageDefinition* lesz.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerkép [-verziót az az rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create)paranccsal. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és 1 replikát hozunk létre az *USA déli középső* régiójában, az USA *2. keleti* régiójában pedig 1 replikát használunk a Zone-redundáns tárolás használatával. A célcsoportok replikáláshoz való kiválasztásakor ne feledje, hogy a felügyelt lemez vagy pillanatkép *forrás* régióját is meg kell adnia a replikálás céljának.

Adja át a pillanatkép vagy a felügyelt lemez AZONOSÍTÓját a `--os-snapshot` paraméterben.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Ha az adatlemezeket is bele kívánja foglalni a lemezképbe, akkor azt is `--data-snapshot-luns` be kell állítania a LUN-számhoz beállított paraméterrel, valamint az `--data-snapshots` adatlemez vagy pillanatkép azonosítójának beállítását.

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> Az összes rendszerkép-verzió replikáját a [zóna redundáns tárolójában](../storage/common/storage-redundancy.md) is tárolhatja `--storage-account-type standard_zrs` a rendszerkép verziójának létrehozásakor.
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet egy [speciális rendszerkép-verzióból](vm-specialized-image-version-cli.md).

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).