---
title: fájl belefoglalása
description: fájl belefoglalása
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 1881b05c32fb0a7206ba6439db5c44ad909de798
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101751114"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A [Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Egy üzembe helyezett kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](../create-communication-resource.md).

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Nyissa meg a terminál vagy a parancs ablakát. Navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java-alkalmazást. Futtassa az alábbi parancsot a Java-projekt létrehozásához a Maven-archetípus-Gyorsindítás sablonból.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Megfigyelheti, hogy a "létrehozás" feladat létrehozta a könyvtárat ugyanazzal a névvel `artifactId` . Ebben a könyvtárban a src/Main/Java könyvtár tartalmazza a projekt forráskódját, a `src/test/java directory` tartalmazza a teszt forrását, a `pom.xml` fájl pedig a projekt projekt-objektummodell-modellje, vagy Pom.

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg a **pom.xml** fájlt a szövegszerkesztőben. Adja hozzá a függőségek csoportjához a következő függőségi elemet.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0-beta.3</version> 
</dependency>
```

### <a name="set-up-the-app-framework"></a>Az alkalmazás-keretrendszer beállítása

A projekt könyvtárából:

1. Navigáljon a */src/Main/Java/com/Communication/Quickstart* könyvtárához
1. Nyissa meg az *app. Java* fájlt a szerkesztőben
1. Az `System.out.println("Hello world!");` utasítás cseréje
1. `import`Irányelvek hozzáadása

A kezdéshez használja a következő kódot:

```java
import com.azure.communication.identity.*;
import com.azure.communication.common.*;
import java.io.*;
import java.util.*;
import java.time.*;

import com.azure.core.http.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Példány létrehozása az `CommunicationIdentityClient` erőforráshoz tartozó hozzáférési kulccsal és végponttal. Ismerje meg, hogyan [kezelheti az erőforrás kapcsolódási karakterláncát](../create-communication-resource.md#store-your-connection-string).

Adja hozzá a következő kódot a `main` metódushoz:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
// Use com.azure.core.http.netty.NettyAsyncHttpClientBuilder if that suits your needs
// -> Add "import com.azure.core.http.netty.*;"
// -> Add azure-core-http-netty dependency to file pom.xml

HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Az ügyfelet bármely olyan egyéni HTTP-ügyféllel inicializálhatja, amely megvalósítja a `com.azure.core.http.HttpClient` felületet. A fenti kód azt mutatja be, hogy az [Azure alapszintű](/java/api/overview/azure/core-http-netty-readme?preserve-view=true&view=azure-java-stable) , az által biztosított http-ügyfelet használja `azure-core` .

A teljes kapcsolati karakterláncot a connectionString () függvény használatával is megadhatja a végpont és a hozzáférési kulcs megadása helyett. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="create-an-identity"></a>Identitás létrehozása

Az Azure kommunikációs szolgáltatások egy egyszerűsített identitási könyvtárat tartanak fenn. A `createUser` metódus használatával hozzon létre egy új bejegyzést a címtárban egyedi értékkel `Id` . Tárolja a kapott identitást az alkalmazás felhasználóinak való leképezéssel. Például úgy, hogy az alkalmazás-kiszolgáló adatbázisában tárolja őket. Az identitást később kell megadni a hozzáférési tokenek kiküldéséhez.

```java
CommunicationUser identity = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + identity.getId());
```

## <a name="issue-access-tokens"></a>Hozzáférési tokenek kiadása

A `issueToken` metódus használatával kiállíthat egy hozzáférési jogkivonatot a már meglévő kommunikációs szolgáltatások identitásához. A paraméter olyan `scopes` primitívek készletét határozza meg, amelyek engedélyezik ezt a hozzáférési jogkivonatot. Tekintse meg a [támogatott műveletek listáját](../../concepts/authentication.md). A paraméter új példánya az `user` Azure kommunikációs szolgáltatás identitásának karakterlánc-ábrázolása alapján hozható létre.

```java
// Issue an access token with the "voip" scope for an identity
List<String> scopes = new ArrayList<>(Arrays.asList("voip"));
CommunicationUserToken response = communicationIdentityClient.issueToken(identity, scopes);
OffsetDateTime expiresOn = response.getExpiresOn();
String token = response.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresOn + ": " + token);
```

A hozzáférési jogkivonatok olyan rövid élettartamú hitelesítő adatok, amelyeket újra kell adni. Ha ezt nem teszi meg, az alkalmazás felhasználói élményének megszakadását okozhatja. A `expiresAt` Response tulajdonság a hozzáférési jogkivonat élettartamát jelzi.

## <a name="refresh-access-tokens"></a>Hozzáférési jogkivonatok frissítése

Hozzáférési jogkivonat frissítéséhez használja az objektumot az `CommunicationUser` újrakibocsátáshoz:

```java  
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUser identity = new CommunicationUser(existingIdentity);
response = communicationIdentityClient.issueToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Hozzáférési tokenek visszavonása

Bizonyos esetekben explicit módon visszavonhatja a hozzáférési jogkivonatokat. Például amikor egy alkalmazás felhasználója megváltoztatja a szolgáltatásban való hitelesítéshez használt jelszót. `revokeTokens`A metódus érvényteleníti az összes aktív hozzáférési jogkivonatot, amelyet az identitáshoz adtak ki.

```java  
communicationIdentityClient.revokeTokens(identity, OffsetDateTime.now());
System.out.println("\nSuccessfully revoked all access tokens for identity with ID: " + identity.getId());
```

## <a name="delete-an-identity"></a>Identitás törlése

Az identitás törlése visszavonja az összes aktív hozzáférési jogkivonatot, és megakadályozza, hogy az identitáshoz hozzáférési jogkivonatokat bocsásson ki. Emellett eltávolítja az identitáshoz társított összes megőrzött tartalmat is.

```java
communicationIdentityClient.deleteUser(identity);
System.out.println("\nDeleted the identity with ID: " + identity.getId());
```

## <a name="run-the-code"></a>A kód futtatása

Navigáljon a *pom.xml* fájlt tartalmazó könyvtárra, és fordítsa le a projektet a következő `mvn` parancs használatával.

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
