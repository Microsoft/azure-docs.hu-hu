---
title: 'Oktatóanyag: Java-Webalkalmazás létrehozása Azure Cosmos DB és az SQL API használatával'
description: 'Oktatóanyag: Ez a Java-webalkalmazás-oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure websites szolgáltatásban tárolt Java-alkalmazás adatait a Azure Cosmos DB és az SQL API használatával.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 930594d2c1af198aba3dae936cd0ce8fcf91c336
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214099"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Oktatóanyag: Java-Webalkalmazás létrehozása Azure Cosmos DB és az SQL API használatával
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Ez a Java-webalkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure App Service Web Appsben tárolt Java-alkalmazás adatait a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatással. Ebben a cikkben a következőkkel ismerkedhet meg:

* Alapszintű JavaServer Pages- (JSP-) alkalmazás létrehozása az Eclipse-ben.
* Az Azure Cosmos DB szolgáltatás használata az [Azure Cosmos DB Java SDK-val](https://github.com/Azure/azure-documentdb-java).

Ez a Java-alkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan hozhat létre egy webalapú feladatkezelő alkalmazást, amellyel feladatokat hozhat létre, kérhet le, valamint „kész” jelöléssel láthatja el azokat, ahogyan azt az alábbi illusztráció is mutatja. A rendszer a teendőlistában szereplő összes feladatot JSON-dokumentumként tárolja az Azure Cosmos DB-ben.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Saját teendőlista Java-alkalmazása":::

> [!TIP]
> Ez az alkalmazásfejlesztési oktatóanyag feltételezi, hogy rendelkezik korábbi tapasztalattal a Java használatát illetően. Ha még nem ismeri a javát vagy az [előfeltételt jelentő eszközöket](#Prerequisites), javasoljuk, hogy töltse le a teljes [Todo] https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) projektet a githubról, és [a cikk végén található utasítások](#GetProject)alapján végezze el a fordítást. A projekt lefordítása után a cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>A jelen Java-alkalmazásokra vonatkozó oktatóanyag előfeltételei

Az alkalmazásfejlesztési oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java fejlesztői készlet (JDK) 7+](/java/azure/jdk/).
* [Eclipse IDE for Java EE Developers.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Engedélyezett Java-futtatókörnyezettel (pl. Tomcat vagy Jetty) rendelkező Azure-webhely.](../app-service/quickstart-java.md)

Ha első alkalommal telepíti ezeket az eszközöket, a coreservlets.com végigvezeti a telepítési folyamat lépésein a gyors üzembe helyezési [útmutatóban: a TomCat7 telepítése és az Eclipse használatával](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) című cikk.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Azure Cosmos DB-fiók létrehozása

Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik fiókkal vagy az oktatóanyagban az Azure Cosmos DB Emulatort használja, továbbléphet a [2. lépés: Új Java JSP-alkalmazás létrehozása](#CreateJSP) című lépésre.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>A Java JSP-alkalmazás létrehozása

JSP-alkalmazás létrehozása:

1. Először egy Java-projekt létrehozásával kezdjük. Indítsa el az Eclipse-t, kattintson a **File** (Fájl), **New** (Új), majd a **Dynamic Web Projekt** (Dinamikus webes projekt) lehetőségre. Ha nem jelenik meg a **dinamikus webes projekt** elérhető projektként való felsorolása, tegye a következőket: kattintson a **fájl** menü **új** elemére, majd a **projekt**..., a **web** elemre, végül a **dinamikus webes projekt** elemre, és kattintson a **tovább** gombra.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java-alkalmazások fejlesztése":::

1. Adja meg a projekt nevét a **Project name** (Projekt neve) mezőben, majd a **Target Runtime** (Tervezett futásidő) legördülő menüben válasszon ki egy értéket (pl. Apache Tomcat v7.0) (nem kötelező), és kattintson a **Finish** (Befejezés) gombra. A tervezett futásidő megadása lehetővé teszi, hogy helyileg, az Eclipse-ben is futtathassa projektjét.

1. Az Eclipse Project Explorer (Projektböngésző) nézetében bontsa ki a projektet. Kattintson a jobb gombbal a **WebContent** (Webes tartalom), majd a **New** (Új) elemre, és végül a **JSP File** (JSP-fájl) elemre.

1. A **New JSP File** (Új JSP-fájl) párbeszédablakban nevezze el a fájlt az alábbi módon: **index.jsp**. A szülőmappa neve maradjon **WebContent**, ahogy azt az alábbi ábra is mutatja, majd kattintson a **Next** (Tovább) lehetőségre.
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Új JSP-fájl létrehozása – Java-alkalmazásokra vonatkozó oktatóanyag":::

1. A **Select JSP Template** (JSP-sablon kiválasztása) párbeszédablakban, a jelen oktatóanyag céljából válassza a **New JSP File (html)** (Új JSP-fájl (html)) lehetőséget, majd kattintson a **Finish** (Befejezés) lehetőségre.

1. Ha a *index.jsp* -fájl az Eclipse-ben nyílik meg, szöveg hozzáadásával jelenítheti meg a **Hello World!** a már meglévő `<body>` elemhez. A frissített `<body>` tartalomnak az alábbi kódhoz kell hasonlítania:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Mentse a *index.jsp* fájlt.

1. Ha megadta a tervezett futásidőt a 2. lépésben, most rákattinthat a **Project** (Projekt), majd a **Run** (Futtatás) elemre a JSP-alkalmazás helyileg történő futtatásához.

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World – Java-alkalmazásokra vonatkozó oktatóanyag":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Az SQL Java SDK telepítése

Az SQL Java SDK, valamint annak függőségei a legegyszerűbben az [Apache Maven](https://maven.apache.org/) használatával kérhetők le. Ehhez a következő lépések végrehajtásával át kell alakítania a projektet egy Maven-projektre:

1. Kattintson a jobb gombbal a projektre a Project Explorer (Projektböngésző) nézetben, kattintson a **Configure** (Konfigurálás), majd a **Convert to Maven Project** (Konvertálás Maven-projekté) lehetőségre.

1. Az **új Pom létrehozása** ablakban fogadja el az alapértelmezett értékeket, majd kattintson a **Befejezés** gombra.

1. A **Project Explorer** (Projektböngésző) nézetben nyissa meg a pom.xml fájlt.

1. A **Dependencies** (Függőségek) lap **Dependencies** (Függőségek) paneljén kattintson az **Add** (Hozzáadás) lehetőségre.

1. A **Select Dependency** (Függőség kiválasztása) ablakban tegye a következőket:
   
   * A **csoport azonosítója** mezőbe írja be a értéket `com.azure` .
   * Az összetevő- **azonosító** mezőben adja meg a értéket `azure-cosmos` .
   * A **verzió** mezőbe írja be a értéket `4.11.0` .
  
   Vagy hozzáadhatja a függőségi XML-t a csoport AZONOSÍTÓhoz és az összetevő-AZONOSÍTÓhoz közvetlenül a *pom.xml* fájlhoz:

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. Kattintson az **OK** gombra, és a Maven telepíti az SQL Java SDK-t, vagy mentse a pom.xml fájlt.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>A Azure Cosmos DB szolgáltatás használata Java-alkalmazásokban

Most adjuk hozzá a modelleket, a nézeteket és a vezérlőket a webalkalmazáshoz.

### <a name="add-a-model"></a>Modell hozzáadása

Először is Definiáljon egy modellt egy új, *TodoItem. Java* fájlon belül. Az `TodoItem` osztály meghatározza egy adott tétel sémáját a beolvasó és a beállító metódusokkal együtt:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Az adatelérési objektum (DAO) osztályainak hozzáadása

Hozzon létre egy adatelérési objektumot (DAO) a teendők elAzure Cosmos DBához. Ha menteni szeretné a teendőket egy gyűjteménybe, az ügyfélnek tudnia kell, melyik adatbázisban és gyűjteményben kívánja azt megőrizni (ahogy erre az önhivatkozások is hivatkoznak). Általában érdemes gyorsítótárazni az adatbázist és a gyűjteményt, amennyiben ez lehetséges, így elkerülhető az adatbázissal való fölösleges üzenetváltás.

1. A Azure Cosmos DB szolgáltatás meghívásához új objektumot kell létrehoznia `cosmosClient` . Általánosságban elmondható, hogy az objektumot újra fel kell használni ahelyett, hogy `cosmosClient` minden további kérelemhez új ügyfelet kellene létrehoznia. Az ügyfelet a osztályon belüli definiálásával is felhasználhatja `cosmosClientFactory` . Frissítse a GAZDAGÉPet, és MASTER_KEY az 1. [lépésben](#CreateDB)mentett értékeket. Cserélje le a GAZDAGÉP változót az URI-val, és cserélje le a MASTER_KEYt az elsődleges KULCSra. A következő kód használatával hozza létre az `CosmosClientFactory` osztályt a *CosmosClientFactory. Java* fájlban:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Hozzon létre egy új *TodoDao. Java* fájlt, és adja hozzá az `TodoDao` osztályt a teendők létrehozásához, frissítéséhez, olvasásához és törléséhez:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Hozzon létre egy új *MockDao. Java* fájlt, és adja hozzá az `MockDao` osztályt, ez az osztály implementálja az `TodoDao` osztályt a szifiliszi műveletek végrehajtásához az elemeken:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Hozzon létre egy új *DocDbDao. Java* fájlt, és adja hozzá az `DocDbDao` osztályt. Ez az osztály a TodoItems a tárolóba való megőrzéséhez, az adatbázis és a gyűjtemény lekéréséhez, vagy egy új, a nem létező hely létrehozásához szükséges kódot határozza meg. Ez a példa a [Gson](https://code.google.com/p/google-gson/) használatával szerializálja és deszerializálja a TodoItem egyszerű régi Java-objektumokat (szerializálói) a JSON-dokumentumokra. Ha menteni szeretné a teendőket egy gyűjteménybe, az ügyfélnek tudnia kell, melyik adatbázisban és gyűjteményben kívánja azt megőrizni (ahogy erre az önhivatkozások is hivatkoznak). Ez az osztály azt is meghatározza, hogy a segítő függvény hogyan kérheti le a dokumentumokat egy másik attribútum (pl. "ID") helyett. A Helper metódus használatával TodoItem JSON-dokumentumot kérhet le azonosító alapján, majd deszerializálhatja egy POJO.

   Az TodoItems egy SQL- `cosmosClient` lekérdezés használatával is beolvashatja a gyűjteményt vagy a listát. Végezetül a DELETE metódust is megadhatja egy TodoItem törléséhez a listából. A következő kód a osztály tartalmát jeleníti meg `DocDbDao` :

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Ezután hozzon létre egy új *TodoDaoFactory. Java* fájlt, és adja hozzá `TodoDaoFactory` azt az osztályt, amely létrehoz egy új DocDbDao objektumot:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Vezérlő hozzáadása

Adja hozzá az *TodoItemController* vezérlőt az alkalmazáshoz. Ebben a projektben a [Project Lombok](https://projectlombok.org/) nevű projekt használatával hozzuk létre a konstruktort, a beolvasókat, a beállítókat és a felépítőt. Ezt a kódot manuálisan is megírhatja, vagy létrehozhatja az IDE-t is.:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Servlet létrehozása

Ezután hozzon létre egy servletet a HTTP-kérések továbbításához a vezérlőhöz. Hozza létre a *ApiServlet. Java* fájlt, és adja meg a következő kódot:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>A Java-alkalmazás többi részének egyesítése

Most, hogy elvégezte a mókás biteket, csak egy gyors felhasználói felületet kell létrehoznia, és csatlakoztatnia kell a DAO-hez.

1. Szüksége lesz egy webes felhasználói felületre a felhasználó számára való megjelenítéshez. Írja át újra a korábban létrehozott *index.jsp* -t a következő kóddal:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Végezetül írjon néhány ügyféloldali JavaScriptet a webes felhasználói felület és a servlet összekapcsolásához:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Most már csak le kell tesztelni az alkalmazást. Futtassa az alkalmazást helyileg, és adjon hozzá néhány teendőt. Ehhez adja meg az elemek nevét és kategóriáját, majd kattintson az **Add Task** (Feladat hozzáadása) elemre. Az elem megjelenése után a jelölőnégyzet bejelölésével vagy a **feladatok frissítése** lehetőségre kattintva frissítheti, hogy befejeződött-e.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Java-alkalmazás üzembe helyezése az Azure webhelyek szolgáltatásban

Az Azure Websites megkönnyíti a Java-alkalmazások telepítését. Nincs más dolga, mint exportálni az alkalmazást WAR-fájlként, majd feltölteni azt egy forráskezelő rendszer (pl. Git) vagy FTP segítségével.

1. Az alkalmazás WAR-fájlként történő exportálásához kattintson a jobb gombbal a projektre a **Project Explorer** (Projektböngésző) nézetben, majd kattintson az **Export** (Exportálás), és végül a **WAR File** (WAR-fájl) lehetőségre.

1. A **WAR Export** (WAR-fájl exportálása) ablakban tegye a következőket:
   
   * A webes projekt mezőben adja meg az Azure-Cosmos-Java-Sample értéket.
   * A Destination (Cél) mezőben válassza ki, hova szeretné menteni a WAR-fájlt.
   * Kattintson a **Finish** (Befejezés) gombra.

1. Most, hogy megvan a WAR-fájl, egyszerűen töltse fel az Azure Websites **webapps** könyvtárába. A fájl feltöltésével kapcsolatos további információkért lásd a [Java-alkalmazások az Azure App Service Web Appshoz való hozzáadását](../app-service/quickstart-java.md) ismertető cikket. Miután feltöltötte a WAR-fájlt a webapps könyvtárba, a futásidejű környezet felismeri azt, és automatikusan betöltődik.

1. A kész termék megtekintéséhez lépjen a `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` webhelyre, és kezdje meg a feladatok hozzáadását.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>A projekt beszerzése a GitHubról

A jelen oktatóanyag minden példáját megtalálhatja a GitHubról elérhető [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) (teendők) projektben. A teendők projekt Eclipse-be történő importálásához győződjön meg arról, hogy rendelkezik az [Előfeltételek](#Prerequisites) szakaszban ismertetett szoftverekkel és erőforrásokkal, majd tegye a következőket:

1. Telepítse a [Project Lombok](https://projectlombok.org/) nevű projektet. A projekt konstruktorainak, beolvasóinak, beállítóinak létrehozása a Lombok használatával történik. A lombok.jar fájl letöltése után kattintson rá duplán annak telepítéséhez, vagy telepítse azt a parancssorból.

1. Ha az Eclipse meg van nyitva, zárja be, és indítsa el újra a Lombok betöltéséhez.

1. Az Eclipse **File** (Fájl) menüjében kattintson az **Import** (Importálás) elemre.

1. Az **Import** (Importálás) ablakban kattintson a **Git**, majd a **Projects from Git** (Git-projektek), és végül a **Next** (Tovább) lehetőségre.

1. A **Select Repository Source** (Tárház forrásának kiválasztása) képernyőn kattintson a **Clone URI** (URI klónozása) lehetőségre.

1. A **Source Git Repository** (Forrás Git-adattár) képernyő **URI** mezőjében adja meg a https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app címet, majd kattintson a **Next** (Tovább) gombra.

1. A **ág kiválasztása** képernyőn győződjön meg arról, hogy a **Main** van kiválasztva, majd kattintson a **tovább** gombra.

1. A **Local Destination** (Helyi cél) képernyőn kattintson a **Browse** (Tallózás) lehetőségre, válassza ki a mappát, ahova a tárházat másolni szeretné, majd kattintson a **Next** (Tovább) gombra.

1. A **Select a wizard to use for importing projects** (Varázsló kiválasztása a projektek importálásához) képernyőn győződjön meg arról, hogy az **Import existing projects** (Létező projektek importálása) lehetőség van kiválasztva, majd kattintson a **Next** (Tovább) gombra.

1. Az **Import Projects** (Projektek importálása) képernyőn törölje a **DocumentDB** projekt jelölését, majd kattintson a **Finish** (Befejezés) gombra. A DocumentDB-projekt tartalmazza az Azure Cosmos DB Java SDK-t, amelyet inkább függőségként adunk hozzá.

1. A **Project Explorerben** navigáljon a Azure-Cosmos-Java-sample\src\com.microsoft.Azure.Cosmos.sample.dao\DocumentClientFactory.Java elemre, és cserélje le a gazdagépet, és MASTER_KEY értékeket a Azure Cosmos db-fiók URI-ja és elsődleges kulcsával, majd mentse a fájlt. További információ: [1. lépés. Hozzon létre egy Azure Cosmos-adatbázis-fiókot](#CreateDB).

1. A **Project Explorerben** kattintson a jobb gombbal az **Azure-Cosmos-Java-Sample** elemre, kattintson a **Build útvonala** lehetőségre, majd a **Build elérési útjának konfigurálása** elemre.

1. A **Java Build Path** (Java-verzió elérési útja) képernyő jobb oldalsó paneljén válassza ki a **Libraries** (Könyvtárak) lapot, majd kattintson az **Add External JARs** (Külső JAR-fájlok hozzáadása) lehetőségre. Navigáljon a lombok.jar fájl helyére, kattintson az **Open** (Megnyitás), majd az **OK** gombra.

1. A 12. lépésben leírtak segítségével nyissa meg ismét a **Properties** (Tulajdonságok) ablakot, majd a bal oldali ablaktáblán kattintson a **Targeted Runtimes** (Tervezett futásidők) elemre.

1. A **Targeted Runtimes** (Tervezett futásidők) képernyőn kattintson a **New** (Új) elemre, válassza ki az **Apache Tomcat v7.0** lehetőséget, majd kattintson az **OK** gombra.

1. A 12. lépésben leírtak segítségével nyissa meg ismét a **Properties** (Tulajdonságok) ablakot, majd a bal oldali ablaktáblán kattintson a **Project Facets** (A projekt aspektusai) elemre.

1. A **Project Facets** (A projekt aspektusai) képernyőn válassza a **Dynamic Web Module** (Dinamikus webmodul), majd a **Java** lehetőséget, és kattintson az **OK** gombra.

1. A **Servers** (Kiszolgálók) lapon, a képernyő alján, kattintson a jobb gombbal a **Tomcat v7.0 Server at localhost** (Tomcat 7.0-s verziójú kiszolgáló itt: localhost), majd az **Add and Remove** (Hozzáadás és eltávolítás) lehetőségre.

1. Az **Add and Remove (Hozzáadás és eltávolítás** ) ablakban helyezze át az **Azure-Cosmos-Java-Sample** parancsot a **konfigurált** mezőbe, majd kattintson a **Befejezés** gombra.

1. A **Servers** (Kiszolgálók) lapon kattintson a jobb gombbal a **Tomcat v7.0 Server at localhost** (Tomcat 7.0-s verziójú kiszolgáló itt: localhost) elemre, majd kattintson a **Restart** (Újraindítás) lehetőségre.

1. Egy böngészőből lépjen a `http://localhost:8080/azure-cosmos-java-sample/` címre, és kezdje el hozzáadni a feladatait a listához. Ügyeljen arra, hogy ha módosította a portok alapértelmezett értékét, akkor a 8080 értéket módosítsa a választott értékre.

1. A projekt Azure-webhelyre történő üzembe helyezéséhez lásd: [6. lépés. Az alkalmazás üzembe helyezése az Azure webhelyek szolgáltatásban](#Deploy).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hozzon létre egy node.js alkalmazást Azure Cosmos DB](sql-api-nodejs-application.md)