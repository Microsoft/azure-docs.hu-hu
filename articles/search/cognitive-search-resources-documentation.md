---
title: Dokumentációs hivatkozások AI-bővítéshez
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search mesterséges intelligencia-bővítési munkaterhelésekhez kapcsolódó cikkek, oktatóanyagok, minták és blogbejegyzések jegyzetekkel ellátott listája.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935347"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Az AI-bővítés dokumentációs erőforrásai az Azure-ban Cognitive Search

Az AI-bővítés egy olyan bővítmény az indexelő-alapú indexeléshez, amely nem szöveges forrásokban és nem differenciált szövegekben talál látens információkat, és átalakítja azt teljes szöveges kereshető tartalomra az Azure Cognitive Searchban. 

A beépített feldolgozáshoz a Cognitive Services előképzett AI-modelljeit belsőleg nevezzük az elemzések végrehajtásához. Az egyéni modelleket Azure Machine Learning, Azure Functions vagy más megközelítéssel is integrálhatja.

Az alábbi lista a AI-bővítés dokumentációjának összevont listáját tartalmazza.

## <a name="concepts"></a>Alapelvek

+ [AI-bővítések](cognitive-search-concept-intro.md)
+ [Készségek](cognitive-search-working-with-skillsets.md)
+ [Munkamenetek hibakeresése](cognitive-search-debug-session.md)
+ [Tudástárak](knowledge-store-concept-intro.md)
+ [Leképezések](knowledge-store-projection-overview.md)
+ [Növekményes gazdagodás (a gyorsítótárazott dúsított dokumentumok újrafelhasználása)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Gyakorlati útmutatók

+ [Gyors útmutató: kognitív készségkészlet létrehozása a Azure Portalban](cognitive-search-quickstart-blob.md)
+ [Oktatóanyag: bővített indexelés az AI-vel](cognitive-search-tutorial-blob.md)
+ [Oktatóanyag: a készségkészlet diagnosztizálása, javítása és végrehajtása hibakeresési munkamenetekkel](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Tudástárak

+ [Gyors útmutató: Knowledge Store létrehozása a Azure Portal](knowledge-store-create-portal.md)
+ [Knowledge Store létrehozása REST és Poster használatával](knowledge-store-create-rest.md)
+ [Tudásbázis megtekintése Storage Explorer](knowledge-store-view-storage-explorer.md)
+ [Tudástár összekötése Power BI](knowledge-store-connect-power-bi.md)
+ [Kivetítési példák (a dúsítások formálása és exportálása)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Egyéni képességek (speciális)

+ [Egyéni szaktudás-illesztőfelület definiálása](cognitive-search-custom-skill-interface.md)
+ [Példa: egyéni képesség létrehozása Azure Functions (és Bing Entity Search API-k) használatával](cognitive-search-create-custom-skill-example.md)
+ [Példa: egyéni szakértelem létrehozása a Python használatával](cognitive-search-custom-skill-python.md)
+ [Példa: egyéni képesség létrehozása az űrlap-felismerő használatával](cognitive-search-custom-skill-form.md) 
+ [Példa: egyéni képesség létrehozása Azure Machine Learning használatával](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Útmutató

+ [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md)
+ [Képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [jegyzetek készségkészlet való hivatkozása](cognitive-search-concept-annotations-syntax.md)
+ [Mezők leképezése indexre](cognitive-search-output-field-mapping.md)
+ [Képeken szereplő információk feldolgozása és kinyerése](cognitive-search-concept-image-scenarios.md)
+ [A növekményes dúsítás gyorsítótárazásának konfigurálása](search-howto-incremental-index.md)
+ [Azure Cognitive Search index újraépítése](search-howto-reindex.md)
+ [Tervezési tippek](cognitive-search-concept-troubleshooting.md)
+ [Gyakori hibák és figyelmeztetések](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Képességek referenciája

+ [Beépített képességek](cognitive-search-predefined-skills.md)
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. Skills. Text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. Skills. Text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. Skills. Text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. Skills. Text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft. Skills. Text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. Skills. Text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. Skills. vízió. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. Skills. vízió. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. Skills. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Egyéni készségek
  + [Microsoft. Skills. Custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft. Skills. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Elavult képességek](cognitive-search-skill-deprecated.md)
  + [Microsoft. Skills. Text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API-k

+ [REST API](/rest/api/searchservice/)
  + [Készségkészlet létrehozása (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Indexelő létrehozása (API-Version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Lásd még

+ [Az Azure Cognitive Search REST API-jai](/rest/api/searchservice/)
+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Mi az az Azure Cognitive Search?](search-what-is-azure-search.md)