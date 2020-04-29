---
title: Importálás és adatfeldolgozás a keresési indexekben
titleSuffix: Azure Cognitive Search
description: Adatok feltöltése és feltöltése az Azure Cognitive Searchban külső adatforrásokból származó indexbe.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc3f38e9bb96ce76263a3124f8bfdc49dc638bfd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79282756"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Az adatimportálás áttekintése – Azure Cognitive Search

Az Azure Cognitive Search a lekérdezéseket a rendszer a [keresési indexbe](search-what-is-an-index.md)betöltött és mentett tartalomon hajtja végre. Ez a cikk az indexek feltöltésének két alapvető módszerét vizsgálja: az adatokat programozott módon *leküldheti* az indexbe, vagy az [Azure Cognitive Search indexelő](search-indexer-overview.md) egy támogatott adatforrásra *irányíthatja, hogy* lekérje az adatokat.

A cél az, hogy az adatok egy külső adatforrásból egy Azure Cognitive Search indexbe *töltődnek* be. Az Azure Cognitive Search lehetővé teszi, hogy üres indexet hozzon létre, de amíg nem küldi le vagy nem kéri le az adatgyűjtést, nem kérdezhető le.

## <a name="pushing-data-to-an-index"></a>Adatok elküldése egy indexbe
A legrugalmasabb megközelítés a leküldéses modell, amellyel programozott módon küldhet adatait az Azure Cognitive Searchba. Először is nem korlátozza az adatforrás típusát. A JSON-dokumentumokból álló összes adatkészlet leküldhető egy Azure Cognitive Search indexbe, feltéve, hogy az adatkészlet minden dokumentuma mezői vannak hozzárendelve az index sémában definiált mezőkhöz. Emellett nem korlátozza a végrehajtás gyakoriságát sem. Tetszőleges gyakorisággal továbbíthat módosításokat az indexeknek. Nagyon alacsony késleltetési követelményekkel rendelkező alkalmazások esetében (ha például arra van szükség, hogy a keresési műveletek szinkronizálva legyenek a dinamikus leltáradatbázissal) kizárólag a leküldéses modell használható.

Ez a megközelítés rugalmasabb a lekéréses modellnél, mivel dokumentumokat feltölthet egyedileg, illetve kötegek formájában is (kötegenként legfeljebb 1000 darabot vagy 16 MB-t, amelyik a kisebb). A leküldéses modell lehetővé teszi a dokumentumok Azure Cognitive Searchba való feltöltését, függetlenül attól, hogy az adatok hol vannak.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Az adatküldés Azure Cognitive Search indexbe

Az alábbi API-kat használhatja egy vagy több dokumentum indexbe való betöltésére:

+ [Dokumentumok hozzáadása, frissítése vagy törlése (REST API)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [indexAction osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) vagy [indexBatch osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

A portálon keresztül történő adatleküldéshez jelenleg nincsenek támogató eszközök.

Az egyes módszerek bevezetését lásd: gyors útmutató [: azure Cognitive Search index létrehozása a PowerShell](search-create-index-rest-api.md) -lel vagy a C# gyors üzembe helyezésével [: Azure Cognitive Search index létrehozása a .net SDK használatával](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Indexelési műveletek: feltöltés, Egyesítés, mergeOrUpload, törlés

Az indexelési művelet típusát a dokumentumok alapján szabályozhatja, megadhatja, hogy a dokumentumot teljes egészében fel kell-e tölteni, össze kell-e vonni a meglévő dokumentum tartalmával, vagy törölni kell azt.

A REST API a JSON-kéréseket tartalmazó HTTP POST kérelmeket az Azure Cognitive Search index végpontjának URL-címére adja ki. Az "érték" tömbben található minden JSON-objektum tartalmazza a dokumentum kulcsát, és megadja, hogy egy indexelési művelet hozzáadja-e a dokumentum tartalmát, illetve frissíti vagy törli. A kód például: [dokumentumok betöltése](search-get-started-dotnet.md#load-documents).

A .NET SDK-ban csomagolja ki az adatait egy `IndexBatch` objektumba. Az `IndexBatch` egy `IndexAction` objektumokból álló gyűjteményt ágyaz be, amely tartalmaz egy dokumentumot és egy olyan tulajdonságot, amely közli az Azure-Cognitive Search, hogy milyen műveletet hajtson végre az adott dokumentumban. A kód például a [C#](search-get-started-dotnet.md)gyors üzembe helyezési útmutatójában található.


| @search.action | Leírás | Az egyes dokumentumok kötelező mezői | Megjegyzések |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Az `upload` művelet működése hasonló az „upsert” (frissítés/beszúrás) műveletéhez, ahol a rendszer az új dokumentumot beilleszti, ha pedig már létező dokumentumról van szó, akkor frissíti/kicseréli azt. |billentyű, továbbá a meghatározni kívánt egyéb mezők |Létező dokumentum frissítése/cseréje esetén a kérésben nem megadott mezők beállítása a következő lesz: `null`. Ez történik abban az esetben is, ha a mező korábban nem null értékre lett beállítva. |
| `merge` |Egy meglévő dokumentumot frissít a megadott mezőkkel. Ha a dokumentum nem található az indexben, az egyesítés meg fog hiúsulni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |A rendszer az egyesítési művelet során megadott mezőkre cseréli a dokumentum meglévő mezőit. A .NET SDK-ban a következő típusú `DataType.Collection(DataType.String)`mezők szerepelnek:. A REST API a következő típusú `Collection(Edm.String)`mezőket tartalmazza:. Ha például a dokumentum egy `["budget"]` értékű `tags` mezőt tartalmaz, és egyesítést hajt végre a `tags` mező `["economy", "pool"]` értékével, a `tags` mező végső értéke `["economy", "pool"]` lesz. Nem pedig a következő lesz: `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Ha az indexben már létezik az adott kulccsal ellátott dokumentum, ezen művelet viselkedése hasonló lesz a `merge` műveletéhez. Ha nem létezik ilyen dokumentum, a művelet viselkedése az `upload` új dokumentum esetében mutatott viselkedésének fog megfelelni. |billentyű, továbbá a meghatározni kívánt egyéb mezők |- |
| `delete` |Eltávolítja a megadott dokumentumot az indexből. |csak billentyű |A rendszer figyelmen kívül hagyja a kulcsmezőn kívül megadott mezőket. Ha egyetlen mezőt kíván eltávolítani a dokumentumból, e helyett használja a `merge` műveletet, és a mező számára explicit módon adja meg a null értéket. |

## <a name="decide-which-indexing-action-to-use"></a>A használni kívánt indexelési művelet megadása
Adatok importálása a .NET SDK használatával (feltöltés, Egyesítés, törlés és mergeOrUpload). Attól függően, hogy az alábbi műveletek közül melyiket választja ki, az egyes dokumentumok esetében csak bizonyos mezők lesznek kötelezően megjelenítendők:


### <a name="formulate-your-query"></a>A lekérdezés meghatározása
Kétféleképpen [keresheti meg az indexet a REST API használatával](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Az egyik lehetőség egy HTTP POST kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kéréstörzs JSON-objektumában. A másik lehetőség egy HTTP GET kérés kiadása azon a helyen, ahol a lekérdezési paraméterek vannak meghatározva a kérés URL-címén belül. A lekérdezési paraméterek méretének tekintetében a POST több [enyhe korlátozással](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) rendelkezik, mint a GET. Éppen ezért a POST használatát javasoljuk, hacsak nem állnak fenn olyan speciális körülmények, amelyek a GET használatát kényelmesebbé tennék.

A POST és a GET esetében egyaránt meg kell majd adnia a *szolgáltatás nevét*, az *index nevét*, valamint a megfelelő *API-verziót* (a jelen dokumentum kiadásakor érvényes API-verzió: `2019-05-06`) a kérés URL-címében. A GET esetében a lekérdezési paramétereket az URL-cím végén található *lekérdezési sztringben* kell megadni. Az URL-cím formátuma alább látható:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

A POST esetében a formátum ugyanez, azzal a kiegészítéssel, hogy a lekérdezési sztring paraméterei között csak az API-verzió szerepel.


## <a name="pulling-data-into-an-index"></a>Adatok lekérése indexbe
A lekéréses modell feltérképezi a támogatott adatforrást, majd automatikusan feltölti az adatokat az indexbe. Az Azure Cognitive Search-ban ez a képesség az *Indexelő*használatával valósul meg, jelenleg a következő platformokon érhető el:

+ [Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Azure SQL Database és az Azure virtuális gépeken futó SQL Server](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Az indexelők indexeket csatlakoztatnak az adatforrásokhoz (általában táblákhoz, nézetekhez vagy ezekkel egyenértékű struktúrákhoz), és leképezik a forrásmezőket a megfelelő mezőkre az indexben. A végrehajtás során a sorkészlet automatikusan át lesz alakítva JSON formátumba, és be lesz töltve a meghatározott indexbe. Minden indexelő támogatja az ütemezést, így meghatározhatja, hogy az adatok milyen gyakran legyenek frissítve. A legtöbb indexelő biztosít változáskövetési funkciókat, ha az adatforrás támogatja azokat. Az új dokumentumok felismerésén kívül az indexelők a meglévő dokumentumok módosításainak és a törléseinek nyomon követésével küszöbölik ki az aktív adatkezelés szükségességét az indexben. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Az Azure Cognitive Search indexbe történő adatkérés

Az indexelő funkció az [Azure Portalon](search-import-data-portal.md), a [REST API](/rest/api/searchservice/Indexer-operations) és a [.NET SDK](/dotnet/api/microsoft.azure.search.indexersoperationsextensions) részeként van közzétéve. 

A portál használatának előnye, hogy az Azure Cognitive Search általában létrehoz egy alapértelmezett index-sémát az Ön számára a forrás-adatkészlet metaadatainak beolvasásával. A létrehozott indexet annak feldolgozásáig módosíthatja, azt követően azonban csak azok a sémamódosítások engedélyezettek, amelyekhez újraindexelés nem szükséges. Ha a végrehajtani kívánt módosítások közvetlen hatással vannak a sémára, újra kell építenie az indexet. 

## <a name="verify-data-import-with-search-explorer"></a>Az adatimportálás ellenőrzése a keresési Explorerrel

A dokumentum feltöltésének előzetes ellenőrzésének gyors módja a **Search Explorer** használata a portálon. A keresési ablak segítségével bármiféle kód írása nélkül kérdezheti le az indexeket. A keresési élmény az alapértelmezett beállításokon alapul, például az [egyszerű szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search) és az alapértelmezett [searchMode lekérdezési paraméter](/rest/api/searchservice/search-documents). A rendszer az eredményeket JSON-formátumban adja vissza, így a teljes dokumentum vizsgálható.

> [!TIP]
> Számos [Azure Cognitive Search code-minta](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) tartalmaz beágyazott vagy könnyen elérhető adatkészleteket, így könnyen megkezdheti az első lépéseket. A portál emellett egy mintaindexelőt és egy adatforrást is kínál, amely egy kisméretű ingatlan-adatkészletet tartalmaz („realestate-us-sample” néven). Amikor az előre konfigurált indexelő a minta adatforráson futtatja, a rendszer létrehoz egy indexet, és betölti azokat dokumentumokkal, amelyek ezután a keresési Explorerben vagy az Ön által írt kóddal kérhetők le.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Útmutató a Portal használatához: indexek létrehozása, betöltése és lekérdezése](search-get-started-portal.md)
