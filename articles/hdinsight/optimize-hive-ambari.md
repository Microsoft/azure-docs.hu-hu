---
title: Apache Hive optimalizálása az Apache Ambari az Azure HDInsight
description: A Apache Hive konfigurálásához és optimalizálásához használja az Apache Ambari webes FELÜLETét.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 349f58720e6fff52191dfff65108cd1320e41eed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939256"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Apache Hive optimalizálása az Apache Ambari az Azure HDInsight

Az Apache Ambari egy webes felület a HDInsight-fürtök felügyeletéhez és figyeléséhez. A Ambari webes felhasználói felületének bevezetését lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületén keresztül](hdinsight-hadoop-manage-ambari.md).

A következő szakaszok ismertetik az általános Apache Hive teljesítmény optimalizálásához szükséges konfigurációs beállításokat.

1. A struktúra konfigurációs paramétereinek módosításához válassza a **struktúra** lehetőséget a szolgáltatások oldalsávon.
1. Navigáljon a **konfigurációk** lapra.

## <a name="set-the-hive-execution-engine"></a>A kaptár-végrehajtó motor beállítása

A kaptár két végrehajtó motort biztosít: Apache Hadoop MapReduce és Apache TEZ. A TEZ gyorsabb, mint a MapReduce. A HDInsight Linux-fürtök alapértelmezett TEZ rendelkeznek. A végrehajtási motor módosítása:

1. A kaptár- **konfigurációk** lapon írja be a végrehajtó **motor** értéket a szűrő mezőbe.

    ![Apache Ambari keresési végrehajtó motor](./media/optimize-hive-ambari/ambari-search-execution.png)

1. Az **optimalizálási** tulajdonság alapértelmezett értéke **TEZ**.

    ![Optimalizálás – Apache TEZ Engine](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Leképezések hangolása

A Hadoop egyetlen fájlt próbálfelosztani több fájlba, és párhuzamosan dolgozza fel az eredményül kapott fájlokat. A leképezések száma a felosztások számától függ. A következő két konfigurációs paraméter a TEZ-végrehajtó motor felosztásának számát hajtja végre:

* `tez.grouping.min-size`: Egy csoportosított felosztás méretének alsó korlátja, amelynek alapértelmezett értéke 16 MB (16 777 216 bájt).
* `tez.grouping.max-size`: Egy csoportosított felosztás felső korlátja, amelynek alapértelmezett értéke 1 GB (1 073 741 824 bájt).

Teljesítményi útmutatóként a paraméterek csökkentése érdekében csökkentse a késést, növelje a további átviteli sebességet.

Ha például négy Mapper-feladatot szeretne beállítani 128 MB adatmérethez, mindkét paramétert 32 MB-ra (33 554 432 bájt) kell beállítania.

1. A határérték-paraméterek módosításához navigáljon a TEZ szolgáltatás **konfigurációk** lapjára. Bontsa ki az **általános** panelt, és keresse meg a `tez.grouping.max-size` és a `tez.grouping.min-size` paramétereket.

1. Mindkét paraméter **33 554 432** bájtra (32 MB) állítható be.

    ![Apache Ambari TEZ-csoportosítási méretek](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Ezek a módosítások a kiszolgálón lévő összes TEZ-feladatot érintik. Az optimális eredmény eléréséhez válassza a megfelelő paraméterek értékét.

## <a name="tune-reducers"></a>Szűkítők hangolása

Az Apache ork és a Snappy egyaránt nagy teljesítményt nyújt. A kaptár azonban alapértelmezés szerint túl kevés szűkítővel rendelkezhet, ami szűk keresztmetszetet okoz.

Tegyük fel például, hogy a bemeneti adatok mérete 50 GB. Az ork formátumú, Snappy-tömörítéssel rendelkező adat 1 GB. A struktúra a következőhöz szükséges szűkítők számát becsüli meg: `hive.exec.reducers.bytes.per.reducer`

Az alapértelmezett beállításokkal ez a példa négy szűkítőt mutat be.

A `hive.exec.reducers.bytes.per.reducer` paraméter határozza meg a feldolgozható bájtok számát. Az alapértelmezett érték 64 MB. Az érték finomhangolása növeli a párhuzamosságot, és növelheti a teljesítményt. A túl alacsony hangolás túl sok szűkítőt is eredményezhet, ami potenciálisan hátrányosan befolyásolhatja a teljesítményt. Ez a paraméter az adott adatkövetelményektől, a tömörítési beállításoktól és más környezeti tényezőktől függ.

1. A paraméter módosításához navigáljon a struktúra- **konfigurációk** lapra, és keresse meg a beállítások lapon található, redukáló paraméterrel rendelkező **adatmennyiséget** .

    ![Apache Ambari-adatmennyiség csökkentése](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. Válassza a **Szerkesztés** lehetőséget az érték 128 MB-ra (134 217 728 bájt) való módosításához, majd nyomja le az **ENTER** billentyűt a mentéshez.

    ![Ambari-adatmennyiség csökkentése – szerkesztett](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    A bemeneti méret 1 024 MB, a 128 MB adatmennyiség pedig a redukáló értéknél nyolc szűkítő (1024/128).

1. Az **adatvesztési** paramétereknél az adat helytelen értéke nagy számú szűkítőt eredményezhet, ami kedvezőtlen hatással lehet a lekérdezési teljesítményre. A szűkítők maximális számának korlátozásához állítsa be a `hive.exec.reducers.max` megfelelő értéket. Az alapértelmezett érték a 1009.

## <a name="enable-parallel-execution"></a>Párhuzamos végrehajtás engedélyezése

A kaptár-lekérdezések végrehajtása egy vagy több szakaszban történik. Ha a független szakaszok párhuzamosan is futtathatók, a lekérdezési teljesítményt is növeli.

1. A párhuzamos lekérdezés végrehajtásának engedélyezéséhez keresse meg a struktúra **konfigurációja** fület, és keresse meg a `hive.exec.parallel` tulajdonságot. Az alapértelmezett érték a hamis. Módosítsa az értéket True értékre, majd nyomja le az **ENTER** billentyűt az érték mentéséhez.

1. A párhuzamosan futtatandó feladatok számának korlátozásához módosítsa a `hive.exec.parallel.thread.number` tulajdonságot. Az alapértelmezett érték 8.

    ![Apache Hive exec párhuzamos megjelenítés](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Vektorizációt engedélyezése

A struktúra soronként dolgozza fel az adatsort. A vektorizációt a Kaptárat úgy irányítja át, hogy az adatfeldolgozást egy sor helyett 1 024 sor formájában dolgozza fel. A vektorizációt csak az ork fájlformátumra alkalmazható.

1. A vektoros lekérdezés végrehajtásának engedélyezéséhez navigáljon a kaptár **konfigurációk** lapra, és keresse meg a `hive.vectorized.execution.enabled` paramétert. Az alapértelmezett érték a kaptár 0.13.0 vagy újabb verziója esetén igaz.

1. A lekérdezés csökkentése érdekében a vektoros végrehajtás engedélyezéséhez állítsa igaz értékre a `hive.vectorized.execution.reduce.enabled` paramétert. Az alapértelmezett érték a hamis.

    ![Vektoros végrehajtás Apache Hive](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Cost-alapú optimalizálás engedélyezése (CBO)

Alapértelmezés szerint a kaptár egy olyan szabályt követ, amely egy optimális lekérdezés-végrehajtási tervet keres. A Cost-based Optimization (CBO) több tervet értékel ki egy lekérdezés végrehajtásához. És minden csomaghoz hozzárendel egy díjat, majd meghatározza a lekérdezés végrehajtásának legolcsóbb tervét.

A CBO engedélyezéséhez lépjen a **kaptár**-  >  **konfigurációk**  >  **beállításaihoz** , és keresse meg a **Cost-alapú optimalizáló engedélyezése** lehetőséget, majd kapcsolja be a váltás gombot a **be** értékre.

![HDInsight-alapú optimalizáló](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

A következő további konfigurációs paraméterek fokozzák a kaptár-lekérdezések teljesítményét, ha a CBO engedélyezve van:

* `hive.compute.query.using.stats`

    Ha igaz értékre van állítva, a struktúra a saját metaadattár tárolt statisztikát használja az egyszerű lekérdezések megválaszolásához, például: `count(*)` .

    ![Számítási lekérdezés Apache Hive statisztikák használatával](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Az oszlop statisztikái akkor jönnek létre, ha a CBO engedélyezve van. A kaptár a metaadattár tárolt oszlopok statisztikáit használja a lekérdezések optimalizálására. Az oszlopok statisztikáinak beolvasása az egyes oszlopoknál tovább tart, ha az oszlopok száma magas. Ha false értékre van állítva, akkor ez a beállítás letiltja az oszlopok statisztikáinak beolvasását a metaadattár.

    ![Apache Hive statisztika-készlet oszlopainak statisztikája](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Az alapszintű partíciók statisztikái, például a sorok száma, az adatok mérete és a fájlméret a metaadattár-ben tárolódnak. Ha igaz értékre van állítva, a partíció statisztikái a metaadattár-ből lesznek beolvasva. Hamis érték esetén a rendszer beolvassa a fájl méretét a fájlrendszerből. És a sorok száma a sor sémából beolvasva.

    ![Struktúra-stats set Partition stats](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Közbenső tömörítés engedélyezése

A leképezési feladatok a csökkentő tevékenységek által használt köztes fájlokat hoznak létre. A köztes tömörítés a köztes fájlméretet csökkenti.

A Hadoop feladatok általában I/O-torlódások. Az adatok tömörítése felgyorsíthatja az I/O-t és a teljes hálózati átvitelt.

A rendelkezésre álló tömörítési típusok a következők:

| Formátum | Eszköz | Algoritmus | Fájlkiterjesztés | Feloszthatók? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | No |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Yes |
| LZO | `Lzop` | LZO | `.lzo` | Igen, ha indexelve van |
| Snappy | N/A | Snappy | Snappy | No |

Általános szabály, hogy a tömörítési módszer megosztója fontos, ellenkező esetben a rendszer néhány leképezést hoz létre. Ha a bemeneti adatok szöveg, `bzip2` a legjobb megoldás. Az ork formátum esetében a Snappy a leggyorsabb tömörítési lehetőség.

1. A köztes tömörítés engedélyezéséhez navigáljon a struktúra- **konfigurációk** lapra, és állítsa a `hive.exec.compress.intermediate` paramétert True (igaz) értékre. Az alapértelmezett érték a hamis.

    !["Kaptár exec compress Intermediate"](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > A köztes fájlok tömörítéséhez válassza ki a tömörítési kodeket, amely alacsonyabb CPU-költségeket tartalmaz, még akkor is, ha a kodek nem rendelkezik magas tömörítési kimenettel.

1. A köztes tömörítési kodek beállításához adja hozzá az egyéni tulajdonságot `mapred.map.output.compression.codec` a `hive-site.xml` vagy a `mapred-site.xml` fájlhoz.

1. Egyéni beállítás hozzáadása:

    a. Navigáljon a **kaptár**  >  **konfigurációk**  >  **speciális**  >  **Egyéni struktúra-hely** elemre.

    b. Válassza a **tulajdonság hozzáadása...** lehetőséget az egyéni struktúra – hely ablaktábla alján.

    c. A tulajdonság hozzáadása ablakban adja meg `mapred.map.output.compression.codec` a kulcsot és `org.apache.hadoop.io.compress.SnappyCodec` az értéket.

    d. Válassza a **Hozzáadás** lehetőséget.

    !["Apache Hive egyéni tulajdonság hozzáadása"](./media/optimize-hive-ambari/hive-custom-property.png)

    Ezzel a beállítással a rendszer a köztes fájlt a Snappy Compression használatával tömöríti. A tulajdonság hozzáadása után az megjelenik az egyéni struktúra – hely ablaktáblán.

    > [!NOTE]  
    > Ez az eljárás módosítja a `$HADOOP_HOME/conf/hive-site.xml` fájlt.

## <a name="compress-final-output"></a>Végső kimenet tömörítése

A struktúra utolsó kimenete is tömöríthető.

1. A struktúra utolsó kimenetének tömörítéséhez navigáljon a struktúra- **konfigurációk** lapra, és állítsa a `hive.exec.compress.output` paramétert True (igaz) értékre. Az alapértelmezett érték a hamis.

1. A kimeneti tömörítési kodek kiválasztásához adja hozzá az `mapred.output.compression.codec` Egyéni tulajdonságot az egyéni struktúra – hely panelhez az előző szakasz 3. lépésében leírtak szerint.

    ![Apache Hive egyéni tulajdonság add2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Spekulatív végrehajtás engedélyezése

A spekulatív végrehajtás bizonyos számú ismétlődő feladatot indít el a lassú működésű feladat nyomon követéséhez és megtagadásához. Az egyes feladatok eredményeinek optimalizálásával javíthatja az általános feladatok végrehajtását.

A spekulatív végrehajtás nem kapcsolható be nagy mennyiségű bemenettel rendelkező, hosszan futó MapReduce feladatokhoz.

* A spekulációs végrehajtás engedélyezéséhez navigáljon a kaptár **konfigurációk** lapra, és állítsa a `hive.mapred.reduce.tasks.speculative.execution` paramétert True (igaz) értékre. Az alapértelmezett érték a hamis.

    !["A kaptár mapred csökkenti a feladatok spekulációs végrehajtását"](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Dinamikus partíciók hangolása

A struktúra lehetővé teszi, hogy dinamikus partíciókat hozzon létre, amikor rekordokat szúr be egy táblába az összes partíció elődefiniálása nélkül. Ez a képesség egy hatékony szolgáltatás. Bár ez nagy számú partíció létrehozását eredményezheti. És az egyes partíciók nagy mennyiségű fájlja.

1. Ahhoz, hogy a struktúra dinamikus partíciókat végezzen, a `hive.exec.dynamic.partition` paraméter értékének igaznak kell lennie (az alapértelmezett beállítás).

1. Módosítsa a dinamikus partíciós módot a *szigorú* értékre. Szigorú módban legalább egy partíciónak statikusnak kell lennie. Ezzel a beállítással megakadályozható, hogy a WHERE záradékban a partíciós szűrő nélküli lekérdezések, azaz az összes partíciót ellenőrző lekérdezések *szigorú* megakadályozása. Navigáljon a kaptár- **konfigurációk** lapra, és állítsa a `hive.exec.dynamic.partition.mode` **szigorú** értékre. Az alapértelmezett érték nem **szigorú**.

1. A létrehozandó dinamikus partíciók számának korlátozásához módosítsa a `hive.exec.max.dynamic.partitions` paramétert. Az alapértelmezett érték a 5000.

1. A dinamikus partíciók teljes számának korlátozásához módosítsa a csomópontot `hive.exec.max.dynamic.partitions.pernode` . Az alapértelmezett érték a 2000.

## <a name="enable-local-mode"></a>Helyi mód engedélyezése

A helyi mód lehetővé teszi, hogy a kaptár egyetlen gépen hajtsa végre a feladat összes feladatát. Vagy időnként egyetlen folyamatban. Ez a beállítás javítja a lekérdezési teljesítményt, ha a bemeneti adatok kicsik. A lekérdezési feladatok elindításának terhelése a lekérdezés teljes végrehajtásának jelentős hányadát használja fel.

A helyi mód engedélyezéséhez adja hozzá a (z `hive.exec.mode.local.auto` ) paramétert az egyéni struktúra – hely panelhez a [köztes tömörítés engedélyezése](#enable-intermediate-compression) szakasz 3. lépésében leírtak szerint.

![Apache Hive exec mód helyi automatikus](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Egyetlen MapReduce többcsoportos beállítása

Ha ez a tulajdonság TRUE (igaz) értékre van állítva, a közös csoport – kulcsokkal rendelkező többcsoportos lekérdezések egyetlen MapReduce-feladatot hoznak létre.  

Ha engedélyezni szeretné ezt a viselkedést, adja hozzá a `hive.multigroupby.singlereducer` paramétert az egyéni struktúra – hely ablaktáblához, ahogy az a [köztes tömörítés engedélyezése](#enable-intermediate-compression) szakasz 3. lépésében leírtak szerint.

![Struktúra egyetlen MapReduce többcsoportos beállítása](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>További kaptár-optimalizálások

A következő szakaszok a kaptárral kapcsolatos további optimalizálásokat ismertetik.

### <a name="join-optimizations"></a>Csatlakozás optimalizációk

A kaptárban az alapértelmezett illesztési típus egy *shuffle illesztés*. A kaptárban a speciális leképezések beolvassák a bemenetet, és egy összekapcsolási kulcs/érték párokat bocsátanak ki egy köztes fájlba. A Hadoop rendezi és egyesíti ezeket a párokat egy véletlenszerű szakaszban. Ez a véletlenszerű szakasz költséges. Ha kiválasztja a megfelelő illesztést az adatai alapján, jelentősen növelheti a teljesítményt.

| Csatlakozás típusa | Mikor | Hogyan | Struktúra beállításai | Megjegyzések |
| --- | --- | --- | --- | --- |
| Összekeverhető illesztés | <ul><li>Alapértelmezett választás</li><li>Mindig működik</li></ul> | <ul><li>Olvasás az egyik táblázat részéből</li><li>Az illesztési kulcshoz tartozó gyűjtők és rendezések</li><li>Egy gyűjtőt küld minden egyes csökkentéshez</li><li>A csatlakozás a csökkentés oldalon történik</li></ul> | Nincs szükség jelentős kaptár-beállításra | Minden alkalommal működik |
| Csatlakoztatás leképezése | <ul><li>Egy tábla fér el a memóriában</li></ul> | <ul><li>Kisméretű tábla beolvasása a memória kivonatának táblájába</li><li>Streamek a nagyméretű fájl részeként</li><li>Az egyes rekordok összekapcsolása a kivonatoló táblából</li><li>Egyedül a Mapper csatlakozik</li></ul> | `hive.auto.confvert.join=true` | Gyors, de korlátozott |
| Egyesítési gyűjtők rendezése | Ha mindkét tábla: <ul><li>Azonos sorrend</li><li>Azonos</li><li>Csatlakozás a rendezett/gyűjtő oszlophoz</li></ul> | Minden folyamat: <ul><li>Egy gyűjtő beolvasása az egyes táblákból</li><li>A legkisebb értékű sort dolgozza fel</li></ul> | `hive.auto.convert.sortmerge.join=true` | Hatékony |

### <a name="execution-engine-optimizations"></a>Végrehajtó motor optimalizálása

További javaslatok a kaptár-végrehajtó motor optimalizálásához:

| Beállítás | Ajánlott | HDInsight alapértelmezett értéke |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Igaz = biztonságosabb, lassabb; hamis = gyorsabb | hamis |
| `tez.am.resource.memory.mb` | 4 GB felső korlát a legtöbb | Automatikusan hangolt |
| `tez.session.am.dag.submit.timeout.secs` | 300 + | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000 + | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000 + | 20000 |

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETtel](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Apache Hive-lekérdezések optimalizálása az Azure HDInsightban](./hdinsight-hadoop-optimize-hive-query.md)
* [Fürtök optimalizálása](./optimize-hive-ambari.md)
* [Az Apache HBase optimalizálása](./optimize-hbase-ambari.md)
* [Az Apache Pig optimalizálása](./optimize-pig-ambari.md)
