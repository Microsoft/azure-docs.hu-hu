---
title: Oktatóanyag – Többtárolós csoport üzembe helyezése a Docker Compose használatával
description: A Docker Compose használatával többtárolós alkalmazást hozhat létre és futtathat, majd az alkalmazást a tárolóba Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: ef08b9f9e0f596f1d94c0e6edfd46f735fe78053
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786915"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Oktatóanyag: Többtárolós csoport üzembe helyezése a Docker Compose használatával 

Ebben az oktatóanyagban a [Docker Compose](https://docs.docker.com/compose/) használatával helyileg definiálhat és futtathat egy többtárolós alkalmazást, majd üzembe helyezheti tárolócsoportként [a](container-instances-container-groups.md) Azure Container Instances. 

A tárolókat igény Azure Container Instances futtathat, amikor natív felhőalkalmazásokat fejleszt a Docker segítségével, és zökkenőmentesen szeretne váltani a helyi fejlesztésről a felhőalapú üzembe helyezésre. Ezt a képességet a Docker és az Azure közötti integráció [teszi lehetővé.](https://docs.docker.com/engine/context/aci-integration/) Natív Docker-parancsokkal egyetlen tárolópéldányt vagy többtárolós csoportot futtathat az Azure-ban. [](quickstart-docker-cli.md)

> [!IMPORTANT]
> Nem minden szolgáltatás Azure Container Instances támogatott. Adjon visszajelzést a Docker-Azure a [Docker ACI Integration](https://github.com/docker/aci-integration-beta) GitHub-adattárban található probléma létrehozásával.

> [!TIP]
> A [Docker-bővítményt a Visual Studio Code-hoz](https://aka.ms/VSCodeDocker) használhatja a tárolók, rendszerképek és környezetek integrált fejlesztéséhez, futtatásához és kezeléséhez.

Ebben a cikkben:

> [!div class="checklist"]
> * Azure tárolóregisztrációs adatbázis létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Lemezkép összeállítása és többtárolós alkalmazás helyi futtatása a Docker Compose használatával
> * Az alkalmazás rendszerképének leküldése a tároló-beállításjegyzékbe
> * Azure-környezet létrehozása a Dockerhez
> * Az alkalmazás Azure Container Instances

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – Az Azure CLI-nek telepítve kell lennie a helyi számítógépen. A 2.10.1-es vagy újabb verzió használata javasolt. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

* **Docker Desktop** – A Docker Desktop 2.3.0.5-ös vagy újabb verzióját kell használnia, amely [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) vagy [macOS rendszeren érhető el.](https://desktop.docker.com/mac/edge/Docker.dmg) Vagy telepítse a [Linuxhoz való Docker ACI integrációs CLI-t.](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux)

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A jelen oktatóanyagban használt mintaalkalmazás egy alapszintű szavazóalkalmazás. Az alkalmazás egy előtérbeli webes összetevőből és egy háttérbeli Redis-példányból áll. A webes összetevő egy egyéni tárolórendszerképbe van csomagolva. A Redis-példány a Docker Hubról származó, módosítatlan rendszerképet használ.

A [git](https://git-scm.com/downloads) használatával klónozza a mintaalkalmazást a fejlesztési környezetbe:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Váltsa át a klónozott könyvtárra.

```console
cd azure-voting-app-redis
```

A könyvtárban található az alkalmazás forráskódja és egy előre létrehozott Docker Compose-fájl, a docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Docker Compose-fájl módosítása

Nyissa meg a docker-compose.yaml fájlját egy szövegszerkesztőben. A fájl konfigurálja a `azure-vote-back` és a `azure-vote-front` szolgáltatást.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

A `azure-vote-front` konfigurációban tegye a következő két módosítást:

1. Frissítse a `image` tulajdonságot a `azure-vote-front` szolgáltatásban. A rendszerkép nevének előtagját az Azure Container Registry bejelentkezési kiszolgálójának \<acrName\> .azurecr.io. Ha például a regisztrációs adatbázis neve *myregistry,* a bejelentkezési kiszolgáló neve *myregistry.azurecr.io* (csak kisbetűs), a rendszerkép tulajdonsága pedig `myregistry.azurecr.io/azure-vote-front` .
1. Módosítsa a `ports` leképezést a következőre: `80:80` . Mentse a fájlt.

A frissített fájlnak az alábbihoz hasonlóan kell kinéznie:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Ezekkel a helyettesítésekkel a következő lépésben felépített rendszerkép fel lesz címkézve az Azure Container Registryhez, és a rendszerkép leküldhető a `azure-vote-front` Azure Container Instances.

> [!TIP]
> Ehhez a forgatókönyvhöz nem kell Azure Container Registryt használnia. Választhat például egy privát adattárat a Docker Hub az alkalmazás rendszerképének a gazdagépeként. Ha másik beállításjegyzéket választ, frissítse megfelelően a rendszerkép tulajdonságát.

## <a name="run-multi-container-application-locally"></a>Többtárolós alkalmazás helyi futtatása

Futtassa [a docker-compose fájlt,](https://docs.docker.com/compose/reference/up/)amely a mintafájlt használja a tárolólemezkép felépítéséhez, a Redis-rendszerkép letöltéséhez és az `docker-compose.yaml` alkalmazás indításához:

```console
docker-compose up --build -d
```

Amikor elkészült, a [docker images](https://docs.docker.com/engine/reference/commandline/images/) paranccsal tekintheti meg a létrehozott rendszerképeket. Három rendszerkép lett letöltve vagy jött létre. A `azure-vote-front` rendszerkép tartalmazza az előoldali alkalmazást, amely a `uwsgi-nginx-flask` rendszerképet használja alapként. A `redis` rendszerkép használatával indítható el egy Redis-példány.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Futtassa a [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) parancsot a futó tárolók megtekintéséhez:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

A futó alkalmazás megtekintéséhez lépjen a `http://localhost:80` helyre egy helyi böngészőben. A mintaalkalmazás betöltődik az alábbi példában látható módon:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Szavazóalkalmazás képe":::

A helyi alkalmazás kipróbálása után futtassa a [docker-compose futtatásával](https://docs.docker.com/compose/reference/down/) állítsa le az alkalmazást, és távolítsa el a tárolókat.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Rendszerkép leküldése a tároló-beállításjegyzékbe

Az alkalmazás üzembe helyezéséhez a Azure Container Instances le kell helyeznie a rendszerképet `azure-vote-front` a tároló-beállításjegyzékbe. Futtassa [a docker-compose leküldést](https://docs.docker.com/compose/reference/push) a rendszerkép leküldéshez:

```console
docker-compose push
```

A beállításjegyzékbe való leküldés eltarthat néhány percig.

Annak ellenőrzéséhez, hogy a rendszerkép tárolva van-e a regisztrációs adatbázisban, futtassa az [az acr repository show](/cli/azure/acr/repository#az_acr_repository_show) parancsot:

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Alkalmazás üzembe helyezése az Azure Container Instancesben

Ezután váltsa át az ACI-környezetet. A további Docker-parancsok ebben a környezetben futnak.

```console
docker context use myacicontext
```

Futtassa `docker compose up` a következőt az alkalmazás Azure Container Instances. A rendszer leküldi a rendszer a rendszerképet a tároló-beállításjegyzékből, és a `azure-vote-front` tárolócsoportot a Azure Container Instances.

```console
docker compose up
```

> [!NOTE]
> Az ACI-környezetben jelenleg elérhető Docker Compose-parancsok a `docker compose up` következők: és `docker compose down` . Ezekben a parancsokban és között nincs `docker` `compose` kötőjel.

A rendszer rövid időn belül üzembe helyez egy tárolócsoportot. Példa a kimenetre:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Futtassa `docker ps` a következőt: a futó tárolók és a tárolócsoporthoz rendelt IP-cím.

```console
docker ps
```

Példa a kimenetre:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

A futó alkalmazás felhőben való megjelenítéséhez írja be a megjelenített IP-címet egy helyi webböngészőben. Ebben a példában adja meg a következőt: `52.179.23.131` . A mintaalkalmazás betöltődik az alábbi példában látható módon:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Szavazóalkalmazás képe az ACI-ban":::

Az előoldali tároló naplóinak megtekintéséhez futtassa a [docker logs parancsot.](https://docs.docker.com/engine/reference/commandline/logs) Például:

```console
docker logs azurevotingappredis_azure-vote-front
```

Az üzembe helyezett tárolócsoport Azure Portal azure-eszközök használatával is láthatja az üzembe helyezett tárolócsoport tulajdonságait és állapotát.

Amikor befejezte az alkalmazás kipróbálását, állítsa le az alkalmazást és a tárolókat a `docker compose down` következővel:

```console
docker compose down
```

Ez a parancs törli a tárolócsoportot a Azure Container Instances.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Docker Compose segítségével váltott a többtárolós alkalmazások helyi futtatásáról a Azure Container Instances. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Azure tárolóregisztrációs adatbázis létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Lemezkép összeállítása és többtárolós alkalmazás helyi futtatása a Docker Compose használatával
> * Az alkalmazás rendszerképének leküldése a tároló-beállításjegyzékbe
> * Azure-környezet létrehozása a Dockerhez
> * Az alkalmazás Azure Container Instances

Az Visual Studio [Code Docker-bővítményét](https://aka.ms/VSCodeDocker) is használhatja a tárolók, rendszerképek és környezetek integrált fejlesztéséhez, futtatásához és kezeléséhez.

Ha több funkciót is ki szeretne használni a Azure Container Instances, használjon Azure-eszközöket egy többtárolós csoport megadásához. Tekintse meg például a tárolócsoport az Azure CLI és egy [YAML-fájl](container-instances-multi-container-yaml.md)használatával való üzembe helyezését ismertető oktatóanyagokat, vagy egy Azure Resource Manager [használatával.](container-instances-multi-container-group.md) 