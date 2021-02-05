---
title: 'Gyors útmutató: felügyeleti csoport létrehozása a .NET Core használatával'
description: Ebben a rövid útmutatóban a .NET Core használatával hozzon létre egy felügyeleti csoportot az erőforrások erőforrás-hierarchiába rendezéséhez.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a74cea9b142785c093b8ed235fc40049746f11a0
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592551"
---
# <a name="quickstart-create-a-management-group-with-net-core"></a>Gyors útmutató: felügyeleti csoport létrehozása a .NET Core használatával

A felügyeleti csoportok olyan tárolók, amelyek segítségével kezelheti a hozzáférést, a szabályzatot és a megfelelőséget több előfizetés között. Hozza létre ezeket a tárolókat egy olyan hatékony és hatékony hierarchia létrehozásához, amely a [Azure Policy](../policy/overview.md) és az [Azure szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md)használható. A felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](overview.md).

A címtárban létrehozott első felügyeleti csoport akár 15 percet is igénybe vehet. Az Azure-ban a címtárhoz a felügyeleti csoportok szolgáltatás beállításához első alkalommal futó folyamatok futnak. A folyamat befejezésekor értesítést kap. További információ: [a felügyeleti csoportok kezdeti beállítása](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

- Egy Azure-szolgáltatásnév, beleértve a _clientId_ és a _clientSecret_. Ha nem rendelkezik a szolgáltatással való használatra Azure Policy vagy újat szeretne létrehozni, tekintse meg a .NET- [hitelesítéshez készült Azure felügyeleti kódtárakat](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ugorja át a lépést a .NET Core-csomagok telepítéséhez, ahogy azt a következő lépésekben tesszük.

- A bérlő bármely Azure AD-felhasználója létrehozhat egy felügyeleti csoportot anélkül, hogy a felügyeleti csoport írási engedélye nincs hozzárendelve ehhez a felhasználóhoz, ha nincs engedélyezve a [hierarchia védelme](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Ez az új felügyeleti csoport a gyökérszintű felügyeleti csoport vagy az [alapértelmezett felügyeleti](./how-to/protect-resource-hierarchy.md#setting---default-management-group) csoport gyermeke lesz, a létrehozó pedig "tulajdonos" szerepkör-hozzárendelést kap. A felügyeleti csoport szolgáltatás lehetővé teszi, hogy a szerepkör-hozzárendelések nem szükségesek a gyökérszintű szinten. A létrehozáskor egyetlen felhasználó sem férhet hozzá a gyökérszintű felügyeleti csoporthoz. Ha el szeretné kerülni, hogy az Azure AD globális rendszergazdái megkeressék a felügyeleti csoportokat, lehetővé tesszük a kezdeti felügyeleti csoportok legfelső szintű létrehozását.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Alkalmazásbeállítás

Ha engedélyezni szeretné a .NET Core-t a felügyeleti csoportok felügyeletéhez, hozzon létre egy új konzolszoftver-alkalmazást, és telepítse a szükséges csomagokat.

1. Győződjön meg arról, hogy a legújabb .NET Core telepítve van (legalább **3.1.8**). Ha még nincs telepítve, töltse le a következő címen: [DotNet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializáljon egy új, "mgCreate" nevű .NET Core Console-alkalmazást:

   ```dotnetcli
   dotnet new console --name "mgCreate"
   ```

1. Módosítsa a címtárakat az új projekt mappájába, és telepítse a Azure Policy szükséges csomagokat:

   ```dotnetcli
   # Add the Azure Policy package for .NET Core
   dotnet add package Microsoft.Azure.Management.ManagementGroups --version 1.1.1-preview

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
   using Microsoft.Azure.Management.ManagementGroups;
   using Microsoft.Azure.Management.ManagementGroups.Models;
   
   namespace mgCreate
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strGroupId = args[3];
               string strDisplayName = args[4];
   
               var authContext = new AuthenticationContext($"https://login.microsoftonline.com/{strTenant}");
               var authResult = await authContext.AcquireTokenAsync(
                      "https://management.core.windows.net",
                      new ClientCredential(strClientId, strClientSecret));
   
               using (var client = new ManagementGroupsAPIClient(new TokenCredentials(authResult.AccessToken)))
               {
                   var mgRequest = new CreateManagementGroupRequest
                   {
                       DisplayName = strDisplayName
                   };
                   var response = await client.ManagementGroups.CreateOrUpdateAsync(strGroupId, mgRequest);
               }
           }
       }
   }
   ```

1. A konzol alkalmazás létrehozása és közzététele `mgCreate` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="create-the-management-group"></a>A felügyeleti csoport létrehozása

Ebben a rövid útmutatóban egy új felügyeleti csoportot hoz létre a gyökérszintű felügyeleti csoportban.

1. Módosítsa a címtárakat az `{run-folder}` előző paranccsal megadott értékre `dotnet publish` .

1. Adja meg a következő parancsot a terminálon:

   ```bash
   mgCreate.exe `
      "{tenantId}" `
      "{clientId}" `
      "{clientSecret}" `
      "{groupID}" `
      "{displayName}"
   ```

Az előző parancsok a következő információkat használják:

- `{tenantId}` – Cserélje le a-t a bérlői AZONOSÍTÓra
- `{clientId}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-azonosítójával.
- `{clientSecret}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-titkos kódjára
- `{groupID}` – Cserélje le az új felügyeleti csoport AZONOSÍTÓját
- `{displayName}` – Cserélje le az új felügyeleti csoport felhasználóbarát nevére

Az eredmény egy új felügyeleti csoport a gyökérszintű felügyeleti csoportban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

- Törölje az új felügyeleti csoportot a portálon keresztül.

- Ha el szeretné távolítani a .NET Core Console alkalmazást és a telepített csomagokat, törölje a `mgCreate` Project mappát.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyeleti csoportot az erőforrás-hierarchia rendszerezéséhez. A felügyeleti csoport rendelkezhet előfizetésekkel vagy más felügyeleti csoportokkal.

Ha többet szeretne megtudni a felügyeleti csoportokról és az erőforrás-hierarchia kezeléséről, folytassa a következővel:

> [!div class="nextstepaction"]
> [Erőforrások kezelése felügyeleti csoportokkal](./manage.md)