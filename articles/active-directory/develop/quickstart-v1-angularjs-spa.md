---
title: AngularJS egyoldalas alkalmazás létrehozása bejelentkezéshez és kijelentkezés az Azure AD-vel | Microsoft Docs
description: Útmutató a bejelentkezés céljából az Azure AD-vel integrálható, és az Azure AD által védett API-kat OAuth használatával meghívó egyoldalas AngularJS-alkalmazás létrehozásához.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: javascript
ms.topic: quickstart
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8e97c985d0a19842e4a514705e3e34665eeb499
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921254"
---
# <a name="quickstart-build-an-angularjs-single-page-app-for-sign-in-and-sign-out-with-azure-active-directory"></a>Rövid útmutató: Egyoldalas AngularJS-alkalmazás létrehozása az Azure Active Directoryval történő bejelentkezéshez és kijelentkezéshez

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

> [!IMPORTANT]
> A [Microsoft Identity platform](v2-overview.md) a Azure Active Directory (Azure ad) fejlesztői platform fejlődése. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat építsenek, amelyek az összes Microsoft-identitás használatával biztonságosan jelentkeznek be, és jogkivonattal hívják meg az olyan Microsoft API-kat, mint a Microsoft Graph, vagy olyan API-kat, amelyeket fejlesztők készítettek.
> Ha a munkahelyi és iskolai fiókok mellett engedélyeznie kell a bejelentkezést a személyes fiókokhoz, használhatja a *[Microsoft Identity platform végpontját](azure-ad-endpoint-comparison.md)* .
> Ez a rövid útmutató a régebbi, az Azure AD 1.0-s verziójának végpontja. Javasoljuk, hogy a 2.0-s végpontot használja új projektekhez. További információért tekintse meg [ezt a JavaScript Spa-oktatóanyagot](tutorial-v2-javascript-spa.md) , valamint a *Microsoft Identity platform végpontját*ismertető [cikket](active-directory-v2-limitations.md) .

Az Azure Active Directory (Azure AD) révén egyszerűen és magától értetődő módon adhat hozzá bejelentkezési és kijelentkezési funkciót, továbbá biztonságos OAuth API-hívásokat az egyoldalas alkalmazásokhoz. A szolgáltatás lehetővé teszi az alkalmazások számára a felhasználók hitelesítését a felhasználók Windows Server Active Directory-fiókjával, valamint az olyan webes API-k használatát, amelyek védelméhez az Azure AD nyújt segítséget. Ilyenek például az Office 365 API-k vagy az Azure API.

A böngészőben futó JavaScript-alkalmazások esetén az Azure AD az Active Directory Authentication Libraryt (ADAL) vagy adal.js-t biztosítja. Az adal.js egyetlen célja a hozzáférési jogkivonat lekérésének a megkönnyítése az alkalmazások számára.

Ebben a rövid útmutatóban megtanulhatja, hogy miként hozhat létre egy olyan teendőlistás AngularJS-alkalmazást, amely:

* Bejelentkezteti a felhasználót az alkalmazásba az Azure AD-t használva identitásszolgáltatóként.
* Információkat jelenít meg a felhasználóval kapcsolatban.
* Biztonságos módon meghívja az alkalmazás teendőlista API-ját az Azure AD-ből származó tulajdonosi jogkivonatok használatával.
* Kijelentkezteti a felhasználót az alkalmazásból.

Egy teljes körű, működő alkalmazás létrehozásához az alábbiakat kell tennie:

1. Az alkalmazás regisztrálása az Azure AD-ben.
2. Az ADAL telepítése, és az egyoldalas alkalmazás konfigurálása.
3. Az ADAL használata az egyoldalas alkalmazás oldalainak védelemmel való ellátásához.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként végre kell hajtania az alábbi lépéseket:

* [Töltse le az alkalmazás vázát](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) vagy [a kész mintát](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).
* Rendelkeznie kell egy Azure AD-bérlővel, amelyben felhasználókat hozhat létre, és regisztrálhat egy alkalmazást. Ha még nem rendelkezik bérlővel, [itt megtudhatja, hogyan tehet szert egyre](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>1\. lépés: A DirectorySearcher alkalmazás regisztrálása

Ha lehetővé szeretné tenni az alkalmazás számára a felhasználók hitelesítését és a jogkivonatok lekérését, először regisztrálnia kell az alkalmazást az Azure AD-bérlőben:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
1. Ha több címtárba jelentkezett be, lehetséges, hogy ellenőriznie kell, hogy a megfelelő címtárat látja-e. Ehhez kattintson a fiókjára a felső sávon. A **Címtár** listában válassza ki azt az Azure AD-bérlőt, amelyben az alkalmazást regisztrálni kívánja.
1. Kattintson a **Minden szolgáltatás** lehetőségre a bal oldali panelen, majd válassza az **Azure Active Directory** elemet.
1. Kattintson a **Alkalmazásregisztrációk**elemre, majd válassza az **új regisztráció**lehetőséget.
1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
1. Válassza ki az **átirányítási URI** **szakaszban található webplatformot** , és állítsa be `https://localhost:44326/` értékre (az Azure ad által visszaadott jogkivonatok helyét).
1. Miután végzett, válassza a **Regisztrálás** lehetőséget. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét.
1. Az Adal.js az OAuth implicit folyamatát használja az Azure AD-vel folytatott kommunikációhoz. Engedélyeznie kell az alkalmazás implicit folyamatát. A regisztrált alkalmazás bal oldali navigációs paneljén válassza a **hitelesítés**lehetőséget.
1. Az **Implicit engedélyek** alatt a **Speciális beállításoknál** jelölje be mind az **Azonosító-jogkivonatok**, mind a **Hozzáférési jogkivonatok** jelölőnégyzetet. Az azonosító jogkivonatok és hozzáférési tokenek szükségesek, mivel az alkalmazásnak be kell jelentkeznie a felhasználókba, és hívnia kell egy API-t.
1. Kattintson a **Mentés** gombra.
1. Biztosítson engedélyeket a bérlőben az alkalmazás számára. Lépjen az **API-engedélyek**elemre, és válassza a jogosultság **megadása a rendszergazdai jóváhagyáshoz** **lehetőséget.**
1. Válassza az **Igen** lehetőséget a megerősítéshez.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>2\. lépés: Az ADAL telepítése, és az egyoldalas alkalmazás konfigurálása

Mivel már rendelkezik egy alkalmazással az Azure AD-ben, telepítheti az adal.js-t, és megírhatja az identitáshoz kapcsolódó kódot.

### <a name="configure-the-javascript-client"></a>A JavaScript-ügyfél konfigurálása

Kezdje az adal.js a TodoSPA projekthez történő hozzáadásával, a csomagkezelő konzolt használva:

1. Töltse le az [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) szkriptet, és adja hozzá az `App/Scripts/` projektkönyvtárhoz.
2. Töltse le az [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) szkriptet, és adja hozzá az `App/Scripts/` projektkönyvtárhoz.
3. Az egyes szkripteket az `index.html` `</body>` elemének a vége előtt töltse be:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>A háttérkiszolgáló konfigurálása

Ahhoz, hogy az egyoldalas alkalmazás teendőlistás háttér-API-ja elfogadhasson jogkivonatokat a böngészőtől, a háttérrendszernek szüksége van az alkalmazásregisztrációval kapcsolatos konfigurációs adatokra. A TodoSPA projektben nyissa meg a `web.config` fájlt. Cserélje le az `<appSettings>` szakaszban található elemek értékét az Azure Portalon használt értékekre. A kód ezekre az értékekre fog hivatkozni az ADAL használatakor.

   * `ida:Tenant` – az Azure AD-bérlő tartománya, például: contoso.onmicrosoft.com.
   * A `ida:Audience` az alkalmazás portálról kimásolt ügyfél-azonosítója.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>3\. lépés: Az ADAL használata az egyoldalas alkalmazás oldalainak védelemmel való ellátásához

Az adal.js AngularJS-útvonallal és HTTP-szolgáltatókkal integrálható, így könnyebben láthatja el védelemmel az egyoldalas alkalmazás egyes nézeteit.

1. Az `App/Scripts/app.js` fájlban hívja meg az adal.js modult:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicializálja az `adalProvider` szolgáltatót az alkalmazásregisztráció, valamint az `App/Scripts/app.js` konfigurációs értékeit használva:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. A `TodoList` nézet védelemmel való ellátását elősegítendő csak egy sor kódot használjon: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Összefoglalás

Rendelkezik egy biztonságos egyoldalas alkalmazással, amely képes a felhasználók bejelentkeztetésére, valamint tulajdonosi jogkivonatokkal védett kérések kezdeményezésére a háttér-API-ja felé. Amikor egy felhasználó a **TodoList** hivatkozásra kattint, az adal.js automatikusan az Azure AD-be irányítja át a bejelentkezéshez, ha szükséges. Az adal.js emellett automatikusan csatolni fog egy hozzáférési jogkivonatot az alkalmazás háttérrendszerének küldött Ajax-kérések mindegyikéhez.

Az előző lépések pusztán a minimálisan szükséges lépések ahhoz, hogy egy egyoldalas alkalmazást az adal.js használatával létrehozhasson. Néhány további szolgáltatás azonban szintén hasznos lehet az egyoldalas alkalmazásban:

* A bejelentkezési és kijelentkezési kérések explicit kiadásához függvényeket definiálhat a vezérlőkben az adal.js meghívásához. Az `App/Scripts/homeCtrl.js` szkriptben:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Érdemes megjeleníteni a felhasználóval kapcsolatos információkat az alkalmazás felhasználói felületén. Az ADAL szolgáltatás hozzáadása a `userDataCtrl` vezérlőhöz már megtörtént, így a `userInfo` objektumot a társított `App/Views/UserData.html` nézetben érheti el:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Számos olyan forgatókönyv van, amikor célszerű tudnia, hogy a felhasználó bejelentkezett-e vagy sem. A `userInfo` objektumot ezen információk gyűjtéséhez is használhatja. Az `index.html` fájl segítségével például megjelenítheti a **Bejelentkezés** vagy a **Kijelentkezés** gombot a hitelesítési állapot alapján:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Az Azure AD-be integrált egyoldalas alkalmazás hitelesítheti a felhasználókat, biztonságos módon meghívhatja a háttérrendszerét az OAuth 2.0 használatával, és alapvető információkat kérhet le a felhasználóval kapcsolatban. Ha eddig még nem tette meg, itt az ideje a bérlő néhány felhasználóval történő feltöltésének. Futtassa az egyoldalas teendőlista alkalmazást, és jelentkezzen be ezen felhasználók valamelyikével. Adjon hozzá feladatokat a felhasználó teendőlistájához, jelentkezzen ki, majd jelentkezzen be újra.

Az adal.js segítségével egyszerűen építheti be az alkalmazásba ezeket az általános identitáskezelési szolgáltatásokat. Elvégzi az összes piszkos munkát, többek között a gyorsítótár kezelését, az OAuth protokoll támogatását, a bejelentkezésre szolgáló felhasználói felületet biztosít a felhasználónak, és frissíti a lejárt jogkivonatokat.

A referenciaként használható kész minta (a konfigurációs értékek nélkül) a [GitHubon](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) érhető el.

## <a name="next-steps"></a>Következő lépések

Most már továbbléphet a további forgatókönyvekre.

> [!div class="nextstepaction"]
> [CORS webes API meghívása egyoldalas alkalmazásból](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).
