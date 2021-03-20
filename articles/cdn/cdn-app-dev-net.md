---
title: Ismerkedés a .NET-hez készült Azure CDN-függvénytárral | Microsoft Docs
description: Ismerje meg, hogyan írhat .NET-alkalmazásokat a Visual Studióval való Azure CDN kezeléséhez.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: a812704c42a4da5ddf89fe6c5ba0c9a684047f75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919488"
---
# <a name="get-started-with-the-azure-cdn-library-for-net"></a>Ismerkedés a .NET-hez készült Azure CDN-függvénytárral
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
>
>

A [.net-hez készült Azure CDN kódtár](/dotnet/api/overview/azure/cdn) segítségével automatizálhatja a CDN-profilok és-végpontok létrehozását és felügyeletét.  Ez az oktatóanyag végigvezeti egy olyan egyszerű .NET-konzol alkalmazás létrehozásán, amely számos elérhető műveletet mutat be.  Ez az oktatóanyag nem ismerteti részletesen a Azure CDN-függvénytár összes aspektusát.

Az oktatóanyag elvégzéséhez szüksége lesz a Visual Studio 2015-re.  A [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) ingyenesen letölthető.

> [!TIP]
> Az [oktatóanyagból származó befejezett projekt](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) letölthető az MSDN webhelyen.
>
>

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>A projekt létrehozása és Nuget-csomagok hozzáadása
Most, hogy létrehozott egy erőforráscsoportot a CDN-profilokhoz, és az Azure AD-alkalmazás engedélyt kapott a CDN-profilok és-végpontok kezelésére a csoporton belül, elkezdheti az alkalmazás létrehozását.

A Visual Studio 2015-es verziójában kattintson a **fájl**, **új**, **projekt...** elemre az új projekt párbeszédpanel megnyitásához.  Bontsa ki a **Visual C#** elemet, majd a bal oldali ablaktáblán válassza a **Windows** lehetőséget.  Kattintson a **konzol alkalmazás** elemre a középső ablaktáblán.  Nevezze el a projektet, majd kattintson **az OK** gombra.

![Új projekt](./media/cdn-app-dev-net/cdn-new-project.png)

A projektünk Nuget-csomagokban található Azure-kódtárakat fog használni.  Vegyük fel ezeket a projektbe.

1. Kattintson az **eszközök** menüre, a **Nuget csomagkezelő**, majd a **Package Manager konzol** elemre.

    ![Nuget-csomagok kezelése](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. A Package Manager konzolon hajtsa végre a következő parancsot a **Active Directory-hitelesítési tár telepítéséhez (ADAL)**:

    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. A **Azure CDN felügyeleti könyvtár** telepítéséhez hajtsa végre a következő műveleteket:

    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Irányelvek, állandók, Main metódus és segítő módszerek
Nézzük meg a programunk alapszintű szerkezetét.

1. Lépjen vissza a program. cs lapra, és cserélje le a `using` felül található irányelveket a következőre:

    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Meg kell határozni néhány állandót, amelyeket a metódusok használni fognak.  A `Program` osztályban, de a metódus előtt `Main` adja hozzá a következőt.  A helyőrzőket cserélje le úgy, hogy a **&lt; zárójeleket &gt;** is beleértve, szükség esetén a saját értékeivel.

    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";

    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Az osztály szintjén is definiálja ezt a két változót.  Ezeket később a profil és a végpont létezésének megállapításához fogjuk használni.

    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Cserélje le a `Main` metódust a következőképpen:

   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();

       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };

       ListProfilesAndEndpoints(cdn);

       // Create CDN Profile
       CreateCdnProfile(cdn);

       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);

       Console.WriteLine();

       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);

       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);

       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);

       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Más módszerekkel a felhasználó "igen/nem" kérdésekkel fog megjelenni.  Adja hozzá a következő metódust, hogy egy kicsit könnyebb legyen:

    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Most, hogy a program alapszintű szerkezete meg van írva, létre kell hoznia a metódus által hívott metódusokat `Main` .

## <a name="authentication"></a>Hitelesítés
Ahhoz, hogy használhassa a Azure CDN felügyeleti könyvtárat, hitelesítenie kell az egyszerű szolgáltatásnevet, és be kell szereznie egy hitelesítési jogkivonatot.  Ez a metódus a ADAL használatával kéri le a tokent.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult =
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Ha egyéni felhasználói hitelesítést használ, a `GetAccessToken` metódus némileg eltérő lesz.

> [!IMPORTANT]
> Csak akkor használja ezt a kódot, ha egy egyszerű szolgáltatásnév helyett egyéni felhasználói hitelesítést szeretne használni.
>
>

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Ügyeljen arra, hogy `<redirect URI>` az alkalmazás Azure ad-ban való regisztrálása során megadott átirányítási URI-t cserélje le.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN-profilok és-végpontok listázása
Most már készen áll a CDN-műveletek végrehajtására.  A módszer első lépése az erőforráscsoport összes profiljának és végpontjának felsorolása, és ha az Állandókban megadott profilhoz és végponthoz tartozó nevek egyezést talál, akkor azt később is jegyezze fel, ezért nem próbálunk ismétlődéseket létrehozni.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN-profilok és-végpontok létrehozása
Ezután létrehozunk egy profilt.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

A profil létrehozása után hozzunk létre egy végpontot.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> A fenti példa hozzárendeli a végpontot a *contoso* nevű forráshoz egy állomásnévvel `www.contoso.com` .  Ezt úgy kell módosítania, hogy a saját forrásához tartozó állomásnévre mutasson.
>
>

## <a name="purge-an-endpoint"></a>Végpont kiürítése
Feltételezve, hogy a végpont létrejött, egy gyakori feladat, amelyet a programban végre szeretnénk hajtani, a végponton lévő tartalom megtisztítása.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> A fenti példában a karakterlánc azt `/*` jelöli, hogy a végpont elérési útjának gyökerében mindent törölni kívánok.  Ez egyenértékű az **összes törlés** ellenőrzésével a Azure Portal "Purge" párbeszédablakban. A `CreateCdnProfile` metódusban létrehoztam a profilt a **Verizon** -profilból Azure CDN a kóddal `Sku = new Sku(SkuName.StandardVerizon)` , így ez sikeres lesz.  A **Akamai-profiloktól való Azure CDN** azonban nem támogatja az **összes kiürítést**, így ha az oktatóanyaghoz Akamai-profilt használok, a törléshez konkrét elérési utakat is meg kell adni.
>
>

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN-profilok és-végpontok törlése
A legutóbbi metódusok törlik a végpontot és a profilt.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>A program futtatása
Most már lefordíthatja és futtathatja a programot a Visual Studióban a **Start** gombra kattintva.

![Program fut](./media/cdn-app-dev-net/cdn-program-running-1.png)

Ha a program eléri a fenti kérést, vissza kell tudnia térni az erőforráscsoporthoz a Azure Portalban, és látnia kell, hogy a profil létrejött.

![Sikerült!](./media/cdn-app-dev-net/cdn-success.png)

Ezután megerősítheti a kéréseket, hogy futtassa a program többi részét.

![Program befejezése](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Következő lépések
Ha meg szeretné tekinteni a kész projektet ebből az útmutatóból, [töltse le a mintát](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Ha további dokumentációt szeretne keresni a .NET-hez készült Azure CDN Management Library-hez, tekintse meg az MSDN-beli [referenciát](/dotnet/api/overview/azure/cdn).

A CDN-erőforrások kezelése a [PowerShell](cdn-manage-powershell.md)-lel.
