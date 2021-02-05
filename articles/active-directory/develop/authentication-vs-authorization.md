---
title: Hitelesítés és engedélyezés | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Identity platform hitelesítésének és engedélyezésének alapjait.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5244a8e572abb56c00d87d0bdd7e8d1291af9b9e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581907"
---
# <a name="authentication-vs-authorization"></a>Hitelesítés és engedélyezés

Ez a cikk a hitelesítést és az engedélyezést határozza meg. Röviden ismerteti azt is, hogyan használhatja a Microsoft Identity platformot a webalkalmazások, webes API-k vagy a védett webes API-kat hívó alkalmazások felhasználóinak hitelesítésére és engedélyezésére. Ha úgy látja, hogy nem ismeri a kifejezést, próbálja ki a [szószedetet](developer-glossary.md) vagy a [Microsoft Identity platform videóit](identity-videos.md), amelyek az alapfogalmakat fedik le.

## <a name="authentication"></a>Hitelesítés

A *hitelesítés* az a folyamat, amely igazolja, hogy Ön azt mondja, hogy Ön. Időnként lerövidíthető a *AuthN*. A Microsoft Identity platform az [OpenID Connect](https://openid.net/connect/) protokollt használja a hitelesítés kezelésére.

## <a name="authorization"></a>Engedélyezés

Az *Engedélyezés* a hitelesített fél engedélyének megadására irányuló művelet. Itt adhatja meg, hogy milyen adatelérési lehetőségekkel férhet hozzá, és hogy mit tehet az adott adattal. Az engedélyezés időnként lerövidíthető a *AuthZ*. A Microsoft Identity platform az [OAuth 2,0](https://oauth.net/2/) protokollt használja az engedélyezés kezelésére.

## <a name="authentication-and-authorization-using-the-microsoft-identity-platform"></a>Hitelesítés és engedélyezés a Microsoft Identity platform használatával

A saját felhasználónevét és jelszavát megtartó alkalmazások létrehozása magas adminisztrációs terhet jelent a felhasználók több alkalmazáshoz való hozzáadásakor vagy eltávolításakor. Ehelyett az alkalmazások delegálják ezt a feladatot egy központi identitás-szolgáltatónak.

Azure Active Directory (Azure AD) egy központi identitás-szolgáltató a felhőben. A hitelesítés és az engedélyezés delegálása olyan forgatókönyveket tesz lehetővé, mint például a következők:

- Feltételes hozzáférési szabályzatok, amelyek megkövetelik, hogy a felhasználók egy adott helyen legyenek.
- A [többtényezős hitelesítés](../authentication/concept-mfa-howitworks.md)használata, amely más néven kétfaktoros hitelesítés vagy 2FA.
- Ha engedélyezi a felhasználó számára a bejelentkezést egyszer, és automatikusan bejelentkezik az összes olyan webalkalmazásba, amelyek ugyanazt a központi könyvtárat használják. Ezt a képességet *egyszeri bejelentkezésnek (SSO)* nevezzük.

A Microsoft Identity platform leegyszerűsíti az alkalmazások fejlesztőinek engedélyezését és hitelesítését azáltal, hogy az identitást szolgáltatásként biztosítja. Támogatja az iparági szabványnak megfelelő protokollokat és a nyílt forráskódú kódtárakat a különböző platformokon, hogy segítsen a kódolás gyors megkezdésében. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat [Microsoft Graph](https://developer.microsoft.com/graph/)hozzanak létre, amelyek az összes Microsoft-identitást bejelentkeznek.

Ez a videó ismerteti a Microsoft Identity platformot és a modern hitelesítés alapjait: 

> [!VIDEO https://www.youtube.com/embed/tkQJSHFsduY]

A Microsoft Identity platform által használt protokollok összehasonlítása:

* **OAuth és OpenID Connect**: a platform az OAuth-t használja az engedélyezéshez és az OpenID connecthez (OIDC) a hitelesítéshez. Az OpenID Connect a OAuth 2,0-re épül, így a terminológia és a folyamat a kettő között hasonló. Akár egyszerre is hitelesítheti a felhasználót (az OpenID Connect használatával), és engedélyt kaphat egy olyan védett erőforrás elérésére, amelyet a felhasználó birtokol (az OAuth 2,0-on keresztül) egy kérelemben. További információ: [OAuth 2,0 és OpenID Connect protokollok](active-directory-v2-protocols.md) és [OpenID Connect Protocol](v2-protocols-oidc.md).
* **OAuth és SAML**: a platform a OAuth 2,0-et használja az engedélyezéshez és az SAML hitelesítéshez. Ha további információt szeretne arról, hogyan használhatja ezeket a protokollokat a felhasználók hitelesítéséhez és a védett erőforrások eléréséhez szükséges engedélyek beszerzéséhez, tekintse meg a [Microsoft Identity platform és a OAuth 2,0 SAML-tulajdonos állítási folyamatát](./scenario-token-exchange-saml-oauth.md).
* **OpenID Connect és SAML**: a platform az OpenID Connect és az SAML használatával hitelesíti a felhasználót, és lehetővé teszi az egyszeri bejelentkezést. Az SAML-hitelesítés gyakran használatos az Azure AD-vel összevont identitás-szolgáltatók, például a Active Directory összevonási szolgáltatások (AD FS) (AD FS) számára, ezért a vállalati alkalmazásokban gyakran használatos. Az OpenID Connect általában kizárólag a felhőben, például a Mobile Appsben, a webhelyeken és a webes API-kon található alkalmazások esetében használatos.

## <a name="next-steps"></a>Következő lépések

A hitelesítési és engedélyezési alapismereteket magában foglalja a következő témakörökben:

* A hozzáférési jogkivonatok, a frissítési tokenek és az azonosító jogkivonatok hitelesítéshez és hitelesítéshez való használatáról a [biztonsági jogkivonatok](security-tokens.md)című témakörben olvashat.
* Ha szeretné megtudni az alkalmazás regisztrálásának folyamatát, hogy integrálható legyen a Microsoft Identity platformmal, tekintse meg az [Application Model](application-model.md)című témakört.