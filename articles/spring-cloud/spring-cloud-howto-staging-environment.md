---
title: Átmeneti környezet beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan használható a kék-zöld üzembe helyezés az Azure Spring Cloud használatával
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d610e79773dabb9942352747f1f032b17730ffca
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738752"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Átmeneti környezet beállítása az Azure Spring Cloud-ban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ez a cikk bemutatja, hogyan állíthat be átmeneti üzembe helyezést a kék-zöld üzembe helyezési mintával az Azure Spring Cloud használatával. A kék-zöld üzembe helyezés egy Azure DevOps-alapú folyamatos kézbesítési minta, amely egy meglévő (kék) verzió megtartására támaszkodik, míg egy új (zöld). Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az átmeneti üzembe helyezést az éles környezetbe történő telepítés módosítása nélkül.

## <a name="prerequisites"></a>Előfeltételek

* Azure Spring Cloud-példány *standard szintű* **díjszabással**.
* Azure CLI [Azure Spring Cloud-bővítmény](/cli/azure/azure-cli-extensions-overview)

Ez a cikk a Spring inicializáló által készített alkalmazást használja. Ha ehhez a példához egy másik alkalmazást szeretne használni, akkor az alkalmazás nyilvános részén egyszerű módosítást kell végeznie az átmeneti üzembe helyezés az éles környezetből való elkülönítéséhez.

>[!TIP]
> A Azure Cloud Shell egy ingyenes interaktív felület, amelyet a cikkben szereplő utasítások futtatására használhat.  Gyakori, előre telepített Azure-eszközöket tartalmaz, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el a [Azure Cloud Shell](https://shell.azure.com).  További információ: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md).

A kék-zöld környezetek Azure Spring Cloud-beli üzembe helyezéséhez kövesse a következő szakasz utasításait.

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Alkalmazások és központi telepítések előkészítése
Az alkalmazás létrehozásához kövesse az alábbi lépéseket:
1. A minta alkalmazás kódjának előállítása a Spring inicializáló használatával ezzel a [konfigurációval](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Töltse le a kódot.
3. Adja hozzá a következő forrásfájlt a HelloController. Java fájlhoz a mappához `\src\main\java\com\example\hellospring\` .
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud!"; 
  } 

} 
```
4. Hozza létre a. jar fájlt:
```azurecli
mvn clean packge -DskipTests
```
5. Hozza létre az alkalmazást az Azure Spring Cloud-példányában:
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --is-public
```
6. Az alkalmazás üzembe helyezése az Azure Spring Cloud-on:
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. A kód módosítása az átmeneti üzembe helyezéshez:
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. Hozza létre újra a. jar fájlt:
```azurecli
mvn clean packge -DskipTests
```
9. Hozza létre a zöld üzembe helyezést: 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>Alkalmazások és központi telepítések megtekintése

A telepített alkalmazásokat a következő eljárásokkal tekintheti meg.

1. Nyissa meg az Azure Spring Cloud-példányát a Azure Portal.

1. A bal oldali navigációs panelen nyissa meg az "alkalmazások" panelt a szolgáltatási példányhoz tartozó alkalmazások megtekintéséhez.

    [![Alkalmazások – irányítópult](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Rákattinthat egy alkalmazásra, és megtekintheti a részleteket.

    [![Alkalmazások – áttekintés](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. A **központi telepítések** megnyitásával tekintheti meg az alkalmazás összes központi telepítését. A rács az éles és átmeneti üzembe helyezéseket is megjeleníti.

    [![Alkalmazás/központi telepítések irányítópult](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Kattintson az URL-címre a jelenleg telepített alkalmazás megnyitásához.
    ![Telepített URL-cím](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Az alapértelmezett alkalmazás megjelenítéséhez kattintson az **állapot** oszlopban az **éles üzem** elemre.
    ![Alapértelmezett futás](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Az átmeneti alkalmazás megjelenítéséhez kattintson az **állapot** oszlopban az **előkészítés** elemre.
    ![Előkészítés folyamatban](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Győződjön meg arról, hogy a tesztelési végpont perjel (/) karakterrel végződik, hogy a CSS-fájl megfelelően van-e betöltve.  
> * Ha a böngésző megköveteli a bejelentkezési hitelesítő adatok megadását a lap megtekintéséhez, használja az [URL-címet](https://www.urldecoder.org/) a teszt végpont dekódolásához. Az URL-cím dekódolása egy "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green" formátumú URL-címet ad vissza.  Ezt az űrlapot használhatja a végpont eléréséhez.

>[!NOTE]    
> A konfigurációs kiszolgáló beállításai az átmeneti környezetre és a termelésre egyaránt érvényesek. Ha például az alkalmazás-átjáró környezeti elérési útját ( `server.servlet.context-path` ) a konfigurációs kiszolgálón a *somepath* értékre állítja, a zöld telepítés elérési útja a "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/..." értékre változik.
 
 Ha ezen a ponton látogatja meg a nyilvános alkalmazás-átjárót, az új módosítás nélkül látnia kell a régi oldalt.

## <a name="set-the-green-deployment-as-the-production-environment"></a>A zöld telepítés beállítása éles környezetként

1. Miután ellenőrizte a változást az átmeneti környezetben, leküldheti azt az éles környezetbe. Az **alkalmazások** / **központi telepítései** lapon válassza ki a jelenleg használt alkalmazást `Production` .

1. Kattintson a zöld telepítés **regisztrációs állapota** után a három pontra, és állítsa be az átmeneti buildet éles környezetbe. 

   [![Éles környezet beállítása előkészítéshez](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Most az alkalmazás URL-címének meg kell jelennie a módosításokat.

   ![Előkészítés most az üzembe helyezés során](media/spring-cloud-blue-green-staging/new-production-deployment.png)

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