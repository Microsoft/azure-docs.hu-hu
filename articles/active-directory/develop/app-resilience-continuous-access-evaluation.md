---
title: A folyamatos hozzáférés kiértékelését engedélyező API-k használata az alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Az alkalmazások biztonságának és rugalmasságának növelése a folyamatos hozzáférés kiértékelésének támogatásával, a kritikus események és a házirendek kiértékelése alapján visszavonható, hosszú élettartamú hozzáférési tokenek engedélyezésével.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652058"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>A folyamatos hozzáférés kiértékelését engedélyező API-k használata az alkalmazásokban

A [folyamatos hozzáférés kiértékelése](../conditional-access/concept-continuous-access-evaluation.md) (CAE) egy új iparági szabvány, amely lehetővé teszi a hozzáférési tokenek visszavonását a [kritikus események](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) és a [házirendek kiértékelése](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) alapján, nem pedig a jogkivonat lejáratának élettartama alapján. Egyes erőforrás-API-k esetében, mivel a kockázat és a házirend valós időben lett kiértékelve, ez a token élettartamát akár 28 óráig is növelheti. Ezek a hosszú élettartamú tokenek proaktív módon frissülnek a Microsoft Authentication Library (MSAL) használatával, ami növeli az alkalmazások rugalmasságát.

Ez a cikk bemutatja, hogyan használhatók a CAE-kompatibilis API-k az alkalmazásokban.

## <a name="implementation-considerations"></a>Implementálási szempontok

A folyamatos hozzáférés kiértékeléséhez az alkalmazásnak és az erőforrás-API-nak is elérhetőnek kell lennie a CAE-enabled beállításban. Azonban a kód előkészítése a CAE-kompatibilis erőforrások használatára nem akadályozza meg, hogy olyan API-kat használjon, amelyek nem engedélyezettek a CAE-ben.

Ha egy erőforrás-API a CAE-t implementálja, és az alkalmazása kijelenti, hogy képes a CAE kezelésére, az alkalmazás az adott erőforráshoz tartozó CAE-jogkivonatokat kap. Ebben az esetben, ha az alkalmazáshoz tartozó CAE-t kinyilvánítja, az alkalmazásnak kezelnie kell a CAE-jogcím kihívását minden olyan erőforrás-API esetében, amely elfogadja a Microsoft Identity hozzáférési jogkivonatokat. Ha nem kezeli a CAE-válaszokat ezekben az API-hívásokban, az alkalmazás egy hurokban próbálkozhat egy olyan tokenrel, amely még mindig a token visszaadott élettartamában van, de a CAE miatt visszavonták.

## <a name="the-code"></a>A kód

Első lépésként hozzá kell adnia egy kódot az erőforrás-API válaszának kezeléséhez, amely elutasítja a CAE által okozott hívást. A CAE esetében az API-k 401 állapotot és WWW-Authenticate fejlécet adnak vissza, ha a hozzáférési tokent visszavonták, vagy az API észleli a használt IP-cím változását. Az WWW-Authenticate fejléc olyan jogcímeket tartalmaz, amelyeket az alkalmazás használhat új hozzáférési jogkivonat beszerzéséhez.

Például:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Az alkalmazás a következőket vizsgálja:

- az 401 állapotot visszaadó API-hívás
- a következőket tartalmazó WWW-Authenticate fejléc megléte:
  - "insufficient_claims" értékű "Error" paraméter
  - "jogcím" paraméter

Ha ezek a feltételek teljesülnek, az alkalmazás képes kinyerni és dekódolni a jogcímek kérdését.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Az alkalmazás ezután a jogcímek Challenge használatával új hozzáférési jogkivonatot fog beszerezni az erőforráshoz.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Ha az alkalmazás készen áll a CAE-t használó erőforrás által visszaadott jogcímek kezelésére, megtudhatja, hogy a Microsoft Identity készen áll-e az alkalmazásra. Ehhez a MSAL-alkalmazásban hozza létre a nyilvános ügyfelet a "CP1" ügyfél-képességeinek használatával.

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Az alkalmazás teszteléséhez jelentkezzen be egy felhasználót az alkalmazásba, majd használja a Azure Portal a felhasználó munkameneteinek visszavonásához. Amikor az alkalmazás legközelebb meghívja a CAE-kompatibilis API-t, a rendszer megkéri a felhasználót, hogy végezze el a hitelesítést.

## <a name="next-steps"></a>Következő lépések

További információ: [folyamatos hozzáférés kiértékelése](../conditional-access/concept-continuous-access-evaluation.md).
