---
title: 'Oktatóanyag: Linux Java-alkalmazás a MongoDB-sel'
description: Megtudhatja, hogyan használhat adatvezérelt Linux Java-alkalmazást az Azure App Service-ban, és hogyan létesít kapcsolatot egy Azure-ban futó MongoDB-hez (Cosmos DB).
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 7a0dd1198bcad675f7662a2cf4eb369f2a276445
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479406"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Oktatóanyag: Java Spring Boot webalkalmazás összeállítása Azure App Service on Linux és Azure Cosmos DB

Ez az oktatóanyag végigvezeti a Java-webalkalmazások Azure-ban való építésén, konfigurálásán, üzembe helyezésén és méretezésén. Ha elkészült, egy olyan alkalmazással fog [Spring Boot,](https://projects.spring.io/spring-boot/) amely a következő helyen [Azure Cosmos DB](../cosmos-db/index.yml) adatokat [Azure App Service on Linux.](overview.md)

![Spring Boot alkalmazás az adatokat a Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Cosmos DB-adatbázist.
> * Mintaalkalmazás csatlakoztatása az adatbázishoz és annak helyi tesztelése
> * A mintaalkalmazás üzembe helyezése az Azure-ban
> * Diagnosztikai naplók streamelése a App Service
> * További példányok hozzáadása a mintaalkalmazás horizontális felskálához

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A saját számítógépére telepített [Azure CLI.](/cli/azure/overview) 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>A minta TODO alkalmazás klónozása és az alkalmazás előkészítése

Ez az oktatóanyag egy minta TODO list alkalmazást használ egy webes felhasználói felülettel, amely egy [Spring Data-REST API által](https://github.com/Microsoft/spring-data-cosmosdb)Azure Cosmos DB. Az alkalmazás kódja elérhető a [GitHubon.](https://github.com/Microsoft/spring-todo-app) A Java-alkalmazások Spring és Cosmos DB használatával való írásával kapcsolatos további információkért tekintse meg az [Spring Boot Startert](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) az Azure Cosmos DB SQL API-val című oktatóanyagot és a [Spring Data Azure Cosmos DB gyors útmutatót.](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)


Futtassa az alábbi parancsokat a terminálban a mintaadattára klónozásához és a mintaalkalmazás-környezet beállításához.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Kövesse az alábbi lépéseket egy Azure Cosmos DB létrehozásához az előfizetésében. A TODO list alkalmazás ehhez az adatbázishoz csatlakozik, és futtatáskor tárolja az adatait, és az alkalmazás állapotát az alkalmazás futtatás helyének függetlenül tárolja.

1. Jelentkezzen be az Azure CLI-be, és igény szerint állítsa be az előfizetését, ha több is csatlakozik a bejelentkezési hitelesítő adataihoz.

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Hozzon létre egy Azure-erőforráscsoportot, és nevezze el az erőforráscsoportot.

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Hozzon Azure Cosmos DB a `GlobalDocumentDB` következővel: . A név Cosmos DB csak kisbetűket használhat. Jegyezze fel `documentEndpoint` a parancs válaszában a mezőt.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Szerezze be Azure Cosmos DB kulcsot az alkalmazáshoz való csatlakozáshoz. Tartsa a `primaryMasterKey` közelében a et, mert a következő lépésben `documentEndpoint` szüksége lesz rájuk.

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>A TODO alkalmazás tulajdonságainak konfigurálása

Nyisson meg egy terminált a számítógépén. Másolja ki a klónozott adattáraban található parancsfájlmintát, hogy testre szabni tudja az Cosmos DB létrehozott adatbázishoz.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
`.scripts/set-env-variables.sh`Szerkessze a adatokat a kedvenc szerkesztőjében, és Azure Cosmos DB adatokat. Az App Service Linux-konfigurációhoz használja ugyanazt a régiót, mint a korábban ( ) és az erőforráscsoportot () az adatbázis `your-resource-group-region` `your-azure-group-name` Cosmos DB használni. Válasszon egy WEBAPP_NAME egyedi nevet, mivel nem duplikálhatja a webalkalmazás nevét egy Azure-beli üzemelő példányban sem.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Ezután futtassa a szkriptet:

```bash
source .scripts/set-env-variables.sh
```
   
Ezeket a környezeti változókat a TODO list alkalmazás `application.properties` használja. A tulajdonságfájl mezői beállítják a Spring Data alapértelmezett adattár-konfigurációját:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Ezután a mintaalkalmazás a alkalmazásból importált jegyzet használatával beállít egy entitástípust, amelyet a rendszer tárol és `@Document` `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

A maven használatával futtassa a mintát.

```bash
mvn package spring-boot:run
```

A kimenetnek az alábbihoz hasonlónak kell lennie.

```output
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

A Spring TODO alkalmazást a következő hivatkozással, az alkalmazás indítódása után helyileg is elérheti: `http://localhost:8080/` .

 ![A Spring TODO alkalmazás helyi elérése](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Ha a "Started TodoApplication" üzenet helyett kivételeket lát, ellenőrizze, hogy az előző lépésben használt szkript megfelelően exportálta-e a környezeti változókat, és hogy az értékek helyesek-e a létrehozott `bash` Azure Cosmos DB-adatbázishoz.

## <a name="configure-azure-deployment"></a>Az Azure-telepítés konfigurálása

Nyissa meg `pom.xml` a fájlt a könyvtárban, és adja hozzá a következő Azure Web App Plugin for `initial/spring-boot-todo` [Maven-konfigurációt.](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.13.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property> 
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>

        </configuration>
    </plugin>           
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Üzembe helyezés App Service on Linux

A Maven célja a TODO alkalmazás üzembe `mvn azure-webapp:deploy` helyezése a Azure App Service on Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.11.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Auth Type : AZURE_CLI, Auth Files : [C:\Users\testuser\.azure\azureProfile.json, C:\Users\testuser\.azure\accessTokens.json]
[INFO] Subscription : xxxxxxxxx
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

A kimenet tartalmazza az üzembe helyezett alkalmazás URL-címét (ebben a példában `https://spring-todo-app.azurewebsites.net` ). Ezt az URL-címet a webböngészőbe másolhatja, vagy futtathatja a terminálablakban a következő parancsot az alkalmazás betöltéséhez.

```bash
explorer https://spring-todo-app.azurewebsites.net
```

A címsorban a távoli URL-címmel futó alkalmazásnak kell futnia:

 ![Spring Boot URL-címet futtató alkalmazás futtatása](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>A TODO alkalmazás horizontális felskál letöltése

Skálázja fel horizontálisan az alkalmazást egy másik feldolgozó hozzáadásával:

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége egy másik oktatóanyaghoz (lásd a következő [lépéseket),](#next)akkor a következő parancs futtatásával törölheti őket a Cloud Shell:Â â€ â 
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

[Azure Java-fejlesztőknek](/java/azure/) 
 [Spring Boot](https://spring.io/projects/spring-boot), [Spring Data for Cosmos DB](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db), [Azure Cosmos DB](../cosmos-db/introduction.md) és [App Service Linux](overview.md).

További információ a Java-alkalmazások App Service on Linux fejlesztői útmutatóban.

> [!div class="nextstepaction"] 
> [Java a Linuxos App Service-ben – fejlesztői útmutató](configure-language-java.md?pivots=platform-linux)
