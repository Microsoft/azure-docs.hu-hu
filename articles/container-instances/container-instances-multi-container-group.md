---
title: Oktatóanyag – Többtárolós csoport üzembe helyezése – sablon
description: Ez az oktatóanyag bemutatja, hogyan helyezhet üzembe egy tárolócsoportot több tárolóval a Azure Container Instances az Azure CLI-Azure Resource Manager használatával.
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 93ab139342795634a968cd538672e0e1b9bb08ae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763900"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>Oktatóanyag: Többtárolós csoport üzembe helyezése Resource Manager sablonnal

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances támogatja több tároló üzembe helyezését egyetlen gazdagépen egy [tárolócsoport használatával.](container-instances-container-groups.md) A tárolócsoport akkor hasznos, ha alkalmazásoldalikocsit épít naplózáshoz, monitorozáshoz vagy bármely más konfigurációhoz, ahol egy szolgáltatáshoz egy második csatolt folyamatra van szükség.

Ebben az oktatóanyagban egy egyszerű kéttárolós oldalkocsi-konfiguráció futtatásának lépéseit fogja követni egy új Azure Resource Manager az Azure CLI használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Többtárolós csoportsablon konfigurálása
> * A tárolócsoport üzembe helyezése
> * A tárolók naplóinak megtekintése

A Resource Manager sablon azonnal adaptálható olyan forgatókönyvekhez, amikor további Azure-szolgáltatási erőforrásokat (például Azure Files-megosztást vagy virtuális hálózatot) kell üzembe helyeznie a tárolócsoporttal. 

> [!NOTE]
> A többtárolós csoportok jelenleg Linux-tárolókra vannak korlátozva. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>Sablon konfigurálása

Először másolja a következő JSON-t egy nevű új `azuredeploy.json` fájlba. A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárban:

```
code azuredeploy.json
```

Ez Resource Manager sablon meghatároz egy tárolócsoportot két tárolóval, egy nyilvános IP-címmel és két elérhetővé tenni kívánt porttal. A csoport első tárolója egy internetkapcsolattal elérhető webalkalmazást futtat. A második tároló, az oldalkocsi HTTP-kérést ad a fő webalkalmazásnak a csoport helyi hálózatán keresztül.

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

Privát tároló rendszerkép-beállításjegyzékének használata előtt adjon hozzá egy objektumot a JSON-dokumentumhoz az alábbi formátumban. A konfiguráció megvalósításának példáiért tekintse meg az [ACI-Resource Manager referenciadokumentációját.][template-reference]

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

A sablon üzembe helyezése az [az deployment group create paranccsal.][az-deployment-group-create]

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
```

Néhány másodpercen belül meg kell kapnia az Azure kezdeti válaszát.

## <a name="view-deployment-state"></a>Központi telepítési állapot megtekintése

Az üzembe helyezés állapotának megtekintéséhez használja az [az container show parancsot:][az-container-show]

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

Ha meg szeretné tekinteni a futó alkalmazást, nyissa meg annak IP-címét a böngészőben. Az IP-cím például a `52.168.26.124` következő kimenetben van:

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>Tárolónaplók megtekintése

Tekintse meg egy tároló naplókimenetét az [az container logs paranccsal.][az-container-logs] A `--container-name` argumentum határozza meg azt a tárolót, amelyből a naplókat le kell lekérte. Ebben a példában a `aci-tutorial-app` tároló van megadva.

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

Az oldalkocsi tároló naplóinak megtekintéséhez futtason egy hasonló parancsot, amely megadja a `aci-tutorial-sidecar` tárolót.

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

Amint látható, az oldalkocsi rendszeres időközönként HTTP-kérést ad a fő webalkalmazásnak a csoport helyi hálózatán keresztül, hogy ellenőrizze, fut-e. Ez az oldalkocsi példa kibővíthető úgy, hogy riasztást váltsa ki, ha nem a HTTP-válaszkódot `200 OK` kapta.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure Resource Manager használt egy többtárolós csoport üzembe helyezéséhez a Azure Container Instances. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Többtárolós csoportsablon konfigurálása
> * A tárolócsoport üzembe helyezése
> * A tárolók naplóinak megtekintése

További sablonmintákért lásd: Azure Resource Manager [sablonok a Azure Container Instances.](container-instances-samples-rm.md)

YaML-fájllal többtárolós csoportot [is megadhat.](container-instances-multi-container-yaml.md) A YAML formátum tömörebb jellege miatt a YAML-fájllal való üzembe helyezés jó választás, ha az üzembe helyezés csak tárolópéldányokat tartalmaz.


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
