---
title: Oktatóanyag – a Docker-összeállítás használata többtárolós csoport üzembe helyezéséhez
description: Hozzon létre és futtasson egy többtárolós alkalmazást a Docker-összeállítás használatával, majd hozza létre az alkalmazást a Azure Container Instances
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: a71ff438feaef555a85c33d818c287c64621d40d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913840"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Oktatóanyag: több tárolós csoport üzembe helyezése a Docker-összeállítás használatával 

Ebben az oktatóanyagban a [Docker-összeállítást](https://docs.docker.com/compose/) használja a többtárolós alkalmazások helyi definiálásához és futtatásához, [majd a Azure Container instances-ben való](container-instances-container-groups.md) üzembe helyezéséhez. 

Azure Container Instances igény szerint futtathat tárolókat, amikor Felhőbeli natív alkalmazásokat fejleszt a Docker használatával, és zökkenőmentesen szeretne váltani a helyi fejlesztéstől a felhőbe történő üzembe helyezésig. Ezt a funkciót a [Docker és az Azure integrációja](https://docs.docker.com/engine/context/aci-integration/)teszi lehetővé. A natív Docker-parancsokkal [egyetlen tároló-példány](quickstart-docker-cli.md) vagy több tárolós csoport futtatható az Azure-ban.

> [!IMPORTANT]
> A Azure Container Instances egyik funkciója sem támogatott. Küldjön visszajelzést a Docker-Azure integrációról azáltal, hogy problémát hoz létre a [Docker ACI Integration](https://github.com/docker/aci-integration-beta) GitHub-tárházban.

> [!TIP]
> A [Docker-bővítmény a Visual Studio Code](https://aka.ms/VSCodeDocker) -hoz a tárolók, képek és környezetek fejlesztéséhez, futtatásához és kezeléséhez használható integrált felhasználói élmény érdekében.

Ebben a cikkben:

> [!div class="checklist"]
> * Azure tárolóregisztrációs adatbázis létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Hozzon létre egy rendszerképet, és futtasson helyileg egy többtárolós alkalmazást a Docker-összeállítás használatával
> * Az alkalmazás rendszerképének leküldése a tároló-beállításjegyzékbe
> * Azure-környezet létrehozása a Docker számára
> * Az alkalmazás üzembe helyezése Azure Container Instances

## <a name="prerequisites"></a>Előfeltételek

* **Azure CLI** – a helyi számítógépen telepítve kell lennie az Azure CLI-nek. A 2.10.1 vagy újabb verzió használata javasolt. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket.

* **Docker Desktop** – a [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) vagy [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg)rendszerhez elérhető Docker Desktop 2.3.0.5 vagy újabb verziót kell használnia. Vagy telepítse a [Docker ACI Integration CLI-t a Linux rendszerhez](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A jelen oktatóanyagban használt mintaalkalmazás egy alapszintű szavazóalkalmazás. Az alkalmazás egy előtérbeli webes összetevőből és egy háttérbeli Redis-példányból áll. A webes összetevő egy egyéni tárolórendszerképbe van csomagolva. A Redis-példány a Docker Hubról származó, módosítatlan rendszerképet használ.

A [git](https://git-scm.com/downloads) használatával klónozza a mintaalkalmazást a fejlesztési környezetbe:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Váltson a klónozott könyvtárra.

```console
cd azure-voting-app-redis
```

A könyvtáron belül az alkalmazás forráskódja, valamint egy előre létrehozott Docker-összeállítási fájl, Docker-levélírás. YAML.

## <a name="modify-docker-compose-file"></a>Docker-összeállítási fájl módosítása

Nyissa meg a Docker-levélírás. YAML egy szövegszerkesztőben. A fájl konfigurálja a `azure-vote-back` és a `azure-vote-front` szolgáltatásokat.

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

A `azure-vote-front` konfigurációban hajtsa végre a következő két módosítást:

1. Frissítse a `image` tulajdonságot a `azure-vote-front` szolgáltatásban. Előtagként adja meg a rendszerkép nevét az Azure Container Registry ( \<acrName\> . azurecr.IO) bejelentkezési kiszolgálójának nevével. Ha például a beállításjegyzék neve *myregistry*, a bejelentkezési kiszolgáló neve *myregistry.azurecr.IO* (mind kisbetűs), a rendszerkép tulajdonság pedig ezután `myregistry.azurecr.io/azure-vote-front` .
1. Módosítsa a leképezést a következőre: `ports` `80:80` . Mentse a fájlt.

A frissített fájlnak a következőhöz hasonlóan kell kinéznie:

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

A helyettesítéssel a `azure-vote-front` következő lépésben felépített rendszerképet az Azure Container registryhez címkézi, és a rendszerképet lehívhatja Azure Container instances futtatására.

> [!TIP]
> Ehhez a forgatókönyvhöz nem kell Azure Container registryt használnia. Például választhat egy privát tárházat a Docker hub-ban az alkalmazás rendszerképének üzemeltetéséhez. Ha másik beállításjegyzéket választ, akkor megfelelően frissítse a rendszerkép tulajdonságot.

## <a name="run-multi-container-application-locally"></a>Többtárolós alkalmazás helyi futtatása

Futtassa a [Docker – build up](https://docs.docker.com/compose/reference/up/)parancsot, amely a mintaadatbázis használatával hozza `docker-compose.yaml` létre a tároló rendszerképét, töltse le a Redis-rendszerképet, és indítsa el az alkalmazást:

```console
docker-compose up --build -d
```

Amikor elkészült, a [docker images](https://docs.docker.com/engine/reference/commandline/images/) paranccsal tekintheti meg a létrehozott rendszerképeket. Három rendszerkép lett letöltve vagy jött létre. A `azure-vote-front` rendszerkép tartalmazza az előtér-alkalmazást, amely `uwsgi-nginx-flask` alapszintű képet használ. A `redis` rendszerkép használatával indítható el egy Redis-példány.

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

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="A szavazási alkalmazás képe":::

A helyi alkalmazás kipróbálása után futtassa a [Docker-összeállít](https://docs.docker.com/compose/reference/down/) parancsot az alkalmazás leállításához és a tárolók eltávolításához.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Rendszerkép leküldése a tároló-beállításjegyzékbe

Az alkalmazás Azure Container Instancesre való telepítéséhez le kell küldenie a `azure-vote-front` lemezképet a tároló-beállításjegyzékbe. A [Docker-levélírás](https://docs.docker.com/compose/reference/push) futtatásával küldje le a rendszerképet:

```console
docker-compose push
```

Eltarthat néhány percig, amíg leküldi a beállításjegyzéket.

A rendszerkép a beállításjegyzékben való tárolásának ellenőrzéséhez futtassa az az [ACR repository show](/cli/azure/acr/repository#az-acr-repository-show) parancsot:

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Alkalmazás üzembe helyezése az Azure Container instances szolgáltatásban

Ezután váltson az ACI-környezetre. A további Docker-parancsok ebben a környezetben futnak.

```console
docker context use myacicontext
```

Futtassa `docker compose up` az alkalmazást az Azure Container instances-ben való alkalmazás indításához. A rendszer `azure-vote-front` lekéri a rendszerképet a tároló-beállításjegyzékből, és a tároló csoportot Azure Container instances hozza létre.

```console
docker compose up
```

> [!NOTE]
> Az ACI-környezetekben jelenleg elérhető Docker-összeállítási parancsok `docker compose up` és `docker compose down` . A parancsok között nem szerepel `docker` kötőjel `compose` .

Rövid idő alatt a rendszer üzembe helyezi a tároló csoportot. Példa a kimenetre:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Futtassa a parancsot a `docker ps` futó tárolók és a tároló csoporthoz rendelt IP-cím megtekintéséhez.

```console
docker ps
```

Példa a kimenetre:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

A futó alkalmazás Felhőbeli megjelenítéséhez adja meg a megjelenített IP-címet egy helyi böngészőben. Ebben a példában írja be a következőt: `52.179.23.131` . A mintaalkalmazás betöltődik az alábbi példában látható módon:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Az ACI-beli szavazási alkalmazás képe":::

Az előtér-tároló naplófájljainak megtekintéséhez futtassa a [Docker logs](https://docs.docker.com/engine/reference/commandline/logs) parancsot. Például:

```console
docker logs azurevotingappredis_azure-vote-front
```

A Azure Portal vagy más Azure-eszköz használatával is megtekintheti a telepített tároló csoport tulajdonságait és állapotát.

Amikor befejezte az alkalmazás kipróbálását, állítsa le az alkalmazást és a tárolókat az alábbiakkal `docker compose down` :

```console
docker compose down
```

Ez a parancs törli a tároló csoportot Azure Container Instancesban.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a Docker-összeállítást használta a többtárolós alkalmazások helyi futtatására a Azure Container Instances-ben való futtatáshoz. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Azure tárolóregisztrációs adatbázis létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Hozzon létre egy rendszerképet, és futtasson helyileg egy többtárolós alkalmazást a Docker-összeállítás használatával
> * Az alkalmazás rendszerképének leküldése a tároló-beállításjegyzékbe
> * Azure-környezet létrehozása a Docker számára
> * Az alkalmazás üzembe helyezése Azure Container Instances

A [Docker-bővítményt a Visual Studio Code](https://aka.ms/VSCodeDocker) -hoz is használhatja a tárolók, képek és környezetek fejlesztéséhez, futtatásához és kezeléséhez.

Ha szeretné kihasználni a Azure Container Instances további funkcióit, az Azure Tools segítségével megadhat egy többtárolós csoportot. Például tekintse meg az oktatóanyagokat, amelyekkel üzembe helyezhet egy tároló csoportot az Azure CLI-vel egy [YAML-fájllal](container-instances-multi-container-yaml.md), vagy üzembe helyezheti [Azure Resource Manager sablon](container-instances-multi-container-group.md)használatával. 