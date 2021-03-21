---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70803006"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Azure Resource Manager jogkivonat beszerzése
A Azure Active Directorynak hitelesítenie kell az erőforrásokon végrehajtott összes feladatot a Azure Resource Manager használatával. Az itt látható példa jelszavas hitelesítést használ, más módszerekkel kapcsolatban lásd: [Azure Resource Manager kérelmek hitelesítése][lnk-authenticate-arm].

1. Adja hozzá a következő kódot a program. cs **Main** metódusához, hogy az alkalmazás-azonosítóval és a jelszóval lekérje az Azure ad-beli tokent.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Hozzon létre egy **ResourceManagementClient** objektumot, amely a tokent használja a következő kód a **Main** metódus végéhez való hozzáadásával:
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Hozzon létre vagy szerezzen be egy hivatkozást a használt erőforráscsoporthoz:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx