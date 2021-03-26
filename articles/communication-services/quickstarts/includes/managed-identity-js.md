---
ms.openlocfilehash: 3ec650f82b95cdfabc923e92325959c63f2846de
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110740"
---
## <a name="add-managed-identity-to-your-communication-services-solution-js"></a>Felügyelt identitás hozzáadása a kommunikációs szolgáltatások megoldásához (JS)

### <a name="install-the-sdk-packages"></a>Az SDK-csomagok telepítése

```console
npm install @azure/communication-identity
npm install @azure/communication-common
npm install @azure/communication-sms
npm install @azure/identity
```

### <a name="use-the-sdk-packages"></a>Az SDK-csomagok használata

Adja hozzá a következő `import` irányelveket a kódjához az Azure Identity és az Azure Storage SDK-k használatához.

```typescript
import { DefaultAzureCredential } from "@azure/identity";
import { CommunicationIdentityClient, CommunicationUserToken } from "@azure/communication-identity";
import { SmsClient, SmsSendRequest } from "@azure/communication-sms";
```

Az alábbi példák a [DefaultAzureCredential](/javascript/api/azure.identity.defaultazurecredential)használják. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez.

Az alkalmazások fejlesztési környezetben való regisztrálásához és környezeti változók beállításához lásd: [hozzáférés engedélyezése felügyelt identitással](../managed-identity-from-cli.md)  

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Identitás létrehozása és a jogkivonat kiadása felügyelt identitással

A következő mintakód bemutatja, hogyan hozhat létre egy felügyelt identitással rendelkező szolgáltatás-ügyfél objektumot, majd az ügyfél használatával kiállítson egy új felhasználót a jogkivonat számára:

```JavaScript
export async function createIdentityAndIssueToken(resourceEndpoint: string): Promise<CommunicationUserToken> {
     let credential = new DefaultAzureCredential();
     const client = new CommunicationIdentityClient(resourceEndpoint, credential);
     return await client.createUserAndToken(["chat"]);
}
```

### <a name="send-an-sms-with-managed-identity"></a>SMS küldése felügyelt identitással

A következő mintakód bemutatja, hogyan hozhat létre egy felügyelt identitással rendelkező szolgáltatás-ügyféloldali objektumot, majd hogyan küldhet SMS-üzenetet az ügyfélnek:

```JavaScript
export async function sendSms(resourceEndpoint: string, fromNumber: any, toNumber: any, message: string) {
     let credential = new DefaultAzureCredential();
     const smsClient = new SmsClient(resourceEndpoint, credential);
     const sendRequest: SmsSendRequest = { 
          from: fromNumber, 
          to: [toNumber], 
          message: message 
     };

     const response = await smsClient.send(
          sendRequest, 
          {} //Optional SendOptions
          );
}
```

