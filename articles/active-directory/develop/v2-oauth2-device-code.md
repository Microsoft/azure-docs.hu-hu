---
title: OAuth 2,0-eszköz kódjának folyamata | Azure
titleSuffix: Microsoft identity platform
description: Felhasználói bejelentkezés böngésző nélkül. Beágyazott és böngésző nélküli hitelesítési folyamatokat hozhat létre az eszköz engedélyezési engedélye alapján.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 8c757f3e067aeac5d8145ca47b2eac145daba574
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88272450"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-authorization-grant-flow"></a>Microsoft Identity platform és a OAuth 2,0-eszköz engedélyezési folyamata

A Microsoft Identity platform támogatja az [eszköz engedélyezésének](https://tools.ietf.org/html/rfc8628)engedélyezését, amely lehetővé teszi a felhasználók számára, hogy bejelentkezzenek a bemeneti korlátozás alá tartozó eszközökre, például az intelligens TV-re, a IoT eszközre vagy a nyomtatóra.  A folyamat engedélyezéséhez az eszközön a felhasználó meglátogat egy weblapot a böngészőben egy másik eszközre a bejelentkezéshez.  Ha a felhasználó bejelentkezik, az eszköz igény szerint elérheti a hozzáférési jogkivonatokat, és szükség szerint frissítheti a jogkivonatokat.

Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

## <a name="protocol-diagram"></a>Protokoll diagramja

A teljes eszköz kódjának folyamata a következő diagramhoz hasonlóan néz ki. A cikk későbbi részében leírt lépéseket ismertetjük.

![Eszköz kódjának folyamata](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Eszköz-engedélyezési kérelem

Az ügyfélnek először ellenőriznie kell a hitelesítési kiszolgálót a hitelesítés kezdeményezéséhez használt eszköz és felhasználói kód esetében. Az ügyfél ezt a kérést a `/devicecode` végponttól gyűjti. Ebben a kérelemben az ügyfélnek tartalmaznia kell a felhasználótól beszerzett engedélyeket is. A kérelem elküldésekor a felhasználónak csak 15 perce van a bejelentkezéshez (a szokásos érték `expires_in` ), ezért csak akkor hajtsa végre ezt a kérést, ha a felhasználó jelezte, hogy készen áll a bejelentkezésre.

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán!
> [![Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```HTTP
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Paraméter | Feltétel | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | /Gyakori hibák,/consumers vagy/Organizations. lehet  Azt is megteheti, hogy az a címtár-bérlő, amelyhez engedélyeket szeretne kérni a GUID vagy a felhasználóbarát név formátumában.  |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal – Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felhasználói felület **(ügyfél) azonosítója** . |
| `scope` | Kötelező | Egy szóközzel tagolt lista, melyben a felhasználónak jóvá kell hagynia a [hatókört](v2-permissions-and-consent.md) .  |

### <a name="device-authorization-response"></a>Eszköz-engedélyezési válasz

A sikeres válasz egy JSON-objektum, amely tartalmazza a szükséges információkat, amelyek lehetővé teszik a felhasználó számára a bejelentkezést.

| Paraméter | Formátum | Leírás |
| ---              | --- | --- |
|`device_code`     | Sztring | Egy hosszú karakterlánc, amellyel ellenőrizhető a munkamenet az ügyfél és az engedélyezési kiszolgáló között. Az ügyfél ezzel a paraméterrel kéri le a hozzáférési jogkivonatot az engedélyezési kiszolgálóról. |
|`user_code`       | Sztring | Egy rövid karakterlánc, amelyet a rendszer a másodlagos eszköz munkamenetének azonosítására használt felhasználó számára mutat.|
|`verification_uri`| URI | A bejelentkezéshez a felhasználónak az URI azonosítóval kell rendelkeznie `user_code` . |
|`expires_in`      | int | A és a lejárat előtti másodpercek száma `device_code` `user_code` . |
|`interval`        | int | Azon másodpercek száma, ameddig az ügyfélnek várnia kell a lekérdezési kérelmek között. |
| `message`        | Sztring | Egy ember által olvasható karakterlánc, amely a felhasználóra vonatkozó utasításokat tartalmaz. Ez honosítható úgy, hogy egy **lekérdezési paramétert** is megadhat az űrlap kérelmében `?mkt=xx-XX` , a megfelelő nyelvi kulturális kód kitöltésével. |

> [!NOTE]
> A `verification_uri_complete` Válasz mező jelenleg nem szerepel vagy nem támogatott.  Ezt azért említjük, mert ha elolvasta a [szabványost](https://tools.ietf.org/html/rfc8628) , akkor az `verification_uri_complete` eszköz Code flow standard választható részeként jelenik meg.

## <a name="authenticating-the-user"></a>A felhasználó hitelesítése

A és a megérkezése után `user_code` `verification_uri` az ügyfél megjeleníti ezeket a felhasználókat, és arra utasítja őket, hogy jelentkezzenek be a mobiltelefon vagy a számítógép böngészője segítségével.

Ha a felhasználó személyes fiókkal (/gyakori hibák vagy/consumers) végez hitelesítést, a rendszer arra kéri, hogy jelentkezzen be újra a hitelesítési állapot az eszközre való átvitele érdekében.  A rendszer arra is felszólítja, hogy adja meg a beleegyező engedélyeket, hogy biztosan tisztában legyenek a megadott engedélyekkel.  Ez nem vonatkozik a hitelesítéshez használt munkahelyi vagy iskolai fiókokra.

Amíg a felhasználó hitelesítést `verification_uri` végez, az ügyfélnek a használatával kell lekérdezni a `/token` végpontot a kért jogkivonat számára `device_code` .

```HTTP
POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8...
```

| Paraméter | Kötelező | Leírás|
| -------- | -------- | ---------- |
| `tenant`  | Kötelező | A kezdeti kérelemben használt bérlői vagy bérlői alias. |
| `grant_type` | Kötelező | Kötelező `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Kötelező | Meg kell egyeznie a `client_id` kezdeti kérelemben használt értékkel. |
| `device_code`| Kötelező | A `device_code` visszaadott eszköz engedélyezési kérelme.  |

### <a name="expected-errors"></a>Várt hibák

Az eszköz kódjának folyamata egy lekérdezési protokoll, így az ügyfélnek meg kell várnia, hogy hibákat kapjon, mielőtt a felhasználó befejezte a hitelesítést.

| Hiba | Leírás | Ügyfél művelete |
| ------ | ----------- | -------------|
| `authorization_pending` | A felhasználó nem fejezte be a hitelesítést, de nem szakította meg a folyamatot. | Ismételje meg a kérést legalább `interval` másodperc múlva. |
| `authorization_declined` | A végfelhasználó megtagadta az engedélyezési kérelmet.| A lekérdezés leállítása és visszaállítása nem hitelesített állapotra.  |
| `bad_verification_code`| `device_code` `/token` Nem ismerhető fel a végpontnak elküldve. | Győződjön meg arról, hogy az ügyfél elküldi a megfelelőt a `device_code` kérelemben. |
| `expired_token` | Legalább `expires_in` másodperc telt el, és a hitelesítés már nem lehetséges `device_code` . | A lekérdezés leállítása és visszaállítása nem hitelesített állapotra. |

### <a name="successful-authentication-response"></a>Sikeres hitelesítési válasz

A jogkivonat sikeres válasza A következőképpen fog kinézni:

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
| `token_type` | Sztring| Mindig "tulajdonos". |
| `scope` | Szóközzel tagolt karakterláncok | Ha egy hozzáférési jogkivonatot adott vissza, akkor felsorolja azokat a hatóköröket, amelyekre érvényes a hozzáférési jogkivonat. |
| `expires_in`| int | A befoglalt hozzáférési jogkivonat érvényessége előtti másodpercek száma. |
| `access_token`| Átlátszatlan karakterlánc | A kért [hatókörökhöz](v2-permissions-and-consent.md) lett kiállítva.  |
| `id_token`   | JWT | Kiadva, ha az eredeti `scope` paraméter tartalmazza a `openid` hatókört.  |
| `refresh_token` | Átlátszatlan karakterlánc | Kiadva, ha az eredeti `scope` paraméter szerepel `offline_access` .  |

A frissítési jogkivonattal új hozzáférési jogkivonatok szerezhetők be, és a tokenek frissítése a [OAuth-programkód dokumentációjában](v2-oauth2-auth-code-flow.md#refresh-the-access-token)ismertetett folyamattal megegyező folyamattal végezhető el.
