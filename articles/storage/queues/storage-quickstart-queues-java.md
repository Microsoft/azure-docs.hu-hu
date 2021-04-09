---
title: 'Gyors útmutató: Azure Queue Storage Client Library V12-Java'
description: Ebből Queue Storage a témakörből megtudhatja, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket. Ebből a cikkből megtudhatja, hogyan olvashatja és törölheti az üzeneteket a várólistából. Azt is megtudhatja, hogyan törölhet egy várólistát.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 814531adc9dafa524797d0c2674a1e600e407bed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588216"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>Gyors útmutató: Azure Queue Storage Client Library V12 a Javához

Ismerkedjen meg a Javához készült Azure Queue Storage ügyfél-kódtár V12-es verziójának használatába. Az Azure Queue Storage egy olyan szolgáltatás, amely nagy mennyiségű üzenetet tárol a későbbi lekérésekhez és feldolgozáshoz. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja a programkódot az alapszintű feladatokhoz.

A Javához készült Azure Queue Storage a V12-es ügyféloldali kódtár használatával:

- Üzenetsor létrehozása
- Üzenetek hozzáadása egy várólistához
- Üzenetek betekintése egy várólistába
- Üzenetsor frissítése
- Üzenetek fogadása és törlése egy várólistából
- Üzenetsor törlése

További források:

- [API-referenciadokumentáció](/java/api/overview/azure/storage-queue-readme)
- [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [Csomag (Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [Példák](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Előfeltételek

- A [Java Development Kit (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- Azure Storage-fiók – [Storage-fiók létrehozása](../common/storage-account-create.md)

## <a name="setting-up"></a>Beállítás

Ez a szakasz végigvezeti a projekt előkészítésének folyamatán a Javához készült Azure Queue Storage ügyféloldali kódtár V12-es verziójának használatával.

### <a name="create-the-project"></a>A projekt létrehozása

Hozzon létre egy nevű Java-alkalmazást `queues-quickstart-v12` .

1. A konzol ablakban (például cmd, PowerShell vagy bash) a Maven használatával hozzon létre egy új, a nevű Console-alkalmazást `queues-quickstart-v12` . A `mvn` "Hello World" Java-projekt létrehozásához írja be a következő parancsot.

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

1. A projekt létrehozásának eredményét a következőhöz hasonló módon kell kinéznie:

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

1. Váltson az újonnan létrehozott `queues-quickstart-v12` címtárra.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a fájlt a szövegszerkesztőben `pom.xml` . Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Navigáljon a `/src/main/java/com/queues/quickstart` címtárhoz
1. Nyissa meg a `App.java` fájlt a szerkesztőben
1. Az `System.out.println("Hello, world");` utasítás törlése
1. `import`Irányelvek hozzáadása

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

Az Azure Queue Storage szolgáltatás nagy számú üzenet tárolására szolgál. Egy üzenetsor-üzenet akár 64 KB méretű is lehet. Egy üzenetsor akár több millió üzenetet is tartalmazhat, akár egy Storage-fiók teljes kapacitási korlátját. A várólistákat általában arra használják, hogy egy várakozó munkafolyamatot hozzon létre aszinkron feldolgozásra. A Queue Storage háromféle típusú erőforrást kínál:

- A Storage-fiók
- A Storage-fiókban lévő üzenetsor
- Üzenetek a várólistán belül

Az alábbi ábra az ezen erőforrások közötti kapcsolatot mutatja be.

![Üzenetsor-tárolási architektúra ábrája](./media/storage-queues-introduction/queue1.png)

A következő Java-osztályok használhatók az alábbi erőforrásokkal való kommunikációhoz:

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): Az `QueueClientBuilder` osztály egy objektumot konfigurál és hoz létre `QueueClient` .
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): A `QueueServiceClient` lehetővé teszi a Storage-fiók összes várólistájának kezelését.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): Az `QueueClient` osztály segítségével kezelheti és kezelheti az egyes várólistákat és az üzeneteiket.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): Az `QueueMessageItem` osztály a várólista hívásakor visszaadott egyedi objektumokat jelöli [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) .

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő műveleteket az Azure Queue Storage Javához készült ügyféloldali kódtár használatával:

- [A kapcsolati sztring lekérése](#get-the-connection-string)
- [Üzenetsor létrehozása](#create-a-queue)
- [Üzenetek hozzáadása egy várólistához](#add-messages-to-a-queue)
- [Üzenetek betekintése egy várólistába](#peek-at-messages-in-a-queue)
- [Üzenetsor frissítése](#update-a-message-in-a-queue)
- [Üzenetek fogadása és törlése egy várólistából](#receive-and-delete-messages-from-a-queue)
- [Üzenetsor törlése](#delete-a-queue)

### <a name="get-the-connection-string"></a>A kapcsolati sztring lekérése

A következő kód lekéri a Storage-fiókhoz tartozó kapcsolatok karakterláncát. A rendszer tárolja a hálózati karakterláncot a [tárolási kapcsolatok karakterláncának konfigurálása](#configure-your-storage-connection-string) szakaszban létrehozott környezeti változóban.

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

Döntse el az új üzenetsor nevét. A következő kód egy GUID értéket fűz hozzá a várólista neveként, hogy az egyedi legyen.

> [!IMPORTANT]
> A várólisták nevei csak kisbetűket, számokat és kötőjeleket tartalmazhatnak, és betűvel vagy számmal kell kezdődnie. A kötőjelek előtt és után csak nem kötőjel karakter állhat. A névnek 3 – 63 karakter hosszúnak kell lennie. További információ a várólisták elnevezéséről: [várólisták és metaadatok elnevezése](/rest/api/storageservices/naming-queues-and-metadata).

Hozza létre a osztály egy példányát [`QueueClient`](/java/api/com.azure.storage.queue.queueclient) . Ezután hívja meg a [`Create`](/java/api/com.azure.storage.queue.queueclient.create) metódust az üzenetsor létrehozásához a Storage-fiókban.

Adja hozzá ezt a kódot a metódus végéhez `main` :

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

### <a name="add-messages-to-a-queue"></a>Üzenetek hozzáadása egy várólistához

A következő kódrészlet üzeneteket hoz létre a várólistához a metódus meghívásával [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) . Egy [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult) hívást is visszaküld `sendMessage` . Az eredmény az üzenet későbbi, a programban való frissítésére szolgál.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Üzenetek betekintése egy várólistába

A metódus meghívásával megtekintheti a várólistában lévő üzeneteket [`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) . Ez a metódus egy vagy több üzenetet kér le a várólista elejéről, de nem módosítja az üzenet láthatóságát.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>Üzenetsor frissítése

Az üzenet tartalmának frissítéséhez hívja meg a [`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) metódust. Ez a metódus módosíthatja az üzenet láthatósági időtúllépését és tartalmát. Az üzenet tartalmának UTF-8 kódolású karakterláncnak kell lennie, amely akár 64 KB méretű is lehet. Az üzenethez tartozó új tartalom mellett adja át az üzenet AZONOSÍTÓját és a pop-nyugtát a `SendMessageResult` kódban korábban mentett fájl használatával. Az üzenet azonosítója és a pop-visszaigazolás azonosítja a frissítendő üzenetet.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>Üzenetek fogadása és törlése egy várólistából

A korábban hozzáadott üzenetek letöltése a metódus meghívásával [`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) . A példában a kód a fogadás és a feldolgozás után is törli az üzeneteket a várólistából. Ebben az esetben a feldolgozás csak az üzenet megjelenítését jeleníti meg a konzolon.

Az alkalmazás a felhasználói bevitel szüneteltetését hívja `System.console().readLine();` meg az üzenetek fogadása és törlése előtt. A törlés előtt ellenőrizze, hogy az erőforrások megfelelően lettek-e létrehozva a [Azure Portal](https://portal.azure.com) . A nem explicit módon törölt üzenetek később ismét láthatóvá válnak a várólistában, hogy egy másik lehetőség is feldolgozza őket.

Adja hozzá ezt a kódot a metódus végéhez `main` :

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

A következő kód törli az alkalmazás által létrehozott erőforrásokat az üzenetsor törlésével a [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) metódus használatával.

Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>A kód futtatása

Ez az alkalmazás három üzenetet hoz létre és tesz hozzá egy Azure-várólistához. A kód felsorolja a várólistán lévő üzeneteket, majd lekéri és törli őket, mielőtt véglegesen törölné a várólistát.

A konzol ablakban navigáljon az alkalmazás könyvtárába, majd hozza létre és futtassa az alkalmazást.

```console
mvn compile
```

Ezután hozza létre a csomagot.

```console
mvn package
```

Az `mvn` alkalmazás futtatásához használja a következő parancsot.

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

Ha az alkalmazás szünetelteti az üzenetek fogadását, akkor a [Azure Portalban](https://portal.azure.com)keresse meg a Storage-fiókját. Ellenőrizze, hogy az üzenetek szerepelnek-e a várólistán.

Kattintson a `Enter` kulcsra az üzenetek fogadásához és törléséhez. Amikor a rendszer kéri, nyomja le `Enter` ismét a gombot a várólista törléséhez és a bemutató befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan hozhat létre várólistát, és hogyan adhat hozzá üzeneteket a Java-kóddal. Ezután megtanulta az üzenetek betekintését, beolvasását és törlését. Végezetül megtanulta, hogyan törölhet egy üzenetsor-várólistát.

Oktatóanyagok, minták, gyors indítás és egyéb dokumentáció:

> [!div class="nextstepaction"]
> [Azure Java-felhőfejlesztőknek](/azure/developer/java/)

- További Azure Queue Storage-példákat az [azure Queue Storage a Java-mintákhoz készült ügyféloldali kódtárat](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)ismertető témakörben talál.
