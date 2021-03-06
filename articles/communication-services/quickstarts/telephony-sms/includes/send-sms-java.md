---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 1c4f3c47e3ac6e1e701b673574bb664237c1a9af
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244803"
---
Ismerkedés az Azure kommunikációs szolgáltatásokkal a kommunikációs szolgáltatások Java SMS ügyféloldali kódtár használatával SMS-üzenetek küldéséhez.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

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
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

Adja hozzá a `azure-core-http-netty` függőséget a **pom.xml** -fájlhoz.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Nyissa meg a **/src/Main/Java/com/Communication/Quickstart/app.Java** egy szövegszerkesztőben, adja hozzá az importálási irányelveket, és távolítsa el az `System.out.println("Hello world!");` utasítást:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik az Azure kommunikációs szolgáltatások SMS ügyféloldali kódtárajának főbb funkcióit a Javához.

| Név                                                             | Leírás                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Ez az osztály hozza létre a SmsClient. Ezt a végponttal, a hitelesítő adatokkal és egy http-ügyféllel biztosíthatja. |
| SmsClient                    | Ez az osztály minden SMS-funkcióhoz szükséges. Az SMS-üzenetek küldésére használható.                |
| SendSmsResponse               | Ez az osztály az SMS szolgáltatás válaszát tartalmazza.                                          |
| PhoneNumber                   | Ez az osztály a telefonszámra vonatkozó információkat tartalmazza.

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Példány létrehozása `SmsClient` a-val a kapcsolatok karakterláncával. Az alábbi kód lekéri a végpont és a hitelesítő adatok karakterláncait az erőforráshoz a (z) nevű környezeti változók alapján, a `COMMUNICATION_SERVICES_ENDPOINT_STRING` `COMMUNICATION_SERVICES_CREDENTIAL_STRING` hitelesítő adat pedig a `Key` Azure Portal. Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `main` metódushoz:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Az ügyfelet bármely olyan egyéni HTTP-ügyféllel inicializálhatja, amely megvalósítja a `com.azure.core.http.HttpClient` felületet. A fenti kód azt mutatja be, hogy az [Azure alapszintű](/java/api/overview/azure/core-http-netty-readme) , az által biztosított http-ügyfelet használja `azure-core` .

A teljes kapcsolati karakterláncot a connectionString () függvény használatával is megadhatja a végpont és a hozzáférési kulcs megadása helyett. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
SmsClient client = new SmsClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="send-an-sms-message"></a>SMS küldése

SMS-üzenet küldése a üzenetküldés metódus meghívásával. Adja hozzá ezt a kódot a metódus végéhez `main` :

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

`<leased-phone-number>`Egy SMS-kompatibilis telefonszámot kell cserélnie a kommunikációs szolgáltatások erőforrásához, és `<to-phone-number>` azt a telefonszámot, amelyhez üzenetet kíván küldeni.

A `enableDeliveryReport` paraméter egy opcionális paraméter, amely a kézbesítési jelentéskészítés konfigurálására használható. Ez olyan esetekben hasznos, amikor az SMS-üzenetek kézbesítése során eseményeket szeretne kibocsátani. Tekintse meg az [SMS-események kezelése](../handle-sms-events.md) rövid útmutatót az SMS-üzenetek kézbesítési jelentéskészítésének konfigurálásához.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

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