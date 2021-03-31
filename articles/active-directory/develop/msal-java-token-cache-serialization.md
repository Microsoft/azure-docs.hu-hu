---
title: Egyéni jogkivonat-gyorsítótár szerializálása (MSAL4j)
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan szerializálhatja a MSAL for Java jogkivonat-gyorsítótárát
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: e85cfb8c03111b889a9f95509f337d415c517163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87312367"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Egyéni jogkivonat-gyorsítótár szerializálás a MSAL-ben Javához

Ha meg szeretné őrizni a jogkivonat-gyorsítótárat az alkalmazás példányai között, testre kell szabnia a szerializálást. A jogkivonat-gyorsítótár szerializálásában résztvevő Java-osztályok és-felületek a következők:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): a biztonsági jogkivonat-gyorsítótárat jelképező csatoló.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): a hozzáférés előtt és után a kód végrehajtásának műveletét jelképező illesztő. Az @Override *beforeCacheAccess* és a *afterCacheAccess* a gyorsítótár szerializálására és deszerializálására felelős logikával.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): az interfészt jelképező környezet, amelyben a jogkivonat-gyorsítótár elérhető. 

Az alábbiakban a jogkivonat-gyorsítótár szerializálási/deszerializálása egyéni szerializálásának naiv implementációja látható. Ne másolja és illessze be ezt éles környezetbe.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Tudjon meg többet

Ismerje meg [, hogyan kérhet le és távolíthat el fiókokat a jogkivonat-gyorsítótárból a MSAL for Java használatával](msal-java-get-remove-accounts-token-cache.md).
