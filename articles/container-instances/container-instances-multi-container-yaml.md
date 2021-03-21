---
title: Oktatóanyag – több tárolós csoport üzembe helyezése – YAML
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy Azure Container Instances több tárolót tartalmazó YAML-fájllal az Azure CLI használatával.
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 6f9dda7735587dfee1dde86c85375efcf057daa7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605161"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>Oktatóanyag: több tárolós csoport üzembe helyezése YAML-fájl használatával

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances támogatja több tároló üzembe helyezését egyetlen gazdagépre egy [tároló csoport](container-instances-container-groups.md)használatával. A Container Group akkor hasznos, ha az oldalkocsit olyan naplózási, figyelési vagy egyéb konfigurációra készíti, ahol a szolgáltatásnak második csatolt folyamatra van szüksége.

Ebben az oktatóanyagban egy egyszerű, kéttárolós oldalkocsis konfiguráció futtatását hajtja végre egy [YAML-fájl](container-instances-reference-yaml.md) Azure CLI-vel történő üzembe helyezésével. A YAML-fájlok tömör formátumot biztosítanak a példányok beállításainak megadásához. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * YAML-fájl konfigurálása
> * A tároló csoport üzembe helyezése
> * A tárolók naplóinak megtekintése

> [!NOTE]
> A többtárolós csoportok jelenleg csak Linux-tárolók számára korlátozódnak.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>YAML-fájl konfigurálása

Többtárolós csoport az az [Container Create][az-container-create] paranccsal történő üzembe helyezéséhez az Azure CLI-ben meg kell adnia a tároló csoport konfigurációját egy YAML fájlban. Ezután adja át a YAML-fájlt paraméterként a parancsnak.

Először másolja a következő YAML egy **Deploy-ACI. YAML** nevű új fájlba. A Azure Cloud Shell a Visual Studio Code használatával hozhatja létre a fájlt a munkakönyvtárában:

```
code deploy-aci.yaml
```

Ez a YAML-fájl definiál egy "myContainerGroup" nevű tároló csoportot két tárolóval, egy nyilvános IP-címmel és két elérhető porttal. A tárolók nyilvános Microsoft-rendszerképekből telepíthetők. A csoport első tárolója egy internetre irányuló webalkalmazást futtat. A második tároló, az oldalkocsi, rendszeres időközönként HTTP-kéréseket küld az első tárolóban futó webalkalmazásnak a tároló csoport helyi hálózatán keresztül.

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

Ha privát tároló rendszerkép-beállításjegyzéket szeretne használni, adja hozzá a `imageRegistryCredentials` (z) tulajdonságot a tároló csoporthoz a környezetének megfelelő értékekkel:

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>A tároló csoport üzembe helyezése

Hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Telepítse a tároló csoportot az az [Container Create][az-container-create] paranccsal, és adja át a YAML fájlt argumentumként:

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
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

Ebben az oktatóanyagban egy YAML-fájlt használt egy többtárolós csoport üzembe helyezéséhez Azure Container Instancesban. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * YAML-fájl konfigurálása több tárolós csoport számára
> * A tároló csoport üzembe helyezése
> * A tárolók naplóinak megtekintése

Egy [Resource Manager-sablonnal](container-instances-multi-container-group.md)is megadhat egy többtárolós csoportot. Egy Resource Manager-sablon könnyen adaptálható olyan forgatókönyvekhez, amikor további Azure-szolgáltatási erőforrásokat kell üzembe helyeznie a Container Group használatával.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
