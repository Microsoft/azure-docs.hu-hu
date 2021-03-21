---
title: Átmeneti környezet beállítása az Azure Spring Cloud-ban | Microsoft Docs
description: Ismerje meg, hogyan használható a kék-zöld üzembe helyezés az Azure Spring Cloud használatával
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016576"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Átmeneti környezet beállítása az Azure Spring Cloud-ban

**Ez a cikk a következőkre vonatkozik:** ✔️ Java

Ez a cikk bemutatja, hogyan állíthatja be az átmeneti üzembe helyezést a kék-zöld üzembe helyezési minta használatával az Azure Spring Cloud-ban. A kék-zöld üzembe helyezés egy Azure DevOps-alapú folyamatos kézbesítési minta, amely egy meglévő (kék) verzió megtartására támaszkodik egy új (zöld) egy üzembe helyezése közben. Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az átmeneti üzembe helyezést az éles környezetbe történő telepítés módosítása nélkül.

## <a name="prerequisites"></a>Előfeltételek

* Azure Spring Cloud-példány standard szintű díjszabással
* [Azure Spring Cloud-bővítmény](/cli/azure/azure-cli-extensions-overview) az Azure CLI-hez

Ez a cikk a Spring inicializáló által készített alkalmazást használja. Ha ehhez a példához egy másik alkalmazást szeretne használni, akkor az alkalmazás egy nyilvános részén egyszerű módosítást kell végeznie az átmeneti üzembe helyezés az éles környezetből való elkülönítéséhez.

>[!TIP]
> A [Azure Cloud Shell](https://shell.azure.com) egy ingyenes interaktív felület, amelyet a cikkben szereplő utasítások futtatására használhat.  Gyakori, előre telepített Azure-eszközöket tartalmaz, beleértve a git, a JDK, a Maven és az Azure CLI legújabb verzióit. Ha bejelentkezett az Azure-előfizetésbe, indítsa el Cloud Shell-példányát. További információ: [Azure Cloud Shell áttekintése](../cloud-shell/overview.md).

A kék-zöld üzembe helyezés az Azure Spring Cloud-ban való beállításához kövesse a következő szakasz utasításait.

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

Telepítse az Azure CLI-hez készült Azure Spring Cloud-bővítményt az alábbi paranccsal:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Az alkalmazás és a központi telepítések előkészítése
Az alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. A minta alkalmazás kódjának előállítása a Spring inicializáló használatával ezzel a [konfigurációval](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Töltse le a kódot.
3. Adja hozzá a következő HelloController. Java forrásfájlt a mappához `\src\main\java\com\example\hellospring\` :

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
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
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

A telepített alkalmazásokat a következő eljárással tekintheti meg:

1. Nyissa meg az Azure Spring Cloud-példányát a Azure Portal.

1. A bal oldali ablaktáblában nyissa meg az **alkalmazások** ablaktáblát a szolgáltatási példányhoz tartozó alkalmazások megtekintéséhez.

   ![Képernyőkép az alkalmazások megnyitása panelről.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Kijelölhet egy alkalmazást, és megtekintheti a részleteket.

   ![Képernyőkép az alkalmazás részleteiről.](media/spring-cloud-blue-green-staging/app-overview.png)

1. A **központi telepítések** megnyitásával tekintheti meg az alkalmazás összes központi telepítését. A rács az éles és átmeneti üzembe helyezéseket is megjeleníti.

   ![Képernyőkép, amely a felsorolt alkalmazások központi telepítéseit jeleníti meg.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Válassza ki az URL-címet a jelenleg telepített alkalmazás megnyitásához.
    
   ![Képernyőkép, amely az üzembe helyezett alkalmazáshoz tartozó U R L-t jeleníti meg.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Az alapértelmezett alkalmazás megjelenítéséhez válassza a **termelés** lehetőséget az **állapot** oszlopban.
    
   ![Képernyőkép, amely megjeleníti az U R L-t az alapértelmezett alkalmazáshoz.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Az átmeneti alkalmazás megjelenítéséhez válassza az **állapot** oszlopban az **előkészítés** lehetőséget.
    
   ![Képernyőkép, amely az előkészítési alkalmazáshoz tartozó U R L-t jeleníti meg.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Győződjön meg arról, hogy a tesztelési végpont perjel (/) karakterrel végződik, hogy a CSS-fájl megfelelően van-e betöltve.  
> * Ha a böngésző megköveteli a bejelentkezési hitelesítő adatok megadását a lap megtekintéséhez, használja az [URL-címet](https://www.urldecoder.org/) a teszt végpont dekódolásához. Az URL-cím dekódolása egy URL-címet ad vissza a *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green* formátumban. Ezt a formátumot használhatja a végpont eléréséhez.

>[!NOTE]    
> A konfigurációs kiszolgáló beállításai az átmeneti környezetre és az éles környezetre is érvényesek. Ha például az alkalmazás-átjáró környezeti elérési útját (*Server. servlet. Context-Path*) adja meg a konfigurációs kiszolgálón a *somepath*, a zöld telepítés elérési útja a *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/..*..
 
Ha ezen a ponton látogatja meg a nyilvános alkalmazás-átjárót, az új módosítás nélkül látnia kell a régi oldalt.

## <a name="set-the-green-deployment-as-the-production-environment"></a>A zöld telepítés beállítása éles környezetként

1. Miután ellenőrizte a változást az átmeneti környezetben, leküldheti azt az éles környezetbe. Az **alkalmazások**  >  **központi telepítései** lapon válassza ki az aktuálisan **éles** környezetben üzemelő alkalmazást.

1. A zöld telepítés **regisztrációjának állapota** után válassza a három pontot, majd válassza a **beállítás éles** környezetben lehetőséget. 

   ![Képernyőkép, amely megjeleníti az átmeneti Build éles környezetbe való beállításának beállításait.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Győződjön meg róla, hogy az alkalmazás URL-címe megjeleníti a módosításokat.

   ![Képernyőkép, amely az alkalmazás U R L-t jeleníti meg most éles környezetben.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Miután beállította a zöld telepítést üzemi környezetként, az előző üzemelő példány lesz az átmeneti üzembe helyezés.

## <a name="modify-the-staging-deployment"></a>Az átmeneti telepítés módosítása

Ha nem elégedett a módosítással, módosíthatja az alkalmazás kódját, létrehozhat egy új. jar csomagot, és feltöltheti azt a zöld üzembe helyezéshez az Azure CLI használatával:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Az előkészítési központi telepítés törlése

Ha törölni szeretné az átmeneti üzembe helyezést a Azure Portalról, nyissa meg az átmeneti üzembe helyezés lapját, és válassza a **Törlés** gombot.

Azt is megteheti, hogy az alábbi parancs futtatásával törli az átmeneti üzembe helyezést az Azure CLI-ből:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Következő lépések

* [CI/CD az Azure Spring Cloud-hoz](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)