---
title: A Text Analytics API v2-es verziójának áttelepítési útmutatója
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan helyezheti át alkalmazásait a Text Analytics API 3. verziójának használatára.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 416ef4ceddbb43e9f1606d44a66ffd5295cee4e6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699895"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Migrálás a Text Analytics API 3. x verziójára

Ha a Text Analytics API 2,1-es verzióját használja, ez a cikk segítséget nyújt az alkalmazás frissítéséhez a 3. x verzió használatára. Az 3,0-es verzió általánosan elérhető, és olyan új funkciókat vezet be, mint az [elnevezett entitás-felismerés](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) és a [modell verziószámozása](concepts/model-versioning.md). A v 3.1 (v 3.1-preview. x) előzetes verziója is elérhető, amely olyan funkciókat is tartalmaz, mint a [vélemény bányászata](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). A v2-ben használt modellek nem kapják meg a jövőbeli frissítéseket. 

## <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Szolgáltatások módosításai 

Az 2,1-es verzióban található Hangulatelemzés az API-nak elküldett minden dokumentum esetében 0 és 1 értékkel tér vissza. A 3. verzió Ehelyett visszaadja az érzelmek címkéit (például "pozitív" vagy "negatív") mind a mondatok, mind a dokumentum egészének, valamint a hozzájuk kapcsolódó megbízhatósági pontszámoknak. 

### <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

#### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a v3-végpontra az érzelmek elemzéséhez. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Emellett frissítenie kell az alkalmazást az [API válaszában](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results)visszaadott hangulati címkék használatára. 

A JSON-válaszra vonatkozó példák a dokumentációban találhatók.
* [2,1-es verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [3,0-es verzió](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [3,1-es verzió – előzetes verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[Az entitások összekapcsolása](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Szolgáltatások módosításai

Az 2,1-es verzióban a Text Analytics API egy végpontot használ az elnevezett entitások felismeréséhez és az entitások összekapcsolásához. A 3. verzió kiterjesztett névvel ellátott entitások észlelését teszi lehetővé, és külön végpontokat használ a meghívásos és az entitások összekapcsolására A 3.1-es verziótól kezdődően (előzetes verzió: 1) a személyes `pii` és az egészségügyi információk is észlelhetők `phi` . 

### <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

#### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a meghívásos és/vagy az entitás összekapcsolására szolgáló v3-végpontokra.

Entitáskapcsolás
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Emellett frissítenie kell az alkalmazást az [API-válaszban](how-tos/text-analytics-how-to-entity-linking.md#view-results)visszaadott [entitás-kategóriák](named-entity-types.md) használatára.

A JSON-válaszra vonatkozó példák a dokumentációban találhatók.
* [2,1-es verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [3,0-es verzió](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [3,1-es verzió – előzetes verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>2,1-es verziójú entitások kategóriái

A következő táblázat felsorolja a (z) v 2.1-ben visszaadott entitási kategóriákat.

| Kategória   | Leírás                          |
|------------|--------------------------------------|
| Személy   |   Személyek nevei.  |
|Hely    | Természetes és emberi által készített tereptárgyak, struktúrák, földrajzi funkciók és geopolitikai entitások |
|Szervezet | Vállalatok, politikai csoportok, zenei zenekarok, sport klubok, kormányzati szervek és nyilvános szervezetek. A nemzetiségek és a vallások nem szerepelnek ebben az entitás-típusban. |
| PhoneNumber | Telefonszámok (csak az USA-beli és az EU-telefonszámok). |
| E-mail | E-mail-címek. |
| URL-cím | Webhelyek URL-címei. |
| IP | Hálózati IP-címek. |
| DateTime | Dátum és napszakok.| 
| Date | Naptári dátumok. |
| Idő | Napszakok |
| Dátumtartomány | Dátumtartomány. |
| Időtartomány | Időtartományok. |
| Időtartam | Időtartamok. |
| Beállítás | Beállítás, ismétlődő időpontok. |
| Mennyiség | Számok és numerikus mennyiségek. |
| Szám | Számok. |
| Százalék | Százalékos.|
| Sorszám | Sorszámok száma |
| Életkor | Alábbi korhatárt szabja. |
| Pénznem | Valuták. |
| Dimenzió | Méretek és mérések. |
| Hőmérséklet | Hőmérsékletek. |

## <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

### <a name="feature-changes"></a>Szolgáltatások módosításai 

A nyelvi észlelési funkció kimenete a v3-as verzióban megváltozott. A JSON-válasz a `ConfidenceScore` helyett fog szerepelni `score` . A v3 is csak egy nyelvet ad vissza egy  `detectedLanguage` attribútumban az egyes dokumentumokhoz.

### <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

#### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a v3 végpontra a nyelvfelismerés érdekében. Például: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Emellett frissítenie kell az alkalmazást, hogy az az `ConfidenceScore` `score` [API válasza](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results)helyett használni fogja. 

A JSON-válaszra vonatkozó példák a dokumentációban találhatók.
* [2,1-es verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [3,0-es verzió](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [3,1-es verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Fő kifejezés kibontása](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Szolgáltatások módosításai 

A Key kifejezés kibontása szolgáltatás nem módosult a v3 verzión kívül.

### <a name="steps-to-migrate"></a>Az áttelepíteni kívánt lépések

#### <a name="rest-api"></a>REST API

Ha az alkalmazás a REST API használja, frissítse a kérelem végpontját a v3 végpontra a fő kifejezés kinyeréséhez. Például: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

A JSON-válaszra vonatkozó példák a dokumentációban találhatók.
* [2,1-es verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [3,0-es verzió](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [3,1-es verzió](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Ügyfélkódtárak

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Lásd még

* [Mi a Text Analytics API](overview.md)
* [Nyelvi támogatás](language-support.md)
* [Modell verziószámozása](concepts/model-versioning.md)
