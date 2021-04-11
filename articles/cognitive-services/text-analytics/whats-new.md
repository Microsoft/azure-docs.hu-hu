---
title: A Text Analytics API újdonságai
titleSuffix: Text Analytics - Azure Cognitive Services
description: Ez a cikk az Azure Cognitive Services Text Analytics új kiadásairól és funkcióiról nyújt információkat.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 0ed3a11381285a9422380eb14ff301a2b9ea816a
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106093554"
---
# <a name="whats-new-in-the-text-analytics-api"></a>A Text Analytics API újdonságai.

A Text Analytics API frissítése folyamatosan történik. Ha naprakészen szeretne maradni a legutóbbi fejleményekkel, ez a cikk az új kiadásokkal és szolgáltatásokkal kapcsolatos információkat tartalmaz.

## <a name="march-2021"></a>Március 2021

### <a name="general-api-updates"></a>Általános API-frissítések
* Az új API v 3.1 – preview. 4 kiadása, amely tartalmazza a következőket: 
   * Változások a vélemény bányászati JSON-válasz törzsében: 
      * `aspects` most `targets` és most `opinions` `assessments` . 
   * A Text Analytics for Health üzemeltetett webes API-jával kapcsolatos JSON-válasz törzsében bekövetkezett változások: 
      * Az `isNegated` észlelt entitás-objektum logikai nevét a rendszer elavultnak nyilvánítja, és lecseréli az érvényesítési észleléssel.
      * Egy új nevű tulajdonság `role` mostantól egy attribútum és egy entitás közötti kinyert kapcsolat része, valamint az entitások közötti kapcsolat.  Ez az észlelt kapcsolattípus sajátosságát adja meg.
   * Az entitások összekapcsolása mostantól aszinkron feladatként érhető el a `/analyze` végponton.
   * `pii-categories`Mostantól elérhető egy új paraméter a `/pii` végpontban.
      * Ezzel a paraméterrel megadhatja a személyre szabási entitások kiválasztását, valamint azokat, amelyeket a bemeneti nyelv alapértelmezés szerint nem támogat.
* Frissített ügyféloldali kódtárak, amelyek tartalmazzák az aszinkron elemzést és az Text Analytics az állapotfigyelő műveletekhez. Példákat a GitHubon talál:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples/javascript)
    
> [!div class="nextstepaction"]
> [További információ: Text Analytics API v 3.1 – előzetes verzió. 4](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-4/operations/Languages)

### <a name="text-analytics-for-health-updates"></a>Text Analytics az állapot frissítéseihez

* A `2021-03-01` végpont és a helyszíni tároló új modell-verziója, amely a következőt `/health` biztosítja:
    * Az `Gene` entitás típusának átnevezése a következőre: `GeneOrProtein` .
    * Új `Date` entitás típusa
    * Az érvényesítés észlelése, amely a tagadás észlelését váltja fel (csak az API v 3.1-es verziójában érhető el – előzetes verzió: 4).
    * A társított entitások új előnyben részesített `name` tulajdonsága, amely különböző ontológiákat és kódolási rendszerekből van normalizálva (csak az API v 3.1-es verziójában érhető el. 4). 
* Egy új, címkével `3.0.015490002-onprem-amd64` és az új modell-verzióval rendelkező tároló-rendszerkép `2021-03-01` lett kiadva a Container Preview adattárba. 
    * A tároló képe a továbbiakban nem lesz letölthető a `containerpreview.azurecr.io` 2021. április 26. után.
* Az állapot-tárolóhoz tartozó, ugyanazzal a modell-verzióval rendelkező új Text Analytics mostantól elérhető a következő címen: `mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare` . Április 26-ától kezdődően a tárolót csak ebből a tárházból töltheti le.

> [!div class="nextstepaction"]
> [További információ az Text Analyticsról az állapotról](how-tos/text-analytics-for-health.md)

### <a name="text-analytics-resource-portal-update"></a>Az erőforrás-portál Text Analytics frissítése
* A **feldolgozott szöveges rekordok** mostantól mérőszámként elérhetők a Azure Portal Text Analytics erőforrásának **figyelés** szakaszában.  

## <a name="february-2021"></a>2021. február

* A személyes adatokhoz tartozó `2021-01-15` végpont modell-verziója a [nevesített entitás-felismerés](how-tos/text-analytics-how-to-entity-linking.md) v 3.1-es verziójában – preview. x, amely a következőt biztosítja: 
  * Kiterjesztett támogatás 9 új nyelvhez
  * Továbbfejlesztett AI-minőség az elnevezett entitások kategóriái számára a támogatott nyelvekhez.
* Az S4 díjszabási szintjein keresztüli S0 a 2021. március 8-án megszűnik. Ha már rendelkezik egy meglévő Text Analytics erőforrással a S0 az S4 díjszabási szinten keresztül, akkor a standard (S) [árképzési](how-tos/text-analytics-how-to-call-api.md#change-your-pricing-tier)szint használatára kell frissítenie.
* A [nyelvfelismerés tároló](how-tos/text-analytics-how-to-install-containers.md?tabs=sentiment) már általánosan elérhető.
* az API v 2.1-es verziójának kivonása folyamatban van. 

## <a name="january-2021"></a>2021. január

* A `2021-01-15` modell verziója az [elnevezett Entity Recognition](how-tos/text-analytics-how-to-entity-linking.md) v3. x-nek, amely a következőt biztosítja: 
  * Bővített nyelvi támogatás [számos általános entitási kategóriához](named-entity-types.md). 
  * Az általános entitások kategóriáinak továbbfejlesztett AI-minősége az összes támogatott v3 nyelv esetében. 

* A `2021-01-05` [nyelvi észlelési](how-tos/text-analytics-how-to-language-detection.md)modell verziója, amely további [nyelvi támogatást](language-support.md?tabs=language-detection)biztosít.

Ezek a modell-verziók jelenleg nem érhetők el az USA keleti régiójában. 

> [!div class="nextstepaction"]
> [További információ az új tanulási modellről](https://azure.microsoft.com/updates/text-analytics-ner-improved-ai-quality)

## <a name="december-2020"></a>2020. december

* A Text Analytics API [frissített díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) .

## <a name="november-2020"></a>2020. november

* Egy [új végpont](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Analyze) Text Analytics API v 3.1-es verziójával – előzetes verzió. 3 az új ASZINKRON elemzési [API](how-tos/text-analytics-how-to-call-api.md?tabs=analyze)-hoz, amely támogatja a kötegelt feldolgozást az egyidejű, a személyes és a kulcsfontosságú kifejezéseket kinyerő műveletekhez.
* Egy [új végpont](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) Text Analytics API v 3.1-es verziójával (előzetes verzió), amely az új, az [állapotba](how-tos/text-analytics-for-health.md) helyezett API-hoz készült aszinkron Text Analytics a kötegelt feldolgozás támogatásával.
* A fent felsorolt új funkciók csak a következő régiókban érhetők el: `West US 2` , `East US 2` , `Central US` , `North Europe` és `West Europe` régiók.
* A portugál (brazíliai) `pt-BR` mostantól támogatott a [Hangulatelemzés](how-tos/text-analytics-how-to-sentiment-analysis.md) v3. x verzióban, a modell verziójától kezdve `2020-04-01` . Hozzáadja a `pt-PT` portugál meglévő támogatásához.
* Frissített ügyféloldali kódtárak, amelyek tartalmazzák az aszinkron elemzést és az Text Analytics az állapotfigyelő műveletekhez. Példákat a GitHubon talál:

    * [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
    * [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
    * [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)
    * 
> [!div class="nextstepaction"]
> [További információ: Text Analytics API v 3.1 – előzetes verzió. 3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

## <a name="october-2020"></a>2020. október

* Hindi támogatás a Hangulatelemzés v3. x verzióhoz, a modell verziójától kezdve `2020-04-01` . 
* Modell verziója `2020-09-01` a v3/languages végponthoz, amely növeli a nyelvfelismerés és a pontosság növelését.
* v3 elérhetőség Közép-Indiában és Észak-Egyesült Arab Emírségekben.

## <a name="september-2020"></a>2020. szeptember

### <a name="general-api-updates"></a>Általános API-frissítések

* A Text Analytics v 3.1 nyilvános előzetes verziójának új URL-címének kiadása a következő elnevezett Entity Recognition v3-végpontok frissítéseinek támogatásához: 
    * `/pii` a végpont mostantól tartalmazza a `redactedText` Válasz JSON-ban található új tulajdonságot, ahol a bemeneti szövegben található személyes adatokat feltüntető entitásokat az adott `*` entitások minden karaktere helyettesíti.
    * `/linking` a végpont mostantól tartalmazza a `bingID` csatolt entitások válasz JSON-fájljának tulajdonságát.
* A következő Text Analytics előzetes verziójú API-végpontok a 2020-es szeptember 4-ig lettek kivonva:
    * v 2.1 – előzetes verzió
    * 3.0-s előzetes verzió
    * v 3.0 – előzetes verzió. 1
    
> [!div class="nextstepaction"]
> [További információ: Text Analytics API v 3.1 – előzetes verzió. 2](quickstarts/client-libraries-rest-api.md)

### <a name="text-analytics-for-health-container-updates"></a>Text Analytics az állapot-tároló frissítéseihez

A következő frissítések csak az Text Analytics az állapotfigyelő tárolóra vonatkozó szeptemberi kiadására vonatkoznak.
* Az új Model-Version címkével rendelkező új tároló `1.1.013530001-amd64-preview` -rendszerkép `2020-09-03` megjelent a tároló előzetes adattárában. 
* A modell verziója az entitások felismerésének, a rövidítések észlelésének és a késések fejlesztésének fejlesztését teszi lehetővé.

> [!div class="nextstepaction"]
> [További információ az Text Analyticsról az állapotról](how-tos/text-analytics-for-health.md)

## <a name="august-2020"></a>2020. augusztus

### <a name="general-api-updates"></a>Általános API-frissítések

* Modell verziója `2020-07-01` a v3 `/keyphrases` és a `/pii` `/languages` végpontok számára, amely a következőket adja:
    * További kormányzati és ország-specifikus [entitások kategóriái](named-entity-types.md?tabs=personal) a nevesített entitások felismeréséhez.
    * Norvég és török támogatás a Hangulatelemzés v3-as verzióban.
* A rendszer mostantól HTTP 400-es hibát ad vissza az olyan V3 API-kérelmek esetében, amelyek túllépik a közzétett [adatkorlátot](concepts/data-limits.md). 
* Az eltolást visszaadó végpontok támogatják a választható `stringIndexType` paramétert, amely a visszaadott `offset` és az értékeket úgy állítja be, `length` hogy az megfeleljen egy támogatott [karakterlánc-indexelési sémának](concepts/text-offsets.md).

### <a name="text-analytics-for-health-container-updates"></a>Text Analytics az állapot-tároló frissítéseihez

A következő frissítések csak az Text Analytics az állapotfigyelő tárolóra vonatkozó augusztusi kiadására vonatkoznak.

* Új modell – a Text Analytics for Health verziója: `2020-07-24`
* Új URL-cím az Text Analytics küldéséhez az állapotadatok kéréséhez: `http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health` (vegye figyelembe, hogy a böngésző gyorsítótárának törlésére lesz szükség az új tároló-rendszerképben található bemutató webalkalmazás használatához)

A JSON-válasz következő tulajdonságai módosultak:

* A `type` új nevet kapott: `category` 
* A `score` új nevet kapott: `confidenceScore`
* `category`A JSON-kimenet mezőjében szereplő entitások mostantól Pascal-ügyben vannak. A következő entitások lettek átnevezve:
    * `EXAMINATION_RELATION` átnevezve: `RelationalOperator` .
    * `EXAMINATION_UNIT` átnevezve: `MeasurementUnit` .
    * `EXAMINATION_VALUE` átnevezve: `MeasurementValue` .
    * `ROUTE_OR_MODE` átnevezve `MedicationRoute` .
    * A relációs entitás `ROUTE_OR_MODE_OF_MEDICATION` át lett nevezve a következőre: `RouteOfMedication` .

A következő entitások lettek hozzáadva:

* NER
    * `AdministrativeEvent`
    * `CareEnvironment`
    * `HealthcareProfession`
    * `MedicationForm` 

* Kapcsolatok kibontása
    * `DirectionOfCondition`
    * `DirectionOfExamination`
    * `DirectionOfTreatment`

> [!div class="nextstepaction"]
> [További információ a Text Analytics az állapotfigyelő tárolóról](how-tos/text-analytics-for-health.md)

## <a name="july-2020"></a>2020. július 

### <a name="text-analytics-for-health-container---public-gated-preview"></a>Text Analytics az állapotfigyelő szolgáltatáshoz – nyilvánosan kezdeményezett előzetes verzió

A Text Analytics for Health Container mostantól nyilvános, megnyitható előzetes verzióban érhető el, amely lehetővé teszi az adatok kinyerését a nem strukturált angol nyelvű szövegből olyan klinikai dokumentumokban, mint például a betegek bevitele, az orvos megjegyzései, a kutatási dokumentumok és a mentesítési összefoglalók. Jelenleg nem számítunk fel díjat a Text Analytics állapotának az állapotfigyelő tároló használata esetén.

A tároló a következő funkciókat kínálja:

* Megnevezett entitások felismerése
* Kapcsolatok kibontása
* Entitás összekapcsolása
* Negáció

## <a name="may-2020"></a>2020. május

### <a name="text-analytics-api-v3-general-availability"></a>A Text Analytics API v3 általánosan elérhető

A Text Analysis API v3 mostantól általánosan elérhető a következő frissítésekkel:

* Modell verziója `2020-04-01`
* Új [adatkorlátok](concepts/data-limits.md) az egyes szolgáltatásokhoz
* Frissítve a [Hangulatelemzés (SA) v3](how-tos/text-analytics-how-to-sentiment-analysis.md) [nyelvi támogatása](language-support.md)
* Különálló végpont az entitás összekapcsolásához 
* Új "címe" entitásának kategóriája [elnevezett entitás-felismerési () v3](how-tos/text-analytics-how-to-entity-linking.md).
* Új alkategóriák:
   * Földrajzi hely
   * Hely – szerkezeti
   * Szervezet – Értéktőzsde
   * Szervezet – orvosi
   * Szervezet – sportok
   * Esemény – kulturális
   * Esemény – természetes
   * Esemény – sport

A JSON-válaszban a következő tulajdonságok lettek hozzáadva:
   * `SentenceText` Hangulatelemzés
   * `Warnings` minden dokumentumhoz 

A JSON-válaszban a következő tulajdonságok nevei módosultak, ahol lehetséges:

* A `score` új nevet kapott: `confidenceScore`
    * `confidenceScore` két tizedesjegy pontossággal rendelkezik. 
* A `type` új nevet kapott: `category`
* A `subtype` új nevet kapott: `subcategory`

> [!div class="nextstepaction"]
> [További információ a Text Analytics API v3-ról](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)

### <a name="text-analytics-api-v31-public-preview"></a>Text Analytics API v 3.1 nyilvános előzetes verzió
   * Új Hangulatelemzés funkció – [vélemény bányászata](how-tos/text-analytics-how-to-sentiment-analysis.md#opinion-mining)
   * Új személyes ( `PII` ) tartományi szűrő a védett állapotra vonatkozó információkhoz ( `PHI` ).

> [!div class="nextstepaction"]
> [További információ a Text Analytics API v 3.1 előzetes verziójáról](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/Languages)

## <a name="february-2020"></a>2020. február

### <a name="sdk-support-for-text-analytics-api-v3-public-preview"></a>SDK-támogatás a Text Analytics API v3 nyilvános előzetes verziójához

Az [egységes Azure SDK kiadás](https://techcommunity.microsoft.com/t5/azure-sdk/january-2020-unified-azure-sdk-release/ba-p/1097290)részeként a Text Analytics API v3 SDK mostantól elérhető nyilvános előzetes verzióként a következő programozási nyelvekhez:
   * [C#](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-csharp&tabs=version-3)
   * [Python](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-python&tabs=version-3)
   * [JavaScript (Node.js)](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-javascript&tabs=version-3)
   * [Java](./quickstarts/client-libraries-rest-api.md?pivots=programming-language-java&tabs=version-3)
   
> [!div class="nextstepaction"]
> [További információ a Text Analytics API v3 SDK-ról](./quickstarts/client-libraries-rest-api.md?tabs=version-3)

### <a name="named-entity-recognition-v3-public-preview"></a>Elnevezett entitás-felismerés v3 nyilvános előzetes verziója

További entitás-típusok mostantól elérhetők az elnevezett entitás-felismerési (megnevezett) v3 nyilvános előzetes verzióban, mivel kiterjesztjük a szövegben található általános és személyes információk entitások észlelését. Ez a frissítés bevezeti a [modell verzióját](concepts/model-versioning.md) `2020-02-01` , amely a következőket tartalmazza:

* A következő általános entitás-típusok felismerése (csak angol nyelven):
    * PersonType
    * Termék
    * Esemény
    * Geopolitikai entitás (GPE) a hely alatt altípusként
    * Ügyességi

* A személyes adatok következő típusú entitás-típusainak felismerése (csak angol nyelven):
    * Személy
    * Szervezet
    * A mennyiség alatti altípusok kora
    * Dátum a DateTime alatt altípusként
    * E-mail 
    * Telefonszám (csak US)
    * URL-cím
    * IP-cím

### <a name="october-2019"></a>2019. október

#### <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

* Egy [új végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionPii) a személyes információk entitás-típusai felismeréséhez (csak angol nyelven)

* Különálló végpontok az [entitások felismeréséhez](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesRecognitionGeneral) és az [entitások összekapcsolásához](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/EntitiesLinking).

* [Modell verziója](concepts/model-versioning.md) `2019-10-01` , amely a következőket tartalmazza:
    * A szövegben talált entitások kibővített észlelése és kategorizálása. 
    * A következő új entitások típusának felismerése:
        * Telefonszám
        * IP-cím

Az entitások csatolása az angol és a spanyol nyelvet támogatja. A nyelv támogatása az entitás típusától függően változik.

#### <a name="sentiment-analysis-v3-public-preview"></a>Hangulatelemzés v3 nyilvános előzetes verzió

* [Új végpont](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-Preview-2/operations/Sentiment) az érzelmek elemzéséhez.
* [Modell verziója](concepts/model-versioning.md) `2019-10-01` , amely a következőket tartalmazza:

    * Az API szövegének és pontozásának pontosságának és részletességének jelentős javulása.
    * Automatikus címkézés a különböző érzelemekhez a szövegben.
    * A dokumentumok és a mondatok szintjének elemzése és kimenete. 

Támogatja az angol ( `en` ), a japán (), a `ja` kínai egyszerűsített ( `zh-Hans` ), a kínai hagyományos (), a `zh-Hant` francia (), az `fr` olasz () `it` , a spanyol (), a `es` holland () `nl` , a portugál ( `pt` ) és a német () nyelveket `de` , és a következő régiókban érhető el: `Australia East` `Central Canada` `Central US` `East Asia` `East US` `East US 2` `North Europe` `Southeast Asia` `South Central US` `UK South` `West Europe` `West US 2` ,,,,,,,,,, és. 

> [!div class="nextstepaction"]
> [További információ a Hangulatelemzés v3-ról](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## <a name="next-steps"></a>Következő lépések

* [Mi a Text Analytics API?](overview.md)  
* [Példa felhasználói forgatókönyvek](text-analytics-user-scenarios.md)
* [Hangulatelemzés](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Nyelvfelismerés](how-tos/text-analytics-how-to-language-detection.md)
* [Entitások felismerése](how-tos/text-analytics-how-to-entity-linking.md)
* [Fő kifejezés kibontása](how-tos/text-analytics-how-to-keyword-extraction.md)
