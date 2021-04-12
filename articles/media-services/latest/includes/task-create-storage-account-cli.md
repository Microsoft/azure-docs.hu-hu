---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: f0d0322f6f5f14b94a67285fe8688d72c941b3a4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105104277"
---
<!-- ### Create a storage account -->

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](../storage-account-concept.md).

Rendelkeznie kell egy **elsődleges** Storage-fiókkal, és tetszőleges számú **másodlagos** Storage-fiók társítható a Media Services fiókjához. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../../../storage/common/storage-account-overview.md) című témakört. 

Ebben a példában egy általános célú v2 standard LRS-fiókot hozunk létre. Ha a Storage-fiókokkal szeretne kísérletezni, használja a következőt: `--sku Standard_LRS` . Ha azonban az üzemi célú SKU-t választotta, érdemes figyelembe vennie az `--sku Standard_RAGRS` üzleti folytonosságot, amely földrajzi replikációt tesz lehetővé. További információ: Storage- [fiókok](/cli/azure/storage/account).

Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben helyettesítse be a `storageaccountforams` saját unqiue nevét, amelynek hossza kevesebb, mint 24 karakter. `amsResourceGroup` az előző lépésben az erőforráscsoport számára megadott értéknek kell megegyeznie.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
