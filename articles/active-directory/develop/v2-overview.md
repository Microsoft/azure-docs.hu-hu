---
title: A Microsoft Identity platform áttekintése – Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Identity platform összetevőit, valamint azt, hogy miként segíthetnek az alkalmazásokban az identitás-és hozzáférés-kezelési (IAM) támogatás létrehozásában.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40, contperf-fy21q2
ms.openlocfilehash: a4ce8242bd3110fee038ac826973e6a134413344
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102426841"
---
# <a name="what-is-the-microsoft-identity-platform"></a>Mi az a Microsoft Identitásplatform?

A Microsoft Identity platform segítségével olyan alkalmazásokat hozhat létre, amelyeket a felhasználók és az ügyfelek bejelentkezhetnek a Microsoft-identitásuk vagy közösségi fiókjaik használatára, valamint jogosult hozzáférést biztosítanak a saját API-khoz vagy a Microsoft API-khoz, például a Microsoft Graphhoz.

A Microsoft Identity platform számos összetevőből áll:

- **OAuth 2,0 és OpenID Connect standard-kompatibilis hitelesítési szolgáltatás** , amely lehetővé teszi a fejlesztők számára a különböző identitások hitelesítését, beleértve a következőket:
  - Az Azure AD-n keresztül kiépített munkahelyi vagy iskolai fiókok
  - Személyes Microsoft-fiók, például Skype, Xbox és Outlook.com
  - Közösségi vagy helyi fiókok Azure AD B2C használatával
- **Nyílt forráskódú kódtárak**: Microsoft Authentication librarys (MSAL) és más szabványoknak megfelelő kódtárak támogatása
- **Application Management Portal**: a Azure Portal regisztrációs és konfigurációs felhasználói felülete, valamint a többi Azure felügyeleti lehetőség.
- **Alkalmazás-konfigurációs API és PowerShell**: az alkalmazások programozott konfigurációja a Microsoft Graph API-val és a PowerShell-lel, hogy automatizálható legyen a DevOps-feladatok.
- **Fejlesztői tartalom**: technikai dokumentáció, beleértve a gyors útmutatókat, oktatóanyagokat, útmutatók és a kód mintáit.

A fejlesztők számára a Microsoft Identity platform a modern innovációk integrációját kínálja az identitás és a biztonság terén, például a jelszó-alapú hitelesítéshez, a lépésenkénti hitelesítéshez és a feltételes hozzáféréshez. Nincs szükség ilyen funkciók megvalósítására: a Microsoft Identity platformmal integrált alkalmazások natív módon kihasználhatják ezeket az innovációkat.

A Microsoft Identity platformmal egyszer is írhat kódot, és elérheti a felhasználókat. Egyszerre létrehozhat egy alkalmazást, és több platformon is dolgozhat, vagy olyan alkalmazást hozhat létre, amely ügyfélként, valamint erőforrás-alkalmazásként (API) működik.

A platform és a hitelesítési élmény bemutatójának áttekintését lásd: [Mi a Microsoft Identity platform for Developers?](https://youtu.be/uDU1QTSw7Ps).

## <a name="getting-started"></a>Első lépések

Válassza ki a létrehozni kívánt [alkalmazási forgatókönyvet](authentication-flows-app-scenarios.md) . A forgatókönyvek mindegyikének elérési útja áttekintést nyújt, és egy rövid útmutatóra mutató hivatkozásokat tartalmaz, amelyekkel megkezdheti a működést:

- [Egyoldalas alkalmazás (SPA)](scenario-spa-overview.md)
- [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md)
- [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md)
- [Védett webes API](scenario-protected-web-api-overview.md)
- [Webes API-kat hívó webes API](scenario-web-api-call-api-overview.md)
- [Asztali alkalmazás](scenario-desktop-overview.md)
- [Démonalkalmazások](scenario-daemon-overview.md)
- [mobilalkalmazás](scenario-mobile-overview.md)

Amikor együttműködik a Microsoft Identity platformmal az alkalmazások hitelesítésének és engedélyezésének integrálásához, tekintse át ezt a rendszerképet, amely az alkalmazás leggyakoribb forgatókönyveit és identitás-összetevőit ismerteti. Válassza ki a képet a teljes méret megtekintéséhez.

[![A Microsoft Identity platform számos alkalmazási forgatókönyvét bemutató Metro Map](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>A hitelesítési fogalmak megismerése

Ismerje meg, hogyan vonatkoznak az alapszintű hitelesítés és az Azure AD-fogalmak a Microsoft Identity platformra ebben az ajánlott cikkben:

- [Hitelesítési alapok](./authentication-vs-authorization.md)
- [Alkalmazás-és egyszerű szolgáltatások](app-objects-and-service-principals.md)
- [Közönség](v2-supported-account-types.md)
- [Engedélyek és jóváhagyás](v2-permissions-and-consent.md)
- [Azonosító jogkivonatok](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
- [Hitelesítési folyamatok és alkalmazási helyzetek](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>További identitás-és hozzáférés-kezelési lehetőségek

[Azure ad B2C](../../active-directory-b2c/overview.md) – ügyfelek által elérhető alkalmazások létrehozása a felhasználók bejelentkezhetnek a közösségi fiókjaik, például a Facebook vagy a Google használatával, vagy e-mail-címmel és jelszóval.

[Azure ad B2B](../external-identities/what-is-b2b.md) – külső felhasználók meghívása az Azure ad-bérlőbe "vendég" felhasználóként, valamint engedélyek kiosztása a hitelesítéshez a meglévő hitelesítő adataik használata közben.

[Azure Active Directory fejlesztők számára (v 1.0)](../azuread-dev/v1-overview.md) – itt láthatók a régebbi 1.0-s verziókat használó meglévő alkalmazásokkal rendelkező fejlesztők számára. **Ne használja az** 1.0-s verziókat új projektekhez.

## <a name="next-steps"></a>Következő lépések

Ha van olyan Azure-fiókja, amelyhez már van hozzáférése egy Azure Active Directory bérlőhöz, a Microsoft Identity platform fejlesztőinek azonban a saját Azure AD-bérlőre van szükségük az alkalmazások fejlesztéséhez, a "dev bérlő".

Megtudhatja, hogyan hozhat létre saját bérlőt az alkalmazások összeállítása során való használatra:

[Gyors útmutató: Azure AD-bérlő beállítása](quickstart-create-new-tenant.md)
