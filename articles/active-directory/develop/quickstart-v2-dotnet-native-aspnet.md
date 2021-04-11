---
title: 'Rövid útmutató: a Microsoft Identity platform által védett ASP.NET webes API meghívása | Azure'
titleSuffix: Microsoft identity platform
description: Ebből a rövid útmutatóból megtudhatja, hogyan hívhat meg egy ASP.NET webes API-t, amelyet a Microsoft Identity platform véd a Windows asztali (WPF) alkalmazásból.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/05/2020
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: 7d802aa83e2d48e7b66980b5c06aece10a93f37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104658526"
---
# <a name="quickstart-call-an-aspnet-web-api-thats-protected-by-microsoft-identity-platform"></a>Rövid útmutató: a Microsoft Identity platform által védett ASP.NET webes API meghívása

Ebben a rövid útmutatóban egy kódrészletet tölt le és futtat, amely bemutatja, hogyan lehet védelemmel ellátni egy ASP.NET webes API-t azáltal, hogy korlátozza az erőforrásokhoz való hozzáférést csak a jogosult fiókok számára. A minta támogatja a személyes Microsoft-fiókok és-fiókok engedélyezését bármely Azure Active Directory (Azure AD) szervezeten belül.

A cikk egy Windows megjelenítési alaprendszer (WPF) alkalmazást is használ annak bemutatására, hogyan kérhet hozzáférési jogkivonatot a webes API-k eléréséhez.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Visual Studio 2017 vagy 2019. Töltse le [ingyenesen a Visual studiót](https://www.visualstudio.com/downloads/).

## <a name="clone-or-download-the-sample"></a>A minta klónozása vagy letöltése

A mintát kétféleképpen szerezheti be:

* Klónozás a rendszerhéjból vagy parancssorból:
   ```console
   git clone https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git
   ```
* [Töltse le zip-fájlként](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip).

[!INCLUDE [active-directory-develop-path-length-tip](../../../includes/active-directory-develop-path-length-tip.md)]

## <a name="register-your-web-api"></a>Webes API regisztrálása

Ebben a szakaszban regisztrálnia kell a webes API-t a Azure Portal **Alkalmazásregisztrációkban** .

### <a name="choose-your-azure-ad-tenant"></a>Azure AD-bérlő kiválasztása

Az alkalmazások manuális regisztrálásához válassza ki azt a Azure Active Directory (Azure AD) bérlőt, amelyben létre szeretné hozni az alkalmazásokat.

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, használja a felső menüben a **címtár + előfizetés** szűrőt a :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: használni kívánt bérlő kiválasztásához.

### <a name="register-the-todolistservice-app"></a>A TodoListService alkalmazás regisztrálása

1. Jelentkezzen be az <a href="https://portal.azure.com/" target="_blank">Azure Portalra</a>.
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt**.
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk**  >  **új regisztráció** lehetőséget.
1. Adja meg az alkalmazás **nevét** , például: `AppModelv2-NativeClient-DotNet-TodoListService` . Előfordulhat, hogy az alkalmazás felhasználói láthatják ezt a nevet, és később is megváltoztathatók.
1. A **támogatott fióktípus** esetében válassza a **fiókok lehetőséget bármely szervezeti címtárban**.
1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.
1. Az alkalmazás **– Áttekintés** oldalon keresse meg az **alkalmazás (ügyfél) azonosító** értékét, majd jegyezze fel későbbi használatra. Ehhez a projekthez a Visual Studio konfigurációs fájlját (azaz `ClientId` a *TodoListService\Web.config* fájlban) kell konfigurálnia.
1. A **kezelés** területen válassza **az API**  >  **hozzáadása hatókör** lehetőséget. Fogadja el a javasolt alkalmazás-azonosító URI-t ( `api://{clientId}` ) a **Mentés és folytatás** lehetőség kiválasztásával, majd adja meg a következő adatokat:

    1. A **hatókör neve** mezőbe írja be a következőt: `access_as_user` .
    1. Válassza ki a **rendszergazdák és a felhasználók** lehetőséget, hogy **ki is** jogosult legyen.
    1. A **rendszergazdai engedély megjelenítendő neve** mezőbe írja be a (z `Access TodoListService as a user` ) értéket.
    1. A **rendszergazdai engedély leírása** mezőbe írja be a értéket `Accesses the TodoListService web API as a user` .
    1. A **felhasználó beleegyezik megjelenítendő neve** mezőbe írja be a (z `Access TodoListService as a user` ) értéket.
    1. A **felhasználó beleegyezik leírása** mezőbe írja be a értéket `Accesses the TodoListService web API as a user` .
    1. **Állapot** esetén tartsa be a következőt: **engedélyezve**.
1. Válassza a **hatókör hozzáadása** elemet.

### <a name="configure-the-service-project"></a>A szolgáltatási projekt konfigurálása

Konfigurálja a szolgáltatási projektet úgy, hogy az megfeleljen a regisztrált webes API-nak a következő módon:

1. Nyissa meg a megoldást a Visual Studióban, majd nyissa meg a *Web.config* fájlt a TodoListService-projekt gyökerében.

1. Cserélje le a paraméter értékét az `ida:ClientId` ügyfél-azonosító (Application ID) értékre a **Alkalmazásregisztrációk** -portálon imént regisztrált alkalmazásból.

### <a name="add-the-new-scope-to-the-appconfig-file"></a>Az új hatókör hozzáadása a app.config fájlhoz

Az új hatókör TodoListClient *app.config* fájlhoz való hozzáadásához tegye a következőket:

1. A TodoListClient projekt gyökérkönyvtárában nyissa meg a *app.config* fájlt.

1. Illessze be az alkalmazás AZONOSÍTÓját a paraméterben a TodoListService-projekthez imént regisztrált alkalmazásból `TodoListServiceScope` , és cserélje le a `{Enter the Application ID of your TodoListService from the app registration portal}` karakterláncot.

  > [!NOTE]
  > Győződjön meg arról, hogy az alkalmazás azonosítója a következő formátumot használja: `api://{TodoListService-Application-ID}/access_as_user` (ahol `{TodoListService-Application-ID}` a a TodoListService-alkalmazáshoz tartozó alkalmazás-azonosítót jelképező GUID).

## <a name="register-the-todolistclient-client-app"></a>A TodoListClient-ügyfélalkalmazás regisztrálása

Ebben a szakaszban regisztrálnia kell a TodoListClient alkalmazást a Azure Portal **Alkalmazásregisztrációkban** , majd konfigurálnia kell a kódot a TodoListClient projektben. Ha az ügyfél és a kiszolgáló *ugyanazt az alkalmazást* veszi figyelembe, a 2. lépésben regisztrált alkalmazást is újra felhasználhatja. Használja ugyanazt az alkalmazást, ha azt szeretné, hogy a felhasználók személyes Microsoft-fiókkal jelentkezzenek be.

### <a name="register-the-app"></a>Az alkalmazás regisztrálása

A TodoListClient alkalmazás regisztrálásához tegye a következőket:

1. Nyissa meg a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) portált.
1. Válassza az **új regisztráció** lehetőséget.
1. Amikor megnyílik az **alkalmazás regisztrálása lap** , adja meg az alkalmazás regisztrációs adatait:

    1. A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára (például **NativeClient-DotNet-TodoListClient**).
    1. A **támogatott fióktípus** esetében válassza a **fiókok lehetőséget bármely szervezeti címtárban**.
    1. Válassza a **Regisztráció** elemet az alkalmazás létrehozásához.

   > [!NOTE]
   > A TodoListClient projektben *app.config* fájl alapértelmezett értéke a következő: `ida:Tenant` `common` . Lehetséges értékek:
   > - `common`: Munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal is bejelentkezhet (mivel a 3b. lépésben **bármelyik szervezeti címtárban** kiválasztotta a fiókokat).
   > - `organizations`: Munkahelyi vagy iskolai fiókkal is bejelentkezhet.
   > - `consumers`: Csak személyes Microsoft-fiókkal lehet bejelentkezni.

1. Az alkalmazás **áttekintése** lapon válassza a **hitelesítés** lehetőséget, majd tegye a következőket:

    1. A **platform-konfigurációk** területen válassza a **platform hozzáadása** gombot.
    1. **Mobil-és asztali alkalmazások** esetében válassza a **mobil-és asztali alkalmazások** lehetőséget.
    1. Az **átirányítási URI**-k esetében jelölje be a **https://login.microsoftonline.com/common/oauth2/nativeclient** jelölőnégyzetet.
    1. Válassza a **Konfigurálás** lehetőséget.

1. Válassza az **API-engedélyek** lehetőséget, majd tegye a következőket:

    1. Nyomja meg **Az engedély hozzáadása** gombot.
    1. Válassza a **saját API** -k fület.
    1. Az API-k listájában válassza a **AppModelv2-NativeClient-DotNet-TODOLISTSERVICE API** vagy a webes API-hoz megadott nevet.
    1. Ha még nincs kiválasztva, jelölje be a **access_as_user** engedély jelölőnégyzetet. Ha szükséges, használja a keresőmezőt.
    1. Kattintson az **engedélyek hozzáadása** gombra.

### <a name="configure-your-project"></a>A projekt konfigurálása

A TodoListClient-projekt konfigurálásához tegye a következőket:

1. Az **Alkalmazásregisztrációk** portál **Áttekintés** lapján másolja az **alkalmazás (ügyfél) azonosítójának** értékét.

1. A TodoListClient projekt gyökérkönyvtárában nyissa meg a *app.config* fájlt, majd illessze be az alkalmazás-azonosító értékét a `ida:ClientId` paraméterbe.

## <a name="run-your-todolistclient-project"></a>A TodoListClient-projekt futtatása

A TodoListClient-projekt futtatásához tegye a következőket:

1. Nyomja le az F5 billentyűt a TodoListClient-projekt megnyitásához. A projekt oldalának nyitva kell lennie.

1. A jobb felső sarokban válassza a **Bejelentkezés** lehetőséget, majd jelentkezzen be ugyanazokkal a hitelesítő adatokkal, amelyeket az alkalmazás regisztrálásához használt, vagy jelentkezzen be felhasználóként ugyanabban a címtárban.

   Ha első alkalommal jelentkezik be, előfordulhat, hogy a rendszer a TodoListService webes API-hoz való hozzájárulásukat kéri.

   A TodoListService webes API elérésének és a *Tennivalók* kezelésének megkönnyítéséhez a bejelentkezés a *access_as_user* hatóköréhez is hozzáférési jogkivonatot kér.

## <a name="pre-authorize-your-client-application"></a>Az ügyfélalkalmazás előzetes engedélyezése

Az egyik lehetőség, hogy más címtárakból származó felhasználók is hozzáférjenek a webes API-hoz, az ügyfélalkalmazás előzetes engedélyezése a webes API-hoz való hozzáféréshez. Ezt úgy teheti meg, hogy hozzáadja az alkalmazás AZONOSÍTÓját az ügyfélalkalmazás a webes API-hoz tartozó, előre jóváhagyott alkalmazások listájához. Egy előre engedélyezett ügyfél hozzáadásával engedélyezheti a felhasználók számára a webes API elérését anélkül, hogy meg kellene adni a beleegyezett. Az ügyfélalkalmazás előzetes engedélyezéséhez tegye a következőket:

1. A **Alkalmazásregisztrációk** portálon nyissa meg a TodoListService-alkalmazás tulajdonságait.
1. Az **API közzététele** szakasz a **jóváhagyott ügyfélalkalmazások** területen válassza az **ügyfélalkalmazás hozzáadása** elemet.
1. Az **ügyfél-azonosító** mezőben illessze be a TODOLISTCLIENT alkalmazás azonosítóját.
1. A **jogosultsági hatókörök** szakaszban válassza ki a `api://<Application ID>/access_as_user` webes API hatókörét.
1. Válassza az **alkalmazás hozzáadása** lehetőséget.

### <a name="run-your-project"></a>A projekt futtatása

1. A projekt futtatásához nyomja le az F5 billentyűt. A TodoListClient alkalmazásnak nyitva kell lennie.
1. A jobb felső sarokban válassza a **Bejelentkezés** lehetőséget, majd jelentkezzen be egy személyes Microsoft-fiók, például a Live.com vagy a hotmail.com, vagy egy munkahelyi vagy iskolai fiók használatával.

## <a name="optional-limit-sign-in-access-to-certain-users"></a>Nem kötelező: a bejelentkezési hozzáférés korlátozása bizonyos felhasználók számára

Alapértelmezés szerint, ha követte a fenti lépéseket, a személyes fiókok, például a outlook.com vagy a live.com, valamint az Azure AD-vel integrált szervezetek munkahelyi vagy iskolai fiókjai igényelhetnek jogkivonatokat, és hozzáférhetnek a webes API-hoz.

Annak megadásához, hogy ki tud bejelentkezni az alkalmazásba, használja az alábbi lehetőségek egyikét:

### <a name="option-1-limit-access-to-a-single-organization-single-tenant"></a>1. lehetőség: egyetlen szervezethez való hozzáférés korlátozása (egyetlen bérlő)

Az alkalmazáshoz való bejelentkezési hozzáférést korlátozhatja egyetlen Azure AD-bérlőben lévő felhasználói fiókokra, beleértve a bérlő *vendég fiókjait* is. Ez a forgatókönyv az üzletági *alkalmazások* esetében gyakori.

1. Nyissa meg a *App_Start \startup.auth* fájlt, majd módosítsa a metaadatok végpontjának értékét a `OpenIdConnectSecurityTokenProvider` -ba `"https://login.microsoftonline.com/{Tenant ID}/v2.0/.well-known/openid-configuration"` . A bérlő nevét is használhatja, például: `contoso.onmicrosoft.com` .
2. Ugyanebben a fájlban állítsa be a `ValidIssuer` tulajdonságot a to értékre `TokenValidationParameters` `"https://sts.windows.net/{Tenant ID}/"` , és állítsa be az `ValidateIssuer` argumentumot a `true` következőre:.

### <a name="option-2-use-a-custom-method-to-validate-issuers"></a>2. lehetőség: egyéni módszer használata a kibocsátók érvényesítéséhez

Egyéni módszert is alkalmazhat a kiállítók érvényesítésére a paraméter használatával `IssuerValidator` . A paraméterrel kapcsolatos további információkért lásd: [TokenValidationParameters osztály](/dotnet/api/microsoft.identitymodel.tokens.tokenvalidationparameters).

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Következő lépések
További információ a Microsoft Identity platform által támogatott védett webes API-forgatókönyvről:
> [!div class="nextstepaction"]
> [Védett webes API-forgatókönyv](scenario-protected-web-api-overview.md)
