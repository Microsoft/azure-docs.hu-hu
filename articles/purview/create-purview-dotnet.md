---
title: Purview-fiók létrehozása a .NET SDK használatával
description: Hozzon létre egy Azure Purview-fiókot a .NET SDK használatával.
author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 4/2/2021
ms.author: nayenama
ms.openlocfilehash: b3dc7bf8ac7650a7219c15a09a31d4dcf84a40bf
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587799"
---
# <a name="quickstart-create-a-purview-account-using-net-sdk"></a>Rövid útmutató: Purview-fiók létrehozása a .NET SDK használatával

Ez a rövid útmutató azt ismerteti, hogyan használható a .NET SDK egy Azure Purview-fiók létrehozására 

> [!IMPORTANT]
> Az Azure Purview jelenleg előzetes verzióban érhető el. A [Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziók kiegészítő használati feltételei további jogi feltételeket tartalmaznak, amelyek a bétaverzióban, előzetes verzióban vagy más, általánosan még nem elérhető Azure-funkciókra vonatkoznak.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Saját [Azure Active Directory-bérlő](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* A fióknak engedéllyel kell rendelkeznie erőforrások létrehozásához az előfizetésben

* Ha nem **Azure Policy** az összes alkalmazás számára a **Storage-fiók** és az **EventHub-névtér** létrehozását, szabályzati kivételt kell létrehoznia a címke használatával, amelyet a Purview-fiók létrehozása során lehet megadni. Ennek fő oka az, hogy minden létrehozott Purview-fiókhoz létre kell hoznia egy felügyelt erőforráscsoportot, valamint ezen az erőforráscsoporton belül egy Storage-fiókot és egy EventHub-névteret. További információ: [Katalógusportál létrehozása](create-catalog-portal.md)

### <a name="visual-studio"></a>Visual Studio

A cikk útmutatója a 2019 Visual Studio t használja. A 2013 Visual Studio 2015-ös vagy 2017-es év eljárásai némileg eltérnek.

### <a name="azure-net-sdk"></a>Azure .NET SDK

Töltse le és telepítse az [Azure .NET SDK](https://azure.microsoft.com/downloads/)-t a gépen.

## <a name="create-an-application-in-azure-active-directory"></a>Alkalmazás létrehozása az Azure Active Directoryban

A Útmutató: A portál használata erőforrásokhoz hozzáférő *Azure AD-alkalmazás* és -szolgáltatásnév létrehozásához szakaszból kövesse az alábbi feladatok elvégzéséhez szükséges utasításokat:

1. A Create an Azure Active Directory application (Új alkalmazás [létrehozása) lehetőségben](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)hozzon létre egy alkalmazást, amely az oktatóanyagban létrehozott .NET-alkalmazást képviseli. A bejelentkezési URL-hez megadhat egy hamis URL-t, a cikkben láthatóak szerint (`https://contoso.org/exampleapp`).
2. A [Get values for signing in](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)(Értékek lekérte a bejelentkezéshez) mezőben szerezze be az **alkalmazás-azonosítót** és a bérlőazonosítót, és jegyezze fel ezeket az értékeket, amelyekre később az oktatóanyagban lesz majd majd használni. 
3. A [Tanúsítványok és titkos kulcsok mezőben](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options)szerezze be a hitelesítési kulcsot, és jegyezze fel ezt az értéket, amit az oktatóanyag későbbi oktatóanyagában használni fog. 
4. Az [Alkalmazás hozzárendelése szerepkörhöz](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)alatt rendelje  hozzá az alkalmazást a Közreműködő szerepkörhöz az előfizetés szintjén, hogy az alkalmazás adat-üzemeket hoz létre az előfizetésben.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ezután hozzon létre egy C# .NET-konzolalkalmazást a Visual Studio:

1. Indítsa **el Visual Studio.**
2. A Start ablakban válassza az Új projekt **létrehozása Konzolalkalmazás**  >  **(.NET-keretrendszer) lehetőséget.** A lépések elvégzéséhez a .NET 4.5.2-es vagy újabb verziója szükséges.
3. A **Projekt neve alatt adja meg** a **PurviewQuickStart nevet.**
4. A projekt létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

1. Válassza az **Eszközök** > **NuGet-csomagkezelő** > **Package Csomagkezelő konzol** elemet.
2. A Csomagkezelő **konzol panelen** futtassa a következő parancsokat a csomagok telepítéséhez. További információ: [Microsoft.Azure.Management.Purview NuGet-csomag.](https://www.nuget.org/packages/Microsoft.Azure.Management.Purview/)

    ```powershell
    Install-Package Microsoft.Azure.Management.Purview
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-purview-client"></a>Purview-ügyfél létrehozása

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

2. Adja hozzá az alábbi kódot a **Main** metódushoz, amely beállítja a változókat. Cserélje le a helyőrzőket a saját értékeire. Azon Azure-régiók listáját, amelyekben a Purview jelenleg elérhető, keresse meg az **Azure Purview** listában, és válassza ki az Ön számára érdekes régiókat a következő oldalon: Régiónként elérhető [termékek.](https://azure.microsoft.com/global-infrastructure/services/)

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

3. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **PurviewManagementClient osztály egy példányát.** Ezzel az objektummal hozhat létre egy Purview-fiókot.

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

## <a name="create-a-purview-account"></a>Purview-fiók létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Purview-fiókot.** 

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

Készítse el és indítsa el az alkalmazást, majd ellenőrizze a végrehajtást.

A konzol a Purview-fiók létrehozásának előrehaladását nyomtatja ki.

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

A nézet **Nézetfiókok** lapjára Azure Portal, és ellenőrizze a fenti kóddal létrehozott fiókot. [](https://portal.azure.com) 

## <a name="delete-purview-account"></a>Purview-fiók törlése

A Purview-fiók programozott módon való törléséhez adja hozzá a következő kódsorokat a programhoz: 

```csharp
    Console.WriteLine("Deleting the Purview Account");
    client.Accounts.Delete(resourceGroup, purviewAccountName);
```

## <a name="check-if-purview-account-name-is-available"></a>Ellenőrizze, hogy elérhető-e a Purview-fiók neve

A végleges fiók rendelkezésre állásának ellenőrzéshez használja a következő kódot: 

```csharp
    CheckNameAvailabilityRequest checkNameAvailabilityRequest = new CheckNameAvailabilityRequest()
    {
      Name = purviewAccountName,
      Type =  "Microsoft.Purview/accounts"
    };
    Console.WriteLine("Check Purview account name");
    Console.WriteLine(client.Accounts.CheckNameAvailability(checkNameAvailabilityRequest).NameAvailable);
```

A fenti kód "True" (Igaz) kóddal, ha a név elérhető, és "False" (Hamis) értéket, ha a név nem érhető el.


## <a name="next-steps"></a>Következő lépések

A kód ebben az oktatóanyagban létrehoz egy purview-fiókot, töröl egy purview fiókot, és ellenőrzi a fiók nevének rendelkezésre állását. Most már letöltheti a .NET SDK-t, és megismerheti a Purview-fiókokon végrehajtható egyéb erőforrás-szolgáltatói műveleteket.

A következő cikkből megtudhatja, hogyan engedélyezheti a felhasználóknak az Azure Purview-fiókhoz való hozzáférést. 

> [!div class="nextstepaction"]
> [Felhasználók hozzáadása az Azure Purview-fiókhoz](catalog-permissions.md)
