---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: 443595a52458d7ff7c168f4c120257cfb60fad2e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110889"
---
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/dotnet/).
- Az operációs rendszer legújabb verziója [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core) .
- Szerezze be a [.net Identity SDK](/dotnet/api/azure.identity)legújabb verzióját.
- Szerezze be a [.net Management SDK](../../concepts/sdk-options.md)legújabb verzióját.

## <a name="installing-the-sdk"></a>Az SDK telepítése

Először is vegye fel a kommunikációs szolgáltatások Management SDK-t a C#-projektbe:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>Előfizetés azonosítója

Ismernie kell az Azure-előfizetés AZONOSÍTÓját. Ezt a portálról lehet beszerezni:

1.  Jelentkezzen be az Azure-fiókjába
2.  A bal oldali oldalsávon válassza az előfizetések lehetőséget.
3.  Válassza ki, hogy melyik előfizetésre van szükség
4.  Kattintson az Áttekintés gombra
5.  Előfizetés-azonosító kiválasztása

Ebben a rövid útmutatóban feltételezzük, hogy az előfizetés-azonosítót egy nevű környezeti változóban tárolta `AZURE_SUBSCRIPTION_ID` .

## <a name="authentication"></a>Hitelesítés

Az Azure kommunikációs szolgáltatásokkal való kommunikációhoz először hitelesítenie kell magát az Azure-ban. Ezt általában egyszerű szolgáltatásnév használatával hajtja végre.

### <a name="option-1-managed-identity"></a>1. lehetőség: felügyelt identitás

Ha a kód Azure-szolgáltatásként fut, a hitelesítés legegyszerűbb módja, ha felügyelt identitást szeretne beszerezni az Azure-ból. További információ a [felügyelt identitásokról](../../../active-directory/managed-identities-azure-resources/overview.md).

[Felügyelt identitásokat támogató Azure-szolgáltatások](../../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

[Felügyelt identitások használata App Service és Azure Functions](../../../app-service/overview-managed-identity.md?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Rendszer által hozzárendelt felügyelt identitás](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Felhasználó által hozzárendelt felügyelt identitás](../../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity)

A létrehozott felügyelt identitás ClientId explicit módon át kell adni `ManagedIdentityCredential` .

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>2. lehetőség: egyszerű szolgáltatásnév

Felügyelt identitás használata helyett érdemes lehet az Azure-ban hitelesíteni az Ön által felügyelt egyszerű szolgáltatásnév használatával. További tudnivalókat az [egyszerű szolgáltatásnév létrehozásával és kezelésével](../../../active-directory/develop/howto-create-service-principal-portal.md)kapcsolatos dokumentációban talál Azure Active Directory.

Miután létrehozta a szolgáltatásnevet, a következő információkat kell összegyűjtenie a Azure Portalből:

- **Ügyfél-azonosító**
- **Ügyfél titka**
- **Bérlőazonosító**

Tárolja ezeket az értékeket a (z) és a (z) és a (z) nevű környezeti változókban `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET` `AZURE_TENANT_ID` Ezután a következőhöz hasonló kommunikációs szolgáltatások felügyeleti ügyfelet hozhat létre:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>3. lehetőség: felhasználói identitás

Ha az Azure-t egy interaktív felhasználó nevében szeretné hívni, és nem a szolgáltatás identitását használja, az alábbi kód használatával hozhat létre Azure Communication Services Felügyeleti ügyfelet. Ekkor megnyílik egy böngészőablak, amely felszólítja a felhasználót a MSA vagy az Azure AD-beli hitelesítő adataik megadására.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>A kommunikációs szolgáltatások erőforrásainak kezelése

### <a name="interacting-with-azure-resources"></a>Interakció az Azure-erőforrásokkal

Most, hogy hitelesítve van, használhatja a felügyeleti ügyfelet, hogy API-hívásokat hajtson végre.

A következő példák mindegyike esetében a kommunikációs szolgáltatások erőforrásai egy meglévő erőforráscsoporthoz vannak rendelve.

Ha létre kell hoznia egy erőforráscsoportot, ezt a [Azure Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md) vagy a [Azure Resource Manager SDK](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md)használatával teheti meg.

### <a name="create-and-manage-a-communication-services-resource"></a>Kommunikációs szolgáltatások erőforrásának létrehozása és kezelése

A Communication Services Management SDK-ügyfél () példánya a ``Azure.ResourceManager.Communication.CommunicationManagementClient`` kommunikációs szolgáltatások erőforrásain végzett műveletek végrehajtásához használható.

#### <a name="create-a-communication-services-resource"></a>Communication Services-erőforrás létrehozása

A kommunikációs szolgáltatások erőforrásának létrehozásakor meg kell adnia az erőforráscsoport nevét és az erőforrás nevét. Vegye figyelembe, hogy a `Location` tulajdonság mindig lesz `global` , és a nyilvános előzetes verzióban az `DataLocation` értéknek kell lennie `UnitedStates` .

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Kommunikációs szolgáltatások erőforrásának frissítése

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>A kommunikációs szolgáltatások összes erőforrásának listázása

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Kommunikációs szolgáltatások erőforrásának törlése

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Kulcsok és a kapcsolatok karakterláncának kezelése

Minden kommunikációs szolgáltatás erőforráshoz tartozik egy pár hozzáférési kulcs és a megfelelő kapcsolati karakterláncok. Ezek a kulcsok elérhetők a Management SDK-val, majd más kommunikációs szolgáltatások SDK-k használatával hitelesítik magukat az Azure kommunikációs szolgáltatásokban.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Kommunikációs szolgáltatások erőforrásaihoz tartozó hozzáférési kulcsok beolvasása

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Kommunikációs szolgáltatások erőforrásához tartozó hozzáférési kulcs újralétrehozása

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```