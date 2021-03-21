---
title: Oktatóanyag – alkalmazás üzembe helyezése az Azure Service Fabric Meshban
description: Ebből az oktatóanyagból megismerheti, hogyan helyezhet üzembe egy alkalmazást a Service Fabric Meshben egy sablonnal.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-azurecli
ms.openlocfilehash: 589e881eb48daf7da9cd2a934b14acfcc76dc5f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625416"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Oktatóanyag: Alkalmazás üzembe helyezése a Service Fabric Meshben sablon használatával

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez az oktatóanyag egy sorozat első része. Megtudhatja, hogyan helyezhet üzembe egy alkalmazást a Service Fabric Meshben egy sablonnal.  Az alkalmazás egy ASP.NET webes kezelőfelületi szolgáltatásból és egy ASP.NET Core Web API háttérszolgáltatásból áll, amelyek a Docker Hubban találhatók.  A két tárolólemezképet a Docker Hubból való lekérés után a saját privát regisztrációs adatbázisába küldheti. Ezután létrehoz egy Azure RM-sablont az alkalmazáshoz, majd üzembe helyezi az alkalmazást a tárolóregisztrációs adatbázisból a Service Fabric Meshben. Amikor elkészült, egy egyszerű teendőlistás alkalmazása lesz a Service Fabric Meshben.

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Privát Azure Container Registry-példány létrehozása
> * Tárolólemezkép leküldése a regisztrációs adatbázisba
> * RM-sablon és paraméterfájlok létrehozása
> * Alkalmazás üzembe helyezése a Service Fabric Meshben

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Alkalmazás üzembe helyezése a Service Fabric Meshben sablon használatával
> * [A Service Fabric Meshben futó alkalmazás szolgáltatásainak méretezése](service-fabric-mesh-tutorial-template-scale-services.md)
> * [A Service Fabric Meshben futó alkalmazás frissítése](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Alkalmazás eltávolítása](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* [A Docker telepítése](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Telepítse helyileg az Azure CLI-t és a Service Fabric Mesh CLI-t](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

A Service Fabric Mesh-alkalmazás szolgáltatásaival társított tárolólemezképeket egy tárolóregisztrációs adatbázisban kell tárolni.  Ez az oktatóanyag egy privát Azure Container Registry-példányt használ. 

ACR-példány létrehozásához hajtsa végre a következő lépéseket.  Ha már rendelkezik egy beállított ACR-példánnyal, átugorhatja ezt a szakaszt.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba, és állítsa be az aktív előfizetést.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő paranccsal hozzon létre egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>A tárolóregisztrációs adatbázis létrehozása

Hozzon létre egy ACR-példányt az `az acr create` paranccsal. A regisztrációs adatbázis nevének egyedinek kell lennie az Azure rendszerben, és 5–50 alfanumerikus karaktert kell tartalmaznia. Az alábbi példában a *myContainerRegistry* nevet használjuk. Ha hibaüzenetet kap, mely szerint ez a név már használatban van, válasszon másik nevet.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

A regisztrációs adatbázis létrehozásakor a kimenet a következő példához hasonló:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Rendszerképek leküldése az Azure Container Registrybe

Ez az oktatóanyag egy teendőlistás mintaalkalmazást használ példaként.  A [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) és a [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) szolgáltatások tárolólemezképei a Docker Hubban találhatók. A Visual Studióban történő alkalmazás létrehozásával kapcsolatos információkért lásd: [Service Fabric Mesh-Webalkalmazás létrehozása](service-fabric-mesh-tutorial-create-dotnetcore.md) . A Service Fabric Mesh Windows és Linux rendszerű tárolókon futhat.  Ha Linux-tárolókkal dolgozik, válassza a Docker **Váltás Linux-tárolókra** lehetőségét.  Ha Windows-tárolókkal dolgozik, válassza a Docker **Váltás Windows-tárolókra** lehetőségét.

Ahhoz, hogy képet tudjon küldeni egy ACR-példányba, először szüksége van egy tárolólemezképre. Ha még nincs egy helyi tárolólemezképe sem, a [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) paranccsal kérje le a [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) és a [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) képeit a Docker Hubból.

>[!NOTE]
> 2020. november 2., a [letöltési sebességre vonatkozó korlátozások](https://docs.docker.com/docker-hub/download-rate-limit/) a Docker ingyenes csomag fiókjaiból származó névtelen és hitelesített kérelmekre vonatkoznak, és az IP-cím kényszeríti. 
> 
> Ezek a parancsok a Docker hub nyilvános lemezképeit használják. Vegye figyelembe, hogy a díjszabás korlátozott lehet. További részletek: [hitelesítés a Docker hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub)használatával.

Windows-rendszerképek lekérése:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Mielőtt leküldhetné a rendszerképet a regisztrációs adatbázisba, fel kell címkéznie az ACR bejelentkezési kiszolgálójának teljes nevével.

A következő paranccsal kérheti le az ACR-példány bejelentkezési kiszolgálójának teljes nevét.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Címkézze fel a Docker-rendszerképet a [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) paranccsal. Az alábbi példa a seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 és a seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 rendszerképeket címkézi fel. Ha eltérő rendszerképeket használ, használja azok neveit a következő parancsban.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Jelentkezzen be a Azure Container Registryba.

```azurecli
az acr login -n myContainerRegistry
```

Küldje le a képet az ARC-példányba a [docker push](https://docs.docker.com/engine/reference/commandline/push/) paranccsal:

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Tárolórendszerképek listázása

Az ACR-példány adattárainak megtekintéséhez futtassa a következő parancsot:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

A következő példa az **azure-mesh-todo-service** adattárban lévő címkéket sorolja fel.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

Az előző kimenet megerősíti az `azure-mesh-todo-service:1.0-nanoserver-1709` elem jelenlétét a privát tárolóregisztrációs adatbázisban.  Ellenőrizze az `azure-mesh-todo-webfrontend:1.0-nanoserver-1709` elem jelenlétét is.

## <a name="retrieve-credentials-for-the-registry"></a>Regisztrációs adatbázis hitelesítő adatainak lekérése

> [!IMPORTANT]
> Éles forgatókönyvekben nem javasolt a rendszergazdai felhasználók engedélyezése az ACR-példányokon. Ebben az oktatóanyagban csak az egyszerűség kedvéért tesszük. Éles forgatókönyvekben használjon [szolgáltatásnevet](../container-registry/container-registry-auth-service-principal.md) mind a felhasználók, mind a rendszer hitelesítéséhez.

Tárolópéldánynak a sablonnal létrehozott regisztrációs adatbázisból történő üzembe helyezéséhez meg kell adnia a regisztrációs adatbázis hitelesítő adatait az üzembe helyezéskor. Elsőként a következő paranccsal engedélyezze a rendszergazdai felhasználót a regisztrációs adatbázisban:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Ezután kérje le a regisztrációs adatbázis bejelentkezési kiszolgálónevét, a felhasználónevet, valamint a jelszót a következő parancsokkal:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Az RM-sablon és a paraméterfájlok a következő szakaszban történő létrehozásakor használja a kapott ACR-beli bejelentkezési kiszolgálónevet, felhasználónevet és jelszavat.

## <a name="download-and-explore-the-template-and-parameters-files"></a>A sablon és a paraméterfájlok letöltése és megismerése

A Service Fabric Mesh-alkalmazás egy olyan Azure-erőforrás, amelyet az Azure Resource Manager (RM) sablonjaival helyezhet üzembe és kezelhet. Ha nem jártas az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmakban, tekintse meg [Az Azure Resource Manager áttekintése](../azure-resource-manager/management/overview.md) és az [RM-sablonok struktúrájának és szintaxisának megismerése](../azure-resource-manager/templates/template-syntax.md) című cikkeket.

Ez az oktatóanyag egy teendőlistát használ példaként.  Új sablon és paraméterfájlok létrehozása helyett töltse le a [mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) üzembe helyezési sablont és a [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) paramétereket.

### <a name="parameters"></a>Paraméterek
Ha a sablon olyan értékeket tartalmaz, amelyek az alkalmazás üzembe helyezését követően vagy üzemelő példányonként várhatóan változni fognak (amennyiben más üzemelő példányokhoz is használni kívánja a sablont), az ajánlott eljárás az értékek paraméterezése. Ehhez hozzon létre egy „paraméterek” szakaszt az üzembe helyezési sablon tetején, majd adja meg a paraméterek neveit és tulajdonságait, amelyekre később az üzembe helyezési sablonban hivatkozhat. A paraméterdefiníciók *type*, *defaultValue* és egy nem kötelező *metadata* szakaszt tartalmaznak *leírással* együtt.

A Paraméterek szakasz az üzembe helyezési sablon tetején, az *erőforrások* szakasz előtt van definiálva:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

A [mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) üzembe helyezési sablon területén a következő paraméterek vannak deklarálva: location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount, serviceReplicaCount.  Az egyes paraméterek leírásai az üzembe helyezési sablon fájljában találhatók.

Ezek a paraméterek a [mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json) paraméterfájlban szerepelnek.  Egy másik paraméterfájl hozzáadásával üzembe helyezésenként módosíthatja a paraméterértékeket anélkül, hogy frissítenie kellene az üzembe helyezési sablont.

### <a name="overview-of-the-application-and-services"></a>Az alkalmazás és a szolgáltatások áttekintése

A szolgáltatások az alkalmazás-erőforrás tulajdonságaiként vannak megszabva a sablonban.  Az alkalmazások egy privát hálózaton vannak üzembe helyezve, amely erőforrásként van deklarálva a sablonban.  A szolgáltatások kötetekkel őrzik meg az adatokat, amelyek erőforrásként vannak deklarálva a sablonban.  Minden szolgáltatás esetében az operációs rendszer típusa, a kódcsomag(ok), a replikák száma és a hálózat van megszabva szolgáltatástulajdonságként.  Minden kódcsomaghoz meg kell adnia a tárolólemezképet, a végpontokat, a memória- és CPU-erőforrásokat, valamint a rendszerkép-adattár hitelesítő adatait. Magas szinten a több szolgáltatással rendelkező Service Fabric Mesh-alkalmazások sablonja a következőhöz hasonlít:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 80
                    }
                  ],
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
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

A teendőlistás alkalmazás konkrét leírását a [mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) üzembe helyezési sablonban találhatja meg.

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Alkalmazás üzembe helyezése a Service Fabric Meshben
Az alábbi paranccsal hozza létre az alkalmazást és a kapcsolódó erőforrásokat, majd adja meg az előző lépésben ([Regisztrációs adatbázis hitelesítő adatainak lekérése](#retrieve-credentials-for-the-registry)) található hitelesítő adatokat.

A paraméterfájlban frissítse a következő paraméterértékeket:

|Paraméter|Érték|
|---|---|
|location|Az alkalmazás üzembe helyezési régiója.  Például: „eastus”.|
|registryPassword|A [Regisztrációs adatbázis hitelesítő adatainak lekérése](#retrieve-credentials-for-the-registry) szakaszban lekért jelszó. A sablon ezen paramétere egy biztonságos sztring, amely nem jelenik meg az üzembe helyezési állapot képernyőjén és az `az mesh service show` típusú parancsokban.|
|registryUserName|A [Regisztrációs adatbázis hitelesítő adatainak lekérése](#retrieve-credentials-for-the-registry) szakaszban lekért felhasználónév.|
|registryServer|A [Regisztrációs adatbázis hitelesítő adatainak lekérése](#retrieve-credentials-for-the-registry) szakaszban lekért regisztrációs adatbázis kiszolgálóneve.|
|frontEndImage|A kezelőfelületi szolgáltatás tárolólemezképe.  Például: `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.|
|serviceImage|A háttérszolgáltatás tárolólemezképe.  Például: `<myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709`.|

Az alkalmazás üzembe helyezéséhez futtassa a következő parancsot:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

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
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Az alkalmazás megnyitása

Az alkalmazás sikeres üzembe helyezése után kérje le a szolgáltatásvégpont nyilvános IP-címét. Az üzembe helyezési parancs lekéri a szolgáltatásvégpont nyilvános IP-címét. Igény szerint a hálózati erőforráson végzett lekérdezéssel is megkeresheti a szolgáltatásvégpont nyilvános IP-címét. Az alkalmazás hálózati erőforrásának neve `todolistappNetwork`; a következő paranccsal lekérheti a hozzá tartozó információkat. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Nyissa meg az IP-címet egy webböngészőben.

## <a name="check-application-status"></a>Alkalmazás állapotának ellenőrzése

Az alkalmazás állapotát az app show paranccsal ellenőrizheti. Az üzembe helyezett alkalmazás alkalmazásneve „todolistapp”, ez alapján lekérheti az adatait.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Vizsgálja meg az üzembe helyezett alkalmazás naplóit az `az mesh code-package-log get` paranccsal:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Privát tárolóregisztrációs adatbázis létrehozása
> * Tárolólemezkép leküldése a regisztrációs adatbázisba
> * Sablon és paraméterfájl létrehozása
> * Alkalmazás üzembe helyezése a Service Fabric Meshben

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [A Service Fabric Meshben futó alkalmazás méretezése](service-fabric-mesh-tutorial-template-scale-services.md)