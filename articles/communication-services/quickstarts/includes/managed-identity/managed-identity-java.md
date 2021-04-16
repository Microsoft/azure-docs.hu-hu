---
ms.openlocfilehash: b51f52e24ca843abd94a8511e86b3193a797edd5
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510496"
---
## <a name="additional-prerequisites-for-java"></a>A Javára vonatkozó további előfeltételek
Javához a következőre is szüksége lesz:
- [A Java fejlesztői készlet (JDK)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) 8-as vagy újabb verziója.
- [Apache Maven](https://maven.apache.org/download.cgi).

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-java-application"></a>Új Java-alkalmazás létrehozása

Nyissa meg a terminált vagy a parancsablakot. Lépjen arra a könyvtárra, ahol létre szeretné hozni a Java-alkalmazást. Az alábbi parancs futtatásával hozza létre a Java-projektet a maven-archetype-quickstart sablonból.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Figyelje meg, hogy a "létrehozási" feladat létrehozott egy könyvtárat a `artifactId` névvel. Ebben a könyvtárban az src/main/java könyvtár tartalmazza a projekt forráskódját, a tartalmazza a tesztforrást, a fájl pedig a projekt `src/test/java directory` `pom.xml` projektobjektum-modelljét vagy POM-jét.

### <a name="install-the-package"></a>A csomag telepítése

Nyissa meg **pom.xml** fájlt a szövegszerkesztőben. Adja hozzá a következő függőségi elemet a függőségek csoportjához.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0</version>
</dependency>
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="use-the-sdk-packages"></a>Az SDK-csomagok használata

Adja hozzá a következő `import` irányelveket a kódhoz az Azure Identity és az Azure Communication SDK-k használata érdekében.

```java
import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;
```

## <a name="create-a-defaultazurecredential"></a>DefaultAzureCredential létrehozása

Ebben a rövid útmutatóban a [DefaultAzureCredential](/java/api/com.azure.identity.defaultazurecredential) értéket fogjuk használni. Ez a hitelesítő adat éles és fejlesztési környezetekhez megfelelő. Mivel minden művelethez szükség van rá, hozza létre a `App.java` osztályon belül. Adja hozzá a következőt a osztály `App.java` tetejéhez.

```java
private TokenCredential credential = new DefaultAzureCredentialBuilder().build();
```

## <a name="issue-a-token-with-managed-identities"></a>Jogkivonat kiállítása felügyelt identitásokkal

Most hozzáadunk egy kódot, amely a létrehozott hitelesítő adatokat használja a VoIP hozzáférési jogkivonat kiállításához. Ezt a kódot később fogjuk hívni;

```java
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }
```

## <a name="send-an-sms-with-managed-identities"></a>SMS küldése felügyelt identitásokkal

A felügyelt identitások használatának egy másik példájaként hozzáadjuk ezt a kódot, amely ugyanazt a hitelesítő adatokat használja AZ SMS-ek elküldését:

```java
     public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
     }
```
## <a name="write-the-main-method"></a>A Main metódus megírása

A már rendelkezik Egy Main metódussal. Adjunk hozzá néhány kódot, amely a korábban létrehozott kódot hívja meg a felügyelt identitások használatának `App.java` szemlélteti:
```java
    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
```

A `App.java` végeredménynek így kell kinéznie:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.communication.sms.*;
import com.azure.communication.sms.models.*;
import com.azure.core.credential.*;
import com.azure.identity.*;

import java.util.*;

public class App 
{

    private TokenCredential credential = new DefaultAzureCredentialBuilder().build();

    public SmsSendResult sendSms(String endpoint, String from, String to, String message) {
          SmsClient smsClient = new SmsClientBuilder()
               .endpoint(endpoint)
               .credential(this.credential)
               .buildClient();

          // Send the message and check the response for a message id
          return smsClient.send(from, to, message);
    }
    public AccessToken createIdentityAndGetTokenAsync(String endpoint) {
          CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
                    .endpoint(endpoint)
                    .credential(this.credential)
                    .buildClient();

          CommunicationUserIdentifier user = communicationIdentityClient.createUser();
          return communicationIdentityClient.getToken(user, new ArrayList<>(Arrays.asList(CommunicationTokenScope.CHAT)));
    }

    public static void main(String[] args) {
          App instance = new App();
          // You can find your endpoint and access key from your resource in the Azure portal
          // e.g. "https://<RESOURCE_NAME>.communication.azure.com";
          String endpoint = "https://<RESOURCE_NAME>.communication.azure.com/";

          System.out.println("Retrieving new Access Token, using Managed Identities");
          AccessToken token = instance.createIdentityAndGetTokenAsync(endpoint);
          System.out.println("Retrieved Access Token: "+ token.getToken());

          System.out.println("Sending SMS using Managed Identities");
          // You will need a phone number from your resource to send an SMS.
          SmsSendResult result = instance.sendSms(endpoint, "<FROM NUMBER>", "<TO NUMBER>", "Hello from Managed Identities");
          System.out.println("Sms id: "+ result.getMessageId());
          System.out.println("Send Result Successful: "+ result.isSuccessful());
    }
}
```

## <a name="run-the-code"></a>A kód futtatása

Keresse meg apom.xml *fájlt* tartalmazó könyvtárat, és fordítsa le a projektet a következő `mvn` paranccsal.

```console
mvn compile
```

Ezután készítse el a csomagot.

```console
mvn package
```

Futtassa a `mvn` következő parancsot az alkalmazás végrehajtásához.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

A végső kimenetnek az alábbihoz hasonlónak kell lennie:
```
Retrieving new Access Token, using Managed Identities
Retrieved Access Token: ey..A
Sending SMS using Managed Identities
Sms id: Outgoing_202104...33f8ae1f_noam
Send Result Successful: true
```

