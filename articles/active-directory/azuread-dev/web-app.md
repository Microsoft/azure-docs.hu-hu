---
title: Azure Active Directory webalkalmazások
description: Leírja, hogy mely webalkalmazások és az alkalmazás típusához tartozó protokollok, regisztráció és tokenek lejáratának alapjai.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: e4a7fb72d40f5db65e8e30264e9d68b2727749e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154406"
---
# <a name="web-apps"></a>Webalkalmazások

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A webalkalmazások olyan alkalmazások, amelyek egy webböngészőben hitelesítik a felhasználókat egy webalkalmazásban. Ebben az esetben a webalkalmazás irányítja a felhasználó böngészőjében, hogy bejelentkezzen az Azure AD-be. Az Azure AD egy bejelentkezési választ ad vissza a felhasználó böngészőjén keresztül, amely a felhasználóhoz tartozó jogcímeket tartalmaz egy biztonsági jogkivonatban. Ez a forgatókönyv az OpenID Connect, az SAML 2,0 és a WS-Federation protokollok használatával támogatja a bejelentkezést.

## <a name="diagram"></a>Ábra

![A böngésző és a webes alkalmazás közötti hitelesítési folyamat](./media/authentication-scenarios/web-browser-to-web-api.png)

## <a name="protocol-flow"></a>Protokoll folyamatábrája

1. Amikor egy felhasználó meglátogatja az alkalmazást, és be kell jelentkeznie, a rendszer átirányítja az Azure AD hitelesítési végpontjának bejelentkezési kérelmén keresztül.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Ha a hitelesítés sikeres, az Azure AD létrehoz egy hitelesítési jogkivonatot, és egy bejelentkezési választ ad vissza az alkalmazásnak a Azure Portal konfigurált válasz URL-címére. Éles alkalmazás esetében a válasz URL-címének HTTPS-nek kell lennie. A visszaadott jogkivonat tartalmazza a felhasználóval és az Azure AD-val kapcsolatos jogcímeket, amelyek az alkalmazás által a jogkivonat érvényesítéséhez szükségesek.
1. Az alkalmazás érvényesíti a jogkivonatot egy nyilvános aláíró kulccsal és kiállítói információval az Azure AD összevonási metaadat-dokumentumában. Miután az alkalmazás érvényesíti a jogkivonatot, egy új munkamenetet indít el a felhasználóval. Ez a munkamenet lehetővé teszi, hogy a felhasználó az érvényesség lejárta előtt hozzáférjen az alkalmazáshoz.

## <a name="code-samples"></a>Kódminták

Tekintse meg a webalkalmazási forgatókönyvek webböngészőben való megjelenítéséhez használható példákat. Az új minták hozzáadásakor gyakran térjen vissza újra.

## <a name="app-registration"></a>Alkalmazásregisztráció

Webalkalmazás regisztrálásához tekintse meg [az alkalmazás regisztrálása](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)című témakört.

* Egyetlen bérlő – ha a szervezete számára hoz létre alkalmazást, azt a Azure Portal használatával kell regisztrálni a vállalati címtárban.
* Több-bérlő – ha olyan alkalmazást hoz létre, amelyet a szervezeten kívüli felhasználók is használhatnak, regisztrálni kell a vállalati címtárban, de az alkalmazást használó összes szervezet címtárában is regisztrálni kell. Ahhoz, hogy az alkalmazás elérhető legyen a címtárában, olyan regisztrációs folyamatot is megadhat az ügyfeleknek, amely lehetővé teszi számukra az alkalmazáshoz való hozzájárulásukat. Amikor regisztrálnak az alkalmazásra, a rendszer egy párbeszédpanelt jelenít meg, amely megjeleníti az alkalmazás által igényelt engedélyeket, majd a beleegyező lehetőséget. A szükséges engedélyektől függően szükség lehet a másik szervezet rendszergazdájának jóváhagyásra. Ha a felhasználó vagy a rendszergazda beleegyezik, az alkalmazás regisztrálva van a címtárában.

## <a name="token-expiration"></a>Jogkivonat lejárata

A felhasználó munkamenete lejár, ha az Azure AD által kiadott jogkivonat élettartama lejár. Az alkalmazás igény szerint lerövidítheti ezt az időtartamot, például a felhasználók inaktivitási időtartam alapján történő kijelentkezését. A munkamenet lejáratakor a rendszer kérni fogja a felhasználótól, hogy jelentkezzen be újra.

## <a name="next-steps"></a>Következő lépések

* További információ az egyéb [alkalmazási típusokról és forgatókönyvekről](app-types.md)
* Tudnivalók az Azure AD- [alapú hitelesítés alapjairól](v1-authentication-scenarios.md)
