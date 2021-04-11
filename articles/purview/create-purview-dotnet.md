---
title: Hatáskörébe tartozó fiók létrehozása a .NET SDK használatával
description: Hozzon létre egy Azure-beli hatáskörébe tartozó fiókot a .NET SDK használatával.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: 04ed5cef351c81355a2390dd0b983c162f2b9532
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387482"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Gyors útmutató: a .NET SDK használatával hozzon létre egy hatáskörébe tartozó fiókot

Ez a rövid útmutató azt ismerteti, hogyan használható a .NET SDK egy Azure-beli hatáskörébe-fiók létrehozásához 

> [!IMPORTANT]
> Az Azure-beli hatáskörébe jelenleg előzetes verzió érhető el. A [Microsoft Azure előzetes verzióinak kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) közé tartoznak azok a további jogi feltételek, amelyek az Azure olyan szolgáltatásaira vonatkoznak, amelyek béta-, előzetes verziójú vagy egyéb módon még nem lettek nyilvánosan elérhetők.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A fióknak engedéllyel kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre az előfizetésben

* Ha **Azure Policy** blokkolja az összes alkalmazást a Storage- **fiók** és a **EventHub-névtér** létrehozásához, akkor a címkével kell megadnia a szabályzatot, amely a hatáskörébe tartozó fiók létrehozásának folyamata során megadható. Ennek fő oka, hogy minden létrehozott felügyeleti fiókhoz létre kell hoznia egy felügyelt erőforráscsoportot, és ezen az erőforráscsoport, egy Storage-fiók és egy EventHub-névtér között. További információt a katalógus- [portál létrehozása](create-catalog-portal.md) című témakörben talál.

### <a name="visual-studio"></a>Visual Studio

A jelen cikkben található útmutató a Visual Studio 2019-et használja. A Visual Studio 2013, 2015 vagy 2017 eljárása némileg eltér.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Töltse le és telepítse az [Azure .NET SDK](https://azure.microsoft.com/downloads/)-t a gépen.

## <a name="create-an-application-in-azure-active-directory"></a>Alkalmazás létrehozása az Azure Active Directoryban

A How to: című részből megtudhatja, *hogyan hozhat létre az erőforrásokhoz hozzáférő Azure ad-alkalmazást és egyszerű szolgáltatást*, a feladatok végrehajtásához kövesse az alábbi utasításokat:

1. A [Azure Active Directory alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)területen hozzon létre egy alkalmazást, amely az oktatóanyagban létrehozott .NET-alkalmazást képviseli. A bejelentkezési URL-hez megadhat egy hamis URL-t, a cikkben láthatóak szerint (`https://contoso.org/exampleapp`).
2. A beléptetési [értékek beolvasása](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)területen szerezze be az **alkalmazás azonosítóját** és a **bérlő azonosítóját**, és jegyezze fel ezeket az értékeket, amelyeket az oktatóanyag későbbi részében használ. 
3. A [tanúsítványok és titkok](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)területen szerezze be a **hitelesítési kulcsot**, és jegyezze fel ezt az értéket, amelyet az oktatóanyag későbbi részében használ.
4. Az [alkalmazás szerepkörhöz való hozzárendeléséhez](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)rendelje hozzá az alkalmazást a **közreműködő** szerepkörhöz az előfizetés szintjén, hogy az alkalmazás létre tudja hozni az adat-előállítókat az előfizetésben.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Következő lépésként hozzon létre egy C# .NET-konzol alkalmazást a Visual Studióban:

1. Indítsa el a **Visual studiót**.
2. A Start ablakban válassza az **új Project**  >  **Console-alkalmazás létrehozása (.NET-keretrendszer)** lehetőséget. A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
3. A **Project Name (projekt neve**) mezőben adja meg a **ADFv2QuickStart**.
4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Válassza az **Eszközök** > **NuGet-csomagkezelő** > **Package Csomagkezelő konzol** elemet.
2. A **Package Manager konzol** ablaktábláján futtassa a következő parancsokat a csomagok telepítéséhez. További információ: [Microsoft. Azure. Management. hatáskörébe NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/).

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>A hatáskörébe tartozó ügyfél létrehozása

1. Nyissa meg a **Program.cs** fájlt, majd illessze be az alábbi utasításokat, hogy a névterekre mutató hivatkozásokat tudjon felvenni.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
      using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.Purview;
      using Microsoft.Azure.Management.Purview.Models;
      using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adja hozzá az alábbi kódot a **Main** metódushoz, amely beállítja a változókat. Cserélje le a helyőrzőket a saját értékeire. Azon Azure-régiók listáját, amelyekben a hatáskörébe jelenleg elérhető, keressen rá az **Azure hatáskörébe** , és válassza ki a régiókat, amelyek érdeklik Önt a következő oldalon: az [elérhető termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/).

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string purviewAccountName = 
       "<specify the name of purview account to create. It must be globally unique.>";
   ```

3. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **PurviewManagementClient** osztály egy példányát. Ezzel az objektummal hozzon létre egy hatáskörébe tartozó fiókot.

   ```csharp
   // Authenticate and create a purview management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
   "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new PurviewManagementClient(cred)
   {
      SubscriptionId = subscriptionId           
   };
   ```

## <a name="create-a-purview-account"></a>Hatáskörébe tartozó fiók létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **hatáskörébe tartozó fiókot**. 

```csharp
    // Create a purview Account
    Console.WriteLine("Creating Purview Account " + purviewAccountName + "...");
    Account account = new Account()
    {
    Location = region,
    Identity = new Identity(type: "SystemAssigned"),
    Sku = new AccountSku(name: "Standard", capacity: 4)
    };            
    try
    {
      client.Accounts.CreateOrUpdate(resourceGroup, purviewAccountName, account);
      Console.WriteLine(client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState);                
    }
    catch (ErrorResponseModelException purviewException)
    {
      Console.WriteLine(purviewException.StackTrace);
    }
    Console.WriteLine(
      SafeJsonConvert.SerializeObject(account, client.SerializationSettings));
    while (client.Accounts.Get(resourceGroup, purviewAccountName).ProvisioningState ==
           "PendingCreation")
    {
      System.Threading.Thread.Sleep(1000);
    }
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
```

## <a name="run-the-code"></a>A kód futtatása

Hozza létre és indítsa el az alkalmazást, majd ellenőrizze a végrehajtást.

A konzol kinyomtatja a hatáskörébe tartozó fiók létrehozásának folyamatát.

### <a name="sample-output"></a>Példakimenet

```json
Creating Purview Account testpurview...
Succeeded
{
  "sku": {
    "capacity": 4,
    "name": "Standard"
  },
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "southcentralus"
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Kimenet ellenőrzése

Lépjen a Azure Portal **hatáskörébe tartozó fiókok** lapra, [](https://portal.azure.com) és ellenőrizze a fenti kóddal létrehozott fiókot. 

## <a name="delete-purview-account"></a>A hatáskörébe tartozó fiók törlése

A hatáskörébe tartozó fiókok programozott törléséhez adja hozzá a következő sornyi kódot a programhoz: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Ellenőrizze, hogy elérhető-e a hatáskörébe tartozó fióknév

A hatáskörébe tartozó fiókok rendelkezésre állásának vizsgálatához használja a következő kódot: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

A fenti kód "true" (igaz) nyomtatásával, ha a név elérhető, és "false", ha a név nem érhető el.


## <a name="next-steps"></a>Következő lépések

Az oktatóanyagban szereplő kód egy hatáskörébe tartozó fiókot hoz létre, törli a hatáskörébe tartozó fiókot, és ellenőrzi a hatáskörébe tartozó fiók nevének rendelkezésre állását. Most már letöltheti a .NET SDK-t, és megismerheti a hatáskörébe tartozó fiókokhoz elvégezhető egyéb erőforrás-szolgáltatói műveleteket.

A következő cikkből megtudhatja, hogyan engedélyezheti a felhasználók számára az Azure hatáskörébe tartozó fiók elérését. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure-beli hatáskörébe-fiókhoz](catalog-permissions.md)
