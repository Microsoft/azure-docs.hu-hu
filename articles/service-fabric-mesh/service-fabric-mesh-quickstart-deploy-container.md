---
title: Gyors útmutató – Hello World üzembe helyezése az Azure Service Fabric Meshban
description: Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe Service Fabric Mesh-alkalmazást az Azure Service Fabric Meshben.
author: georgewallace
ms.author: gwallace
ms.date: 11/27/2018
ms.topic: quickstart
ms.openlocfilehash: c89cc1972a554cac85ce2a258873f6c810e45167
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217720"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Rövid útmutató: A Hello World üzembe helyezése a Service Fabric Meshben

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

A [Service Fabric Mesh](service-fabric-mesh-overview.md) segítségével egyszerűen hozhat létre és felügyelhet mikroszolgáltatás-alkalmazásokat az Azure-ban anélkül, hogy virtuális gépeket kellene kiépítenie. Ebben a rövid útmutatóban létrehozhat egy Hello World-alkalmazást az Azure-ban, és közzéteheti azt az interneten. Ez a művelet egyetlen paranccsal hajtható végre. Néhány perc elteltével a következő nézetet láthatja a böngészőben:

![A Hello World alkalmazás a böngészőben][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Ha még nem rendelkezik Azure-fiókkal, első lépésként [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="set-up-service-fabric-mesh-cli"></a>A Service Fabric Mesh parancssori felületének beállítása 
A rövid útmutató teljesítéséhez használhatja az Azure Cloud Shellt vagy az Azure CLI helyileg telepített példányát. Az Azure Service Fabric Mesh CLI-bővítmény moduljának telepítéséhez kövesse ezeket az [útmutatásokat](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure-ba, és állítsa be az előfizetését.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot, amelyben az alkalmazást üzembe helyezheti. Egy meglévő erőforráscsoportot is használhat, és úgy kihagyhatja ezt a lépést. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Az alkalmazás üzembe helyezése

>[!NOTE]
> 2020. november 2., a [letöltési sebességre vonatkozó korlátozások](https://docs.docker.com/docker-hub/download-rate-limit/) a Docker ingyenes csomag fiókjaiból származó névtelen és hitelesített kérelmekre vonatkoznak, és az IP-cím kényszeríti. 
> 
> Ezek a sablonok a Docker hub nyilvános rendszerképeit használják. Vegye figyelembe, hogy a díjszabás korlátozott lehet. További részletek: [hitelesítés a Docker hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)használatával.

Hozza létre az alkalmazást az erőforráscsoportban az `az mesh deployment create` paranccsal.  Futtassa a következőt:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Az előző parancs egy Linux-alkalmazást telepít [linux.jshasználatával a sablonon](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.linux.json). Ha Windows-alkalmazást szeretne telepíteni, használja [ awindows.jssablont](https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/helloworld.windows.json). A Windows-tárolórendszerképek nagyobbak, mint a Linux-tárolórendszerképek, ezért több ideig tarthat az üzembe helyezésük.

Ez a parancs létrehoz egy JSON-kódrészletet, amely az alábbiakban látható. A ```outputs``` JSON-kimenet szakasza alatt másolja a ```publicIPAddress``` tulajdonságot.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Ez az információ az ```outputs``` ARM-sablonban található szakaszból származik. Ahogy az az alábbi ábrán is látható, ez a szakasz az átjáró erőforrására hivatkozik a nyilvános IP-cím beolvasásához. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Az alkalmazás megnyitása
Az alkalmazás sikeres üzembe helyezése után másolja a szolgáltatásvégpont nyilvános IP-címét a parancssori felület kimenetéből. Nyissa meg az IP-címet egy webböngészőben. Megjelenik egy weboldal az Azure Service Fabric Mesh logójával.

## <a name="check-the-application-details"></a>Az alkalmazás részleteinek ellenőrzése
Az alkalmazás állapotát az `az mesh app show` paranccsal ellenőrizheti. Ez a parancs hasznos információkat biztosít, amelyeket nyomon követhet.

A rövid útmutatóban az alkalmazás neve `helloWorldApp`. Az alkalmazás részleteinek begyűjtéséhez futtassa a következő parancsot:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Alkalmazásnaplók megtekintése

Vizsgálja meg az üzembe helyezett alkalmazás naplóit az `az mesh code-package-log get` paranccsal:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor készen áll az alkalmazás törlésére, futtassa az [az group delete][az-group-delete] parancsot az erőforráscsoport és az abban lévő alkalmazás és hálózati erőforrások eltávolításához.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

A Service Fabric Mesh-alkalmazások létrehozásáról és üzembe helyezésével kapcsolatos további információért lépjen tovább az oktatóanyagra.
> [!div class="nextstepaction"]
> [Többszolgáltatású webalkalmazás létrehozása és üzembe helyezése](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group
[azure-cli-install]: /cli/azure/install-azure-cli