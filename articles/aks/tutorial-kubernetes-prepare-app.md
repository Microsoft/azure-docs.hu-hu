---
title: Az Azure-on futó Kubernetes oktatóanyaga – Alkalmazás előkészítése
description: Ebben az Azure Kubernetes Service-hez (AKS-hez) tartozó oktatóanyagban megismerheti, hogyan készíthet elő és hozhat létre többtárolós alkalmazást a Docker Compose használatával, amelyet aztán üzembe helyezhet az AKS-ben.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc
ms.openlocfilehash: 349bf90ea0b344d5232c885358814f39fba4c19f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98251954"
---
# <a name="tutorial-prepare-an-application-for-azure-kubernetes-service-aks"></a>Oktatóanyag: Alkalmazás előkészítése az Azure Kubernetes Service (AKS) szolgáltatáshoz

Ebben az oktatóanyagban, amely egy hétrészes sorozat első része, egy többtárolós alkalmazást fog előkészíteni a Kubernetesben való használathoz. Meglévő fejlesztői eszközök, például a Docker Compose segítségével helyileg hozhat létre és tesztelhet egy alkalmazást. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Mintaalkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása a mintaalkalmazás forrásából
> * A többtárolós alkalmazás tesztelése helyi Docker-környezetben

Miután végzett ezzel, az alábbi alkalmazás a helyi fejlesztői környezetben fut majd.

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Képernyőfelvétel: a helyi webböngészőben helyileg megnyitott Azure-beli szavazási alkalmazás a tároló-rendszerképben" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

A későbbi oktatóanyagokban a rendszer feltölti a tároló lemezképét egy Azure Container Registryba, majd üzembe helyezi egy AK-fürtbe.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyag feltételezi, hogy rendelkezik a Docker fő fogalmaira, például a tárolókra, tárolórendszerképekre és a `docker`-parancsokra vonatkozó alapvető ismeretekkel. A tárolókkal kapcsolatos alapfogalmakért tekintse meg [a Docker használatának első lépéseivel][docker-get-started] foglalkozó témakört.

Az oktatóanyag elvégzéséhez szüksége lesz egy Linuxos tárolókat futtató helyi Docker fejlesztési környezetre. A Docker csomagokat biztosít, amelyekkel a Docker [Mac][docker-for-mac], [Windows][docker-for-windows] vagy [Linux][docker-for-linux] rendszereken konfigurálható.

> [!NOTE]
> Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyagok lépéseinek elvégzéséhez szükséges Docker-összetevőket. Ezért ajánlott egy teljes Docker fejlesztési környezet használata.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban használt [minta alkalmazás][sample-application] egy alapszintű webes összetevőből és egy háttérbeli Redis-példányból álló alapszintű szavazó alkalmazás. A webes összetevő egy egyéni tárolórendszerképbe van csomagolva. A Redis-példány a Docker Hubról származó, módosítatlan rendszerképet használ.

A [git][] használatával klónozza a mintaalkalmazást a fejlesztési környezetbe:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Váltson a klónozott könyvtárra.

```console
cd azure-voting-app-redis
```

A könyvtárán belül található meg az alkalmazás forráskódja, egy előre létrehozott Docker Compose-fájl és egy Kubernetes-jegyzékfájl. Ezeket a fájlokat használjuk az oktatóanyagokban. A könyvtár tartalma és szerkezete a következő:

```output
azure-voting-app-redis
│   azure-vote-all-in-one-redis.yaml
│   docker-compose.yaml
│   LICENSE
│   README.md
│
├───azure-vote
│   │   app_init.supervisord.conf
│   │   Dockerfile
│   │   Dockerfile-for-app-service
│   │   sshd_config
│   │
│   └───azure-vote
│       │   config_file.cfg
│       │   main.py
│       │
│       ├───static
│       │       default.css
│       │
│       └───templates
│               index.html
│
└───jenkins-tutorial
        config-jenkins.sh
        deploy-jenkins-vm.sh
```

## <a name="create-container-images"></a>Tárolórendszerképek létrehozása

A [Docker Compose][docker-compose] segítségével automatizálhatja a tárolórendszerképek összeállítását és a többtárolós alkalmazások üzembe helyezését.

A mintául szolgáló `docker-compose.yaml` fájl használatával hozza létre a tárolórendszerképet, töltse le a Redis-rendszerképet, és indítsa el az alkalmazást:

```console
docker-compose up -d
```

Amikor elkészült, a [docker images][docker-images] paranccsal tekintheti meg a létrehozott rendszerképeket. Három rendszerkép lett letöltve vagy jött létre. Az *Azure-vote-elülső* rendszerkép tartalmazza az előtér-alkalmazást, és az *Nginx-lombik* rendszerképet használja alapként. A *Redis* -rendszerkép a Redis-példány elindítására szolgál.

```
$ docker images

REPOSITORY                                     TAG                 IMAGE ID            CREATED             SIZE
mcr.microsoft.com/azuredocs/azure-vote-front   v1                  84b41c268ad9        9 seconds ago       944MB
mcr.microsoft.com/oss/bitnami/redis            6.0.8               3a54a920bb6c        2 days ago          103MB
tiangolo/uwsgi-nginx-flask                     python3.6           a16ce562e863        6 weeks ago         944MB
```

Futtassa a [docker ps][docker-ps] parancsot a futó tárolók megtekintéséhez:

```
$ docker ps

CONTAINER ID        IMAGE                                             COMMAND                  CREATED             STATUS              PORTS                           NAMES
d10e5244f237        mcr.microsoft.com/azuredocs/azure-vote-front:v1   "/entrypoint.sh /sta…"   3 minutes ago       Up 3 minutes        443/tcp, 0.0.0.0:8080->80/tcp   azure-vote-front
21574cb38c1f        mcr.microsoft.com/oss/bitnami/redis:6.0.8         "/opt/bitnami/script…"   3 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp          azure-vote-back
```

## <a name="test-application-locally"></a>Az alkalmazás helyi tesztelése

A futó alkalmazás megtekintéséhez lépjen a `http://localhost:8080` helyre egy helyi böngészőben. A mintaalkalmazás betöltődik az alábbi példában látható módon:

:::image type="content" source="./media/container-service-kubernetes-tutorials/azure-vote-local.png" alt-text="Képernyőfelvétel: a helyi webböngészőben helyileg megnyitott Azure-beli szavazási alkalmazás a tároló-rendszerképben" lightbox="./media/container-service-kubernetes-tutorials/azure-vote-local.png":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Most, hogy az alkalmazás működésének ellenőrzése megtörtént, a futó tárolók leállíthatók és eltávolíthatók. *Ne **törölje a tároló lemezképeit** _ – a következő oktatóanyagban a _azure-vote-elülső * lemezkép fel lesz töltve egy Azure Container Registry példányba.

Állítsa le és távolítsa el a tárolópéldányokat és -erőforrásokat a [docker-compose down][docker-compose-down] paranccsal:

```console
docker-compose down
```

Ha a helyi alkalmazás el lett távolítva, egy olyan Docker-lemezképpel rendelkezik, amely tartalmazza az Azure vote-alkalmazást, az *Azure-vote-* előfizetést, a következő oktatóanyaghoz való használatra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy alkalmazást teszteltünk, és tárolórendszerképeket hoztunk létre az alkalmazáshoz. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Mintaalkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása a mintaalkalmazás forrásából
> * A többtárolós alkalmazás tesztelése helyi Docker-környezetben

Folytassa a következő oktatóanyaggal, amelyben a tárolórendszerképek az Azure Container Registry-ben való tárolásának módját ismerheti meg.

> [!div class="nextstepaction"]
> [Rendszerképek leküldése az Azure Container Registrybe][aks-tutorial-prepare-acr]

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
[sample-application]: https://github.com/Azure-Samples/azure-voting-app-redis

<!-- LINKS - internal -->
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
