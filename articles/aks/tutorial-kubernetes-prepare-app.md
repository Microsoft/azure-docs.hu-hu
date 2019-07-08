---
title: Az Azure-on futó Kubernetes oktatóanyaga – Alkalmazás előkészítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan készíthet elő és hozhat létre többtárolós alkalmazást a Docker Compose használatával, amelyet aztán üzembe helyezhet az AKS-ben.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 2b4ba646a037b2f5561dabf97dee14454c07c573
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614225"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Oktatóanyag: Az alkalmazás előkészítése az Azure Kubernetes Service (AKS)

Ebben az oktatóanyagban, amely egy hétrészes sorozat első része, egy többtárolós alkalmazást fog előkészíteni a Kubernetesben való használathoz. Meglévő fejlesztői eszközök, például a Docker Compose segítségével helyileg hozhat létre és tesztelhet egy alkalmazást. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Mintaalkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása a mintaalkalmazás forrásából
> * A többtárolós alkalmazás tesztelése helyi Docker-környezetben

Miután végzett ezzel, az alábbi alkalmazás a helyi fejlesztői környezetben fut majd.

![Egy Azure-beli Kubernetes-fürt képe](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

A további oktatóanyagokban a tárolórendszerképet feltöltött egy Azure Container registrybe, és majd egy AKS-fürtöt helyezi üzembe.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és a `docker`-parancsokra vonatkozó alapvető ismeretekkel. A tárolókkal kapcsolatos alapfogalmakért tekintse meg [a Docker használatának első lépéseivel][docker-get-started] foglalkozó témakört.

Az oktatóanyag elvégzéséhez szüksége lesz egy Linuxos tárolókat futtató helyi Docker fejlesztési környezetre. A docker csomagokat biztosít, a Docker konfigurálható egy [Mac][docker-for-mac], [Windows][docker-for-windows], vagy [Linux][docker-a-linux] rendszer.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyagok lépéseinek elvégzéséhez szükséges Docker-összetevőket. Ezért ajánlott egy teljes Docker fejlesztési környezet használata.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A jelen oktatóanyagban használt mintaalkalmazás egy alapszintű szavazóalkalmazás. Az alkalmazás egy előtérbeli webes összetevőből és egy háttérbeli Redis-példányból áll. A webes összetevő egy egyéni tárolórendszerképbe van csomagolva. A Redis-példány a Docker Hubról származó, módosítatlan rendszerképet használ.

A [git][] használatával klónozza a mintaalkalmazást a fejlesztési környezetbe:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Módosítsa a klónozott könyvtárra.

```console
cd azure-voting-app-redis
```

A könyvtárán belül található meg az alkalmazás forráskódja, egy előre létrehozott Docker Compose-fájl és egy Kubernetes-jegyzékfájl. Ezeket a fájlokat használjuk az oktatóanyagokban.

## <a name="create-container-images"></a>Tárolórendszerképek létrehozása

[A docker Compose][docker-compose] automatizálásához használható tárolórendszerképek és a többtárolós alkalmazások üzembe helyezését.

A mintául szolgáló `docker-compose.yaml` fájl használatával hozza létre a tárolórendszerképet, töltse le a Redis-rendszerképet, és indítsa el az alkalmazást:

```console
docker-compose up -d
```

Amikor elkészült, a [docker images][docker-images] paranccsal tekintheti meg a létrehozott rendszerképeket. Három rendszerkép lett letöltve vagy jött létre. Az *azure-vote-front* rendszerkép tartalmazza az előtér-alkalmazást, és a `nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép használatával indítható el egy Redis-példány.

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Futtassa a [docker ps][docker-ps] parancsot a futó tárolók megtekintéséhez:

```
$ docker ps

CONTAINER ID        IMAGE             COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        azure-vote-front  "/usr/bin/supervisord"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
b68fed4b66b6        redis             "docker-entrypoint..."   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Az alkalmazás helyi tesztelése

A futó alkalmazás megtekintéséhez lépjen a `http://localhost:8080` helyre egy helyi böngészőben. A mintaalkalmazás betöltődik az alábbi példában látható módon:

![Egy Azure-beli Kubernetes-fürt képe](./media/container-service-tutorial-kubernetes-prepare-app/azure-vote.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy az alkalmazás működésének ellenőrzése megtörtént, a futó tárolók leállíthatók és eltávolíthatók. Ne törölje a tárolórendszerképeket – a következő oktatóanyagban az *azure-vote-front* rendszerképet töltjük fel egy Azure Container Registry-példányba.

Állítsa le, és távolítsa el a container Instances szolgáltatásban és az erőforrások a [le a docker-compose][docker-compose-down] parancsot:

```console
docker-compose down
```

A helyi alkalmazás eltávolításával kapott Docker-rendszerkép az *azure-front-front* Azure Vote alkalmazást tartalmazza majd, amelyet a következő oktatóanyagban fogunk használni.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy alkalmazást teszteltünk, és tárolórendszerképeket hoztunk létre az alkalmazáshoz. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Mintaalkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása a mintaalkalmazás forrásából
> * A többtárolós alkalmazás tesztelése helyi Docker-környezetben

Folytassa a következő oktatóanyaggal, amelyben a tárolórendszerképek az Azure Container Registry-ben való tárolásának módját ismerheti meg.

> [!div class="nextstepaction"]
> [Rendszerképek leküldése az Azure Container Registry-be][aks-tutorial-prepare-acr]

<!-- LINKS - external -->
[docker-compose]: https://docs.docker.com/compose/
[docker-for-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-for-mac]: https://docs.docker.com/docker-for-mac/
[docker-for-windows]: https://docs.docker.com/docker-for-windows/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-ps]: https://docs.docker.com/engine/reference/commandline/ps/
[docker-compose-down]: https://docs.docker.com/compose/reference/down
[git]: https://git-scm.com/downloads

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
