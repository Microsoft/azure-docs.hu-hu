---
title: Az Azure Cognitive Search újdonságai
description: Új és továbbfejlesztett funkciókkal kapcsolatos bejelentések, beleértve a Azure Search az Azure Cognitive Search-ba történő átnevezését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 02/08/2021
ms.custom: references_regions
ms.openlocfilehash: 7e3f6eeab13a29eddd671411f46ac2220c10f68f
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820857"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. Könyvjelző ezen az oldalon naprakészen tarthatja a szolgáltatást. Tekintse meg az előzetes verziójú szolgáltatások [listáját](search-api-preview.md) , ahol megtekintheti a nyilvános előzetes verzióban elérhető funkciókat.

## <a name="january-2021"></a>2021. január

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  |  Description | Rendelkezésre állás  |
|------------------------------|-------------|---------------|
| [Megoldás-gyorsító az Azure Cognitive Search és QnA Maker](https://github.com/Azure-Samples/search-qna-maker-accelerator) | Kérdéseket és válaszokat kér le a dokumentumból, és a legfontosabb válaszokat ajánlja fel. Egy élő bemutató alkalmazás a következő címen érhető el: [https://aka.ms/qnaWithAzureSearchDemo](https://aka.ms/qnaWithAzureSearchDemo) .  | Nyílt forráskódú projekt (SLA nélkül) |

## <a name="2020-archive"></a>2020 Archívum

| Month (hónap) | Szolgáltatás | Leírás |
|-------|---------|-------------|
| November | [Ügyfél által felügyelt kulcs titkosítása (bővített)](search-security-manage-encryption-keys.md) | kiterjeszti az ügyfél által felügyelt titkosítást a keresési szolgáltatás által létrehozott és kezelt eszközök teljes skáláján. Általánosan elérhető.|
| Szeptember | [Felügyelt szolgáltatás identitása (indexelő)](search-howto-managed-identities-data-sources.md) | Általánosan elérhető.  |
| Szeptember | [Privát hivatkozást használó kimenő kérelmek](search-indexer-howto-access-private.md) | Általánosan elérhető.  |
| Szeptember | [Felügyeleti REST API (2020-08-01)](/rest/api/searchmanagement/management-api-versions) | Általánosan elérhető. |
| Szeptember | [Felügyeleti REST API (2020-08-01 – előzetes verzió)](/rest/api/searchmanagement/management-api-versions) | Megosztott privát kapcsolati erőforrást hoz létre Azure Functions és az Azure SQL for MySQL-adatbázisokhoz. |
| Szeptember | [Felügyelet .NET SDK 4,0](/dotnet/api/overview/azure/search/management) |  Az Azure SDK frissítése a Management SDK-hoz, az 2020-08-01-es verzió megcélozva REST API. Általánosan elérhető.|
| Augusztus | [dupla titkosítás](search-security-overview.md#encryption) | Általánosan elérhető minden olyan keresési szolgáltatáshoz, amely 2020. augusztus 1-től a következő régiókban lett létrehozva: USA 2. nyugati régiója, USA keleti régiója, USA déli középső régiója, US Gov Virginia, US Gov Arizona. |
| Július | [Azure.Search.Documents ügyféloldali kódtár](/dotnet/api/overview/azure/search.documents-readme) | A .NET-hez készült Azure SDK, általánosan elérhető. |
| Július | [azure.search.documents ügyféloldali kódtár](/python/api/overview/azure/search-documents-readme)  | A Pythonhoz készült Azure SDK, általánosan elérhető. |
| Július | [@azure/search-documents ügyféloldali kódtár](/javascript/api/overview/azure/search-documents-readme)  | Általánosan elérhető az Azure SDK a JavaScripthez. |
| Június | [Tudástár](knowledge-store-concept-intro.md) | Általánosan elérhető. |
| Június | [Keresés REST API 2020-06-30](/rest/api/searchservice/) | Általánosan elérhető. |
| Június | [Keresés REST API 2020-06-30 – előzetes verzió](/rest/api/searchservice/) | Visszaállítja az alaphelyzetbe állítási Készségkészlet a szaktudás szelektív újrafeldolgozásához és a növekményes bővítéshez. |
| Június | [Okapi BM25 relevanciás algoritmus](index-ranking-similarity.md) | Általánosan elérhető. |
| Június |  **executionEnvironment** (a keresési szolgáltatásokra vonatkozik az Azure Private link használatával.) | Általánosan elérhető. |
| Június | [PÉNZMOSÁS-képesség (előzetes verzió)](cognitive-search-aml-skill.md) | Egy olyan kognitív képesség, amely kibővíti az AI-bővítést egy egyéni Azure Machine Learning (pénzmosás) modellel. |
| Máj | [Hibakeresési munkamenetek (előzetes verzió)](cognitive-search-debug-session.md) | Készségkészlet hibakereső a portálon.  |
| Máj | [A kötött tűzfal-támogatás IP-szabályai](service-configure-firewall.md) | Általánosan elérhető.  |
| Máj | [Azure Private-hivatkozás privát keresési végponthoz](service-create-private-endpoint.md) | Általánosan elérhető.  |
| Máj | [Felügyelt szolgáltatás identitása (indexelő) – (előzetes verzió)](search-howto-managed-identities-data-sources.md) | Azure-adatforrásokhoz való kapcsolódás felügyelt identitás használatával.  |
| Máj | [munkamenet-lekérdezési paraméter](index-similarity-and-scoring.md), [scoringStatistics = globális paraméter](index-similarity-and-scoring.md#scoring-statistics)  | Globális keresési statisztikák, amelyek hasznosak a [gépi tanulási (LearnToRank) modellek kereséséhez](https://github.com/Azure-Samples/search-ranking-tutorial).  |
| Máj | [a featuresMode relevancia pontszámának bővítése (előzetes verzió)](index-similarity-and-scoring.md#featuresMode-param)  |   |
|Március  | [Natív blob – Soft Delete (előzetes verzió)](search-howto-index-changed-deleted-blobs.md) | Törli a keresési dokumentumokat, ha a forrás blobot nem törlik a blob Storage-ban. |
|Március  | [Felügyeleti REST API (2020-03-13)](/rest/api/searchmanagement/management-api-versions) | Általánosan elérhető. |
|Február | [Személyes adatok észlelésének jártassága (előzetes verzió)](cognitive-search-skill-pii-detection.md)  | Olyan kognitív képesség, amely személyes adatokat gyűjt és takar. |
|Február | [Egyéni entitás keresési képességei (előzetes verzió)](cognitive-search-skill-custom-entity-lookup.md) | Egy kognitív képesség, amely egy listából szavakat és kifejezéseket keres, és az összes olyan dokumentumot felcímkézi, amely megfelelő entitásokkal rendelkezik.  |
|January | [Ügyfél által felügyelt kulcs titkosítása](search-security-manage-encryption-keys.md) | Általánosan elérhető  |
|January | [A kötött tűzfalak támogatásának IP-szabályai (előzetes verzió)](service-configure-firewall.md) | Új **IpRule** és **NetworkRuleSet** tulajdonságok a [CreateOrUpdate API](/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service)-ban.  |
|January | [Privát végpont létrehozása (előzetes verzió)](service-create-private-endpoint.md) | Hozzon létre egy privát hivatkozást a keresési szolgáltatáshoz való biztonságos csatlakozáshoz. Ez az előzetes verziójú szolgáltatás a megoldás részeként tartalmaz egy függőségi [Azure Private-hivatkozást](../private-link/private-link-overview.md) és [Azure-Virtual Network](../virtual-network/virtual-networks-overview.md) . |

## <a name="2019-archive"></a>2019 Archívum

| Month (hónap) | Szolgáltatás | Leírás |
|-------|---------|-------------|
|December | [Bemutató alkalmazás létrehozása (előzetes verzió)](search-create-app-portal.md) | Egy olyan varázsló, amely egy lekérdezési (írásvédett) hozzáféréssel rendelkező letölthető HTML-fájlt hoz létre egy indexhez, amelynek célja ellenőrzési és tesztelési eszköz, nem pedig a teljes ügyfélalkalmazás rövid kivágása.|
|November | [Növekményes gazdagodás (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) | Gyorsítótárazza a készségkészlet feldolgozását a későbbi újrafelhasználáshoz.  |
|November | [Dokumentum-extrakciós szakértelem (előzetes verzió)](cognitive-search-skill-document-extraction.md) | Egy kognitív képesség, amellyel egy fájl tartalmát kinyerheti egy készségkészlet belülről.|
|November | [Szöveges fordítási képesség](cognitive-search-skill-text-translation.md) | Az indexelés során használt kognitív képesség, amely kiértékeli és lefordítja a szöveget. Általánosan elérhető.|
|November | [Power BI sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) | Sablon a tartalom megjelenítéséhez a Knowledge Store-ban |
|November | [Azure Data Lake Storage Gen2 (előzetes verzió)](search-howto-index-azure-data-lake-storage.md), [Cosmos db Gremlin API (előzetes verzió)](search-howto-index-cosmosdb.md)és [Cosmos db Cassandra API (előzetes verzió)](search-howto-index-cosmosdb.md) | A nyilvános előzetes verzióban elérhető új indexelő adatforrások. |
|Július | [Felhő-támogatás Azure Government](../azure-government/compare-azure-government-global-azure.md#azure-cognitive-search) | Általánosan elérhető.|

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Új szolgáltatásnév

A Azure Search az **Azure 2019 Cognitive Search** lett átnevezve, amely a kognitív képességek kibővített (de opcionális) használatát mutatja be a fő műveletekben. Az API-verziók, a NuGet-csomagok, a névterek és a végpontok változatlanok. Az új és a meglévő keresési megoldásokat nem érinti a szolgáltatás nevének változása.

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [Service Update-hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) megtalálhatók az Azure webhelyén.
