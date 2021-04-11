---
title: Kód minták a Microsoft Identity platformhoz
description: Az elérhető Microsoft Identity platform-mintakód-minták indexét kínálja forgatókönyv szerint rendezve.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a19ca14a37bd24e499fdf0681b3510e9a4a8ea3f
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075032"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity platform Code Samples (v 2.0 végpont)

A Microsoft Identity platform használatával a következőket végezheti el:

- Adja hozzá a hitelesítést és az engedélyezést a webalkalmazásokhoz és a webes API-khoz.
- Hozzáférési jogkivonat megkövetelése egy védett webes API eléréséhez.

Ez a cikk röviden leírja, és a Microsoft Identity platformra mutató hivatkozásokat tartalmaz. Ezek a minták bemutatják, hogyan végezheti el, és az alkalmazásaiban használható kódrészleteket is megadhat. A kód mintája lapon a követelményekkel, a telepítéssel és a telepítéssel kapcsolatos részletes információkkal foglalkozó témaköröket talál. A kódban található megjegyzések segítenek megérteni a kritikus szakaszt.

Az egyes típusok alapvető forgatókönyvének megismeréséhez tekintse meg [a Microsoft Identity platform alkalmazás-típusai](v2-app-types.md)című témakört.

Emellett a GitHubon is hozzájárulhat a mintákhoz. További információ: [Microsoft Azure Active Directory minták és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Ezek a minták azt mutatják be, hogyan lehet egy egyoldalas alkalmazást biztonságossá tennie a Microsoft Identity platformmal. Ezek a minták a MSAL.js egyikét használják.

| Platform | Leírás | Hivatkozás |
| -------- | --------------------- | -------- |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-hívások Microsoft Graph |[JavaScript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) látható | SPA-hívások Microsoft Graph az Auth Code flow PKCE |[JavaScript – v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-hívások B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) látható | SPA meghívása a B2C-vel az Auth Code flow-t/PKCE |[B2C-JavaScript-Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) látható | A SPA meghívja az egyéni webes API-t, amely viszont meghívja a Microsoft Graph  | [MS-Identity-JavaScript-oktatóanyag-chapter4-OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph) |
| ![Ez a képen látható szögletes embléma ](media/sample-v2-code/logo_angular.png) [(szögletes MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-hívások Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp-szögletes](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása látható ](media/sample-v2-code/logo_angular.png) [(MSAL szögletes 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-hívások Microsoft Graph az Auth Code flow PKCE | [MS-Identity-JavaScript-szögletes-Spa](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása látható ](media/sample-v2-code/logo_angular.png) [(MSAL szögletes 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)| SPA-hívások egyéni webes API | [MS-Identity-JavaScript-szögletes-Spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Ez a képen látható szögletes embléma ](media/sample-v2-code/logo_angular.png) [(szögletes MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | SPA-hívások B2C |[Active-Directory-B2C-JavaScript-szögletes-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása látható ](media/sample-v2-code/logo_angular.png) [(MSAL szögletes 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | A SPA meghívja az egyéni webes API-t az alkalmazás szerepköreivel és biztonsági csoportjaival |[MS-Identity-JavaScript-szögletes-Spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |
| ![Ez a képen a reagáló embléma ](media/sample-v2-code/logo_react.png) [reagál (MSAL reagál)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA-hívások Microsoft Graph az Auth Code flow PKCE | [MS-Identity-JavaScript-reakciós-Spa](https://github.com/Azure-Samples/ms-identity-javascript-react-spa) |
| ![Ez a képen a reagáló embléma ](media/sample-v2-code/logo_react.png) [reagál (MSAL reagál)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| SPA-hívások egyéni webes API | [MS-Identity-JavaScript-reagálás-oktatóanyag](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api) |
| ![Ez a rendszerkép az reagáló logót mutatja ](media/sample-v2-code/logo_react.png) [(MSAL.js 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| A SPA meghívja az egyéni webes API-t, amely viszont meghívja a Microsoft Graph  | [MS-Identity-JavaScript-reakciós-Spa-dotnetcore-webapi-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Ez a képen a Blazer logo ](media/sample-v2-code/logo-blazor.png) [Blazer webszerelvénye (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) látható | A Blazer webszerelvényének oktatóanyaga a felhasználók bejelentkezéséhez és az API-k hívásához Azure Active Directory |[MS-Identity-Blazer-wasm](https://github.com/Azure-Samples/ms-identity-blazor-wasm) |

## <a name="web-applications"></a>Webalkalmazások

Az alábbi példák bemutatják a felhasználók bejelentkezési webalkalmazásait. Néhány minta azt is bemutatja, hogy az alkalmazás meghívja a Microsoft Grapht, vagy a saját webes API-ját a felhasználó identitásával.

| Platform | Csak a felhasználókhoz tartozó jelek | A felhasználók és a hívások jelei Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [ASP.NET Core WebApp Signs-in Users oktatóanyag](https://aka.ms/aspnetcore-webapp-sign-in) | Ugyanaz a minta a [ASP.net Core Web App calls Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fázisban</p>Speciális példa [a bejelentkezett felhasználó jogkivonat-gyorsítótárának elérésére a háttérben futó alkalmazások, API-k és szolgáltatások](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) segítségével |
| ![Ez a képen a ASP.NET-keretrendszer emblémája látható](media/sample-v2-code/logo_NETframework.png)</p>ASP.NET Core | [AD FS az Azure ad Application Migration forgatókönyvét a fejlesztők](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) számára, hogy megtudja, hogyan lehet biztonságosan és biztonságosan migrálni az alkalmazásokat a Active Directory összevonási szolgáltatások (AD FS) (AD FS) szolgáltatással integrált Azure Active Directory (Azure ad) | |
| ![Ez a képen a ASP.NET-keretrendszer emblémája látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET gyors útmutató](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [DotNet-WebApp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [DotNet-rendszergazda-korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> [msgraph – képzés – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  |[Java servlet-oktatóanyag – 1,1. fejezet](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Bejelentkezés a HRE| |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  |[Java servlet-oktatóanyag – 1,2. fejezet](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Bejelentkezés B2C-vel |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  | | [Java servlet-oktatóanyag – 2,1. fejezet](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Bejelentkezés a HRE és a Call Graph szolgáltatással|
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  |[Java servlet-oktatóanyag – 3,1. fejezet](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Jelentkezzen be a HRE, és szabályozza a hozzáférést a roles jogcímhez| |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  | | [Java servlet-oktatóanyag – 3,2. fejezet](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Jelentkezzen be a HRE, és szabályozza a hozzáférést a groups jogcímhez|
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | |[Java servlet-oktatóanyag – 4,1. fejezet](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/blob/main/README.md) Üzembe helyezés Azure App Service|
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  | | [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  | [MS-Identity-B2C-Java-servlet-WebApp-hitelesítés](https://github.com/Azure-Samples/ms-identity-b2c-java-servlet-webapp-authentication)|  |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL csomópont) | [Expressz webalkalmazás-bejelentkezések – felhasználói oktatóanyag](https://github.com/Azure-Samples/ms-identity-node) | |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | [Python-lombik – oktatóanyag – 1,1. fejezet](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Bejelentkezés a HRE  |  |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | [Python-lombik – oktatóanyag – 1,2. fejezet](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Bejelentkezés B2C-vel                    |  |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | | [Python-lombik – oktatóanyag – 2,1. fejezet](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Bejelentkezés a HRE és a Call Graph szolgáltatással |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | |[Python-lombik – oktatóanyag – 3,1. fejezet](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial/blob/main/README.md) Üzembe helyezés Azure App Service  |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | [Python Django-oktatóanyag – 1,1. fejezet](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)   Bejelentkezés a HRE  | |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | [Python Django-oktatóanyag – 1,2. fejezet](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Bejelentkezés B2C-vel                    |  |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | | [Python Django-oktatóanyag – 2,1. fejezet](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md)  Bejelentkezés a HRE és a Call Graph szolgáltatással|
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | | [Python Django-oktatóanyag – 3,1. fejezet](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/blob/main/README.md) Üzembe helyezés Azure App Service                    |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | | [Python Flask-webalkalmazás](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ez a képen a Ruby embléma látható](media/sample-v2-code/logo_ruby.png) |                   | [msgraph – képzés – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |
| ![Ez a képen a Blazer embléma látható](media/sample-v2-code/logo-blazor.png)</p>Blazor Server | [A Blazer Server alkalmazás bejelentkezési felhasználói oktatóanyaga](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC) | [A Blazer Server-alkalmazások hívása Microsoft Graph](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph)</p>Chapterwise-oktatóanyag: [a Blazer Server-alkalmazás a felhasználók bejelentkezéséhez és az API-k hívásához Azure Active Directory](https://github.com/Azure-Samples/ms-identity-blazor-server) |

## <a name="desktop-and-mobile-public-client-apps"></a>Asztali és mobil nyilvános ügyfélalkalmazások

Az alábbi példák olyan nyilvános ügyfélalkalmazások (asztali vagy mobil alkalmazások) megjelenítését mutatják be, amelyek hozzáférnek a Microsoft Graph API-hoz vagy a saját webes API-ját egy felhasználó nevében. Az *asztalon (konzol) és a WAM* -mintán kívül az összes ügyfélalkalmazás a Microsoft Authentication Library (MSAL) alkalmazást használja.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph | ASP.NET Core webes API meghívása |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Asztali oktatóanyag (.NET Core) – opcionálisan a következő használatával:</p>– a platformfüggetlen jogkivonat-gyorsítótár</p>– egyéni webes felhasználói felület | ![Ez a rendszerkép a .NET/C# emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code)| [MS-Identity-DotNet-Desktop-oktatóanyag](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| Asztali (WPF)      | ![Ez a rendszerkép a .NET Desktop/C# emblémát jeleníti meg](media/sample-v2-code/logo_NET.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code)| [DotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Asztal (konzol)   | ![A .NET/C# (asztali) emblémát megjelenítő rendszerkép](media/sample-v2-code/logo_NET.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) | [DotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Asztal (konzol)   | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Asztal (konzol)   | ![Ez a .NET/C# (asztali) embléma](media/sample-v2-code/logo_NETcore.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[dotnetcore – v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Asztal (konzol) a WAM-vel  | ![Ez a .NET/C# (asztali) emblémája](media/sample-v2-code/logo_NETcore.png) | Interaktív a [web Account Managerrel](/windows/uwp/security/web-account-manager) (WAM) |[DotNet-Native-uwp-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Asztal (konzol)   | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Asztal (konzol)   | ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python-asztali](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Asztali (Electron)   | ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL csomópont) | [Engedélyezési kód (PKCE)](msal-authentication-flows.md#authorization-code) |[MS-Identity-JavaScript-NodeJS-Desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Ez a rendszerkép a .NET/C# (Xamarin) emblémát jeleníti meg](media/sample-v2-code/logo_xamarin.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |[xamarin – natív – v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![A képen az iOS/Objective-C vagy a Swift látható](media/sample-v2-code/logo_iOS.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |[iOS-Swift-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – natív-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Asztali (macOS)       | macOS | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |[macOS-Swift-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android – Java)   | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android – Kotlin)   | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Démonalkalmazások

A következő minták egy olyan alkalmazást mutatnak be, amely a saját identitásával fér hozzá a Microsoft Graph API-hoz (felhasználó nélkül).

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Ez a rendszerkép a .NET Core emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [dotnetcore-Daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webalkalmazás | ![Képernyőkép, amely a ASP.NET emblémát jeleníti meg.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [DotNet-Daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-Daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (MSAL csomópont)| [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [MS-Identity-JavaScript-NodeJS-konzol](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| Console | ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Fej nélküli alkalmazások

Az alábbi példa egy, a webböngészőt nem használó eszközön futó nyilvános ügyfélalkalmazás. Az alkalmazás lehet egy parancssori eszköz, egy Linux vagy Mac rendszerű alkalmazás, vagy egy IoT-alkalmazás. A minta olyan alkalmazást tartalmaz, amely a Microsoft Graph API-t használja egy olyan felhasználó nevében, aki egy másik eszközön (például egy mobiltelefonon) interaktívan jelentkezik be. Ez az ügyfélalkalmazás a Microsoft Authentication Library (MSAL) szolgáltatást használja.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Asztal (konzol)   | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Asztal (konzol)   | ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[MS-Identity-Python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások

Az alábbi példák bemutatják, hogyan konfigurálhatja az alkalmazást úgy, hogy fogadja a bejelentkezéseket bármely Azure Active Directory (Azure AD) bérlőről. Ha úgy konfigurálja az alkalmazást, hogy *több-bérlős* legyen, azt jelenti, hogy egy **szolgáltatott szoftver** (SaaS) alkalmazás számos szervezet számára elérhetővé válik, lehetővé téve a felhasználóknak az alkalmazásba való bejelentkezést a beleegyező engedély megadása után.

| Platform | Leírás | Hivatkozás |
| -------- | --------------------- | -------- |
| ![Ez a képen a szögletes embléma szögletes ábrázolása látható ](media/sample-v2-code/logo_angular.png) [(MSAL szögletes 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Több-bérlős SPA-hívások Graph API |[MS-Identity-JavaScript-szögletes-Spa-ASPNET-webapi-több-bérlős](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása látható ](media/sample-v2-code/logo_angular.png) [(MSAL szögletes 2,0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | Több-bérlős SPA több-bérlős egyéni webes API-t hív meg |[MS-Identity-JavaScript-szögletes-Spa-ASPNET-webapi-több-bérlős](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Ez a rendszerkép a ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.net Core-t (MSAL.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) jeleníti meg | ASP.NET Core MVC webalkalmazás-hívások Graph API |[Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Ez a rendszerkép a ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.net Core-t (MSAL.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) jeleníti meg | ASP.NET Core MVC webalkalmazás meghívása ASP.NET Core webes API-val |[Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Webes API-k

Az alábbi példák bemutatják, hogyan lehet védelemmel ellátni egy webes API-t a Microsoft Identity platformmal, és hogyan hívhat meg egy alsóbb rétegbeli API-t a webes API-ból.

| Platform | Sample |
| -------- | ------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core web API (szolgáltatás) a [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NET.png)</p>ASP.NET, MVC | [MS-Identity-ASPNET-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) webes API (szolgáltatás) |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) webes API (szolgáltatás) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| Az [Active Directory-JavaScript-NodeJS-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) webes API (szolgáltatás) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| B2C webes API (szolgáltatás) az [Active Directory-B2C-JavaScript-NodeJS-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions webes API-kként

Az alábbi példák azt mutatják be, hogyan lehet védelemmel ellátni egy Azure-függvényt a HttpTrigger használatával, és hogyan tehetők elérhetővé a webes API-k a Microsoft Identity platformmal, valamint hogyan hívható le egy alsóbb rétegbeli API a

| Platform | Sample |
| -------- | ------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core web API (szolgáltatás) Azure-függvény a [DotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) web API (szolgáltatás) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| ANode.js és a Passport webes API-je (szolgáltatás) [ – Azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js (Passport.js)| A [Node.js és a Passport-Azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) webes API-je (szolgáltatás) a következő nevében: |

## <a name="other-microsoft-graph-samples"></a>Egyéb Microsoft Graph minták

A Microsoft Graph API különböző használati mintáit bemutató [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és oktatóanyagok megismeréséhez, beleértve az Azure ad-vel történő hitelesítést, tekintse meg a [Microsoft Graph közösségi példákat & oktatóanyagokat](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[A Microsoft Graph API fogalma és referenciája](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)
