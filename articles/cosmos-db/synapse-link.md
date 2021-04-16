---
title: Azure Synapse Hivatkozás Azure Cosmos DB, előnyök és mikor használható
description: További információ Azure Synapse Link for Azure Cosmos DB. Synapse Link segítségével közel valós idejű elemzéseket (HTAP) futtathat Azure Synapse Analytics adatokon a Azure Cosmos DB.
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/30/2020
ms.reviewer: sngun
ms.custom: synapse-cosmos-db
ms.openlocfilehash: 123c443e1afaf8eaded7021b963b68b3d8a8f554
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483758"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db"></a>Mi az az Azure Cosmos DB-hez készült Azure Synapse Link?
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Synapse Link for Azure Cosmos DB egy natív felhőbeli hibrid tranzakciós és elemzési feldolgozási (HTAP) képesség, amellyel közel valós idejű elemzéseket futtathat a felhőben lévő Azure Cosmos DB. Azure Synapse Link szorosan zökkenőmentes integrációt hoz létre a Azure Cosmos DB és a Azure Synapse Analytics.

A [Azure Cosmos DB](analytical-store-introduction.md)elemzési tára, a teljesen elkülönített oszloptároló használatával a Azure Synapse Link nem tesz lehetővé [](../synapse-analytics/overview-what-is.md) kinyerési-átalakítási-betöltési (ETL) elemzést Azure Synapse Analytics a nagy léptékű operatív adatokon. Az üzleti elemzők, az adatmérnökök és az adattudósok mostantól a Synapse Sparkot vagy az Synapse SQL-t felcserélhető módon használják közel valós idejű üzleti intelligencia, elemzés és gépi tanulási folyamatok futtatásához. Mindez a tranzakciós számítási feladatok teljesítményének csökkenése nélkül érhető el az Azure Cosmos DB-n. 

Az alábbi képen a Azure Synapse Link integrációja látható Azure Cosmos DB és Azure Synapse Analytics: 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Architektúradiagram Azure Synapse Analytics integrációhoz a Azure Cosmos DB" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> Előnyök

A nagy méretű operatív adatkészletek elemzéséhez és a kritikus fontosságú tranzakciós számítási feladatok teljesítményére gyakorolt hatás minimalizálásához hagyományosan az Azure Cosmos DB-ban található működési adatokat kinyerik és feldolgoznak az Extract-Transform-Load (ETL) folyamatok. Az ETL-folyamatok számos adatátmozgatási réteget igényelnek, ami sokkal összetettebbé teszi a működést, és hatással van a tranzakciós számítási feladatok teljesítményére. Emellett növeli a késést a működési adatoknak a forrástól való elemzéséhez.

A hagyományos ETL-alapú megoldásokhoz képest a Azure Synapse Link for Azure Cosmos DB számos előnnyel jár, például:  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>Csökkentett összetettség, nincs szükség ETL-feladatok kezelésére

Azure Synapse Link lehetővé teszi Azure Cosmos DB, hogy összetett adatátmozgatás Azure Synapse Analytics nélkül közvetlenül hozzáfér Azure Synapse Analytics elemzési tárolóhoz. Az operatív adatok frissítései közel valós időben láthatók az elemzési tárolóban ETL- vagy változáscsatorna-feladatok nélkül. Az elemzési táran nagy léptékű elemzéseket futtathat a Azure Synapse Analytics adatátalakítás nélkül.

### <a name="near-real-time-insights-into-your-operational-data"></a>Közel valós idejű elemzések az üzemeltetési adatokról

Mostantól közel valós időben kaphat gazdag elemzési adatokat az operatív adatokról a Azure Synapse Link használatával. Az ETL-alapú rendszerek általában nagyobb késéssel elemzik az operatív adatokat, mivel az operatív adatok kinyeréséhez, átalakításához és betöltéséhez számos réteg szükséges. Az elemzési Azure Cosmos DB és a Azure Synapse Analytics natív integrációjával közel valós időben elemezheti az operatív adatokat új üzleti forgatókönyvek lehetővé tételéhez. 

### <a name="no-impact-on-operational-workloads"></a>Nincs hatással az üzemeltetési számítási feladatokra

A Azure Synapse Link használatával elemzési lekérdezéseket futtathat egy Azure Cosmos DB elemzési tárolón (egy külön oszloptárolón), míg a tranzakciós műveletek feldolgozása a tranzakciós számítási feladathoz (soralapú tranzakciós tároló) kiépített átviteli sebesség használatával történik.  Az elemzési számítási feladat a tranzakciós számítási feladatok forgalmától függetlenül lesz kiszolgálva anélkül, hogy az operatív adatokhoz szükséges átviteli sebességet fogyasztanának.

### <a name="optimized-for-large-scale-analytics-workloads"></a>Nagy léptékű elemzési számítási feladatokhoz optimalizálva

Azure Cosmos DB elemzési tároló úgy van optimalizálva, hogy skálázhatóságot, rugalmasságot és teljesítményt nyújtson az elemzési számítási feladatokhoz anélkül, hogy a számítási feladatok futási időtől függenének. Az elemzési számítási feladatok optimalizálása érdekében a tárolási technológia saját maga által felügyelt. A tárolási réteghez való beépített Azure Synapse Analytics az egyszerűséget és a nagy teljesítményt biztosítja.

### <a name="cost-effective"></a>Költséghatékony.

A Azure Synapse Link használatával költségoptimalált, teljes körűen felügyelt megoldást kaphat az operatív elemzésekhez. Kiküszöböli a hagyományos ETL-folyamatokban a működési adatok elemzéséhez szükséges további tárolási és számítási rétegeket. 

Azure Cosmos DB elemzési tár egy használatalapú díjszabási modellt követ, amely az adattároláson, az elemzési olvasási/írási műveleteken és a végrehajtott lekérdezésen alapul. Ehhez nem szükséges átviteli sebességet kiépítenünk, ahogy jelenleg is a tranzakciós számítási feladatok esetében. Az adatok rugalmas számítási motorokkal való elérése a Azure Synapse Analytics teszi a tárolás és a számítás futtatásának teljes költségét.


### <a name="analytics-for-locally-available-globally-distributed-multi-region-writes"></a>Helyileg elérhető, globálisan elosztott, többrétű írások elemzése

Az elemzési lekérdezéseket hatékonyan futtathatja a lekérdezésekben található adatok legközelebbi regionális Azure Cosmos DB. Azure Cosmos DB a legaktikusabb képességet biztosítja a globálisan elosztott elemzési számítási feladatok, valamint a tranzakciós számítási feladatok aktív-aktív módon való futtatására.

## <a name="enable-htap-scenarios-for-your-operational-data"></a>HTAP-forgatókönyvek engedélyezése a működési adatokhoz

Synapse Link elemzési Azure Cosmos DB az elemzési Azure Synapse támogatásával. Ez az integráció lehetővé teszi natív felhőbeli HTAP-megoldások (hibrid tranzakciós/analitikai feldolgozás) felépítését, amelyek a nagy adatkészletek működési adatainak valós idejű frissítései alapján hoznak létre elemzéseket. Új üzleti forgatókönyveket old fel, amelyek élő trendek alapján küldenek riasztásokat, közel valós idejű irányítópultokat és felhasználói viselkedésen alapuló üzleti felületeket hoznak létre.

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB elemzési tároló

Azure Cosmos DB elemzési tároló az operatív adatok oszlopalapú ábrázolása a Azure Cosmos DB. Ez az elemzési tároló nagy operatív adatkészletek gyors, költséghatékony lekérdezéséhez alkalmas anélkül, hogy átmásolja az adatokat, és hatással lenne a tranzakciós számítási feladatok teljesítményére.

Az elemzési tár a tranzakciós számítási feladatok nagy gyakoriságú beszúrásokat, frissítéseket és törléseket szinte valós időben automatikusan átvesz a szolgáltatás teljes körűen felügyelt képessége ("automatikus szinkronizálása") Azure Cosmos DB. Nincs szükség változáscsatornára vagy ETL-re. 

Ha globálisan elosztott fiókkal Azure Cosmos DB, miután engedélyezi az elemzési tárolót egy tárolóhoz, az a fiók minden régiójában elérhető lesz. Az elemzési tárolóval kapcsolatos további információkért lásd az [elemzési Azure Cosmos DB áttekintését ismertető cikket.](analytical-store-introduction.md)

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>Integráció a Azure Synapse Analytics

A Synapse Link mostantól közvetlenül csatlakozhat a Azure Cosmos DB-tárolókhoz a Azure Synapse Analytics és külön összekötők nélkül férhet hozzá az elemzési tárolóhoz. Azure Synapse Analytics jelenleg a [Synapse](../synapse-analytics/spark/apache-spark-concepts.md) Synapse Link és a kiszolgáló nélküli [SQL Apache Spark készletek használatával támogatja a használatot.](../synapse-analytics/sql/on-demand-workspace-overview.md)

Egyidejűleg lekérdezheti az adatokat Azure Cosmos DB elemzési tárolóból, a különböző elemzési futási időkben való interop használatával, amelyet a Azure Synapse Analytics. Az operatív adatok elemzéséhez nincs szükség további adatátalakításra. Az elemzési adattár adatait a következő használatával lehet lekérdezni és elemezni:

* Synapse Apache Spark Scala, Python, SparkSQL és C# teljes körű támogatásával. A Synapse Spark központi szerepet tartalmaz az adatmérnöki és adattudományi forgatókönyvekben

* Kiszolgáló nélküli SQL-készlet T-SQL nyelvvel és a jól ismert BI-eszközök (például Power BI Premium stb.) támogatása

> [!NOTE]
> A Azure Synapse Analytics elemzési és tranzakciós tárolókhoz is hozzáférhet a Azure Cosmos DB tárolóban. Ha azonban nagy léptékű elemzést vagy vizsgálatot szeretne futtatni az operatív adatokon, javasoljuk, hogy elemzési tárolót használjon a tranzakciós számítási feladatok teljesítményére gyakorolt hatásának elkerülése érdekében.

> [!NOTE]
> Alacsony késésű elemzéseket futtathat egy Azure-régióban, ha csatlakoztatja a Azure Cosmos DB-tárolót a régióban található Synapse-runtime-hoz.

Ez az integráció a következő HTAP-forgatókönyveket teszi lehetővé a különböző felhasználók számára:

* Egy BI-mérnök, aki egy jelentésmodellt szeretne Power BI és közzétenné, hogy közvetlenül a Azure Cosmos DB élő működési adataihoz fér Synapse SQL.

* Egy adatelemző, aki egy Azure Cosmos DB-tárolóban található működési adatokból szeretne elemzéseket kihozni az Synapse SQL-val való lekérdezéssel, nagy méretekben szeretné olvasni az adatokat, és ezeket az eredményeket más adatforrásokkal kombinálni.

* Egy adattudós, aki a Synapse Spark használatával szeretne egy olyan funkciót találni, amely összetett adattervezés nélkül fejleszti és betanítja a modellt. A modell bejegyzésének eredményeit a Spark Synapse-on keresztül Azure Cosmos DB adatok valós idejű pontozására is kiírhatja.

* Adatmérnök, aki adatokat szeretne elérhetővé tenni a fogyasztók számára azáltal, hogy SQL- vagy Spark-táblákat hoz létre Azure Cosmos DB tárolókban manuális ETL-folyamatok nélkül.

További információ a Azure Synapse Analytics-támogatásról: Azure Cosmos DB Azure Synapse Analytics a Cosmos DB [támogatásához.](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>Mikor használja a Azure Synapse Linket Azure Cosmos DB?

Synapse Link ajánlott a következő esetekben:

* Ha Ön egy Azure Cosmos DB, és elemzéseket, bi-adatokat és gépi tanulást szeretne futtatni a működési adatokon. Ilyen esetekben a Synapse Link integráltabb elemzési élményt biztosít anélkül, hogy befolyásolta volna a tranzakciós tároló kiépített átviteli sebességét. Például:

  * Ha elemzéseket vagy ÜZLETI adatokat futtat a Azure Cosmos DB a működési adatokat közvetlenül külön összekötők használatával, vagy

  * Ha ETL-folyamatokat futtat a működési adatok külön elemzési rendszerbe való kinyerését.
 
Ilyen esetekben a Synapse Link integráltabb elemzési élményt biztosít anélkül, hogy befolyásolta volna a tranzakciós tároló kiépített átviteli sebességét.

Synapse Link használata nem ajánlott, ha olyan hagyományos adattárház-követelményeket keres, mint a magas egyidejűség, a számítási feladatok kezelése és az összesítések több adatforrásban való állandósága. További információkért tekintse meg az Azure Synapse Link for Azure Cosmos DB gyakori [forgatókönyveket.](synapse-link-use-cases.md)

## <a name="limitations"></a>Korlátozások

* Az Azure Cosmos DB-hez készült Azure Synapse Link támogatott az SQL API-k és a MongoDB-hez készült Azure Cosmos DB API esetében. A Gremlin API, a Cassandra API és a Table API.

* Az elemzési tárolók csak új tárolókhoz engedélyezhetők. Ha elemzési tárat használ a meglévő tárolókhoz, migrálja az adatokat a meglévő tárolókból az új tárolókba a [Azure Cosmos DB migrálási eszközökkel.](cosmosdb-migrationchoices.md) Engedélyezheti a Synapse Link új és meglévő Azure Cosmos DB fiókokon.

* Az elemzési tárat bekapcsolt tárolók esetén az adatok automatikus biztonsági mentése és visszaállítása az elemzési tárolóban jelenleg nem támogatott. Ha Synapse Link engedélyezve van egy adatbázisfiókban, a Azure Cosmos DB [](./online-backup-and-restore.md) továbbra is automatikusan biztonsági másolatot készít a tárolók tranzakciós tárolóiban tárolt adatairól az ütemezett biztonsági mentési időközökben, ahogy mindig. Fontos megjegyezni, hogy ha egy elemzési tárolják bekapcsolt tárolót visszaállít egy új fiókba, a tároló csak tranzakciós tárolóval lesz visszaállva, és nincs engedélyezve az elemzési tároló.

* A Azure Cosmos DB elemzési Synapse SQL való elérése jelenleg nem érhető el.

## <a name="security"></a>Biztonság

Synapse Link használatával közel valós idejű elemzéseket futtathat a kritikus fontosságú adatokon a Azure Cosmos DB. Rendkívül fontos, hogy a kritikus fontosságú üzleti adatok biztonságosan tárolódnak a tranzakciós és elemzési tárolókban is. Azure Synapse Link for Azure Cosmos DB a következő funkciókkal segít megfelelni ezeknek a biztonsági követelményeknek:

* **Hálózatelszigetelés** privát végpontok használatával – A tranzakciós és elemzési tárolókban lévő adatok hálózati hozzáférését egymástól függetlenül szabályozhatja. A hálózat elkülönítése külön felügyelt privát végpontok használatával történik az egyes tárolókhoz, a felügyelt virtuális hálózatokon belül Azure Synapse munkaterületeken. További információ: Privát [végpontok konfigurálása elemzésitárhoz.](analytical-store-private-endpoints.md)

* **Adattitkosítás** ügyfél által kezelt kulcsokkal – Az adatokat zökkenőmentesen titkosíthatja a tranzakciós és elemzési tárolókban ugyanazokkal az ügyfél által kezelt kulcsokkal, automatikus és átlátható módon. További információ: Ügyfél által kezelt [kulcsok konfigurálása.](how-to-setup-cmk.md)

* **Biztonságos kulcskezelés** – Az elemzési tárolóban lévő adatok Synapse Sparkból és kiszolgáló nélküli Synapse SQL-készletekből való eléréséhez Azure Cosmos DB kulcsokat kell Synapse Analytics munkaterületeken. Ahelyett, hogy a Azure Cosmos DB Spark-feladatokban vagy SQL-szkriptekben beágyazott fiókkulcsokat használ, a Azure Synapse Link biztonságosabb képességeket biztosít.

  * Kiszolgáló nélküli Synapse SQL-készletek használata esetén lekérdezheti az Azure Cosmos DB elemzési tárolóját úgy, hogy előre létrehozza a fiókkulcsokat tároló SQL-hitelesítő adatokat, és ezekre hivatkozik a `OPENROWSET` függvényben. További információ: Lekérdezés kiszolgáló nélküli SQL-készlet használatával a [Azure Synapse Link című cikkben.](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

  * A Synapse Spark használata esetén a fiókkulcsokat egy Azure Cosmos DB-adatbázisra hivatkozó csatolt szolgáltatásobjektumban tárolhatja, és futtatáskor hivatkozhat erre a Spark-konfigurációban. További információ: [Adatok](../synapse-analytics/synapse-link/how-to-copy-to-sql-pool.md) másolása dedikált SQL-készletbe az Apache Spark használatával.


## <a name="pricing"></a>Díjszabás

A Azure Synapse Link számlázási modellje tartalmazza a Azure Cosmos DB elemzési tároló és a Synapse-futtatás használatával felmerült költségeket. További tudnivalókért tekintse meg az [elemzési Azure Cosmos DB és](analytical-store-introduction.md#analytical-store-pricing) díjszabását Azure Synapse Analytics [cikkeket.](https://azure.microsoft.com/pricing/details/synapse-analytics/)

## <a name="next-steps"></a>Következő lépések

További tudnivalókért tekintse meg a következő dokumentumokban:

* [Azure Cosmos DB elemzési tároló áttekintése](analytical-store-introduction.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használatának első lépései](configure-synapse-link.md)
 
* [Mit támogat az Azure Synapse Analytics futtatókörnyezete?](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [Gyakori kérdések az Azure Cosmos DB-hez készült Azure Synapse Linkkel kapcsolatban](synapse-link-frequently-asked-questions.md)

* [Az Azure Cosmos DB-hez készült Azure Synapse Link használati esetei](synapse-link-use-cases.md)
