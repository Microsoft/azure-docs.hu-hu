---
title: Oktatóanyag – több tárolós csoport üzembe helyezése – sablon
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy tároló-csoportot több tárolóval Azure Container Instances egy Azure Resource Manager sablonnal az Azure CLI használatával.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bc956bed8324398c2d60f4641cd0bcb821fb51c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93091344"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Oktatóanyag: több tárolós csoport üzembe helyezése Resource Manager-sablonnal

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances támogatja több tároló üzembe helyezését egyetlen gazdagépre egy [tároló csoport](container-instances-container-groups.md)használatával. A Container Group akkor hasznos, ha az oldalkocsit olyan naplózási, figyelési vagy egyéb konfigurációra készíti, ahol a szolgáltatásnak második csatolt folyamatra van szüksége.

Ebben az oktatóanyagban egy egyszerű, kéttárolós oldalkocsis konfiguráció futtatását hajtja végre egy Azure Resource Manager-sablon Azure CLI-vel történő üzembe helyezésével. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Többtárolós csoport sablonjának konfigurálása
> * A tároló csoport üzembe helyezése
> * A tárolók naplóinak megtekintése

A Resource Manager-sablonok könnyen alkalmazhatók olyan forgatókönyvekhez, amikor további Azure-szolgáltatási erőforrásokat (például egy Azure Files-megosztást vagy egy virtuális hálózatot) kell telepítenie a Container Group használatával. 

> [!NOTE]
> A többtárolós csoportok jelenleg csak Linux-tárolók számára korlátozódnak. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Sablon konfigurálása

Először másolja a következő JSON-t egy nevű új fájlba `azuredeploy.json` . A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárában:

```
code azuredeploy.json
```

Ez a Resource Manager-sablon definiál egy tároló csoportot két tárolóval, egy nyilvános IP-címmel és két elérhető porttal. A csoport első tárolója egy internetre irányuló webalkalmazást futtat. A második tároló, az oldalkocsi, HTTP-kérést tesz elérhetővé a fő webalkalmazásnak a csoport helyi hálózatán keresztül.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

A Private Container-rendszerkép beállításjegyzékének használatához adjon hozzá egy objektumot a JSON-dokumentumhoz a következő formátumban. Ennek a konfigurációnak a megvalósításához tekintse meg az [ACI Resource Manager-sablon referenciájának][template-reference] dokumentációját.

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Hozzon létre egy erőforráscsoportot az [az group create][az-group-create] paranccsal.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát.

## <a name="view-deployment-state"></a>Központi telepítési állapot megtekintése

A központi telepítés állapotának megtekintéséhez használja a következőt az [Container show][az-container-show] paranccsal:

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Ha meg szeretné tekinteni a futó alkalmazást, navigáljon az IP-címére a böngészőben. Például az IP-cím ebben a `52.168.26.124` példában kimenet:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolók naplózási kimenetét az az [Container logs][az-container-logs] paranccsal tekintheti meg. Az `--container-name` argumentum megadja azt a tárolót, amelyből a naplófájlokat le szeretné húzni. Ebben a példában a `aci-tutorial-app` tároló meg van adva.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

Kimenet:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

Az oldalkocsi tároló naplófájljainak megtekintéséhez futtasson egy hasonló parancsot, amely megadja a `aci-tutorial-sidecar` tárolót.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

Kimenet:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

Amint láthatja, az oldalkocsi rendszeres időközönként HTTP-kérést küld a fő webalkalmazásnak a csoport helyi hálózatán keresztül annak biztosítása érdekében, hogy az fut. Ez az oldalkocsis példa kiterjeszthető úgy, hogy riasztást indítson, ha nem a HTTP-válasz kódját kapta `200 OK` .

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Resource Manager sablont használt egy többtárolós csoport üzembe helyezéséhez Azure Container Instancesban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Többtárolós csoport sablonjának konfigurálása
> * A tároló csoport üzembe helyezése
> * A tárolók naplóinak megtekintése

További sablon-minták: [Azure Resource Manager sablonok a Azure Container Instanceshoz](container-instances-samples-rm.md).

Többtárolós csoportot is megadhat egy [YAML-fájl](container-instances-multi-container-yaml.md)használatával. A YAML formátumának tömörebb jellege miatt a YAML-fájllal történő üzembe helyezés jó választás, ha a központi telepítés csak tároló példányokat tartalmaz.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
