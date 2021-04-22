---
title: 'Rövid útmutató: Jogkivonat & hívása Microsoft Graph konzolalkalmazásban | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja egy .NET Core-mintaalkalmazás az ügyfél-hitelesítő adatok folyamatát egy jogkivonat lehívására és a Microsoft Graph.
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
ms.openlocfilehash: a31bf345f523eea940be5d56495890e8ab5c6dbd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861642"
---
# <a name="quickstart-get-a-token-and-call-the-microsoft-graph-api-by-using-a-console-apps-identity"></a>Rövid útmutató: Jogkivonat lehívása és a Microsoft Graph API hívása egy konzolalkalmazás identitásával

Ebben a rövid útmutatóban letölt és futtat egy kódmintát, amely bemutatja, hogyan tud egy .NET [](/graph/api/user-list) Core-konzolalkalmazás hozzáférési jogkivonatot szerezni a Microsoft Graph API hívásához és a címtárban található felhasználók listájának megjelenítéséhez. A kódminta azt is bemutatja, hogyan futhat egy feladat vagy egy Windows-szolgáltatás egy alkalmazásidentitokkal a felhasználó identitása helyett. Az ebben a rövid útmutatóban található mintakonzol-alkalmazás egyben démonalkalmazás is, ezért bizalmas ügyfélalkalmazás.

> [!div renderon="docs"]
> A mintaalkalmazás működését az alábbi ábra szemlélteti:
>
> ![Az ebben a rövid útmutatóban létrehozott mintaalkalmazás működését bemutató ábra.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)
>

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz [.NET Core 3.1 SDK szükséges,](https://dotnet.microsoft.com/download) de a .NET 5.0 SDK-val is működni fog.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-app"></a>Az alkalmazás regisztrálása és letöltése

> [!div renderon="docs" class="sxs-lookup"]
>
> Kétféle lehetőség áll rendelkezésre az alkalmazás építésének elkezdenie: automatikus vagy manuális konfigurálás.
>
> ### <a name="automatic-configuration"></a>Automatikus konfiguráció
>
> Ha regisztrálni és automatikusan konfigurálni szeretné az alkalmazást, majd le szeretné tölteni a kódmintát, kövesse az alábbi lépéseket:
>
> 1. Az <a href="https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs" target="_blank">alkalmazásregisztrációhoz Azure Portal lapra.</a>
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az utasításokat az új alkalmazás egyetlen kattintással való letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="manual-configuration"></a>Manuális konfigurálás
>
> Ha manuálisan szeretné konfigurálni az alkalmazást és a kódmintát, használja az alábbi eljárásokat.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portal. </span> </a>
> 1. Ha több bérlőhöz is hozzáféréssel rendelkezik, a felső menü **Címtár és** előfizetés szűrője segítségével válassza ki azt a bérlőt, amelyben regisztrálni szeretné :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: az alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **Kezelés alatt** válassza a **Alkalmazásregisztrációk**  >  **regisztráció lehetőséget.**
> 1. A **Név alatt** adja meg az alkalmazás nevét. Írja be például a **Daemon-console (Démon-konzol) adhatja meg a következőt:**. Az alkalmazás felhasználói látni fogják ezt a nevet, és később módosíthatja.
> 1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
> 1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.
> 1. A **Titkos ügyfél titkos kulcsok alatt** válassza az Új titkos **ügyfélkomóta** lehetőséget, adjon meg egy nevet, majd válassza a **Hozzáadás lehetőséget.** Rögzítse a titkos adatokat egy biztonságos helyen egy későbbi lépésben való használatra.
> 1. A **Kezelés alatt** válassza az **API-engedélyek**  >  **Engedély hozzáadása lehetőséget.** Válassza a **Microsoft Graph** lehetőséget.
> 1. Válassza **az Alkalmazásengedélyek lehetőséget.**
> 1. A Felhasználó **csomópont alatt** válassza a **User.Read.All**, majd az **Engedélyek hozzáadása lehetőséget.**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>A gyorsindítási alkalmazás letöltése és konfigurálása
>
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása a Azure Portal
> Ahhoz, hogy a rövid útmutatóban használt kódminta működjön, hozzon létre egy titkos ügyfélkódot, és adja hozzá a Graph API **User.Read.All** alkalmazásengedélyét.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Ezeket a módosításokat a számomra kell eltenem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-netcore-daemon/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-your-visual-studio-project"></a>2. lépés: A Visual Studio-projekt letöltése

> [!div renderon="docs"]
> [A Visual Studio letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)
>
> A megadott projektet futtathatja a Visual Studio vagy Visual Studio for Mac.


> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet a 2019 Visual Studio használatával.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-visual-studio-project"></a>3. lépés: A Visual Studio-projekt konfigurálása
>
> 1. Bontsa ki a .zip fájlt egy helyi mappába, amely a lemez gyökerének közelében található. Bontsa ki például a *következőt: C:\Azure-Samples.*
>
>    Javasoljuk, hogy bontsa ki az archívumot a meghajtó gyökerének közelében található könyvtárba, hogy elkerülje a Windows elérésiút-hosszának korlátozásai által okozott hibákat.
>
> 1. Nyissa meg a megoldást a Visual Studio: *1-Call-MSGraph\daemon-console.sln* (nem kötelező).
> 1. A *appsettings.jsa ,,* és értékeit `Tenant` cserélje `ClientId` le: , és `ClientSecret` :
>
>    ```json
>    "Tenant": "Enter_the_Tenant_Id_Here",
>    "ClientId": "Enter_the_Application_Id_Here",
>    "ClientSecret": "Enter_the_Client_Secret_Here"
>    ```
>    A kódban:
>    - `Enter_the_Application_Id_Here` A a regisztrált alkalmazás alkalmazás- (ügyfél-) azonosítója.
        Az alkalmazás (ügyfél) azonosítójának és a címtár (bérlő) azonosítójának értékeit az alkalmazás Áttekintés lapján találja a Azure Portal. 
>    - Cserélje `Enter_the_Tenant_Id_Here` le a helyére a bérlőazonosítót vagy a bérlő nevét (például: `contoso.microsoft.com` ).
>    - Cserélje `Enter_the_Client_Secret_Here` le a helyére az 1. lépésben létrehozott titkos ügyféltitkot.
    Új kulcs létrehozásához kattintson a Tanúsítványok és titkos **& lapra.**

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>3. lépés: Rendszergazdai jóváhagyás

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>4. lépés: Rendszergazdai jóváhagyás

Ha ezen a ponton próbálja futtatni az alkalmazást, a *következő HTTP 403 – Tiltott* hibaüzenet jelenik meg: "Nem megfelelő jogosultságok a művelet végrehajtásához.". Ez a hiba azért fordul elő, mert minden csak alkalmazásra vonatkozó engedélyhez a címtár globális rendszergazdájának kell jóváhagyást adni az alkalmazáshoz. Szerepkörétől függően válasszon az alábbi lehetőségek közül.

##### <a name="global-tenant-administrator"></a>Globális bérlői rendszergazda

> [!div renderon="docs"]
> Ha Ön globális bérlői rendszergazda, akkor a vállalati alkalmazások 2016-ban **Azure Portal.** Válassza ki az alkalmazásregisztrációt, majd válassza az **Engedélyek** lehetőséget **a** bal oldali panel Biztonság szakaszában. Ezután válassza a Rendszergazdai jóváhagyás megadása a következőhöz} feliratú nagy gombot (ahol a **{Bérlő** **neve}** a címtár neve).

> [!div renderon="portal" class="sxs-lookup"]
> Ha Ön globális rendszergazda, akkor az **API-engedélyek** oldalon válassza a **Rendszergazdai** jóváhagyás megadása a Enter_the_Tenant_Name_Here.
> > [!div id="apipermissionspage"]
> > [Ugrás az API-engedélyek lapra]()

##### <a name="standard-user"></a>Általános felhasználó

Ha Ön a bérlő általános jogú felhasználója, kérjen meg egy globális rendszergazdát, hogy adjon rendszergazdai jóváhagyást az alkalmazáshoz. Ehhez adja meg a következő URL-címet a rendszergazdának:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
> Az URL-címben:
> * Cserélje `Enter_the_Tenant_Id_Here` le a helyére a bérlő azonosítóját vagy a bérlő nevét (például: `contoso.microsoft.com` ).
> * `Enter_the_Application_Id_Here` A a regisztrált alkalmazás alkalmazás- (ügyfél-) azonosítója.

Az "AADSTS50011: No reply address is registered for the application" (AADSTS50011: Nincs válaszcím regisztrálva az alkalmazáshoz) hibaüzenet jelenhet meg, miután az előző URL-cím használatával engedélyt adott az alkalmazásnak. Ez a hiba azért fordul elő, mert az alkalmazás és az URL-cím nem tartalmaz átirányítási URI-t. Ezt figyelmen kívül hagyhatja.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>4. lépés: Az alkalmazás futtatása

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>5. lépés: Az alkalmazás futtatása

Ha az alkalmazást Visual Studio vagy Visual Studio for Mac, nyomja le az **F5** billentyűt az alkalmazás futtatásához. Ellenkező esetben futtassa az alkalmazást parancssorból, konzolból vagy terminálból:

```dotnetcli
cd {ProjectFolder}\1-Call-MSGraph\daemon-console
dotnet run
```
A kódban:
* `{ProjectFolder}` A az a mappa, ahová kibontotta a .zip fájlt. Például: `C:\Azure-Samples\active-directory-dotnetcore-daemon-v2`.

Ennek eredményeképpen megjelenik a Azure Active Directory listája.

Ez a rövid útmutatót használó alkalmazás titkos ügyféltitkot használ a bizalmas ügyfélként való azonosításához. A titkos ügyfél titkos adatokat a rendszer egyszerű szöveges fájlként hozzáadja a projektfájlokhoz. Biztonsági okokból azt javasoljuk, hogy az alkalmazás éles alkalmazásként való használata előtt használjon tanúsítványt titkos ügyfél titkos ügyfél helyett. A tanúsítványok használatával kapcsolatos további információkért tekintse meg ezeket az utasításokat a minta GitHub-adattárában. [](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates)

## <a name="more-information"></a>További információ
Ez a szakasz áttekintést nyújt a felhasználók bejelentkezéséhez szükséges kódról. Ez az áttekintés hasznos lehet a kód működése, a fő argumentumok, valamint a bejelentkezés hozzáadása egy meglévő .NET Core-konzolalkalmazáshoz.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="how-the-sample-works"></a>A minta működése
>
> ![Az ebben a rövid útmutatóban létrehozott mintaalkalmazás működését bemutató ábra.](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

### <a name="msalnet"></a>MSAL.NET

A Microsoft Authentication Library (MSAL, a [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) csomagban) a felhasználók bejelentkeztetése és a Microsoft identitásplatformja által védett API-k eléréséhez szükséges jogkivonatok lekérésére használt kódtár. Ez a rövid útmutató a jogkivonatokat az alkalmazás saját identitásának használatával kéri le a delegált engedélyek helyett. A hitelesítési folyamatot ebben az esetben ügyfél-hitelesítő [adatok OAuth-folyamatának nevezik.](v2-oauth2-client-creds-grant-flow.md) Az ügyfél-hitelesítő adatok folyamatával MSAL.NET további információt ebben a [cikkben talál.](https://aka.ms/msal-net-client-credentials)

 A MSAL.NET a következő parancs futtatásával telepíthető a Visual Studio Csomagkezelő konzolon:

```dotnetcli
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezután inicializálja az MSAL-t az alábbi kóddal:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
```

 | Elem | Leírás |
 |---------|---------|
 | `config.ClientSecret` | Az alkalmazásban létrehozott titkos ügyfél titkos Azure Portal. |
 | `config.ClientId` | Az alkalmazásban regisztrált alkalmazás (ügyfél) azonosítója Azure Portal. Ezt az értéket az alkalmazás Áttekintés **oldalán** találja a Azure Portal. |
 | `config.Authority`    | (Nem kötelező) A biztonsági jogkivonat szolgáltatás (STS) végpontja a felhasználó számára a hitelesítéshez. Ez általában a nyilvános felhőre igaz, ahol a a bérlője neve vagy a `https://login.microsoftonline.com/{tenant}` `{tenant}` bérlő azonosítója.|

További információért tekintse meg a [referenciadokumentációját. `ConfidentialClientApplication` ](/dotnet/api/microsoft.identity.client.iconfidentialclientapplication)

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

Jogkivonat az alkalmazás identitásával való lekéréséhez használja a `AcquireTokenForClient` metódust:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

|Elem| Leírás |
|---------|---------|
| `scopes` | A kért hatókörök. Bizalmas ügyfelek esetében ennek az értéknek a következő formátumot kell használnia: `{Application ID URI}/.default` . Ez a formátum azt jelzi, hogy a kért hatókörök azok, amelyek statikusan vannak definiálva az alkalmazásobjektumban a Azure Portal. A Microsoft Graph pont `{Application ID URI}` erre `https://graph.microsoft.com` mutat: . Egyéni webes API-k esetén a definíciója a Azure Portal `{Application ID URI}` az Alkalmazásregisztráció **(előzetes verzió)**  >  **API-k elérhetővé tevése alatt található.** |

További információért tekintse meg a [referenciadokumentációját. `AcquireTokenForClient` ](/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

A démonalkalmazásokkal kapcsolatos további információkért tekintse meg a forgatókönyv áttekintését:

> [!div class="nextstepaction"]
> [Webes API-kat behívó démonalkalmazás](scenario-daemon-overview.md)
