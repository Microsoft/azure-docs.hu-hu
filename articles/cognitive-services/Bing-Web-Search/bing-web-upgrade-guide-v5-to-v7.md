---
title: Verziófrissítés az API V5-ből a v7-Bing Web Search APIra
titleSuffix: Azure Cognitive Services
description: Határozza meg, hogy az alkalmazás mely részeit kell frissíteni a Bing Web Search v7 API-k használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349520"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Frissítés Bing Web Search API V5-ről a v7-re

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ez a frissítési útmutató a Bing Web Search API 5. és 7. verziójának változásait azonosítja. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma a V5 verzióról a v7-re változott. Például: https: \/ \/ API.Cognitive.microsoft.com/Bing/**v 7.0**/Search.

### <a name="error-response-objects-and-error-codes"></a>Hibás objektumok és hibakódok

- Az összes sikertelen kérelemnek tartalmaznia kell egy `ErrorResponse` objektumot a válasz törzsében.

- A következő mezőket adta hozzá az `Error` objektumhoz.  
  - `subCode`&mdash;A hibakódot különálló gyűjtőbe particionálja, ha lehetséges
  - `moreDetails`&mdash;További információ a mezőben leírt hibáról `message`


- Az V5-hibakódokat a következő lehetséges `code` és értékekkel cserélte le `subCode` .

|Code|Alkód|Description
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>Nincs implementálva|A Bing visszaadja a ServerError, ha az alkódok bármely feltétele teljesül. A válasz ezeket a hibákat fogja tartalmazni, ha a HTTP-állapotkód 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blokkolva|A Bing visszaadja a InvalidRequest, ha a kérelem bármely része érvénytelen. Például hiányzik egy kötelező paraméter, vagy a paraméter értéke érvénytelen.<br/><br/>Ha a hiba ParameterMissing vagy ParameterInvalidValue, a HTTP-állapotkód 400.<br/><br/>Ha a hiba HttpNotAllowed, a 410-es HTTP-állapotkód.
|RateLimitExceeded||A Bing visszaadja a RateLimitExceeded, amikor a másodpercenkénti lekérdezések (QPS) vagy a havi lekérdezés (QPM) kvóta meghaladja a kvótát.<br/><br/>A Bing a 429-as HTTP-állapotkódot adja vissza, ha túllépte a QPS és a 403 értéket, ha túllépte a QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|A Bing visszaadja a InvalidAuthorization, ha a Bing nem tudja hitelesíteni a hívót. Például `Ocp-Apim-Subscription-Key` hiányzik a fejléc, vagy az előfizetési kulcs érvénytelen.<br/><br/>A redundancia akkor fordul elő, ha egynél több hitelesítési módszert ad meg.<br/><br/>Ha a hiba InvalidAuthorization, a HTTP-állapotkód 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|A Bing visszaadja a InsufficientAuthorization, ha a hívónak nincs engedélye az erőforrás elérésére. Ez a hiba akkor fordulhat elő, ha az előfizetési kulcs le van tiltva vagy lejárt. <br/><br/>Ha a hiba InsufficientAuthorization, a HTTP-állapotkód 403.

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


## <a name="non-breaking-changes"></a>Nem törhető változások  

### <a name="headers"></a>Fejlécek

- Hozzáadta a választható [sorpragmákat](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma) -kérelem fejlécét. Alapértelmezés szerint a Bing gyorsítótárazott tartalmat ad vissza, ha van ilyen. Ha nem szeretné, hogy a Bing gyorsítótárazott tartalmat adjon vissza, állítsa a Pragma fejlécet no-cache értékre (például Pragma: no-cache).

### <a name="query-parameters"></a>Lekérdezési paraméterek

- A [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) lekérdezési paraméter hozzáadva. Ezzel a paraméterrel adhatja meg, hogy a válasz hány választ tartalmazzon. A válaszok rangsorolás alapján vannak kiválasztva. Ha például a paramétert három (3) értékre állítja be, a válasz az első három rangsorolt választ tartalmazza.  

- A [promóció](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) lekérdezési paramétere hozzáadva. Használja ezt a paramétert, `answerCount` hogy explicit módon tartalmazzon egy vagy több választ, függetlenül azok rangsorolásának. A videók és a képek válaszba való előléptetéséhez például az előléptetést a *videók, a képek* lehetőségre kell beállítani. Az előléptetni kívánt válaszok listája nem számít bele a `answerCount` korlátba. Ha például a 2 értékre `answerCount` van állítva, és a `promote` *videókra, a képekre* van beállítva, a válasz tartalmazhat weblapokat, híreket, videókat és képeket.

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta a `someResultsRemoved` mezőt a [webválaszi](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) objektumhoz. A mező egy logikai értéket tartalmaz, amely jelzi, hogy a válasz kizárta-e a webes válasz eredményét.