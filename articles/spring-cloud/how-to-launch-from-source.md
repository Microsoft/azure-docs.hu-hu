---
title: A Spring Cloud-alkalmazás elindítása a forráskódból
description: Ebből a rövid útmutatóból megtudhatja, hogyan indíthatja el az Azure Spring Cloud-alkalmazást közvetlenül a forráskódból
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879270"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>A Spring Cloud-alkalmazás elindítása forráskódból

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Az Azure Spring Cloud lehetővé teszi a Spring Cloud-alapú Service-alkalmazások használatát az Azure-ban.

Alkalmazásokat közvetlenül a Java-forráskódból vagy egy előre elkészített JAR-ból indíthat. Ez a cikk a telepítési eljárásokat ismerteti.

Ez a rövid útmutató a következőket ismerteti:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi létrehozása
> * Az egyes szolgáltatások üzembe helyezése
> * Nyilvános végpont kiosztása az alkalmazáshoz

## <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, győződjön meg arról, hogy az Azure-előfizetése rendelkezik a szükséges függőségekkel:

1. [A Git telepítése](https://git-scm.com/)
2. [A JDK 8 telepítése](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [A Maven 3,0-es vagy újabb verziójának telepítése](https://maven.apache.org/download.cgi)
4. [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)
5. [Feliratkozás Azure-előfizetésre](https://azure.microsoft.com/free/)

> [!TIP]
> Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat.  A közös Azure-eszközök előre telepítve vannak, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud shellt](https://shell.azure.com) a shell.Azure.com webhelyről.  A Azure Cloud Shellről a [dokumentáció beolvasásával](../cloud-shell/overview.md) tájékozódhat bővebben

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-bővítményt az Azure CLI-hez a következő paranccsal

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Szolgáltatási példány kiépítése az Azure CLI használatával

Jelentkezzen be az Azure CLI-be, és válassza ki az aktív előfizetését. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Hozzon létre egy erőforráscsoportot, amely tartalmazza az Azure Spring Cloud Service-t. További információ az Azure- [erőforráscsoportok](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Futtassa az alábbi parancsokat az Azure Spring Cloud egy példányának kiépítéséhez. Készítse elő az Azure Spring Cloud-szolgáltatás nevét. A névnek 4 és 32 karakter közöttinek kell lennie, és csak kisbetűket, számokat és kötőjeleket tartalmazhat. A szolgáltatás nevének első karakterének betűnek kell lennie, és az utolsó karakternek betűnek vagy számnak kell lennie.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

A szolgáltatási példány körülbelül öt percet vesz igénybe.

Állítsa be az alapértelmezett erőforráscsoport-nevet és az Azure Spring Cloud instance nevet az alábbi parancsokkal:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Az Azure Spring Cloud-alkalmazás létrehozása

A következő parancs létrehoz egy Azure Spring Cloud-alkalmazást az előfizetésében.  Ez egy üres szolgáltatást hoz létre, amelyhez feltöltheti az alkalmazást.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>A Spring Cloud-alkalmazás üzembe helyezése

Az alkalmazást üzembe helyezheti egy előre elkészített JAR vagy egy Gradle vagy Maven adattárból.  Az alábbi esetekben talál útmutatást.

### <a name="deploy-a-built-jar"></a>Beépített JAR üzembe helyezése

A helyi gépre épülő JAR üzembe helyezéséhez győződjön meg arról, hogy a Build [FAT-jar-](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven)t hoz létre.

A FAT-JAR telepítése aktív központi telepítésre

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

A FAT-JAR üzembe helyezése egy adott központi telepítésben

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Üzembe helyezés forráskódból

Az Azure Spring Cloud a [kpack](https://github.com/pivotal/kpack) használatával hozza létre a projektet.  Az Azure CLI-vel feltöltheti a forráskódot, felépítheti a projektet a kpack használatával, és üzembe helyezheti azt a célalkalmazás alkalmazásban.

> [!WARNING]
> A projektnek csak egy JAR-fájlt kell létrehoznia (a Maven-környezetek esetében `main-class` `MANIFEST.MF` `target` ) vagy `build/libs` (Gradle üzemelő példányok esetén) egy bejegyzéssel.  Több JAR-fájl `main-class` bejegyzéseivel a telepítés sikertelen lesz.

Egymodulos Maven/Gradle-projektekhez:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

Több modult tartalmazó Maven/Gradle-projektek esetében ismételje meg az egyes modulokat:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Telepítési naplók megjelenítése

Tekintse át a kpack-létrehozási naplókat a következő paranccsal:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> A kpack-naplók csak akkor jelennek meg a legújabb központi telepítésben, ha a központi telepítés forrása a kpack használatával készült.

## <a name="assign-a-public-endpoint-to-gateway"></a>Nyilvános végpont társítása az átjáróhoz

1. Nyissa meg az **alkalmazás-irányítópult** lapot.
2. Válassza ki az alkalmazást az `gateway` **alkalmazás részletei** lap megjelenítéséhez.
3. Válassza a **végpont társítása** lehetőséget egy nyilvános végpontnak az átjáróhoz való hozzárendeléséhez. Ez eltarthat néhány percig. 
4. A futó alkalmazás megtekintéséhez adja meg a hozzárendelt nyilvános IP-címet a böngészőben.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Szolgáltatási példány kiépítése
> * Konfigurációs kiszolgáló beállítása egy példányhoz
> * A Services-alkalmazások helyi létrehozása
> * Az egyes szolgáltatások üzembe helyezése
> * Alkalmazások környezeti változóinak szerkesztése
> * Nyilvános IP-cím kiosztása az Application gatewayhez

> [!div class="nextstepaction"]
> [Tavaszi Felhőbeli naplók, metrikák, nyomkövetés](spring-cloud-quickstart-logs-metrics-tracing.md)

További minták érhetők el a GitHubon: [Azure Spring Cloud Samples](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).