---
title: Oktatóanyag – az Azure Service Fabric Meshban futó alkalmazások eltávolítása
description: Ebben az oktatóanyagban elsajátíthatja, hogyan lehet eltávolítani a Service Fabric Meshben futó alkalmazásokat, és törölni az erőforrásokat.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4aa2fd08491616c1202cc19fb1a6b9bc8e89853c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826056"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Oktatóanyag: Alkalmazás és erőforrások eltávolítása

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

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * A Service Fabric Meshben futó alkalmazás törlése
> * Az alkalmazás erőforrásainak törlése
