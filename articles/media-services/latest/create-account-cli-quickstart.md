---
title: Rövid útmutató – Azure Media Services-fiók létrehozása az Azure CLI használatával | Microsoft Docs
description: Ezen rövid útmutató lépéseivel egy Azure Media Services-fiókot hozhat létre.
services: media-services
documentationcenter: ''
author: Juliako
manager: cflower
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/27/2018
ms.author: juliako
ms.openlocfilehash: abed9fd8d466b582b534b365f4be4257f4986435
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736002"
---
# <a name="quickstart-create-an-azure-media-services-account"></a>Rövid útmutató: Azure Media Services-fiók létrehozása

> [!NOTE]
> Az Azure Media Services legújabb verziója (2018. március 30.) előzetes verzió. A verzió v3 néven is ismert. 

Függetlenül attól, hogy fejlesztő vagy médiatartalmak létrehozója, a médiatartalmaknak az Azure-ban történő tárolásához, titkosításához, kódolásához, felügyeletéhez és streameléséhez Media Services-fiókot kell létrehoznia. A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás azonosítóját. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. A Storage-fiók erőforrásának a Media Services-fiókkal azonos földrajzi régióban kell lennie.  

Ez a rövid útmutató az új Azure Media Services-fiókok az Azure CLI használatával történő létrehozásának lépéseit ismerteti.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az [Azure Portalra](http://portal.azure.com), és indítsa el a **CloudShell** szolgáltatást a parancssori felületi parancsok végrehajtásához, ahogy az az alábbi lépésekben látható.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0-ás vagy újabb verziójára lesz szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket. 

## <a name="set-the-azure-subscription"></a>Az Azure-előfizetés beállítása

Az alábbi parancsban adja meg a Media Services-fiókhoz használni kívánt Azure-előfizetés azonosítóját. Az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) lapot megnyitva tekintheti meg az Ön által elérhető előfizetések listáját.

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## <a name="create-an-azure-resource-group"></a>Azure-erőforráscsoport létrehozása

Az alábbi parancs létrehozza azt az erőforráscsoportot, amelyben a Storage- és a Media Services-fiókot használni szeretné. A *myresourcegroup* helyőrzőt cserélje le az erőforráscsoporthoz használni kívánt névvel.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## <a name="create-an-azure-storage-account"></a>Azure Storage-fiók létrehozása

A Media Services-fiók létrehozásakor meg kell adnia egy Azure Storage-fiókhoz tartozó erőforrás azonosítóját. A rendszer a Media Services-fiókhoz csatolja a megadott Storage-fiókot. 

Egy **elsődleges** és tetszőleges számú **másodlagos** Storage-fiókot társíthat a Media Services-fiókhoz. A Media Services támogatja az **általános célú v2** és az **általános célú v1** fiókokat is. A blobfiók **elsődleges** tárfiókként nem megengedett. További információ a tárfiókokról: [Az Azure-tárfiókok áttekintése](../../storage/common/storage-account-overview.md). 

Az alábbi parancs egy Storage-fiókot hoz létre, amelyet a rendszer az (elsődleges) Media Services-fiókhoz fog társítani. Az alábbi szkriptben cserélje le a *storageaccountforams* helyőrzőt. A fiók nevének rövidebbnek kell lennie 24 karakternél.

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>
```

## <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

Az alábbi Azure CLI-parancsok egy új Media Services-fiókot hoznak létre. Csak a következő kiemelt értékeket kell lecserélnie:

* *myamsaccountname*
* *myresourcegroup*
* *storageaccountforams*

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az erőforráscsoportjában lévő egyik erőforrásra sem, beleértve a jelen rövid útmutatóban létrehozott Media Services-fiókot is, törölje az erőforráscsoportot.

Hajtsa végre az alábbi parancsot a **CloudShellben**:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Fájl streamelése](stream-files-dotnet-quickstart.md)
