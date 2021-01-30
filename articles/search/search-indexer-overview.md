---
title: Az indexelő áttekintése
titleSuffix: Azure Cognitive Search
description: A kereshető adatok kinyeréséhez és az Azure Cognitive Search indexének feltöltéséhez bejárási Azure SQL Database, SQL felügyelt példány, Azure Cosmos DB vagy Azure Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: ceeaad64734099f8669590d39f1629b9f4173e19
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097148"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexelők az Azure Cognitive Searchben

Az Azure Cognitive Search *Indexelő* egy olyan webbejáró, amely egy külső Azure-adatforrásból Kinyeri a kereshető szöveget és metaadatokat, és a keresési indexet a forrásadatok és az index közötti mező-mező leképezések használatával tölti fel. Ezt a módszert más néven "lekéréses modellnek" is nevezzük, mert a szolgáltatás olyan kódot kér le, amely nem rendelkezik olyan kóddal, amely az adott indexbe felveszi az adattípust. Az indexelő a Cognitive Search [AI](cognitive-search-concept-intro.md) -bővítési képességeit is elvezetik, és integrálják a tartalom külső feldolgozását egy indexbe.

Az indexelő csak az Azure-ban, az [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), az [Azure Cosmos db](search-howto-index-cosmosdb.md), az [Azure Table Storage](search-howto-indexing-azure-tables.md) és a [blob Storage](search-howto-indexing-azure-blob-storage.md)egyéni indexelő szolgáltatásával. Az indexelő konfigurálásakor meg kell adnia egy adatforrást (forrás), valamint egy indexet (cél). Számos forrás – például a blob Storage – rendelkezik az adott tartalomtípushoz tartozó további konfigurációs tulajdonságokkal.

Az Indexelő szolgáltatást igény szerint vagy ismétlődő adatfrissítési ütemezés szerint futtathatja, amely akár öt percenként is futtatható. A gyakoribb frissítések esetében egy ["leküldéses modell"](search-what-is-data-import.md) szükséges, amely egyidejűleg frissíti az Azure Cognitive Search és a külső adatforrás adatait is.

## <a name="usage-scenarios"></a>Használati forgatókönyvek

Az indexelő egyetlen eszközként használhatja az adatfeldolgozáshoz, vagy olyan technikák kombinációjának részeként, amelyek betöltenek és opcionálisan átalakítanak vagy gazdagítanak tartalmakat az út mentén. A következő táblázat összefoglalja a főbb forgatókönyveket.

| Eset |Stratégia |
|----------|---------|
| Egyetlen adatforrás | Ez a minta a legegyszerűbb: az egyik adatforrás a keresési index egyetlen szolgáltatója. A forrásból egy olyan mezőt fog azonosítani, amely egyedi értékeket tartalmaz, amelyek a keresési indexben a dokumentum kulcsaként szolgálnak. A rendszer az egyedi értéket fogja használni azonosítóként. Minden más forrás mező implicit módon vagy explicit módon van leképezve az index megfelelő mezőihez. </br></br>Fontos elvihetőség, hogy a dokumentum kulcsa a forrásadatokből származik. A keresési szolgáltatás nem hoz alapértékeket. A későbbi futtatások során új kulcsokkal rendelkező bejövő dokumentumok lesznek hozzáadva, míg a meglévő kulcsokkal rendelkező bejövő dokumentumok egyesítése vagy felülírása attól függően, hogy az index mezői null értékűek vagy fel vannak-e töltve. |
| Több adatforrás | Az indexek több forrásból is fogadhatnak tartalmakat, ahol minden egyes Futtatás új tartalmat hoz egy másik forrásból. </br></br>Az egyik eredmény lehet egy olyan index, amely minden indexelő futtatása után a dokumentumokat fogja megnyerni, és az összes forrásból származó teljes dokumentum teljes egészében létrejött. Az 1-100-as dokumentumok például a blob Storage-ból származnak, a 101-200-es dokumentumok pedig az Azure SQL-ből, és így tovább. Ennek a forgatókönyvnek a feladata az összes bejövő adathoz használható index séma megtervezése, valamint a keresési indexben egységes dokumentum-kulcs szerkezete. Natív módon a dokumentumok egyedi azonosítására szolgáló értékek egy blob-tárolóban és egy SQL-tábla elsődleges kulcsa metadata_storage_path. Képzelje el, hogy az egyik vagy mindkét forrást módosítani kell, hogy a kulcsfontosságú értékeket közös formátumban adja meg, függetlenül a tartalom eredetétől. Ebben az esetben várhatóan bizonyos szintű előfeldolgozást kell végeznie az adatok homogenizálása érdekében, hogy azt egyetlen indexbe lehessen húzni. </br></br>Lehetséges, hogy az első futtatáskor részlegesen kitöltött dokumentumok kereshetők, majd a későbbi futtatások további kitöltésével más forrásokból származó értékeket hoznak. Például a 1-10 mezők blob Storage-ból, 11-20 az Azure SQL-ből és így tovább. Ennek a mintának az a kihívása, hogy minden indexelési Futtatás ugyanazt a dokumentumot célozza meg. A mezők meglévő dokumentumba való egyesítéséhez meg kell egyeznie a dokumentum kulcsával. A forgatókönyv bemutatását lásd [: oktatóanyag: index több adatforrásból](tutorial-multiple-data-sources.md). |
| Több indexelő | Ha több adatforrást használ, előfordulhat, hogy több indexelő is szükséges, ha a futásidejű paramétereket, az ütemtervet vagy a mező-hozzárendeléseket kell módosítania. Bár több indexelő adatforrás-készlet is megcélozhatja ugyanazt az indexet, ügyeljen arra, hogy az indexelő fusson, amely felülírhatja a meglévő értékeket az indexben. Ha egy második indexelő-adatforrás ugyanazokat a dokumentumokat és mezőket célozza meg, akkor az első futtatásból származó értékek felül lesznek írva. A mezőértékek teljes egészében le vannak cserélve. az indexelő nem tudja egyesíteni az értékeket több futtatásból ugyanabba a mezőbe.</br></br>Egy másik többszörös indexelő használati eset a [Cognitive Search régiók közötti kiskálázása](search-performance-optimization.md#use-indexers-for-updating-content-on-multiple-services). Előfordulhat, hogy ugyanazon keresési index különböző régiókban található másolatai vannak. A keresési index tartalmának szinkronizálásához több indexelő is tartozhat ugyanabból az adatforrásból, ahol minden indexelő egy másik keresési indexet céloz meg.</br></br>A nagyon nagy adatkészletek [párhuzamos indexeléséhez](search-howto-large-index.md#parallel-indexing) több indexelő stratégia is szükséges. Minden indexelő az adathalmazt célozza meg. |
| Tartalom átalakítása | A Cognitive Search támogatja a választható [AI-gazdagító](cognitive-search-concept-intro.md) viselkedéseket, amelyek képelemzést és természetes nyelvi feldolgozást hoznak létre új kereshető tartalom és struktúra létrehozásához. Az AI-bővítés Indexer-alapú, csatolt [készségkészlet](cognitive-search-working-with-skillsets.md)keresztül történik. Az AI-bővítés végrehajtásához az indexelő továbbra is szüksége van egy indexre és egy Azure-adatforrásra, azonban ebben az esetben a készségkészlet-feldolgozást hozzáadja az indexelő végrehajtásához. |

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Támogatott adatforrások

Az indexelő adattárakat térképez fel az Azure-ban.

+ [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
+ [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (előzetes verzió)
+ [Azure-Table Storage](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](search-howto-index-cosmosdb.md)
+ [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [SQL Managed Instance](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)
+ [SQL Server az Azure Virtual Machines szolgáltatásban](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

## <a name="stages-of-indexing"></a>Az indexelés szakaszai

Ha a kezdeti futtatáskor az index üres, akkor az indexelő a táblázatban vagy a tárolóban megadott összes adattal beolvasható. A későbbi futtatások során az indexelő általában csak a módosított adatértékeket észlelheti és kérheti le. A Blobok esetében az észlelés módosítása automatikus. Más adatforrások esetében, mint például az Azure SQL vagy a Cosmos DB, engedélyezni kell az észlelés módosítását.

Minden kapott dokumentum esetében az indexelő több lépést valósít meg vagy koordinál, a dokumentum lekérésével a "handoff" végső keresőmotorba az indexeléshez. Opcionálisan az indexelő is szerepet játszik a készségkészlet-végrehajtás és-kimenetek vezetésében, feltételezve, hogy készségkészlet van definiálva.

:::image type="content" source="media/search-indexer-overview/indexer-stages.png" alt-text="Indexelő szakaszai" border="false":::

### <a name="stage-1-document-cracking"></a>1. lépés: a dokumentum repedése

A dokumentumok repedése a fájlok megnyitásának és a tartalom kinyerésének folyamata. Az adatforrás típusától függően az indexelő megpróbál különböző műveleteket végrehajtani a potenciálisan indexelhető tartalom kinyeréséhez.  

Angol nyelvű Példák:  

+ Ha a dokumentum egy [Azure SQL-adatforrás](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)egyik rekordja, az indexelő Kinyeri a rekord összes mezőjét.
+ Ha a dokumentum egy [Azure Blob Storage adatforrásban](search-howto-indexing-azure-blob-storage.md)található PDF-fájl, az indexelő Kinyeri a szöveget, a képeket és a metaadatokat.
+ Ha a dokumentum egy [Cosmos db adatforrásban](search-howto-index-cosmosdb.md)lévő rekord, az indexelő kibontja a mezőket és almezőket a Cosmos db dokumentumból.

### <a name="stage-2-field-mappings"></a>2. fázis: mező-hozzárendelések 

Az indexelő Kinyeri a szöveget a forrás mezőből, és elküldi azt egy index vagy egy Tudásbázis célhely mezőjébe. Ha a mezők nevei és típusai egybeesnek, az elérési út üres. Előfordulhat azonban, hogy más neveket vagy típusokat szeretne használni a kimenetben, ebben az esetben meg kell adnia az indexelő számára a mező leképezését. 

Ez a lépés a dokumentum repedése, de az átalakítások előtt következik be, amikor az indexelő beolvassa a forrás dokumentumait. Amikor meghatároz egy [mező-hozzárendelést](search-indexer-field-mappings.md), a forrás mező értékét a rendszer a cél mezőre, módosítás nélkül továbbítja. 

### <a name="stage-3-skillset-execution"></a>3. fázis: Készségkészlet végrehajtás

A készségkészlet-végrehajtás egy opcionális lépés, amely a beépített vagy egyéni AI-feldolgozást hívja meg. Előfordulhat, hogy az optikai karakterfelismerést (OCR) képelemzés formájában kell megadnia, ha a forrásadatok bináris képek, vagy ha a tartalom különböző nyelveken van, szükség lehet nyelvi fordításra. 

Az átalakítástól függetlenül a készségkészlet-végrehajtás is az, ahol a dúsítás történik. Ha egy indexelő egy folyamat, úgy gondolhatja, hogy egy [készségkészlet](cognitive-search-defining-skillset.md) "folyamat a folyamaton belül".

### <a name="stage-4-output-field-mappings"></a>4. fázis: kimeneti mezők leképezése

Ha készségkészlet tartalmaz, valószínűleg a kimeneti mezők leképezéseit is meg kell adnia. A készségkészlet kimenete valójában a *dúsított dokumentumnak* nevezett információk fája. A kimeneti mezők leképezése lehetővé teszi, hogy kiválassza a fa azon részeit, amelyek az index mezőibe képezhetők le. Útmutató a [kimeneti mezők leképezésének definiálásához](cognitive-search-output-field-mapping.md).

Míg a mező-hozzárendelések az adatforrásból a cél mezőkhöz társított Verbatim-értékeket, a kimeneti mezők leképezései azt mondják el, hogy az indexelő Hogyan rendeli hozzá az átalakított értékeket a dúsított dokumentumhoz az indexben lévő cél mezőkhöz. A mező-hozzárendelések eltérően, amelyek nem kötelezőnek számítanak, minden esetben meg kell adnia egy kimeneti mező leképezését minden olyan átalakított tartalomhoz, amelynek egy indexben kell lennie.

A következő képen egy példa indexelő [hibakeresési munkamenetének](cognitive-search-debug-session.md) ábrázolása látható az indexelő szakaszaiban: dokumentum repedések, mező-hozzárendelések, készségkészlet-végrehajtás és kimeneti mezők leképezése.

:::image type="content" source="media/search-indexer-overview/sample-debug-session.png" alt-text="Példa hibakeresési munkamenetre" lightbox="media/search-indexer-overview/sample-debug-session.png":::

## <a name="basic-workflow"></a>Alapvető munkafolyamat

Az indexelők az adott adatforrások esetében egyedi funkciókat biztosítanak. Ezért az indexelő- vagy az adatforrás-konfiguráció egyes szempontjai az indexelő típusától függően változnak. Az alapvető felépítés és követelmények azonban minden indexelő esetében azonosak. Az alábbiakban az összes indexelőre érvényes lépések láthatóak.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Az indexelő olyan *adatforrás* -objektumot igényelnek, amely egy kapcsolatok karakterláncot és esetleg hitelesítő adatokat biztosít. Az erőforrás létrehozásához hívja meg az [adatforrás létrehozása (REST)](/rest/api/searchservice/create-data-source) vagy a [SearchIndexerDataSourceConnection osztályt](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) .

Az adatforrások konfigurálása és kezelése az azokat használó indexelőktől függetlenül történik, ami azt jelenti, hogy egy adatforrást több indexelő is használhat egyidejűleg, egynél több index betöltésére.

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása

Az indexelők automatizálni tudják az adatfeldolgozáshoz kapcsolódó bizonyos feladatokat, de az indexek létrehozása nem tartozik ezek közé. Előfeltételként olyan előre meghatározott indexre van szükség, amelynek mezői egyeznek a külső adatforrás mezőivel. A mezőknek meg kell egyezniük a név és az adattípus alapján. Ha nem, [meghatározhatja a hozzárendeléseket](search-indexer-field-mappings.md) a társítás létrehozásához. Az indexek strukturálásával kapcsolatos további információkért lásd: [index létrehozása (REST)](/rest/api/searchservice/Create-Index) vagy [SearchIndex osztály](/dotnet/api/azure.search.documents.indexes.models.searchindex).

> [!Tip]
> Az indexelők nem tudnak indexet létrehozni Önnek, de a portál **Adatok importálása** varázslója a segítségére lehet ebben. A legtöbb esetben a varázsló következtetni tud az indexsémára a forrás meglévő metaadataiból, és előállít egy olyan előzetes indexsémát, amely beágyazott módon szerkeszthető mindaddig, amíg a varázsló aktív. Miután létrejött az index a szolgáltatásban, a további szerkesztés a portálon a legtöbb esetben új mezők hozzáadására van korlátozva. A varázsló használatát érdemes megfontolnia az indexek létrehozásakor, de az áttekintésükkor nem. A gyakorlati tanuláshoz végezze el a [portál útmutatójában](search-get-started-portal.md) foglalt lépéseket.

### <a name="step-3-create-and-run-or-schedule-the-indexer"></a>3. lépés: az indexelő létrehozása és futtatása (vagy ütemezett)

Az indexelő akkor fut le, amikor először [hoz létre egy indexelő](/rest/api/searchservice/Create-Indexer) a keresési szolgáltatásban. Csak akkor hozza létre vagy futtatja az indexelő, ha az adatforrás elérhető, vagy a készségkészlet érvényes. Az első futtatás után a [Run indexelő](/rest/api/searchservice/run-indexer)használatával újra futtathatja igény szerint, vagy megadhat [ismétlődő ütemezést](search-howto-schedule-indexers.md)is. 

Az indexelő állapotát a portálon vagy az [Indexelő status API](/rest/api/searchservice/get-indexer-status)-n keresztül követheti nyomon. A [lekérdezéseket az indexen is futtatnia](search-query-create.md) kell, hogy a várt eredményt ellenőrizze.

## <a name="next-steps"></a>Következő lépések

Most, hogy már bemutatkozott, a következő lépés az indexelő tulajdonságainak, a paraméterek, az ütemezés és az indexelő figyelésének áttekintése. Azt is megteheti, hogy visszatérhet a [támogatott adatforrások](#supported-data-sources) listájához egy adott forrással kapcsolatos további információkért.

+ [Indexelő létrehozása](search-howto-create-indexers.md)
+ [Indexelők ütemezése](search-howto-schedule-indexers.md)
+ [Mező-hozzárendelések definiálása](search-indexer-field-mappings.md)
+ [Indexelő állapotának figyelése](search-howto-monitor-indexers.md)