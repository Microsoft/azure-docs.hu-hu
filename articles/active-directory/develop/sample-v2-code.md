---
title: Kód minták a Microsoft Identity platformhoz
description: A rendelkezésre álló Microsoft Identity platform (v 2.0 végpont) kód mintáinak indexét kínálja forgatókönyv szerint rendezve.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 10/21/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 49bba8054065123d7409f26b61d1dfc2580ef720
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636051"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Microsoft Identity platform Code Samples (v 2.0 végpont)

A Microsoft Identity platformot a következő célra használhatja:

- Adja hozzá a hitelesítést és az engedélyezést a webalkalmazásokhoz és a webes API-khoz.
- Hozzáférési jogkivonat megkövetelése egy védett webes API eléréséhez.

Ez a cikk röviden leírja a Microsoft Identity platform végpontjának mintáit, és hivatkozásokat tartalmaz. Ezek a minták bemutatják, hogyan végezheti el, és az alkalmazásaiban használható kódrészleteket is megadhat. A kód mintája lapon a követelményekkel, a telepítéssel és a telepítéssel kapcsolatos részletes információkkal foglalkozó témaköröket talál. A kódban található megjegyzések segítenek megérteni a kritikus szakaszt.

Az egyes típusok alapvető forgatókönyvének megismeréséhez tekintse meg [a Microsoft Identity platform végpontjának alkalmazás-típusai](v2-app-types.md)című témakört.

Emellett a GitHubon is hozzájárulhat a mintákhoz. További információ: [Microsoft Azure Active Directory minták és dokumentáció](https://github.com/Azure-Samples?page=3&query=active-directory).

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Ezek a minták azt mutatják be, hogyan lehet egy egyoldalas alkalmazást biztonságossá tennie a Microsoft Identity platformmal. Ezek a minták a MSAL.js egyikét használják.

| Platform | Leírás | Hivatkozás |
| -------- | --------------------- | -------- |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-hívások Microsoft Graph |[JavaScript-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA-hívások Microsoft Graph az Auth Code flow PKCE |[JavaScript – v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-hívások B2C |[B2C-JavaScript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | SPA meghívása a B2C-vel az Auth Code flow-t/PKCE |[B2C-JavaScript-Spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása ](media/sample-v2-code/logo_angular.png) [(MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) látható| SPA-hívások Microsoft Graph  | [Active-Directory-JavaScript-singlepageapp-szögletes](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása ](media/sample-v2-code/logo_angular.png) [(MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) látható| SPA-hívások egyéni webes API | [MS-Identity-JavaScript-szögletes-Spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása ](media/sample-v2-code/logo_angular.png) [(MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) látható | SPA-hívások B2C |[Active-Directory-B2C-JavaScript-szögletes-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Ez a képen a reagáló embléma ](media/sample-v2-code/logo_react.png) [reagál (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| A SPA meghívja az egyéni webes API-t, amely viszont meghívja a Microsoft Graph  | [MS-Identity-JavaScript-reakciós-Spa-dotnetcore-webapi-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | A SPA meghívja az egyéni webes API-t, amely viszont meghívja a Microsoft Graph  | [MS-Identity-JavaScript-oktatóanyag-chapter4-OBO](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/4-1-call-api-graph) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása ](media/sample-v2-code/logo_angular.png) [(MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) látható | A SPA meghívja az egyéni webes API-t az alkalmazás szerepköreivel és biztonsági csoportjaival |[MS-Identity-JavaScript-szögletes-Spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Webalkalmazások

Az alábbi példák bemutatják a felhasználók bejelentkezési webalkalmazásait. Néhány minta azt is bemutatja, hogy az alkalmazás meghívja a Microsoft Grapht, vagy a saját webes API-ját a felhasználó identitásával.

| Platform | Csak a felhasználókhoz tartozó jelek | A felhasználók és a hívások jelei Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET-mag | [ASP.NET Core WebApp Signs-in Users oktatóanyag](https://aka.ms/aspnetcore-webapp-sign-in) | Ugyanaz a minta a [ASP.net Core Web App calls Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fázisban |
| ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET gyors útmutató](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [DotNet-WebApp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [DotNet-rendszergazda-korlátozott-hatókörök-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[msgraph – képzés – aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-WebApp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)  | [MS-Identity-Python-lombik-WebApp-Authentication](https://github.com/Azure-Samples/ms-identity-python-flask-webapp-authentication) | [MS-Identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Ez a képen a Ruby embléma látható](media/sample-v2-code/logo_ruby.png) |                   | [msgraph – képzés – rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Asztali és mobil nyilvános ügyfélalkalmazások

Az alábbi példák olyan nyilvános ügyfélalkalmazások (asztali vagy mobil alkalmazások) megjelenítését mutatják be, amelyek hozzáférnek a Microsoft Graph API-hoz vagy a saját webes API-ját egy felhasználó nevében. Az *asztalon (konzol) és a WAM* -mintán kívül az összes ügyfélalkalmazás a Microsoft Authentication Library (MSAL) alkalmazást használja.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph | ASP.NET Core webes API meghívása |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Asztali (WPF)      | ![Ez a rendszerkép a .NET/C# emblémát jeleníti meg](media/sample-v2-code/logo_NET.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code)| [DotNet-Desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NET.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) | [DotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Asztal (konzol)   | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Integrált Windows-hitelesítés](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[dotnetcore – v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Asztal (konzol) a WAM-vel  | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | Interaktív a [web Account Managerrel](/windows/uwp/security/web-account-manager) (WAM) |[DotNet-Native-uwp-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Asztal (konzol)   | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Asztal (konzol)   | ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | [Felhasználónév/jelszó](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Python-asztali](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobil (Android, iOS, UWP)   | ![Ez a rendszerkép a .NET/C# (Xamarin) emblémát jeleníti meg](media/sample-v2-code/logo_xamarin.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |[xamarin – natív – v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobil (iOS)       | ![A képen az iOS/Objective-C vagy a Swift látható](media/sample-v2-code/logo_iOS.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |[iOS-Swift-ObjC-Native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [iOS – natív-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Asztali (macOS)       | macOS | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |[macOS-Swift-ObjC-Native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobile (Android – Java)   | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |  [Android – Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobil (Android – Kotlin)   | ![Ez a rendszerkép az Android-emblémát jeleníti meg](media/sample-v2-code/logo_Android.png) | [Engedélyezési kód](msal-authentication-flows.md#authorization-code) |  [Android – Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-alkalmazások

A következő minták egy olyan alkalmazást mutatnak be, amely a saját identitásával fér hozzá a Microsoft Graph API-hoz (felhasználó nélkül).

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![Ez a rendszerkép a .NET Core emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [dotnetcore-Daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Webalkalmazás | ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [DotNet-Daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-Daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | [Ügyfél hitelesítő adatai](msal-authentication-flows.md#client-credentials) | [MS-Identity-Python-Daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Fej nélküli alkalmazások

Az alábbi példa egy, a webböngészőt nem használó eszközön futó nyilvános ügyfélalkalmazás. Az alkalmazás lehet egy parancssori eszköz, egy Linux vagy Mac rendszerű alkalmazás, vagy egy IoT-alkalmazás. A minta olyan alkalmazást tartalmaz, amely a Microsoft Graph API-t használja egy olyan felhasználó nevében, aki egy másik eszközön (például egy mobiltelefonon) interaktívan jelentkezik be. Ez az ügyfélalkalmazás a Microsoft Authentication Library (MSAL) szolgáltatást használja.

| Ügyfélalkalmazás | Platform | Folyamat/támogatás | Hívások Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Asztal (konzol)   | ![Ez a rendszerkép a .NET/C# (asztali) emblémát jeleníti meg](media/sample-v2-code/logo_NETcore.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Asztal (konzol)   | ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Asztal (konzol)   | ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png) | [Eszköz kódjának folyamata](msal-authentication-flows.md#device-code) |[MS-Identity-Python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>Több-bérlős SaaS-alkalmazások

Az alábbi példák bemutatják, hogyan konfigurálhatja az alkalmazást úgy, hogy fogadja a bejelentkezéseket bármely Azure Active Directory (Azure AD) bérlőről. Ha úgy konfigurálja az alkalmazást, hogy *több-bérlős* legyen, azt jelenti, hogy egy **szolgáltatott szoftver** (SaaS) alkalmazás számos szervezet számára elérhetővé válik, lehetővé téve, hogy a felhasználók bejelentkezzenek az alkalmazásba, miután beleegyezett a fiók használatára.

| Platform | Leírás | Hivatkozás |
| -------- | --------------------- | -------- |
| ![Ez a képen a JavaScript ](media/sample-v2-code/logo_js.png) [-embléma javascript (MSAL.js) látható.](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser) | Több-bérlős SPA-hívások Graph API |[MS-Identity-JavaScript-szögletes-Spa-ASPNET-webapi-több-bérlős](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter1) |
| ![Ez a képen a szögletes embléma szögletes ábrázolása ](media/sample-v2-code/logo_angular.png) [(MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) látható | Több-bérlős SPA több-bérlős egyéni webes API-t hív meg |[MS-Identity-JavaScript-szögletes-Spa-ASPNET-webapi-több-bérlős](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![Ez a rendszerkép a ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.net Core-t (MSAL.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) jeleníti meg | ASP.NET Core MVC webalkalmazás-hívások Graph API |[Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![Ez a rendszerkép a ASP.NET Core logo ](media/sample-v2-code/logo_NETcore.png) [.net Core-t (MSAL.net)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) jeleníti meg | ASP.NET Core MVC webalkalmazás meghívása ASP.NET Core webes API-val |[Active-Directory-aspnetcore-WebApp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Webes API-k

Az alábbi példák bemutatják, hogyan lehet védelemmel ellátni egy webes API-t a Microsoft Identity platform-végponttal, és hogyan hívhat meg egy alsóbb rétegbeli API-t a webes API-ból.

| Platform | Sample |
| -------- | ------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET-mag | ASP.NET Core web API (szolgáltatás) a [DotNet-Native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![Ez a képen a ASP.NET embléma látható](media/sample-v2-code/logo_NET.png)</p>ASP.NET, MVC | [MS-Identity-ASPNET-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) webes API (szolgáltatás) |
| ![Ez a rendszerkép a Java-emblémát jeleníti meg](media/sample-v2-code/logo_java.png) | [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) webes API (szolgáltatás) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png) | Az [Active Directory-JavaScript-NodeJS-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) webes API (szolgáltatás) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png) | B2C webes API (szolgáltatás) az [Active Directory-B2C-JavaScript-NodeJS-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions webes API-kként

Az alábbi példák azt mutatják be, hogyan lehet védelemmel ellátni egy Azure-függvényt a HttpTrigger használatával, és hogyan tehetők elérhetővé a webes API-k a Microsoft Identity platform-végponttal, valamint hogyan hívható le egy alsóbb rétegbeli API

| Platform | Sample |
| -------- | ------------------- |
| ![Ez a képen a ASP.NET Core embléma látható](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET-mag | ASP.NET Core web API (szolgáltatás) Azure-függvény a [DotNet-Native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Ez a képen a Python-embléma látható](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) web API (szolgáltatás) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>Node.js | ANode.js és a Passport webes API-je (szolgáltatás) [ – Azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Ez a képen a Node.js embléma látható](media/sample-v2-code/logo_nodejs.png)</p>NodeJS | [NodeJS és Passport-](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) alapú webes API (szolgáltatás) a következő nevében: |

## <a name="other-microsoft-graph-samples"></a>Egyéb Microsoft Graph minták

A Microsoft Graph API különböző használati mintáit bemutató [minták](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) és oktatóanyagok megismeréséhez, beleértve az Azure ad-vel történő hitelesítést, tekintse meg a [Microsoft Graph közösségi példákat & oktatóanyagokat](https://github.com/microsoftgraph/msgraph-community-samples).

## <a name="see-also"></a>Lásd még

[A Microsoft Graph API fogalma és referenciája](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta)
