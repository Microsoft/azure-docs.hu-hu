---
title: Apache Spark 2,1-es vagy 2,2-os számítási feladatok migrálása 2,3 vagy 2,4-Azure HDInsight
description: Megtudhatja, hogyan telepíthet át Apache Spark 2,1-es és 2,2-es verziókat 2,3-ra vagy 2,4-
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944745"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Apache Spark 2,1-es és 2,2-es számítási feladatok migrálása 2,3-ra és 2,4-re

Ez a dokumentum ismerteti, hogyan telepíthet át Apache Spark munkaterheléseket a Spark 2,1-es és 2,2-es verzióján 2,3-ra vagy 2,4-re

A [kibocsátási megjegyzésekben](../hdinsight-release-notes.md#upcoming-changes)a 2020. július 1-től kezdődően a következő fürtkonfiguráció nem támogatott, és az ügyfelek nem fognak tudni új fürtöket létrehozni ezekkel a konfigurációkkal:
 - Spark 2,1 és 2,2 egy HDInsight 3,6 Spark-fürtben
 - Spark 2,3 egy HDInsight 4,0 Spark-fürtben

A konfigurációkban lévő meglévő fürtök a Microsoft támogatásának hiányában lesznek futtatva. Ha a Spark 2,1-es vagy 2,2-es HDInsight 3,6-on található, a HDInsight 3,6-es Spark 2,3-re váltson, hogy elkerülje a lehetséges rendszer/támogatás megszakadását. Ha a Spark 2,3-es HDInsight 4,0-fürtön van, váltson a Spark 2,4-ra HDInsight 30 2020 4,0-re, hogy elkerülje a lehetséges rendszer/támogatás megszakadását.

A HDInsight-fürtök 3,6-4,0-re való áttelepítésével kapcsolatos általános információkért lásd: a [HDInsight-fürt áttelepítése újabb verzióra](../hdinsight-upgrade-cluster.md). Az Apache Spark újabb verziójára való áttelepítéssel kapcsolatos általános információkért lásd [: Apache Spark: verziószámozási házirend](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Útmutató a Spark verziófrissítésének frissítéséhez a HDInsight-on

| Frissítési forgatókönyv | Mechanizmus | Megfontolandó dolgok | Spark/Kas-integráció |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3,6 Spark 2,1 a HDInsight 3,6 Spark 2,3-be| Fürtök újbóli létrehozása a HDInsight Spark 2,3 | Tekintse át a következő cikkeket: <br> [Apache Spark: a Spark SQL 2,2-ról 2,3-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: a Spark SQL 2,1-ról 2,2-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Nincs változás |
|HDInsight 3,6 Spark 2,2 a HDInsight 3,6 Spark 2,3-be | Fürtök újbóli létrehozása a HDInsight Spark 2,3 | Tekintse át a következő cikkeket: <br> [Apache Spark: a Spark SQL 2,2-ról 2,3-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Nincs változás |
| HDInsight 3,6 Spark 2,1 a HDInsight 4,0 Spark 2,4-be | Fürtök újbóli létrehozása a HDInsight 4,0 Spark 2,4-mel | Tekintse át a következő cikkeket: <br> [Apache Spark: a Spark SQL 2,3-ról 2,4-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: a Spark SQL 2,2-ról 2,3-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: a Spark SQL 2,1-ról 2,2-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | A Spark és a kaptár integrációja megváltozott a HDInsight 4,0-ben. <br><br> A HDInsight 4,0-ben a Spark és a kaptár független katalógusokat használ a SparkSQL-vagy kaptár-táblák eléréséhez. A Spark-katalógusban a Spark által létrehozott tábla él. A kaptárak által létrehozott tábla a kaptár-katalógusban él. Ez a viselkedés eltér a HDInsight 3,6-nél, ahol a kaptár és a Spark közös katalógusa található. A HDInsight 4,0 struktúra és Spark integrációja a kaptár Warehouse-összekötőre (ÜZEMELTETHETŐ WEBMAG) támaszkodik. A ÜZEMELTETHETŐ WEBMAG a Spark és a kaptár közötti hídként működik. Tudnivalók a kaptár-tárház összekötőről. <br> A HDInsight 4,0-ben, ha meg szeretné osztani a kaptár és a Spark közötti metaadattár, ehhez módosítsa a metaadattár. Catalog. default tulajdonságot a Spark-fürtben található struktúrára. Ezt a tulajdonságot a Ambari Advanced spark2-kaptár-site-felülbírálásban találja. Fontos tisztában lenni azzal, hogy a metaadattár megosztása csak a külső struktúra táblái esetében működik, ez nem fog működni, ha belső/felügyelt struktúra-táblákat vagy savas táblákat tartalmaz. <br><br>További információért olvassa el az [Azure HDInsight 3,6 kaptár számítási feladatait a HDInsight 4,0-](../interactive-query/apache-hive-migrate-workloads.md) re című témakört.<br><br> |
| HDInsight 3,6 Spark 2,2 a HDInsight 4,0 Spark 2,4-be | Fürtök újbóli létrehozása a HDInsight 4,0 Spark 2,4-mel | Tekintse át a következő cikkeket: <br> [Apache Spark: a Spark SQL 2,3-ról 2,4-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: a Spark SQL 2,2-ról 2,3-re való frissítés](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | A Spark és a kaptár integrációja megváltozott a HDInsight 4,0-ben. <br><br> A HDInsight 4,0-ben a Spark és a kaptár független katalógusokat használ a SparkSQL-vagy kaptár-táblák eléréséhez. A Spark-katalógusban a Spark által létrehozott tábla él. A kaptárak által létrehozott tábla a kaptár-katalógusban él. Ez a viselkedés eltér a HDInsight 3,6-nél, ahol a kaptár és a Spark közös katalógusa található. A HDInsight 4,0 struktúra és Spark integrációja a kaptár Warehouse-összekötőre (ÜZEMELTETHETŐ WEBMAG) támaszkodik. A ÜZEMELTETHETŐ WEBMAG a Spark és a kaptár közötti hídként működik. Tudnivalók a kaptár-tárház összekötőről. <br> A HDInsight 4,0-ben, ha meg szeretné osztani a kaptár és a Spark közötti metaadattár, ehhez módosítsa a metaadattár. Catalog. default tulajdonságot a Spark-fürtben található struktúrára. Ezt a tulajdonságot a Ambari Advanced spark2-kaptár-site-felülbírálásban találja. Fontos tisztában lenni azzal, hogy a metaadattár megosztása csak a külső struktúra táblái esetében működik, ez nem fog működni, ha belső/felügyelt struktúra-táblákat vagy savas táblákat tartalmaz. <br><br>További információért olvassa el az [Azure HDInsight 3,6 kaptár számítási feladatait a HDInsight 4,0-](../interactive-query/apache-hive-migrate-workloads.md) re című témakört.|

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürt migrálása egy újabb verzióra](../hdinsight-upgrade-cluster.md)
* [Az Azure HDInsight 3,6 kaptár számítási feladatait áttelepítheti HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md)
