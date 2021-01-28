---
title: Az Apache HBase optimalizálása az Apache Ambari az Azure HDInsight
description: Az Apache Ambari webes FELÜLETének használatával konfigurálhatja és optimalizálhatja az Apache HBase.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 307e9decad35c20885fbe550cc1f7de58c9386c0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942596"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Az Apache HBase optimalizálása az Apache Ambari az Azure HDInsight

Az Apache Ambari egy webes felület a HDInsight-fürtök felügyeletéhez és figyeléséhez. A Ambari webes felhasználói felületének bevezetését lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületén keresztül](hdinsight-hadoop-manage-ambari.md).

Az Apache HBase-konfiguráció a **HBase konfigurációk** lapról módosul. A következő szakaszok ismertetik a HBase teljesítményét befolyásoló fontos konfigurációs beállításokat.

## <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE beállítása

A HBase halom mérete határozza meg a *régiók* *és főkiszolgálók* által megabájtban használt maximális halom mennyiségét. Az alapértelmezett érték 1 000 MB. Ezt az értéket be kell hangolni a fürt számítási feladataihoz.

1. A módosításhoz navigáljon a HBase **konfigurációk** lap **speciális HBase-env** paneljére, és keresse meg a `HBASE_HEAPSIZE` beállítást.

1. Módosítsa az alapértelmezett értéket 5 000 MB-ra.

    !["Apache Ambari HBase Memory heapsize"](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Olvasási és nagy terhelések optimalizálása

A következő konfigurációk fontosak a nagy teljesítményű számítási feladatok teljesítményének javításához.

### <a name="block-cache-size"></a>Gyorsítótár méretének letiltása

A blokk gyorsítótára az olvasási gyorsítótár. A méretet a paraméter szabályozza `hfile.block.cache.size` . Az alapértelmezett érték 0,4, amely a teljes régióbeli kiszolgáló memóriájának 40 százalékát képezi. Minél nagyobb a blokk gyorsítótárának mérete, annál gyorsabb lesz a véletlenszerű olvasás.

1. A paraméter módosításához navigáljon a **Beállítások** lapra a HBase **konfigurációk** lapon, majd keresse meg az **olvasási pufferek számára lefoglalt RegionServer%**-át.

    ![Apache HBase-gyorsítótár mérete](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Az érték módosításához kattintson a **Szerkesztés** ikonra.

### <a name="memstore-size"></a>Memstore mérete

A rendszer az összes módosítást a *Memstore* nevezett memória-pufferben tárolja. Ez a puffer növeli a lemezre egyetlen művelet során írható összes adatmennyiséget. Emellett a legutóbbi szerkesztésekhez is felgyorsítja a hozzáférést. A Memstore méretét a következő két paraméter határozza meg:

* `hbase.regionserver.global.memstore.UpperLimit`: Meghatározza, hogy a Memstore legfeljebb hány százalékos arányt használhat.

* `hbase.regionserver.global.memstore.LowerLimit`: Meghatározza, hogy a Memstore együtt használható-e a régió-kiszolgáló minimális százalékos aránya.

A véletlenszerű olvasások optimalizálásához csökkentheti a Memstore alsó és felső korlátját.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>A lemezről való vizsgálatkor beolvasott sorok száma

A `hbase.client.scanner.caching` beállítás határozza meg a lemezről beolvasott sorok számát, ha a `next` metódust egy képolvasó hívja meg.  Az alapértelmezett érték 100. Minél nagyobb a szám, annál kevesebb az ügyfél és a régió-kiszolgáló közötti távoli hívások száma, ami gyorsabb vizsgálatot eredményez. Ez a beállítás azonban növeli a memória terhelését is az ügyfélen.

![Apache HBase beolvasott sorok száma](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Ne állítsa be úgy az értéket, hogy a képolvasó következő metódusának hívása közötti idő nagyobb legyen, mint a képolvasó időkorlátja. A képolvasó időtúllépési időtartamát a tulajdonság határozza meg `hbase.regionserver.lease.period` .

## <a name="optimize-write-heavy-workloads"></a>Írási-nagy számítási feladatok optimalizálása

A következő konfigurációk fontosak a nagy mennyiségű számítási feladatok teljesítményének javításához.

### <a name="maximum-region-file-size"></a>A régió maximális fájlmérete

A HBase belső fájlformátumban tárolja az adattárolást, amelynek neve *HFile*. A tulajdonság a `hbase.hregion.max.filesize` régió egyetlen HFile méretét határozza meg.  Ha egy régió összes HFiles összege meghaladja ezt a beállítást, a régió két régióra oszlik.

!["Apache HBase HRegion Max filesize"](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Minél nagyobb a régió fájlmérete, annál kisebb a felosztások száma. Megnövelheti a fájlméretet úgy, hogy meghatározza a maximális írási teljesítményt eredményező értéket.

### <a name="avoid-update-blocking"></a>A frissítés blokkolásának elkerülése

* A tulajdonság `hbase.hregion.memstore.flush.size` azt a méretet határozza meg, amelyben a Memstore kiürítése lemezre történik. Az alapértelmezett méret 128 MB.

* A HBase-régió blokk szorzóját a határozza meg `hbase.hregion.memstore.block.multiplier` . Az alapértelmezett érték a 4. A maximálisan engedélyezett érték 8.

* A HBase letiltja a frissítéseket, ha a Memstore ( `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` ) bájtok.

    A kiürítési méret és a szorzó alapértelmezett értékeivel a frissítések le lesznek tiltva, ha a Memstore 128 * 4 = 512 MB méretű. A frissítési blokkolások számának csökkentéséhez növelje a értékét `hbase.hregion.memstore.block.multiplier` .

![Apache HBase region Block szorzó](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Memstore méretének meghatározása

A Memstore méretét a és a paraméterek határozzák meg `hbase.regionserver.global.memstore.upperLimit` `hbase.regionserver.global.memstore.lowerLimit` . Ha ezeket az értékeket úgy állítja be, hogy azok egyenlőek legyenek az írások során (ami még gyakoribb kiürítést is eredményez), és növeli az írási teljesítményt.

## <a name="set-memstore-local-allocation-buffer"></a>Memstore helyi foglalási pufferének beállítása

A Memstore helyi kiosztási puffer használatát a tulajdonság határozza meg `hbase.hregion.memstore.mslab.enabled` . Ha engedélyezve van (igaz), ez a beállítás megakadályozza a halom töredezettségét a nehéz írási művelet során. Az alapértelmezett érték az igaz.

![hbase. hregion. memstore. mslab. enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETtel](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Fürtök optimalizálása](./hdinsight-changing-configs-via-ambari.md)
* [Az Apache Hive optimalizálása](./optimize-hive-ambari.md)
* [Az Apache Pig optimalizálása](./optimize-pig-ambari.md)
