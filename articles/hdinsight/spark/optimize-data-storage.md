---
title: Az adattároló optimalizálása Apache Spark-Azure-HDInsight
description: Megtudhatja, hogyan optimalizálhatja az adattárolást az Azure HDInsight Apache Spark való használatra.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: 10f99bdc4a5d418ae1b432a6799c5979e473c5ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98926986"
---
# <a name="data-storage-optimization-for-apache-spark"></a>Apache Spark adattárolási optimalizálása

Ez a cikk azokat a stratégiákat ismerteti, amelyekkel optimalizálhatja az adattárolást az Azure HDInsight hatékony Apache Spark feladatok végrehajtásához.

## <a name="overview"></a>Áttekintés

A Spark számos formátumot támogat, például a CSV-t, a JSON-t, az XML-t, a parketta, az ork és a Avro. A Spark bővíthető a külső adatforrásokkal rendelkező több formátum támogatásához – további információ: [Apache Spark csomagok](https://spark-packages.org).

A teljesítmény legjobb formátuma a Parquet és a *Snappy Compression*, amely az alapértelmezett a Spark 2. x verzióban. A Parquet oszlopos formátumban tárolja az adatok, és a Spark-ban is nagyon optimalizált.

## <a name="choose-data-abstraction"></a>Adatabsztrakció kiválasztása

A korábbi Spark-verziók a RDD és az absztrakt adatokat, a Spark 1,3-es és a 1,6-es verzióját használták a DataFrames és adatkészletek használatára. Vegye figyelembe a következő relatív Érdemeiket:

* **Adatkeretek**
    * Legjobb választás a legtöbb esetben.
    * Lekérdezési optimalizálást biztosít a katalizátoron keresztül.
    * Teljes fázisú programkódok létrehozása.
    * Közvetlen memória-hozzáférés.
    * Alacsony terhelésű gyűjtés (GC) terhelése.
    * Nem fejlesztőként, mint adatkészletek, mivel nincsenek fordítási idejű ellenőrzések vagy tartományi objektumok programozása.
* **Adatkészletek**
    * Olyan összetett ETL-folyamatokban jó, ahol a teljesítményre gyakorolt hatás elfogadható.
    * Nem jó olyan összesítésekben, ahol a teljesítményre gyakorolt hatás jelentős lehet.
    * Lekérdezési optimalizálást biztosít a katalizátoron keresztül.
    * Fejlesztőknek – a tartományi objektumok programozása és a fordítási idő ellenőrzésének biztosításával.
    * Szerializálási/deszerializálási terhelés hozzáadására szolgál.
    * Magas GC-terhelés.
    * A teljes fázisú programkódok létrehozásának megszakítása.
* **RDD-k**
    * Nem kell RDD használnia, hacsak nem kell új egyéni RDD létrehoznia.
    * Nincs lekérdezés optimalizálása a katalizátoron keresztül.
    * Nincs egész fázist generáló kód.
    * Magas GC-terhelés.
    * A Spark 1. x örökölt API-kat kell használnia.

## <a name="select-default-storage"></a>Alapértelmezett tároló kiválasztása

Új Spark-fürt létrehozásakor kiválaszthatja az Azure Blob Storage vagy Azure Data Lake Storaget a fürt alapértelmezett tárolója. Mindkét lehetőség biztosítja a hosszú távú tárolás előnyeit az átmeneti fürtök esetében. Így az adatai nem törlődnek automatikusan, amikor törli a fürtöt. Újra létrehozhat egy átmeneti fürtöt, és továbbra is hozzáférhet az adataihoz.

| Áruház típusa | Fájlrendszer | Sebesség | Átmeneti | Használati esetek |
| --- | --- | --- | --- | --- |
| Azure Blob Storage | **wasb:**//URL/ | **Standard** | Yes | Átmeneti fürt |
| Azure Blob Storage (biztonságos) | **wasbs:**//URL/ | **Standard** | Yes | Átmeneti fürt |
| Azure Data Lake Storage Gen 2| **abfs:**//URL/ | **Gyorsabb** | Yes | Átmeneti fürt |
| Azure Data Lake Storage Gen 1| **ADL:**//URL/ | **Gyorsabb** | Yes | Átmeneti fürt |
| Helyi HDFS | **hdfs:**//URL/ | **Leggyorsabb** | No | Interaktív 24/7-fürt |

A tárolási lehetőségek teljes leírását lásd: [tárolási lehetőségek összehasonlítása az Azure HDInsight-fürtökkel való használathoz](../hdinsight-hadoop-compare-storage-options.md).

## <a name="use-the-cache"></a>Gyorsítótár használata

A Spark saját natív gyorsítótárazási mechanizmusokat biztosít, amelyek különböző módszerekkel használhatók, például:, `.persist()` `.cache()` és `CACHE TABLE` . Ez a natív gyorsítótárazás a kis adathalmazok és az ETL-folyamatok esetében érvényes, ahol a közbenső eredmények gyorsítótárazására van szükség. A Spark natív gyorsítótárazás azonban jelenleg nem működik megfelelően a particionálással, mivel a gyorsítótárazott táblák nem őrzik meg a particionálási adatmennyiséget. Egy általánosabb és megbízható gyorsítótárazási módszer a *tárolási réteg gyorsítótárazása*.

* Natív Spark-gyorsítótárazás (nem ajánlott)
    * Jó kis adatkészletekhez.
    * Nem működik a particionálással, ami megváltozhat a jövőbeli Spark-kiadásokban.

* Tárolási szint gyorsítótárazása (ajánlott)
    * Az [i/o-gyorsítótár](apache-spark-improve-performance-iocache.md) funkciójának használatával valósítható meg a HDInsight.
    * Memóriabeli és SSD-gyorsítótárazást használ.

* Helyi HDFS (ajánlott)
    * `hdfs://mycluster` elérési útja.
    * SSD-gyorsítótárazást használ.
    * A gyorsítótárazott adatvesztés a fürt törlésekor elveszik, a gyorsítótár újraépítését igényli.

## <a name="optimize-data-serialization"></a>Az adatszerializálás optimalizálása

A Spark-feladatok terjesztése megtörténik, ezért a megfelelő adatszerializálás fontos a legjobb teljesítmény érdekében.  A Spark két szerializálási lehetőséggel rendelkezik:

* A Java-szerializálás az alapértelmezett.
* `Kryo` a szerializálás egy újabb formátum, amely gyorsabb és kompakt szerializálást eredményezhet a Javánál.  `Kryo` Ehhez regisztrálnia kell az osztályokat a programban, és még nem támogatja az összes szerializálható típust.

## <a name="use-bucketing"></a>Gyűjtés használata

A gyűjtő hasonló az adatparticionáláshoz. Az egyes gyűjtők azonban nem csupán egy oszlop értékeit tárolhatják. Ez a módszer jól működik, ha nagy (millió vagy több) értéket, például termékazonosítókat kíván particionálni. A gyűjtőt a sor gyűjtő kulcsának kivonatolásával határozzuk meg. A gyűjtő táblák egyedi optimalizációkat biztosítanak, mert metaadatokat tárolnak a gyűjtők és a rendezésük módjával kapcsolatban.

Bizonyos speciális gyűjtő funkciók a következők:

* Lekérdezés optimalizálása a meta-adatok gyűjtője alapján.
* Optimalizált összesítések.
* Optimalizált illesztések.

Egyszerre használhatja a particionálást és a gyűjtőt.

## <a name="next-steps"></a>Következő lépések

* [Az adatfeldolgozás optimalizálása Apache Spark](optimize-cluster-configuration.md)
* [Memória használatának optimalizálása Apache Spark](optimize-memory-usage.md)
* [A fürt konfigurációjának optimalizálása Apache Spark](optimize-cluster-configuration.md)
