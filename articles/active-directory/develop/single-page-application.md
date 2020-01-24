---
title: Egyoldalas alkalmazások a Azure Active Directory
description: Leírja, hogy mi az egyoldalas alkalmazások (SPAs), valamint a protokollok, a regisztráció és a jogkivonat lejáratának alapjai az alkalmazás típusához.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: d956bdfe793f2e8fa3ce8ce83e03b9dae8033757
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701433"
---
# <a name="single-page-applications"></a>Egyoldalas alkalmazások

Az egyoldalas alkalmazások (SPAs) jellemzően JavaScript megjelenítési rétegként (előtér) vannak strukturálva, amely a böngészőben fut, valamint egy webes API-háttérrendszer, amely egy kiszolgálón fut, és megvalósítja az alkalmazás üzleti logikáját. Ha többet szeretne megtudni az implicit engedélyezési támogatásról, és segít eldönteni, hogy megfelelő-e az alkalmazási forgatókönyvhöz, tekintse meg [a OAuth2 implicit engedélyezési folyamat Azure Active Directoryban való megismerését](v1-oauth2-implicit-grant-flow.md)ismertető témakört.

Ebben a forgatókönyvben, amikor a felhasználó bejelentkezik, a JavaScript előtér [Active Directory-hitelesítési tárt használ a javascripthez (ADAL. JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) és az implicit engedélyezési engedély megadása az azonosító jogkivonat (id_token) Azure ad-ből való beszerzéséhez. A rendszer gyorsítótárazza a tokent, és az ügyfél a tulajdonosi jogkivonatként csatolja a kérést a webes API-háttér felé irányuló hívásokhoz, amely a OWIN-alapú middleware használatával biztosítva van.

## <a name="diagram"></a>Ábra

![Egyoldalas alkalmazás diagramja](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Protokoll folyamatábrája

1. A felhasználó a webalkalmazásra navigál.
1. Az alkalmazás a JavaScript előtér-(bemutató réteget) adja vissza a böngészőnek.
1. A felhasználó kezdeményezi a bejelentkezést, például egy bejelentkezési hivatkozásra kattintva. A böngésző elküldi az Azure AD-engedélyezési végpontot egy azonosító jogkivonat igényléséhez. Ez a kérelem tartalmazza az alkalmazás AZONOSÍTÓját és a válasz URL-címét a lekérdezési paraméterekben.
1. Az Azure AD ellenőrzi a válasz URL-címét a Azure Portalban konfigurált regisztrált válasz URL-címén.
1. A felhasználó bejelentkezik a bejelentkezési oldalon.
1. Ha a hitelesítés sikeres, az Azure AD létrehoz egy azonosító jogkivonatot, és visszaadja azt URL-töredékként (#) az alkalmazás válasz URL-címére. Éles alkalmazás esetében a válasz URL-címének HTTPS-nek kell lennie. A visszaadott jogkivonat tartalmazza a felhasználóval és az Azure AD-val kapcsolatos jogcímeket, amelyek az alkalmazás által a jogkivonat érvényesítéséhez szükségesek.
1. A böngészőben futó JavaScript-ügyfél kódja kibontja a tokent az alkalmazás webes API-hátterében megjelenő hívások biztonságossá tételéhez használt válasz alapján.
1. A böngésző az engedélyezési fejléc azonosító jogkivonatával hívja meg az alkalmazás webes API-hátterét. Az Azure AD hitelesítési szolgáltatás olyan azonosító jogkivonatot bocsát ki, amely tulajdonosi jogkivonatként használható, ha az erőforrás megegyezik az ügyfél-AZONOSÍTÓval (ebben az esetben ez igaz, mert a webes API az alkalmazás saját háttere).

## <a name="code-samples"></a>Kódminták

Tekintse [meg az egyoldalas alkalmazás forgatókönyveit tartalmazó kódot](sample-v1-code.md#single-page-applications). Ügyeljen arra, hogy gyakran térjen vissza az új minták hozzáadásakor.

## <a name="app-registration"></a>Alkalmazásregisztráció

* Egyetlen bérlő – ha a szervezete számára hoz létre alkalmazást, azt a Azure Portal használatával kell regisztrálni a vállalati címtárban.
* Több-bérlő – ha olyan alkalmazást hoz létre, amelyet a szervezeten kívüli felhasználók is használhatnak, regisztrálni kell a vállalati címtárban, de az alkalmazást használó összes szervezet címtárában is regisztrálni kell. Ahhoz, hogy az alkalmazás elérhető legyen a címtárában, olyan regisztrációs folyamatot is megadhat az ügyfeleknek, amely lehetővé teszi számukra az alkalmazáshoz való hozzájárulásukat. Amikor regisztrálnak az alkalmazásra, a rendszer egy párbeszédpanelt jelenít meg, amely megjeleníti az alkalmazás által igényelt engedélyeket, majd a beleegyező lehetőséget. A szükséges engedélyektől függően szükség lehet a másik szervezet rendszergazdájának jóváhagyásra. Ha a felhasználó vagy a rendszergazda beleegyezik, az alkalmazás regisztrálva van a címtárában.

Az alkalmazás regisztrálását követően úgy kell konfigurálni, hogy az OAuth 2,0 implicit engedélyezési protokollt használja. Alapértelmezés szerint ez a protokoll le van tiltva az alkalmazásokhoz. Az alkalmazáshoz tartozó implicit OAuth2 engedélyezéséhez szerkessze az alkalmazás jegyzékfájlját a Azure Portal, és állítsa a "oauth2AllowImplicitFlow" értéket igaz értékre. További információ: [Application manifest](reference-app-manifest.md).

## <a name="token-expiration"></a>Jogkivonat lejárata

A ADAL. js használata a következőkkel segít:

* Lejárt jogkivonat frissítése
* Hozzáférési jogkivonat kérése webes API-erőforrás meghívásához

A sikeres hitelesítés után az Azure AD egy cookie-t ír a felhasználó böngészőjében egy munkamenet létrehozásához. Figyelje meg, hogy a munkamenet létezik a felhasználó és az Azure AD között (nem a felhasználó és a webalkalmazás között). Ha egy jogkivonat lejár, a ADAL. js ezt a munkamenetet használja egy másik jogkivonat csendes beszerzéséhez. A ADAL. js egy rejtett iFrame használatával küldi el és fogadja el a kérést az OAuth implicit engedélyezési protokollal. A ADAL. js ugyanezt a mechanizmust is használhatja a más webes API-erőforrások elérési jogkivonatának csendes beszerzéséhez, feltéve, hogy ezek az erőforrások támogatják az eltérő eredetű erőforrás-megosztást (CORS), regisztrálva vannak a felhasználói címtárban, és minden szükséges beleegyezik a felhasználó adja meg a bejelentkezés során.

## <a name="next-steps"></a>Következő lépések

* További információ az egyéb [alkalmazási típusokról és forgatókönyvekről](app-types.md)
* Tudnivalók az Azure AD- [alapú hitelesítés alapjairól](v1-authentication-scenarios.md)
