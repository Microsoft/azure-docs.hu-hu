---
title: 'Rövid útmutató: Azure Queue Storage kódtár v12 -es elérésű – Java'
description: Megtudhatja, hogyan használhatja Azure Queue Storage Java-hoz használható ügyféloldali kódtár v12-t egy üzenetsor létrehozásához és üzenetek hozzáadásához. Ezután megtudhatja, hogyan olvashatja és törölheti az üzeneteket az üzenetsorból. Azt is megtudhatja, hogyan törölhet egy üzenetsort.
author: twooley
ms.author: twooley
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- devx-track-java
- mode-api
ms.openlocfilehash: f4e33cac6ba00be56b0f63cf5a10b2dce32e1be7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534464"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Rövid útmutató: Azure Queue Storage Kódtár v12 for Java használata

Első lépések a Azure Queue Storage Java-hoz való v12-es kódtárával. Azure Queue Storage egy nagy számú üzenet tárolására szolgáló szolgáltatás későbbi lekéréshez és feldolgozáshoz. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot.

Használja a Azure Queue Storage Java-hoz való 12-es ügyféloldali kódtárát a következőre:

- Üzenetsor létrehozása
- Üzenetek hozzáadása üzenetsorhoz
- Betekintés az üzenetsor üzeneteibe
- Üzenetsor üzenetének frissítése
- Üzenetek fogadása és törlése egy üzenetsorból
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/java/api/overview/azure/storage-queue-readme)
- [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Példák](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- [Java fejlesztői készlet (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure-előfizetés [– hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/)
- Azure Storage-fiók [– tárfiók létrehozása](../common/storage-account-create.md)

## <a name="setting-up"></a>Beállítása

Ez a szakasz végigvezeti egy projekt előkészítésén a Azure Queue Storage Java-hoz készült 12-es Azure Queue Storage való munkához.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy nevű `queues-quickstart-v12` Java-alkalmazást.

1. Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új konzolalkalmazást a Maven használatával `queues-quickstart-v12` néven. Írja be a következő `mvn` parancsot egy "hello world" Java-projekt létrehozásához.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

1. A projekt generálásának kimenete a következő lesz:

    ```console
    [INFO] Scanning for projects...
    [INFO]
    [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
    [INFO] Building Maven Stub Project (No POM) 1
    [INFO] --------------------------------[ pom ]---------------------------------
    [INFO]
    [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
    [INFO]
    [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
    [INFO]
    [INFO]
    [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
    [INFO] Generating project in Batch mode
    [INFO] ----------------------------------------------------------------------------
    [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
    [INFO] ----------------------------------------------------------------------------
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. Váltson az újonnan létrehozott `queues-quickstart-v12` könyvtárra.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg `pom.xml` a fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemet a függőségek csoportjához.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projektkönyvtárból:

1. Lépjen a `/src/main/java/com/queues/quickstart` könyvtárba
1. Nyissa meg `App.java` a fájlt a szerkesztőben
1. A utasítás `System.out.println("Hello, world");` törlése
1. `import`Direktíva hozzáadása

A kód a következő:

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektummodell

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Az üzenetsor-üzenetek mérete legfeljebb 64 KB lehet. Az üzenetsorok több millió üzenetet tartalmazhatnak, a tárfiókok maximális kapacitásának korlátjig. Az üzenetsorokat gyakran használják hátralékos feladatok létrehozásához aszinkron feldolgozáshoz. Queue Storage háromféle erőforrást kínál:

- A tárfiók
- Üzenetsor a tárfiókban
- Üzenetsoron belüli üzenetek

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![A Queue Storage architektúrájának ábrája](./media/storage-queues-introduction/queue1.png)

Az alábbi Java-osztályokkal használhatja ezeket az erőforrásokat:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): A `QueueClientBuilder` osztály konfigurál és példányosul egy `QueueClient` objektumot.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): A lehetővé teszi a tárfiókban lévő `QueueServiceClient` összes üzenetsor kezelését.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): A `QueueClient` osztály lehetővé teszi az egyes üzenetsorok és üzeneteik kezelését és kezelését.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): A osztály az üzenetsoron való híváskor `QueueMessageItem` visszaadott egyes [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) objektumokat jelöli.

## <a name="code-examples"></a>Kódpéldák

Ezek a példakódrészletek azt mutatják be, hogyan adhatja meg a következő műveleteket Azure Queue Storage Java-ügyfélkódtárban:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása üzenetsorhoz](#add-messages-to-a-queue)
- [Betekintés az üzenetsor üzeneteibe](#peek-at-messages-in-a-queue)
- [Üzenetsor üzenetének frissítése](#update-a-message-in-a-queue)
- [Üzenetek fogadása és törlése egy üzenetsorból](#receive-and-delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a tárfiók kapcsolati sztringét. A kapcsolati sztring a Tároló kapcsolati sztring konfigurálása szakaszban létrehozott környezeti [változót tárolja.](#configure-your-storage-connection-string)

Adja hozzá ezt a kódot a `main` metódushoz:

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Üzenetsor létrehozása

Döntse el az új üzenetsor nevét. Az alábbi kód egy GUID-értéket fűz hozzá az üzenetsor nevéhez, hogy az egyedi legyen.

> [!IMPORTANT]
> Az üzenetsorok nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3–63 karakter hosszúságúnak kell lennie. Az üzenetsorok elnevezésével kapcsolatos további információkért lásd: [Üzenetsorok és metaadatok elnevezése.](/rest/api/storageservices/naming-queues-and-metadata)

Hozza létre a osztály egy [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) példányát. Ezután hívja meg a [`Create`](/java/api/com.azure.storage.queue.queueclient.create) metódust az üzenetsor létrehozásához a tárfiókban.

Adja hozzá ezt a kódot a metódus `main` véghez:

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása üzenetsorhoz

Az alábbi kódrészlet üzeneteket ad hozzá az üzenetsorhoz a metódus [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) hívásával. Emellett egy hívásból [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) visszaadott et is `sendMessage` ment. Az eredmény az üzenet frissítésére lesz használva a program későbbi részen.

Adja hozzá ezt a kódot a metódus `main` véghez:

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Betekintés az üzenetsor üzeneteibe

A metódus hívásával bepillantás az üzenetsor [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) üzeneteibe. Ez a metódus egy vagy több üzenetet ad vissza az üzenetsor elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a metódus `main` véghez:

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Üzenet frissítése egy üzenetsorban

Frissítse egy üzenet tartalmát a metódus [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) hívásával. Ez a metódus módosíthatja az üzenetek láthatósági időkorlátját és tartalmát. Az üzenet tartalmának UTF-8 kódolású sztringnek kell lennie, amely legfeljebb 64 KB méretű lehet. Az üzenet új tartalma mellett adja meg az üzenet azonosítóját és a nyugtát a kódban korábban mentett `SendMessageResult` használatával. Az üzenetazonosító és a pop-nyugta határozza meg, hogy melyik üzenetet kell frissíteni.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Üzenetek fogadása és törlése egy üzenetsorból

Töltse le a korábban hozzáadott üzeneteket a metódus [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) hívásával. A példakód a fogadott és feldolgozott üzeneteket is törli az üzenetsorból. Ebben az esetben a feldolgozás csak az üzenetet jeleníti meg a konzolon.

Az alkalmazás az üzenetek fogadása és törlése előtt a hívással szünetelteti `System.console().readLine();` a felhasználói bevitelt. Ellenőrizze a [Azure Portal,](https://portal.azure.com) hogy az erőforrások megfelelően, a törlés előtt létrejöttek-e. A explicit módon nem törölt üzenetek végül ismét láthatóvá válnak az üzenetsorban, hogy egy másik lehetőség legyen a feldolgozásukra.

Adja hozzá ezt a kódot a metódus `main` véghez:

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>Üzenetsor törlése

Az alábbi kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor a metódussal való [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) törlésével.

Adja hozzá ezt a kódot a metódus `main` véghez:

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és ad hozzá egy Azure-üzenetsorhoz. A kód listázza az üzenetsorban lévő üzeneteket, majd lekéri és törli őket, mielőtt végül töröl egy üzenetsort.

A konzolablakban keresse meg az alkalmazás könyvtárát, majd építse fel és futtassa az alkalmazást.

```console
mvn compile
```

Ezután készítse el a csomagot.

```console
mvn package
```

Futtassa az `mvn` alkalmazást a következő paranccsal.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

Az alkalmazás kimenete az alábbi példához hasonló:

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

Amikor az alkalmazás szünetel, mielőtt üzeneteket kap, ellenőrizze a tárfiókot a [Azure Portal.](https://portal.azure.com) Ellenőrizze, hogy az üzenetek az üzenetsorban vannak-e.

Nyomja le `Enter` a billentyűt az üzenetek fogadására és törlésére. Amikor a rendszer kéri, nyomja le újra a billentyűt az üzenetsor `Enter` törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan hozhat létre üzenetsort, és hogyan adhat hozzá üzeneteket Java-kód használatával. Ezután megtanulta, hogyan lehet betekintésbe, lekérni és törölni az üzeneteket. Végül megtanulta, hogyan törölhet üzenetsort.

Az oktatóanyagokért, mintákért, rövid útmutatókért és egyéb dokumentációkért látogasson el a következő webhelyre:

> [!div class="nextstepaction"]
> [Azure Java-felhőfejlesztőknek](/azure/developer/java/)

- További Azure Queue Storage a Java [12-es](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)Azure Queue Storage ügyféloldali kódtárát – minták.
