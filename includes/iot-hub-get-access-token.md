---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: b6ea8c7b3a6374572c8bd31e3c62b788efbafcbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156313"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Egy Azure Resource Manager-jogkivonat beszerzése
Az Azure Active Directory hitelesítést kell végeznie az Azure Resource Manager-erőforrásokon végrehajtott összes feladatot. Az itt látható példában jelszó-hitelesítést használ, további egyéb megközelítések: [hitelesítése az Azure Resource Manager-kérelmek][lnk-authenticate-arm].

1. Adja hozzá a következő kódot a **fő** metódus a program.cs fájlban egy token lekérése az Azure AD-ba, az alkalmazás azonosítóját és jelszavát.
   
    ```
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
2. Hozzon létre egy **ResourceManagementClient** objektum, amely használja a jogkivonatot a következő kód hozzáadásával végéhez a **fő** módszer:
   
    ```
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Hozzon létre, vagy szerezzen be egy hivatkozást, az erőforráscsoportot használja:
   
    ```
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx