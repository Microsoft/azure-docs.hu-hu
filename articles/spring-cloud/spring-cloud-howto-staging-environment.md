---
title: Átmeneti környezet beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan használható a kék-zöld üzembe helyezés az Azure Spring Cloud használatával
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053063"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Átmeneti környezet beállítása az Azure Spring Cloud-ban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ebből a cikkből megtudhatja, hogyan állíthatja be az átmeneti üzembe helyezést a kék-zöld üzembe helyezési minta használatával az Azure Spring Cloud-ban. A kék/zöld üzembe helyezés egy Azure DevOps folyamatos kézbesítési minta, amelynek lényege, hogy működésben tart egy meglévő (kék) verziót, miközben üzembe helyez egy új (zöld) verziót. Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az előkészítési telepítést éles környezetben anélkül, hogy az éles üzembe helyezést közvetlenül módosítaná.

## <a name="prerequisites"></a>Előfeltételek

* Egy futó alkalmazás.  Lásd [: gyors útmutató: az első Azure Spring Cloud-alkalmazás üzembe helyezése](spring-cloud-quickstart.md).
* Azure CLI [Asc bővítmény](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview)

Ha ehhez a példához egy másik alkalmazást szeretne használni, egyszerű módosítást kell végeznie az alkalmazás nyilvános részén.  Ez a változás megkülönbözteti az átmeneti üzembe helyezést az éles környezetben.

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amelyet a cikkben szereplő utasítások futtatására használhat.  Gyakori, előre telepített Azure-eszközöket tartalmaz, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud Shell](https://shell.azure.com).  További információ: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md).

Ha átmeneti környezetet szeretne beállítani az Azure Spring Cloud-ban, kövesse a következő szakasz utasításait.

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Alkalmazások és központi telepítések megtekintése

A telepített alkalmazásokat a következő eljárásokkal tekintheti meg.

1. Nyissa meg az Azure Spring Cloud-példányát a Azure Portal.

1. A bal oldali navigációs ablaktáblán nyissa meg a **központi telepítéseket**.

    [![Központi telepítés – elavult](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Nyissa meg az "alkalmazások" panelt a szolgáltatási példányhoz tartozó alkalmazások megtekintéséhez.

    [![Alkalmazások – irányítópult](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Rákattinthat egy alkalmazásra, és megtekintheti a részleteket.

    [![Alkalmazások – áttekintés](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. A **központi telepítések** panel megnyitásával tekintheti meg az alkalmazás összes központi telepítését. Az üzembe helyezési rács mutatja, hogy a telepítés éles vagy átmeneti.

    [![Központi telepítések irányítópultja](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Az üzembe helyezés áttekintését a központi telepítés nevére kattintva tekintheti meg. Ebben az esetben az egyetlen központi telepítés neve *alapértelmezett*.

    [![Központi telepítések áttekintése](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Előkészítési központi telepítés létrehozása

1. A helyi fejlesztési környezetben végezze el az alkalmazás kis módosítását. Így egyszerűen megkülönböztetheti a két üzemelő példányt. A jar-csomag létrehozásához futtassa a következő parancsot: 

    ```console
    mvn clean package -DskipTests
    ```

1. Az Azure CLI-ben hozzon létre egy új központi telepítést, és adja meg a "zöld" átmeneti telepítési nevet.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. Miután a CLI üzembe helyezése sikeresen befejeződött, nyissa meg az **alkalmazás irányítópultját**, és tekintse meg az összes példányát a bal oldali **központi telepítések** lapon.

   [![Központi telepítések irányítópult zöld üzembe helyezés után](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> A felderítési állapot *OUT_OF_SERVICE* , így az ellenőrzés befejeződése előtt a rendszer nem irányítja át a forgalmat ehhez a központi telepítéshez.

## <a name="verify-the-staging-deployment"></a>Az átmeneti telepítés ellenőrzése

Annak ellenőrzéséhez, hogy a zöld átmeneti fejlesztés működik-e:
1. Lépjen az **üzembe helyezések** pontra, és kattintson az `green` **átmeneti telepítésre**.
1. Az **Áttekintés** lapon kattintson a **teszt végpontra**.
1. Ekkor megnyílik a módosításokat bemutató előkészítési Build.

>[!TIP]
> * Győződjön meg arról, hogy a tesztelési végpont perjel (/) karakterrel végződik, hogy a CSS-fájl megfelelően van-e betöltve.  
> * Ha a böngésző megköveteli a bejelentkezési hitelesítő adatok megadását a lap megtekintéséhez, használja az [URL-címet](https://www.urldecoder.org/) a teszt végpont dekódolásához. Az URL-cím dekódolása egy "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green" formátumú URL-címet ad vissza.  Ezt az űrlapot használhatja a végpont eléréséhez.

>[!NOTE]    
> A konfigurációs kiszolgáló beállításai az átmeneti környezetre és a termelésre egyaránt érvényesek. Ha például az alkalmazás-átjáró környezeti elérési útját ( `server.servlet.context-path` ) a konfigurációs kiszolgálón a *somepath* értékre állítja, a zöld telepítés elérési útja a "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/..." értékre változik.
 
 Ha ezen a ponton látogatja meg a nyilvános alkalmazás-átjárót, az új módosítás nélkül látnia kell a régi oldalt.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>A zöld telepítés beállítása éles környezetként

1. Miután ellenőrizte a változást az átmeneti környezetben, leküldheti azt az éles környezetbe. Térjen vissza a **központi telepítés felügyeletéhez**, és válassza ki a jelenleg használt alkalmazást `Production` .

1. A **regisztrációs állapot** után kattintson a három pontra, majd állítsa be a termelési buildet a értékre `staging` .

   [![Üzembe helyezések előkészítési központi telepítésének beállítása](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Térjen vissza a **központi telepítés kezelése** lapra.  A `green` központi telepítés központi telepítési állapota megjelenik. Most már fut a futó éles környezet.

   [![Központi telepítés előkészítési eredményének beállítása](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Másolja és illessze be az URL-címet egy új böngészőablakba, és az új alkalmazás oldalának meg kell jelennie a módosításaival.

>[!NOTE]
> Miután beállította a zöld telepítést üzemi környezetként, az előző üzemelő példány lesz az átmeneti üzembe helyezés.

## <a name="modify-the-staging-deployment"></a>Az átmeneti telepítés módosítása

Ha nem elégedett a módosítással, módosíthatja az alkalmazás kódját, létrehozhat egy új jar-csomagot, és feltöltheti azt a zöld üzembe helyezéshez az Azure CLI használatával.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Az előkészítési központi telepítés törlése

Ha törölni szeretné az átmeneti üzembe helyezést az Azure-portról, nyissa meg az átmeneti üzembe helyezés lapot, majd kattintson a **Törlés** gombra.

Azt is megteheti, hogy az alábbi parancs futtatásával törli az átmeneti üzembe helyezést az Azure CLI-ből:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Következő lépések

* [CI/CD az Azure Spring Cloud-hoz](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)