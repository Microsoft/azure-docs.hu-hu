---
title: Oktatóanyag – az Azure Service Fabric Meshban futó alkalmazások frissítése
description: Ebből az oktatóanyagból elsajátíthatja, hogyan lehet frissíteni a Service Fabric Meshben futó Service Fabric-alkalmazások szolgáltatásait.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 8a71e854f03bee75b757e0a0aa02e7aa2c24469b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626561"
---
# <a name="tutorial-upgrade-a-service-fabric-application-running-in-service-fabric-mesh"></a>Oktatóanyag: A Service Fabric Meshben futó Service Fabric-alkalmazások frissítése

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez az oktatóanyag egy sorozat harmadik része. Elsajátíthatja, hogyan frissíthet [korábban a Service Fabric Meshben üzembe helyezett](service-fabric-mesh-tutorial-template-deploy-app.md) Service Fabric-alkalmazásokat a lefoglalt CPU-erőforrások mennyiségének növelésével.  Ha elkészült, a webes előtér-szolgáltatás nagyobb CPU-erőforrásokkal fut.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazáskonfiguráció módosítása
> * A Service Fabric Meshben futó alkalmazás frissítése

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Alkalmazás üzembe helyezése a Service Fabric Meshben sablon használatával](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [A Service Fabric Meshben futó alkalmazás méretezése](service-fabric-mesh-tutorial-template-scale-services.md)
> * A Service Fabric Meshben futó alkalmazás frissítése
> * [Alkalmazás eltávolítása](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Nyissa meg az [Azure Cloud Shellt](service-fabric-mesh-howto-setup-cli.md), vagy [telepítse helyileg az Azure CLI-t és a Service Fabric Mesh CLI-t](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="upgrade-application-configurations"></a>Alkalmazáskonfigurációk frissítése

Az alkalmazások Service Fabric Meshben való üzembe helyezésének egyik legfőbb előnye, hogy az alkalmazáskonfigurációt egyszerűen lehet frissíteni.  Például a szolgáltatások CPU- és memória-erőforrásait.

Ez az oktatóanyag az [előzőleg telepített](service-fabric-mesh-tutorial-template-deploy-app.md) és már futó Teendőlista-mintát használja példaként. Az alkalmazás két szolgáltatással rendelkezik: WebFrontEnd és ToDoService. Eredetileg mindegyik szolgáltatás 0,5 CPU-erőforrással lett üzembe helyezve.  A WebFrontEnd szolgáltatás CPU-erőforrásait a következő futtatásával tekintheti meg:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

Az alkalmazás erőforrásának üzembehelyezési sablonjában mindegyik szolgáltatás rendelkezik egy *cpu* tulajdonsággal, amellyel megadhatja a használni kívánt CPU-erőforrásokat. Egy alkalmazás több szolgáltatásból is állhat, és minden szolgáltatás egyedi *cpu* beállítással rendelkezik, amelyeket a rendszer együtt helyez üzembe és kezel. A webes előtér-szolgáltatás CPU-erőforrásainak növeléséhez módosítsa a *cpue* értéket a telepítési sablonban vagy a Parameters fájlban.  Ezt követően frissítse az alkalmazást.

### <a name="modify-the-deployment-template-parameters"></a>Az üzembehelyezési sablon paramétereinek módosítása

Ha a sablon olyan értékeket tartalmaz, amelyek az alkalmazás üzembe helyezését követően vagy üzemelő példányonként várhatóan változni fognak (amennyiben más üzemelő példányokhoz is használni kívánja a sablont), az ajánlott eljárás az értékek paraméterezése.

Korábban az alkalmazás a [mesh_rp.windows.json deployment template](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) és a [mesh_rp.windows.parameter.json parameters](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) fájl segítségével lett üzembe helyezve.

Nyissa meg helyileg a [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) paraméterfájlt, és állítsa be a *frontEndCpu* értéket 1-re:

```json
      "frontEndCpu":{
        "value": "1"
      }
```

Mentse a paraméterfájl módosításait.  

A *frontEndCpu* paraméter a [mesh_rp.windows.json üzembehelyezési sablon](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json)*paraméterek* szakaszában van deklarálva:

```json
"frontEndCpu": {
    "defaultValue": "0.5",
    "type": "string",
    "metadata": {
        "description": "The CPU resources for the front end web service."
    }
}
```

A WebFrontEnd szolgáltatás *codePackages -> erőforrás -> kérelmek cpu* tulajdonság a *frontEndCpu* paraméterre hivatkozik:

```json
    "services": [
          {
            "name": "WebFrontEnd",
            "properties": {
              "description": "WebFrontEnd description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "WebFrontEnd",
                  "image": "[parameters('frontEndImage')]",
                  ...
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              ...
```

### <a name="upgrade-your-application"></a>Az alkalmazás frissítése

Az alkalmazást futás közben is frissítheti, ha újratelepíti a sablont és a frissített paraméterfájlt:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Ekkor elindul az alkalmazás működés közbeni frissítése, és néhány percen belül látnia kell a CPU-erőforrások mennyiségének növekedését.  A WebFrontEnd szolgáltatás CPU-erőforrásait a következő futtatásával tekintheti meg:

```azurecli
az mesh service show --resource-group myResourceGroup --name WebFrontEnd --app-name todolistapp
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Az alkalmazáskonfiguráció módosítása
> * A Service Fabric Meshben futó alkalmazás frissítése

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Service Fabric Mesh-alkalmazás eltávolítása](service-fabric-mesh-tutorial-template-remove-app.md)
