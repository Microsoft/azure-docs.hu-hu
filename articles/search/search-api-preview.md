---
title: Előzetes verziójú funkciók listája
titleSuffix: Azure Cognitive Search
description: Az előzetes verziójú funkciók kiadása lehetővé teszi, hogy az ügyfelek visszajelzést tudjanak adni a kialakításról és a segédprogramról. Ez a cikk a jelenleg előzetes verzióban elérhető összes funkció átfogó listáját tartalmazza.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: bf2ff2fc20d3581a297881fbd9e6db15d825814d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518997"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Az Azure előzetes verziójának szolgáltatásai Cognitive Search

Ez a cikk a nyilvános előzetes verzióban elérhető összes funkció átfogó listáját tartalmazza. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az általánosan elérhetővé vált előzetes verziójú funkciók törlődnek a listából. Ha egy szolgáltatás nem szerepel az alábbi listában, akkor feltételezheti, hogy általánosan elérhető. Az általános elérhetőséggel kapcsolatos közlemények: [szolgáltatások frissítései](https://azure.microsoft.com/updates/?product=search) [vagy Újdonságok](whats-new.md).

|Vonás&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Kategória | Leírás | Rendelkezésre állás  |
|---------|------------------|-------------|---------------|
| [**Azure Machine Learning (pénzmosás) ismerete**](cognitive-search-aml-skill.md) | MI-bővítés| Egy új, az Azure Machine Learning-ból származó következtetési végpont integrálására szolgáló képzettségi típus. Ismerkedjen meg az [oktatóanyaggal](cognitive-search-tutorial-aml-custom-skill.md). | Használja a [Search REST API 2020-06-30 – Preview](/rest/api/searchservice/) vagy 2019-05-06-Preview lehetőséget. A portálon is elérhető a készségkészlet-kialakításban, feltéve, hogy a Cognitive Search és az Azure ML-szolgáltatások ugyanabban az előfizetésben vannak telepítve. |
| [**featuresMode paraméter**](/rest/api/searchservice/preview-api/search-documents#query-parameters) | Relevancia (pontozás) | A relevancia pontszámának kiterjesztése a részletekre: a mező szerinti hasonlóság pontszáma, a mezők kifejezésének gyakorisága, valamint az egyedi tokenek számának egyeztetése. Ezeket az adatpontokat [Egyéni pontozási megoldásokban](https://github.com/Azure-Samples/search-ranking-tutorial)használhatja fel. | Adja hozzá ezt a lekérdezési paramétert a [Search Documents (REST)](/rest/api/searchservice/preview-api/search-documents) használatával az API-Version = 2020-06 -30-Preview vagy a 2019-05-06-Preview értékkel. |
| [**Hibakeresési munkamenetek**](cognitive-search-debug-session.md) | Portál, AI-dúsítás (készségkészlet) | Egy munkameneten belüli készségkészlet-szerkesztő, amely a készségkészlet kapcsolatos problémák vizsgálatára és megoldására szolgál. A hibakeresési munkamenet során alkalmazott javítások menthetők a szolgáltatás egyik készségkészlet is. | Egy hibakeresési munkamenet megnyitásához csak a portálon, az áttekintő lapon található, a lap közepére mutató hivatkozásokat használva. |
| [**Natív blob – Soft delete**](search-howto-index-changed-deleted-blobs.md) | Indexelő, Azure-Blobok| Az Azure Blob Storage indexelő az Azure Cognitive Search felismeri a törölt állapotú blobokat, és eltávolítja a megfelelő keresési dokumentumot az indexelés során. | Adja hozzá ezt a konfigurációs beállítást a [create indexelő (REST)](/rest/api/searchservice/create-indexer) API-Version = 2020-06 -30-Preview vagy API-Version = 2019-05 -06-Preview paranccsal. |
| [**Egyéni entitás keresési képességei**](cognitive-search-skill-custom-entity-lookup.md ) | AI-dúsítás (készségkészlet) | Egy kognitív képesség, amely a szavak és kifejezések egyéni, felhasználó által definiált listájáról keres szöveget. Ezzel a listával minden olyan dokumentumot felcímkéz, amely minden egyező entitással rendelkezik. A képesség emellett olyan zavaros egyezést is támogat, amely a hasonló, de nem pontos egyezések keresésére is alkalmazható. | Tekintse meg ezt az előzetes szaktudást a portál Készségkészlet szerkesztőjével, vagy [hozzon létre készségkészlet (REST)](/rest/api/searchservice/create-skillset) az API-Version = 2020-06 -30-Preview vagy API-Version = 2019-05 -06-Preview használatával. |
| [**Személyes adatok észlelésének képessége**](cognitive-search-skill-pii-detection.md) | AI-dúsítás (készségkészlet) | Az indexelés során használt kognitív képesség, amely Kinyeri a személyes adatokat egy bemeneti szövegből, és lehetővé teszi, hogy az adott szövegtől különböző módokon maszkot adjon. | Tekintse meg ezt az előzetes szaktudást a portál Készségkészlet szerkesztőjével, vagy [hozzon létre készségkészlet (REST)](/rest/api/searchservice/create-skillset) az API-Version = 2020-06 -30-Preview vagy API-Version = 2019-05 -06-Preview használatával. |
| [**Növekményes bővítés**](cognitive-search-incremental-indexing-conceptual.md) | Indexelő konfigurálása| Gyorsítótárazást ad hozzá egy alkoholtartalom-növelési folyamathoz, amely lehetővé teszi a meglévő kimenet újrafelhasználását, ha egy célként megadott módosítás, például egy készségkészlet vagy egy másik objektum frissítése nem módosítja a tartalmat. A gyorsítótárazás csak a készségkészlet által létrehozott dúsított dokumentumokra vonatkozik.| Adja hozzá ezt a konfigurációs beállítást a [create indexelő (REST)](/rest/api/searchservice/create-indexer) API-Version = 2020-06 -30-Preview vagy API-Version = 2019-05 -06-Preview paranccsal. |
| [**Cosmos DB indexelő: MongoDB API, Gremlin API, Cassandra API**](search-howto-index-cosmosdb.md) | Indexelő adatforrása | Cosmos DB az SQL API általánosan elérhető, de a MongoDB, a Gremlin és a Cassandra API előzetes verzióban érhető el. | Csak Gremlin és Cassandra esetén [regisztráljon először](https://aka.ms/azure-cognitive-search/indexer-preview) , hogy a támogatás engedélyezve legyen az előfizetéséhez a háttéren. A MongoDB-adatforrások konfigurálhatók a portálon. Ellenkező esetben az adatforrás-konfiguráció mind a három API-hoz támogatott a [create adatforrás (REST)](/rest/api/searchservice/create-data-source) és az API-Version = 2020-06 -30 – Preview vagy API-Version = 2019-05 -06-Preview használatával. |
|  [**Azure Data Lake Storage Gen2 indexelő**](search-howto-index-azure-data-lake-storage.md) | Indexelő adatforrása | Tartalom és metaadatok indexelése Data Lake Storage Gen2ból.| A [regisztrációra](https://aka.ms/azure-cognitive-search/indexer-preview) azért van szükség, hogy a támogatás engedélyezve legyen az előfizetéséhez a háttérbeli alkalmazásban. Ezt az adatforrást az adatforrások [(REST) létrehozásával](/rest/api/searchservice/create-data-source) érheti el API-Version = 2020-06 -30-Preview vagy API-Version = 2019-05 -06-Preview használatával. |
| [**moreLikeThis**](search-more-like-this.md) | Lekérdezés | Megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. | Adja hozzá ezt a lekérdezési paramétert a [Search Documents (REST)](/rest/api/searchservice/search-documents) hívásokhoz a következő API-Version = 2020-06 -30-preview, 2019-05-06-preview, 2016-09-01-Preview, vagy 2017-11-11-preview. |

## <a name="how-to-call-a-preview-rest-api"></a>Előnézet REST API meghívása

Az Azure Cognitive Search mindig előzetesen kibocsátja a kísérleti szolgáltatásokat a REST API a .NET SDK előzetes verzióival.

Az előzetes verziójú funkciók tesztelésre és kísérletezésre használhatók, és a szolgáltatás kialakításával és megvalósításával kapcsolatos visszajelzések összegyűjtésének célja. Ezért az előzetes verziójú funkciók idővel változhatnak, valószínűleg olyan módon, hogy a visszafelé való kompatibilitást is megszakítja. Ez ellentétben áll a GA verziójának szolgáltatásaival, amelyek stabilak, és nem valószínű, hogy a kis visszamenőlegesen kompatibilis javítások és fejlesztések kivételével megváltoznak. Emellett az előzetes verziójú funkciók nem mindig teszik azt elérhetővé a GA kiadásban.

Előfordulhat, hogy néhány előzetes verziójú funkció elérhető a Portálon és a .NET SDK-ban, a REST API mindig rendelkezik előzetes verziójú szolgáltatásokkal.

+ A keresési műveletek esetében [**`2020-06-30-Preview`**](/rest/api/searchservice/index-preview) az aktuális előzetes verzió.

+ A felügyeleti műveletek esetében [**`2019-10-01-Preview`**](/rest/api/searchmanagement/index-2019-10-01-preview) az aktuális előzetes verzió.

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód meghívja `api-version=2019-05-06-Preview` vagy `api-version=2016-09-01-Preview` vagy vagy `api-version=2017-11-11-Preview` , ezek a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal.

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

```HTTP
POST https://[service name].search.windows.net/indexes/hotels-idx/docs/search?api-version=2020-06-30-Preview  
  Content-Type: application/json  
  api-key: [admin key]
```

Az Azure Cognitive Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>Következő lépések

Tekintse át a keresési REST előzetes verzió API-dokumentációját. Ha problémákba ütközik, kérjen segítséget [stack overflow](https://stackoverflow.com/) vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája (előzetes verzió)](/rest/api/searchservice/index-preview)