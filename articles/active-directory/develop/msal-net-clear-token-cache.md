---
title: Jogkivonat-gyorsítótár törlése (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan törölheti a jogkivonat-gyorsítótárat a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064759"
---
# <a name="clear-the-token-cache-using-msalnet"></a>A jogkivonat-gyorsítótár kiürítése a MSAL.NET használatával

Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával szerzi be a [hozzáférési jogkivonatot](msal-acquire-cache-tokens.md) , a rendszer gyorsítótárazza a tokent. Ha az alkalmazásnak tokenre van szüksége, először hívja meg a `AcquireTokenSilent` metódust annak ellenőrzéséhez, hogy elfogadható token van-e a gyorsítótárban. 

A gyorsítótár kiürítésével a fiókok a gyorsítótárból való eltávolításával érhetők el. Ez nem távolítja el a böngészőben található munkamenet-cookie-t, de.  A következő példa egy nyilvános ügyfélalkalmazás létrehozását, beolvassa az alkalmazás fiókját, és eltávolítja a fiókokat.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

A tokenek beszerzésével és gyorsítótárazásával kapcsolatos további tudnivalókért olvassa el a [hozzáférési token beszerzése](msal-acquire-cache-tokens.md)című témakört.
