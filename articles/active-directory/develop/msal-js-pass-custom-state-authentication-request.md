---
title: Egyéni állapot továbbítása hitelesítési kérelmekben (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan adhat át egyéni állapotú paramétereket a hitelesítési kérelemben a JavaScripthez készült Microsoft Authentication Library (MSAL.js) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063671"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Egyéni állapot továbbítása hitelesítési kérelmekben MSAL.js használatával

A OAuth 2,0 által definiált *State* paraméter a hitelesítési kérelem része, és a jogkivonat válaszában is visszaadja, hogy megakadályozza a helyek közötti kérelmek hamisításának elleni támadásokat. Alapértelmezés szerint a JavaScripthez készült Microsoft Authentication Library (MSAL.js) egy véletlenszerűen generált egyedi *állapot* paramétert ad meg a hitelesítési kérelmekben.

Az állapot paraméter az alkalmazás állapotának az átirányítás előtt történő kódolására is használható. Átadhatja a felhasználó állapotát az alkalmazásban, például a lapon vagy a nézetben, a paraméter bemenetének megfelelően. A MSAL.js könyvtár lehetővé teszi, hogy a következő objektumban adja át az egyéni állapot paramétert `Request` :

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Ha egy gyorsítótárazott tokent szeretne kihagyni, és a kiszolgálóra lép, adja át a logikai értéket `forceRefresh` a bejelentkezési/jogkivonat-kérelem létrehozásához használt AuthenticationParameters objektumba.
> `forceRefresh` Alapértelmezés szerint nem ajánlott használni az alkalmazás teljesítményére gyakorolt hatás miatt.
> A gyorsítótárra támaszkodva jobb felhasználói élményt biztosíthat a felhasználóknak.
> A gyorsítótár kihagyása csak olyan helyzetekben használható, ahol tudja, hogy a jelenleg gyorsítótárazott adatok nem rendelkeznek naprakész információkkal.
> Például egy felügyeleti eszköz, amely szerepköröket rendel hozzá egy olyan felhasználóhoz, akinek új jogkivonatot kell beszereznie a frissített szerepkörökkel.

Például:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

A rendszer hozzáfűzi az átadott állapotot a kérés elküldésekor MSAL.js egyedi GUID-készlethez. Ha a rendszer visszaadja a választ, MSAL.js ellenőrzi az állapot egyezését, majd az objektumban lévő egyéni átadott állapotot adja vissza `Response` `accountState` .

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

További információ: [egyoldalas alkalmazások (Spa) létrehozása](scenario-spa-overview.md) MSAL.js használatával.