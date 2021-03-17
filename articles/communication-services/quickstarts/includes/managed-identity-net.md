---
ms.openlocfilehash: c1b74b43c6ef884c68282dcaaae8dfc9a5541453
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622192"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Felügyelt identitás hozzáadása a kommunikációs szolgáltatások megoldásához (.NET)

### <a name="install-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak telepítése

```console
dotnet add package Azure.Communication.Identity  --version 1.0.0-beta.5
dotnet add package Azure.Communication.Sms  --version 1.0.0-beta.4
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak használata

Adja hozzá a következő `using` irányelveket a kódhoz az Azure Identity és az Azure Storage ügyféloldali kódtárainak használatához.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
using Azure.Core;
```

Az alábbi példák a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)használják. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` és `AZURE_TENANT_ID` környezeti változókra van szükség egy objektum létrehozásához `DefaultAzureCredential` . A regisztrált alkalmazások fejlesztési környezetben való létrehozásához és a környezeti változók beállításához lásd: [hozzáférés engedélyezése felügyelt identitással](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Identitás létrehozása és a jogkivonat kiadása felügyelt identitással

A következő mintakód bemutatja, hogyan hozhat létre Azure Active Directory tokenekkel rendelkező szolgáltatás-ügyfél objektumot.

Ezután használja az ügyfelet egy új felhasználó jogkivonatának kibocsátására:

```csharp
     public Response<AccessToken> CreateIdentityAndGetTokenAsync(Uri resourceEndpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();

          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = client.CreateUser();
          var identity = identityResponse.Value;

          var tokenResponse = client.GetToken(identity, scopes: new[] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>SMS küldése felügyelt identitással

A következő mintakód bemutatja, hogyan hozható létre egy SMS-szolgáltatásbeli ügyfél-objektum felügyelt identitással, majd hogyan küldhet SMS-üzenetet az ügyfélnek:

```csharp
     public SmsSendResult SendSms(Uri resourceEndpoint, string from, string to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          // "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          SmsSendResult sendResult = smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SmsSendOptions(enableDeliveryReport: true) // optional
          );

          return sendResult;
      }
```

