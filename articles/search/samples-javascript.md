---
title: JavaScript-minták
titleSuffix: Azure Cognitive Search
description: Keresse meg az Azure Cognitive Search bemutató JavaScript-kód mintáit, amelyek a JavaScripthez készült Azure .NET SDK-t használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: 85a4d6390087100d8d9521f6ac20dbace3a711eb
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955941"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>JavaScript-kód minták az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search-megoldás funkcióit és munkafolyamatait bemutató JavaScript-kód mintáit. Ezek a minták az Azure [**Cognitive Search ügyféloldali kódtárat**](/javascript/api/overview/azure/search-documents-readme) használják a [**Javascripthez készült Azure SDK**](/azure/developer/javascript/)-hoz, amelyet a következő hivatkozások segítségével vizsgálhat meg.

| Cél | Hivatkozás |
|--------|------|
| Csomag letöltése | [www.npmjs.com/package/@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) |
| API-referencia | [@azure/search-documents](/javascript/api/@azure/search-documents/)  |
| API-tesztelési esetek | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test) |
| Forráskód | [github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents)  |

## <a name="sdk-samples"></a>SDK-minták

Az Azure SDK Fejlesztői csapatának az API használatának bemutatása. Ezeket a mintákat az [**Azure-SDK-for-js/Tree/Master/SDK/Search/Search-Documents/Samples**](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples) on githubon találja meg.

### <a name="javascript-sdk-samples"></a>JavaScript SDK-minták

| Példák | Leírás |
|---------|-------------|
| [indexek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | Bemutatja a [keresési indexek](search-what-is-an-index.md)létrehozását, frissítését, beolvasását, listázását és törlését. Ez a minta egy szolgáltatási statisztikai mintát is tartalmaz. |
| [dataSourceConnections (indexek esetében)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő alapú indexeléséhez szükségesek. |
| [indexelők](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  Bemutatja, hogyan lehet [Indexelő](search-indexer-overview.md)létrehozni, frissíteni, beolvasni, listázni, visszaállítani és törölni.|
| [Készségkészlet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet olyan [szakértelmével](cognitive-search-working-with-skillsets.md) , amelyek indexelő, valamint az indexelés során mesterségesen alapuló dúsítást végeznek. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | Bemutatja a [szinonimák leképezésének](search-synonyms.md)létrehozását, frissítését, beolvasását, listázását és törlését.  |

### <a name="typescript-samples"></a>Írógéppel minták

| Példák | Leírás |
|---------|-------------|
| [indexek](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | Bemutatja a [keresési indexek](search-what-is-an-index.md)létrehozását, frissítését, beolvasását, listázását és törlését. Ez a minta egy szolgáltatási statisztikai mintát is tartalmaz. |
| [dataSourceConnections (indexek esetében)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő alapú indexeléséhez szükségesek. |
| [indexelők](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  Bemutatja, hogyan lehet [Indexelő](search-indexer-overview.md)létrehozni, frissíteni, beolvasni, listázni, visszaállítani és törölni.|
| [Készségkészlet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   Bemutatja, hogyan hozhat létre, frissíthet, kérhet le, listázhat és törölhet olyan [szakértelmével](cognitive-search-working-with-skillsets.md) , amelyek indexelő, valamint az indexelés során mesterségesen alapuló dúsítást végeznek. |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | Bemutatja a [szinonimák leképezésének](search-synonyms.md)létrehozását, frissítését, beolvasását, listázását és törlését.  |

## <a name="doc-samples"></a>Doc-minták

A Cognitive Search csapata által bemutatott funkciók és munkafolyamatok. Ezen minták közül sokat az oktatóanyagok, a rövid útmutatók és a útmutató cikkek tartalmaznak. Ezeket a mintákat az [**Azure-Samples/Azure-Search-JavaScript-Samples**](https://github.com/Azure-Samples/azure-search-javascript-samples) on GitHub webhelyen találja.

| Példák | Cikk |
|---------|---------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet a JavaScriptben](search-get-started-javascript.md). Ez a cikk a keresési indexek mintaadatok használatával történő létrehozásához, betöltéséhez és lekérdezéséhez szükséges alapszintű munkafolyamatot ismerteti. |

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=javascript&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="other-samples"></a>Egyéb minták

A Cognitive Search csapata a következő mintákat is közzéteszi, de a dokumentációban nem hivatkozik rá. A kapcsolódó readme-fájlok használati utasításokat tartalmaznak.

| Példák | Leírás |
|---------|-------------|
| [Azure-Search – reagálás – sablon](https://github.com/dereklegenzoff/azure-search-react-template) | Sablon megválaszolása az Azure Cognitive Search (github.com) |