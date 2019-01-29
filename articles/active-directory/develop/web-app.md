---
title: Az Azure Active Directory Web apps
description: Protokoll flow, a regisztráció és a jogkivonat lejárati az alkalmazástípushoz a webalkalmazások vannak és alapjainak ismertetése
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: a54a2624d15aad2be6135b233be5900c8d83e567
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095123"
---
# <a name="web-apps"></a>Webalkalmazások

Web Apps alkalmazások olyan alkalmazások, amelyek egy webalkalmazást egy webböngészőben a felhasználó hitelesítéséhez. Ebben a forgatókönyvben a webes alkalmazás a felhasználó böngészőben jelentkezzen be őket az Azure AD irányítja. Az Azure AD választ jelentkezzen be a felhasználó böngészőjében, amely tartalmazza a biztonsági jogkivonat a felhasználóval kapcsolatos jogcímek keresztül. Ebben a forgatókönyvben támogatja a bejelentkezést az OpenID Connect, SAML 2.0 és WS-Federation protokollal.

## <a name="diagram"></a>Ábra

![A böngészőben a webalkalmazás a hitelesítési folyamat](./media/authentication-scenarios/web_browser_to_web_api.png)

## <a name="protocol-flow"></a>Protokoll folyamat

1. Amikor egy felhasználó meglátogat alkalmazáshoz, és jelentkezzen be kell, akkor a rendszer átirányítja egy bejelentkezési kérelmet a hitelesítési végpontra keresztül az Azure ad-ben.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Sikeres hitelesítés esetén a Azure ad-ben létrehoz egy hitelesítési tokent, és választ küld be az alkalmazás válasz URL-cím, amelyet az Azure Portalon. Éles alkalmazás esetén a válasz URL-cím HTTPS kell lennie. A visszaadott jogkivonat tartalmazza a jogkivonatot érvényesíteni az alkalmazás által igényelt jogcímeket a felhasználó és az Azure AD.
1. Az alkalmazás egy nyilvános aláírókulcs és issuer információk használatával érhető el, az összevonási metaadatok dokumentuma az Azure Active Directory érvényesíti a jogkivonatot. Az alkalmazás érvényesíti a jogkivonatot, miután a felhasználó egy új munkamenetet kezdődik. Ez a munkamenet a felhasználó az alkalmazás eléréséhez, amíg le nem jár.

## <a name="code-samples"></a>Kódminták

Tekintse meg a webes alkalmazás-forgatókönyvek a webböngésző Kódminták. És a új mintát gyakran hozzáadása során gyakran készítsen biztonsági ellenőrzést.

## <a name="app-registration"></a>Appok regisztrálása

Webes alkalmazás regisztrálni, lásd: [alkalmazás regisztrálása az Azure AD-1.0-s verziójú végpont](quickstart-v1-add-azure-ad-app.md).

* Egyetlen bérlő – Ha egy alkalmazás csak a szervezet számára hoz létre, azt regisztrálni kell a vállalati címtárban az Azure portal használatával.
* Multi-tenan – Ha olyan alkalmazás, amely a szervezeten kívüli felhasználók által használható hoz létre, azt a vállalati címtárban szerepelnie kell, de is regisztrálni kell az alkalmazást használó minden egyes szervezet címtárában. Elérhetővé teszi az alkalmazás a címtárban, megadhatja a regisztrációs folyamat, amely lehetővé teszi, hogy engedélyt adjanak az alkalmazásnak az ügyfelek számára. Amikor regisztrál az alkalmazáshoz, akkor megjelenik egy párbeszédpanel, amely tartalmazza az alkalmazáshoz szükséges engedélyeket, majd a beállítást, hogy engedélyt adjanak. Attól függően, a szükséges engedélyekkel a másik szervezet rendszergazda is szükség beleegyezés. A felhasználó vagy rendszergazda beleegyezik, az alkalmazás regisztrálva lesz a címtárban.

## <a name="token-expiration"></a>Jogkivonat lejáratáról

A felhasználói munkamenet lejár, ha lejár az Azure AD által kiállított jogkivonatok élettartamát. Az alkalmazás ebben az időszakban igény szerint adott ideig tartó alapuló felhasználók kijelentkeztetése például lerövidítheti. Ha a munkamenet lejár, a felhasználó kéri, jelentkezzen be újra.

## <a name="next-steps"></a>További lépések

* További információk egyéb [alkalmazástípusok és forgatókönyvek](app-types.md)
* További tudnivalók az Azure AD [hitelesítés alapjai](authentication-scenarios.md)
