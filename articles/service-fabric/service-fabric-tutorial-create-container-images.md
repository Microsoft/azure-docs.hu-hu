---
title: Tárolórendszerképek létrehozása a Service Fabricben az Azure-ban | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre tárolórendszerképeket többtárolós Service Fabric-alkalmazásokhoz.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: Docker, tárolók, mikroszolgáltatások, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 0bba3c936463ad4fa8b4d06bf6f9df4f0ce6b863
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752313"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Oktatóanyag: Tárolórendszerképek létrehozása Linux-alapú Service Fabric-fürtön

Ez az oktatóanyag egy olyan sorozat első része, amely azt mutatja be, hogyan használhatók tárolók a Linux Service Fabric-fürtökben. Ebben az oktatóanyagban egy többtárolós alkalmazást fog előkészíteni a Service Fabrichez. Az ezt követő oktatóanyagok ezeket a rendszerképeket fogják használni egy Service Fabric-alkalmazás részeként. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Alkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * Egy Azure Container Registry (ACR)-példány üzembe helyezése
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Tárolórendszerképek létrehozása a Service Fabrichez
> * [Tárolókkal rendelkező Service Fabric-alkalmazás összeállítása és futtatása](service-fabric-tutorial-package-containers.md)
> * [A feladatátvétel és a méretezés kezelése a Service Fabricben](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Előfeltételek

* A Linux fejlesztési környezet beállítása a Service Fabrichez. Kövesse az [itt](service-fabric-get-started-linux.md) található utasításokat a Linux környezet beállításához.
* Az oktatóanyag elvégzéséhez az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.
* Egy Azure-előfizetéssel is rendelkeznie kell. Az ingyenes próbaverzióról további információt [itt](https://azure.microsoft.com/free/) talál.

## <a name="get-application-code"></a>Az alkalmazáskód letöltése

A jelen oktatóanyagban használt mintaalkalmazás egy szavazóalkalmazás. Az alkalmazás egy előtérbeli webes összetevőből és egy háttérbeli Redis-példányból áll. Az összetevők tárolórendszerképekbe vannak csomagolva.

A git használatával töltse le az alkalmazás egy másolatát a fejlesztői környezetbe.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

A megoldás két mappát és egy „docker-compose.yml” fájlt tartalmaz. Az „azure-vote” mappa a Python előtér-szolgáltatást és a rendszerkép felépítéséhez használt Docker-fájlt tartalmazza. A „Voting” könyvtárban található meg a fürtön üzembe helyezett Service Fabric-alkalmazáscsomag. Ezek a könyvtárak tartalmazzák a jelen oktatóanyag elvégzéséhez szükséges elemeket.

## <a name="create-container-images"></a>Tárolórendszerképek létrehozása

Az **azure-vote** könyvtárban futtassa a következő parancsot az előtéri webes összetevő rendszerképének felépítéséhez. Ez a parancs a könyvtárban lévő Docker-fájl alapján építi fel a rendszerképet.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Ha az engedély megtagadását jelző hibát kap, akkor kövesse [ezt](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) a dokumentációt a docker sudo nélküli használatához.

A futtatása eltarthat egy ideig, mert az összes szükséges függőséget le kell kérnie a Docker Hubról. Amikor elkészült, a [docker images](https://docs.docker.com/engine/reference/commandline/images/) paranccsal tekintheti meg a létrehozott rendszerképeket.

```bash
docker images
```

Figyelje meg, hogy két rendszerkép töltődött le vagy jött létre. Az *azure-vote-front* rendszerkép tartalmazza az alkalmazást. Ez a Docker Hubról származó egyik *python* rendszerképből ered.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Azure Container Registry üzembe helyezése

Először futtassa az **az login** parancsot, hogy bejelentkezzen az Azure-fiókjába.

```bash
az login
```

Ezután az **az account** paranccsal válassza ki az előfizetését az Azure tárolóregisztrációs adatbázis létrehozásához. Be kell helyettesítenie a <subscription_id> kifejezést az Azure-előfizetés azonosítójával.

```bash
az account set --subscription <subscription_id>
```

Az Azure Container Registry üzembe helyezéséhez először is szükség van egy erőforráscsoportra. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az **az group create** paranccsal. Ebben a példában egy *myResourceGroup* nevű erőforráscsoportot hozunk létre a *westus* régióban.

```bash
az group create --name <myResourceGroup> --location westus
```

Hozzon létre egy Azure tárolóregisztrációs adatbázist az **az acr create** paranccsal. Cserélje le az \<acrName> kifejezést az előfizetésben létrehozni kívánt tárolóregisztrációs adatbázis nevére. Ennek a névnek alfanumerikusnak és egyedinek kell lennie.

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Az oktatóanyag hátralevő részében az „acrName” kifejezés helyettesíti a tárolóregisztrációs adatbázis választott nevét. Jegyezze fel ezt az értéket.

## <a name="log-in-to-your-container-registry"></a>Bejelentkezés a tárolóregisztrációs adatbázisba

Jelentkezzen be az ACR-példányba, mielőtt rendszerképeket próbálna leküldeni rá. Használja az **az acr login** parancsot a művelet befejezéséhez. Adja meg a tárolóregisztrációs adatbázis egyedi nevét, amelyet a létrehozásakor adott meg.

```bash
az acr login --name <acrName>
```

A parancs a „Bejelentkezés sikeres” üzenetet adja vissza, ha befejeződött.

## <a name="tag-container-images"></a>Tárolórendszerképek címkézése

Minden tárolórendszerképet fel kell címkézni a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Ezt a címkét a rendszer az útválasztáshoz használja, amikor tárolórendszerképeket küld le egy regisztrációs adatbázisba.

A meglévő rendszerképek listájának megtekintéséhez használja a [docker images](https://docs.docker.com/engine/reference/commandline/images/) parancsot.

```bash
docker images
```

Kimenet:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

A bejelentkezési kiszolgáló nevének lekéréséhez futtassa a következő parancsot:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Ez a következő eredményeket tartalmazó táblát adja ki. A rendszer ezt használja az **azure-vote-front** rendszerkép címkézéséhez, mielőtt leküldené azt a tárolóregisztrációs adatbázisra a következő lépésben.

```bash
Result
------------------
<acrName>.azurecr.io
```

Most címkézze fel az *azure-vote-front* rendszerképet a tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának nevével. Adja hozzá a `:v1` kifejezést a rendszerkép nevének végéhez. Ez a címke a rendszerkép verziószámát jelöli.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Ha elkészült a címkézéssel, futtassa a „docker images” parancsot a művelet ellenőrzéséhez.

Kimenet:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Rendszerképek leküldése a regisztrációs adatbázisba

Küldje le az *azure-vote-front* rendszerképet a regisztrációs adatbázisba. 

Az alábbi példában helyettesítse be az ACR bejelentkezési kiszolgálójának nevét az adott környezet bejelentkezési kiszolgálójának nevével.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

A docker push parancsok futtatása eltarthat néhány percig.

## <a name="list-images-in-registry"></a>A regisztrációs adatbázisban található rendszerképek felsorolása

Az Azure Container Registrybe leküldött rendszerképek listájának lekéréséhez használja az [az acr repository list](/cli/azure/acr/repository) parancsot. Frissítse a parancsot az ACR-példány nevével.

```bash
az acr repository list --name <acrName> --output table
```

Kimenet:

```bash
Result
----------------
azure-vote-front
```

Az oktatóanyag végeztével a tárolórendszerképet egy privát Azure Container Registry-példány tárolja. Ezt a rendszerképet telepítjük az ACR-ből egy Service Fabric-fürtre a következő oktatóanyagok során.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy alkalmazást kértünk le a Githubról, és tárolórendszerképeket létrehozott és leküldte a tárolójegyzékbe. A következő lépéseket hajtotta végre:

> [!div class="checklist"]
> * Alkalmazás forrásának klónozása a GitHubról
> * Tárolórendszerkép létrehozása az alkalmazás forrásából
> * Egy Azure Container Registry (ACR)-példány üzembe helyezése
> * Egy tárolórendszerkép címkézése az ACR-hez
> * A rendszerkép feltöltése az ACR-be

Folytassa a következő oktatóanyaggal, amely azt ismerteti, hogyan csomagolhatók tárolók egy Service Fabric-alkalmazásba a Yeoman használatával.

> [!div class="nextstepaction"]
> [Tárolók csomagolása és üzembe helyezése Service Fabric-alkalmazásként](service-fabric-tutorial-package-containers.md)
