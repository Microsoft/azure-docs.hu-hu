---
title: Beépített szöveg-és képfeldolgozás az indexelés során
titleSuffix: Azure Cognitive Search
description: Az adatok kinyerése, természetes nyelve, képfeldolgozása kognitív szaktudása szemantikai és strukturálatlan tartalmat ad egy Azure Cognitive Search-folyamat nyers tartalmához.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bde82a0ad50c2172e70337ad202b91af0a62c530
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547815"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Beépített kognitív képességek a szöveg-és képfeldolgozáshoz az indexelés során (Azure Cognitive Search)

Ebből a cikkből megismerheti az Azure Cognitive Search által biztosított kognitív képességeket, amelyeket felvehet egy készségkészlet a tartalom és a struktúra kinyeréséhez. A *kognitív képességek* egy modul vagy művelet, amely valamilyen módon átalakítja a tartalmakat. Gyakran előfordul, hogy ez egy olyan összetevő, amely adatokat vagy kikövetkeztetett struktúrát gyűjt, és így fokozza a bemeneti adatok megértését. Szinte mindig a kimenet Text-alapú. A *készségkészlet* olyan szaktudás gyűjteménye, amely meghatározza a dúsítási folyamatot. 

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást kell csatolnia](cognitive-search-attach-cognitive-services.md). Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedésének részeként az Azure Cognitive Searchban. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását az [Azure Cognitive Search díjszabási oldalán](https://azure.microsoft.com/pricing/details/search/)találja.
>
> A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) funkció lehetővé teszi egy olyan gyorsítótár megadását, amely lehetővé teszi, hogy az indexelő hatékonyabban fusson, és csak azokat a kognitív képességeket futtassa, amelyekre szükség van, ha később módosítja a készségkészlet, így időt és pénzt takarít meg.


## <a name="built-in-skills"></a>Beépített képességek

Az általuk felhasznált vagy előállított képességek számos képességgel rendelkeznek. Általánosságban elmondható, hogy a legtöbb ismeret az előre betanított modelleken alapul, ami azt jelenti, hogy a modellt nem lehet a saját betanítási adataival betanítani. A következő táblázat felsorolja és leírja a Microsoft által biztosított képességeket. 

| Ügyességi | Leírás |
|-------|-------------|
|[Microsoft. Skills. Text. CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Szöveget keres a szavak és kifejezések egyéni, felhasználó által meghatározott listájából.|
| [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md) | Ez a képesség egy előképzett modellt használ a fontos kifejezések elhelyezésére, a nyelvi szabályokra, a más feltételekhez való közelségre, valamint arra, hogy a kifejezés milyen szokatlanul legyen a forrásadatok között. |
| [Microsoft. Skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Ez a képesség egy előképzésen alapuló modellt használ a használt nyelv észleléséhez (dokumentum-azonosító egy nyelven). Ha az azonos szöveges szegmensekben több nyelv is használatos, a kimenet a túlnyomórészt használt nyelv LCID-je.|
| [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md) | Egy mezőkből álló gyűjteményből származó szöveget egyesít egyetlen mezőbe.  |
| [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Ez a képesség egy előre betanított modellt használ a rögzített kategóriákhoz tartozó entitások létrehozásához: személyek, hely, szervezet, e-mailek, URL-címek, datetime-mezők. |
| [Microsoft. Skills. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Ez a képesség egy előképzett modellt használ a személyes adatok egy adott szövegből való kinyeréséhez. A képesség számos különböző lehetőséget is kínál az észlelt személyes információ entitások szövegben való maszkolására.  |
| [Microsoft. Skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)  | Ez a képesség egy előre betanított modellt használ a rekordok pozitív vagy negatív ismeretének megértékelésére a rekord alapján. A pontszám 0 és 1 között van. Semleges pontszámok fordulnak elő mind a null eset esetében, ha a rendszer nem észleli a véleményét, és a semlegesnek minősülő szöveg.  |
| [Microsoft. Skills. Text. SplitSkill](cognitive-search-skill-textsplit.md) | Feldarabolja a szöveget a lapokra, így Növekményesen bővítheti vagy kibővítheti a tartalmat. |
| [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md) | Ez a képesség egy előképzett modellt használ a bemeneti szöveg különböző nyelvekre való lefordításához a normalizálás vagy a honosítás használati eseteihez. |
| [Microsoft. Skills. vízió. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Ez a képesség egy képészlelési algoritmust használ a rendszerkép tartalmának azonosításához és szöveges leírás létrehozásához. |
| [Microsoft. Skills. vízió. OcrSkill](cognitive-search-skill-ocr.md) | Optikai karakterfelismerés. |
| [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Lehetővé teszi a szűrést, az alapértelmezett érték hozzárendelését és az adategyesítést egy feltétel alapján.|
| [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) | A tartalom kibontása egy fájlból a dúsítási folyamaton belül. |
| [Microsoft. Skills. util. ShaperSkill](cognitive-search-skill-shaper.md) | Leképezi a kimenetet egy összetett típusra (egy többrészes adattípusra, amely a teljes név, a többsoros címek vagy a vezetéknév és a személyes azonosító kombinációja esetén használható) |
| [Microsoft. Skills. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md) | Lehetővé teszi egy mesterséges intelligencia-bővítési folyamat bővíthetőségét azáltal, hogy HTTP-hívást végez egy egyéni webes API-n |
| [Microsoft. Skills. Custom. AmlSkill](cognitive-search-aml-skill.md) | Lehetővé teszi az AI-bővítési folyamat bővíthetőségét Azure Machine Learning modellel |


Az [egyéni képességek](cognitive-search-custom-skill-web-api.md)létrehozásával kapcsolatos útmutatásért lásd: [Egyéni felület definiálása](cognitive-search-custom-skill-interface.md) és [példa: egyéni képesség létrehozása AI](cognitive-search-create-custom-skill-example.md)-bővítéshez.

## <a name="see-also"></a>Lásd még

+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Egyéni képességek kezelőfelületének definíciója](cognitive-search-custom-skill-interface.md)
+ [Oktatóanyag: bővített indexelés az AI-vel](cognitive-search-tutorial-blob.md)
