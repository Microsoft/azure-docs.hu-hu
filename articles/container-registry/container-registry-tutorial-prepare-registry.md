---
title: Oktatóanyag – Georeplikált beállításjegyzék létrehozása
description: Létrehozhat egy Azure Container Registryt, georeplikációt konfigurálhat, előkészíthet egy Docker-rendszerképet, és üzembe helyezheti azt a tárolójegyzékben. Ez egy háromrészes sorozat első része.
ms.topic: tutorial
ms.date: 06/30/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: c5f5627462f016c857d956be46c276dda78e9b2b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780658"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Oktatóanyag: Georeplikált Azure Container Registry előkészítése

Az Azure Container Registryk az Azure-ban üzembe helyezett privát Docker-tárolójegyzékek, amelyeket a hálózaton elérhető távolságban tarthat az üzemelő példányokhoz. Ebből a háromrészes oktatóanyagból megtudhatja, hogyan használhat georeplikációt egy Linux-tárolóban futó ASP.NET Core-webalkalmazás két [Web Apps for Containers](../app-service/index.yml)-példányban történő üzembe helyezéséhez. Láthatja, hogyan helyezi üzembe automatikusan az Azure a rendszerképet minden Web App-példányon a legközelebbi georeplikált adattárból.

A háromrészes sorozat első oktatóanyagának tartalma:

> [!div class="checklist"]
> * Egy georeplikált Azure Container Registry létrehozása
> * Alkalmazás forráskódjának klónozása a GitHubról
> * Docker-tároló rendszerképének összeállítása alkalmazásforrásból
> * Tároló rendszerképének leküldése a tárolójegyzékbe

A következő oktatóanyagokban egy két Azure-régióban futó webalkalmazás számára helyezi üzembe a tárolót a privát tárolójegyzékből. Ezután frissíti az alkalmazásban lévő programkódot, és egyetlen `docker push` paranccsal frissíti mindkét webalkalmazás-példányt a tárolójegyzékben.

## <a name="before-you-begin"></a>Előkészületek

Az oktatóanyaghoz az Azure CLI (2.0.31-es vagy újabb verzió) helyi telepítésére lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

Ismernie kell a Docker fő fogalmait, például a tárolókat, a tárolórendszerképeket és a Docker parancssori felületének alapszintű parancsait. A tárolókkal kapcsolatos alapfogalmakért tekintse meg [a Docker használatának első lépéseivel]( https://docs.docker.com/get-started/) foglalkozó témakört.

Az oktatóanyag elvégzéséhez a szüksége lesz egy helyi Docker-telepítésre. A Docker a [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) és [Linux](https://docs.docker.com/engine/installation/#supported-platforms) rendszerhez biztosít telepítési utasításokat.

Az Azure Cloud Shell nem tartalmazza a jelen oktatóanyag lépéseinek elvégzéséhez szükséges Docker-összetevőket, Ezért ajánlott az Azure CLI és a Docker fejlesztőkörnyezet helyi telepítése.

## <a name="create-a-container-registry"></a>Tárolóregisztrációs adatbázis létrehozása

Ebben az oktatóanyagban szüksége lesz egy Azure Container Registryre a Prémium szolgáltatási szinten. Új Azure Container Registry létrehozásához kövesse az ebben a szakaszban található lépéseket.

> [!TIP]
> Ha korábban létrehozott egy beállításjegyzéket, és frissítenie kell, tekintse meg a [következőt: Szintek módosítása.](container-registry-skus.md#changing-tiers) 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Válassza **az Erőforrás létrehozása**  >  **Tárolók Azure Container Registry.**  >  

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-01.png" alt-text="Tároló-beállításjegyzék létrehozása az Azure Portalon":::

Konfigurálja az új beállításjegyzéket az alábbi beállításokkal. Az Alapvető **beállítások lapon:**

* **Regisztrációs adatbázis neve**: Olyan nevet hozzon létre a tárolójegyzékhez, amely globálisan egyedi az Azure-ban, és 5–50 alfanumerikus karakterből áll.
* **Erőforráscsoport:** **Új létrehozása** > `myResourceGroup`
* **Hely:**`West US`
* **Termékváltozat:** `Premium` (georeplikációhoz szükséges)

A **beállításjegyzék-példány létrehozásához** válassza az Áttekintés + **létrehozás,** majd a Létrehozás lehetőséget.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-02.png" alt-text="Tároló-beállításjegyzék konfigurálása a Azure Portal":::

Az oktatóanyag hátralevő részében a nevet használjuk helyőrzőként a választott `<acrName>` tároló-beállításjegyzék-névhez. 

> [!TIP]
> Mivel az Azure tárolójegyzékek általában több tárológazdagép által használt hosszútávú erőforrások, ajánlott a saját erőforráscsoportjában létrehozni a tárolójegyzéket. A georeplikált tárolójegyzékek és webhookok konfigurálásakor a további erőforrások ugyanabba az erőforráscsoportba kerülnek.

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

Most, hogy egy prémium szintű tárolójegyzékkel rendelkezik, konfigurálhatja a georeplikációt. A webalkalmazás – amelyet a következő oktatóanyagban konfigurál a két régióban való futtatáshoz – ezután le tudja kérni a tároló rendszerképeit a legközelebbi tárolójegyzékből.

Lépjen az új tárolójegyzékhez a Azure Portal majd válassza a **Replikációk** lehetőséget a **Szolgáltatások alatt:**

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-03.png" alt-text="Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén":::

Megjelenik egy térkép a georeplikációhoz elérhető Azure-régiókat jelző zöld hatszögekkel:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-map-01.png" alt-text="Régiótérkép az Azure Portalon":::

Replikálja a tárolójegyzékét az USA keleti régiójába a zöld hatszög kiválasztásával, majd válassza a **Replikáció létrehozása** területen lévő **Létrehozás** lehetőséget:

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-04.png" alt-text="Replikáció létrehozásának felhasználói felülete az Azure Portalon":::

A replikáció befejezése után a portál mindkét régió esetén a *Kész* állapotot jeleníti meg. A **Frissítés** gombbal frissítse a replikáció állapotát. Körülbelül egy percbe telhet a replikák létrehozása és szinkronizálása.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-05.png" alt-text="Replikációs állapot felhasználói felülete az Azure Portalon":::


## <a name="enable-admin-account"></a>Rendszergazdai fiók engedélyezése

Az ezt követő oktatóanyagokban egy tároló rendszerképét helyezheti üzembe a regisztrációs adatbázisból közvetlenül a Web App for Containers. A képesség engedélyezéséhez a beállításjegyzék rendszergazdai fiókját is [engedélyeznie kell.](container-registry-authentication.md#admin-account)

Lépjen az új tároló-beállításjegyzékhez a Azure Portal majd válassza a **Hozzáférési kulcsok** lehetőséget a **Beállítások alatt.** A **Rendszergazdai felhasználó** elem alatt válassza az **Engedélyezés** lehetőséget.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-portal-06.png" alt-text="Rendszergazdai fiók engedélyezése a Azure Portal":::


## <a name="container-registry-login"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Most, hogy konfigurálta a georeplikációt, állítson össze egy tárolórendszerképet, és küldje le azt a tárolójegyzékbe. Először be kell jelentkeznie a regisztrációs adatbázisba, mielőtt rendszerképeket ad hozzá.

Az [az acr login](/cli/azure/acr#az_acr_login) paranccsal hitelesítheti és gyorsítótárazhatja a tárolójegyzék hitelesítő adatait. Cserélje le a `<acrName>` elemet a korábban létrehozott tárolójegyzék nevére.

```azurecli
az acr login --name <acrName>
```

A parancs a `Login Succeeded` értéket adja vissza, ha befejeződött.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

Az oktatóanyagban foglalt példa egy, az [ASP.NET Core][aspnet-core] használatával létrehozott kis webalkalmazást tartalmaz. Az alkalmazás egy HTML-oldalt működtet, amely azt a régiót jeleníti meg, amelyről az Azure Container Registry a rendszerképet üzembe helyezte.

:::image type="content" source="./media/container-registry-tutorial-prepare-registry/tut-app-01.png" alt-text="Az oktatóanyag alkalmazása böngészőben megjelenítve":::

Töltse le a mintát a Git használatával egy helyi könyvtárba, és a `cd` használatával lépjen be a könyvtárba:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Ha a `git` nincs telepítve, közvetlenül a GitHubról [töltheti le a ZIP-archívumot][acr-helloworld-zip].

## <a name="update-dockerfile"></a>A Dockerfile frissítése

A mintában szereplő Dockerfile bemutatja a tároló összeállításának menetét. Egy hivatalos ASP.NET Core futásidejű rendszerképből indul ki, átmásolja az alkalmazásfájlokat a tárolóba, telepíti a függőségeket, lefordítja a kimenetet a hivatalos .NET Core SDK-rendszerkép használatával, és végül egy optimalizált aspnetcore-rendszerképet hoz létre.

A [Dockerfile][dockerfile] a klónozott forrásban a következő helyen található: `./AcrHelloworld/Dockerfile`.

```Dockerfile
FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM mcr.microsoft.com/dotnet/core/sdk:2.2 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

Az *acr-helloworld* rendszerképben lévő alkalmazás megpróbálja meghatározni azt a régiót, amelyből a tárolója üzembe lett helyezve. Ehhez lekérdezi a DNS-ből a tárolójegyzék bejelentkezési kiszolgálójával kapcsolatos adatokat. A tárolójegyzék bejelentkezési kiszolgálójának teljes tartománynevét a Dockerfile `DOCKER_REGISTRY` környezeti változójában kell megadnia.

Először kérje le a tárolójegyzék bejelentkezési kiszolgálóját az `az acr show` paranccsal. Cserélje le az `<acrName>` elemet az előző lépésekben létrehozott tárolójegyzék nevére.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Kimenet:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Ezután frissítse az `ENV DOCKER_REGISTRY` sort a tárolójegyzék bejelentkezési kiszolgálójának teljes tartománynevével. Ebben a példában a példa tárolójegyzékének neve (*uniqueregistryname*) látható:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Tárolórendszerkép összeállítása

Most, hogy a frissítette a Dockerfile-t a tárolójegyzék bejelentkezési kiszolgálójának teljes tartománynevével, a `docker build` paranccsal hozhatja létre a tárolórendszerképet. Futtassa a következő parancsot a rendszerkép összeállításához és a privát tárolójegyzék URL-címével való felcímkézésével, majd ismét cserélje le az `<acrName>` elemet a tárolójegyzék nevére:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Több kimeneti sor jelenik meg a Docker-rendszerkép összeállításakor (itt csonkolva láthatók):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM mcr.microsoft.com/dotnet/core/aspnet:2.2 AS base
2.2: Pulling from mcr.microsoft.com/dotnet/core/aspnet
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

A `docker images` használatával tekintse meg a felépített és címkézett rendszerképet:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

Ezután a `docker push` paranccsal küldje le az *acr-helloworld*-rendszerképet a tárolójegyzékbe. Cserélje le a `<acrName>` elemet a tárolójegyzék nevére.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Mivel georeplikációhoz konfigurálta a tárolójegyzéket, a rendszerkép ezzel az egy `docker push` paranccsal automatikusan replikálva lesz az *USA nyugati régióján* és az *USA keleti régióján* is.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy privát, georeplikált tárolójegyzéket, felépített egy tárolórendszerképet, majd ezt a rendszerképet leküldte a tárolójegyzékbe.

Folytassa a következő oktatóanyaggal, amely üzembe helyezi a tárolót több Web Apps for Containers-példányon, georeplikációt használva a rendszerképek helyi kiszolgálásához.

> [!div class="nextstepaction"]
> [Webalkalmazás üzembe helyezése az Azure Container Registryből](container-registry-tutorial-deploy-app.md)

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile
