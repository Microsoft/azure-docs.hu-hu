---
title: Bing News Search API V5 frissítése a v7-re
titleSuffix: Azure Cognitive Services
description: Annak a Bing News Search alkalmazásnak a részeit azonosítja, amelyet frissítenie kell a 7-es verzió használatára.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: scottwhi
ms.openlocfilehash: a114cb24d79189f9e370fae1962f60ca97241d90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96351367"
---
# <a name="news-search-api-upgrade-guide"></a>News Search API-frissítési útmutató

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ez a frissítési útmutató a Bing News Search API 5. és 7. verziójának változásait azonosítja. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma a V5 verzióról a v7-re változott. Például: `https://api.cognitive.microsoft.com/bing/v7.0/news/search`.

### <a name="error-response-objects-and-error-codes"></a>Hibás objektumok és hibakódok

- Az összes sikertelen kérelemnek tartalmaznia kell egy `ErrorResponse` objektumot a válasz törzsében.

- A következő mezőket adta hozzá az `Error` objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőbe particionálja, ha lehetséges
  - `moreDetails`&mdash;További információ a mezőben leírt hibáról `message`

- Az V5-hibakódokat a következő lehetséges `code` és értékekkel cserélte le `subCode` .

|Code|Alkód|Leírás
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing visszaadja a ServerError, ha az alkódok bármely feltétele teljesül. A válasz tartalmazza ezeket a hibákat, ha a HTTP-állapotkód 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolva|A Bing visszaadja a InvalidRequest, ha a kérelem bármely része érvénytelen. Például hiányzik egy kötelező paraméter, vagy a paraméter értéke érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a 410-es HTTP-állapotkód.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded, amikor a másodpercenkénti lekérdezések (QPS) vagy a havi lekérdezés (QPM) kvóta meghaladja a kvótát.<br/><br/>A Bing a 429-as HTTP-állapotkódot adja vissza, ha túllépte a QPS és a 403 értéket, ha túllépte a QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. Például `Ocp-Apim-Subscription-Key` hiányzik a fejléc, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

- A következő leképezi az előző hibakódokat az új kódokra. Ha az V5-hibakódok függőségét vette igénybe, frissítse a kódját ennek megfelelően.

|5. verzió kódja|7-es verzió kódja. alkód
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled (Letiltva)|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError. UnexpectedError
DataSourceErrors|ServerError. ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
Nincs implementálva|ServerError. nincs implementálva
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blokkolva|InvalidRequest. Blocked

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta a `contractualRules` mezőt a [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objektumhoz. A `contractualRules` mező tartalmazza a követni kívánt szabályok listáját (például cikk-hozzárendelés). A használata helyett a megadott hozzárendelést kell alkalmaznia `contractualRules` `provider` . A cikk `contractualRules` csak akkor szerepel, ha a [Web Search API](../bing-web-search/overview.md) -válasz hírekre adott választ tartalmaz.

## <a name="non-breaking-changes"></a>Nem törhető változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- A termékek olyan lehetséges értékként lettek hozzáadva, mint a [Kategória](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#category) lekérdezési paraméterének beállítása. Tekintse meg [a kategóriákat piacok alapján](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

- Hozzáadta a [sortby](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortby) lekérdezési paramétert, amely a legutóbb megjelenő dátum szerint rendezve jeleníti meg a trendi témákat.

- A lekérdezési paraméter [óta](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#since) hozzáadott egy, a Bing által a megadott UNIX-időbélyeggel vagy azt követően felderített trendek témakört.

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta a `mentions` mezőt a [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objektumhoz. A `mentions` mező a cikkben található entitások (személyek vagy helyek) listáját tartalmazza.

- Hozzáadta a `video` mezőt a [NewsArticle](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) objektumhoz. A `video` mező a újságcikkhez kapcsolódó videót tartalmaz. A videó \<iframe\> beágyazható vagy mozgó miniatűr is lehet.

- Hozzáadta a `sort` mezőt a [News](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) objektumhoz. A `sort` mező a cikkek rendezési sorrendjét jeleníti meg. A cikkek például a relevancia (alapértelmezett) vagy a dátum szerint vannak rendezve.

- Hozzáadta a [SortValue](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#sortvalue) objektumot, amely meghatározza a rendezési sorrendet. A `isSelected` mező jelzi, hogy a válasz a rendezési sorrendet használta-e. Ha az **értéke igaz**, a válasz a rendezési sorrendet használta. Ha a `isSelected` értéke **false (hamis**), a mezőben szereplő URL-cím használatával `url` más rendezési sorrendet is igényelhet.