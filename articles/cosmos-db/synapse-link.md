---
title: Azure szinapszis-hivatkozás a Azure Cosmos DBhoz, az előnyökhöz és a használathoz
description: Ismerje meg a Azure Cosmos DB Azure szinapszis-hivatkozását. A szinapszis-hivatkozás lehetővé teszi, hogy közel valós idejű elemzést (HTAP) futtasson az Azure szinapszis Analytics használatával Azure Cosmos DB operatív adatain keresztül.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: c22a4030bb44c1499995071849ca8942ae2110c3
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864346"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Mi az az Azure Cosmos DB-hez készült Azure Synapse Link?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Az Azure szinapszis hivatkozása Azure Cosmos DB egy felhőalapú hibrid tranzakciós és analitikai feldolgozási (HTAP) képesség, amely lehetővé teszi a közel valós idejű elemzések futtatását Azure Cosmos DB-ban lévő operatív adaton. Az Azure szinapszis-kapcsolat szoros zökkenőmentes integrációt hoz létre a Azure Cosmos DB és az Azure szinapszis Analytics között.

A [Azure Cosmos db analitikus tároló](analytical-store-introduction.md), amely egy teljesen elszigetelt oszlopdiagram, az Azure szinapszis-hivatkozás lehetővé teszi, hogy az [Azure szinapszis](../synapse-analytics/overview-what-is.md) -elemzések ne legyenek kinyerve átalakítási-betöltési (etl) elemzések az operatív adatok nagy méretében. Az üzleti elemzők, az adatmérnökök és az adatszakértők mostantól a szinapszis Spark vagy a szinapszis SQL használatát is használhatják a közel valós idejű üzleti intelligencia, elemzés és gépi tanulási folyamatok futtatásához. Mindez a tranzakciós számítási feladatok teljesítményének csökkenése nélkül érhető el az Azure Cosmos DB-n. 

Az alábbi képen az Azure szinapszis hivatkozásának Azure Cosmos DB és az Azure szinapszis Analytics szolgáltatással való integrációja látható: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Architektúra-diagram az Azure szinapszis Analytics-integrációhoz Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Előnyei

A nagyméretű működési adatkészletek elemzéséhez, miközben minimálisra csökkenti a kritikus fontosságú tranzakciós számítási feladatok teljesítményére gyakorolt hatást, a hagyományos módon a Azure Cosmos DBben lévő operatív adatokat kinyeri és dolgozza fel a kinyert átalakító-betöltési (ETL) folyamatok. Az ETL-folyamatok sok adatátviteli réteget igényelnek, ami sok működési bonyolultságot eredményez, és teljesítménybeli hatással van a tranzakciós munkaterhelésekre. Emellett növeli a késést a működési adatoknak a származási időpontból való elemzéséhez.

A hagyományos ETL-alapú megoldásokkal összehasonlítva az Azure szinapszis hivatkozása Azure Cosmos DB számos előnyt kínál, például:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Kevesebb bonyolultság a kezelendő ETL-feladatok nélkül

Az Azure szinapszis-hivatkozás lehetővé teszi, hogy az Azure szinapszis Analytics használatával közvetlenül hozzáférhessen Azure Cosmos DB analitikus áruházhoz az összetett adatáthelyezés nélkül. Az operatív adatokon végrehajtott frissítések közel valós időben láthatók az analitikai tárolóban, amely nem tartalmaz ETL-t vagy adatcsatorna-módosítási feladatot. Az Azure szinapszis Analytics segítségével nagy léptékű elemzéseket futtathat az analitikus áruházból, további adatátalakítás nélkül.

### <a name="near-real-time-insights-into-your-operational-data"></a>Közel valós idejű betekintést nyerhet az operatív adataiba

Mostantól közel valós időben részletes elemzéseket készíthet az operatív adatairól az Azure szinapszis-hivatkozás használatával. Az ETL-alapú rendszerek általában nagyobb késéssel rendelkeznek az operatív adatok elemzéséhez, mert számos réteget kell kinyerni, átalakítani és betölteni az operatív adatokból. A Azure Cosmos DB Analytical Store és az Azure szinapszis Analytics natív integrációjának köszönhetően az operatív adatok közel valós időben elemezhetők, így új üzleti forgatókönyvek is elérhetők. 

### <a name="no-impact-on-operational-workloads"></a>Nincs hatással az üzemeltetési munkaterhelésekre

Az Azure szinapszis-hivatkozással analitikai lekérdezéseket futtathat egy Azure Cosmos DB analitikus tárolón (külön oszlopos tárolón), míg a tranzakciós műveletek a tranzakciós munkaterhelés (egy sor-alapú tranzakciós tároló) kiosztott átviteli sebessége alapján lesznek feldolgozva.  Az analitikai munkaterhelés a tranzakciós munkaterhelés-forgalomtól függetlenül szolgál a működési adatokhoz kiépített átviteli sebesség felhasználása nélkül.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Nagyméretű elemzési számítási feladatokhoz optimalizált

Azure Cosmos DB analitikus tároló úgy van optimalizálva, hogy skálázhatóságot, rugalmasságot és teljesítményt nyújtson az analitikai számítási feladatokhoz, és ez nem függ a számítási futtatási időktől. A tárolási technológia önállóan felügyelhető az elemzési számítási feladatok optimalizálása érdekében. Az Azure szinapszis Analytics beépített támogatásával a tárolási réteghez való hozzáférés egyszerűséget és nagy teljesítményt biztosít.

### <a name="cost-effective"></a>Költséghatékony.

Az Azure szinapszis-hivatkozással költséghatékony, teljes körűen felügyelt megoldás érhető el az operatív elemzésekhez. Ezzel kiküszöböli a hagyományos ETL-folyamatokban a működési adatok elemzéséhez szükséges további tárolási és számítási kapacitást. 

Azure Cosmos DB analitikus tároló egy fogyasztáson alapuló díjszabási modellt követ, amely az adattárolás és az analitikus olvasási/írási műveletek és a végrehajtott lekérdezések alapján történik. A tranzakciós számítási feladatokhoz jelenleg nem szükséges átviteli sebességet kiépíteni. Az adatoknak az Azure szinapszis Analytics szolgáltatással való nagy mértékben rugalmas számítási motorokkal való elérése teljes körűen biztosítja a tárterület és a számítási kapacitások hatékony üzemeltetését.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Elemzés a helyileg elérhető, globálisan elosztott, többrégiós írásokhoz

Az elemzési lekérdezéseket hatékonyan futtathatja Azure Cosmos DBban található adatközpont legközelebbi regionális példányán. A Azure Cosmos DB a legkorszerűbb képességet biztosítja a globálisan elosztott analitikai számítási feladatok, valamint a tranzakciós munkaterhelések aktív-aktív módon történő futtatásához.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>HTAP-forgatókönyvek engedélyezése az operatív adatokhoz

A szinapszis-hivatkozás az Azure szinapszis Analytics futtatókörnyezet támogatásával együtt Azure Cosmos DB analitikus áruházat. Ez az integráció lehetővé teszi, hogy olyan Felhőbeli natív HTAP (hibrid tranzakciós/analitikus feldolgozási) megoldásokat építsen ki, amelyek valós idejű frissítéseken alapuló elemzéseket hoznak létre a nagy adatkészleteken keresztül. Feloldja az új üzleti forgatókönyvek felépítését a riasztások élő trendek alapján történő előléptetésére, közel valós idejű irányítópultok létrehozásához és a felhasználói viselkedésen alapuló üzleti élményekhez.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB analitikus tároló

Azure Cosmos DB analitikus tároló az operatív adatok oszlopos ábrázolása Azure Cosmos DBban. Ez az analitikai tároló alkalmas a nagy teljesítményű adatkészletek gyors és költséghatékony lekérdezésére, az adatok másolása és a tranzakciós számítási feladatok teljesítményének befolyásolása nélkül.

Az analitikus tároló automatikusan magas frekvenciájú lapkákat, frissítéseket és a tranzakciós munkaterheléseket törli a közel valós időben, a Azure Cosmos DB teljes körűen felügyelt funkciójaként ("automatikus szinkronizálás"). Nincs szükség módosítási csatornára vagy ETL-ra. 

Ha globálisan elosztott Azure Cosmos DB-fiókkal rendelkezik, a tárolóhoz tartozó analitikai tároló engedélyezése után a fiók minden régiójában elérhető lesz. További információ az analitikai tárolóról: [Azure Cosmos db analitikus áruház – áttekintés](analytical-store-introduction.md) cikk.

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integráció az Azure szinapszis Analytics szolgáltatással

A szinapszis link használatával mostantól közvetlenül kapcsolódhat Azure Cosmos DB tárolóhoz az Azure szinapszis Analytics szolgáltatásból, és külön összekötők nélkül férhet hozzá az analitikai tárolóhoz. Az Azure szinapszis Analytics jelenleg támogatja a szinapszis-kapcsolatot a [szinapszis Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) és a [kiszolgáló nélküli SQL-készlettel](../synapse-analytics/sql/on-demand-workspace-overview.md).

Az adatok lekérdezése Azure Cosmos DB analitikus áruházból egyidejűleg végezhető el, az Azure szinapszis Analytics által támogatott különböző elemzési futtatási időpontokban való együttműködéssel. Az operatív információk elemzéséhez nincs szükség további adatátalakításra. Az analitikai tár adatai az alábbiak használatával kérdezhető le és elemezhetők:

* A szinapszis Apache Spark teljes körű támogatást nyújt a Scala, a Python, a SparkSQL és a C# nyelvhez. A szinapszis Spark az adattervezés és az adatelemzési forgatókönyvek központi része

* Kiszolgáló nélküli SQL-készlet a T-SQL nyelvvel és az ismerős BI-eszközök támogatása (például Power BI Premium stb.)

> [!NOTE]
> Az Azure szinapszis Analytics szolgáltatásban a Azure Cosmos DB tárolóban található analitikai és tranzakciós tárolók is elérhetők. Ha azonban nagy léptékű elemzéseket vagy vizsgálatokat szeretne futtatni az operatív adatokon, javasoljuk, hogy az analitikai tároló használatával elkerülje a tranzakciós számítási feladatokra gyakorolt hatást.

> [!NOTE]
> Az elemzést alacsony késéssel futtathatja egy Azure-régióban. ehhez csatlakoztassa a Azure Cosmos DB tárolót az adott régióban található szinapszis-futtatókörnyezethez.

Ez az integráció a következő HTAP forgatókönyveket teszi lehetővé a különböző felhasználók számára:

* Egy BI-mérnök, aki Power BI jelentést szeretne készíteni és közzétenni az élő operatív adatAzure Cosmos DB közvetlenül a szinapszis SQL-en keresztül való eléréséhez.

* Az adatelemzők, akik egy Azure Cosmos DB tároló operatív adatait szeretnék származtatni a szinapszis SQL-sel való lekérdezéssel, az adatok nagy mennyiségű beolvasását és az eredmények más adatforrásokkal való összevonását.

* Egy adattudós, aki a szinapszis Sparkot kívánja használni a modell fejlesztéséhez és a modell betanításához anélkül, hogy összetett adattervezést kellene végeznie. Emellett a modell eredményeit is megírhatják Azure Cosmos DB a Spark szinapszis használatával történő valós idejű pontozásra.

* Egy adatmérnök, amely a felhasználók számára elérhetővé szeretné tenni az SQL-vagy Spark-táblákat Azure Cosmos DB tárolók manuális ETL-folyamatok nélküli létrehozásával.

További információ az Azure szinapszis Analytics futtatókörnyezet Azure Cosmos DB támogatásáról: [Az Azure szinapszis Analytics használata Cosmos db támogatáshoz](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md).

## <a name="security"></a>Biztonság

A szinapszis-hivatkozás lehetővé teszi, hogy közel valós idejű elemzéseket futtasson a Azure Cosmos DB kritikus fontosságú adatain. Fontos annak biztosítása, hogy a kritikus fontosságú üzleti adatszolgáltatásokat a tranzakciós és az analitikus tárolókban is biztonságosan tárolja. A Azure Cosmos DB Azure szinapszis-hivatkozása úgy van kialakítva, hogy az alábbi funkciókkal segítsen kielégíteni ezeket a biztonsági követelményeket:

* **Hálózati elkülönítés magánhálózati végpontok használatával** – a tranzakciós és az analitikai tárolókban lévő, egymástól független hálózati hozzáférés szabályozására van lehetőség. A hálózat elkülönítése külön felügyelt magánhálózati végpontok használatával történik, amelyek az Azure szinapszis-munkaterületeken található felügyelt virtuális hálózatokon belül vannak tárolva. További információ: [privát végpontok konfigurálása az analitikai tárolóhoz](analytical-store-private-endpoints.md) cikk.

* **Adattitkosítás az ügyfél által felügyelt kulcsokkal** – a tranzakciós és analitikus adattárakban zökkenőmentesen titkosíthatja az adatvédelmet, ha ugyanazt az ügyfél által felügyelt kulcsokat használja automatikus és átlátható módon. További információt az [ügyfél által felügyelt kulcsok konfigurálása](how-to-setup-cmk.md) című cikkben talál.

* **Biztonságos kulcskezelő** szolgáltatás – a szinapszis Spark és a szinapszis kiszolgáló nélküli SQL-készletekben található adatokhoz való hozzáféréshez szükséges a szinapszis Analytics-munkaterületeken lévő Azure Cosmos db kulcsok kezelése. A Spark-feladatok és az SQL-parancsfájlok Azure Cosmos DB-fiók kulcsainak használata helyett az Azure szinapszis-kapcsolat biztonságosabb képességeket biztosít.

  * A szinapszis kiszolgáló nélküli SQL-készletek használatakor lekérdezheti a Azure Cosmos DB analitikus tárolót a fiók kulcsainak tárolására szolgáló SQL-hitelesítő adatok előzetes létrehozásával, és a `OPENROWSET` függvényre hivatkozva. További információért lásd: [lekérdezés egy kiszolgáló nélküli SQL-készlettel az Azure szinapszis hivatkozásáról](../synapse-analytics/sql/query-cosmos-db-analytical-store.md) szóló cikkben.

  * A szinapszis Spark használatakor tárolhatja a fiók kulcsait a társított szolgáltatási objektumokban, amelyek egy Azure Cosmos DB adatbázisra mutatnak, és ezt a Spark-konfigurációban hivatkozhatnak futásidőben. További információ: [az adat másolása DEDIKÁLT SQL-készletbe Apache Spark cikk használatával](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) .

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Mikor kell használni a Azure Cosmos DB Azure szinapszis-hivatkozást?

A szinapszis hivatkozását a következő esetekben javasoljuk:

* Ha Ön Azure Cosmos DB ügyfél, és az operatív adatain át szeretné futtatni az elemzést, a BI-t és a gépi tanulást. Ilyen esetekben a szinapszis-hivatkozás egy integráltabb elemzési élményt biztosít anélkül, hogy ez befolyásolná a tranzakciós tár kiosztott átviteli sebességét. Például:

  * Ha elemzést vagy BI-t futtat Azure Cosmos DB operatív adatait közvetlenül a különálló összekötők használatával, vagy

  * Ha ETL-folyamatokat futtat, az operatív adatok egy külön elemzési rendszerbe való kinyeréséhez.
 
Ilyen esetekben a szinapszis-hivatkozás egy integráltabb elemzési élményt biztosít anélkül, hogy ez befolyásolná a tranzakciós tár kiosztott átviteli sebességét.

A szinapszis-hivatkozás nem ajánlott, ha olyan hagyományos adattárház-követelményeket keres, mint például a magas Egyidejűség, a számítási feladatok kezelése és az összesítések megőrzése több adatforráson belül. További információkért tekintse meg a [gyakori forgatókönyveket, amelyek a Azure Cosmos db Azure szinapszis-hivatkozással](synapse-link-use-cases.md)használhatók.

## <a name="limitations"></a>Korlátozások

* Az Azure Cosmos DB-hez készült Azure Synapse Link támogatott az SQL API-k és a MongoDB-hez készült Azure Cosmos DB API esetében. A Gremlin API, a Cassandra API és a Table API esetében nem támogatott.

* Az analitikai tároló csak új tárolók esetében engedélyezhető. Ha a meglévő tárolók esetében az analitikai tárolót szeretné használni, [Azure Cosmos db áttelepítési eszközökkel](cosmosdb-migrationchoices.md)áttelepítheti a meglévő tárolók adatait az új tárolókra. A szinapszis hivatkozását engedélyezheti az új és a meglévő Azure Cosmos DB fiókokon.

* Az analitikai tárolóval rendelkező tárolók esetében a rendszer jelenleg nem támogatja az automatikus biztonsági mentést és visszaállítást az analitikus tárolóban. Ha a szinapszis-hivatkozás engedélyezve van egy adatbázis-fiókon, Azure Cosmos DB továbbra is automatikusan [biztonsági másolatot](./online-backup-and-restore.md) készít az adatairól a tranzakciós tárolóban (csak) a tárolók ütemezett biztonsági mentési időintervallumában, ahogy mindig. Fontos megjegyezni, hogy ha az analitikai tárolót bekapcsoló tároló egy új fiókra van visszaállítva, a tároló csak tranzakciós tárolóval lesz visszaállítva, és nincs engedélyezve az analitikus tároló.

* Jelenleg nem érhető el a Azure Cosmos DB Analytics-tároló elérése a szinapszis SQL kiépített állapotával.

## <a name="pricing"></a>Díjszabás

Az Azure szinapszis hivatkozás számlázási modellje tartalmazza a Azure Cosmos DB analitikus tároló és a szinapszis-futtatókörnyezet használatával felmerülő költségeket. További információért lásd a [Azure Cosmos db analitikus áruház díjszabását](analytical-store-introduction.md#analytical-store-pricing) és az [Azure szinapszis Analytics árképzési](https://azure.microsoft.com/pricing/details/synapse-analytics/) cikkeit.

## <a name="next-steps"></a>Következő lépések

További információért lásd a következő dokumentumokat:

* [Azure Cosmos DB analitikus áruház – áttekintés](analytical-store-introduction.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használatának első lépései](configure-synapse-link.md)
 
* [Mit támogat az Azure Synapse Analytics futtatókörnyezete?](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban](synapse-link-frequently-asked-questions.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használati esetei](synapse-link-use-cases.md)
