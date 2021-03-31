---
title: 'Gyors útmutató: új szabályzat-hozzárendelés a .NET Core használatával'
description: Ebben a rövid útmutatóban a .NET Core használatával hozzon létre egy Azure Policy-hozzárendelést a nem megfelelő erőforrások azonosításához.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c4c8f8e9df544b6fc00b5b7701435f5a606f9764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91604679"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-net-core"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a .NET Core használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása. Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre a felügyelt lemezeket nem használó virtuális gépek azonosításához. Ha elkészült, azonosíthatja azokat a virtuális gépeket, amelyek _nem megfelelőek_.

A .NET Core Library az Azure-erőforrások kezelésére szolgál. Ez az útmutató ismerteti, hogyan használható a .NET Core Library for Azure Policy egy szabályzat-hozzárendelés létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- Egy Azure-szolgáltatásnév, beleértve a _clientId_ és a _clientSecret_. Ha nem rendelkezik a szolgáltatással való használatra Azure Policy vagy újat szeretne létrehozni, tekintse meg a .NET- [hitelesítéshez készült Azure felügyeleti kódtárakat](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ugorja át a lépést a .NET Core-csomagok telepítéséhez, ahogy azt a következő lépésekben tesszük.

## <a name="create-the-azure-policy-project"></a>A Azure Policy projekt létrehozása

Ha engedélyezni szeretné a .NET Core-t a Azure Policy felügyeletéhez, hozzon létre egy új Console-alkalmazást, és telepítse a szükséges csomagokat.

1. Győződjön meg arról, hogy a legújabb .NET Core telepítve van (legalább **3.1.8**). Ha még nincs telepítve, töltse le a következő címen: [DotNet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializáljon egy új, "policyAssignment" nevű .NET Core Console-alkalmazást:

   ```dotnetcli
   dotnet new console --name "policyAssignment"
   ```

1. Módosítsa a címtárakat az új projekt mappájába, és telepítse a Azure Policy szükséges csomagokat:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceManager --version 3.10.0-preview

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Cserélje le az alapértelmezett értéket `program.cs` a következő kódra, és mentse a frissített fájlt:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceManager;
   using Microsoft.Azure.Management.ResourceManager.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
               string strDisplayName = args[5];
               string strPolicyDefID = args[6];
               string strDescription = args[7];
               string strScope = args[8];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));

               using (var client = new PolicyClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyAssignment = new PolicyAssignment
                   {
                       DisplayName = strDisplayName,
                       PolicyDefinitionId = strPolicyDefID,
                       Description = strDescription
                   };
                   var response = await client.PolicyAssignments.CreateAsync(strScope, strName, policyAssignment);
               }
           }
       }
   }
   ```

1. A konzol alkalmazás létrehozása és közzététele `policyAssignment` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-a-policy-assignment"></a>Szabályzat-hozzárendelés létrehozása

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és hozzárendeli **azokat a naplózási virtuális gépeket, amelyek nem használnak Managed Disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ) definíciót. Ez a szabályzat-definíció olyan erőforrásokat azonosít, amelyek nem felelnek meg a szabályzat-definícióban meghatározott feltételeknek.

1. Módosítsa a címtárakat az `{run-folder}` előző paranccsal megadott értékre `dotnet publish` .

1. Adja meg a következő parancsot a terminálon:

   ```bash
   policyAssignment.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks" `
      "Audit VMs without managed disks Assignment" `
      "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      "Shows all virtual machines not using managed disks" `
      "{scope}"
   ```

Az előző parancsok a következő információkat használják:

- `{tenantId}` – Cserélje le a-t a bérlői AZONOSÍTÓra
- `{clientId}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-azonosítójával.
- `{clientSecret}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-titkos kódjára
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára
- **név** – a házirend-hozzárendelési objektum egyedi neve. A fenti példa a _manageddisks-_ t használja.
- **DisplayName** – a szabályzat-hozzárendelés megjelenítendő neve. Ebben az esetben a _virtuális gépek naplózása felügyelt lemezek hozzárendelése nélkül_ történik.
- **policyDefID** – a házirend-definíció elérési útja, amely alapján létrehozza a hozzárendelést. Ebben az esetben ez a házirend-definíciós _virtuális gépek azonosítója, amelyek nem használnak felügyelt lemezeket_.
- **description (Leírás** ) – mélyebb magyarázat arról, hogy mit tesz a szabályzat, vagy miért van hozzárendelve ehhez a hatókörhöz.
- **hatókör** – a hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrásokat vagy erőforrásokat kíván érvényesíteni. Egy felügyeleti csoportból egy adott erőforrásra terjedhet. Ügyeljen arra, hogy a `{scope}` következő minták egyikét cserélje le:
  - Felügyeleti csoport: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Előfizetés: `/subscriptions/{subscriptionId}`
  - Erőforráscsoport: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Erőforrás `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

Most már készen áll a nem megfelelő erőforrások azonosítására a környezet megfelelőségi állapotának megismerése érdekében.

## <a name="identify-non-compliant-resources"></a>A nem megfelelő erőforrások azonosítása

Most, hogy létrehozta a szabályzat-hozzárendelést, azonosíthatja azokat az erőforrásokat, amelyek nem megfelelőek.

1. Inicializáljon egy új, "policyCompliance" nevű .NET Core Console-alkalmazást:

   ```dotnetcli
   dotnet new console --name "policyCompliance"
   ```

1. Módosítsa a címtárakat az új projekt mappájába, és telepítse a Azure Policy szükséges csomagokat:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.PolicyInsights --version 3.1.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. Cserélje le az alapértelmezett értéket `program.cs` a következő kódra, és mentse a frissített fájlt:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.PolicyInsights;
   using Microsoft.Azure.Management.PolicyInsights.Models;
   
   namespace policyAssignment
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                   "https://management.core.windows.net",
                   new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new PolicyInsightsClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var policyQueryOptions = new QueryOptions
                   {
                       Filter = $"IsCompliant eq false and PolicyAssignmentId eq '{strName}'",
                       Apply = "groupby(ResourceId)"
                   };
   
                   var response = await client.PolicyStates.ListQueryResultsForSubscriptionAsync(
                       "latest", strSubscriptionId, policyQueryOptions);
                   Console.WriteLine(response.Odatacount);
               }
           }
       }
   }
   ```

1. A konzol alkalmazás létrehozása és közzététele `policyAssignment` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

1. Módosítsa a címtárakat az `{run-folder}` előző paranccsal megadott értékre `dotnet publish` .

1. Adja meg a következő parancsot a terminálon:

   ```bash
   policyCompliance.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{subscriptionId}" `
      "audit-vm-manageddisks"
   ```

Az előző parancsok a következő információkat használják:

- `{tenantId}` – Cserélje le a-t a bérlői AZONOSÍTÓra
- `{clientId}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-azonosítójával.
- `{clientSecret}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-titkos kódjára
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára
- **név** – a házirend-hozzárendelési objektum egyedi neve. A fenti példa a _manageddisks-_ t használja.

Az eredmények `response` megegyeznek a szabályzat-hozzárendelés **erőforrás-megfelelőség** lapján a Azure Portal nézetben.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

- Törölje a házirend-hozzárendelési _naplózási virtuális gépeket a felügyelt lemezek nélkül_ , a portálon keresztül. A házirend-definíció beépített, így nem lehet eltávolítani a definíciót.

- Ha el szeretné távolítani a .NET Core-konzol alkalmazásait és a telepített csomagokat, törölje a `policyAssignment` és a `policyCompliance` projekt mappáját.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzárendelt egy szabályzatdefiníciót az Azure-környezetben megtalálható, nem megfelelő erőforrások azonosítása céljából.

Ha többet szeretne megtudni a szabályzat-definíciók hozzárendeléséről, hogy ellenőrizze, hogy az új erőforrások megfelelőek-e, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)
