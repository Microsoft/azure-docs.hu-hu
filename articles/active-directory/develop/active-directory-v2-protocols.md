---
title: További tudnivalók az Azure AD v2.0 által támogatott hitelesítési protokollok |} A Microsoft Docs
description: Egy útmutató, amellyel az Azure AD v2.0-végpont által támogatott protokollok.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 57a3d5fc50c2278b34fddbfba61b12b0d81a33ed
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424527"
---
# <a name="v20-protocols---oauth-20--openid-connect"></a>v2.0 protokoll – az OAuth 2.0 és OpenID Connect

A v2.0-végpontra is használhatja az Azure AD identitás--szolgáltatásként az iparági szabványos protokollok, OpenID Connect és az OAuth 2.0. Szabványokkal kompatibilis a szolgáltatás pedig finom eltérések ezeket a protokollokat bármely két megvalósítása között lehet. Az adatok itt lesznek lehet hasznos, ha úgy dönt, hogy a kód írása közvetlenül elküldött & kezelése a HTTP-kérelmek vagy használja a 3. fél nyílt forráskódú kódtár, nem pedig az egyik a [nyissa meg a forráskódtárak](reference-v2-libraries.md).

> [!NOTE]
> Nem minden Azure Active Directory-forgatókönyvet és funkciót támogatja a v2.0-végpontra. Annak megállapításához, ha a v2.0-végpont használja, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).

## <a name="the-basics"></a>Az alapok

A gyakorlatilag az összes OAuth és OpenID Connect folyamatok találhatók négy felek részt az exchange:

![Az OAuth 2.0-szerepkörök](../../media/active-directory-v2-flows/protocols_roles.png)

* A **az engedélyezési kiszolgáló** a v2.0-végpont. Ez felelős a felhasználói identitás biztosítása, biztosítása és erőforrásokhoz való hozzáférés visszavonása és a jogkivonatok kiállítása. Más néven az identitásszolgáltató – biztonságos kezelési semmit sem kell elvégezni a megbízhatósági kapcsolatok egy flow-ban a felek között, a felhasználói adatok és azok elérhető.
* A **erőforrás tulajdonosa** általában a végfelhasználó van. A felet, az adatok tulajdonosa, és lehetővé teszik a harmadik feleket arra, hogy adatokat, vagy az erőforrás elérésére jogosult legyen.
* A **OAuth-ügyfél** használ az alkalmazás azonosítja az alkalmazás azonosítója. Általában a felet, a végfelhasználó kommunikál, és jogkivonatok kér az engedélyezési kiszolgálón. Az ügyfél által az erőforrás tulajdonosa erőforrás elérésére engedéllyel kell rendelkezni.
* A **erőforrás-kiszolgáló** van, ahol az erőforrásra vagy található. Az engedélyezési kiszolgáló biztonságos hitelesítéséhez és engedélyezéséhez az OAuth-ügyfél megbízik, és tulajdonosi access_tokens használ, győződjön meg arról, hogy az erőforrásokhoz való hozzáférést is megadható.

## <a name="app-registration"></a>Alkalmazásregisztráció
A v2.0-végpont használó alkalmazásokat kell regisztrálható [apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) előtt OAuth vagy OpenID Connect használata kezelheti. Az alkalmazásregisztrációs művelet során fog gyűjteni & néhány értéket rendelni az alkalmazást:

* Egy **alkalmazásazonosító** , amely egyedileg azonosítja az alkalmazást
* A **átirányítási URI-t** vagy **csomagazonosítót** , amely közvetlen válaszokhoz az alkalmazáshoz használható
* Néhány más forgatókönyv-specifikus értékeket.

További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](quickstart-v2-register-an-app.md).

## <a name="endpoints"></a>Végpontok

Regisztrálása után az alkalmazás kommunikál az Azure AD v2.0-végpontra való kérések küldésével:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Ha a `{tenant}` négy különböző értékek valamelyikét hajthatja végre:

| Érték | Leírás |
| --- | --- |
| `common` |Lehetővé teszi, hogy a személyes Microsoft-fiókok és a munkahelyi vagy iskolai fiókokkal rendelkező felhasználók jelentkezhetnek be az alkalmazás Azure Active Directoryból. |
| `organizations` |Lehetővé teszi, hogy csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók jelentkezhetnek be az alkalmazás Azure Active Directoryból. |
| `consumers` |Lehetővé teszi, hogy csak a felhasználók a személyes Microsoft-fiókok (MSA) jelentkezzen be az alkalmazást. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` vagy `contoso.onmicrosoft.com` |Az egy adott Azure Active Directory-bérlővel, jelentkezzen be az alkalmazás lehetővé teszi, hogy csak a munkahelyi vagy iskolai fiókkal rendelkező felhasználók. Az Azure AD-bérlő rövid tartományneve, vagy a bérlő guid-azonosító is használható. |

Hogyan kezelheti ezeket a végpontokat az bővebben az alábbi adott alkalmazás típusának kiválasztása.

## <a name="tokens"></a>Tokenek

Az OAuth 2.0 és OpenID Connect v2.0 megvalósítását győződjön erősen igénybe veszi a tulajdonosi jogkivonatokat, beleértve a tulajdonosi jogkivonatokat JWTs-kiszolgálókként. A tulajdonosi jogkivonatot, amely hozzáférést biztosít a "tulajdonos" védett erőforrásokhoz való könnyű biztonsági jogkivonat. Ebben az értelemben a "tulajdonos" minden olyan entitás, amely a token is jelenthet. Bár egy entitás először hitelesítenie kell magát az Azure AD-fogadni a tulajdonosi jogkivonattal, ha a szükséges lépéseket a rendszer nem hajtja végre a biztonságos átvitelét és tárolását a jogkivonatot, hozzá, és egy nem kívánt entitás használják. Néhány biztonsági jogkivonatokat rendelkezik egy beépített mechanizmus megakadályozza az illetéktelen fél használja őket, amíg a tulajdonosi jogkivonatokat nem kell ezt a mechanizmust, és kell szállítani, például a transport layer security (HTTPS) biztonságos csatornán. A titkosítatlan továbbított tulajdonosi jogkivonattal, ha a man-a a középső támadás segítségével egy rosszindulatú fél beszerezni a jogkivonatot, és használja a védett erőforrások jogosulatlan hozzáférési. A biztonsági alapelveket alkalmazható, ha a tárolásával, sem a gyorsítótárazás a tulajdonosi jogkivonatokat későbbi használatra. Mindig győződjön meg arról, hogy az alkalmazás továbbítja, és biztonságosan tárolja a tulajdonosi jogkivonatokat. A tulajdonosi jogkivonatokat további biztonsági szempontokért lásd: [RFC 6750 5. szakasz](https://tools.ietf.org/html/rfc6750).

További részletekért lásd a v2.0-végpont használt tokenek különböző típusú érhető el a [a v2.0-végpont jogkivonat referenciái](v2-id-and-access-tokens.md).

## <a name="protocols"></a>Protokollok

Ha szeretné látni, néhány példa kérelmek, az egyik első lépései az alábbi oktatóanyagok. Mindegyik felel meg egy adott hitelesítési forgatókönyv. Ha az Ön számára a megfelelő folyamat megállapításához segítségre van szüksége, tekintse meg az [a típusú alkalmazásokat hozhat létre a v2.0-](v2-app-types.md).

* [Az OAuth 2.0-val a mobil- és natív alkalmazás létrehozása](v2-oauth2-auth-code-flow.md)
* [Az Open ID Connect web Apps-alkalmazások](v2-protocols-oidc.md)
* [Az OAuth 2.0 Implicit folyamat egyoldalas alkalmazások fejlesztése](v2-oauth2-implicit-grant-flow.md)
* [Démonok vagy az OAuth 2.0 ügyfél hitelesítő adatai Flow-kiszolgáló oldalán folyamatok létrehozása](v2-oauth2-client-creds-grant-flow.md)
* [A jogkivonatok lekérésére, webes API-t az OAuth 2.0--meghatalmazásos folyamat](v2-oauth2-on-behalf-of-flow.md)
