---
title: Rendszerképverzió másolása másik katalógusból a CLI használatával
description: Rendszerképverzió másolása másik katalógusból az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e2cd885d886a0f13783e61a04c7243efdf12967e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784982"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Rendszerkép másolása másik katalógusból az Azure CLI használatával

Ha a szervezetben több katalógus is található, létrehozhat rendszerképverziókat is a más katalógusban tárolt meglévő rendszerképverziókból. Tegyük fel például, hogy fejlesztési és tesztelési katalógusa van az új lemezképek létrehozásához és teszteléséhez. Ha készen állnak az éles környezetben való használatra, az alábbi példával átmásolhatja őket egy éles katalógusba. Lemezképet egy másik katalógusban található képből is létrehozhat a [Azure PowerShell.](image-version-another-gallery-powershell.md)



## <a name="before-you-begin"></a>Előkészületek

A cikk befejezéséhez egy meglévő forráskatatárra, rendszerkép-definícióra és rendszerképverzióra van lehetőség. Emellett egy célgalériának is kell lennie. 

A forrás rendszerkép verzióját replikálni kell arra a régióra, ahol a célkatatár található. 

A cikk munka közben szükség esetén cserélje le az erőforrásneveket.



## <a name="get-information-from-the-source-gallery"></a>Információk lekérte a forrásgyűjteményből

A forrásként használt rendszerkép definíciójának információira lesz szüksége, hogy másolatot hoz létre róla az új katalógusban.

Az elérhető rendszerkép-katalógusok információinak felsorolása [az az sig list](/cli/azure/sig#az_sig_list) használatával a forráskatatárra vonatkozó információk keresésére.

```azurecli-interactive 
az sig list -o table
```

Listsa ki a katalógusban található képdefiníciókat [az az sig image-definition list használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_list) Ebben a példában képdefiníciókat keresünk a *myGallery* nevű katalógusban a *myGalleryRG* erőforráscsoportban.

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Listsa ki a katalógusban található rendszerkép verzióit az [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) használatával, hogy megtalálja az új katalógusba másolni kívánt rendszerképverziót. Ebben a példában a *myImageDefinition* rendszerképdefiníció részét képezi összes rendszerképverziót keressük.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Ha minden szükséges információ megvan, a forrásként használt rendszerkép-verzió azonosítóját [az az sig image-version show használatával kaphatja meg.](/cli/azure/sig/image-version#az_sig_image_version_show)

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>A rendszerkép-definíció létrehozása 

Létre kell hoznia egy olyan rendszerkép-definíciót, amely megfelel a forrásként használt rendszerképverzió képdefiníciójának. A képdefiníció új katalógusban való újbóli létrehozása érdekében az [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show)használatával minden információt láthat.

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

Hozzon létre egy új képdefiníciót az új katalógusban a fenti kimenetben található információk alapján.


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

Hozzon létre verziókat [az az image gallery create-image-version használatával.](/cli/azure/sig/image-version#az_sig_image_version_create) Meg kell adni a felügyelt rendszerkép azonosítóját, hogy alapkonfigurációként használva létrehozza a rendszerkép verzióját. Az az [image list használatával](/cli/azure/image?view#az_image_list) információkat kaphat az erőforráscsoportban található rendszerképekről. 

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *MajorVersion.* *Alverzió.* *Javítás.*

Ebben a példában a rendszerkép verziója *1.0.0,* és 1  replikát fogunk létrehozni az USA  déli középső régiójában, és 1 replikát az USA keleti régiójában zónaredundáns tárolás használatával.


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
> Meg kell várnia, amíg a rendszerkép teljes verziószáma befejeződik és replikálódik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozásához használhatja.
>
> A rendszerképet prémium szintű tárolóban is tárolhatja, ha hozzáadja a et, vagy zónaredundáns tárolást ad hozzá a `--storage-account-type  premium_lrs` rendszerképverzió [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` létrehozásakor.
>

## <a name="next-steps"></a>Következő lépések

Hozzon létre egy virtuális gépet [egy általánosított vagy](vm-generalized-image-version-cli.md) egy [specializált rendszerképverzióból.](vm-specialized-image-version-cli.md)

Emellett próbálja ki az [Azure Image Builder (előzetes verzió)](./image-builder-overview.md) szolgáltatását, amely segíthet automatizálni a rendszerképverzió létrehozását, sőt akár egy meglévő rendszerképverzióból is frissíthet és létrehozhat egy új [rendszerképverziót.](./linux/image-builder-gallery-update-image-version.md) 

A vásárlási terv információinak megszabadlása: Supply Azure Marketplace purchase plan information when creating images (Vásárlási terv Azure Marketplace a [rendszerképek létrehozásakor).](marketplace-images.md)