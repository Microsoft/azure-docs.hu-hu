---
title: Azure Cosmos DB használata az Azure szinapszis-kapcsolaton keresztüli Apache Spark használatával
description: A Azure Cosmos DB használata az Azure szinapszis-beli Apache Spark használatával
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 4a8367ea41ea96d8a412af965346684737d190fe
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627574"
---
# <a name="interact-with-azure-cosmos-db-using-apache-spark-in-azure-synapse-link"></a>Azure Cosmos DB használata az Azure szinapszis-kapcsolaton keresztüli Apache Spark használatával

Ebből a cikkből megtudhatja, hogyan kommunikálhat a Azure Cosmos DB a szinapszis Apache Spark használatával. A Scala, a Python, a SparkSQL és a C# teljes körű támogatásával a szinapszis Apache Spark az Azure-beli [szinapszis-hivatkozáson](../../cosmos-db/synapse-link.md)alapuló adatelemzési, adattervezési, adatelemzési és adatelemzési forgatókönyvek (Azure Cosmos db) központi része.

A következő képességek támogatottak a Azure Cosmos DB való interakció közben:
* A szinapszis Apache Spark lehetővé teszi az olyan Azure Cosmos DB-tárolókban lévő adatok elemzését, amelyek az Azure szinapszis hivatkozásával közel valós időben engedélyezve vannak, anélkül, hogy ez hatással lenne a tranzakciós számítási feladatok teljesítményére. A következő két lehetőség érhető el a Spark Azure Cosmos DB [analitikai tárolójának](../../cosmos-db/analytical-store-introduction.md) lekérdezéséhez:
    + Betöltés a Spark DataFrame
    + Spark-tábla létrehozása
* A szinapszis Apache Spark lehetővé teszi az adatAzure Cosmos DBba való betöltését is. Fontos megjegyezni, hogy az adatmennyiséget a tranzakciós tárolón keresztül mindig Azure Cosmos DB tárolóba kell bevenni. Ha a szinapszis-hivatkozás engedélyezve van, az új beszúrások, frissítések és törlések automatikusan szinkronizálva lesznek az analitikai tárolóba.
* A szinapszis Apache Spark a Spark strukturált streamet is támogatja Azure Cosmos DB forrásként és fogadóként is. 

A következő szakasz végigvezeti a fenti képességek szintaxisán. Az Azure szinapszis Analytics-munkaterületen található kézmozdulatok úgy lettek kialakítva, hogy könnyen használható felhasználói élményt nyújtsanak az első lépésekhez. A kézmozdulatok akkor láthatók, ha a jobb gombbal rákattint egy Azure Cosmos DB tárolóra a szinapszis munkaterület **adatlapján.** A kézmozdulatokkal gyorsan hozhat létre kódot, és testre szabhatja azt az igényei szerint. A kézmozdulatok az adatok egy kattintással való felderítéséhez is ideálisan használhatók.

> [!IMPORTANT]
> Vegye figyelembe az analitikai séma bizonyos korlátozásait, ami az betöltési műveletek váratlan viselkedését eredményezheti.
> Például csak a tranzakciós séma első 1000 tulajdonsága érhető el az analitikai sémában, a szóközökkel rendelkező tulajdonságok nem érhetők el stb. Ha nem várt eredményeket tapasztal, további részletekért tekintse meg az [analitikai tároló sémájának korlátozásait](../../cosmos-db/analytical-store-introduction.md#schema-constraints) .

## <a name="query-azure-cosmos-db-analytical-store"></a>Lekérdezés Azure Cosmos DB analitikus tárolóban

Mielőtt megtudhatja, hogyan lehet lekérdezni Azure Cosmos DB analitikus áruházat, betöltheti a Spark DataFrame, és létrehozza a Spark Table-et, érdemes megvizsgálnia a különböző élményekben rejlő lehetőségeket, így kiválaszthatja az igényeinek megfelelő lehetőséget.

A tapasztalatok közötti különbség az, hogy az Azure Cosmos DB tárolóban lévő alapul szolgáló adatváltozások automatikusan megjelenjenek-e a Sparkban végzett elemzés során. Ha egy Spark-DataFrame regisztrálva van, vagy egy Spark-tábla jön létre egy tároló analitikus tárolóján, a rendszer az elemzési tárolóban lévő adatok aktuális pillanatképét a Sparkba olvassa be a további elemzések hatékony pushdown. Fontos megjegyezni, hogy mivel a Spark egy lusta kiértékelési szabályzatot követ, kivéve, ha egy műveletet a Spark-DataFrame vagy egy SparkSQL-lekérdezést hajt végre a Spark-táblán, a tényleges adatok nem kerülnek beolvasásra a mögöttes tároló analitikus tárolójából.

A **Spark DataFrame-be való betöltéskor** a beolvasott metaadatok a Spark-munkamenet teljes élettartama alatt gyorsítótárazva maradnak, így a DataFrame-en meghívott további műveletek kiértékelése a DataFrame létrehozásakor az elemzési tárba került pillanatkép alapján történik.

Ezzel szemben a **Spark-táblák létrehozásakor** a rendszer nem gyorsítótárazza a Sparkban az elemzési tár állapotának metaadatait, hanem újra betölti őket a Spark-táblán végrehajtott összes SparkSQL-lekérdezés végrehajtásakor.

Ezért választhat a Spark DataFrame betöltése és a Spark-táblázat létrehozása között aszerint, hogy a Spark-elemzést az elemzési tár rögzített pillanatképével vagy az elemzési tár legújabb pillanatképével összehasonlítva szeretné elvégezni.

> [!NOTE]
> A Mongo DB-fiókok Azure Cosmos DB API- [ját az analitikai](../../cosmos-db/analytical-store-introduction.md#analytical-schema) tárolóban és a használni kívánt bővített tulajdonságok neveiben tekintheti meg.

### <a name="load-to-spark-dataframe"></a>Betöltés a Spark DataFrame

Ebben a példában egy Spark-DataFrame fog létrehozni, amely a Azure Cosmos DB analitikus tárolóra mutat. Ezt követően további elemzéseket végezhet a Spark-műveletek a DataFrame való meghívásával. Ez a művelet nem érinti a tranzakciós tárolót.

A **Python** szintaxisa a következő:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .load()
```

A **Scala** egyenértékű szintaxisa a következő lesz:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    load()
```

### <a name="create-spark-table"></a>Spark-tábla létrehozása

Ebben a példában egy Spark-táblázatot hozunk létre, amely a Azure Cosmos DB analitikus tárolót mutat. Ezt követően további elemzéseket végezhet, ha SparkSQL-lekérdezéseket küld a táblára. Ez a művelet nem befolyásolja a tranzakciós tárolót, és semmilyen adatáthelyezést sem jelent. Ha úgy dönt, hogy törli ezt a Spark-táblázatot, a mögöttes Azure Cosmos DB tárolót és a hozzá tartozó analitikus tárolót nem érinti a rendszer. 

Ez a forgatókönyv alkalmas a Spark-táblázatok harmadik féltől származó eszközökön keresztüli újrafelhasználására, és lehetővé teszi az alapul szolgáló adat elérését a Futtatás ideje alatt.

A Spark-tábla létrehozásának szintaxisa a következő:
```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService '<enter linked service name>',
    spark.cosmos.container '<enter container name>'
)
```

> [!NOTE]
> Olyan forgatókönyvek esetében, amikor az alapul szolgáló Azure Cosmos DB-tároló tároló sémája idővel megváltozik, és ha szeretné, hogy a séma változásai automatikusan megjelenjenek a Spark-tábla lekérdezéseiben, a Spark-tábla beállításaiban állítsa `true` értékre a `spark.cosmos.autoSchemaMerge` beállítást.


## <a name="write-spark-dataframe-to-azure-cosmos-db-container"></a>Spark-DataFrame írása Azure Cosmos DB tárolóba

Ebben a példában egy Spark-DataFrame fog írni egy Azure Cosmos DB tárolóba. Ez a művelet hatással lesz a tranzakciós munkaterhelések teljesítményére és a Azure Cosmos DB tárolón vagy a megosztott adatbázison kiépített igénylési egységek felhasználására.

A **Python** szintaxisa a következő:
```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

A **Scala** egyenértékű szintaxisa a következő lesz:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Adatfolyam-DataFrame betöltése a tárolóból
Ebben a kézmozdulatban a Spark streaming funkciót fogja használni az adatok egy tárolóból egy dataframe való betöltéséhez. Az adattárolást a rendszer a munkaterülethez csatlakoztatott elsődleges adatlake-fiókban (és fájlrendszerben) tárolja. 
> [!NOTE]
> Ha a szinapszis Apache Sparkban található külső könyvtárakra hivatkozik, további információt [itt talál](#external-library-management). Ha például a Mongo DB-hez készült Cosmos DB API-t tartalmazó Spark-DataFrame szeretne bevenni, akkor a Spark Mongo DB-összekötőjét [itt](https://docs.mongodb.com/spark-connector/master/)is kihasználhatja.

## <a name="load-streaming-dataframe-from-azure-cosmos-db-container"></a>Adatfolyam-DataFrame betöltése Azure Cosmos DB tárolóból
Ebben a példában a Spark strukturált adatfolyam-továbbítási funkcióját fogja használni a Azure Cosmos DB tárolóból származó adatok egy Spark streaming-DataFrame való betöltéséhez a Azure Cosmos DB a hírcsatornák módosítása funkciójának használatával. A Spark által használt ellenőrzőpont-információ a munkaterülethez csatlakoztatott elsődleges adatlake-fiókban (és fájlrendszerben) lesz tárolva.

Ha a */localReadCheckpointFolder* mappa nincs létrehozva (az alábbi példában), a rendszer automatikusan létrehozza. Ez a művelet hatással lesz a tranzakciós számítási feladatok teljesítményére és a Azure Cosmos DB tárolón vagy megosztott adatbázison kiépített igénylési egységek felhasználására.

A **Python** szintaxisa a következő:
```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<enter linked service name>")\
    .option("spark.cosmos.container", "<enter container name>")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

A **Scala** egyenértékű szintaxisa a következő lesz:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "<enter linked service name>").
    option("spark.cosmos.container", "<enter container name>").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamQuery").
    load()
```

## <a name="write-streaming-dataframe-to-azure-cosmos-db-container"></a>Adatfolyam-DataFrame írása Azure Cosmos DB tárolóba
Ebben a példában egy streaming DataFrame fog írni egy Azure Cosmos DB tárolóba. Ez a művelet hatással lesz a tranzakciós számítási feladatok teljesítményére és a Azure Cosmos DB tárolón vagy megosztott adatbázison kiépített igénylési egységek felhasználására. Ha a */localWriteCheckpointFolder* mappa nincs létrehozva (az alábbi példában), a rendszer automatikusan létrehozza. 

A **Python** szintaxisa a következő:

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

# If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "<enter linked service name>")\
        .option("spark.cosmos.container", "<enter container name>")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

A **Scala** egyenértékű szintaxisa a következő lesz:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

// If you are using managed private endpoints for Azure Cosmos DB analytical store and using batch writes/reads and/or streaming writes/reads to transactional store you should set connectionMode to Gateway. 

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "<enter linked service name>").
            option("spark.cosmos.container", "<enter container name>").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```

## <a name="external-library-management"></a>Külső kódtár kezelése

Ebből a példából megtudhatja, hogyan hivatkozhat a külső kódtárak a JAR-fájlokból a Synpase Apache Spark-munkaterületeken Spark notebookok használatakor. A JAR-fájlokat elhelyezheti a munkaterülethez csatlakoztatott elsődleges adatközpont-fiókban lévő tárolóban, majd hozzáadhatja a következő `%configure` utasítást a Spark Notebookban:

```cmd
%%configure -f
{
    "jars": [
        "abfss://<storage container name>@<data lake account name>.dfs.core.windows.net/<path to jar>"
    ]
}
```
Ha távoli Spark-feladatokra vonatkozó definíciókat szeretne elküldeni egy kiszolgáló nélküli Apache Spark készletbe, megtudhatja, hogyan hivatkozhat a külső könyvtárakra az [oktatóanyag](../spark/apache-spark-job-definitions.md)követésével.

## <a name="next-steps"></a>Következő lépések

* [Minták az Azure szinapszis hivatkozásának megkezdéséhez a GitHubon](https://aka.ms/cosmosdb-synapselink-samples)
* [Ismerje meg, mi támogatott az Azure szinapszis-hivatkozás Azure Cosmos DB](./concept-synapse-link-cosmos-db-support.md)
* [Csatlakozás a szinapszis hivatkozásához Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
