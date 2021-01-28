---
title: 'Oktatóanyag: több-bérlős démon létrehozása, amely hozzáfér Microsoft Graph üzleti adatszolgáltatásokhoz | Azure'
titleSuffix: Microsoft identity platform
description: Ebből az oktatóanyagból megtudhatja, hogyan hívhat meg Azure Active Directory által védett ASP.NET webes API-t egy Windows asztali (WPF-) alkalmazásból. A WPF-ügyfél hitelesíti a felhasználót, hozzáférési jogkivonatot kér, és meghívja a webes API-t.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 984b85ff831146060f1642b9eeec7079ff966db3
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937824"
---
# <a name="tutorial-build-a-multi-tenant-daemon-that-uses-the-microsoft-identity-platform"></a>Oktatóanyag: a Microsoft Identity platformot használó több-bérlős démon létrehozása

Ebben az oktatóanyagban egy ASP.NET Daemon-webalkalmazást tölt le és futtat, amely azt mutatja be, hogy a OAuth 2,0 ügyfél hitelesítő adatai megadják a hozzáférési jogkivonatot a Microsoft Graph API meghívásához.

Ebben az oktatóanyagban:

> [!div class="checklist"]
> * Daemon-alkalmazás integrálása a Microsoft Identity platformmal
> * Alkalmazás engedélyeinek közvetlenül az alkalmazáshoz való megadása rendszergazdaként
> * Hozzáférési jogkivonat beszerzése a Microsoft Graph API meghívásához
> * Hívja meg a Microsoft Graph API-t.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

- [Visual Studio 2017 vagy 2019](https://visualstudio.microsoft.com/downloads/).
- Egy Azure AD-bérlő. További információ: [Azure ad-bérlő beszerzése](quickstart-create-new-tenant.md).
- Egy vagy több felhasználói fiók az Azure AD-bérlőben. Ez a minta nem fog működni Microsoft-fiók. Ha bejelentkezett a [Azure Portalba](https://portal.azure.com) egy Microsoft-fiók, és még soha nem hozott létre felhasználói fiókot a címtárban, tegye meg most.

## <a name="scenario"></a>Eset

Az alkalmazás ASP.NET MVC-alkalmazásként van felépítve. A OWIN OpenID Connect middleware használatával jelentkezik be a felhasználókba.

Ebben a példában a "Daemon" összetevő egy API-vezérlő `SyncController.cs` . A vezérlő hívásakor a rendszer lekéri az ügyfél Azure Active Directory (Azure AD) bérlőben lévő felhasználók listáját a Microsoft Graph. `SyncController.cs` egy AJAX-hívás indítja el a webalkalmazásban. A .NET- [hez készült Microsoft Authentication Library (MSAL)](msal-overview.md) használatával szerzi be Microsoft Graph hozzáférési jogkivonatát.

Mivel az alkalmazás egy több-bérlős alkalmazás a Microsoft üzleti ügyfelei számára, meg kell adnia egy módot arra, hogy az ügyfelek "regisztráljanak" vagy "csatlakozzanak" az alkalmazáshoz a vállalati adatszolgáltatásokhoz. A kapcsolati folyamat során a vállalati rendszergazda először közvetlenül az *alkalmazáshoz ad engedélyeket* , így nem interaktív módon férhet hozzá a vállalati adatbázisokhoz a bejelentkezett felhasználó jelenléte nélkül. A példában szereplő logika többsége azt mutatja be, hogyan valósítható meg ez a kapcsolódási folyamat az Identity platform [rendszergazdai engedélyezési](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) végpontjának használatával.

![A diagram az Azure-hoz csatlakozó három helyi elemmel rendelkező UserSync-alkalmazást mutatja be, a Start dot Auth pedig interaktív módon beolvassa a tokent az Azure A D-hez való csatlakozáshoz, így a rendszergazda beleegyezik az Azure-ba való csatlakozáshoz, és a SyncController Microsoft Graphhoz való csatlakozáshoz.](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Az ebben a mintában használt fogalmakkal kapcsolatos további információkért olvassa el az [ügyfél hitelesítő adatai protokoll dokumentációját az Identity platformhoz](v2-oauth2-client-creds-grant-flow.md).

## <a name="clone-or-download-this-repository"></a>A tárház klónozása vagy letöltése

A rendszerhéjból vagy parancssorból írja be a következő parancsot:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Vagy [töltse le a mintát egy zip-fájlba](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Az alkalmazás regisztrálása

Ez a minta egy projekttel rendelkezik. Az alkalmazás Azure AD-Bérlővel való regisztrálásához az alábbiakat teheti:

- Kövesse a [minta regisztrálása a Azure Active Directory Bérlővel](#register-the-client-app-dotnet-web-daemon-v2) című témakör lépéseit, és [konfigurálja a mintát az Azure ad-bérlő használatára](#choose-the-azure-ad-tenant).
- Használjon olyan PowerShell-parancsfájlokat, amelyek:
  - *Automatikusan* létrehozhatja az Azure ad-alkalmazásokat és a kapcsolódó objektumokat (jelszavak, engedélyek, függőségek).
  - Módosítsa a Visual Studio-projektek konfigurációs fájljait.

Ha az automationt szeretné használni:

1. Windows rendszeren futtassa a PowerShellt, és lépjen a klónozott könyvtár gyökeréhez.
1. Futtassa ezt a parancsot:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Futtassa a szkriptet az Azure AD-alkalmazás létrehozásához, és ennek megfelelően konfigurálja a minta alkalmazás kódját:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   A parancsfájlok futtatásának egyéb módjai az [alkalmazás-létrehozási parancsfájlok](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md)című témakörben találhatók.

1. Nyissa meg a Visual Studio-megoldást, és kattintson a **Start** gombra a kód futtatásához.

Ha nem kívánja használni az automatizálást, kövesse az alábbi részben ismertetett lépéseket.

### <a name="choose-the-azure-ad-tenant"></a>Az Azure AD-bérlő kiválasztása

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.


### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Az ügyfélalkalmazás regisztrálása (DotNet-web-Daemon-v2)

1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** , például: `dotnet-web-daemon-v2` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. A **támogatott fiókok típusai** szakaszban válassza a **fiókok lehetőséget bármely szervezeti címtárban**.
1. Az **átirányítási URI (nem kötelező)** szakaszban válassza a kombinált lista **webes** elemét, és adja meg az `https://localhost:44316/` és `https://localhost:44316/Account/GrantPermissions` az átirányítási URI-t.

    Ha kettőnél több átirányítási URI van, akkor az alkalmazás sikeres létrehozása után később hozzá kell adnia őket a **hitelesítés** lapon.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **Áttekintés** lapján keresse meg az **alkalmazás (ügyfél) azonosító** értékét, és jegyezze fel későbbi használatra. Ehhez a projekthez a Visual Studio konfigurációs fájlját kell konfigurálnia.
1. A **kezelés** területen válassza a **hitelesítés** lehetőséget.
1. Az **előtérben kijelentkezési URL-cím** beállítása a következőre: `https://localhost:44316/Account/EndSession` .
1. Az **implicit támogatás** szakaszban válassza a **hozzáférési jogkivonatok** és **azonosító tokenek** elemet. Ez a minta megköveteli, hogy az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezze a bejelentkezést a felhasználó felé, és hívjon fel egy API-t.
1. Kattintson a **Mentés** gombra.
1. A **Kezelés** területen válassza a **Tanúsítványok és titkos kódok** lehetőséget.
1. Az **ügyfél titkai** szakaszban válassza az **új ügyfél titka** elemet. 
1. Adja meg a kulcs leírását (például az **alkalmazás titkát**).
1. Válassza ki a kulcs időtartamát **1 év** vagy **2 év** között, vagy **Soha ne járjon le**.
1. Válassza a **Hozzáadás** elemet. Jegyezze fel a kulcs értékét egy biztonságos helyen. Erre a kulcsra később szüksége lesz a projekt konfigurálásához a Visual Studióban.
1. A **kezelés** területen válassza az **API-engedélyek**  >  **Hozzáadás engedélyt**.
1. A **gyakran használt Microsoft API** -k szakaszban válassza a **Microsoft Graph** lehetőséget.
1. Az **alkalmazás engedélyei** szakaszban győződjön meg arról, hogy a megfelelő engedélyek vannak kiválasztva: **User. Read. All**.
1. Válassza az **engedélyek hozzáadása** lehetőséget.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>A minta beállítása az Azure AD-bérlő használatára

A következő lépésekben a **ClientID** ugyanaz, mint az "Application id" vagy a **AppID**.

A projektek konfigurálásához nyissa meg a megoldást a Visual Studióban.

### <a name="configure-the-client-project"></a>Az ügyfél-projekt konfigurálása

Ha a telepítési parancsfájlokat használta, a következő módosításokat alkalmazza a rendszer.

1. Nyissa meg a **UserSync\Web.Config** fájlt.
1. Keresse meg az **Ida: ClientId** alkalmazás kulcsát. Cserélje le a meglévő értéket a Azure Portal-ból másolt, a **DotNet-web-Daemon-v2** alkalmazás alkalmazás-azonosítójával.
1. Keresse meg az **Ida: ClientSecret** alkalmazás kulcsát. Cserélje le a meglévő értéket arra a kulcsra, amelyet a **DotNet-web-Daemon-v2** alkalmazás létrehozásakor mentett a Azure Portal.

## <a name="run-the-sample"></a>Minta futtatása

Tisztítsa meg a megoldást, hozza létre újra a megoldást, futtassa a UserSync alkalmazást, majd jelentkezzen be rendszergazdaként az Azure AD-bérlőben. Ha nem rendelkezik Azure AD-Bérlővel a teszteléshez, az [alábbi utasításokat követve](quickstart-create-new-tenant.md) kérhet le egyet.

Amikor bejelentkezik, az alkalmazás először engedélyt kér a bejelentkezésre, és beolvassa a felhasználói profilt. Ez az engedély lehetővé teszi az alkalmazás számára, hogy Ön üzleti felhasználó legyen.

![Felhasználói beleegyezés](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Az alkalmazás ezután megpróbálja szinkronizálni az Azure AD-bérlőből származó felhasználók listáját Microsoft Graphon keresztül. Ha nem tudja, megkéri, hogy (a bérlői rendszergazda) csatlakozhasson a bérlőhöz az alkalmazáshoz.

Az alkalmazás ezután engedélyt kér a bérlőben lévő felhasználók listájának olvasásához.

![Rendszergazdai jóváhagyás](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Az engedély megadása után kijelentkezett az alkalmazásból. Ez a kijelentkezés biztosítja, hogy a Microsoft Graph összes meglévő hozzáférési jogkivonata el legyen távolítva a jogkivonat-gyorsítótárból. Ha újra bejelentkezik, a kapott friss jogkivonat rendelkezik a szükséges engedélyekkel, hogy a rendszer meghívja a Microsoft Graph.


Az engedély megadásakor az alkalmazás bármikor lekérdezheti a felhasználókat. Ezt a **felhasználók szinkronizálása** gomb kiválasztásával és a felhasználók listájának frissítésével ellenőrizheti. Próbálkozzon egy felhasználó hozzáadásával vagy eltávolításával, majd a lista újraszinkronizálásával. (De vegye figyelembe, hogy az alkalmazás csak a felhasználók első oldalát szinkronizálja.)

## <a name="about-the-code"></a>A kód ismertetése

A példához tartozó kód a következő fájlokban található:

- **App_Start \startup.auth.cs**, **Controllers\AccountController.cs**: kezdeti bejelentkezés. Különösen a vezérlő műveletei **rendelkeznek egy engedélyezési** attribútummal, amely arra kényszeríti a felhasználót, hogy jelentkezzen be. Az alkalmazás az [engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) használja a bejelentkezéshez a felhasználónak.
- **Controllers\SyncController.cs**: a felhasználók listájának szinkronizálása a helyi memóriában tárolt tárolóban.
- **Controllers\UserController.cs**: a helyi memóriában tárolt tárolóban lévő felhasználók listájának megjelenítése.
- **Controllers\AccountController.cs**: engedélyek beszerzése a bérlői rendszergazdától a rendszergazdai engedélyezési végpont használatával.

## <a name="re-create-the-sample-app"></a>A minta alkalmazás újbóli létrehozása

1. A Visual Studióban hozzon létre egy új **Visual C#** **ASP.net webalkalmazás (.NET-keretrendszer)** projektet.
1. A következő képernyőn válassza ki az **MVC** -projekt sablonját. Adja hozzá a **webes API**-hoz tartozó mappa-és alapvető referenciákat is, mivel később egy webes API-vezérlőt fog hozzáadni. Hagyja meg a projekt kiválasztott hitelesítési módját alapértelmezettként: **Nincs hitelesítés**.
1. Válassza ki a projektet a **megoldáskezelő** ablakban, és válassza ki az **F4** -kulcsot.
1. A projekt tulajdonságai között állítsa az **SSL** beállítást **igaz** értékre. Jegyezze fel az adatokat az **SSL URL-címében**. Szüksége lesz rá az alkalmazás regisztrációjának Azure Portal való konfigurálásakor.
1. Adja hozzá a következő ASP.NET OWIN köztes NuGet-csomagokat:
   - Microsoft. Owin. Security. ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft. IdentityModel. Protocol. Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft. Identity. Client
1. A **App_Start** mappában:
   1. Hozzon létre egy **Startup.auth.cs** nevű osztályt.
   1. Eltávolítás **. App_Start** a névtér nevéből.
   1. Cserélje le az **indítási** osztály kódját a minta alkalmazás ugyanazon fájljából származó kódra.
   Ügyeljen arra, hogy a teljes osztály definícióját használja. A definíció a **nyilvános osztályból** indul a **nyilvános részleges osztály indításakor.**
1. A **Startup.auth.cs**-ben a Visual Studio IntelliSense által javasolt utasítások **használatával** hárítsa el a hiányzó hivatkozásokat.
1. Kattintson a jobb gombbal a projektre, válassza a **Hozzáadás**, majd az **osztály** elemet.
1. A keresőmezőbe írja be a **OWIN** kifejezést. A **OWIN indítási osztálya** kijelölésként jelenik meg. Jelölje ki, és nevezze el a **Startup.cs** osztályt.
1. A **Startup.cs**-ben cserélje le az **indítási** osztály kódját a minta alkalmazás ugyanazon fájljából származó kódra. Azt is vegye figyelembe, hogy a definíció a **nyilvános osztályból indításról** **nyilvános, részleges indításra** vált.
1. A **modellek** mappában adjon hozzá egy új, **MsGraphUser.cs** nevű osztályt. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Adjon hozzá egy új **MVC 5 vezérlőt –** a **AccountController** nevű üres példányt. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Adjon hozzá egy új **MVC 5 vezérlőt –** a **UserController** nevű üres példányt. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Új **webes API 2 vezérlő hozzáadása – üres** példány, **SyncController** néven. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. A felhasználói felületen a **Views\Account** mappában adjon hozzá három **üres (modell nélküli) nézetet** a **GrantPermissions**, az **index** és a **UserMismatch** nevű példányhoz. Vegyen fel és egy nevesített **indexet** a **Views\User** mappában. Cserélje le a megvalósítást a mintából származó azonos nevű fájl tartalmára.
1. Frissítse a **megosztott \_ elrendezést. cshtml** és **Home\Index.cshtml** , hogy megfelelően összekapcsolja a különböző nézeteket.

## <a name="deploy-the-sample-to-azure"></a>A minta üzembe helyezése az Azure-ban

A projekt webalkalmazás-és webes API-projektekkel rendelkezik. Az Azure-webhelyekre történő üzembe helyezéshez hajtsa végre az alábbi lépéseket:

1. Hozzon létre egy Azure-webhelyet.
1. Tegye közzé a webalkalmazást és a webes API-kat a webhelyen.
1. Frissítse az ügyfeleket, hogy IIS Express helyett a webhelyet hívja meg.

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>DotNet-web-Daemon-v2 létrehozása és közzététele egy Azure-webhelyen

1. Jelentkezzen be a <a href="https://portal.azure.com/" target="_blank">Azure Portalba <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Válassza az **Erőforrás létrehozása** lehetőséget a bal felső sarokban.
1. Válassza **a**  >  **webalkalmazás** lehetőséget, majd adja meg a webhely nevét. Adja meg például a **DotNet-web-Daemon-v2-contoso.azurewebsites.net** nevet.
1. Válassza ki az **előfizetés**, az **erőforráscsoport** és az **app Service-csomag és a hely** adatait. Az **operációs rendszer** **Windows**, és a **Közzététel** **kód**.
1. Válassza a **Létrehozás** lehetőséget, és várja meg az App Service létrehozását.
1. Az **üzembe helyezés sikeres** értesítésének beszerzése után válassza az **erőforrás** megnyitása lehetőséget az újonnan létrehozott app Service-hez való ugráshoz.
1. A webhely létrehozása után keresse meg az **irányítópulton** , és válassza ki az App Service **áttekintő** képernyőjének megnyitásához.
1. Az App Service **Áttekintés** lapján töltse le a közzétételi profilt a **közzétételi profil beolvasása** hivatkozásra kattintva, és mentse. Más központi telepítési mechanizmusokat is használhat, például a forrás-vezérlőelemből való üzembe helyezést.
1. Váltson a Visual Studióra, majd:
   1. Nyissa meg a **DotNet-web-Daemon-v2** projektet.
   1. Kattintson a jobb gombbal a projektre Megoldáskezelő, majd válassza a **Közzététel** lehetőséget.
   1. Válassza a **profil importálása** lehetőséget az alsó sávban, és importálja a korábban letöltött közzétételi profilt.
1. Válassza a **Konfigurálás** lehetőséget.
1. A **kapcsolat** lapon frissítse a cél URL-címet úgy, hogy a "https"-t használja. Használja például a következőt: `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` . Kattintson a **Tovább** gombra.
1. A **Beállítások** lapon győződjön meg arról, hogy a **szervezeti hitelesítés engedélyezése** jelölőnégyzet nincs bejelölve.
1. Kattintson a **Mentés** gombra. Válassza a fő képernyő **Közzététel** elemét.

A Visual Studio közzéteszi a projektet, és automatikusan megnyit egy böngészőt a projekt URL-címére. Ha megjelenik a projekt alapértelmezett weboldala, a kiadvány sikeres volt.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Az Azure AD-bérlői alkalmazás regisztrációjának frissítése a DotNet-web-Daemon-v2-re

1. Térjen vissza a <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk** lehetőséget.
1. Válassza ki a **DotNet-web-Daemon-v2** alkalmazást.
1. Az alkalmazáshoz tartozó **hitelesítés** lapon frissítse az **előtérben KIJELENTKEZÉSI URL-címeket** a szolgáltatás címével. Használja például a következőt: `https://dotnet-web-daemon-v2-contoso.azurewebsites.net/Account/EndSession` .
1. A **branding (védjegyezés** ) menüben frissítse a **Kezdőlap URL-** címét a szolgáltatás címére. Használja például a következőt: `https://dotnet-web-daemon-v2-contoso.azurewebsites.net` .
1. Mentse a konfigurációt.
1. Adja hozzá ugyanazt az URL-címet a **hitelesítési**  >  **átirányítási URI** -k menü értékeinek listájához. Ha több átirányítási URL-címmel rendelkezik, győződjön meg arról, hogy van egy új bejegyzés, amely az App Service URI azonosítóját használja az egyes átirányítási URL-címekhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje az [alkalmazás regisztrálása](#register-your-application) lépésben létrehozott alkalmazás-objektumot.  Az alkalmazás eltávolításához kövesse az [Ön vagy a szervezete által létrehozott alkalmazás eltávolítása](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization)című témakör utasításait.

## <a name="get-help"></a>Segítség kérése

A [stack overflow](http://stackoverflow.com/questions/tagged/msal) segítségével kaphat támogatást a Közösségtől.
Először Kérdezzen rá Stack Overflow kérdéseire, és Böngésszen a meglévő problémák között, és ellenőrizze, hogy valaki megkérdezte-e a kérdést.
Győződjön meg arról, hogy a kérdései vagy megjegyzései "adal", "msal" és "DotNet" címkével rendelkeznek.

Ha hibát talál a mintában, vegye fel a problémát a GitHub- [problémákra](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Ha hibát talál a MSAL.NET-ben, akkor emelje fel a problémát a [MSAL.net GitHub-problémákkal](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues)kapcsolatban.

A javaslatok megadásához nyissa meg a [felhasználói hang lapot](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Következő lépések

További információ a Microsoft Identity platformot használó Daemon-alkalmazások létrehozásáról a védett webes API-k eléréséhez:

> [!div class="nextstepaction"]
> [Forgatókönyv: a webes API-kat meghívó alkalmazás](scenario-daemon-overview.md)
