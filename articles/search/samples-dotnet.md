---
title: .NET-minták
titleSuffix: Azure Cognitive Search
description: Itt megtalálhatja az Azure Cognitive Search bemutató C#-kód mintáit, amelyek a .NET-ügyfél kódtárait használják.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: cbb84a4934eed4d258cf07772753315785f98019
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99218162"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>.NET (C#) kód minták az Azure Cognitive Search

Ismerje meg az Azure Cognitive Search-megoldás funkcióit és munkafolyamatait bemutató C#-kódrészleteket. Ezek a minták az Azure [**Cognitive Search ügyféloldali kódtárat**](/dotnet/api/overview/azure/search) használják a [**.net-hez készült Azure SDK**](/dotnet/azure/)-hoz, amelyet a következő hivatkozások segítségével vizsgálhat meg.

| Cél | Hivatkozás |
|--------|------|
| Csomag letöltése | [www.nuget.org/packages/Azure.Search.Documents/](https://www.nuget.org/packages/Azure.Search.Documents/) |
| API-referencia | [azure.search.documents](/dotnet/api/azure.search.documents)  |
| API-tesztelési esetek | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tesztek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) |
| Forráskód | [github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/src)  |

## <a name="sdk-samples"></a>SDK-minták

Az Azure SDK Fejlesztői csapatának az API használatának bemutatása. Ezek a minták az [**Azure/Azure-SDK-for-net/Tree/Master/SDK/Search/Azure.Search.Documents/Samples**](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/) on githubon találhatók meg.

| Példák | Leírás |
|---------|-------------|
| ["Helló világ", szinkron módon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Bemutatja, hogyan hozhat létre ügyfelet, hitelesítheti és kezelheti a hibákat szinkron metódusok használatával.|
| ["Helló világ", aszinkron módon](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Bemutatja, hogyan hozhat létre ügyfelet, hitelesítheti és kezelheti a hibákat aszinkron metódusok használatával.  |
| [Szolgáltatási szintű műveletek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Bemutatja, hogyan hozhat létre indexeket, indexelő fájlokat, adatforrásokat, szakértelmével és szinonimákat. Ez a minta azt is bemutatja, hogyan kérheti le a szolgáltatás statisztikáit, és hogyan kérdezheti le az indexeket.  |
| [Indexelési műveletek](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Bemutatja, hogyan hajtható végre művelet a meglévő indexben, ebben az esetben az indexben tárolt dokumentumok száma.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | A nem támogatott adattípusok használatának technikáját mutatja be.  |
| [Dokumentumok indexelése (leküldéses modell)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | A "leküldéses" modell indexelése, amely JSON-adattartalom küldését teszi lehetővé egy szolgáltatás indexéhez.   |
| [Titkosítási kulcs mintája](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Azt mutatja be, hogy az ügyfél által felügyelt titkosítási kulcs segítségével további védelmi réteget adhat hozzá a bizalmas tartalomhoz.  |

## <a name="doc-samples"></a>Doc-minták

A Cognitive Search csapata által bemutatott funkciók és munkafolyamatok. Ezen minták közül sokat az oktatóanyagok, a rövid útmutatók és a útmutató cikkek tartalmaznak. Ezeket a mintákat az Azure-Samples [**/Azure-Search-DotNet-Samples**](https://github.com/Azure-Samples/azure-search-dotnet-samples) és az [**Azure-Samples/Search-DotNet-Getting-Started**](https://github.com/Azure-Samples/search-dotnet-getting-started/) on GitHub webhelyen találja.

| Példák | Cikk  |
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Forráskód a gyors üzembe helyezéshez [: hozzon létre egy keresési indexet ](search-get-started-dotnet.md). Ez a cikk a keresési indexek mintaadatok használatával történő létrehozásához, betöltéséhez és lekérdezéséhez szükséges alapszintű munkafolyamatot ismerteti. |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Forráskód a [.net-ügyfél függvénytárának használatához](search-howto-dotnet-sdk.md). Ez a cikk az alapszintű munkafolyamaton halad végig, de részletesebben és az API-használatról is tárgyal.  |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Forráskód [például: szinonimák hozzáadása a C#-ban](search-synonyms-tutorial-sdk.md). A lekérdezési bővítéshez szinonimákat tartalmazó listát kell használni, amely az indexen kívüli, egyeztethető kifejezéseket biztosít. |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | [Oktatóanyag: az Azure SQL-adatkészletek indexelése a .net SDK használatával](search-indexer-tutorial.md). Ebből a cikkből megtudhatja, hogyan konfigurálhat olyan Azure SQL-indexelő, amely rendelkezik ütemezett, mező-hozzárendelésekkel és paraméterekkel.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Forráskód az [ügyfél által felügyelt kulcsok adattitkosításhoz való konfigurálásához](search-security-manage-encryption-keys.md). |
| [Az első alkalmazás létrehozása C-ben #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [Oktatóanyag: az első keresési alkalmazás létrehozása](tutorial-csharp-create-first-app.md). Habár a legtöbb minta a konzolos alkalmazások, ez az MVC-példa egy weboldalt használ a Sample Hotels-index előfizetéséhez, amely az alapszintű keresés, a tördelés, az automatikus kiegészítés és a javasolt lekérdezések, dimenziók és szűrők megjelenítésére szolgál. |
| [több adatforrások](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [Oktatóanyaghoz tartozó forráskód: több adatforrásból származó index](tutorial-multiple-data-sources.md). |
|  [optimalizálás – adatindexelés](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [Oktatóanyag: az indexelés optimalizálása a leküldéses API-val](tutorial-optimize-indexing-push-api.md).  |
| [oktatóanyag – AI-gazdagodás](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | [Oktatóanyag: AI által generált kereshető tartalom az Azure-blobokból a .net SDK használatával](cognitive-search-tutorial-blob-dotnet.md).  |

> [!Tip]
> Próbálja ki a [minták böngészőt](/samples/browse/?languages=csharp&products=azure-cognitive-search) a githubon a Microsoft Code-minták kereséséhez, termék, szolgáltatás és nyelv szerint szűrve.

## <a name="other-samples"></a>Egyéb minták

A Cognitive Search csapata a következő mintákat is közzéteszi, de a dokumentációban nem hivatkozik rá. A kapcsolódó readme-fájlok használati utasításokat tartalmaznak.

| Példák | Leírás |
|---------|-------------|
| [Azure-Search – Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Forráskód a megnyert megoldásokban felépíthető egyéni képességekhez.  |
| [Tudásbányászati megoldásgyorsító](/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | A sablonokkal, a támogatási fájlokkal és az analitikai jelentésekkel a teljes körű tudás-bányászati megoldás prototípusát segíti.  |
| [Covid-19 keresési alkalmazás tárháza](https://github.com/liamca/covid19search) | Forráskód-tárház a Cognitive Search-alapú [Covid-19 keresési alkalmazáshoz](https://covid19search.azurewebsites.net/) |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | További információ a [JFK megoldásról](https://www.microsoft.com/ai/ai-lab-jfk-files). |
| [Keresés + QnA Maker gyorssegéd](https://github.com/Azure-Samples/search-qna-maker-accelerator) | A keresés és a QnA Maker hatékonyságát ötvöző [megoldás](https://techcommunity.microsoft.com/t5/azure-ai/qna-with-azure-cognitive-search/ba-p/2081381) . Tekintse meg az élő [bemutató webhelyet](https://aka.ms/qnaWithAzureSearchDemo). |