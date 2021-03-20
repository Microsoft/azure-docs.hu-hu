---
title: Java fejlesztői referenciája Azure Functions
description: Ismerje meg, hogyan fejlesztheti a függvényeket a Javával.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96326454"
---
# <a name="azure-functions-java-developer-guide"></a>A Java fejlesztői útmutató Azure Functions

Ez az útmutató részletes információkat tartalmaz, amelyek segítenek a Azure Functions a Java használatával való sikeres fejlesztésében.

Ha a Azure Functions új, Java-fejlesztőként, vegye figyelembe a következő cikkek egyikét:

| Első lépések | Alapelvek| 
| -- | -- |  
| <ul><li>[Java-függvény a Visual Studio Code használatával](./create-first-function-vs-code-java.md)</li><li>[Java/Maven függvény a Terminal/parancssor használatával](./create-first-function-cli-java.md)</li><li>[Java-függvény a Gradle használatával](functions-create-first-java-gradle.md)</li><li>[Java-függvény az Eclipse használatával](functions-create-maven-eclipse.md)</li><li>[Java-függvény a IntelliJ IDEA használatával](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Fejlesztői útmutató](functions-reference.md)</li><li>[Üzemeltetési lehetőségek](functions-scale.md)</li><li>[Teljesítménnyel &nbsp; kapcsolatos megfontolások](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>A Java-függvények alapjai

A Java-függvények a `public` jegyzetekkel díszített metódusok `@FunctionName` . Ez a metódus definiálja a Java-függvények bejegyzését, és egyedinek kell lennie egy adott csomagban. A csomag több olyan osztállyal is rendelkezhet, amelyekhez több nyilvános metódus is tartozik `@FunctionName` . Egyetlen csomag van üzembe helyezve egy Azure-beli Function alkalmazásban. Az Azure-ban való futtatáskor a Function app biztosítja az egyes Java-függvények üzembe helyezési, végrehajtási és felügyeleti környezetét.

## <a name="programming-model"></a>Programozási modell 

Az [Eseményindítók és kötések](functions-triggers-bindings.md) fogalmai alapvető fontosságúak a Azure functions. Elindítja a kód végrehajtását. A kötések lehetővé teszik a függvények adatainak átadását és az adatok visszaküldését anélkül, hogy egyéni adatelérési kódot kellene írnia.

## <a name="create-java-functions"></a>Java-függvények létrehozása

Ahhoz, hogy egyszerűbb legyen a Java-függvények létrehozása, az előre definiált Java-sablonokat használó Maven-alapú eszközök és az olyan archetípusok, amelyek segítségével meghatározott függvény-triggerrel hozhat létre projekteket.    

### <a name="maven-based-tooling"></a>Maven-alapú eszközök

A következő fejlesztői környezetek Azure Functions eszközzel rendelkeznek, amely lehetővé teszi a Java-függvények projektjeinek létrehozását: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

A fenti hivatkozások azt mutatják be, hogyan hozhatja létre az első függvényeit az IDE választása szerint. 

### <a name="project-scaffolding"></a>Projekt – Állványzat

Ha a terminálról szeretne parancssori fejlesztést használni, a Java-alapú függvények projektjeinek legegyszerűbb módja az `Apache Maven` archetípusok használata. A Azure Functions Java Maven archetípusa a következő _GroupID_ van közzétéve:_artifactId_: [com. microsoft. Azure: Azure-functions-archetípus](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Az alábbi parancs egy új Java-függvény projektet hoz létre a következő archetípus használatával:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

A jelen archetípus használatának megkezdéséhez tekintse meg a [Java](./create-first-function-cli-java.md)rövid útmutatóját.

## <a name="folder-structure"></a>Mappa szerkezete

Itt látható egy Azure Functions Java-projekthez tartozó mappa szerkezete:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

A Function alkalmazás konfigurálásához használhat megosztott [host.jsa](functions-host-json.md) fájlon. Mindegyik függvényhez tartozik egy saját kódlap (. Java) és egy kötési konfigurációs fájl (function.js).

Egy projektben több függvény is elhelyezhető. Kerülje a függvények különálló tégelybe való elhelyezését. A `FunctionApp` cél könyvtárban az Azure-beli Function alkalmazás üzembe helyezése történik.

## <a name="triggers-and-annotations"></a>Eseményindítók és jegyzetek

 A függvényeket egy eseményindító hívja meg, például egy HTTP-kérelem, egy időzítő vagy egy frissítés az adatokhoz. A függvénynek fel kell dolgoznia az triggert és minden más bemenetet, hogy egy vagy több kimenetet hozzon létre.

Használja a [com. microsoft. Azure. functions. Megjegyzés. *](/java/api/com.microsoft.azure.functions.annotation) csomagban található Java-megjegyzéseket a bemenetek és kimenetek a metódusokhoz való kötéséhez. További információ: [Java-referenciák dokumentációja](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Az Azure Blob Storage, az Azure üzenetsor-tároló vagy az Azure Table Storage-eseményindítók helyi futtatásához konfigurálnia kell egy Azure Storage-fiókot a [local.settings.json](./functions-run-local.md#local-settings-file) .

Példa:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Az `function.json` [Azure-functions-Maven-beépülő modul](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin)által generált adatok:

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="java-versions"></a>Java-verziók

A (z) pom.xml fájlban az Azure-ban futó functions alkalmazás létrehozásakor használt Java verziója van megadva. A Maven archetípus jelenleg a Java 8 pom.xml generál, amelyet a közzététel előtt módosíthat. A pom.xml Java-verziójának egyeznie kell azzal a verzióval, amelyen az alkalmazást helyileg fejlesztette és tesztelte. 

### <a name="supported-versions"></a>Támogatott verziók

Az alábbi táblázat a functions futtatókörnyezet minden egyes főverziójához, az operációs rendszer által támogatott Java-verziókat tartalmazza:

| Függvények verziója | Java-verziók (Windows) | Java-verziók (Linux) |
| ----- | ----- | --- |
| 3. x | 11 <br/>8 | 11 <br/>8 |
| 2. x | 8 | n.a. |

Ha nem ad meg Java-verziót az üzemelő példányhoz, a Maven archetípus alapértelmezett értéke a Java 8 az Azure-ba való üzembe helyezés során.

### <a name="specify-the-deployment-version"></a>A központi telepítés verziójának meghatározása

A (z) paraméter használatával szabályozhatja a Maven archetípus által megcélozott Java-verziót `-DjavaVersion` . A paraméter értéke lehet `8` vagy `11` . 

A Maven archetípus egy pom.xml hoz létre, amely a megadott Java-verziót célozza meg. A pom.xml következő elemei a használni kívánt Java-verziót jelölik:

| Elem |  Java 8 érték | Java 11 érték | Description |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | A Maven-Compiler-beépülő modul által használt Java-verzió. |
| **`JavaVersion`** | 8 | 11 | Az Azure-beli Function alkalmazás által üzemeltetett Java-verzió. |

Az alábbi példák a Java 8 beállításait mutatják be a pom.xml fájl megfelelő részeiben:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> A JAVA_HOME környezeti változónak megfelelően kell beállítania a JDK-címtárat, amelyet a kód a Maven használatával történő fordításakor használ. Győződjön meg arról, hogy a JDK verziója legalább olyan magas, mint a `Java.version` beállítás. 

### <a name="specify-the-deployment-os"></a>A központi telepítés operációs rendszerének meghatározása

A Maven azt is lehetővé teszi, hogy az operációs rendszert, amelyre a Function app fut az Azure-ban. Az `os` elem használatával válassza ki az operációs rendszert. 

| Elem |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | Linux | Docker |

Az alábbi példa az operációs rendszer beállítását mutatja be a `runtime` pom.xml fájl szakaszában:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>A JDK futtatókörnyezet rendelkezésre állása és támogatása 

A Java Function apps helyi fejlesztéséhez töltse le és használja a megfelelő [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java JDK-t a [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/)-től. A Azure Functions egy Azul Java JDK futtatókörnyezetet használ a Function-alkalmazás Felhőbeli üzembe helyezése során.

Az [Azure-támogatás](https://azure.microsoft.com/support/) a JDK és a Function alkalmazással kapcsolatos problémákhoz egy [minősített támogatási csomaggal](https://azure.microsoft.com/support/plans/)érhető el.

## <a name="customize-jvm"></a>JVM testreszabása

A functions lehetővé teszi a Java-függvények futtatásához használt Java virtuális gép (JVM) testreszabását. Alapértelmezés szerint a [következő JVM-beállításokat](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) használja a rendszer:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

További argumentumokat is megadhat egy nevű alkalmazás-beállításban `JAVA_OPTS` . Az Azure-ban üzembe helyezett Function-alkalmazáshoz az Azure Portal vagy az Azure CLI-ben adhat hozzá Alkalmazásbeállítások.

> [!IMPORTANT]  
> A használati tervben hozzá kell adnia a 0 értékkel rendelkező WEBSITE_USE_PLACEHOLDER beállítást is a Testreszabás működéséhez. Ezzel a beállítással növelhető a Java-függvények hideg indítási ideje.

### <a name="azure-portal"></a>Azure Portal

A [Azure Portal](https://portal.azure.com)a beállítás hozzáadásához használja az [Alkalmazásbeállítások lapot](functions-how-to-use-azure-function-app-settings.md#settings) `JAVA_OPTS` .

### <a name="azure-cli"></a>Azure CLI

Az az [functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) paranccsal állíthatja be a `JAVA_OPTS` következő példát, ahogy az az alábbi példában is látható:

# <a name="consumption-plan"></a>[Használatalapú csomag](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[Használatalapú csomag](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Dedikált csomag/Prémium csomag](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Dedikált csomag/Prémium csomag](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

Ez a példa a fej nélküli üzemmódot teszi lehetővé. Cserélje le a `<APP_NAME>` nevet a Function alkalmazás nevére, és `<RESOURCE_GROUP>` az erőforráscsoport elemre. 

## <a name="third-party-libraries"></a>Harmadik féltől származó kódtárak 

A Azure Functions támogatja a külső gyártótól származó kódtárak használatát. Alapértelmezés szerint a rendszer a projektfájl összes függőségét `pom.xml` automatikusan becsomagolja a cél alatt [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) . A fájlban függőségként nem megadott könyvtárak esetében `pom.xml` helyezze azokat a `lib` függvény gyökérkönyvtárában található könyvtárba. A címtárban elhelyezett függőségek `lib` a rendszerosztály-betöltőhöz lesznek hozzáadva futásidőben.

A `com.microsoft.azure.functions:azure-functions-java-library` függőség a osztályútvonal alapértelmezés szerint van megadva, és nem kell szerepelnie a `lib` címtárban. Az [Azure-functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) az [itt](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) felsorolt függőségeket is hozzáadja a osztályútvonal.

## <a name="data-type-support"></a>Adattípusok támogatása

Használhat egyszerű, régi Java-objektumokat (Szerializálói), definiált típusokat `azure-functions-java-library` vagy primitív adattípusokat, például karakterláncot és egész számot a bemeneti vagy kimeneti kötésekhez való kötéshez.

### <a name="pojos"></a>Szerializálói

A bemeneti adatok POJO alakításához az [Azure-functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) a [gson](https://github.com/google/gson) könyvtárat használja. A függvények bemenetként használt POJO-típusok legyenek `public` .

### <a name="binary-data"></a>Bináris adatok

A bináris bemenetek és kimenetek összekötése a `byte[]` `dataType` function.jskövetkezőhöz `binary` :

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Ha a rendszer null értékeket vár, használja a értéket `Optional<T>` .

## <a name="bindings"></a>Kötések

A bemeneti és kimeneti kötések deklaratív módszert biztosítanak a kódban lévő adatokhoz való kapcsolódáshoz. A függvények több bemeneti és kimeneti kötéssel is rendelkezhetnek.

### <a name="input-binding-example"></a>Bemeneti kötési példa

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Ezt a függvényt egy HTTP-kérelemmel hívja meg. 
- Az argumentumhoz a HTTP-kérelem adattartalmát adja át a rendszer `String` `inputReq` .
- A rendszer beolvas egy bejegyzést a Table Storage-ból, és `TestInputData` az argumentumként adja át `inputData` .

Bemeneti kötegek fogadásához a következőhöz köthető:, `String[]` , `POJO[]` `List<String>` vagy `List<POJO>` .

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Ez a függvény akkor aktiválódik, amikor új adat van a konfigurált Event hub-ban. Mivel a értéke `cardinality` `MANY` , a függvény egy köteg üzenetet fogad az Event hub-ból. `EventData` az Event hub-ból a `TestEventData` függvény végrehajtásához konvertálva lesz.

### <a name="output-binding-example"></a>Kimeneti kötési példa

A paranccsal kötést adhat hozzá a visszatérési értékhez `$return` . 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Ha több kimeneti kötés is van, akkor a visszatérési értéket csak az egyikre használja.

Több kimeneti érték küldéséhez használja `OutputBinding<T>` a csomagban definiált értéket `azure-functions-java-library` . 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Ezt a függvényt egy HttpRequest hívja meg. Több értéket ír a várólista-tárolóba.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage és HttpResponseMessage

 Ezek a ben vannak meghatározva `azure-functions-java-library` . A HttpTrigger functions szolgáltatással használható segítő típusok.

| Speciális típus      |       Cél        | Jellemző használat                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-eseményindító     | Metódusok, fejlécek vagy lekérdezések beolvasása |
| `HttpResponseMessage` | HTTP kimeneti kötés | A 200-tól eltérő állapotot ad vissza.   |

## <a name="metadata"></a>Metaadatok

Néhány eseményindító elküldi az [eseményindító-metaadatokat](./functions-triggers-bindings.md) a bemeneti adatokkal együtt. A megjegyzésekkel `@BindingName` kötést használhat a trigger metaadataihoz.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Az előző példában a a `queryValue` `name` http-kérelem URL-címében a lekérdezési karakterlánc paraméterhez van kötve `http://{example.host}/api/metadata?name=test` . Íme egy másik példa, amely bemutatja, hogyan lehet kötést létrehozni `Id` a várólista-trigger metaadatainak használatával.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> A jegyzetben megadott névnek egyeznie kell a metadata tulajdonsággal.

## <a name="execution-context"></a>Végrehajtási környezet

`ExecutionContext`a ben definiált `azure-functions-java-library` , segítő metódusokat tartalmaz a functions futtatókörnyezettel folytatott kommunikációhoz. További információkért tekintse meg a [ExecutionContext-referenciát ismertető cikket](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Tuskózó

A `getLogger` -ben definiált, a `ExecutionContext` naplókat a függvény kódjából való írásához használhatja.

Példa:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Naplók és nyomkövetés megtekintése

Az Azure CLI-vel a Java stdout és a stderr naplózását, valamint más alkalmazások naplózását is elvégezheti. 

Az alábbi módon konfigurálhatja a Function alkalmazást az alkalmazások naplózásának az Azure CLI használatával történő írásához:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Ha az Azure CLI használatával szeretné továbbítani a függvény alkalmazásának naplózási kimenetét, nyisson meg egy új parancssort, bash-t vagy terminál-munkamenetet, és írja be a következő parancsot:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

Az az [WebApp log farok](/cli/azure/webapp/log) paranccsal a lehetőség használatával szűrheti a kimenetet `--provider` . 

Ha az Azure CLI használatával egyetlen ZIP-fájlként szeretné letölteni a naplófájlokat, nyisson meg egy új parancssort, bash-t vagy terminál-munkamenetet, és írja be a következő parancsot:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

A parancs futtatása előtt engedélyeznie kell a fájlrendszer naplózását a Azure Portal vagy az Azure CLI-ben.

## <a name="environment-variables"></a>Környezeti változók

A függvények, az [Alkalmazásbeállítások](functions-app-settings.md), például a szolgáltatási kapcsolatok karakterláncai a végrehajtás során környezeti változókként jelennek meg. Ezeket a beállításokat a () segítségével érheti el `System.getenv("AzureWebJobsStorage")` .

A következő példa beolvassa az [alkalmazás beállítását](functions-how-to-use-azure-function-app-settings.md#settings)a nevű kulccsal `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> A Alkalmazásbeállítás FUNCTIONS_EXTENSION_VERSION értékének a következőnek kell lennie: ~ 2 vagy ~ 3 az optimalizált, hideg indítási élményhez.

## <a name="next-steps"></a>Következő lépések

Az Azure Functions Java-fejlesztéssel kapcsolatos további információkért tekintse meg a következő forrásokat:

* [Azure Functions – ajánlott eljárások](functions-best-practices.md)
* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Eseményindítók és kötések Azure Functions](functions-triggers-bindings.md)
* Helyi fejlesztés és hibakeresés a [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), a [IntelliJ](functions-create-maven-intellij.md)és az [Eclipse](functions-create-maven-eclipse.md) használatával
* [Java-függvények távoli hibakeresése a Visual Studio Code használatával](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven beépülő modul a Azure Functionshoz](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Egyszerűsítse a függvények létrehozását a `azure-functions:add` cél segítségével, és készítse elő a [zip-fájl üzembe helyezésének](deployment-zip-push.md)előkészítési könyvtárát.
