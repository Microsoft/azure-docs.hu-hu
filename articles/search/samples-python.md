---
title: Python-példák
titleSuffix: Azure Cognitive Search
description: Keresse meg az Azure Cognitive Search bemutató Python-kódrészleteket, amelyek a Pythonhoz vagy a REST-hez készült Azure .NET SDK-t használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 0d09851cf8e68cead4a67615aaa792512482f351
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955122"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Python-kód minták az Azure Cognitive Search

Ismerje meg a Python-kódrészleteket, amelyek bemutatják az Azure Cognitive Search-megoldás funkcióit és munkafolyamatait. Ezek a minták az Azure [**Cognitive Search ügyféloldali kódtárat**](/python/api/overview/azure/search-documents-readme) használják a [**Pythonhoz készült Azure SDK**](/azure/developer/python/)-hoz, amely az alábbi hivatkozásokon keresztül is felderíthető.

| Cél | Hivatkozás |
|--------|------|
| Csomag letöltése | [pypi.org/project/azure-search-documents/](https://pypi.org/project/azure-search-documents/) |
| API-referencia | [Azure-keresés – dokumentumok](/python/api/azure-search-documents)  |
| API-tesztelési esetek | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests) |
| Forráskód | [github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents)  |

## <a name="sdk-samples"></a>SDK-minták

Az Azure SDK Fejlesztői csapatának az API használatának bemutatása. Ezeket a mintákat az [**Azure-SDK-for-Python/Tree/Master/SDK/Search/Azure-Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) on githubon találja meg.

| Példák | Leírás |
|---------|-------------|
| [Hitelesítés](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | Bemutatja, hogyan konfigurálhat egy ügyfelet, és hogyan végezhet hitelesítést a szolgáltatásban. | 
| [Index Create-Read-Update-DELETE művelet](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | Bemutatja a [keresési indexek](search-what-is-an-index.md)létrehozását, frissítését, beolvasását, listázását és törlését. |
| [Indexelő létrehozása – olvasás-frissítés – törlési műveletek](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | Bemutatja, hogyan lehet [Indexelő](search-indexer-overview.md)létrehozni, frissíteni, beolvasni, listázni, visszaállítani és törölni. |
| [Indexelő adatforrások keresése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő alapú indexeléséhez szükségesek. |
| [Szinonimák](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | Bemutatja a [szinonimák leképezésének](search-synonyms.md)létrehozását, frissítését, beolvasását, listázását és törlését.  |
| [Dokumentumok betöltése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | Bemutatja, hogyan lehet feltölteni vagy egyesíteni a dokumentumokat egy indexbe egy [adatimportálási](search-what-is-data-import.md) műveletben. |
| [Egyszerű lekérdezés](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | Bemutatja, hogyan állíthat be egy [alapszintű lekérdezést](search-query-overview.md). |
| [Lekérdezés szűrése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | Egy [szűrő kifejezés](search-filters.md)beállítását mutatja be. |
| [Dimenzió lekérdezése](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | Bemutatja az [aspektusokkal](search-filters-facets.md)való munkát. |

## <a name="doc-samples"></a>Doc-minták

A Cognitive Search csapata által bemutatott funkciók és munkafolyamatok. Ezen minták közül sokat az oktatóanyagok, a rövid útmutatók és a útmutató cikkek tartalmaznak. Ezeket a mintákat az [**Azure-Samples/Azure-Search-Python-Samples**](https://github.com/Azure-Samples/azure-search-python-samples) webhelyen találja a githubon.

| Példák | Cikk |
|---------|---------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | Forráskód a gyors üzembe helyezéshez [: keresési index létrehozása a Pythonban](search-get-started-python.md). Ez a cikk a keresési indexek mintaadatok használatával történő létrehozásához, betöltéséhez és lekérdezéséhez szükséges alapszintű munkafolyamatot ismerteti. |
| [oktatóanyag – AI-gazdagodás](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | [Oktatóanyag: a Python és a AI használatával kereshető tartalmak hozhatók elő az Azure-blobokból](cognitive-search-tutorial-blob-python.md). Ez a cikk bemutatja, hogyan hozhat létre egy blob-indexelő egy kognitív készségkészlet, ahol a készségkészlet nyers tartalmat hoz létre és alakít át, hogy kereshető vagy felhasználható legyen. |
| [AzureML – egyéni – szakértelem](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | Forráskód [például: hozzon létre egy egyéni képességet a Python használatával](cognitive-search-custom-skill-python.md). Ez a cikk az indexelő és a készségkészlet integrációját mutatja be a Azure Machine Learning mély tanulási modelljeivel. |

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=python&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.