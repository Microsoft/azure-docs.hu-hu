---
title: Leküldéses & tároló rendszerképének leküldése
description: Docker-rendszerképek leküldése és leküldése az Azure-beli privát tárolójegyzékbe a Docker CLI használatával
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 48f5f1707881ac8461e12212be631d3b80c16ca7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783826"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Az első rendszerkép leküldése az Azure Container Registrybe a Docker CLI használatával

Az Azure Container Registry a nyilvános Docker-tárolók rendszerképeihez hasonló privát tároló-rendszerképeket [és egyéb Docker Hub](https://hub.docker.com/) tárolja és felügyeli. A [Docker parancssori](https://docs.docker.com/engine/reference/commandline/cli/) felületét (Docker CLI) a [](https://docs.docker.com/engine/reference/commandline/pull/)tároló-beállításjegyzékbe való [bejelentkezéshez,](https://docs.docker.com/engine/reference/commandline/login/)leküldéshez, [](https://docs.docker.com/engine/reference/commandline/push/)leküldéshez és egyéb tároló rendszerkép-műveletekhez használhatja.

A következő lépésekben letölt egy nyilvános [Nginx-rendszerképet,](https://store.docker.com/images/nginx)címkéznie kell azt a privát Azure Container Registryhez, le kell töltenie a regisztrációs adatbázisba, majd le kell töltenie a regisztrációs adatbázisból.

## <a name="prerequisites"></a>Előfeltételek

* **Azure Container Registry** – Létrehozhat egy tároló-beállításjegyzéket Azure-előfizetésében. Használhatja például a [Azure Portal](container-registry-get-started-portal.md) vagy az Azure [CLI-t.](container-registry-get-started-azure-cli.md)
* **Docker CLI –** A Dockernek helyileg is telepítve kell lennie. A Docker csomagokat biztosít, amelyekkel a Docker egyszerűen konfigurálható bármely [macOS][docker-mac], [Windows][docker-windows] vagy [Linux][docker-linux] rendszeren.

## <a name="log-in-to-a-registry"></a>Bejelentkezés beállításjegyzékbe

A privát [tárolójegyzékben többféleképpen](container-registry-authentication.md) is hitelesíthet. Parancssorban való munka esetén az az acr login Azure [CLI-parancs használata javasolt.](/cli/azure/acr#az_acr_login) Ha például a *myregistry* nevű regisztrációs adatbázisba való bejelentkezéshez be kell jelentkeznie az Azure CLI-be, majd hitelesítenie kell magát a beállításjegyzékben:

```azurecli
az login
az acr login --name myregistry
```

Docker-bejelentkezéssel is [bejelentkezhet.](https://docs.docker.com/engine/reference/commandline/login/) Előfordulhat például, hogy egy szolgáltatásnév [hozzá lett](container-registry-authentication.md#service-principal) rendelve a regisztrációs adatbázishoz egy automatizálási forgatókönyvhöz. A következő parancs futtatásakor interaktív módon adja meg a szolgáltatásnév appID -ját (felhasználónevét) és jelszavát, amikor a rendszer erre kéri. A bejelentkezési hitelesítő adatok kezeléséhez ajánlott eljárásokért tekintse meg a [docker login](https://docs.docker.com/engine/reference/commandline/login/) parancs referenciát:

```
docker login myregistry.azurecr.io
```

Ha befejeződött, mindkét parancs `Login Succeeded` visszaadja a parancsot.
> [!NOTE]
>* Előfordulhat, hogy a Visual Studio Code-et Docker-bővítvekkel szeretné használni a gyorsabb és kényelmesebb bejelentkezéshez.

> [!TIP]
> Mindig adja meg a regisztrációs adatbázis teljes nevét (csak kisbetűket), amikor és címkével címkéz rendszerképeket a regisztrációs `docker login` adatbázisba való lekért adatokhoz. A cikkben látható példákban a teljes név a következő: *myregistry.azurecr.io.*

## <a name="pull-a-public-nginx-image"></a>Nyilvános Nginx-rendszerkép lekért fájlja

Először is egy nyilvános Nginx-rendszerképet kell lekérte a helyi számítógépre. Ez a példa lekért egy rendszerképet a Microsoft Container Registry.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Futtassa helyileg a tárolót

Futtassa a [következő docker run](https://docs.docker.com/engine/reference/run/) parancsot az Nginx-tároló egy helyi példányának interaktív () a `-it` 8080-as porton való futtatásához. A argumentum azt adja meg, hogy a `--rm` tárolót a leállításkor el kell távolítani.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Tallózással tekintse meg az Nginx által a futó tárolóban kiszolgált alapértelmezett `http://localhost:8080` weblapot. Az alábbihoz hasonló oldalnak kell jelen lennie:

![Nginx egy helyi számítógépen](./media/container-registry-get-started-docker-cli/nginx.png)

Mivel a tárolót interaktív módon indította el a használatával, az Nginx-kiszolgáló kimenetét a parancssorban láthatja, miután a böngészőben rá `-it` navigált.

A tároló leállításhoz és eltávolításához nyomja le az `Control` + `C` billentyűt.

## <a name="create-an-alias-of-the-image"></a>A rendszerkép aliasának létrehozása

A [docker tag használatával](https://docs.docker.com/engine/reference/commandline/tag/) hozzon létre egy aliast a rendszerképhez a regisztrációs adatbázis teljes elérési útját használva. A példa a(z) `samples` névteret határozza meg, hogy ne legyen zsúfolt a beállításjegyzék gyökere.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
```

A névtérekkel történő címkézéssel kapcsolatos [](container-registry-best-practices.md#repository-namespaces) további információkért tekintse meg az Ajánlott eljárások a névterekkel való címkézéshez című [Azure Container Registry.](container-registry-best-practices.md)

## <a name="push-the-image-to-your-registry"></a>A rendszerkép leküldése a beállításjegyzékbe

Most, hogy címkével jelölte meg a rendszerképet a privát regisztrációs adatbázis teljes elérési útával, lekkultálhatja azt a [tárolójegyzékbe a docker push parancsokkal:](https://docs.docker.com/engine/reference/commandline/push/)

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>A rendszerkép lekérése a beállításjegyzékből

A [docker pull paranccsal](https://docs.docker.com/engine/reference/commandline/pull/) lekérte a rendszerképet a regisztrációs adatbázisból:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>A Nginx-tároló indítása

Futtassa a tárolójegyzékből lekért rendszerképet a [docker run](https://docs.docker.com/engine/reference/run/) paranccsal:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Tallózással `http://localhost:8080` tekintse meg a futó tárolót.

A tároló leállításhoz és eltávolításához nyomja le az `Control` + `C` billentyűt.

## <a name="remove-the-image-optional"></a>A kép eltávolítása (nem kötelező)

Ha már nincs szüksége az Nginx-rendszerképre, helyileg törölheti a [docker rmi paranccsal.](https://docs.docker.com/engine/reference/commandline/rmi/)

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Ha rendszerképeket szeretne eltávolítani az Azure Container Registryből, használja az [az acr repository delete](/cli/azure/acr/repository#az_acr_repository_delete)Azure CLI-parancsot. A következő parancs például törli a címke által hivatkozott jegyzékfájlt, az egyedi rétegadatokat és a jegyzékfájlra hivatkozó `samples/nginx:latest` összes többi címkét.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az alapokat, készen áll a beállításjegyzék használatának elkezdeni! Üzembe helyezhet például tároló rendszerképeket a regisztrációs adatbázisból a következőre:

* [Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md)
* [Azure Container Instances](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Ha szükséges, telepítse az [Visual Studio Code Docker-bővítményét](https://code.visualstudio.com/docs/azure/docker) és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményt az Azure-tárolóregisztrálókhoz. Rendszerképek leküldése és leküldése egy Azure Container Registrybe, vagy ACR-feladatok futtatása a Code Visual Studio belül.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
