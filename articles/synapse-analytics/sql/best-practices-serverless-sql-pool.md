---
title: Ajánlott eljárások kiszolgáló nélküli SQL-készlethez
description: Javaslatok és ajánlott eljárások a kiszolgáló nélküli SQL-készlettel való munkavégzéshez.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 05/01/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 75e187369eccefb255ae2bbd88de79afbc4fd4dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669474"
---
# <a name="best-practices-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Ajánlott eljárások kiszolgáló nélküli SQL-készlethez az Azure szinapszis Analyticsben

Ebben a cikkben az ajánlott eljárások gyűjteményét találja a kiszolgáló nélküli SQL-készlet használatára. A kiszolgáló nélküli SQL-készlet az Azure szinapszis Analytics egyik erőforrása.

A kiszolgáló nélküli SQL-készlet lehetővé teszi a fájlok lekérdezését az Azure Storage-fiókokban. Nem rendelkezik helyi tárolási vagy betöltési képességekkel. Tehát az összes olyan fájl, amelyet a lekérdezés a kiszolgáló nélküli SQL-készleten kívülre mutat. A fájlok tárterületről való olvasásával kapcsolatos minden művelet hatással lehet a lekérdezés teljesítményére.

## <a name="storage-and-content-layout"></a>Tárolás és tartalom elrendezése

### <a name="colocate-your-storage-and-serverless-sql-pool"></a>Tároló és kiszolgáló nélküli SQL-készlet közös elhelyezése

A késés csökkentése érdekében keresse meg az Azure Storage-fiókját, vagy a CosmosDB analitikai tárterületet és a kiszolgáló nélküli SQL-készlet végpontját. A munkaterület létrehozása során kiépített Storage-fiókok és-végpontok ugyanabban a régióban találhatók.

Az optimális teljesítmény érdekében, ha a kiszolgáló nélküli SQL-készlettel rendelkező más Storage-fiókokhoz fér hozzá, győződjön meg róla, hogy ugyanabban a régióban vannak. Ha nem ugyanabban a régióban találhatók, az adatok hálózati átvitele nagyobb késéssel jár a távoli régió és a végpont régiója között.

### <a name="azure-storage-throttling"></a>Azure Storage-szabályozás

Előfordulhat, hogy több alkalmazás és szolgáltatás fér hozzá a Storage-fiókhoz. A tárolás szabályozása akkor történik meg, ha az alkalmazások, szolgáltatások és kiszolgáló nélküli SQL-készlet számítási feladatainak által generált kombinált IOPS vagy átviteli sebesség meghaladja a Storage-fiók korlátait. Ennek eredményeképpen jelentős negatív hatást tapasztal a lekérdezési teljesítményre.

A szabályozás észlelése esetén a kiszolgáló nélküli SQL-készlet beépített kezeléssel rendelkezik a megoldásához. A kiszolgáló nélküli SQL-készlet lassabb ütemben kéri a tárolást, amíg a szabályozás meg nem oldódik.

> [!TIP]
> Az optimális lekérdezés-végrehajtás érdekében a lekérdezés végrehajtása során ne hangsúlyozzák a Storage-fiókot más munkaterhelésekkel.

### <a name="azure-ad-pass-through-performance"></a>Azure AD-alapú átmenő teljesítmény

A kiszolgáló nélküli SQL-készlet lehetővé teszi a tárolóban lévő fájlok elérését Azure Active Directory (Azure AD) áteresztő vagy SAS hitelesítő adatok használatával. Előfordulhat, hogy lassabb teljesítményt tapasztal az Azure AD-n keresztül, mint az SAS.

Ha jobb teljesítményre van szüksége, próbálja meg SAS hitelesítő adatok használatával hozzáférni a tárolóhoz.

### <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezéshez

Ha lehetséges, készíthet fájlokat a jobb teljesítmény érdekében:

- A nagyméretű CSV és a JSON konvertálása a parkettára. A parketta oszlopos formátumú. Mivel tömörítve van, a fájlméretük kisebb, mint a CSV-vagy JSON-fájlok, amelyek ugyanazokat az adatmennyiségeket tartalmazzák. A kiszolgáló nélküli SQL-készlet képes kihagyni a lekérdezésben nem szükséges oszlopokat és sorokat, ha a parketta-fájlokat olvas. A kiszolgáló nélküli SQL-készletnek kevesebb időt és kevesebb tárolási kérést kell elolvasnia.
- Ha egy lekérdezés egyetlen nagyméretű fájlt céloz meg, akkor a több kisebb fájlra is kihasználhatja.
- Próbálja megtartani a CSV-fájl méretét 100 MB és 10 GB között.
- Jobb, ha azonos méretű fájlokat szeretne egy OPENROWSET elérési úthoz vagy egy külső tábla HELYéhez.
- Particionálja az adatait úgy, hogy a partíciókat különböző mappákba vagy fájlnevekre tárolja. Lásd: [fájlnév és filepath függvények használata adott partíciók célzásához](#use-filename-and-filepath-functions-to-target-specific-partitions).

## <a name="csv-optimizations"></a>CSV-optimalizálások

### <a name="use-parser_version-20-to-query-csv-files"></a>CSV-fájlok lekérdezése PARSER_VERSION 2,0 használatával

A CSV-fájlok lekérdezése teljesítményre optimalizált elemző használatával végezhető el. Részletekért lásd: [PARSER_VERSION](develop-openrowset.md).

### <a name="manually-create-statistics-for-csv-files"></a>CSV-fájlok statisztikáinak manuális létrehozása

A kiszolgáló nélküli SQL-készlet statisztikái támaszkodik az optimális lekérdezés-végrehajtási tervek létrehozásához. A statisztikák automatikusan létrejönnek a Parquet-fájlok oszlopaihoz, ha szükséges. Jelenleg nem jön létre automatikusan statisztikai adatok a CSV-fájlokban lévő oszlopokhoz, és a lekérdezésekben használt oszlopok esetében manuálisan kell létrehoznia a statisztikát, különösen a DISTINCT, a JOIN, a WHERE, a ORDER BY és a GROUP BY utasításban. A részletekért olvassa [el a kiszolgáló nélküli SQL-készlet statisztikáit](develop-tables-statistics.md#statistics-in-serverless-sql-pool) .


## <a name="data-types"></a>Adattípusok

### <a name="use-appropriate-data-types"></a>Megfelelő adattípusok használata

A lekérdezésben használt adattípusok hatással vannak a teljesítményre. Ha követi a következő irányelveket, jobb teljesítményt érhet el: 

- Használja a legkisebb adatméretet, amely a lehető legnagyobb értéket fogja kielégíteni.
  - Ha a karakteres érték legfeljebb 30 karakter hosszúságú, akkor a karakter adattípusa 30.
  - Ha az összes karakteres oszlop értéke rögzített méretű, használja a **char** vagy a **NCHAR**. Ellenkező esetben használja a **varchar** vagy a **nvarchar**.
  - Ha a maximális egész oszlop értéke 500, használja a **smallint** , mert ez a legkisebb adattípus, amely képes erre az értékre. [Ebben a cikkben](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql?view=azure-sqldw-latest&preserve-view=true)az egész adattípus-tartományokat is megtalálhatja.
- Ha lehetséges, használja a **varchar** és a **char** helyett a **nvarchar** és a **NCHAR** értéket.
- Ha lehetséges, használja az egész szám alapú adattípust. A RENDEZÉSi, ILLESZTÉSi és csoportosítási műveletek a karakteres adatoknál gyorsabban, egész számokon vannak végrehajtva.
- Ha séma-következtetést használ, ellenőrizze a [késleltetett adattípusokat](#check-inferred-data-types).

### <a name="check-inferred-data-types"></a>Késleltetett adattípusok keresése

A [séma-következtetések](query-parquet-files.md#automatic-schema-inference) segítségével gyorsan írhat lekérdezéseket, és megvizsgálhatja az adatfájl-sémák ismerete nélkül. Ennek a kényelemnek a díja, hogy a következtetett adattípusok nagyobbak lehetnek, mint a tényleges adattípusok. Ez akkor fordulhat elő, ha nincs elegendő információ a forrásfájlok számára a megfelelő adattípus használata érdekében. A Parquet-fájlok például nem tartalmaznak metaadatokat a karakteres oszlopok maximális hosszával kapcsolatban. Így a kiszolgáló nélküli SQL-készlet varchar (8000)-ként következtet rá.

A lekérdezés eredményül kapott adattípusait a [sp_describe_first_results_set](/sql/relational-databases/system-stored-procedures/sp-describe-first-result-set-transact-sql?view=sql-server-ver15&preserve-view=true) használatával is megtekintheti.

Az alábbi példa bemutatja, hogyan optimalizálhatja a késleltetett adattípusokat. Ez az eljárás a késleltetett adattípusok megjelenítésére szolgál: 
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

Itt látható az eredményhalmaz:

|is_hidden|column_ordinal|name|system_type_name|max_length|
|----------------|---------------------|----------|--------------------|-------------------||
|0|1|vendor_id|varchar (8000)|8000|
|0|2|pickup_datetime|datetime2 (7)|8|
|0|3|passenger_count|int|4|

Ha ismeri a lekérdezés késleltetett adattípusait, megadhatja a megfelelő adattípusokat:

```sql  
SELECT
    vendor_id, pickup_datetime, passenger_count
FROM 
    OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/taxi/*/*/*',
        FORMAT='PARQUET'
    ) 
    WITH (
        vendor_id varchar(4), -- we used length of 4 instead of the inferred 8000
        pickup_datetime datetime2,
        passenger_count int
    ) AS nyc;
```

## <a name="filter-optimization"></a>Szűrő optimalizálása

### <a name="push-wildcards-to-lower-levels-in-the-path"></a>Helyettesítő karakterek leküldése az elérési út alacsonyabb szintjeire

Az elérési úton helyettesítő karaktereket használhat [több fájl és mappa lekérdezéséhez](query-data-storage.md#query-multiple-files-or-folders). A kiszolgáló nélküli SQL-készlet felsorolja a Storage-fiókban lévő fájlokat, az elsőtől kezdve a Storage API használatával. Kiküszöböli a megadott elérési úttal nem egyező fájlokat. A fájlok kezdeti listájának csökkentése növelheti a teljesítményt, ha sok olyan fájl található, amely megfelel a megadott elérési útnak az első helyettesítő karakternek.

### <a name="use-filename-and-filepath-functions-to-target-specific-partitions"></a>Fájlnevek és filepath függvények használata adott partíciók célzásához

Az adathalmazok gyakran partíciókban vannak rendszerezve. A kiszolgáló nélküli SQL-készletet utasíthatja arra, hogy adott mappákat és fájlokat Kérdezzen le. Ez csökkenti a fájlok számát és a lekérdezés által beolvasott és feldolgozandó adatmennyiséget. A hozzáadott bónusz az, hogy jobb teljesítményt érhet el.

További információért olvassa el a [filename](query-data-storage.md#filename-function) és a [filepath](query-data-storage.md#filepath-function) függvényt, és tekintse meg az [adott fájlok lekérdezésének](query-specific-files.md)példáit.

> [!TIP]
> A filepath és a filename függvények eredményét mindig a megfelelő adattípusokra konvertálja. Ha karakteres adattípusokat használ, ügyeljen arra, hogy a megfelelő hosszúságot használja.

> [!NOTE]
> A partíció-eltávolításhoz, a filepath és a filenamehez használt függvények jelenleg nem támogatottak a külső táblák esetében, az Apache Spark for Azure szinapszis Analytics szolgáltatásban létrehozott minden egyes tábla esetében automatikusan létrehozva.

Ha a tárolt adatai nincsenek particionálva, érdemes particionálni. Ezen függvények használatával optimalizálhatja azokat a lekérdezéseket, amelyek a fájlokat célozzák meg. Ha [lekérdezi az Azure szinapszis-táblák particionált Apache Sparkét](develop-storage-files-spark-tables.md) a kiszolgáló nélküli SQL-készletből, a lekérdezés automatikusan csak a szükséges fájlokat fogja megcélozni.

### <a name="use-proper-collation-to-utilize-predicate-pushdown-for-character-columns"></a>A megfelelő rendezés használata a predikátumok pushdown a karakteres oszlopokhoz

A parketta-fájlban lévő adatsorok sorba vannak rendezve. A kiszolgáló nélküli SQL-készlet kihagyja a sorcsoport-csoportokat a WHERE záradékban megadott predikátum alapján, így csökkenti az IO-t, ami nagyobb lekérdezési teljesítményt eredményez. 

Vegye figyelembe, hogy a parketta-fájlokban szereplő pushdown predikátuma csak Latin1_General_100_BIN2_UTF8 rendezés esetén támogatott. Adott oszlop rendezését megadhatja a WITH záradék használatával. Ha ezt a rendezést a WITH záradék használatával nem adhatja meg, a rendszer az adatbázis rendezését fogja használni.

## <a name="optimize-repeating-queries"></a>Ismétlődő lekérdezések optimalizálása

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezések teljesítményének és illesztésének növeléséhez

A [CETAS](develop-tables-cetas.md) a kiszolgáló nélküli SQL-készlet legfontosabb funkcióinak egyike. A CETAS egy párhuzamos művelet, amely létrehozza a külső tábla metaadatait, és exportálja a SELECT lekérdezési eredményeket a Storage-fiókban lévő fájlok készletére.

A CETAS segítségével megadhatja a lekérdezések gyakran használt részeit, például az összekapcsolt hivatkozási táblázatokat egy új fájlhoz. Ezután ehhez az egyetlen külső táblához csatlakozhat, és nem kell ismétlődő közös illesztéseket használnia több lekérdezésben.

Ahogy a CETAS a parketta-fájlokat hozza létre, a rendszer automatikusan létrehozza a statisztikát, amikor az első lekérdezés erre a külső táblára irányul, ami jobb teljesítményt eredményez a CETAS-vel létrehozott táblázat későbbi lekérdezésekhez.

## <a name="next-steps"></a>Következő lépések

Tekintse át a gyakori problémák megoldására vonatkozó [hibaelhárítási](resources-self-help-sql-on-demand.md) cikket. Ha kiszolgáló nélküli SQL-készlet helyett dedikált SQL-készlettel dolgozik, tekintse meg az ajánlott [eljárásokat a DEDIKÁLT SQL](best-practices-dedicated-sql-pool.md) -készletekhez az adott útmutatáshoz.
