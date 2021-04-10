---
title: További tudnivalók a MSAL | Azure
titleSuffix: Microsoft identity platform
description: A Microsoft Authentication Library (MSAL) lehetővé teszi az alkalmazások fejlesztői számára a jogkivonatok beszerzését a biztonságos webes API-k meghívásához. Ezek a webes API-k lehetnek a Microsoft Graph, más Microsoft API-k, harmadik féltől származó webes API-k vagy a saját webes API-jai. A MSAL több alkalmazás-architektúrát és platformot is támogat.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 90e81e30e26846bf05032dddf1441e8428bc7158
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550982"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>A Microsoft Authentication Library (MSAL) áttekintése
A Microsoft Authentication Library (MSAL) lehetővé teszi a fejlesztők számára a [jogkivonatok](developer-glossary.md#security-token) beszerzését a Microsoft Identity platformból a felhasználók hitelesítéséhez és a biztonságos webes API-k eléréséhez. A szolgáltatással biztonságos hozzáférést biztosíthat a Microsoft Graphhoz, más Microsoft API-khoz, harmadik féltől származó webes API-khoz vagy a saját webes API-hoz. A MSAL számos különböző alkalmazás-architektúrát és platformot támogat, többek között a .NET, JavaScript, Java, Python, Android és iOS rendszereken.

A MSAL számos lehetőséget kínál a jogkivonatok lekérésére, egy egységes API-val számos platformon. A MSAL használata a következő előnyöket biztosítja:

* Nem kell közvetlenül használni a OAuth-kódtárakat vagy-kódokat az alkalmazás protokollján.
* Egy felhasználó nevében vagy egy alkalmazás nevében szerzi be a jogkivonatokat (ha alkalmazható a platformra).
* Fenntart egy jogkivonat-gyorsítótárat, és frissíti a jogkivonatokat, amikor közel vannak a lejárathoz. Nem kell kezelnie a token lejáratát a sajátján.
* Segítséget nyújt annak megadásához, hogy az alkalmazás milyen célközönséget jelentkezzen be (szervezete, több szervezethez, munkahelyi és iskolai és személyes Microsoft-fiók, közösségi identitás a Azure AD B2C, a szuverén és az országos felhőkben).
* Segítséget nyújt az alkalmazás konfigurációs fájlokból való beállításához.
* Segítséget nyújt az alkalmazás hibakereséséhez, így végrehajtható kivételeket, naplózást és telemetria tehet közzé.

## <a name="application-types-and-scenarios"></a>Alkalmazástípusok és forgatókönyvek
A MSAL használatával számos alkalmazásból szerezhet be tokent: webalkalmazások, webes API-k, egylapos alkalmazások (JavaScript), mobil és natív alkalmazások, valamint démonok és kiszolgálóoldali alkalmazások.

A MSAL számos alkalmazási helyzetben használható, többek között az alábbiakat is beleértve:

* [Egyoldalas alkalmazások (JavaScript)](scenario-spa-overview.md)
* [Webalkalmazás-aláírás a felhasználókban](scenario-web-app-sign-user-overview.md)
* [Webalkalmazás-aláírás a felhasználó nevében, és webes API meghívása a felhasználó nevében](scenario-web-app-call-api-overview.md)
* [Webes API-k védelme, hogy csak a hitelesített felhasználók férhessenek hozzá.](scenario-protected-web-api-overview.md)
* [Webes API, amely egy másik alsóbb rétegbeli webes API-t hív meg a bejelentkezett felhasználó nevében](scenario-web-api-call-api-overview.md)
* [A bejelentkezett felhasználó nevében webes API-t hívó asztali alkalmazás](scenario-desktop-overview.md)
* A [mobil alkalmazás a webes API-t hívja interaktív módon bejelentkezett felhasználó nevében](scenario-mobile-overview.md).
* [Desktop/Service Daemon-alkalmazás a webes API-t saját nevében hívja meg](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Nyelvek és keretrendszerek

| Kódtár | Támogatott platformok és keretrendszerek|
| --- | --- |
| [MSAL Android rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL szögletes](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angular)| Egyoldalas alkalmazások szögletes és Angular.js keretrendszerekkel|
| [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS és macOS|
| [MSAL go (előzetes verzió)](https://github.com/AzureAD/microsoft-authentication-library-for-go)|Windows, macOS, Linux|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)| JavaScript/írógéppel keretrendszerek, például Vue.js, Ember.js vagy Durandal.js|
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET-keretrendszer, .NET Core, Xamarin Android, Xamarin iOS, Univerzális Windows-platform|
| [MSAL csomópont](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)|Web Apps expressz, asztali alkalmazásokkal és Electron platformmal, platformfüggetlen konzolos alkalmazásokkal|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|
| [MSAL reagál](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)| Egyoldalas alkalmazások reagáló és reagáláson alapuló kódtárakkal (Next.js, Gatsby.js)|

## <a name="differences-between-adal-and-msal"></a>ADAL és MSAL közötti különbségek

Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL integrálható a Microsoft Identity platformmal. A v 1.0 végpont támogatja a munkahelyi fiókokat, a személyes fiókokat azonban nem. A 2.0-s végpont a személyes Microsoft-fiókok és-munkahelyi fiókok egységesítése egyetlen hitelesítési rendszerbe. Emellett a MSAL-mel is beszerezhetők a Azure AD B2C hitelesítései.

További információért olvassa el a [Migrálás a MSAL.net-ről a ADAL.net](msal-net-migration.md) -ről és a [MSAL.js-re való Migrálás ADAL.jsról ](msal-compare-msal-js-and-adal-js.md)című témakört.
