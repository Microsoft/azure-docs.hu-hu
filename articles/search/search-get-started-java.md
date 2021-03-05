---
title: 'Gyors útmutató: keresési index létrehozása a javas szolgáltatásban'
titleSuffix: Azure Cognitive Search
description: Ebből a Java-útmutatóból megtudhatja, hogyan hozhat létre indexet, tölthet be és futtathat lekérdezéseket a Javához készült Azure Cognitive Search ügyféloldali kódtár használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 03/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: f833c0539d4aa28033cd6f76c1ca7cec7f11f6b5
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124567"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java"></a>Rövid útmutató: Azure Cognitive Search index létrehozása javában
> [!div class="op_single_selector"]
> * [Java](search-get-started-java.md)
> * [JavaScript](search-get-started-javascript.md)
> * [C#](search-get-started-dotnet.md)
> * [Portál](search-get-started-portal.md)
> * [PowerShell](search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [REST](search-get-started-rest.md)

Hozzon létre egy Java-konzolos alkalmazást, amely a [IntelliJ](https://www.jetbrains.com/idea/), a [Java 11 SDK](/java/azure/jdk/)és az [Azure Cognitive Search REST API](/rest/api/searchservice/)használatával hoz létre, tölt be és kérdez le egy keresési indexet. Ez a cikk részletes útmutatást nyújt az alkalmazás létrehozásához. Azt is megteheti, hogy [letölti és futtatja a teljes alkalmazást](hhttps://github.com/Azure-Samples/azure-search-java-samples).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató összeállításához és teszteléséhez a következő szoftvereket és szolgáltatásokat használtuk:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Java 11 SDK](/java/azure/jdk/)

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt. Ehhez a rövid útmutatóhoz ingyenes szolgáltatást is használhat.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A szolgáltatás felé irányuló hívások URL-végpontot és hozzáférési kulcsot igényelnek minden kérelemben. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése" border="false":::

A szolgáltatásnak eljuttatott minden kérelemhez API-kulcs szükséges. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="set-up-your-environment"></a>A környezet kialakítása

Első lépésként nyissa meg a IntelliJ IDEA-t, és állítson be egy új projektet.

### <a name="create-the-project"></a>A projekt létrehozása

1. Nyissa meg a IntelliJ ÖTLETET, és válassza az **új projekt létrehozása** lehetőséget.
1. Válassza a **Maven** lehetőséget.
1. A **Project SDK** listában válassza ki a Java 11 SDK-t.

    :::image type="content" source="media/search-get-started-java/java-quickstart-create-new-maven-project.png" alt-text="Maven-projekt létrehozása" border="false":::

1. A **GroupID** és a **ArtifactId** mezőbe írja be a következőt: `AzureSearchQuickstart` .
1. Fogadja el a fennmaradó alapértékeket a projekt megnyitásához.

### <a name="specify-maven-dependencies"></a>Maven-függőségek meghatározása

1. Válassza a **fájl**  >  **beállításai** lehetőséget.
1. A **Beállítások** ablakban válassza a **Létrehozás, végrehajtás, üzembe helyezés**  >  **eszközök**  >  **Maven**-  >  **Importálás** lehetőséget.
1. Jelölje be a  **Maven-projektek automatikus importálása** jelölőnégyzetet, majd kattintson az **OK** gombra az ablak bezárásához. A Maven beépülő modulok és egyéb függőségek mostantól automatikusan szinkronizálva lesznek, amikor a következő lépésben frissíti a pom.xml fájlt.

    :::image type="content" source="media/search-get-started-java/java-quickstart-settings-import-maven-auto.png" alt-text="A Maven importálási lehetőségei a IntelliJ-beállításokban" border="false":::

1. Nyissa meg a pom.xml fájlt, és cserélje le a tartalmát a következő Maven-konfigurációs részletekre. Ezek közé tartoznak az [exec Maven beépülő modulra](https://www.mojohaus.org/exec-maven-plugin/) és egy [JSON Interface API](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2) -ra vonatkozó hivatkozások

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <packaging>jar</packaging>
        <version>1.0-SNAPSHOT</version>
        <properties>
            <jackson.version>2.12.1</jackson.version>
            <auto-value.version>1.6.2</auto-value.version>
            <junit.version>5.4.2</junit.version>
            <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        </properties>
        <name>azuresearch-console</name>
        <url>http://maven.apache.org</url>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core -->
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-databind</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.fasterxml.jackson.datatype</groupId>
                <artifactId>jackson-datatype-jdk8</artifactId>
                <version>${jackson.version}</version>
            </dependency>
            <dependency>
                <groupId>com.google.auto.value</groupId>
                <artifactId>auto-value-annotations</artifactId>
                <version>${auto-value.version}</version>
            </dependency>
            <dependency>
                <groupId>com.google.auto.value</groupId>
                <artifactId>auto-value</artifactId>
                <version>${auto-value.version}</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>com.azure</groupId>
                <artifactId>azure-search-documents</artifactId>
                <version>11.1.3</version>
            </dependency>
        </dependencies>
    
        <build>
            <plugins>
                <!--put generated source files to generated-sources-->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <!-- For JUnit -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>2.22.1</version>
                </plugin>
                <!-- Add exec plugin to run demo program -->
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>com.microsoft.azure.search.samples.demo.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
    </project>
    ```

<!-- STOPPED HERE -- SENT EMAIL TO TONG XU ASKING FOR INFO -->
### <a name="set-up-the-project-structure"></a>A projekt szerkezetének beállítása

1. Válassza a **fájl**  >  **projekt szerkezete** lehetőséget.
1. Válassza ki a **modulokat**, és bontsa ki a forrás fát a mappa tartalmának eléréséhez `src`  >   `main` .
1. A `src`  >   `main`  >  `java` mappában adja hozzá a következő mappákat `com` : `microsoft` `azure` `search` `samples` ,,,,, `demo` . Ehhez válassza ki a `java` mappát, nyomja le az ALT + INSERT billentyűkombinációt, majd adja meg a mappa nevét.
1. A `src`  >   `main`  > `resources` mappában adja hozzá a `app` és a `service` mappákat.

    Ha elkészült, a projekt fájának az alábbi képhez hasonlóan kell kinéznie.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree.png" alt-text="Projekt könyvtárának szerkezete" border="false":::

1. Kattintson az **OK** gombra az ablak bezárásához.

### <a name="add-azure-cognitive-search-service-information"></a>Azure Cognitive Search szolgáltatás adatainak hozzáadása

1. A **projekt** ablakban bontsa ki a forrás fát a mappa eléréséhez `src`  >   `main`  > `resources`  >  `app` , és adjon hozzá egy `config.properties` fájlt. Ehhez válassza ki a `app` mappát, nyomja le az ALT + INSERT billentyűkombinációt, válassza a **fájl** lehetőséget, majd adja meg a fájl nevét.

1. Másolja a következő beállításokat az új fájlba, és cserélje le a, a `<YOUR-SEARCH-SERVICE-NAME>` `<YOUR-ADMIN-KEY>` és a nevet a `<YOUR-QUERY-KEY>` szolgáltatás nevére és kulcsaira. Ha a szolgáltatási végpontja `https://mydemo.search.windows.net` , a szolgáltatás neve a következő lesz: `"mydemo"` .

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2020-06-30
    ```

### <a name="add-the-main-method"></a>A Main metódus hozzáadása

1. A `src`  >   `main`  >  `java`  >  `app` mappában adjon hozzá egy `App` osztályt. Ehhez válassza ki a `app` mappát, nyomja le az ALT + INSERT billentyűkombinációt, válassza a **Java-osztály** lehetőséget, majd adja meg az osztály nevét.
1. Nyissa meg az `App` osztályt, és cserélje le a tartalmat a következő kódra. Ez a kód tartalmazza a `main` metódust. 

    A nem kommentált kód beolvassa a keresési szolgáltatás paramétereit, és a segítségével létrehozza a keresési szolgáltatás ügyfelének egy példányát. A Search szolgáltatás ügyfelének kódját a következő szakaszban adja hozzá a rendszer.

    Az ebben az osztályban található megjegyzési kódot a rövid útmutató egy későbbi szakaszában törli a rendszer.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>HTTP-műveletek hozzáadása

1. A `src`  >   `main`  >  `java`  >  `service` mappában adjon hozzá egy `SearchServiceClient` osztályt. Ehhez válassza ki a `service` mappát, nyomja le az ALT + INSERT billentyűkombinációt, válassza a **Java-osztály** lehetőséget, majd adja meg az osztály nevét.
1. Nyissa meg az `SearchServiceClient` osztályt, és cserélje le a tartalmát a következő kódra. Ez a kód biztosítja az Azure Cognitive Search REST API használatához szükséges HTTP-műveleteket. Az index létrehozásának, a dokumentumok feltöltésének és az index lekérdezésének további módszerei egy későbbi szakaszban lesznek hozzáadva.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>A projekt felépítése

1. Ellenőrizze, hogy a projekt a következő szerkezettel rendelkezik-e.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png" alt-text="Project Directory-struktúra plusz osztályok" border="false":::

1. Nyissa meg a **Maven** eszköz ablakát, és hajtsa végre a következő Maven-célt: `verify exec:java` 
 :::image type="content" source="media/search-get-started-java/java-quickstart-execute-maven-goal.png" alt-text="Maven-cél végrehajtása: az exec ellenőrzése: Java" border="false":::

A feldolgozás befejezésekor keressen egy sikeres BUILD-üzenetet, amelyet egy nulla (0) kilépési kód követ.

## <a name="1---create-index"></a>1 – index létrehozása

A Hotels index definíciója egyszerű mezőket és egy összetett mezőt tartalmaz. Egyszerű mező például a "pezsgő" vagy a "Description". A "címe" mező egy összetett mező, mert almezővel rendelkezik, például "utca címe" és "város". Ebben a rövid útmutatóban az index definíciója a JSON használatával van megadva.

1. A **projekt** ablakban bontsa ki a forrás fát a mappa eléréséhez `src`  >   `main`  > `resources`  >  `service` , és adjon hozzá egy `index.json` fájlt. Ehhez válassza ki a `app` mappát, nyomja le az ALT + INSERT billentyűkombinációt, válassza a **fájl** lehetőséget, majd adja meg a fájl nevét.

1. Nyissa meg a `index.json` fájlt, és szúrja be a következő index-definíciót.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Az index neve "Hotels-Gyorsindítás" lesz. Az index mezők attribútumai határozzák meg, hogy az indexelt adat hogyan kereshető meg egy alkalmazásban. Az `IsSearchable` attribútumot például minden olyan mezőhöz hozzá kell rendelni, amelynek szerepelnie kell egy teljes szöveges keresésben. Az attribútumokkal kapcsolatos további tudnivalókért tekintse meg az [index létrehozása (REST)](/rest/api/searchservice/create-index)című témakört.
    
    Az `Description` indexben lévő mező a választható `analyzer` tulajdonságot használja az alapértelmezett Lucene nyelvi elemző felülbírálásához. A `Description_fr` mező a francia Lucene Analyzert használja, `fr.lucene` mert francia nyelvű szöveget tárol. A a `Description` választható Microsoft Language Analyzer en. Lucene használja. További információ az elemzők használatáról: az [Azure Cognitive Searchban végzett szövegszerkesztés elemzői](search-analyzers.md).

1. Adja hozzá az alábbi kódot a `SearchServiceClient` osztályhoz. Ezek a módszerek létrehozzák az Azure Cognitive Search REST szolgáltatás URL-címét, amelyek indexet hoznak létre és törölnek, és amelyek meghatározzák, hogy létezik-e index. A metódusok a HTTP-kérést is elvégzik.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. A következő kód megjegyzésének visszaírása a `App` osztályban. Ez a kód törli a "Hotels-Gyorsindítás" indexet, ha létezik, és létrehoz egy új indexet a "index.json" fájl index definíciója alapján. 

    Az index-létrehozási kérelem után egy másodperces szünet van beszúrva. Ez a Szüneteltetés biztosítja, hogy az index a dokumentumok feltöltése előtt legyen létrehozva.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Nyissa meg a **Maven** eszköz ablakát, és hajtsa végre a következő Maven-célt: `verify exec:java`

    A kód futtatásakor keresse meg az "index létrehozása" üzenetet, majd egy 201-es hibakódot. Ez a válasz kód megerősíti, hogy az index létrejött. A futtatásnak egy BUILD SIKERESSÉGi üzenettel és egy nulla (0) kilépési kóddal kell végződnie.
    
## <a name="2---load-documents"></a>2 – dokumentumok betöltése

1. A **projekt** ablakban bontsa ki a forrás fát a mappa eléréséhez `src`  >   `main`  > `resources`  >  `service` , és adjon hozzá egy `hotels.json` fájlt. Ehhez válassza ki a `app` mappát, nyomja le az ALT + INSERT billentyűkombinációt, válassza a  **fájl** lehetőséget, majd adja meg a fájl nevét.
1. Szúrja be a következő szállodai dokumentumokat a fájlba.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Szúrja be a következő kódot a `SearchServiceClient` osztályba. Ez a kód létrehozza a REST-szolgáltatás URL-címét, hogy feltöltse a szállodai dokumentumokat az indexbe, majd végrehajtja a HTTP POST kérelmet.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. A következő kód megjegyzésének visszaírása a `App` osztályban. Ez a kód feltölti a dokumentumokat a "hotels.json" indexbe.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    A feltöltési kérelem után a rendszer két másodperces szünetet szúr be, amely biztosítja, hogy a dokumentum betöltési folyamata befejeződjön, mielőtt lekérdezi az indexet.

1. Nyissa meg a **Maven** eszköz ablakát, és hajtsa végre a következő Maven-célt: `verify exec:java`

    Mivel az előző lépésben létrehozta a "Hotels-Gyorsindítás" indexet, a kód törli, majd újra létrehozza újból a szállodai dokumentumok betöltése előtt.

    A kód futtatásakor keresse meg a "dokumentumok feltöltése" üzenetet, majd egy 200-es hibakódot. Ez a válasz kód megerősíti, hogy a dokumentumokat feltöltötte az indexbe. A futtatásnak egy BUILD SIKERESSÉGi üzenettel és egy nulla (0) kilépési kóddal kell végződnie.

## <a name="3---search-an-index"></a>3 – Keresés az indexekben

Most, hogy betöltötte a szállodák dokumentumait, létrehozhat keresési lekérdezéseket a szállások eléréséhez.

1. Adja hozzá az alábbi kódot a `SearchServiceClient` osztályhoz. Ez a kód az Azure Cognitive Search REST-szolgáltatás URL-címeit építi fel az indexelt adatok keresésére és a keresési eredmények kinyomtatására.

    Az `SearchOptions` osztály és a `createSearchOptions` metódus lehetővé teszi az elérhető Azure-Cognitive Search REST API lekérdezési beállítások részhalmazának megadását. További információ a REST API lekérdezési lehetőségekről: [dokumentumok keresése (Azure Cognitive Search REST API)](/rest/api/searchservice/search-documents).

    A `SearchPlus` metódus létrehozza a keresési lekérdezés URL-címét, végrehajtja a keresési kérelmet, majd kinyomtatja az eredményeket a-konzolra. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. A osztályban írja be `App` a következő kódot a megjegyzésbe: Ez a kód öt különböző lekérdezést állít be, beleértve a keresendő szöveget, a lekérdezési paramétereket és az adatmezőket. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    A [kifejezéseket kétféleképpen lehet egyeztetni a lekérdezésekben](search-query-overview.md#types-of-queries): teljes szöveges keresés és szűrők. A teljes szöveges keresési lekérdezés egy vagy több kifejezést keres az `IsSearchable` index mezőiben. A szűrő egy logikai kifejezés, amely egy index mezőin van kiértékelve `IsFilterable` . A teljes szöveges keresést és a szűrőket együtt vagy külön is használhatja.

1. Nyissa meg a **Maven** eszköz ablakát, és hajtsa végre a következő Maven-célt: `verify exec:java`

    Keresse meg az egyes lekérdezések összegzését és eredményeit. A futtatásnak SIKERESnek kell lennie a BUILD SIKERe üzenettel és egy nulla (0) kilépési kóddal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

Ha ingyenes szolgáltatást használ, ne feledje, hogy Ön legfeljebb három indexet, indexelő és adatforrást használhat. A portálon törölheti az egyes elemeket, hogy a korlát alatt maradjon. 

## <a name="next-steps"></a>Következő lépések

Ebben a Java-rövid útmutatóban egy sor feladatot dolgozott ki egy index létrehozásához, a dokumentumok betöltéséhez és a lekérdezések futtatásához. Ha az alapfogalmakat jól ismeri, javasoljuk, hogy az indexelő műveleteinek a REST-ben való használatát ismertető cikket.

> [!div class="nextstepaction"]
> [Indexelő műveletei](/rest/api/searchservice/indexer-operations)