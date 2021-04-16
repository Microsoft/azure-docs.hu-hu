---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI, devx-track-azurecli
ms.openlocfilehash: ff8bfbeea8bd22619375e88081da0cf9c0770fc9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513407"
---
<!-- ### Create a storage account -->

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás nevét. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. További információ a tárfiókok használatáról a Media Servicesben: [Storage-fiókok](../storage-account-concept.md).

Egy elsődleges **tárfiókkal** kell lennie, és  a fiókhoz bármilyen számú másodlagos tárfiók Media Services társítva. A Media Services támogatja az **Általános célú v2-** (GPv2-) és az **Általános célú v1-** (GPv1-) fiókokat is. A blobfiókok nem megengedettek **elsődleges** tárfiókként. Ha szeretne többet megtudni a Storage-fiókokkal kapcsolatban, tekintse meg [Az Azure Storage-fiók beállításai](../../../storage/common/storage-account-overview.md) című témakört. 

Ebben a példában egy standard általános célú LRS-fiókot hozunk létre. Ha kísérletezni szeretne a tárfiókokkal, használja a `--sku Standard_LRS` et. Ha azonban termékváltozatot választ az éles környezethez, vegye figyelembe a földrajzi replikációt az üzletmenet `--sku Standard_RAGRS` folytonossága érdekében. További információ: [tárfiókok.](/cli/azure/storage/account)

Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer a Media Services-fiókhoz fog társítani. Az alábbi szkriptben a nevére a 24 karakternél rövidebb nevet kell `storageaccountforams` írni. `amsResourceGroup` meg kell egyeznie az előző lépésben az erőforráscsoporthoz megadott értékkel.

```azurecli
az storage account create --name storageaccountforams --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```
