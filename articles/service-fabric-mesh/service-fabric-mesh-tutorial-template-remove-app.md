---
title: Oktatóanyag – az Azure Service Fabric Meshban futó alkalmazások eltávolítása
description: Ebben az oktatóanyagban elsajátíthatja, hogyan lehet eltávolítani a Service Fabric Meshben futó alkalmazásokat, és törölni az erőforrásokat.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: d449343fd00ff958470a71ecb3a37d585d7ff8ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626611"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Oktatóanyag: Alkalmazás és erőforrások eltávolítása

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez az oktatóanyag egy sorozat negyedik része. Elsajátíthatja, hogyan távolíthat el egy futó alkalmazást, amelyet [korábban üzembe helyezett a Service Fabric Meshben](service-fabric-mesh-tutorial-template-deploy-app.md). 

A sorozat negyedik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric Meshben futó alkalmazás törlése
> * Az alkalmazás erőforrásainak törlése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Alkalmazás üzembe helyezése a Service Fabric Meshben sablon használatával](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [A Service Fabric Meshben futó alkalmazás méretezése](service-fabric-mesh-tutorial-template-scale-services.md)
> * [A Service Fabric Meshben futó alkalmazás frissítése](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Alkalmazás eltávolítása

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Nyissa meg az [Azure Cloud Shellt](service-fabric-mesh-howto-setup-cli.md), vagy [telepítse helyileg az Azure CLI-t és a Service Fabric Mesh CLI-t](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Az erőforráscsoportot és az ahhoz tartozó összes erőforrás törlése

Ha már nincs szüksége a létrehozott erőforrásokra, törölje az összeset. Korábban [létrehozott egy új erőforráscsoportot](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) az Azure Container Registry-példány és a Service Fabric Mesh alkalmazás-erőforrásainak üzemeltetéséhez.  Törölheti ezt az erőforráscsoportot, és ezzel az összes társított erőforrást is törli.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Az erőforrások törlése egyenként
Az ACR-példányt, a Service Fabric Mesh-alkalmazást és a hálózati erőforrásokat egyenként is törölheti.

Az ACR-példány törlése:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

A Service Fabric Mesh-alkalmazás törlése:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

A hálózat törlése:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * A Service Fabric Meshben futó alkalmazás törlése
> * Az alkalmazás erőforrásainak törlése
