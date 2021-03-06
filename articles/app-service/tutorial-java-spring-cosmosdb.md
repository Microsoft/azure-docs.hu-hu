---
title: 'Oktatóanyag: linuxos Java-alkalmazás és MongoDB'
description: Megtudhatja, hogyan szerezhet be egy Azure App Serviceon működő, adatvezérelt linuxos Java-alkalmazást az Azure-ban (Cosmos DB) futó MongoDB való kapcsolattal.
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a481e7ef37e36578b7f71a7afe70dcad737de89
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217465"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Oktatóanyag: Java Spring boot-Webalkalmazás létrehozása Azure App Service Linux és Azure Cosmos DB rendszeren

Ez az oktatóanyag végigvezeti a Java-webalkalmazások Azure-ban való létrehozásának, konfigurálásának, üzembe helyezésének és méretezésének folyamatán. Ha elkészült, egy [Spring boot](https://projects.spring.io/spring-boot/) -alkalmazással fog rendelkezni, amely az [Azure app Service Linux](overview.md)rendszeren futó [Azure Cosmos DBban](../cosmos-db/index.yml) tárolja az adattárolást.

![A Spring boot Application az adattárolást Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzon létre egy Cosmos DB adatbázist.
> * Egy minta alkalmazás összekötése az adatbázissal és a helyi tesztelés
> * A minta alkalmazás üzembe helyezése az Azure-ban
> * Stream diagnosztikai naplók a App Serviceból
> * További példányok hozzáadása a minta alkalmazás felskálázásához

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Saját számítógépre telepített [Azure CLI](/cli/azure/overview). 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>A minta TODO-alkalmazás klónozása és a tárház előkészítése

Ez az oktatóanyag egy webes felhasználói felülettel rendelkező minta TODO-lista alkalmazást használ, amely a Spring [adatAzure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb)által támogatott Spring Rest APIt hív meg. Az alkalmazás kódja elérhető [a githubon](https://github.com/Microsoft/spring-todo-app). Ha többet szeretne megtudni a Java-alkalmazások Spring és Cosmos DB használatával történő írásához, tekintse meg a [Spring boot Starter és a Azure Cosmos db SQL API oktatóanyag](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) és a [Spring adatai Azure Cosmos db gyors üzembe helyezés](https://github.com/Microsoft/spring-data-cosmosdb#quick-start)című témakört.


Futtassa a következő parancsokat a terminálon a minta-tárház klónozásához és a minta alkalmazás környezetének beállításához.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB létrehozása

Az alábbi lépéseket követve létrehozhat egy Azure Cosmos DB adatbázist az előfizetésében. A TODO List alkalmazás csatlakozni fog ehhez az adatbázishoz, és a futtatásakor tárolja az adattárolási állapotot, függetlenül az alkalmazás futtatásának helyétől.

1. Jelentkezzen be az Azure CLI-be, és szükség esetén állítsa be az előfizetést, ha több van csatlakoztatva a bejelentkezési hitelesítő adataihoz.

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Hozzon létre egy Azure-erőforráscsoportot, és adja meg az erőforráscsoport nevét.

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Hozzon létre Azure Cosmos DB a `GlobalDocumentDB` típussal. Cosmos DB nevének csak kisbetűket kell használnia. Jegyezze fel a `documentEndpoint` parancs válaszában szereplő mezőt.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Azure Cosmos DB kulcsának beszerzése az alkalmazáshoz való kapcsolódáshoz. A `primaryMasterKey` `documentEndpoint` következő lépésben meg kell őriznie a közeli lépéseket.

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>A TODO alkalmazás tulajdonságainak konfigurálása

Nyisson meg egy terminált a számítógépén. Másolja a minta parancsfájlt a klónozott tárházba, hogy testreszabja az imént létrehozott Cosmos DB-adatbázishoz.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Szerkessze `.scripts/set-env-variables.sh` kedvenc szerkesztőjét, és adja meg Azure Cosmos db a kapcsolatok adatait. A App Service Linux-konfiguráció esetében használja ugyanazt a régiót, mint az előző ( `your-resource-group-region` ) és az erőforráscsoport ( `your-azure-group-name` ), amelyet a rendszer a Cosmos db-adatbázis létrehozásakor használ. Olyan WEBAPP_NAME válasszon, amely egyedi, mert nem tudja duplikálni a webalkalmazások nevét bármely Azure-telepítésben.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Ezután futtassa a parancsfájlt:

```bash
source .scripts/set-env-variables.sh
```
   
Ezeket a környezeti változókat a `application.properties` Todo List alkalmazásban lehet használni. A Properties fájlban lévő mezők a Spring-adat alapértelmezett tárház-konfigurációját állítják be:

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

Ezután a minta alkalmazás a `@Document` -ból importált megjegyzéssel `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` állítja be a Cosmos db által tárolt és felügyelt entitás típusát:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>A mintaalkalmazás futtatása

A minta futtatásához használja a Mavent.

```bash
mvn package spring-boot:run
```

A kimenetnek az alábbihoz hasonlóan kell kinéznie.

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

Az alkalmazás elindítása után az alábbi hivatkozással érheti el helyileg a Spring TODO alkalmazást: `http://localhost:8080/` .

 ![A Spring TODO-alkalmazás helyi elérése](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Ha a "Started TodoApplication" üzenet helyett kivételek jelennek meg, ellenőrizze, hogy az `bash` előző lépésben szereplő parancsfájl megfelelően exportálta-e a környezeti változókat, és hogy helyesek-e a létrehozott Azure Cosmos db adatbázishoz tartozó értékek.

## <a name="configure-azure-deployment"></a>Az Azure-telepítés konfigurálása

Nyissa meg a `pom.xml` fájlt a `initial/spring-boot-todo` címtárban, és adja hozzá a következő  [Azure Web App beépülő modult a Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) -konfigurációhoz.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.11.0</version>
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

## <a name="deploy-to-app-service-on-linux"></a>Üzembe helyezés a Linuxon App Service

A `mvn azure-webapp:deploy` Maven céljával telepítse a ToDo alkalmazást a linuxon Azure app Service.

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

A kimenet tartalmazza a telepített alkalmazás URL-címét (ebben a példában `https://spring-todo-app.azurewebsites.net` ). Másolja az URL-címet a webböngészőjébe, vagy futtassa a következő parancsot a terminál ablakban az alkalmazás betöltéséhez.

```bash
curl https://spring-todo-app.azurewebsites.net
```

A címsorban a távoli URL-címmel futó alkalmazást kell látnia:

 ![Távoli URL-címmel futó Spring boot-alkalmazás](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Diagnosztikai naplók streamelése

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>A TODO-alkalmazás felskálázása

Az alkalmazás felskálázása egy másik feldolgozó hozzáadásával:

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha ezekre az erőforrásokra már nincs szüksége más oktatóanyagokhoz (lásd a [következő lépéseket](#next)), az alábbi parancs a Cloud Shellben való futtatásával törölheti azokat: 
  
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Következő lépések

[Azure Java-fejlesztőknek](/java/azure/) 
 [Spring boot](https://spring.io/projects/spring-boot), Cosmos DB, [Azure Cosmos db](../cosmos-db/introduction.md) és [app Service Linux](overview.md) [Spring-adatvédelme](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db).

További információ a Java-alkalmazások App Service Linux rendszeren való futtatásáról a Fejlesztői útmutatóban.

> [!div class="nextstepaction"] 
> [Java a Linuxos App Service-ben – fejlesztői útmutató](configure-language-java.md?pivots=platform-linux)
