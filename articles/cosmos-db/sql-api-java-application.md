---
title: Oktatóanyag Java alapú alkalmazásfejlesztéshez Azure Cosmos DB használatával
description: Ez a Java-webalkalmazásokkal kapcsolatos oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure Websitesban tárolt Java-alkalmazás adatait az Azure Cosmos DB és az SQL API segítségével.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/22/2018
ms.author: ramkris
ms.openlocfilehash: bd2894c23e206ed5f49fec8aa169d6ed852df4c6
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616655"
---
# <a name="build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Java-webalkalmazás létrehozása az Azure Cosmos DB és az SQL API használatával

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Ez a Java-webalkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan tárolhatja és érheti el az Azure App Service Web Appsben tárolt Java-alkalmazás adatait a [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatással. Ebben a cikkben a következőkkel ismerkedhet meg:

* Alapszintű JavaServer Pages- (JSP-) alkalmazás létrehozása az Eclipse-ben.
* Az Azure Cosmos DB szolgáltatás használata az [Azure Cosmos DB Java SDK-val](https://github.com/Azure/azure-documentdb-java).

Ez a Java-alkalmazásokra vonatkozó oktatóanyag bemutatja, hogyan hozhat létre egy webalapú feladatkezelő alkalmazást, amellyel feladatokat hozhat létre, kérhet le, valamint „kész” jelöléssel láthatja el azokat, ahogyan azt az alábbi illusztráció is mutatja. A rendszer a teendőlistában szereplő összes feladatot JSON-dokumentumként tárolja az Azure Cosmos DB-ben.

![Saját teendőlista Java-alkalmazása](./media/sql-api-java-application/image1.png)

> [!TIP]
> Ez az alkalmazásfejlesztési oktatóanyag feltételezi, hogy rendelkezik korábbi tapasztalattal a Java használatát illetően. Ha még nem ismeri a Javát vagy az [előfeltételt jelentő eszközöket](#Prerequisites), ajánlott letölteni a teljes [teendők](https://github.com/Azure-Samples/documentdb-java-todo-app) projektet a GitHubról, majd lefordítani azt a [jelen cikk végén található utasítások](#GetProject) segítségével. A projekt lefordítása után a cikk áttekintésével betekintést nyerhet a kódba a projekt környezetében.  
> 
> 

## <a id="Prerequisites"></a>A jelen Java-webalkalmazásokra vonatkozó oktatóanyag előfeltételei
Az alkalmazásfejlesztési oktatóanyag elkezdéséhez az alábbiakkal kell rendelkeznie:

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java fejlesztői készlet (JDK) 7+](https://aka.ms/azure-jdks).
* [Eclipse IDE for Java EE Developers.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Engedélyezett Java-futtatókörnyezettel (pl. Tomcat vagy Jetty) rendelkező Azure-webhely.](../app-service/app-service-web-get-started-java.md)

Ha első alkalommal telepíti ezeket az eszközöket, a coreservlets.com a telepítési folyamat áttekintését nyújtja az [oktatóanyag gyorskonfigurálás szakaszában: A TomCat7 telepítése és használata Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) -cikkel.

## <a id="CreateDB"></a>1. lépés: Azure Cosmos DB-fiók létrehozása
Először hozzon létre egy Azure Cosmos DB-fiókot. Ha már rendelkezik fiókkal, vagy ha a Azure Cosmos db emulátort használja ehhez az oktatóanyaghoz, ugorjon a [2. lépésre: Hozzon létre egy Java](#CreateJSP)JSP-alkalmazást.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>2. lépés: A Java JSP-alkalmazás létrehozása
JSP-alkalmazás létrehozása:

1. Először is hozzon létre egy Java-projektet. Indítsa el az Eclipse-t, kattintson a **File** (Fájl), **New** (Új), majd a **Dynamic Web Projekt** (Dinamikus webes projekt) lehetőségre. Ha nem jelenik meg a **Dynamic Web Projekt** (Dinamikus webes projekt) lehetőség az elérhető projektek között, tegye a következőt: kattintson a **File** (Fájl), **New** (Új), **Project** (Projekt) lehetőségre, bontsa ki a **Web** elemet, majd kattintson a **Dynamic Web Projekt** (Dinamikus webes projekt) elemre, és végül a **Tovább** gombra.
   
    ![JSP Java-alkalmazások fejlesztése](./media/sql-api-java-application/image10.png)
2. Adja meg a projekt nevét a **Project name** (Projekt neve) mezőben, majd a **Target Runtime** (Tervezett futásidő) legördülő menüben válasszon ki egy értéket (pl. Apache Tomcat v7.0) (nem kötelező), és kattintson a **Finish** (Befejezés) gombra. A tervezett futásidő megadása lehetővé teszi, hogy helyileg, az Eclipse-ben is futtathassa projektjét.
3. Az Eclipse Project Explorer (Projektböngésző) nézetében bontsa ki a projektet. Kattintson a jobb gombbal a **WebContent** (Webes tartalom), majd a **New** (Új) elemre, és végül a **JSP File** (JSP-fájl) elemre.
4. A **New JSP File** (Új JSP-fájl) párbeszédablakban nevezze el a fájlt az alábbi módon: **index.jsp**. A szülőmappa neve maradjon **WebContent**, ahogy azt az alábbi ábra is mutatja, majd kattintson a **Next** (Tovább) lehetőségre.
   
    ![Új JSP-fájl létrehozása – Java-alkalmazásokra vonatkozó oktatóanyag](./media/sql-api-java-application/image11.png)
5. A **Select JSP Template** (JSP-sablon kiválasztása) párbeszédablakban, a jelen oktatóanyag céljából válassza a **New JSP File (html)** (Új JSP-fájl (html)) lehetőséget, majd kattintson a **Finish** (Befejezés) lehetőségre.
6. Miután megnyílt az index.jsp fájl az Eclipse-ben, adja hozzá a **Hello World!** szöveget a már meglévő `<body>` elemhez. A frissített `<body>` tartalomnak az alábbi kódhoz kell hasonlítania:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Mentse az index.jsp fájlt.
8. Ha megadta a tervezett futásidőt a 2. lépésben, most rákattinthat a **Project** (Projekt), majd a **Run** (Futtatás) elemre a JSP-alkalmazás helyileg történő futtatásához.
   
    ![Hello World – Java-alkalmazásokra vonatkozó oktatóanyag](./media/sql-api-java-application/image12.png)

## <a id="InstallSDK"></a>3. lépés: Az SQL Java SDK telepítése
Az SQL Java SDK, valamint annak függőségei a legegyszerűbben az [Apache Maven](https://maven.apache.org/) használatával kérhetők le.

Ehhez át kell konvertálnia a projektet Maven-projektté az alábbi lépések végrehajtásával:

1. Kattintson a jobb gombbal a projektre a Project Explorer (Projektböngésző) nézetben, kattintson a **Configure** (Konfigurálás), majd a **Convert to Maven Project** (Konvertálás Maven-projekté) lehetőségre.
2. A **Create new POM** (Új POM létrehozása) ablakban fogadja el az alapértelmezett beállításokat, majd kattintson a **Finish** (Befejezés) lehetőségre.
3. A **Project Explorer** (Projektböngésző) nézetben nyissa meg a pom.xml fájlt.
4. A **Dependencies** (Függőségek) lap **Dependencies** (Függőségek) paneljén kattintson az **Add** (Hozzáadás) lehetőségre.
5. A **Select Dependency** (Függőség kiválasztása) ablakban tegye a következőket:
   
   * A **Group Id** (Csoportazonosító) mezőben adja meg a következőt: com.microsoft.azure.
   * Az **Artifact Id** (Összetevő-azonosító) mezőben adja meg a következőt: azure-documentdb.
   * A **Version** (Verzió) mezőben adja meg a következőt: 1.5.1.
     
   ![Az SQL Java Application SDK telepítése](./media/sql-api-java-application/image13.png)
     
   * Vagy adja hozzá a függőség XML-fájljának Group Id (Csoportazonosító) és Artifact Id (Összetevő-azonosító) szakaszát közvetlenül a pom.xml fájlhoz egy szövegszerkesztő segítségével:
        ```xml
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-documentdb</artifactId>
            <version>1.9.1</version>
        </dependency>
        ```
6. Az **OK** gombra való kattintás után a Maven telepíti a SQL Java SDK-t.
7. Mentse a pom.xml fájlt.

## <a id="UseService"></a>4. lépés: A Azure Cosmos DB szolgáltatás használata Java-alkalmazásokban
1. Először is határozza meg a TodoItem (Tennivaló) objektumot a TodoItem.java fájlban:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    Ebben a projektben a [Project Lombok](https://projectlombok.org/) nevű projekt használatával hozzuk létre a konstruktort, a beolvasókat, a beállítókat és a felépítőt. Ezt a kódot kézzel is megírhatja, vagy létrehozhatja azt az IDE használatával.
2. Az Azure Cosmos DB szolgáltatás elindításához példányosítania kell egy új **DocumentClient**-ügyfelet. Általában érdemes újrafelhasználni a **DocumentClient** ügyfelet ahelyett, hogy minden későbbi kérés esetén új ügyfelet hozna létre. Az ügyfél újrafelhasználásához burkolja be azt egy **DocumentClientFactory** használatával. Az [1. lépésben](#CreateDB) a vágólapra mentett URI és PRIMARY KEY (ELSŐDLEGES KULCS) értékeket be kell illeszteni a DocumentClientFactory.java fájlba. Cserélje ki a [YOUR\_ENDPOINT\_HERE] részt az URI-re, a [YOUR\_KEY\_HERE] részt pedig az elsődleges kulcsra.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Ezután hozzon létre egy Data Access Objectet (DAO-t) a teendők az Azure Cosmos DB szolgáltatásba történő kivonatolásához.
   
    Ha menteni szeretné a teendőket egy gyűjteménybe, az ügyfélnek tudnia kell, melyik adatbázisban és gyűjteményben kívánja azt megőrizni (ahogy erre az önhivatkozások is hivatkoznak). Általában érdemes gyorsítótárazni az adatbázist és a gyűjteményt, amennyiben ez lehetséges, így elkerülhető az adatbázissal való fölösleges üzenetváltás.
   
    Az alábbi kód bemutatja, hogyan kérheti le az adatbázist és a gyűjteményt, ha azok léteznek, és hogyan hozhat létre újat, ha azok még nem léteznek:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. A következő lépésben be kell írnia egy kódrészletet a TodoItem elemek a gyűjteményben való megőrzéséhez. A jelen példában a [Gson](https://code.google.com/p/google-gson/) segítségével szerializáltuk és deszerializáltuk a teendő POJO objektumait JSON-dokumentumokká.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Az Azure Cosmos-adatbázisokhoz és-gyűjteményekhez hasonlóan a dokumentumokat is a saját hivatkozások is hivatkoznak. A következő segédfüggvény lehetővé teszi a dokumentumok az önhivatkozástól eltérő attribútum (pl. „id”) alapján történő lekérését:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Az 5. lépésben leírt segédmetódus segítségével lekérheti egy teendő JSON-dokumentumát annak azonosítója alapján, majd deszerializálhatja azt egy POJO-vá.
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. A DocumentClient ügyfél segítségével szintén lekérheti a TodoItem elemek gyűjteményét vagy listáját az SQL használatával:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. A DocumentClient ügyfél segítségével számos módon frissítheti a dokumentumokat. A teendőlista alkalmazásában azt szeretnénk, ha beállíthatnánk, hogy elvégeztük-e az egyes teendőket. Ez a dokumentum „complete” (befejezve) attribútumának frissítésével érhető el:
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Végül azt szeretnénk, ha törölhetnénk az egyes teendőket a listából. Ehhez a korábban megírt segédmetódus használatával kérje le az önhivatkozást, majd adja ki a törlés parancsot az ügyfélnek:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>5. lépés: A Java alkalmazás-fejlesztési projekt többi részének összekötése együtt
Most, hogy elért a könnyebb részek végére, már csak fel kell építenie egy gyors felhasználói felületet, és hozzá kell fűznie azt a DAO-objektumhoz.

1. Először is hozzon létre egy vezérlőt a DAO meghívásához:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    Egy összetettebb alkalmazásban a vezérlő a DAO mellett bonyolult üzleti logikát is tartalmazhat.
2. Ezután hozzon létre egy servletet, amely továbbítja a HTTP-kéréseket a vezérlőhöz:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Szükség lesz egy webes felhasználói felületre, amelyet megjeleníthet a felhasználó számára. Írja át a korábban létrehozott index.jsp fájlt az alábbi módon:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. Majd végezetül írjon egy ügyféloldali JavaScript-kódot, amely megteremti a kapcsolatot a webes felhasználói felület és a servlet között:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Nagyszerű! Most már csak le kell tesztelni az alkalmazást. Futtassa az alkalmazást helyileg, és adjon hozzá néhány teendőt. Ehhez adja meg az elemek nevét és kategóriáját, majd kattintson az **Add Task** (Feladat hozzáadása) elemre.
6. Ha megjelent az elem, a jelölőnégyzet bejelölésével, majd az **Update Tasks** (Feladatok frissítése) gombra való kattintással állíthatja be, hogy elvégezte-e már azt.

## <a id="Deploy"></a>6. lépés: Java-alkalmazás üzembe helyezése az Azure webhelyek szolgáltatásban
Az Azure Websites megkönnyíti a Java-alkalmazások telepítését. Nincs más dolga, mint exportálni az alkalmazást WAR-fájlként, majd feltölteni azt egy forráskezelő rendszer (pl. Git) vagy FTP segítségével.

1. Az alkalmazás WAR-fájlként történő exportálásához kattintson a jobb gombbal a projektre a **Project Explorer** (Projektböngésző) nézetben, majd kattintson az **Export** (Exportálás), és végül a **WAR File** (WAR-fájl) lehetőségre.
2. A **WAR Export** (WAR-fájl exportálása) ablakban tegye a következőket:
   
   * A Web project (Webes projekt) mezőben adja meg a következőt: azure-documentdb-java-sample.
   * A Destination (Cél) mezőben válassza ki, hova szeretné menteni a WAR-fájlt.
   * Kattintson a **Befejezés** gombra.
3. Most, hogy megvan a WAR-fájl, egyszerűen töltse fel az Azure Websites **webapps** könyvtárába. A fájl feltöltésével kapcsolatos további információkért lásd a [Java-alkalmazások az Azure App Service Web Appshoz való hozzáadását](../app-service/web-sites-java-add-app.md) ismertető cikket.
   
    Miután feltöltötte a WAR-fájlt a webapps könyvtárba, a futtatókörnyezet észleli majd annak hozzáadását, és automatikusan betölti azt.
4. A kész termék megtekintéséhez lépjen a `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` webhelyre, és kezdje meg a feladatok hozzáadását.

## <a id="GetProject"></a>A projekt beszerzése a GitHubról
A jelen oktatóanyag minden példáját megtalálhatja a GitHubról elérhető [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) (teendők) projektben. A teendők projekt Eclipse-be történő importálásához győződjön meg arról, hogy rendelkezik az [Előfeltételek](#Prerequisites) szakaszban ismertetett szoftverekkel és erőforrásokkal, majd tegye a következőket:

1. Telepítse a [Project Lombok](https://projectlombok.org/) nevű projektet. A projekt konstruktorainak, beolvasóinak, beállítóinak létrehozása a Lombok használatával történik. A lombok.jar fájl letöltése után kattintson rá duplán annak telepítéséhez, vagy telepítse azt a parancssorból.
2. Ha az Eclipse meg van nyitva, zárja be, és indítsa el újra a Lombok betöltéséhez.
3. Az Eclipse **File** (Fájl) menüjében kattintson az **Import** (Importálás) elemre.
4. Az **Import** (Importálás) ablakban kattintson a **Git**, majd a **Projects from Git** (Git-projektek), és végül a **Next** (Tovább) lehetőségre.
5. A **Select Repository Source** (Tárház forrásának kiválasztása) képernyőn kattintson a **Clone URI** (URI klónozása) lehetőségre.
6. A **Source Git Repository** (Forrás Git-adattár) képernyő **URI** mezőjében adja meg a https://github.com/Azure-Samples/documentdb-java-todo-app.git címet, majd kattintson a **Next** (Tovább) gombra.
7. A **Branch Selection** (Ág kiválasztása) képernyőn válassza a **master** (fő) lehetőséget, majd kattintson a **Next** (Tovább) gombra.
8. A **Local Destination** (Helyi cél) képernyőn kattintson a **Browse** (Tallózás) lehetőségre, válassza ki a mappát, ahova a tárházat másolni szeretné, majd kattintson a **Next** (Tovább) gombra.
9. A **Select a wizard to use for importing projects** (Varázsló kiválasztása a projektek importálásához) képernyőn győződjön meg arról, hogy az **Import existing projects** (Létező projektek importálása) lehetőség van kiválasztva, majd kattintson a **Next** (Tovább) gombra.
10. Az **Import Projects** (Projektek importálása) képernyőn törölje a **DocumentDB** projekt jelölését, majd kattintson a **Finish** (Befejezés) gombra. A DocumentDB-projekt tartalmazza az Azure Cosmos DB Java SDK-t, amelyet inkább függőségként adunk hozzá.
11. A **Project Explorer** (Projektböngésző) nézetben lépjen a következő helyre: azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java. Itt cserélje le a HOST és a MASTER_KEY értékét az Azure Cosmos DB-fiókjának URI és PRIMARY KEY értékeire, majd mentse a fájlt. További információ: [1. lépés Hozzon létre egy Azure Cosmos](#CreateDB)-adatbázis-fiókot.
12. A **Project Explorer** (Projektböngésző) nézetben kattintson a jobb gombbal az **azure-documentdb-java-sample** elemre, majd kattintson a **Build Path** (Verzió elérési útja), és végül a **Configure Build Path** (Verzió elérési útjának konfigurálása) lehetőségre.
13. A **Java Build Path** (Java-verzió elérési útja) képernyő jobb oldalsó paneljén válassza ki a **Libraries** (Könyvtárak) lapot, majd kattintson az **Add External JARs** (Külső JAR-fájlok hozzáadása) lehetőségre. Navigáljon a lombok.jar fájl helyére, kattintson az **Open** (Megnyitás), majd az **OK** gombra.
14. A 12. lépésben leírtak segítségével nyissa meg ismét a **Properties** (Tulajdonságok) ablakot, majd a bal oldali ablaktáblán kattintson a **Targeted Runtimes** (Tervezett futásidők) elemre.
15. A **Targeted Runtimes** (Tervezett futásidők) képernyőn kattintson a **New** (Új) elemre, válassza ki az **Apache Tomcat v7.0** lehetőséget, majd kattintson az **OK** gombra.
16. A 12. lépésben leírtak segítségével nyissa meg ismét a **Properties** (Tulajdonságok) ablakot, majd a bal oldali ablaktáblán kattintson a **Project Facets** (A projekt aspektusai) elemre.
17. A **Project Facets** (A projekt aspektusai) képernyőn válassza a **Dynamic Web Module** (Dinamikus webmodul), majd a **Java** lehetőséget, és kattintson az **OK** gombra.
18. A **Servers** (Kiszolgálók) lapon, a képernyő alján, kattintson a jobb gombbal a **Tomcat v7.0 Server at localhost** (Tomcat 7.0-s verziójú kiszolgáló itt: localhost), majd az **Add and Remove** (Hozzáadás és eltávolítás) lehetőségre.
19. Az **Add and Remove** (Hozzáadás és eltávolítás) ablakban helyezze át az **azure-documentdb-java-sample** kiszolgálót a **Configured** (Konfigurált) mezőbe, majd kattintson a **Finish** (Befejezés) gombra.
20. A **Servers** (Kiszolgálók) lapon kattintson a jobb gombbal a **Tomcat v7.0 Server at localhost** (Tomcat 7.0-s verziójú kiszolgáló itt: localhost) elemre, majd kattintson a **Restart** (Újraindítás) lehetőségre.
21. Egy böngészőből lépjen a `http://localhost:8080/azure-documentdb-java-sample/` címre, és kezdje el hozzáadni a feladatait a listához. Ügyeljen arra, hogy ha módosította a portok alapértelmezett értékét, akkor a 8080 értéket módosítsa a választott értékre.
22. Projekt telepítése egy Azure-webhelyre: [6. lépés Telepítse az alkalmazást az Azure Websitesra](#Deploy).

