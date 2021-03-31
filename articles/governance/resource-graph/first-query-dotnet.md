---
title: 'Rövid útmutató: az első .NET Core-lekérdezés'
description: Ebben a rövid útmutatóban a következő lépésekkel engedélyezheti a .NET Core-hoz készült Resource Graph NuGet-csomagokat, és futtathatja első lekérdezését.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917571"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>Rövid útmutató: az első Resource Graph-lekérdezés futtatása a .NET Core használatával

Az Azure Resource Graph használatának első lépése, hogy meggyőződjön arról, hogy a .NET Core szükséges csomagjai telepítve vannak. Ez a rövid útmutató végigvezeti a csomagok .NET Core-telepítéshez való hozzáadásának folyamatán.

A folyamat végén hozzáadta a csomagokat a .NET Core-telepítéshez, és futtatja az első Resource Graph-lekérdezést.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- Egy Azure-szolgáltatásnév, beleértve a _clientId_ és a _clientSecret_. Ha nem rendelkezik az erőforrás-Gráfmal való használatra, vagy újat szeretne létrehozni, tekintse meg [Az Azure felügyeleti kódtárak a .net-hitelesítéshez](/dotnet/azure/sdk/authentication#mgmt-auth)című témakört.
  Ugorja át a lépést a .NET Core-csomagok telepítéséhez, ahogy azt a következő lépésekben tesszük.

## <a name="create-the-resource-graph-project"></a>Az erőforrás-gráf projekt létrehozása

Ha engedélyezni szeretné a .NET Core-t az Azure Resource Graph lekérdezéséhez, hozzon létre egy új Console-alkalmazást, és telepítse a szükséges csomagokat.

1. Győződjön meg arról, hogy a legújabb .NET Core telepítve van (legalább **3.1.5**). Ha még nincs telepítve, töltse le a következő címen: [DotNet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core).

1. Inicializáljon egy új, "argQuery" nevű .NET Core Console-alkalmazást:

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. Módosítsa a címtárakat az új projekt mappájába, és telepítse az Azure Resource Graph szükséges csomagjait:

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

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
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. A konzol alkalmazás létrehozása és közzététele `argQuery` :

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>Az első Resource Graph-lekérdezés futtatása

A beépített és közzétett .NET Core Console-alkalmazás ideje, hogy kipróbáljon egy egyszerű Resource Graph-lekérdezést. A lekérdezés az első öt Azure-erőforrást adja vissza az egyes erőforrások **nevével** és **erőforrás-típusával** .

Minden egyes hívásban olyan `argQuery` változót használunk, amelyet a saját értékeivel kell helyettesíteni:

- `{tenantId}` – Cserélje le a-t a bérlői AZONOSÍTÓra
- `{clientId}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-azonosítójával.
- `{clientSecret}` – Cserélje le a szolgáltatást a szolgáltatásnév ügyfél-titkos kódjára
- `{subscriptionId}` – Cserélje le az előfizetése azonosítójára

1. Módosítsa a címtárakat az `{run-folder}` előző paranccsal megadott értékre `dotnet publish` .

1. Futtassa az első Azure Resource Graph-lekérdezést a lefordított .NET Core Console-alkalmazás használatával:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > Ez a lekérdezési példa nem biztosít olyan rendezési módosítót, mint az `order by`, tehát ha többször is futtatja, valószínűleg minden kéréssel eltérő erőforráslistát fog kapni.

1. Módosítsa a végső paramétert, `argQuery.exe` és módosítsa a lekérdezést `order by` a **Name (név** ) tulajdonságra:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > Csakúgy, mint az első lekérdezésnél, e lekérdezés többszöri futtatása esetén is valószínűleg minden kéréssel eltérő erőforráslistát fog kapni. Fontos a lekérdezési parancsok sorrendje. Ebben a példában az `order by` a `limit` után következik. Ez a parancs először a lekérdezés eredményeit korlátozza, majd megrendeli azokat.

1. Módosítsa a végső paramétert, `argQuery.exe` és módosítsa a lekérdezést a `order by` **Name (név** ) tulajdonságra, majd az első `limit` öt találatra:

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

Ha a végső lekérdezés többször is fut, feltételezve, hogy a környezetében semmi sem változik, a visszaadott eredmények konzisztensek és a **Name** tulajdonság szerint vannak rendezve, de továbbra is az első öt találatra korlátozódnak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha el szeretné távolítani a .NET Core Console alkalmazást és a telepített csomagokat, ezt a Project mappa törlésével teheti meg `argQuery` .

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy .NET Core Console-alkalmazást a szükséges Resource Graph-csomagokkal, és futtatta az első lekérdezést. Ha többet szeretne megtudni az erőforrás-gráf nyelvéről, folytassa a lekérdezés nyelvének részletei lapon.

> [!div class="nextstepaction"]
> [További információ a lekérdezési nyelvről](./concepts/query-language.md)