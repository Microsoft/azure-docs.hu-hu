---
title: Ismerje meg Apache Spark adatformátumait Azure Data Lake Analytics U-SQL-fejlesztők számára.
description: Ez a cikk Apache Spark fogalmakat ismerteti, amelyek segítenek U_SQL fejlesztőknek megérteni az U-SQL és a Spark adatformátumai közötti különbségeket.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221111"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Az U-SQL és a Spark adatformátumai közötti különbségek ismertetése

Ha [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) vagy [Azure HDInsight Sparkt](../hdinsight/spark/apache-spark-overview.md)szeretne használni, javasoljuk, hogy az adatait [Azure Data Lake Storage Gen1ról](../data-lake-store/data-lake-store-overview.md) [Azure Data Lake Storage Gen2ra](../storage/blobs/data-lake-storage-introduction.md)telepítse át.

A fájlok áthelyezésén kívül a Spark számára elérhető U-SQL-táblákban is el szeretné végezni az adatok tárolását.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Azure Data Lake Storage Gen1 fájlban tárolt adatáthelyezés

A fájlokban tárolt adatfájlok többféleképpen is áthelyezhetők:

- [Azure Data Factory](../data-factory/introduction.md) folyamat írása az adatok [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) -fiókból a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -fiókba való másolásához.
- Írjon be egy Spark-feladatot, amely beolvassa az adatokat a [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) -fiókból, és írja azt a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -fiókba. A használati eset alapján előfordulhat, hogy más formátumban szeretné írni, például a Parquet, ha nem kell megőriznie az eredeti fájlformátumot.

Javasoljuk, hogy tekintse át a [Big Data Analytics-megoldások Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2re való frissítését](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md) ismertető cikket.

## <a name="move-data-stored-in-u-sql-tables"></a>Az U-SQL-táblákban tárolt adatáthelyezés

A Spark nem értelmezi a U-SQL-táblákat. Ha a U-SQL-táblákban tárolt adatokkal rendelkezik, egy U-SQL-feladatot fog futtatni, amely Kinyeri a táblázatos adatok kibontását, és a Spark által értelmezett formátumban menti azt. A legmegfelelőbb formátum a Hive-metaadattár mappa elrendezését követő Parquet-fájlok készletének létrehozása.

A kimenet a U-SQL-ben érhető el, a beépített parketta-előállítók használatával, valamint a dinamikus kimeneti particionálással a fájlokkal a partíciós mappák létrehozásához. [Minden eddiginél több fájlt dolgozhat fel, és a Parquet használatával](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) megtudhatja, hogyan hozhat létre ilyen Spark-adatmennyiséget.

Az átalakítást követően a [Azure Data Lake Storage Gen1 fájlban tárolt adatáthelyezési](#move-data-stored-in-azure-data-lake-storage-gen1-files)fejezetben leírtak szerint másolja az adatfájlokat.

## <a name="caveats"></a>Figyelmeztetések

- Adatszemantika fájlok másolásakor a másolás a bájt szintjén történik. Így ugyanazokat az adatfájlokat kell megadnia a [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) fiókban. Vegye figyelembe azonban, hogy a Spark más karaktereket is értelmez. Előfordulhat például, hogy egy CSV-fájlban eltérő alapértelmezett értéket használ a sorok elválasztója számára.
    Emellett, ha a beírt adatok másolása (táblákból) történik, akkor a parketta és a Spark különböző pontossággal és méretezéssel rendelkezhet néhány beírt értéknél (például lebegőpontos), és a null értékeket különbözőképpen is kezelheti. Például a U-SQL C# szemantikai értékkel rendelkezik a null értékek esetében, míg a Spark három értékű logikát tartalmaz a null értékekhez.

- Az adatszervezet (particionálás) U-SQL-táblák két szintű particionálást biztosítanak. A külső szint ( `PARTITIONED BY` ) értéke és leképezése elsősorban a kaptár/Spark particionálási sémában, a mappa-hierarchiák használatával. Biztosítania kell, hogy a null értékek a megfelelő mappába legyenek leképezve. A `DISTRIBUTED BY` U-SQL belső szintje 4 terjesztési sémát kínál: ciklikus multiplexelés, tartomány, kivonat és közvetlen kivonat.
    A kaptár/Spark táblák csak az U-SQL-nél eltérő kivonatoló függvényt használnak az érték particionálásához vagy a kivonatoló particionáláshoz. Ha a U-SQL-tábla adatait adja meg, valószínűleg csak a Spark érték particionálásához lesz képes leképezni, és a végső Spark-lekérdezésektől függően szükség lehet az adatelrendezés további finomhangolására.

## <a name="next-steps"></a>Következő lépések

- [A Spark Code-fogalmak megismerése U-SQL-fejlesztőknek](understand-spark-code-concepts.md)
- [Frissítse big data Analytics-megoldásait Azure Data Lake Storage Gen1ról Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [.NET az Apache Sparkhoz](/dotnet/spark/what-is-apache-spark-dotnet)
- [Adatátalakítás a Spark-tevékenységgel Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Az adatátalakítás a Hadoop-struktúra tevékenységgel Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Mi az az Azure HDInsight-alapú Apache Spark?](../hdinsight/spark/apache-spark-overview.md)