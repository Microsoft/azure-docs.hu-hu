---
title: Webes API-t hívó egyoldalas alkalmazás létrehozása
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webes API-t meghívó egyoldalas alkalmazást
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 4dd4a40233fc1c030581fd1ae2827061435a0ab3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98753544"
---
# <a name="single-page-application-call-a-web-api"></a>Egyoldalas alkalmazás: webes API meghívása

Javasoljuk, hogy a `acquireTokenSilent` webes API meghívása előtt hívja meg a metódust egy hozzáférési jogkivonat beszerzéséhez vagy megújításához. A jogkivonat meghívása után meghívhat egy védett webes API-t.

## <a name="call-a-web-api"></a>Webes API-hívás

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A beszerzett hozzáférési jogkivonatot tulajdonosként egy HTTP-kérelemben használhatja bármilyen webes API meghívásához, például Microsoft Graph API-t. Például:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

A MSAL szögletes burkolója kihasználja a HTTP-Interceptor előnyeit, hogy a hozzáférési jogkivonatokat csendesen szerezze be, és csatolja őket a HTTP-kérésekhez API-khoz. További információ: [token beszerzése API meghívásához](scenario-spa-acquire-token.md).

---

## <a name="next-steps"></a>Következő lépések

Az ebben a forgatókönyvben lévő következő cikkre való áttéréssel váltson [éles környezetbe](scenario-spa-production.md).