---
title: Rendszerkép összeállítása a Cloud Native Buildpack segítségével
description: Az az acr pack build paranccsal tároló-rendszerképet építhet ki egy alkalmazásból, és leküldhet egy Azure Container Registry dockerfile használata nélkül.
ms.topic: article
ms.date: 10/24/2019
ms.custom: devx-track-js
ms.openlocfilehash: 1700c8fda8ac91e7d447d35c0989da2d5fc3aefe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780928"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Rendszerkép összeállítása és leküldése egy alkalmazásból egy natív felhőalapú buildcsomag használatával

Az Azure CLI-parancs a Buildpacks parancssori felületi eszközét használja egy alkalmazás felépítéséhez és a rendszerkép Azure Container `az acr pack build` [`pack`](https://github.com/buildpack/pack) Registrybe való leküldéséhez. [](https://buildpacks.io/) Ez a funkció lehetővé teszi egy tároló rendszerképének gyors felépítését az alkalmazás forráskódja Node.js, Java és más nyelveken dockerfile definiálása nélkül.

Az ebben a cikkben Azure Cloud Shell azure CLI-t a következő példái futtatásához használhatja: Azure Cloud Shell az Azure CLI helyi telepítése. Ha helyileg szeretné használni, a 2.0.70-es vagy újabb verzió szükséges. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][azure-cli-install].

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="use-the-build-command"></a>A build parancs használata

A tároló rendszerképének a Cloud Native Buildpacks használatával való felépítéséhez és leküldéshez futtassa az [az acr pack build parancsot.][az-acr-pack-build] Míg az [az acr build][az-acr-build] parancs egy Dockerfile-forrásból és egy kapcsolódó kódból buildel és leküld egy rendszerképet, közvetlenül az alkalmazás forrásfáját kell `az acr pack build` megadnia.

A futtatásakor legalább a következőket adja `az acr pack build` meg:

* Egy Azure Container Registry, ahol a parancsot futtatja
* Az eredményül kapott rendszerkép neve és címkéje
* A [tárház támogatott](container-registry-tasks-overview.md#context-locations) környezeti ACR-feladatok, például helyi könyvtár, GitHub-adattár vagy távoli tarball
* Az alkalmazáshoz megfelelő Buildpack builder-rendszerkép neve. Azure Container Registry gyorsítótár-szerkesztő rendszerképeket, például a `cloudfoundry/cnb:0.0.34-cflinuxfs3` gyorsabb buildek érdekében.  

`az acr pack build`A támogatja a ACR-feladatok egyéb [](container-registry-tasks-reference-yaml.md#run-variables) funkcióit, [](container-registry-tasks-logs.md) beleértve a futtatandó változókat és a streamként streamelhető és későbbi lekéréshez mentett feladatfuttassa naplókat.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Példa: Build Node.js rendszerkép a Cloud Foundry Builder segítségével

Az alábbi példa az [Azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) Node.js-alkalmazásból hoz létre egy tároló rendszerképet a `cloudfoundry/cnb:0.0.34-cflinuxfs3` szerkesztő használatával. Ezt a szerkesztőt a Azure Container Registry gyorsítótárazza, ezért nincs `--pull` szükség paraméterre:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Ez a példa felépíti a rendszerképet a címkével, és lekulpeli azt a `node-app` `1.0` *myregistry* tárolójegyzékbe. Ebben a példában a cél-beállításjegyzék neve explicit módon a rendszerképnév előtagja. Ha nincs megadva, a beállításjegyzék bejelentkezési kiszolgálójának neve automatikusan a rendszerkép nevére lesz kiadva.

A parancs kimenete a rendszerkép építés és lekért folyamat előrehaladását mutatja. 

A rendszerkép sikeres megépítése után futtathatja azt a Dockerben, ha telepítve van. Először jelentkezzen be a beállításjegyzékbe:

```azurecli
az acr login --name myregistry
```

Futtassa a rendszerképet:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Nyissa meg `localhost:1337` a webhelyet a kedvenc böngészőjében, és tekintse meg a minta-webalkalmazást. A `[Ctrl]+[C]` tároló leállításhoz nyomja le a billentyűt.

## <a name="example-build-java-image-with-heroku-builder"></a>Példa: Java-rendszerkép összeállítása a Heroku Builder használatával

Az alábbi példa egy tároló rendszerképet hoz létre a [buildpack/sample-java-app](https://github.com/buildpack/sample-java-app) kódtárban található Java-alkalmazásból a `heroku/buildpacks:18` szerkesztő használatával. A `--pull` paraméter megadja, hogy a parancsnak a legújabb szerkesztői rendszerképet kell lekérte. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Ez a példa felépíti a parancs futtatásazonosítójával címkézett rendszerképet, és lekullja azt a `java-app` *myregistry* tároló-beállításjegyzékbe.

A parancs kimenete a rendszerkép építés és lekért folyamat előrehaladását mutatja. 

A rendszerkép sikeres megépítése után futtathatja azt a Dockerben, ha telepítve van. Először jelentkezzen be a beállításjegyzékbe:

```azurecli
az acr login --name myregistry
```

Futtassa a rendszerképet, és a rendszerképcímkét helyettesítve a *runid jelölővel:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Nyissa meg `localhost:8080` a webhelyet a kedvenc böngészőjében, és tekintse meg a minta-webalkalmazást. A `[Ctrl]+[C]` tároló leállításhoz nyomja le a billentyűt.


## <a name="next-steps"></a>Következő lépések

Miután felépít és lek pushpel lekért egy tárolórendszerképet, úgy helyezheti üzembe, mint bármely más rendszerképet `az acr pack build` egy ön által választott célon. Az Azure üzembe helyezési lehetőségei közé tartozik többek között [a](../app-service/tutorial-custom-container.md) App Service vagy [Azure Kubernetes Service](../aks/tutorial-kubernetes-deploy-cluster.md)futtatása.

A szolgáltatásokkal kapcsolatos további ACR-feladatok lásd: Tároló rendszerkép-buildek buildének és karbantartásának automatizálása [a ACR-feladatok.](container-registry-tasks-overview.md)


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az_acr_pack_build
