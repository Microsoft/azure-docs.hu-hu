---
title: Nagyméretű adathalmaz indexelése beépített indexelő használatával
titleSuffix: Azure Cognitive Search
description: Stratégiák a nagyméretű adatindexeléshez vagy a számítási feladatok számításához kötegelt módban, újraelosztással, valamint az ütemezett, párhuzamos és elosztott indexeléshez szükséges technikákkal.
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: b4f54aff78526ba52e56ed9f4cf1feddf40fa69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358392"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Nagyméretű adathalmazok indexelése az Azure-ban Cognitive Search

Az Azure Cognitive Search az adatok keresési indexbe történő importálásának [két alapvető megközelítését](search-what-is-data-import.md) *támogatja: az* adatok programozott módon történő továbbítása az indexbe, vagy az [Azure Cognitive Search indexelő](search-indexer-overview.md) használata egy támogatott adatforráson az adatok *lekéréséhez* .

Mivel az adatmennyiség növekedése vagy feldolgozása szükségessé vált, előfordulhat, hogy az egyszerű vagy az alapértelmezett indexelési stratégiák már nem praktikusak. Az Azure Cognitive Search esetében több módszer áll rendelkezésre a nagyobb adatkészletek elhelyezésére, az adatfeltöltési kérelmek szerkezetének módjától kezdve az ütemezett és elosztott számítási feladatokhoz forrás-specifikus indexelő használatával.

Ugyanezek a technikák a hosszan futó folyamatokra is érvényesek. A [párhuzamos indexelésben](#parallel-indexing) leírt lépések különösen hasznosak a számítási igényű indexeléshez, például a képelemzéshez vagy a természetes nyelvi feldolgozáshoz egy [mesterséges intelligencia](cognitive-search-concept-intro.md)-bővítési folyamatban.

A következő részekben ismertetjük a nagy mennyiségű, a leküldéses API-val és az indexelő szolgáltatással való indexeléshez szükséges technikákat.

## <a name="use-the-push-api"></a>A leküldéses API használata

Ha a [dokumentumok hozzáadása REST API](/rest/api/searchservice/addupdate-or-delete-documents) vagy a [IndexDocuments metódus](/dotnet/api/azure.search.documents.searchclient.indexdocuments)használatával küldi el az információkat egy indexbe, több fontos szempontot is figyelembe kell vennie az indexelés sebességének hatására. Ezeket a tényezőket az alábbi szakaszban ismertetjük, és a szolgáltatás kapacitásának beállítása a kód optimalizálására.

A leküldéses modell indexelését bemutató további információkat és kódokat a következő témakörben talál [: oktatóanyag: az indexelési sebesség optimalizálása](tutorial-optimize-indexing-push-api.md).

### <a name="capacity-of-your-service"></a>A szolgáltatás kapacitása

Első lépésként tekintse át a szolgáltatás kiépített szintjeinek jellemzőit és [korlátait](search-limits-quotas-capacity.md) . Az árképzési szintek egyik kulcsfontosságú megkülönböztető tényezője a partíciók mérete és sebessége, amely közvetlen hatással van az indexelés sebességére. Ha olyan szinten helyezi üzembe a keresési szolgáltatást, amely nem elegendő a számítási feladatokhoz, az új szintre való frissítés a legegyszerűbb és leghatékonyabb megoldás az indexelési teljesítmény növeléséhez.

Ha elégedett a szintjével, a következő lépés lehet a partíciók számának emelése. A partíciók kiosztása a kezdeti indexelés futtatása után lefelé állítható le, hogy csökkentse a szolgáltatás futtatásának teljes költségeit.

> [!NOTE]
> További replikák hozzáadásával növelheti az indexelési sebességet is, de nem garantált. Másfelől a további replikák növelik a keresési szolgáltatás által kezelhető lekérdezési kötetet. A replikák szintén kulcsfontosságú összetevők az [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)beszerzéséhez.
>
> A partíciók/replikák hozzáadása vagy magasabb szintre való frissítése előtt vegye figyelembe a pénzügyi költségeket és a kiosztási időt. A partíciók hozzáadásával jelentősen növelheti az indexelési sebességet, de a Hozzáadás/Eltávolítás akár 15 perctől akár több óráig is eltarthat. További információ: a [kapacitás beállításának](search-capacity-planning.md)dokumentációja.
>

### <a name="review-index-schema"></a>Index-séma áttekintése

Az index sémája fontos szerepet játszik az adatindexelésben. Minél több mező van, és a további megadott tulajdonságok (például *kereshető*, *sokrétű* vagy *szűrhető*) mind hozzájárulnak az indexelési idő növeléséhez. Általánosságban elmondható, hogy csak olyan mezőket kell létrehoznia és megadnia, amelyekhez ténylegesen szüksége van a keresési indexben.

> [!NOTE]
> A dokumentumok méretének megőrzéséhez ne adjon hozzá nem lekérdezhető adatmennyiséget egy indexhez. A képek és más bináris adatfájlok nem kereshetők közvetlenül, és nem tárolhatók az indexben. A nem lekérdezhető adatmennyiség keresési eredményekbe való integrálásához meg kell adnia egy nem kereshető mezőt, amely az erőforrás URL-hivatkozását tárolja.

### <a name="check-the-batch-size"></a>A köteg méretének keresése

A nagyobb adatkészletek indexelésének egyik legegyszerűbb mechanizmusa több dokumentum vagy rekord küldése egyetlen kérelembe. Ha a teljes adattartalom 16 MB alatti, a kérések legfeljebb 1000 dokumentumot kezelhetnek tömeges feltöltési műveletekben. Ezek a korlátozások érvényesek, függetlenül attól, hogy az [Add documents REST API](/rest/api/searchservice/addupdate-or-delete-documents) vagy a [IndexDocuments metódust](/dotnet/api/azure.search.documents.searchclient.indexdocuments) használja a .net SDK-ban. Mindkét API esetében a 1000-es dokumentumokat az egyes kérések törzsébe csomagoljuk.

A batchs használatával a dokumentumok indexelése jelentősen javítja az indexelési teljesítményt. Az adathalmazok optimális méretének meghatározása az indexelési sebesség optimalizálásának egyik fő összetevője. Az optimális batch-méretet befolyásoló két elsődleges tényező a következők:

+ Az index sémája
+ Az adataik mérete

Mivel az optimális batch-méret az indextől és az adatoktól függ, a legjobb módszer a különböző batch-méretek tesztelése annak meghatározására, hogy mi eredményezheti a forgatókönyv leggyorsabb indexelési sebességét. Ez az [oktatóanyag](tutorial-optimize-indexing-push-api.md) a Batch-méretek .net SDK-val történő teszteléséhez nyújt mintakód-kódot. 

### <a name="number-of-threadsworkers"></a>Szálak/feldolgozók száma

Ahhoz, hogy teljes mértékben ki tudja használni az Azure Cognitive Search indexelési sebességét, valószínűleg több szálat kell használnia, hogy a kötegelt indexelési kérelmeket egyidejűleg küldje el a szolgáltatásnak.  

A szálak optimális számát az alábbiak határozzák meg:

+ A keresési szolgáltatás szintje
+ A partíciók száma
+ A kötegek mérete
+ Az index sémája

Módosíthatja ezt a mintát és tesztelheti a különböző szálak számát, hogy meghatározza a forgatókönyv optimális szálak számát. Ha azonban egyszerre több szál fut, akkor a legtöbb hatékonysági előnyt ki kell használnia. 

> [!NOTE]
> A keresési szolgáltatás szintjeinek növelésével vagy a partíciók növelésével növelheti az egyidejű szálak számát is.

A keresési szolgáltatást elérve a kérések felfutásakor előfordulhat, hogy a kérést nem teljesen sikerült [http-állapotkódot](/rest/api/searchservice/http-status-codes) észlelni. Az indexelés során két gyakori HTTP-állapotkód van:

+ **503 a szolgáltatás nem érhető el** – ez a hiba azt jelenti, hogy a rendszer nagy terhelés alatt áll, és a kérés jelenleg nem dolgozható fel.
+ **207 többszörös állapot** – ez a hiba azt jelenti, hogy egyes dokumentumok sikeresek, de legalább egy sikertelen volt.

### <a name="retry-strategy"></a>Újrapróbálkozási stratégia

Ha hiba történik, a kérelmeket az [exponenciális leállítási újrapróbálkozási stratégiájának használatával újra](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)kell próbálkozni.

Az Azure Cognitive Search .NET SDK automatikusan újrapróbálkozik a 503s és más sikertelen kérelmekkel, de a 207s újrapróbálkozásához saját logikát kell megvalósítani. A nyílt forráskódú eszközök, például a [Polly](https://github.com/App-vNext/Polly) is használható az újrapróbálkozási stratégia megvalósításához.

### <a name="network-data-transfer-speeds"></a>Hálózati adatátviteli sebesség

A hálózati adatátviteli sebesség korlátozó tényező lehet az adatok indexelése során. Az Azure-környezetből származó adatok indexelésével egyszerűen felgyorsíthatja az indexelést.

## <a name="use-indexers-pull-api"></a>Indexelő használata (lekéréses API)

Az [Indexelő](search-indexer-overview.md) a támogatott Azure-adatforrások bejárására szolgál a kereshető tartalomhoz. Míg a nem kifejezetten a nagyméretű indexeléshez készült, több indexelő képesség különösen hasznos a nagyobb adatkészletek fogadásához:

+ Az ütemező segítségével rendszeres időközönként kioszthatja az indexelést, így az idő múlásával kiterjesztheti.
+ Az ütemezett indexelés az utolsó ismert leállítási ponton is folytatódhat. Ha egy adatforrást nem teljes egészében bemászott egy 24 órás időszakon belül, az indexelő a második napon folytatja az indexelést, bárhol marad.
+ Az adatmennyiség kisebb különálló adatforrásokra való particionálásával párhuzamos feldolgozást tesz lehetővé. A forrásadatok feloszthatók kisebb összetevőkre, például több tárolóra az Azure Blob Storage-ban, majd létrehozhatja a megfelelő, több [adatforrás-objektumot](/rest/api/searchservice/create-data-source) az Azure Cognitive Searchban, amely párhuzamosan indexelhető.

> [!NOTE]
> Az indexelő az adatforrás-specifikus, ezért az indexelő módszer használata csak az Azure-beli kiválasztott adatforrások esetén használható: [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [blob Storage](search-howto-indexing-azure-blob-storage.md), [Table Storage](search-howto-indexing-azure-tables.md), [Cosmos db](search-howto-index-cosmosdb.md).

### <a name="check-the-batchsize-argument-on-create-indexer"></a>A batchSize argumentum megtekintése a Create Indexer-ben

A leküldéses API-hoz hasonlóan az indexelő is lehetővé teszi az elemek másodpercenkénti számának konfigurálását. Az indexelő [REST API létrehozásán](/rest/api/searchservice/Create-Indexer)alapuló indexek esetében beállíthatja az `batchSize` argumentumot úgy, hogy testreszabja ezt a beállítást, hogy jobban megfeleljen az adatok jellemzőinek. 

Az alapértelmezett batch-méretek az adatforrás-specifikusak. Azure SQL Database és Azure Cosmos DB a Batch alapértelmezett mérete 1000. Ezzel szemben az Azure Blob indexelése 10 dokumentumon állítja be a köteg méretét a nagyobb méretű dokumentumok méretének elismeréseként. 

### <a name="scheduled-indexing"></a>Ütemezett indexelés

Az indexelő ütemezése fontos mechanizmus a nagyméretű adatkészletek feldolgozásához, valamint a lassú futású folyamatokhoz, például a képelemzéshez a kognitív keresési folyamatokban. Az indexelő feldolgozása egy 24 órás időszakon belül működik. Ha a feldolgozást 24 órán belül nem sikerül befejezni, az indexelő ütemezésének viselkedése működhet az előnyben. 

A tervezés szerint a ütemezett indexelés adott időközönként indul el, és a következő ütemezett időközön végzett folytatás előtt a feladatok általában befejeződik. Ha azonban a feldolgozás nem fejeződik be az intervallumon belül, az indexelő leáll (mert elfogyott az idő). A következő időszakban a feldolgozás folytatja az utolsó leállást, és a rendszer nyomon követi, hogy hol történik. 

A gyakorlatban az indexelési terhelés több napra kiterjed, és az indexelő 24 órás időpontra állíthatja. Ha a következő 24 órás ciklusban folytatódik az indexelés, az újraindul az utolsó ismert jó dokumentumon. Így az indexelő az összes feldolgozatlan dokumentum feldolgozását követően több napon belül is dolgozhat a dokumentumon. Az ütemtervek beállításával kapcsolatos további információkért tekintse meg az [indexelő REST API létrehozása](/rest/api/searchservice/Create-Indexer) című témakört, vagy tekintse át az [Azure Cognitive Search indexelő szolgáltatásának beütemezett módját](search-howto-schedule-indexers.md).

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>Párhuzamos indexelés

A párhuzamos indexelési stratégia a több adatforrások egyidejű indexelésén alapul, ahol az egyes adatforrás-definíciók az adathalmazt határozzák meg. 

A nem rutin jellegű, számítási igényű indexelési követelmények – mint például az OCR a beolvasott dokumentumokban egy kognitív keresési folyamatban, képelemzés vagy természetes nyelvi feldolgozás – a párhuzamos indexelési stratégia gyakran a megfelelő módszer a hosszú ideig futó folyamat végrehajtásához a lehető legrövidebb időn belül. Ha törölheti vagy csökkentheti a lekérdezési kérelmeket, a párhuzamos indexelés olyan szolgáltatáson, amely nem kezeli egyszerre a lekérdezéseket, a legjobb stratégiai lehetőség a lassú feldolgozású tartalom nagy törzse. 

A párhuzamos feldolgozás a következő elemekből áll:

+ A forrásadatok felosztása több tároló vagy ugyanazon tárolóban található több virtuális mappa között. 
+ Rendelje hozzá az egyes mini-adatkészleteket saját [adatforrásához](/rest/api/searchservice/create-data-source), párosítva a saját [Indexelő](/rest/api/searchservice/create-indexer)szolgáltatásával.
+ A kognitív kereséshez az egyes indexelő definíciók azonos [készségkészlet](/rest/api/searchservice/create-skillset) hivatkozhat.
+ Írás ugyanabba a cél keresési indexbe. 
+ Ütemezze az összes indexelő futtatását egy időben.

> [!NOTE]
> Az Azure Cognitive Searchban nem rendelhet hozzá különálló replikákat vagy partíciókat az indexeléshez vagy a lekérdezések feldolgozásához. A rendszer határozza meg az erőforrások felhasználásának módját. A lekérdezési teljesítményre gyakorolt hatás megismerése érdekében próbálkozzon párhuzamos indexeléssel egy tesztkörnyezetben, mielőtt éles környezetbe helyezné azokat.  

### <a name="how-to-configure-parallel-indexing"></a>Párhuzamos indexelés konfigurálása

Az indexek esetében a feldolgozási kapacitás lazán a keresési szolgáltatás által használt egyes szolgáltatási egységek (SU) egy indexelő alrendszerén alapul. Több párhuzamos indexelő is lehetséges az Azure Cognitive Search-szolgáltatásokban, amelyek legalább két replikával rendelkező alapszintű vagy standard csomagokban vannak kiépítve. 

1. A [Azure Portal](https://portal.azure.com)keresési szolgáltatás irányítópultjának **Áttekintés** lapján tekintse meg a **díjszabási szintet** annak megerősítéséhez, hogy képes-e párhuzamos indexelést fogadni. Az alapszintű és a standard csomag több replikát is kínál.

2. Több indexelő is futtatható párhuzamosan a szolgáltatásban található keresési egységek számaként. A **Beállítások**  >  **méretezése** elemnél növelje a párhuzamos feldolgozáshoz szükséges [replikákat](search-capacity-planning.md) vagy partíciókat: egy további replikát vagy partíciót az egyes indexelő munkaterhelésekhez. Hagyjon elegendő számot a meglévő lekérdezési kötethez. Az indexeléshez szükséges lekérdezési számítási feladatok feláldozása nem jó kompromisszum.

3. Az Azure Cognitive Search indexelő által elérhetővé tett szinten több tárolóba terjesztheti az adattárat. Ez több tábla lehet Azure SQL Database, több tároló az Azure Blob Storage-ban vagy több gyűjtemény is. Definiáljon egy adatforrás-objektumot minden táblához vagy tárolóhoz.

4. Több indexelő létrehozása és ütemezett futtatása párhuzamosan:

   + Tegyük fel, hogy egy szolgáltatás hat replikával rendelkezik. A hat indexelő beállítása, amelyek mindegyike egy olyan adatforráshoz van rendelve, amely a teljes adathalmaz 6 irányú felosztására szolgáló adathalmaz egy-hatodik példányát tartalmazza. 

   + Minden indexelő ugyanarra az indexre mutasson. A kognitív keresési munkaterhelések esetében minden indexelő ugyanarra a készségkészlet mutat.

   + Az egyes indexelő definíciók között ütemezze a futtatási időpontot. Például `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` létrehoz egy 2018-05-15-os ütemtervet az összes indexelő esetében, amely nyolc órás időközönként fut.

Az ütemezett időpontban minden indexelő megkezdi a végrehajtást, az adattöltést, a dúsítások alkalmazását (ha a kognitív keresési folyamatot konfigurálta), és az indexbe ír. Az Azure Cognitive Search nem zárolja a frissítések indexét. Az egyidejű írásokat a rendszer felügyeli, és újrapróbálkozik, ha egy adott írás nem sikerül az első kísérlet során.

> [!Note]
> A replikák növelésekor érdemes lehet növelni a partíciók darabszámát, ha az index mérete várhatóan jelentősen megnő. A partíciók az indexelt tartalom szeleteit tárolják; minél több partíció van, annál kisebb a szelet, amelyet egyenként tárolnia kell.

## <a name="see-also"></a>Lásd még

+ [Az indexelő áttekintése](search-indexer-overview.md)
+ [Indexelés a portálon](search-import-data-portal.md)
+ [Azure SQL Database indexelő](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB-indexelő](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage-indexelő](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage-indexelő](search-howto-indexing-azure-tables.md)
+ [Biztonság az Azure Cognitive Search](search-security-overview.md)