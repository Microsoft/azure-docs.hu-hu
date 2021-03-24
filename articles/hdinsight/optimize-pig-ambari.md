---
title: Az Apache Pig és az Apache Ambari optimalizálása az Azure HDInsight
description: Az Apache Ambari webes FELÜLETének használatával konfigurálhatja és optimalizálhatja az Apache Pig-t.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 43159360342063af1682a984db037ae286721411
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871163"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Az Apache Pig és az Apache Ambari optimalizálása az Azure HDInsight

Az Apache Ambari egy webes felület a HDInsight-fürtök felügyeletéhez és figyeléséhez. A Ambari webes felhasználói felületének bevezetését lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületén keresztül](hdinsight-hadoop-manage-ambari.md).

Az Apache Pig tulajdonságai módosíthatók a Ambari webes KEZELŐFELÜLETéről a Pig-lekérdezések finomhangolásához. A Pig tulajdonságainak módosítása a Ambari közvetlenül módosítja a Pig tulajdonságait a `/etc/pig/2.4.2.0-258.0/pig.properties` fájlban.

1. A Pig tulajdonságainak módosításához navigáljon a Pig **konfigurációk** lapra, majd bontsa ki a **speciális Pig-Properties** panelt.

1. Keresse meg a módosítani kívánt tulajdonság értékét, és írja meg a megjegyzéseit, és módosítsa azt.

1. Az új érték mentéséhez kattintson a **Save (Mentés** ) gombra az ablak jobb felső részén. Bizonyos tulajdonságok esetében előfordulhat, hogy a szolgáltatás újraindítása szükséges.

    :::image type="content" source="./media/optimize-pig-ambari/advanced-pig-properties.png" alt-text="Speciális Apache Pig-tulajdonságok" border="true":::

> [!NOTE]  
> Minden munkamenet-szintű beállítás felülbírálja a fájl tulajdonságának értékét `pig.properties` .

## <a name="tune-execution-engine"></a>Végrehajtó motor hangolása

A Pig-parancsfájlok végrehajtásához két végrehajtó motor érhető el: MapReduce és TEZ. A TEZ egy optimalizált motor, amely jóval gyorsabb, mint a MapReduce.

1. A végrehajtási motor módosításához a **speciális Pig-Properties** ablaktáblán keresse meg a tulajdonságot `exectype` .

1. Az alapértelmezett érték a **MapReduce**. Módosítsa a **TEZ**.

## <a name="enable-local-mode"></a>Helyi mód engedélyezése

A Kaptárhoz hasonlóan a helyi mód is a feladatok gyorsabb, viszonylag kis mennyiségű adattal való felgyorsítására szolgál.

1. A helyi mód engedélyezéséhez állítsa a `pig.auto.local.enabled` **true (igaz**) értéket. Az alapértelmezett érték a hamis.

1. A tulajdonság értéknél kisebb bemeneti adatmérettel rendelkező feladatok `pig.auto.local.input.maxbytes` kisebb feladatoknak tekintendők. Az alapértelmezett érték 1 GB.

## <a name="copy-user-jar-cache"></a>Felhasználói jar-gyorsítótár másolása

A Pig a UDF által igényelt JAR-fájlokat egy elosztott gyorsítótárba másolja, hogy azok elérhetők legyenek a feladatok csomópontjai számára. Ezek a tégelyek nem változnak gyakran. Ha engedélyezve van, a `pig.user.cache.enabled` beállítás lehetővé teszi, hogy a tégelyek egy gyorsítótárba kerüljenek, hogy újra felhasználhassa azokat az ugyanazon felhasználó által futtatott feladatokhoz. Ez a beállítás kisebb növekedést eredményez a feladatok teljesítményében.

1. Az engedélyezéshez állítsa `pig.user.cache.enabled` igaz értékre. Az alapértelmezett érték a false.

1. A gyorsítótárazott tégelyek alapelérési útjának megadásához állítsa `pig.user.cache.location` az alap elérési utat. A mező alapértelmezett értéke: `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>A teljesítmény optimalizálása a memória beállításaival

A következő memória-beállítások segíthetnek a Pig-parancsfájlok teljesítményének optimalizálásában.

* `pig.cachedbag.memusage`: A táska számára megadott memória mennyisége. A táska a rekordok gyűjteménye. A rekord mezők rendezett halmaza, és a mező egy adat. Ha egy táskában lévő adat a megadott memórián kívül esik, a lemezre kerül. Az alapértelmezett érték 0,2, amely a rendelkezésre álló memória 20 százalékát jelöli. Ez a memória az alkalmazás összes csomagjai között meg van osztva.

* `pig.spill.size.threshold`: Az ennél a kiömlésnél nagyobb méretű, bájtban megadott tasakok a lemezre kerülnek. Az alapértelmezett érték 5 MB.

## <a name="compress-temporary-files"></a>Ideiglenes fájlok tömörítése

A Pig ideiglenes fájlokat hoz létre a feladatok végrehajtása során. Az ideiglenes fájlok tömörítése a fájlok lemezre való olvasásakor vagy írásakor a teljesítmény növekedését eredményezi. A következő beállítások használhatók az ideiglenes fájlok tömörítésére.

* `pig.tmpfilecompression`: Ha igaz, az ideiglenes fájltömörítés engedélyezése. Az alapértelmezett érték a hamis.

* `pig.tmpfilecompression.codec`: Az ideiglenes fájlok tömörítéséhez használandó tömörítési kodek. Az ajánlott tömörítési kodekek a LZO és az alacsonyabb CPU-használathoz szükségesek.

## <a name="enable-split-combining"></a>Felosztott egyesítés engedélyezése

Ha ez a beállítás engedélyezve van, a kis méretű fájlok kevesebb leképezési feladathoz vannak egyesítve. Ez a beállítás javítja a sok kis fájllal rendelkező feladatok hatékonyságát. Az engedélyezéshez állítsa `pig.noSplitCombination` igaz értékre. Az alapértelmezett érték a hamis.

## <a name="tune-mappers"></a>Leképezések hangolása

A leképezések számát a tulajdonság módosításával szabályozhatja `pig.maxCombinedSplitSize` . Ez a tulajdonság határozza meg az egyetlen leképezési feladattal feldolgozandó adatok méretét. Az alapértelmezett érték a fájlrendszer alapértelmezett blokkjának mérete. Ennek az értéknek a növelésével alacsonyabb számú Mapper-feladatot eredményez.

## <a name="tune-reducers"></a>Szűkítők hangolása

A rendszer a paraméter alapján számítja ki a szűkítők számát `pig.exec.reducers.bytes.per.reducer` . A paraméter a redukáló által feldolgozott bájtok számát adja meg, alapértelmezés szerint 1 GB. A szűkítők maximális számának korlátozásához állítsa a `pig.exec.reducers.max` tulajdonságot a 999 alapértelmezett értékre.

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETtel](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Fürtök optimalizálása](./hdinsight-changing-configs-via-ambari.md)
* [Az Apache HBase optimalizálása](./optimize-hbase-ambari.md)
* [Az Apache Hive optimalizálása](./optimize-hive-ambari.md)
