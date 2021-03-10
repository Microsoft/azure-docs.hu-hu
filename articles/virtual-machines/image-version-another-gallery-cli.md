---
title: Rendszerkép verziójának másolása egy másik katalógusból a CLI használatával
description: Másolja a rendszerkép verzióját egy másik katalógusból az Azure CLI-vel.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 0bea4fbac062b498dabe04e6e58d530d09b16d6d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553102"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Rendszerkép másolása egy másik gyűjteményből az Azure CLI használatával

Ha a szervezet több gyűjteményt is tartalmaz, akkor a meglévő rendszerkép-verziókról is létrehozhat képverziókat más gyűjteményekben tárolt képekből. Előfordulhat például, hogy egy fejlesztési és tesztelési galériával új képeket szeretne létrehozni és tesztelni. Ha készen állnak az éles környezetben való használatra, az alábbi példán keresztül másolhatja őket egy éles katalógusba. Létrehozhat egy rendszerképet is egy másik katalógusban lévő rendszerképből [Azure PowerShell](image-version-another-gallery-powershell.md)használatával.



## <a name="before-you-begin"></a>Előkészületek

A cikk végrehajtásához rendelkeznie kell egy meglévő forrás-és képdefinícióval, valamint a rendszerkép verziószámával. Emellett a célhelyen is szerepelnie kell. 

A forrás-rendszerkép verzióját replikálni kell arra a régióra, ahol a célhely található. 

A cikkben végzett munka során szükség esetén cserélje le az erőforrások nevét.



## <a name="get-information-from-the-source-gallery"></a>Információk lekérése a forrás-katalógusból

Információra van szüksége a forrás rendszerkép-definíciójában, hogy másolatot készítsen róla az új katalógusban.

Az az [SIG List](/cli/azure/sig#az-sig-list) paranccsal információkat listázhat a rendelkezésre álló képtárakról a forrás-katalógussal kapcsolatos információk megkereséséhez.

```azurecli-interactive 
az sig list -o table
```

A katalógusban szereplő képdefiníciók listázása az [az SIG rendszerkép-definition List](/cli/azure/sig/image-definition#az-sig-image-definition-list)paranccsal. Ebben a példában a *MyGallery* nevű galériában keresünk képdefiníciókat a *myGalleryRG* erőforráscsoporthoz.

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Egy katalógusban lévő rendszerkép verzióinak listázása az az [SIG rendszerkép-Version List](/cli/azure/sig/image-version#az-sig-image-version-list) paranccsal megkeresheti az új katalógusba másolni kívánt rendszerkép verzióját. Ebben a példában a *myImageDefinition* rendszerkép definíciójának részét képező összes rendszerkép-verziót keresi.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Ha már rendelkezik az összes szükséges információval, a forrás rendszerkép verziójának AZONOSÍTÓját az [az SIG rendszerkép-Version show](/cli/azure/sig/image-version#az-sig-image-version-show)paranccsal kérheti le.

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>A rendszerkép definíciójának létrehozása 

Létre kell hoznia egy rendszerkép-definíciót, amely megfelel a forrás rendszerkép-verziójának definíciójának. A rendszerkép definíciójának újbóli létrehozásához szükséges összes információt megtekintheti az [az SIG rendszerkép-definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show)paranccsal.

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

A kimenet a következőhöz hasonlóan fog kinézni:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Hozzon létre egy új rendszerkép-definíciót az új galériában a fenti kimenetből származó információk használatával.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Verziók létrehozása [az az a rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create). A rendszerkép verziójának létrehozásához meg kell adnia a felügyelt rendszerkép AZONOSÍTÓját. Az [az Image List](/cli/azure/image?view#az-image-list) paranccsal kérheti le az erőforráscsoporthoz tartozó rendszerképekkel kapcsolatos információkat. 

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és 1 replikát hozunk létre az *USA déli középső* régiójában, az USA *keleti* régiójában pedig 1 replikát használunk a zóna redundáns tárolásával.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> A rendszerképet a Premium Storage-ban is tárolhatja `--storage-account-type  premium_lrs` , ha a rendszerkép verziójának létrehozásakor hozzáadja vagy zónába helyezi a [redundáns tárolást](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` .
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet [általánosított](vm-generalized-image-version-cli.md) vagy [speciális](vm-specialized-image-version-cli.md) rendszerkép-verzióból.

Azt is megteheti, hogy az [Azure rendszerkép-szerkesztő (előzetes verzió)](./image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és a meglévő rendszerkép-verzióról is frissítheti és [létrehozhatja az új rendszerkép verzióját](./linux/image-builder-gallery-update-image-version.md). 

A vásárlási tervre vonatkozó információk megadásával kapcsolatos információkért lásd: [Azure Marketplace vásárlási terv információinak megadása képek létrehozásakor](marketplace-images.md).