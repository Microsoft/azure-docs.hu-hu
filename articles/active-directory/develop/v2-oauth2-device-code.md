---
title: Használja a Microsoft identity platform a felhasználók a böngésző nélküli eszközökön |} Az Azure
description: Az eszköz kódmegadásának használata embedded és a böngésző nélküli hitelesítési folyamatokat hozhat létre.
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
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e92e4d0e296e83b413cfd2a67041a5749c16699e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482237"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>A Microsoft identity platform és az OAuth 2.0 eszköz kódfolyamat

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

A Microsoft identity platform támogatja a [eszköz kódmegadás](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), amely lehetővé teszi, hogy a felhasználók számára, hogy jelentkezzen be a bemeneti korlátozott eszközök, mint egy Okostelevízió, IoT-eszközből vagy nyomtató.  Ahhoz, hogy ez a folyamat, az eszköz a böngészőben egy másik eszközön, hogy jelentkezzen be a felhasználói látogatás egy weblap rendelkezik.  Miután a felhasználó bejelentkezik, az eszköz nem képes hozzáférési és frissítési jogkivonatok, igény szerint.  

> [!IMPORTANT]
> Jelenleg a Microsoft identity platform végpont csak támogatja az eszköz a folyamat az Azure AD-bérlőt, de nem személyes fiókokat.  Ez azt jelenti, hogy bérlőként, állítsa be a végpont kell használnia, vagy a `organizations` végpont.  Ez a támogatás hamarosan engedélyezve lesz. 
>
> Személyes fiókokat is meghív egy Azure AD-bérlővel, használhatja az eszköz folyamat engedély megadása, de csak a bérlői kontextusában lesz.
>
> Egy további megjegyzés a `verification_uri_complete` válasz mező nem tartalmaz, vagy jelenleg támogatott.  

> [!NOTE]
> A Microsoft identity platform végpont nem támogatja az összes Azure Active Directory-forgatókönyvek és funkciók. Annak megállapításához, hogy a Microsoft identity platform végpontot kell használnia, olvassa el [a Microsoft identity platform korlátozásai](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes eszköz hitelesítésikód-folyamata a következő diagram hasonlóan néz ki. A cikk későbbi részében lépések mindegyikét ismertetünk.

![Eszköz kódfolyamat](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Eszköz engedélyezési kérése

Az ügyfél először ellenőriznie kell az eszköz- és kódot, amellyel hitelesítését authentication-kiszolgálóval. Az ügyfél ezt a kérelmet a gyűjti a `/devicecode` végpont. A kéréshez az ügyfél is tartalmaznia kell az engedélyeket kell beszerezni a felhasználó elől. Attól a pillanattól kezdve a kérelem érkezik, a felhasználó rendelkezik-e bejelentkezni csak 15 perc (a szokásos értéke `expires_in`), így csak ilyen kérést, ha a felhasználó azt jelezte, azok készen jelentkezzen be.

> [!TIP]
> Próbálja ki a Postmanben a kérelem végrehajtása!
> [![Próbálja meg futtatni a postmanben a kérelem](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező |A directory-bérlőhöz, amelyet szeretne az engedélyt. Ez lehet GUID vagy rövid név formátumban.  |
| `client_id` | Kötelező | A **Alkalmazásazonosítót (ügyfél)** , amely a [az Azure-portál – alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) az alkalmazáshoz rendelt felhasználói élményt. |
| `scope` | Ajánlott | Szóközzel elválasztott listáját [hatókörök](v2-permissions-and-consent.md) , hogy szeretné-e a felhasználót, hogy engedélyt adjanak az.  |

### <a name="device-authorization-response"></a>Eszköz hitelesítési válasza

A sikeres válasz, hogy a felhasználó jelentkezzen be a szükséges adatokat tartalmazó JSON-objektum lesz.  

| Paraméter | Formátum | Leírás |
| ---              | --- | --- |
|`device_code`     | String | Hosszú karakterlánc, amellyel az ügyfél és az engedélyezési kiszolgáló között a munkamenet ellenőrzése. Az ügyfél használja ezt a paramétert, a hozzáférési jogkivonatot kér az engedélyezési kiszolgálón. |
|`user_code`       | String | Egy rövid karakterlánc jelenik meg a felhasználót, hogy a munkamenet egy másodlagos eszközön azonosítására szolgál.|
|`verification_uri`| URI-T | A felhasználónak el kell küldeni az URI a `user_code` annak érdekében, hogy jelentkezzen be. |
|`expires_in`      | int | Mielőtt másodpercben a `device_code` és `user_code` lejár. |
|`interval`        | int | Az ügyfélnek várnia kell lekérdezési kérelmek közötti másodpercek számát. |
| `message`        | String | A felhasználói utasításokat emberek számára olvasható karakterlánc. Ez azzal kell honosított egy **lekérdezési paraméter** az űrlap a kérésben `?mkt=xx-XX`, a megfelelő nyelvi kulturális környezet kód töltését. |

## <a name="authenticating-the-user"></a>A felhasználó hitelesítése

Fogadás után a `user_code` és `verification_uri`, az ügyfél megjeleníti ezeket a felhasználó ezt őket, jelentkezzen be a mobiltelefonon vagy PC-böngésző.  Emellett az ügyfél egy QR-kódot vagy egy hasonló mechanizmus megjelenítéséhez használható a `verfication_uri_complete`, amely vesz igénybe, írja be a lépés a `user_code` a felhasználó számára.

A felhasználó hitelesíti magát amíg a `verification_uri`, az ügyfél lekérdezési kell lennie a `/token` végpont a kért jogkivonat használatával a `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Paraméter | Kötelező | Leírás|
| -------- | -------- | ---------- |
| `grant_type` | Kötelező | Kell lennie `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Kötelező | Meg kell egyeznie a `client_id` a kiindulási kérelemhez használt. |
| `device_code`| Kötelező | A `device_code` adja vissza az eszköz engedélyezési kérésben.  |

### <a name="expected-errors"></a>Várt hibák

Az eszköz kódfolyamat egy olyan lekérdezési protokoll, így az ügyfél kell várhatóan hibák lépnek fel a felhasználó befejezte a hitelesítése előtt.  

| Hiba | Leírás | Ügyfélművelet |
| ------ | ----------- | -------------|
| `authorization_pending` | A felhasználó hitelesítése nem fejeződött be, de nem a folyamat megszakadt. | Ismételje meg a kérelem után legalább `interval` másodperc. |
| `authorization_declined` | A felhasználó a hitelesítési kérelem megtagadva.| Lekérdezés leállítása, és a egy nem hitelesített állapotba visszaállítása.  |
| `bad_verification_code`| A `device_code` küldött a `/token` végpont nem ismerhető fel. | Győződjön meg arról, hogy az ügyfél a megfelelő elküldése `device_code` a kérésben. |
| `expired_token` | Legalább `expires_in` másodperc megfeleltek, és a hitelesítés már nem lehetséges a `device_code`. | Lekérdezés leállítása, és a egy nem hitelesített állapotba visszaállítása. |

### <a name="successful-authentication-response"></a>A sikeres hitelesítési válaszra.

Token sikeres válasz fog kinézni:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Paraméter | Formátum | Leírás |
| --------- | ------ | ----------- |
| `token_type` | String| Mindig "tulajdonosi. |
| `scope` | Szóközzel elválasztva karakterláncok | Egy hozzáférési jogkivonatot adott vissza, ha ez a hozzáférési jogkivonat érvénytelen, a hatókörök sorolja fel. |
| `expires_in`| int | A csomagban foglalt hozzáférési jogkivonat előtt eltelt másodpercek száma esetén érvényes. |
| `access_token`| Átlátszatlan karakterlánc | Kiadva a [hatókörök](v2-permissions-and-consent.md) , amely lett kérve.  |
| `id_token`   | JWT | Kiadott, ha az eredeti `scope` paraméter tartalmazza a `openid` hatókör.  |
| `refresh_token` | Átlátszatlan karakterlánc | Ha kiállított az eredeti `scope` paraméter `offline_access`.  |

A frissítési jogkivonat segítségével szerezzen új hozzáférési jogkivonatok, és frissítési jogkivonatok segítségével dokumentált ugyanezt a folyamatot a [OAuth kód flow dokumentáció](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
