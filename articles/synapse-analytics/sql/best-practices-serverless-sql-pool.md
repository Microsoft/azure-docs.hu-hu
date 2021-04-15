---
title: Ajánlott eljárások kiszolgáló nélküli SQL-készlethez
description: Javaslatok és ajánlott eljárások a kiszolgáló nélküli SQL-készlethez.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: a2656d5c23a465856eee1e84d2c4f6900b21ec41
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477468"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>A kiszolgáló nélküli SQL-készlet ajánlott Azure Synapse Analytics

Ebben a cikkben számos ajánlott eljárás található a kiszolgáló nélküli SQL-készlet használatával kapcsolatban. A kiszolgáló nélküli SQL-készlet a Azure Synapse Analytics.

A kiszolgáló nélküli SQL-készlet lehetővé teszi az Azure-tárfiókok fájljainak lekérdezését. Nem rendelkezik helyi tárolási vagy adatbelési képességekkel. Így a lekérdezés által megcélzott összes fájl a kiszolgáló nélküli SQL-készleten kívül található. A fájlok tárolóból való beolvasásával kapcsolatos minden hatással lehet a lekérdezési teljesítményre.

Néhány általános irányelv:
- Győződjön meg arról, hogy az ügyfélalkalmazások a kiszolgáló nélküli SQL-készletben vannak együtt.
  - Ha az Azure-n kívül (például Power BI Desktop, SSMS, ADS) használ ügyfélalkalmazásokat, győződjön meg arról, hogy egy, az ügyfélszámítógéphez közeli régióban használja a kiszolgáló nélküli készletet.
- Győződjön meg arról, hogy a tároló (Azure Data Lake, Cosmos DB) és a kiszolgáló nélküli SQL-készlet ugyanabban a régióban van.
- Próbálja meg [optimalizálni a tároló elrendezését](#prepare-files-for-querying) particionálással, és tartsa a fájlokat 100 MB és 10 GB közötti tartományban.
- Ha sok eredményt ad vissza, győződjön meg arról, hogy SSMS-t vagy ADS-t használ, és nem Synapse Studio. Synapse Studio egy olyan webes eszköz, amely nem nagy eredményhalmazok számára készült. 
- Ha sztringoszlop alapján szűri az eredményeket, próbáljon meg `BIN2_UTF8` rendezést használni.
- Próbálja meg gyorsítótárazza az eredményeket az ügyféloldalon importálási Power BI mód vagy Azure Analysis Services és rendszeresen frissítse őket. A kiszolgáló nélküli SQL-készletek nem tudnak interaktív élményt nyújtani Power BI Direct Query módban, ha összetett lekérdezéseket használ, vagy nagy mennyiségű adatot használ.

## <a name="client-applications-and-network-connections"></a>Ügyfélalkalmazások és hálózati kapcsolatok

Győződjön meg arról, hogy az ügyfélalkalmazás a lehető legközelebbi Synapse-munkaterülethez csatlakozik az optimális kapcsolattal.
- Ügyfélalkalmazás közös létrehozása a Synapse-munkaterülettel. Ha olyan alkalmazásokat használ, mint a Power BI vagy az Azure Analysis Service, győződjön meg arról, hogy ugyanabban a régióban vannak, ahol a Synapse-munkaterületet helyezte el. Szükség esetén hozza létre az ügyfélalkalmazásokkal párosított különálló munkaterületeket. Ha egy ügyfélalkalmazást és a Synapse-munkaterületet egy másik régióban helyezi el, az nagyobb késést és lassabb streamelést eredményezhet.
- Ha a helyszíni alkalmazásból olvas adatokat, győződjön meg arról, hogy a Synapse-munkaterület a helyéhez közeli régióban található.
- Nagy mennyiségű adat beolvasása közben győződjön meg arról, hogy nem tartalmaz hálózati sávszélességgel kapcsolatos problémákat.
- Ne használja a Synapse Studiót nagy mennyiségű adat visszaadása érdekében. A Synapse Studio egy webes eszköz, amely HTTPS protokollt használ az adatok átviteléhez. A Azure Data Studio vagy SQL Server Management Studio nagy mennyiségű adat beolvassa.

## <a name="storage-and-content-layout"></a>Tárolás és tartalom elrendezése

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>A tároló és a kiszolgáló nélküli SQL-készlet közös használata

A késés minimalizálása érdekében közös hellyel kell elosztani az Azure Storage-fiókot vagy a CosmosDB-beli analytic storage-et és a kiszolgáló nélküli SQL-készlet végpontját. A munkaterület létrehozása során létesített tárfiókok és végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha kiszolgáló nélküli SQL-készletet futtató más tárfiókokat ér el, győződjön meg arról, hogy ugyanabban a régióban vannak. Ha nem ugyanabban a régióban vannak, nagyobb lesz a késés az adatoknak a távoli régió és a végpont régiója közötti hálózati átvitele esetén.

### <a name="azure-storage-throttling"></a>Az Azure Storage szabályozása

A tárfiókhoz több alkalmazás és szolgáltatás is hozzáférhet. A tárterület szabályozása akkor történik, ha az alkalmazások, szolgáltatások és kiszolgáló nélküli SQL-készlet számítási feladatai által létrehozott kombinált IOPS vagy átviteli sebesség meghaladja a tárfiók korlátait. Ennek eredményeképpen jelentős negatív hatással lesz a lekérdezési teljesítményre.

Szabályozás észlelésekor a kiszolgáló nélküli SQL-készlet beépített kezelési megoldással rendelkezik. A kiszolgáló nélküli SQL-készlet lassabban, a szabályozás megoldásáig kéréseket ad a tárolónak.

> [!TIP]
> Az optimális lekérdezés-végrehajtás érdekében a lekérdezések végrehajtása során ne nagy terhelést okoz a tárfiók más számítási feladatokkal.

### <a name="azure-ad-pass-through-performance"></a>Az Azure AD átmenő teljesítménye

A kiszolgáló nélküli SQL-készlet lehetővé teszi a tárterületen lévő fájlok Azure Active Directory (Azure AD) átmenő vagy SAS-hitelesítő adatok használatával. Az Azure AD átmenő szolgáltatása lassabb teljesítményt tapasztalhat, mint az SAS használata.

Ha nagyobb teljesítményre van szüksége, próbáljon SAS-hitelesítő adatokat használni a tároló eléréséhez.

### <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezéshez

Ha lehetséges, előkészítheti a fájlokat a jobb teljesítmény érdekében:

- Nagy méretű CSV és JSON konvertálása Parquetre. A Parquet egy oszlopos formátum. Mivel tömörítve van, a fájlmérete kisebb, mint az azonos adatokat tartalmazó CSV- vagy JSON-fájlok. A kiszolgáló nélküli SQL-készlet képes kihagyni a lekérdezésben nem szükséges oszlopokat és sorokat, ha Parquet-fájlokat olvas. A kiszolgáló nélküli SQL-készlet kevesebb időt és kevesebb tárolási kérést igényel az olvasáshoz.
- Ha egy lekérdezés egyetlen nagy méretű fájlt célozza meg, akkor hasznos lehet több kisebb fájlra felosztjuk.
- Próbálja meg 100 MB és 10 GB között tartani a CSV-fájl méretét.
- Jobb, ha egyenlő méretű fájlokat tartalmaz egyetlen OPENROWSET elérési úthoz vagy egy külső tábla LOCATION helyéhez.
- Particionálhatja az adatokat úgy, hogy a partíciókat különböző mappákban vagy fájlnevekben tárolja. Lásd: Use filename and filepath functions to target specific partitions (Fájlnév és [fájl elérési_elérési__függvények használata adott partíciók megcélhoz).](#use-filename-and-filepath-functions-to-target-specific-partitions)

### <a name="colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool"></a>A CosmosDB elemzési tár és a kiszolgáló nélküli SQL-készlet közös használata

Győződjön meg arról, hogy a CosmosDB elemzési tára ugyanabban a régióban van, mint a Synapse-munkaterület. A régiók közötti lekérdezések nagy késést okozhatnak. Használja a region tulajdonságot a kapcsolati sztringben annak a régiónak a explicit megadásához, ahol az elemzési tároló található (lásd a [CosmosDb](query-cosmos-db-analytical-store.md#overview)kiszolgáló nélküli SQL-készlet használatával való lekérdezését):

```
'account=<database account name>;database=<database name>;region=<region name>'
```

## <a name="csv-optimizations"></a>CSV-optimalizálások

### <a name="use-parser_version-20-to-query-csv-files"></a>A PARSER_VERSION 2.0 használata CSV-fájlok lekérdezéséhez

CSV-fájlok lekérdezéséhez teljesítményoptimalált elemzőt is használhat. Részletekért lásd: [PARSER_VERSION.](develop-openrowset.md)

### <a name="manually-create-statistics-for-csv-files"></a>STATISZTIKÁK manuális létrehozása CSV-fájlokhoz

A kiszolgáló nélküli SQL-készlet statisztikákra támaszkodik az optimális lekérdezés-végrehajtási tervek létrehozásához. Szükség esetén a Rendszer automatikusan statisztikákat hoz létre a Parquet-fájlok oszlopaihoz. Jelenleg a RENDSZER nem hoz létre statisztikákat automatikusan a CSV-fájlok oszlopaihoz, és manuálisan kell statisztikákat létrehoznia a lekérdezésekben használt oszlopokhoz, különösen a DISTINCT, JOIN, WHERE, ORDER BY és GROUP BY oszlopokhoz. A részletekért tekintse meg a kiszolgáló [nélküli SQL-készlet statisztikáit.](develop-tables-statistics.md#statistics-in-serverless-sql-pool)


## <a name="data-types"></a>Adattípusok

### <a name="use-appropriate-data-types"></a>A megfelelő adattípusok használata

A lekérdezésben használt adattípusok hatással vannak a teljesítményre. Jobb teljesítményt kaphat, ha követi az alábbi irányelveket: 

- Használja a legkisebb adatméretet, amely a lehető legnagyobb értéket fogja használni.
  - Ha a karakter maximális hossza 30 karakter, használjon 30 karakter hosszúságú karakter adattípust.
  - Ha minden karakteroszlop-érték rögzített méretű, használja a **char** vagy **az nchar értéket.** Ellenkező esetben használja a **varchar vagy** **az nvarchar használhatja.**
  - Ha az egész oszlop maximális értéke 500, használja a **smallint** értéket, mert ez az érték elhelyezésére alkalmas legkisebb adattípus. Ebben a cikkben egész szám típusú adattípus-tartományokat [talál.](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- Ha lehetséges, használja a **varchar** és **a char** használhatja az **nvarchar és az** **nchar helyett.**
- Ha lehetséges, használjon egész számalapú adattípusokat. A SORT, JOIN és GROUP BY műveletek egész számokkal gyorsabban befejeződnek, mint a karakteradatokon.
- Ha sémakövetkeztatást használ, ellenőrizze [a kikövetkeztetett adattípusokat.](#check-inferred-data-types)

### <a name="check-inferred-data-types"></a>Következtetett adattípusok ellenőrzése

[A séma-következtetés segítségével](query-parquet-files.md#automatic-schema-inference) gyorsan írhat lekérdezéseket és tárhat fel adatokat anélkül, hogy ismernie kellene a fájlsémát. Ennek az egyszerűségnek az az ára, hogy a kikövetkeztetett adattípusok nagyobbak lehetnek a tényleges adattípusnál. Ez akkor fordul elő, ha nincs elég információ a forrásfájlokban a megfelelő adattípus megfelelő használata érdekében. A Parquet-fájlok például nem tartalmaznak metaadatokat a karakteroszlopok maximális hosszáról. A kiszolgáló nélküli SQL-készlet tehát varchar(8000) értékként van kivetve.

A lekérdezés [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) a lekérdezés eredményül kapott adattípusának ellenőrzéséhez.

Az alábbi példa bemutatja, hogyan optimalizálhatja a kikövetkező adattípusokat. Ezzel az eljárással mutatjuk be a kikövetkeztetett adattípusokat: 
```sql  
EXEC sp_describe_first_result_set N'
    SELECT
        vendor_id, pickup_datetime, passenger_count
    FROM 
        OPENROWSET(
            BULK ''https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*'',
            FORMAT=''PARQUET''
        ) AS nyc';
```

Az eredményhalmaz a következő:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar(8000)|8000|
|0|2|pickup_datetime|datetime2(7)|8|
|0|3|passenger_count|int|4|

A lekérdezés kikövetkeztetett adattípusának meghatározása után megadhatja a megfelelő adattípusokat:

```sql  
SELECT
    vendorID, tpepPickupDateTime, passengerCount
FROM 
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=2018/puMonth=*/*.snappy.parquet',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Szűrőoptimalizálás

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Helyettesítő karakterek leküldése az elérési út alacsonyabb szintjeire

Az elérési út helyettesítő karakterekkel több fájlt és mappát [is lekérdezhet.](query-data-storage.md#query-multiple-files-or-folders) A kiszolgáló nélküli SQL-készlet felsorolja a tárfiókban lévő fájlokat, kezdve az első * -től a Storage API használatával. Kiküszöböli azokat a fájlokat, amelyek nem egyeznek a megadott elérési úthoz. A fájlok kezdeti listájának csökkentése javíthatja a teljesítményt, ha sok olyan fájl van, amely megfelel a megadott elérési útnak az első helyettesítő karakternek.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Adott partíciók megcélzott fájlnév- és filepath-függvények használata

Az adatok gyakran partíciókba vannak rendezve. Utasíthatja a kiszolgáló nélküli SQL-készletet adott mappák és fájlok lekérdezésére. Ezzel csökkenti a fájlok számát és a lekérdezés által beolvasott és feldolgozni szükséges adatmennyiséget. Egy további bónusz, hogy jobb teljesítményt érhet el.

További információért olvassa el a fájlnév és a [fájl](query-data-storage.md#filepath-function) elérési_elérési_állomás függvényét, és tekintse meg az adott fájlok [lekérdezésére vonatkozó példákat.](query-specific-files.md) [](query-data-storage.md#filename-function)

> [!TIP]
> A fájl elérési és fájlnév függvényének eredményeit mindig a megfelelő adattípusok szerint kell átveszni. Karakter adattípusok használata esetén mindenképpen a megfelelő hosszúságot használja.

> [!NOTE]
> A partíciók megszüntetéséhez, a fájl elérési úthoz és a fájlnévhez használt függvények jelenleg nem támogatottak a külső táblákhoz, csak azok, amelyek automatikusan vannak létrehozva a Apache Spark a Azure Synapse Analytics.

Ha a tárolt adatok nem particionáltak, fontolja meg a particionálást. Így ezekkel a függvényekkel optimalizálhatja a fájlokat célzó lekérdezéseket. Amikor kiszolgáló nélküli [SQL-készletből](develop-storage-files-spark-tables.md) Apache Spark lekérdezést Azure Synapse, a lekérdezés automatikusan csak a szükséges fájlokat célozza meg.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>A karakteroszlopok predikátumos leküldéses leküldésének használata megfelelő rendezés használatával

A Parquet-fájlban lévő adatok sorcsoportokba rendezve adatokat tartalmaznak. A kiszolgáló nélküli SQL-készlet kihagyja a sorcsoportokat a WHERE záradékban megadott predikátum alapján, így csökkenti az I/O-t, ami nagyobb lekérdezési teljesítményt eredményez. 

Vegye figyelembe, hogy a Parquet-fájlokban lévő karakteroszlopok predikátumos leküldéses leküldése csak Latin1_General_100_BIN2_UTF8 rendezés esetén támogatott. Egy adott oszlophoz a WITH záradékkal adhatja meg a rendezést. Ha nem a WITH záradékkal adja meg ezt a rendezést, a rendszer adatbázis-rendezést használ.

## <a name="optimize-repeating-queries"></a>Ismétlődő lekérdezések optimalizálása

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezési teljesítmény és az illesztés javítása érdekében

[A CETAS](develop-tables-cetas.md) a kiszolgáló nélküli SQL-készlet egyik legfontosabb szolgáltatása. A CETAS egy párhuzamos művelet, amely külső tábla-metaadatokat hoz létre, és exportálja a SELECT lekérdezés eredményeit a tárfiókban lévő fájlok egy készletére.

A CETAS használatával a lekérdezések gyakran használt részeit, például az összekapcsolt referenciatáblákat új fájlkészletekbe materializálhatja. Ezután csatlakozhat ehhez az egyetlen külső táblához ahelyett, hogy több lekérdezésben megismételné a gyakori illesztéseket.

Amikor a CETAS Parquet-fájlokat hoz létre, a rendszer automatikusan statisztikákat hoz létre, amikor az első lekérdezés erre a külső táblára céloz, ami jobb teljesítményt eredményez a CETAS-sel létrehozott táblát célzó további lekérdezések esetén.

## <a name="next-steps"></a>Következő lépések

A gyakori [problémák megoldásához](resources-self-help-sql-on-demand.md) tekintse át a hibaelhárítási cikket. Ha kiszolgáló nélküli SQL-készlet helyett dedikált SQL-készletekkel dolgozik, tekintse meg a dedikált [SQL-készletekkel](best-practices-dedicated-sql-pool.md) kapcsolatos ajánlott eljárásokat.
