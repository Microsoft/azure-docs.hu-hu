---
title: OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon | Azure
titleSuffix: Microsoft identity platform
description: Útmutató a Microsoft Identity platform által támogatott OAuth 2,0 és OpenID Connect protokollokhoz.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 765c363542b07deac44d47b94731e1109fcba045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755755"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>OAuth 2,0 és OpenID Connect protokollok a Microsoft Identity platformon

Az identitás-szolgáltatás Microsoft Identity platform végpontja a hitelesítést és az engedélyezést valósítja meg az iparági szabványoknak az OpenID Connect (OIDC) és a OAuth 2,0, illetve a. Míg a szolgáltatás szabványoknak megfelelő, a protokollok két implementációja között finom különbségek lehetnek. Az itt olvasható információk akkor hasznosak, ha úgy dönt, hogy a kódot közvetlenül a HTTP-kérések küldésével és felügyeletével vagy harmadik féltől származó nyílt forráskódú kódtár használatával írja le, és nem használja a [nyílt forráskódú kódtárakat](reference-v2-libraries.md).

## <a name="the-basics"></a>Az alapok

Csaknem minden OAuth 2,0 és OpenID Connect-folyamaton belül négy fél vesz részt az Exchange-ben:

![A OAuth 2,0 szerepköröket bemutató diagram](./media/active-directory-v2-flows/protocols-roles.svg)

* Az **engedélyezési kiszolgáló** a Microsoft Identity platform, amelynek feladata a felhasználó személyazonosságának biztosítása, az erőforrásokhoz való hozzáférés engedélyezése és visszavonása, valamint a jogkivonatok kiállítása. Az engedélyezési kiszolgálót más néven identitás-szolgáltatónak is nevezzük – biztonságos módon kezeli a felhasználó adatait, hozzáférését, valamint a folyamat résztvevői közötti megbízhatósági kapcsolatait.
* Az **erőforrás tulajdonosa** általában a végfelhasználó. Ez az a fél, amely az adattulajdonost birtokolja, és lehetővé teszi, hogy az ügyfelek hozzáférjenek ehhez az adatforráshoz vagy erőforráshoz.
* Az **OAuth-ügyfél** az alkalmazás azonosítója alapján azonosított alkalmazás. A OAuth-ügyfél általában az a fél, akit a végfelhasználó kommunikál, és az engedélyezési kiszolgálótól kér jogkivonatokat. Az ügyfélnek engedélyt kell adni az erőforrás tulajdonos általi eléréséhez.
* Az **erőforrás-kiszolgáló** , ahol az erőforrás vagy az adat található. Megbízik az engedélyezési kiszolgálón, hogy biztonságosan hitelesítse és engedélyezze az OAuth-ügyfelet, és a tulajdonos hozzáférési jogkivonatait használja annak biztosítására, hogy az erőforrásokhoz való hozzáférés megadható legyen.

## <a name="app-registration"></a>Alkalmazásregisztráció

Minden olyan alkalmazást, amely a személyes és munkahelyi vagy iskolai fiókokat is el kívánja fogadni, regisztrálnia kell a [Azure Portal](https://aka.ms/appregistrations) **Alkalmazásregisztrációk** -felületén, mielőtt aláírja ezeket a felhasználókat a OAuth 2,0 vagy OpenID Connect használatával. Az alkalmazás regisztrációs folyamata összegyűjti és hozzárendel néhány értéket az alkalmazáshoz:

* Egy **alkalmazás-azonosító** , amely egyedileg azonosítja az alkalmazást
* **Átirányítási URI** (nem kötelező), amely a válaszok visszairányítására használható az alkalmazásba
* Néhány más forgatókönyv-specifikus érték.

További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](quickstart-register-app.md).

## <a name="endpoints"></a>Végpontok

A regisztrációt követően az alkalmazás a Microsoft Identity platformmal kommunikál, ha kérelmeket küld a végpontnak:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Ahol a a `{tenant}` négy különböző érték egyikét hajthatja végre:

| Érték | Leírás |
| --- | --- |
| `common` | Lehetővé teszi, hogy a felhasználók személyes Microsoft-fiókokkal és munkahelyi/iskolai fiókkal jelentkezzenek be az Azure AD-be az alkalmazásba. |
| `organizations` | Csak az Azure AD-beli munkahelyi vagy iskolai fiókkal rendelkező felhasználók számára engedélyezi az alkalmazásba való bejelentkezést. |
| `consumers` | Csak személyes Microsoft-fiókkal (MSA) rendelkező felhasználók számára engedélyezi az alkalmazásba való bejelentkezést. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` | Csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók számára engedélyezi az adott Azure AD-bérlőtől az alkalmazásba való bejelentkezést. Az Azure AD-bérlő vagy a bérlő GUID azonosítójának felhasználóbarát tartományneve is használható. |

Ha többet szeretne megtudni ezekről a végpontokról, válasszon egy adott alkalmazást a [protokollok](#protocols) szakaszban, és kövesse a hivatkozásokat további információért.

> [!TIP]
> Az Azure AD-ben regisztrált alkalmazások a Microsoft Identity platformot is használhatják, még akkor is, ha nem jelentkeznek be a személyes fiókokba.  Ily módon áttelepítheti a meglévő alkalmazásokat a Microsoft Identity platformra és [MSAL](reference-v2-libraries.md) az alkalmazás újbóli létrehozása nélkül.

## <a name="tokens"></a>Tokenek

A OAuth 2,0 és az OpenID Connect széleskörűen kihasználja a **tulajdonosi jogkivonatokat**, amelyek általában [JWTs (JSON webes tokenként)](https://tools.ietf.org/html/rfc7519)jelennek meg. A tulajdonosi jogkivonat egy olyan egyszerű biztonsági jogkivonat, amely a "tulajdonos" hozzáférést biztosít egy védett erőforráshoz. Ebben az értelemben a "tulajdonos" olyan személy, aki megkapja a jogkivonat másolatát. Bár a feleknek először hitelesíteniük kell magukat a Microsoft Identity platformmal a tulajdonosi jogkivonat fogadásához, ha a szükséges lépések nem a jogkivonat biztonságossá tételére szolgálnak az átvitel és a tárolás során, akkor azt egy nem szándékolt fél felhasználhatja és használhatja. Míg egyes biztonsági jogkivonatok beépített mechanizmussal rendelkeznek, amely megakadályozza a jogosulatlan felek használatát, a tulajdonosi jogkivonatok nem rendelkeznek ezzel a mechanizmussal, és egy biztonságos csatornán, például a Transport Layer Security (HTTPS) szolgáltatásban kell őket szállítani. Ha egy tulajdonosi jogkivonatot a rendszer egyértelművé tesz, a rosszindulatú felek a támadók megszerezhetik a jogkivonatot, és jogosulatlan hozzáférésre használhatják a védett erőforrásokhoz. Ugyanezek a biztonsági elvek érvényesek a tulajdonosi jogkivonatok későbbi használatra történő tárolására vagy gyorsítótárazására. Mindig győződjön meg arról, hogy az alkalmazás biztonságos módon továbbítja és tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokkal kapcsolatos további biztonsági megfontolásokat lásd: [RFC 6750, 5. szakasz](https://tools.ietf.org/html/rfc6750).

A OAuth 2,0/OIDC-ben elsősorban 3 típusú token használható:

* [Hozzáférési jogkivonatok](access-tokens.md) – az erőforrás-kiszolgáló által az ügyféltől kapott jogkivonatok, amelyek engedélyeket biztosítanak az ügyfélnek.  
* [Azonosító jogkivonatok](id-tokens.md) – az ügyfél által az engedélyezési kiszolgálótól kapott tokenek, amelyekkel a felhasználó bejelentkezhet, és alapszintű információkat kaphat róluk.
* Frissítési tokenek – az ügyfél által használt új hozzáférési és azonosító tokenek időbeli beszerzése.  Ezek átlátszatlan karakterláncok, és csak az engedélyezési kiszolgáló által értelmezhető.

## <a name="protocols"></a>Protokollok

Ha készen áll arra, hogy lásson néhány példát, ismerkedjen meg az alábbi dokumentumok egyikével. Mindegyik egy adott hitelesítési forgatókönyvnek felel meg. Ha segítségre van szüksége annak meghatározásához, hogy melyik a megfelelő folyamat, tekintse meg [a Microsoft Identity platformmal felépíthető alkalmazások típusait](v2-app-types.md).

* [Mobil-, natív és Webalkalmazás létrehozása a OAuth 2,0](v2-oauth2-auth-code-flow.md)
* [Felhasználók aláírása az OpenID Connecttel](v2-protocols-oidc.md)
* [Démonokat vagy kiszolgálóoldali folyamatokat hozhat létre a OAuth 2,0 ügyfél-hitelesítő adatok folyamatával](v2-oauth2-client-creds-grant-flow.md)
* [Jogkivonatok beszerzése egy webes API-ban a OAuth 2,0-es verziójának használatával](v2-oauth2-on-behalf-of-flow.md)
* [Egylapos alkalmazások készítése a OAuth 2,0 implicit folyamattal](v2-oauth2-implicit-grant-flow.md)
