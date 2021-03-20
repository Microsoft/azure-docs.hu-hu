---
title: Java-példák
titleSuffix: Azure Cognitive Search
description: A Java-hoz készült Azure .NET SDK-t használó Azure Cognitive Search bemutató Java-kód mintáinak megkeresése.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: b5ae38a3dc4a9324a4141314106d67c96c06c8e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955037"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Java-kódok az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search-megoldás funkcióit és munkafolyamatait bemutató Java-kódok mintáit. Ezek a minták az Azure [**SDK for Javához**](/azure/developer/java/sdk)készült Azure [**Cognitive Search ügyféloldali kódtárat**](/java/api/overview/azure/search-documents-readme) használják, amelyet a következő hivatkozások segítségével vizsgálhat meg.

| Cél | Hivatkozás |
|--------|------|
| Csomag letöltése | [search.maven.org/artifact/com.azure/azure-search-documents](https://search.maven.org/artifact/com.azure/azure-search-documents) |
| API-referencia | [com.azure.search.documents](/java/api/com.azure.search.documents)  |
| API-tesztelési esetek | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src/test) |
| Forráskód | [github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src](https://github.com/Azure/azure-sdk-for-java/tree/azure-search-documents_11.1.3/sdk/search/azure-search-documents/src)  |

## <a name="sdk-samples"></a>SDK-minták

Az Azure SDK Fejlesztői csapatának az API használatának bemutatása. Ezek a minták az [**Azure-ban/Azure-SDK-Java/Tree/Master/SDK/Search/Azure-Search-Documents/src/Samples**](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) on githubon találhatók meg.

| Példák | Description |
|---------|-------------|
| [Keresési index létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | Bemutatja, hogyan hozhat létre [keresési indexeket](search-what-is-an-index.md). |
| [Szinonimák létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | Bemutatja, hogyan hozhatók létre [szinonima térképek](search-synonyms.md).  |
| [Indexer-létrehozás keresése](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | Bemutatja, hogyan hozhat létre [Indexelő](search-indexer-overview.md). |
| [Keresési indexelő adatforrás létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | Bemutatja, hogyan hozhat létre indexelő adatforrásokat, amelyek a [támogatott Azure-adatforrások](search-indexer-overview.md#supported-data-sources)indexelő-alapú indexeléséhez szükségesek. |
| [Készségkészlet létrehozása](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  Azt mutatja be, hogyan hozhatók létre indexelő [szakértelmével](cognitive-search-working-with-skillsets.md) , és melyek az indexelés során mesterséges intelligencia-alapú dúsítást végeznek. |
| [Dokumentumok betöltése](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | Bemutatja, hogyan lehet feltölteni vagy egyesíteni a dokumentumokat egy indexbe egy [adatimportálási](search-what-is-data-import.md) műveletben. |
| [Lekérdezés szintaxisa](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | Bemutatja, hogyan állíthat be egy [alapszintű lekérdezést](search-query-overview.md). |

## <a name="doc-samples"></a>Doc-minták

A Cognitive Search csapata által bemutatott funkciók és munkafolyamatok. Ezen minták közül sokat az oktatóanyagok, a rövid útmutatók és a útmutató cikkek tartalmaznak. Ezeket a mintákat az [**Azure-Samples/Azure-Search-Java-Samples**](https://github.com/Azure-Samples/azure-search-java-samples) on GitHub webhelyen találja.

| Példák | Cikk | 
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/quickstart) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet a javában és a REST-ben](search-get-started-java.md). Ez a minta nem frissült a Java SDK-val. Meghívja a REST API-kat. |

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=java&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="other-samples"></a>Egyéb minták

A Cognitive Search csapata a következő mintákat is közzéteszi, de a dokumentációban nem hivatkozik rá. A kapcsolódó readme-fájlok használati utasításokat tartalmaznak.

| Példák | Description |
|---------|-------------|
| [Keresés – Java – első lépések](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-getting-started) | A Java SDK ügyféloldali kódtárat használja a keresési indexek létrehozásához, betöltéséhez és lekérdezéséhez. Ez a minta jelenleg önálló. |
| [Keresés – Java-indexelő – bemutató](https://github.com/Azure-Samples/azure-search-java-samples/tree/java-rest-api/search-java-indexer-demo) | Egy Azure Cosmos DB indexelő használatát mutatja be javában. Ez a minta nem frissült a Java SDK-val. Meghívja a REST API-kat.|