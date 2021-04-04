---
title: A kaptár-táblákban lévő adatelemzés a kaptár-lekérdezésekkel – csoportos adatelemzési folyamat
description: A HDInsight Hadoop-fürtben található kaptár-táblákban lévő adatelemzéshez használt minta-struktúra-parancsfájlok használata.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7234a8c45c20c64dddb43a52a099aa92f2d297d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321110"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>A Hive-táblákban tárolt adatok megismerése Hive-lekérdezésekkel

Ez a cikk a HDInsight Hadoop-fürtben található kaptár-táblákban lévő adatvizsgálathoz használható minta-struktúra-parancsfájlokat tartalmazza.

Ez a feladat a [csoportos adatelemzési folyamat](overview.md)egyik lépése.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik a következővel:

* Létrehozott egy Azure Storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* Testreszabott Hadoop-fürt kiépítve a HDInsight szolgáltatással. Ha útmutatásra van szüksége, tekintse meg [a Azure HDInsight Hadoop-fürtök testreszabása speciális elemzésekhez](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)című témakört.
* Az adatfeltöltés Azure HDInsight Hadoop fürtök struktúrájának tábláiba történt. Ha még nem tette meg, kövesse az [adatok létrehozása és betöltése a kaptár tábláiba](move-hive-tables.md) című részben található utasításokat az adatok a kaptár tábláiba való feltöltéséhez.
* Engedélyezve van a távoli hozzáférés a fürthöz. Ha útmutatásra van szüksége, tekintse meg [a Hadoop-fürt fő csomópontjának elérését](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)ismertető témakört.
* Ha a kaptár-lekérdezések elküldésére vonatkozó útmutatásra van szüksége, olvassa el a [kaptár-lekérdezések beküldése](move-hive-tables.md#submit) című témakört.

## <a name="example-hive-query-scripts-for-data-exploration"></a>Példa a kaptár lekérdezési parancsfájljaira az adatfeltáráshoz
1. A megfigyelések számának lekérése egy partíción  `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. A napi megfigyelések számának lekérése  `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. Szintek beolvasása egy kategorikus oszlopban  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Szintek számának lekérése két kategorikus oszlop kombinációjában  `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. Numerikus oszlopok eloszlásának beolvasása  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Rekordok kinyerése két táblázatból
   
    ```hiveql
    SELECT
        a.<common_columnname1> as <new_name1>,
        a.<common_columnname2> as <new_name2>,
        a.<a_column_name1> as <new_name3>,
        a.<a_column_name2> as <new_name4>,
        b.<b_column_name1> as <new_name5>,
        b.<b_column_name2> as <new_name6>
    FROM
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <a_column_name1>,
            <a_column_name2>,
        FROM <databasename>.<tablename1>
        ) a
        join
        (
        SELECT <common_columnname1>,
            <common_columnname2>,
            <b_column_name1>,
            <b_column_name2>,
        FROM <databasename>.<tablename2>
        ) b
        ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>
    ```

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>További lekérdezési parancsfájlok a taxi Trip adatforgatókönyvekhez
A [New York](https://chriswhong.com/open-data/foil_nyc_taxi/) -i taxi-adatforgatókönyvekre jellemző lekérdezések a [GitHub-tárházban](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts)is elérhetők. Ezeknek a lekérdezéseknek már van megadott adatsémája, és készen állnak a futtatásra.