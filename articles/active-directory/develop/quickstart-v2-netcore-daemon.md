---
title: 'Gyors útmutató: token beszerzése & Call Microsoft Graph a konzol alkalmazásban | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a .NET Core-minta alkalmazás az ügyfél hitelesítő adatait a token beszerzéséhez és a Microsoft Graph meghívásához.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:aspnet-core
ms.openlocfilehash: 1b539c168deab7c1893f071a2453be28310fc132
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105022925"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Gyors útmutató: token beszerzése és a Microsoft Graph API meghívása a konzol alkalmazás identitásával

Ebben a rövid útmutatóban egy olyan kódrészletet tölt le és futtat, amely bemutatja, hogy a .NET Core Console-alkalmazás hogyan kaphat hozzáférési jogkivonatot a Microsoft Graph API meghívásához és a címtárban lévő [felhasználók listájának](/graph/api/user-list) megjelenítéséhez. A kód minta azt is bemutatja, hogy egy adott feladatot vagy Windows-szolgáltatást hogyan futtathat egy alkalmazás identitásával a felhasználó identitása helyett. Ebben a rövid útmutatóban a minta konzol alkalmazás is egy démon-alkalmazás, ezért ez egy bizalmas ügyfélalkalmazás.

> [!div renderon="docs"]
> Az alábbi ábrán a minta alkalmazás működése látható:
>
> ![Diagram, amely bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a [.net core 3,1](https://www.microsoft.com/net/download/dotnet-core) -es verzióra van szükség, de a .net Core 5,0 is működni fog.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Az alkalmazás regisztrálása és letöltése

> [!div renderon="docs" class="sxs-lookup"]
>
> Az alkalmazás összeállításának megkezdéséhez két lehetőség közül választhat: automatikus vagy manuális konfiguráció.
>
> ### <a name="automatic-configuration"></a>Automatikus konfiguráció
>
> Ha regisztrálni és automatikusan szeretné konfigurálni az alkalmazást, majd töltse le a kód mintát, kövesse az alábbi lépéseket:
>
> 1. Lépjen a <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">Azure Portal lapra az alkalmazás regisztrálásához</a>.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Az új alkalmazás egyetlen kattintással való letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="manual-configuration"></a>Manuális konfigurálás
>
> Ha manuálisan szeretné konfigurálni az alkalmazás és a kód mintát, kövesse az alábbi eljárásokat.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba </span> </a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kívánja az alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. A **név** mezőben adja meg az alkalmazás nevét. Adja meg például a **Daemon-Console** értéket. Az alkalmazás felhasználói ezt a nevet fogják látni, és később is megváltoztathatók.
> 1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
> 1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.
> 1. Az **ügyfél** titkos kulcsa területen válassza az **új ügyfél titka** elemet, adjon meg egy nevet, majd válassza a **Hozzáadás** lehetőséget. A titkos értéket egy biztonságos helyen rögzítheti egy későbbi lépésben való használatra.
> 1. A **kezelés** területen válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**. Válassza a **Microsoft Graph** lehetőséget.
> 1. Válassza ki az **alkalmazás engedélyeit**.
> 1. A **felhasználó** csomópont alatt válassza a **felhasználó. olvasás. mind** lehetőséget, majd kattintson az **engedélyek hozzáadása** lehetőségre.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>A Gyorsindítás alkalmazás letöltése és konfigurálása
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Az ebben a rövid útmutatóban szereplő mintakód esetében hozzon létre egy ügyfél titkot, és adja hozzá a Graph API **felhasználóját. Read. All** Application engedély.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

> [!div renderon="docs"]
> [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> A megadott projektet a Visual Studióban vagy a Visual Studio for Macban is futtathatja.


> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet a Visual Studio 2019 használatával.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása
>
> 1. Bontsa ki a. zip fájlt egy helyi mappába, amely a lemez gyökeréhez közeledik. Bontsa ki például a következőt: *C:\Azure-Samples*.
>
>    Azt javasoljuk, hogy az archívumot a meghajtó gyökerének közelében egy könyvtárba csomagolja, hogy elkerülje a Windowson a Path Length korlátozásai által okozott hibákat.
>
> 1. Nyissa meg a megoldást a Visual Studióban: *1-Call-MSGraph\daemon-Console.SLN* (nem kötelező).
> 1. A *appsettings.jsa*(z) elemnél cserélje le a `Tenant` , `ClientId` , és `ClientSecret` : értéket.
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    Ebben a kódban:
>    - `Enter_the_Application_Id_Here` a regisztrált alkalmazáshoz tartozó alkalmazás (ügyfél) azonosítója.
        Az alkalmazás (ügyfél) AZONOSÍTÓjának és a könyvtár (bérlő) AZONOSÍTÓjának megkereséséhez nyissa meg a Azure Portal alkalmazás **Áttekintés** lapját.
>    - Cserélje le `Enter_the_Tenant_Id_Here` a helyére a bérlő azonosítóját vagy a bérlő nevét (például: `contoso.microsoft.com` ).
>    - Cserélje le `Enter_the_Client_Secret_Here` az értékét az 1. lépésben létrehozott ügyfél-titkos kulcsra.
    Új kulcs létrehozásához nyissa meg a **tanúsítványok & titkok** lapot.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: rendszergazdai engedély

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: rendszergazdai engedély

Ha ezen a ponton próbálja futtatni az alkalmazást, *HTTP 403-Tiltott* hibaüzenetet kap: "nincs megfelelő jogosultsága a művelet végrehajtásához." Ez a hiba azért fordul elő, mert az alkalmazás csak a címtár globális rendszergazdájának szükséges ahhoz, hogy beleegyezést nyújtson az alkalmazásához. A szerepkörtől függően válasszon a következő lehetőségek közül.

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Ön globális bérlői rendszergazda, látogasson el a Azure Portal **vállalati alkalmazásaiba** . Válassza ki az alkalmazás regisztrációját, és válassza a bal oldali ablaktábla **Biztonság** szakaszának **engedélyek** elemét. Ezután jelölje be a (z) **{bérlő neve}** (ahol a (z) { **bérlő neve}** a címtár neve) címkével ellátott nagyméretű gomb megadását.

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, nyissa meg az **API-engedélyek** lapot, és válassza a **rendszergazdai jóváhagyás megadása Enter_the_Tenant_Name_Herehoz** lehetőséget.
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek oldalra]()

##### <a name="standard-user"></a>Általános jogú felhasználó

Ha Ön a bérlő általános jogú felhasználója, kérje meg a globális rendszergazdát, hogy adja meg az alkalmazás rendszergazdai jóváhagyását. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Az URL-címben:
> * Cserélje le `Enter_the_Tenant_Id_Here` a helyére a bérlő azonosítóját vagy a bérlő nevét (például: `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` a regisztrált alkalmazáshoz tartozó alkalmazás (ügyfél) azonosítója.

Az előző URL-cím használatával megjelenhet a "AADSTS50011: no reply cím regisztrálva az alkalmazáshoz" hibaüzenet, miután beleegyezett az alkalmazásba. Ez a hiba azért fordul elő, mert ez az alkalmazás és az URL-cím nem rendelkezik átirányítási URI-val. Figyelmen kívül hagyhatja.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: az alkalmazás futtatása

Ha a Visual studiót vagy a Visual Studio for Mac használja, nyomja le az **F5** billentyűt az alkalmazás futtatásához. Ellenkező esetben futtassa az alkalmazást parancssor, konzol vagy terminál használatával:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
Ebben a kódban:
* `{ProjectFolder}` az a mappa, ahová kibontotta a. zip fájlt. Például: `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Az eredményként meg kell jelennie a felhasználók listájának Azure Active Directory.

Ez a rövid útmutató alkalmazás egy ügyfél titkos kulcsát használja, amely bizalmas ügyfélként azonosítja magát. Az ügyfél titkos kulcsát a rendszer egyszerű szöveges fájlként adja hozzá a Project-fájlokhoz. Biztonsági okokból javasoljuk, hogy az alkalmazás éles alkalmazásként való megfontolása előtt használjon tanúsítványokat az ügyfél titkos kulcsa helyett. A tanúsítványok használatáról a minta GitHub-tárházában talál [további](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) információt.

## <a name="more-information"></a>További információ
Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódokról. Ez az Áttekintés hasznos lehet a kód működésének megértéséhez, a fő argumentumok megismeréséhez és a bejelentkezés meglévő .NET Core Console-alkalmazáshoz való hozzáadásához.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>A minta működése
>
> ![Diagram, amely bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

A Microsoft-hitelesítési függvénytár (MSAL, a [Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client) csomagban) az a könyvtár, amellyel a felhasználók bejelentkezhetnek, és jogkivonatokat kérhetnek a Microsoft Identity platform által védett API eléréséhez. Ez a rövid útmutató a jogkivonatokat az alkalmazás saját identitásának használatával kéri a delegált engedélyek helyett. Ebben az esetben a hitelesítési folyamat az [ügyfél hitelesítő adatainak OAuth folyamata](v2-oauth2-client-creds-grant-flow.md). A MSAL.NET ügyfél-hitelesítő adatokkal való használatával kapcsolatos további információkért tekintse meg [ezt a cikket](https://aka.ms/msal-net-client-credentials).

 A MSAL.NET a következő parancs futtatásával telepítheti a Visual Studio Package Manager konzolon:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezután inicializálja a MSAL a következő kód használatával:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Elem | Leírás |
 |---------|---------|
 | `config.ClientSecret` | A Azure Portalban az alkalmazáshoz létrehozott ügyfél-titkos kulcs. |
 | `config.ClientId` | A Azure Portalban regisztrált alkalmazás alkalmazás-(ügyfél-) azonosítója. Ez az érték a Azure Portal alkalmazás **Áttekintés** lapján található. |
 | `config.Authority`    | Választható A hitelesítéshez a felhasználóhoz tartozó biztonságijogkivonat-szolgáltatás (STS) végpontja. Általában a `https://login.microsoftonline.com/{tenant}` nyilvános felhőben, ahol a a `{tenant}` bérlő neve vagy a bérlő azonosítója.|

További információt a [dokumentációjában `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)talál.

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Ha az alkalmazás identitásával szeretne jogkivonatot kérni, használja a következő `AcquireTokenForClient` metódust:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Elem| Leírás |
|---------|---------|
| `scopes` | A kért hatóköröket tartalmazza. Bizalmas ügyfelek esetén ennek az értéknek a következőhöz hasonló formátumot kell használnia: `{Application ID URI}/.default` . Ez a formátum azt jelzi, hogy a kért hatókörök azok, amelyek statikusan vannak meghatározva a Azure Portalban beállított alkalmazás-objektumban. Microsoft Graph esetén a következőre `{Application ID URI}` mutat: `https://graph.microsoft.com` . Az egyéni webes API- `{Application ID URI}` k esetében a Azure Portal az **alkalmazás regisztrálása (előzetes verzió)** alatt  >  **elérhetővé teszi az API**-t. |

További információt a [dokumentációjában `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)talál.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A Daemon-alkalmazásokkal kapcsolatos további tudnivalókért tekintse meg a forgatókönyv áttekintését:

> [!div class="nextstepaction"]
> [Webes API-kat meghívó Daemon-alkalmazás](scenario-daemon-overview.md)
