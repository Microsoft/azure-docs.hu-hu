---
title: 'Gyors útmutató: felhasználói bejelentkezés és Microsoft Graph hívása egy Univerzális Windows-platform alkalmazásban | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan szerezhet be egy Univerzális Windows-platform-(UWP-) alkalmazás hozzáférési jogkivonatot, és hogyan hívhat meg a Microsoft Identity platform által védett API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/07/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 88fd6260df0039e960ac87b4b83d43ea497a58b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658509"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Rövid útmutató: A Microsoft Graph API meghívása Univerzális Windows-platform- (UWP-) alkalmazásból

Ebben a rövid útmutatóban egy kód mintát tölt le és futtat, amely bemutatja, hogyan jelentkezhet be egy Univerzális Windows-platform (UWP) alkalmazás a felhasználók számára, és hogyan kérhet hozzáférési jogkivonatot a Microsoft Graph API meghívásához. 

Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

> [!div renderon="docs"]
> ## <a name="prerequisites"></a>Előfeltételek
>
> * Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
> * [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)
>
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> [!div renderon="docs" class="sxs-lookup"]
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Lépjen a <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs" target="_blank">Azure Portal-Alkalmazásregisztrációk</a> gyors üzembe helyezési élményhez.
> 1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való hozzáadásához kövesse az alábbi lépéseket:
> 1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
> 1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
> 1. Keresse meg és válassza ki az **Azure Active Directoryt**.
> 1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
> 1. Adja meg az alkalmazás **nevét** , például: `UWP-App-calling-MsGraph` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
> 1. A **Támogatott fióktípusok** szakaszban jelölje be a **Tetszőleges szervezeti címtárban található fiókok és a Személyes Microsoft-fiókok (például Skype, Xbox, Outlook.com)** beállítást.
> 1. Válassza a **regisztráció** lehetőséget az alkalmazás létrehozásához, majd jegyezze fel az **alkalmazás (ügyfél) azonosítóját** egy későbbi lépésben való használatra.
> 1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
> 1. Válassza **a platform**  >  **mobil-és asztali alkalmazások** hozzáadása lehetőséget.
> 1. Az **átirányítási URI**-k területen válassza a elemet `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> 1. Válassza a **Konfigurálás** lehetőséget.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-the-application"></a>1. lépés: az alkalmazás konfigurálása
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta működjön, adjon hozzá egy **átirányítási URI** -t `https://login.microsoftonline.com/common/oauth2/nativeclient` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-uwp/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-visual-studio-project"></a>2. lépés: a Visual Studio-projekt letöltése

> [!div renderon="docs"]
> [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Futtassa a projektet a Visual Studio 2019 használatával.
> [!div class="sxs-lookup" renderon="portal" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékével konfiguráltuk, és készen áll a futtatásra.

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-visual-studio-project"></a>3. lépés: a Visual Studio-projekt konfigurálása
>
> 1. Bontsa ki a. zip-archívumot egy helyi mappába a meghajtó gyökeréhez. Például a **C:\Azure-Samples**.
> 1. Nyissa meg a projektet a Visual Studióban. Ha a rendszer kéri, telepítse a **univerzális Windows-platform-fejlesztési** számítási feladatot és az egyes SDK-összetevőket.
> 1. A *Főoldal. XAML. cs* fájlban módosítsa a változó értékét a `ClientId` korábban regisztrált alkalmazás **alkalmazás (ügyfél) azonosítójára** .
>
>    ```csharp
>    private const string ClientId = "Enter_the_Application_Id_here";
>    ```
>
>    Az **alkalmazás (ügyfél) azonosítóját** az alkalmazás **Áttekintés** paneljén találja a Azure Portal (**Azure Active Directory**  >  **Alkalmazásregisztrációk**  >  *{az alkalmazás regisztrációja}*).
> 1. Hozzon létre egy új önaláírt teszt tanúsítványt a csomaghoz, majd válassza ki a következőt:
>     1. A **megoldáskezelő** kattintson duplán a *Package. appxmanifest* fájlra.
>     1. Válasszon **csomagolást** a tanúsítvány kiválasztása.  >  **..**  >  **Létrehozás...**
>     1. Adja meg a jelszót, majd kattintson **az OK gombra**.
>     1. Válassza a **kiválasztás fájlból..**. lehetőséget, majd válassza ki az imént létrehozott *Native_UWP_V2_TemporaryKey. pfx* fájlt, majd kattintson **az OK gombra**.
>     1. Zárjuk be a *Package. appxmanifest* fájlt (válassza az **OK** gombot, ha a rendszer kéri a fájl mentését).
>     1. A **megoldáskezelő** kattintson a jobb gombbal a **Native_UWP_V2** projektre, és válassza a **Tulajdonságok** lehetőséget.
>     1. Válassza az **aláírás** lehetőséget, majd válassza ki a. pfx fájlt, amelyet a **válasszon erős nevet tartalmazó kulcsfájl** legördülő menüből.

#### <a name="step-4-run-the-application"></a>4. lépés: az alkalmazás futtatása

A minta alkalmazás futtatása a helyi gépen:

1. A Visual Studio eszköztárán válassza ki a megfelelő platformot (valószínűleg **x64** vagy **x86**, nem ARM). A célként megadott eszköznek az *eszközről* a *helyi gépre* kell váltania.
1. Válassza a **Debug (Hibakeresés)**  > **Start Without Debugging (Indítás hibakeresés nélkül)** lehetőséget.
    
    Ha a rendszer erre kéri, először engedélyeznie kell a **fejlesztői módot**, majd újra kell **indítania a hibakeresést** az alkalmazás elindításához.

Amikor megjelenik az alkalmazás ablaka, a **hívás Microsoft Graph API** gombra kattintva megadhatja a hitelesítő adatait, és beleegyezik az alkalmazás által kért engedélyekkel. Ha a művelet sikeres, az alkalmazás megjeleníti a Microsoft Graph API-nak a hívásból beszerzett néhány jogkivonat-információt és adatot.

## <a name="how-the-sample-works"></a>A minta működése

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-uwp/uwp-intro.svg)

### <a name="msalnet"></a>MSAL.NET

A MSAL ([Microsoft. Identity. Client](https://www.nuget.org/packages/Microsoft.Identity.Client)) a felhasználók bejelentkezésére és biztonsági jogkivonatok igénylésére használt könyvtár. A biztonsági jogkivonatok a Microsoft Identity platform által védett API-k elérésére szolgálnak. Az MSAL telepítéséhez futtassa a következő parancsot a Visual Studio *Package Manager konzolján*:

```powershell
Install-Package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

Az MSAL-re mutató hivatkozás hozzáadásához adja hozzá az alábbi kódot:

```csharp
using Microsoft.Identity.Client;
```

Ezt követően a MSAL inicializálása a következő kód használatával történik:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                                                    .Build();
```

A értéke a `ClientId` Azure Portal regisztrált **alkalmazás (ügyfél) azonosítója** . Ezt az értéket az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

### <a name="requesting-tokens"></a>Jogkivonatok lekérése

A MSAL két módszerrel szerezheti be a jogkivonatokat egy UWP-alkalmazásban: `AcquireTokenInteractive` és `AcquireTokenSilent` .

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Bizonyos helyzetekben a felhasználóknak fel kell kényszeríteni a Microsoft Identity platformot egy előugró ablakon keresztül a hitelesítő adatok érvényesítéséhez vagy a jóváhagyáshoz. Néhány példa:

- Az első alkalommal bejelentkezett felhasználók bejelentkeznek az alkalmazásba
- Ha a felhasználóknak újból meg kell adniuk a hitelesítési adataikat, mert lejárt a jelszó
- Ha az alkalmazás hozzáférést kér egy erőforráshoz, a felhasználónak hozzá kell járulnia
- Ha kétfaktoros hitelesítésre van szükség

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

A `scopes` paraméter tartalmazza a kért hatóköröket, például `{ "user.read" }` Microsoft Graph vagy `{ "api://<Application ID>/access_as_user" }` egyéni webes API-k esetén.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `AcquireTokenSilent` metódussal szerezhet be jogkivonatokat a védett erőforrásokhoz való hozzáféréshez a kezdeti `AcquireTokenInteractive` módszer után. Nem szeretné megkövetelni, hogy a felhasználó minden alkalommal érvényesítse a hitelesítő adataikat, amikor hozzá kell férniük egy erőforráshoz. A tokenek beszerzését és megújítását a legtöbb esetben felhasználói beavatkozás nélkül kell használni

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

* `scopes` a kért hatóköröket tartalmazza, például `{ "user.read" }` Microsoft Graph vagy `{ "api://<Application ID>/access_as_user" }` egyéni webes API-k esetén.
* `firstAccount` Megadja az első felhasználói fiókot a gyorsítótárban (a MSAL több felhasználót is támogat egyetlen alkalmazásban).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések

Próbálja ki az asztali Windowshoz készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

> [!div class="nextstepaction"]
> [UWP: A Graph API meghívása – oktatóanyag](tutorial-v2-windows-uwp.md)
