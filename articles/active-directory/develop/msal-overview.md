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
ms.openlocfilehash: 2b6266f308fce75f136aa4a2482c5b1be6eedb8e
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063365"
---
# <a name="overview-of-the-microsoft-authentication-library-msal"></a>A Microsoft Authentication Library (MSAL) áttekintése
A Microsoft Authentication Library (MSAL) lehetővé teszi a fejlesztők számára a [tokenek](developer-glossary.md#security-token) beszerzését a Microsoft Identity platform-végpontból a felhasználók hitelesítéséhez és a biztonságos webes API-k eléréséhez. A szolgáltatással biztonságos hozzáférést biztosíthat a Microsoft Graphhoz, más Microsoft API-khoz, harmadik féltől származó webes API-khoz vagy a saját webes API-hoz. A MSAL számos különböző alkalmazás-architektúrát és platformot támogat, többek között a .NET, JavaScript, Java, Python, Android és iOS rendszereken.

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
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET-keretrendszer, .NET Core, Xamarin Android, Xamarin iOS, Univerzális Windows-platform|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| JavaScript/írógéppel keretrendszerek, például AngularJS, Ember.js vagy Durandal.js|
| [MSAL Android rendszerhez](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL iOS és macOS rendszerekre](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS és macOS|
| [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Windows, macOS, Linux|
| [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)|Windows, macOS, Linux|

## <a name="differences-between-adal-and-msal"></a>ADAL és MSAL közötti különbségek

Active Directory-hitelesítési tár (ADAL) integrálható az Azure AD for Developers (v 1.0) végponttal, ahol a MSAL a Microsoft Identity platform (v 2.0) végponttal integrálódik. A v 1.0 végpont támogatja a munkahelyi fiókokat, a személyes fiókokat azonban nem. A 2.0-s végpont a személyes Microsoft-fiókok és-munkahelyi fiókok egységesítése egyetlen hitelesítési rendszerbe. Emellett a MSAL-mel is beszerezhetők a Azure AD B2C hitelesítései.

További információért olvassa el a [Migrálás a MSAL.net-ről a ADAL.net](msal-net-migration.md) -ről és a [MSAL.js-re való Migrálás ADAL.jsról ](msal-compare-msal-js-and-adal-js.md)című témakört.
