---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: pvicencio
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/12/2021
ms.topic: include
ms.custom: include file
ms.author: pvicencio
ms.openlocfilehash: 0a59b18fa2c09cff943dbccd2203027cd72e146a
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644356"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások Java SMS SDK használatával SMS-üzenetek küldéséhez.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Java Development Kit (JDK)](/java/azure/jdk/) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../../create-communication-resource.md).
- SMS-kompatibilis telefonszám. [Telefonszám beolvasása](../get-phone-number.md).

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

- A terminál vagy a parancssorablakban futtassa a parancsot `mvn -v` a Maven telepítésének megadásához.
- A kommunikációs szolgáltatások erőforrásához tartozó telefonszámok megtekintéséhez jelentkezzen be a [Azure Portalba](https://portal.azure.com/), keresse meg a kommunikációs szolgáltatások erőforrását, és nyissa meg a **telefonszámok** lapot a bal oldali navigációs ablaktáblán.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Nyissa meg a terminál vagy a parancssorablakot, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java-alkalmazást. Futtassa az alábbi parancsot a Java-projekt létrehozásához a Maven-archetípus-Gyorsindítás sablonból.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

A "létrehozás" cél létrehoz egy könyvtárat a artifactId megegyező névvel. Ebben a könyvtárban a **src/Main/Java** könyvtár tartalmazza a projekt forráskódját, az **src/test/Java könyvtárat** , amely tartalmazza a teszt forrását, és a **pom.xml** fájl a projekt projekt objektummodell-modellje vagy Pom.

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a **pom.xml** fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.4</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Adja hozzá a `azure-core-http-netty` függőséget a **pom.xml** -fájlhoz.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.8.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core</artifactId>
    <version>1.13.0</version> <!-- {x-version-update;com.azure:azure-core;dependency} -->
</dependency>
```

Nyissa meg a **/src/Main/Java/com/Communication/Quickstart/app.Java** egy szövegszerkesztőben, adja hozzá az importálási irányelveket, és távolítsa el az `System.out.println("Hello world!");` utasítást:

```java
package com.communication.quickstart;

import com.azure.communication.sms.models.*;
import com.azure.core.credential.AzureKeyCredential;
import com.azure.communication.sms.*;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;
import com.azure.core.util.Context;
import java.util.Arrays;

public class App
{
    public static void main( String[] args )
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik az Azure kommunikációs szolgáltatások SMS SDK Java-hoz tartozó főbb funkcióit.

| Név                                                             | Leírás                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Ez az osztály hozza létre a SmsClient. Ezt a végponttal, a hitelesítő adatokkal és egy http-ügyféllel biztosíthatja. |
| SmsClient                    | Ez az osztály minden SMS-funkcióhoz szükséges. Az SMS-üzenetek küldésére használható.                |
| SmsSendOptions               | Ez az osztály lehetőséget biztosít egyéni címkék hozzáadására és a kézbesítési jelentéskészítés konfigurálására. Ha a deliveryReportEnabled értéke TRUE (igaz), akkor egy esemény lesz kibocsátva, ha a kézbesítés sikeres volt.|                           |
| SmsSendResult                | Ez az osztály az SMS szolgáltatás eredményét tartalmazza.                                          |

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Példány létrehozása `SmsClient` a-val a kapcsolatok karakterláncával. (A hitelesítő adatok a `Key` Azure Portalból származnak. Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `main` metódushoz:

```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<resource-name>.communication.azure.com/";
AzureKeyCredential azureKeyCredential = new AzureKeyCredential("<access-key-credential>");

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
                .endpoint(endpoint)
                .credential(azureKeyCredential)
                .httpClient(httpClient)
                .buildClient();
```

Az ügyfelet bármely olyan egyéni HTTP-ügyféllel inicializálhatja, amely megvalósítja a `com.azure.core.http.HttpClient` felületet. A fenti kód azt mutatja be, hogy az [Azure alapszintű](/java/api/overview/azure/core-http-netty-readme) , az által biztosított http-ügyfelet használja `azure-core` .

A teljes kapcsolati karakterláncot a connectionString () függvény használatával is megadhatja a végpont és a hozzáférési kulcs megadása helyett. 
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<resource-name>.communication.azure.com/;<access-key>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

SmsClient smsClient = new SmsClientBuilder()
            .connectionString(connectionString)
            .httpClient(httpClient)
            .buildClient();
```

## <a name="send-a-11-sms-message"></a>1:1 SMS-üzenet küldése

Ha SMS-üzenetet szeretne küldeni egyetlen címzettnek, hívja meg a `send` metódust a SmsClient egyetlen címzett telefonszámával. A választható paramétereket is megadhatja annak megadásához, hogy a kézbesítési jelentést engedélyezni kell-e, és egyéni címkéket kell-e beállítani.

```java
SmsSendResult sendResult = smsClient.send(
                "<from-phone-number>",
                "<to-phone-number>",
                "Weekly Promotion");

System.out.println("Message Id: " + sendResult.getMessageId());
System.out.println("Recipient Number: " + sendResult.getTo());
System.out.println("Send Result Successful:" + sendResult.isSuccessful());
```

Egy SMS- `<from-phone-number>` kompatibilis telefonszámot kell cserélnie a kommunikációs szolgáltatások erőforrásaihoz, és `<to-phone-number>` egy telefonszámot, amelyre üzenetet kíván küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>1: N SMS-üzenet küldése a következő beállításokkal
Ha SMS-üzenetet szeretne küldeni a címzettek listájára, hívja meg a `send` metódust a címzett telefonszámok listájával. A választható paramétereket is megadhatja annak megadásához, hogy a kézbesítési jelentést engedélyezni kell-e, és egyéni címkéket kell-e beállítani.
```java
SmsSendOptions options = new SmsSendOptions();
options.setDeliveryReportEnabled(true);
options.setTag("Marketing");

Iterable<SmsSendResult> sendResults = smsClient.send(
    "<from-phone-number>",
    Arrays.asList("<to-phone-number1>", "<to-phone-number2>"),
    "Weekly Promotion",
    options /* Optional */,
    Context.NONE);

for (SmsSendResult result : sendResults) {
    System.out.println("Message Id: " + result.getMessageId());
    System.out.println("Recipient Number: " + result.getTo());
    System.out.println("Send Result Successful:" + result.isSuccessful());
}
```

A lecserélni kívánt `<from-phone-number>` SMS-kompatibilis telefonszámot a kommunikációs szolgáltatások erőforrásaihoz és `<to-phone-number-1>` `<to-phone-number-2>` azokhoz a telefonszám (ok) hoz, amelyekről üzenetet kíván küldeni.

> [!WARNING]
> Vegye figyelembe, hogy a telefonszámokat E. 164 nemzetközi szabvány formátumban kell megadni. (például: + 14255550123).

A `setDeliveryReportEnabled` metódus a kézbesítési jelentéskészítés konfigurálására szolgál. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.

A `setTag` metódus egy címke kézbesítési jelentésre való alkalmazására szolgál.

## <a name="run-the-code"></a>A kód futtatása

Navigáljon a **pom.xml** fájlt tartalmazó könyvtárra, és fordítsa le a projektet a `mvn` parancs használatával.

```console

mvn compile

```

Ezután hozza létre a csomagot.

```console

mvn package

```

Futtassa az alábbi `mvn` parancsot az alkalmazás végrehajtásához.

```console

mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false

```