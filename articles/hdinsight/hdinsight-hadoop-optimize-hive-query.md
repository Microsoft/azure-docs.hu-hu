---
title: Kaptár-lekérdezések optimalizálása az Azure HDInsight
description: Ez a cikk azt ismerteti, hogyan optimalizálhatja Apache Hive lekérdezéseit az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 10/28/2020
ms.openlocfilehash: 551d985ea78e83397e507676c5fd7ecfce12ff7b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864244"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>Apache Hive-lekérdezések optimalizálása az Azure HDInsightban

Ez a cikk a leggyakoribb teljesítmény-optimalizálásokat ismerteti, amelyeket a Apache Hive lekérdezések teljesítményének növelésére használhat.

## <a name="cluster-type-selection"></a>Fürt típusának kiválasztása

Az Azure HDInsight-ban több különböző típusú fürtön is futtathat Apache Hive lekérdezéseket. 

Válassza ki a megfelelő fürtöt a számítási feladatok teljesítményének optimalizálása érdekében:

* Az interaktív **lekérdezési** fürt típusának kiválasztásával optimalizálhatja az `ad hoc` interaktív lekérdezéseket. 
* Válassza az Apache **Hadoop** -fürt típusa lehetőséget a Batch-folyamatként használt kaptár-lekérdezések optimalizálásához. 
* A **Spark** -és **HBase** -fürtök a kaptár-lekérdezéseket is futtathatják, és a számítási feladatok futtatása esetén megfelelőek lehetnek. 

A különböző HDInsight-fürtökön futó kaptár-lekérdezések futtatásával kapcsolatos további információkért lásd: [Mi az a Apache Hive és a HiveQL az Azure HDInsight?](hadoop/hdinsight-use-hive.md)című témakörben.

## <a name="scale-out-worker-nodes"></a>Munkavégző csomópontok vertikális felskálázása

A munkavégző csomópontok számának növelése egy HDInsight-fürtben lehetővé teszi, hogy a munka több leképezést és szűkítőt használjon párhuzamosan. A HDInsight kétféleképpen növelheti a méretezési lehetőségeket:

* Fürt létrehozásakor megadhatja a munkavégző csomópontok számát a Azure Portal, a Azure PowerShell vagy a parancssori felület használatával.  További információ: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). Az alábbi képernyőfelvételen a Azure Portal munkavégző csomópontjának konfigurációja látható:
  
    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png" alt-text="Azure Portal fürt méretének csomópontjai":::

* A létrehozást követően szerkesztheti a munkavégző csomópontok számát úgy, hogy az újbóli létrehozása nélkül is kibővítse a fürtöt:

    :::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png " alt-text="Azure Portal méretezési fürt mérete":::

További információ a HDInsight skálázásáról: HDInsight- [fürtök méretezése](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>Az Apache TEZ használata a Térkép csökkentése helyett

Az [Apache TEZ](https://tez.apache.org/) egy alternatív végrehajtási motor a MapReduce motorhoz. A Linux-alapú HDInsight-fürtök alapértelmezés szerint engedélyezve vannak a TEZ.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png" alt-text="HDInsight Apache TEZ – áttekintési diagram":::

A TEZ az alábbiak miatt gyorsabb:

* **Irányított aciklikus gráf (Dag) végrehajtása egyetlen feladatokként a MapReduce motorban**. A DAG megköveteli, hogy az egyes Mapper-készleteket egy szűkítő-készlet követi. Ez a követelmény több MapReduce-feladat kikapcsolását okozza az egyes struktúra-lekérdezésekhez. A TEZ nem rendelkezik ilyen korlátozással, és feldolgozhatja a komplex DAG-t, mivel egy feladat minimalizálja a feladat indítási terhelését.
* **Elkerüli a szükségtelen írásokat**. Több feladat is feldolgozza ugyanazt a kaptár-lekérdezést a MapReduce motorban. Az egyes MapReduce-feladatok kimenete a köztes adatok HDFS van írva. Mivel a TEZ lekicsinyíti a feladatok számát az egyes struktúra-lekérdezésekhez, el tudja kerülni a szükségtelen írásokat.
* Az **indítási késések csökkentése**. A TEZ jobban képes az indítási késleltetés minimalizálására azáltal, hogy csökkenti az elindításához szükséges leképezések számát, és a teljes optimalizálást is javítja.
* **Tárolók újrafelhasználása**. Amikor lehetséges, a TEZ újra fogja használni a tárolókat, hogy a rendszer csökkenti a tárolók indításának késleltetését.
* **Folyamatos optimalizálási technikák**. A fordítási fázisban hagyományosan optimalizálás történt. A bemenetekkel kapcsolatban azonban további információk érhetők el, amelyek lehetővé teszik a jobb optimalizálást a futtatókörnyezet során. A TEZ folyamatos optimalizálási technikákat használ, amelyek lehetővé teszik, hogy a csomagot a futásidejű fázisba optimalizálja.

További információ ezekről a fogalmakról: [Apache TEZ](https://tez.apache.org/).

A kaptár lekérdezési TEZ a következő SET paranccsal engedélyezheti a lekérdezés előjavításával:

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Struktúra particionálás

Az I/O műveletek a kaptár-lekérdezések futtatásának legnagyobb teljesítménybeli szűk keresztmetszetét jelentik. A teljesítmény javítható, ha az olvasni kívánt adat mennyisége csökkenthető. Alapértelmezés szerint a kaptár lekérdezi a teljes kaptár-táblákat. Az olyan lekérdezéseknél azonban, amelyeknek csak kis mennyiségű adat vizsgálatára van szükségük (például szűréses lekérdezések), ez a viselkedés szükségtelen terhelést okoz. A kaptár particionálása lehetővé teszi, hogy a kaptár-lekérdezések csak a szükséges mennyiségű adattal férhessenek hozzá a kaptárak tábláiban.

A struktúra particionálását úgy valósítja meg, hogy átrendezi a nyers adattárakat új címtárakba. Minden partíció saját könyvtárral rendelkezik. A particionálást a felhasználó határozza meg. Az alábbi ábrán egy struktúra-táblázat particionálását mutatjuk be az oszlop *év* szerint. A rendszer minden évben létrehoz egy új könyvtárat.

:::image type="content" source="./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png" alt-text="HDInsight Apache Hive particionálás":::

Néhány particionálási szempont:

* A **partíciók particionálásának mellőzése** a csak néhány értékkel rendelkező oszlopokban kevés partíciót eredményezhet. Például a nemek szerinti particionálás csak két partíciót hoz létre (hím és nőstény), ezért csökkentse a késést legfeljebb fele.
* **Ne adja át a partíciót** a másik végletnél – a partíció létrehozása egyedi értékkel (például felhasználóazonosító) rendelkező oszlopokban több partíciót eredményez. A több mint partíció a fürt namenode nagy terhelést okoz, mivel a nagy számú könyvtárat kell kezelnie.
* Az **adatdöntés elkerülése** – a particionálási kulcs kiválasztása bölcs módon, hogy az összes partíció mérete is legyen. Előfordulhat például, hogy az *állapot* oszlop particionálásakor az adateloszlás elferdíthető. Mivel Kalifornia állam csaknem 30x a Vermontban, a partíció mérete potenciálisan elferdíthető, és a teljesítmény rendkívül eltérő lehet.

Partíciós tábla létrehozásához használja a *particionált by* záradékot:

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

A particionált tábla létrehozása után létrehozhat statikus particionálást vagy dinamikus particionálást.

* A **statikus particionálás** azt jelenti, hogy a megfelelő címtárakban már vannak a szilánkokra osztott adathalmazok. A statikus partíciók esetében manuálisan adja hozzá a kaptár-partíciókat a címtár helye alapján. A következő kódrészlet egy példa.
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* A **dinamikus particionálás** azt jelenti, hogy azt szeretné, hogy a kaptár automatikusan hozzon létre partíciókat. Mivel már létrehozta a particionálási táblázatot az előkészítési táblából, mindössze annyit kell tennie, hogy az adatok beillesztése a particionált táblába:
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

További információ: [particionált táblák](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables).

## <a name="use-the-orcfile-format"></a>ORCFile formátum használata

A struktúra a különböző fájlformátumokat támogatja. Például:

* **Text (szöveg**): az alapértelmezett fájlformátum, és a legtöbb esetben működik.
* **Avro**: az együttműködési helyzetekben jól működik.
* **Ork/parketta**: a legmegfelelőbb a teljesítményhez.

Az ork (optimalizált sor oszlopos) formátuma igen hatékony módszer a struktúra-adatok tárolására. Más formátumokhoz képest az ork a következő előnyökkel jár:

* összetett típusok támogatása, beleértve a DateTime és az összetett és a félig strukturált típusokat.
* akár 70%-os tömörítés.
* minden 10 000 sort indexel, amely lehetővé teszi a sorok kihagyását.
* a futásidejű végrehajtás jelentős csökkenése.

Az ork formátum engedélyezéséhez először létre kell hoznia egy táblázatot az *ork-ként tárolt* záradékkal:

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

Ezután szúrja be az adatait az ork táblába az előkészítési táblából. Például:

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

További információ az ork formátumról a [Apache Hive nyelvi kézikönyvben](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)olvasható.

## <a name="vectorization"></a>Vektorizációt

A vektorizációt lehetővé teszi, hogy a kaptár egyszerre több sorba dolgozza fel az 1024-es sorok feldolgozását. Ez azt jelenti, hogy az egyszerű műveletek végrehajtása gyorsabban történik, mert kevesebb belső kód futtatására van szükség.

A vektorizációt-előtag a következő beállítással engedélyezhető a kaptár-lekérdezésben:

```hive
set hive.vectorized.execution.enabled = true;
```

További információ: a [vektoros lekérdezés végrehajtása](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution).

## <a name="other-optimization-methods"></a>Egyéb optimalizálási módszerek

Több optimalizálási módszer is megvizsgálható, például:

* **Struktúra-gyűjtő:** olyan technika, amely lehetővé teszi, hogy a lekérdezési teljesítmény optimalizálása érdekében a nagy mennyiségű adat fürtbe vagy szegmentálásba kerüljön.
* **Csatlakozás optimalizálása:** a kaptár lekérdezés-végrehajtásának optimalizálása az összekapcsolások hatékonyságának növelése és a felhasználói célzások szükségességének csökkentése érdekében. További információ: [JOIN Optimization (csatlakozás optimalizálása](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)).
* **Növelje a szűkítőket**.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben több általános kaptár-lekérdezés optimalizálási módszerét is megtanulta. További tudnivalókért olvassa el a következő cikket:

* [Az Apache Hive optimalizálása](./optimize-hive-ambari.md)
* [Repülési késleltetési adatelemzések elemzése a HDInsight interaktív lekérdezés használatával](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Twitter-adataik elemzése a HDInsight Apache Hive használatával](hdinsight-analyze-twitter-data-linux.md)
