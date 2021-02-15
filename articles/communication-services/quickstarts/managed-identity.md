---
title: Felügyelt identitások használata a kommunikációs szolgáltatásokban (.NET)
titleSuffix: An Azure Communication Services quickstart
description: A felügyelt identitások lehetővé teszik az Azure kommunikációs szolgáltatások elérésének engedélyezését az Azure-beli virtuális gépeken, a Function apps-ben és más erőforrásokon futó alkalmazásokból.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: 9fd8a17deeb49d836ff5902042bdb88696e29f31
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417871"
---
# <a name="use-managed-identities-net"></a>Felügyelt identitások használata (.NET)

Az Azure kommunikációs szolgáltatások használatának első lépései a .NET-ben felügyelt identitások használatával. A kommunikációs szolgáltatások felügyelete és az SMS ügyféloldali kódtárak támogatják a Azure Active Directory (Azure AD) hitelesítést az [Azure-erőforrások felügyelt identitásával](../../active-directory/managed-identities-azure-resources/overview.md).

Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti a hozzáférést a felügyelt identitásokat támogató Azure-környezettől az adminisztrációs és SMS-alapú ügyféloldali kódtárak számára. Azt is leírja, hogyan tesztelheti a kódot egy fejlesztési környezetben.

## <a name="prerequisites"></a>Előfeltételek

 - Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free)
 - Aktív kommunikációs szolgáltatások erőforrás-és kapcsolati karakterlánca. [Hozzon létre egy kommunikációs szolgáltatások erőforrást](https://docs.microsoft.com/azure/communication-services/quickstarts/create-communication-resource?tabs=windows&pivots=platform-azp).

## <a name="setting-up"></a>Beállítás

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Felügyelt identitások engedélyezése virtuális gépen vagy app Service-ben

A felügyelt identitásokat engedélyezni kell az Ön által engedélyezett Azure-erőforrásokon. Ha szeretné megtudni, hogyan engedélyezheti az Azure-erőforrások felügyelt identitásait, tekintse meg az alábbi cikkek egyikét:

- [Azure Portalra](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sablon](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager ügyféloldali kódtárak](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Azure-szerepkörök kiosztása a Azure Portal

1. Lépjen az Azure Portalra.
1. Navigáljon az Azure kommunikációs szolgáltatás erőforrásához.
1. Navigáljon a Access Control (IAM) menüre – > + Add-> szerepkör-hozzárendelés hozzáadása.
1. Válassza ki a "közreműködő" szerepkört (ez az egyetlen támogatott szerepkör).
1. Válassza ki a "felhasználóhoz rendelt felügyelt identitás" elemet (vagy egy "rendszerhez rendelt felügyelt identitást"), majd válassza ki a kívánt identitást. Mentse a kijelölést.

![Felügyelt identitási szerepkör](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Azure-szerepkörök kiosztása a PowerShell-lel

Szerepkörök és engedélyek a PowerShell használatával történő hozzárendeléséhez lásd: [Azure szerepkör-hozzárendelések hozzáadása vagy eltávolítása Azure PowerShell használatával](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Felügyelt identitás hozzáadása a kommunikációs szolgáltatások megoldásához

### <a name="install-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak telepítése

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Az ügyféloldali kódtár csomagjainak használata

Adja hozzá a következő `using` irányelveket a kódhoz az Azure Identity és az Azure Storage ügyféloldali kódtárainak használatához.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Az alábbi példák a [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential)használják. Ez a hitelesítő adat megfelelő az éles és a fejlesztési környezetekhez.

### <a name="create-an-identity-and-issue-a-token"></a>Identitás létrehozása és jogkivonat kiadása

A következő mintakód bemutatja, hogyan hozhat létre Azure Active Directory-tokenekkel rendelkező szolgáltatás-ügyfél objektumot, majd az ügyfél használatával kiállíthatja a tokent egy új felhasználó számára:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEdnpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>SMS küldése Azure Active Directory tokenekkel

A következő mintakód bemutatja, hogyan hozhat létre Azure Active Directory tokenekkel rendelkező szolgáltatás-ügyfél objektumot, majd az ügyfél használatával SMS-üzenetet küldhet:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
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

- [További információ az Azure szerepköralapú hozzáférés-vezérléséről](../../../articles/role-based-access-control/index.yml)
- [További információ a .NET-hez készült Azure Identity Library-ről](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)
- [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)
- [SMS küldése](../quickstarts/telephony-sms/send.md)
- [További információ az SMS-ről](../concepts/telephony-sms/concepts.md)
