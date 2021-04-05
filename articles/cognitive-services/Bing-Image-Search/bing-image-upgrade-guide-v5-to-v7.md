---
title: Frissítés Bing Image Search API V5-ről a v7-re
titleSuffix: Azure Cognitive Services
description: Ez a frissítési útmutató a Bing Image Search API 5. és 7. verziójának változásait ismerteti. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d10db37934bb0d6571eb0191d5f1be47dae000ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96342175"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Bing Image Search API v7 frissítési útmutató

> [!WARNING]
> Bing Search API-k átkerülnek a Cognitive Servicesról Bing Search szolgáltatásokra. **2020. október 30-ig** a Bing Search új példányait az [itt](/bing/search-apis/bing-web-search/create-bing-search-service-resource)ismertetett eljárás követésével kell kiépíteni.
> A Cognitive Services használatával kiépített Bing Search API-k a következő három évben vagy a Nagyvállalati Szerződés végéig lesz támogatva, attól függően, hogy melyik történik először.
> Az áttelepítési utasításokért lásd: [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Ez a frissítési útmutató a Bing Image Search API 5. és 7. verziójának változásait azonosítja. Ez az útmutató segítséget nyújt az alkalmazás azon részeinek azonosításához, amelyeket frissítenie kell a 7-es verzió használatához.

## <a name="breaking-changes"></a>Kompatibilitástörő változások

### <a name="endpoints"></a>Végpontok

- A végpont verziószáma a V5 verzióról a v7-re változott. Például https: \/ /API.Cognitive.microsoft.com/Bing/ \* \* v 7.0 * */images/Search.

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



### <a name="query-parameters"></a>Lekérdezési paraméterek

- Átnevezte a `modulesRequested` lekérdezési paramétert a [modulokra](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Átnevezte a megjegyzéseket a címkékre. Lásd: [modulok](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) lekérdezési paramétere címkékre.  

- Módosította a ShoppingSources szűrő értékének támogatott piacainak listáját csak en-US értékre. Lásd: [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Képelemzési változások

- Átnevezte a `annotations` [ImagesInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) mezőjét a következőre: `imageTags` .  

- Átnevezte a `AnnotationModule` [ImageTagsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule)objektumot.  

- Átnevezte az `Annotation` objektumot a [címkére](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag), és eltávolította a `confidence` mezőt.  

- Átnevezte a `insightsSourcesSummary` [rendszerkép](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektum mezőjét a következőre: `insightsMetadata` .  

- Átnevezte a `InsightsSourcesSummary` [InsightsMetadata](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata)objektumot.  

- A végpont hozzáadva `https://api.cognitive.microsoft.com/bing/v7.0/images/details` . Ezzel a végponttal az/images/Search végpont helyett képelemzéseket kérhet. Lásd: [Képelemzések](./image-insights.md).

- A következő lekérdezési paraméterek mostantól csak a `/images/details` végpontnál érvényesek.  

    -   [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modulok](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [Cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [Cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [az](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Átnevezte a `ImageInsightsResponse` [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights)objektumot.  

- Módosította a következő mezők adattípusait a [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) objektumban.  

    -   Módosította a mező típusát a `relatedCollections` `ImageGallery[]` [RelatedCollectionsModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule)értékre.  

    -   Módosította a mező típusát a `pagesIncluding` `Image[]` [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)értékre.  

    -   Módosította a mező típusát a `relatedSearches` `Query[]` [RelatedSearchesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule)értékre.  

    -   Módosította a mező típusát a `recipes` `Recipe[]` [RecipesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule)értékre.  

    -   Módosította a mező típusát a `visuallySimilarImages` `Image[]` [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)értékre.  

    -   Módosította a mező típusát a `visuallySimilarProducts` `ProductSummaryImage[]` [ImagesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule)értékre.  

    -   Eltávolította az `ProductSummaryImage` objektumot, és áthelyezte a termékkel kapcsolatos mezőket a [rendszerkép](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) objektumba. Az `Image` objektum csak akkor tartalmazza a termékkel kapcsolatos mezőket, ha a képet egy képelemzési válaszban szereplő, hasonló termékek részeként jeleníti meg.  

    -   Módosította a mező típusát a `recognizedEntityGroups` `RecognizedEntityGroup[]` [RecognizedEntitiesModule](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule)értékre.  

-   Átnevezte a `categoryClassification` [ImageInsights](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) mezőjét `annotations` , és módosította a típusát `AnnotationsModule` .  

### <a name="images-answer"></a>A képek válasza

-   Eltávolította a displayShoppingSourcesBadges és a displayRecipeSourcesBadges mezőket a [képekből](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images).  

-   Átnevezte a `nextOffsetAddCount` [képek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) mezőjét a következőre: `nextOffset` . Az eltolás használatának módja is megváltozott. Korábban az [eltolás](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) lekérdezési paraméter `nextOffsetAddCount` értékét az előző eltolási értékre és az eredményben szereplő rendszerképek számára kell beállítani. Most `offset` az értékre kell állítani `nextOffset` .  


## <a name="non-breaking-changes"></a>Nem törhető változások

### <a name="query-parameters"></a>Lekérdezési paraméterek

- A rendszer transzparensként adja hozzá a lehetséges [imageType](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype) -szűrő értékét. Az átlátszó szűrő csak az átlátszó háttérrel rendelkező képeket adja vissza.

- Hozzáadta a bármely lehetséges [licenckulcs](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license) -értéket. A bármely szűrő csak a licenccel rendelkező lemezképeket adja vissza.

- Hozzáadta a [maxFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) és a [minFileSize](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) lekérdezési paramétereket. Ezekkel a szűrőkkel a képfájlok egy tartományán belüli képeket adhatnak vissza.  

- Hozzáadta a [maxHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), a [minHeight](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), a [maxWidth](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth)és a [MinWidth értékénél](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) lekérdezési paramétereket. Ezekkel a szűrőkkel a képek a magasság és a szélesség tartományán belül adhatók vissza.  

### <a name="object-changes"></a>Objektum változásai

- Hozzáadta a `description` és a `lastUpdated` mezőket az [ajánlat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) objektumhoz.  

- Hozzáadta a `name` mezőt a [ImageGallery](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) objektumhoz.  

- Hozzáadva `similarTerms` a [képek](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) objektumhoz. Ez a mező a felhasználó lekérdezési karakterláncához hasonló kifejezések listáját tartalmazza.