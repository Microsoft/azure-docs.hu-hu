---
ms.openlocfilehash: 2c816f005dea452c3ffa2889aa7d2742a5762759
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657648"
---
## <a name="add-managed-identity-to-your-communication-services-solution-net"></a>Felügyelt identitás hozzáadása a kommunikációs szolgáltatások megoldásához (.net)

### <a name="install-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak telepítése

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak használata

Adja hozzá a következő `using` irányelveket a kódhoz az Azure Identity és az Azure Storage ügyféloldali kódtárainak használatához.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Sms;
```

Az alábbi példák a [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)használják. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez.

`AZURE_CLIENT_SECRET`, `AZURE_CLIENT_ID` és `AZURE_TENANT_ID` környezeti változókra van szükség egy objektum létrehozásához `DefaultAzureCredential` . A regisztrált alkalmazások fejlesztési környezetben való létrehozásához és a környezeti változók beállításához lásd: [hozzáférés engedélyezése felügyelt identitással](../managed-identity-from-cli.md).

### <a name="create-an-identity-and-issue-a-token-with-managed-identity"></a>Identitás létrehozása és a jogkivonat kiadása felügyelt identitással

A következő mintakód bemutatja, hogyan hozhat létre Azure Active Directory tokenekkel rendelkező szolgáltatás-ügyfél objektumot.

Ezután használja az ügyfelet egy új felhasználó jogkivonatának kibocsátására:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndGetTokenAsync(Uri resourceEdnpoint)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();

          var tokenResponse = await client.GetTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-managed-identity"></a>SMS küldése felügyelt identitással

A következő mintakód bemutatja, hogyan hozható létre egy SMS-szolgáltatásbeli ügyfél-objektum felügyelt identitással, majd hogyan küldhet SMS-üzenetet az ügyfélnek:

```csharp
     public async Task SendSms(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
          // You can find your endpoint and access key from your resource in the Azure portal
          String resourceEndpoint = "https://<RESOURCE_NAME>.communication.azure.com";

          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A hitelesítés ismertetése](../concepts/authentication.md)

A következőket is érdemes elvégezheti:

- [További információ az Azure szerepköralapú hozzáférés-vezérléséről](../../../../articles/role-based-access-control/index.yml)
- [További információ a .NET-hez készült Azure Identity Library-ről](/dotnet/api/overview/azure/identity-readme)
- [Felhasználói hozzáférési tokenek létrehozása](../../quickstarts/access-tokens.md)
- [SMS küldése](../telephony-sms/send.md)
- [További információ az SMS-ről](../../concepts/telephony-sms/concepts.md)
