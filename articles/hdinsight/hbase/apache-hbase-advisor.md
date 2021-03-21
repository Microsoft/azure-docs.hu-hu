---
title: A cluster Advisor ajánlásainak optimalizálása
titleSuffix: Azure HDInsight
description: Optimalizálja az Apache HBase-t a cluster Advisor ajánlásaihoz az Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943015"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Apache HBase-tanácsadók az Azure HDInsight

Ez a cikk több, az Apache HBase teljesítményének az Azure HDInsight-ben való optimalizálását segítő tanácsadót ismertet. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>A HBase optimalizálása a legutóbb írt információk olvasásához

Ha a usecase a HBase által legutóbb írt adatok olvasását is magában foglalja, ez a tanácsadó segítséget nyújt Önnek. A nagy teljesítmény érdekében optimális, hogy a HBase-olvasások a távoli tárterület helyett a memstore legyenek kézbesítve.

A lekérdezési tanácsadó azt jelzi, hogy egy tábla egy adott oszlopának családja > 75% olvasás, amely a memstore-ből lesz kiszolgálva. Ez a kijelző arra utal, hogy még akkor is, ha a memstore kiürítés történik a legutóbbi fájl eléréséhez, és a gyorsítótárban kell lennie. A rendszer először az memstore írja, hogy a rendszer hozzáfér a legutóbbi adatszolgáltatásokhoz. Lehetséges, hogy a belső HBase-kiürítési szálak azt észlelik, hogy egy adott régió elérte a 128M (alapértelmezett) méretet, és elindíthat egy kiürítést. Ez a forgatókönyv akkor is előfordul, ha a memstore körülbelül 128M volt. Ezért előfordulhat, hogy a legutóbbi rekordok egy későbbi beolvasása nem a memstore, hanem egy fájl olvasását igényli. Ezért érdemes optimalizálni, hogy még a közelmúltban kiürített legutóbbi adat is a gyorsítótárban legyen.

A legutóbbi gyorsítótárbeli adatfrissítések optimalizálásához vegye figyelembe a következő konfigurációs beállításokat:

1. Állítsa a következőre: `hbase.rs.cacheblocksonwrite` `true` . Ez az alapértelmezett konfiguráció a HDInsight-HBase `true` , ezért győződjön meg róla, hogy nem áll vissza a következőre: `false` .

2. Növelje az `hbase.hstore.compactionThreshold` értéket, hogy elkerülje a tömörítést a berúgással. Alapértelmezés szerint ez az érték: `3`. Magasabb értéket is megadhat, például: `10` .

3. Ha követi a 2. lépést, és beállítja a compactionThreshold-t, váltson `hbase.hstore.compaction.max` magasabb értékre (például `100` ), és növelje a konfiguráció értékét `hbase.hstore.blockingStoreFiles` magasabb értékre (például `300` ).

4. Ha biztos benne, hogy csak a legutóbbi adatgyűjtést kell elolvasnia, állítsa be `hbase.rs.cachecompactedblocksonwrite` a konfigurációt **a** következőre:. Ez a konfiguráció azt jelzi, hogy a rendszer akkor is a gyorsítótárban marad, ha a tömörítés történik. A konfigurációk a család szintjén is megadhatók. 

   A HBase-rendszerhéjban futtassa a következő parancsot a konfiguráció beállításához `hbase.rs.cachecompactedblocksonwrite` :
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. A blokk-gyorsítótár kikapcsolható egy tábla egy adott családja számára. Ellenőrizze, hogy **be van-e kapcsolva a** legutóbbi adatokat olvasó családokhoz. Alapértelmezés szerint a blokk gyorsítótára be van kapcsolva a tábla összes családja számára. Ha letiltotta a blokk-gyorsítótárat a család számára, és be kell kapcsolni azt, használja az Alter parancsot a hbase-rendszerhéjból.

   Ezek a konfigurációk biztosítják, hogy az adattárolók elérhetők legyenek a gyorsítótárban, és hogy a legutóbbi adatműveletek ne legyenek tömörítve. Ha a forgatókönyvben ÉLETTARTAMa lehetséges, akkor érdemes lehet a dátum-és időrétegbeli tömörítést használni. További információkért lásd: az [Apache HBase útmutatója: a lépcsőzetes tömörítés dátuma](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>A kiürítési várólista optimalizálása

Ez a tanácsadó azt jelzi, hogy a HBase kiürítéséhez szükség lehet a finomhangolásra. Előfordulhat, hogy a kiürítési kezelők jelenlegi konfigurációja nem elég nagy ahhoz, hogy az írási forgalommal kezelje, ami lelassíthatja a kiürítést.

A régió-kiszolgáló felhasználói felületén figyelje meg, hogy a kiürítési várólista 100-nál is nő-e. Ez a küszöbérték azt jelzi, hogy a kiürítések lassúak, és előfordulhat, hogy be kell hangolni a   `hbase.hstore.flusher.count` konfigurációt. Alapértelmezés szerint az érték 2. Győződjön meg arról, hogy a maximális ürítési szálak nem növekednek 6 felett.

Emellett ellenőrizze, hogy van-e javaslata a régiók darabszámának finomhangolásához. Ha igen, javasoljuk, hogy próbálja meg a régió finomhangolását, és ellenőrizze, hogy ez segít-e a gyorsabb kiürítésben. Ellenkező esetben a kiürítési szálak finomhangolása segíthet.

## <a name="region-count-tuning"></a>Régió darabszámának finomhangolása

A régiók száma hangolási tanácsadó azt jelzi, hogy a HBase letiltotta a frissítéseket, és a régiók száma nagyobb lehet, mint az optimálisan támogatott halom mérete. Beállíthatja a halom méretét, a memstore méretét és a régiók darabszámát.

Példa:

- Tegyük fel, hogy a régió kiszolgálójának mérete 10 GB. Alapértelmezés szerint a `hbase.hregion.memstore.flush.size` : `128M` . Az alapértelmezett értéke a következő: `hbase.regionserver.global.memstore.size` `0.4` . Ami azt jelenti, hogy a 10 GB-os, 4 GB-os kiosztása memstore (globális).

- Tegyük fel, hogy az írási terhelés egyenletes eloszlásban van az összes régióban, és feltételezi, hogy minden régió legfeljebb 128 MB-ot vesz igénybe. Ez a beállítás a régiók maximális száma `32` . Ha egy adott régió-kiszolgáló 32-régióra van konfigurálva, a rendszer jobban elkerüli a blokkoló frissítéseket.

- Ezekkel a beállításokkal a régiók száma 100. A 4 GB-os globális memstore mostantól 100 régión belül oszlik meg. Így gyakorlatilag minden egyes régió 40 MB-ot kap a memstore. Ha az írások egységesek, a rendszer gyakori kiürítést végez, és a rendelés kisebb méretét < 40 MB-ot. A sok öblítő szál növelheti a kiürítési sebességet `hbase.hstore.flusher.count` .

A tanácsadó azt jelenti, hogy érdemes átgondolni, hogy a kiszolgálók száma kiszolgálónként, a halom mérete és a globális memstore-méret konfigurációval együtt, valamint a ürítési szálak finomhangolásával elkerülhető legyen a frissítések blokkolása.

## <a name="compaction-queue-tuning"></a>Tömörítési várólista finombeállítása

Ha a HBase tömörítési várólistája több mint 2000-ra nő, és rendszeres időközönként történik, a tömörítési szálakat nagyobb értékre növelheti.

Ha túl sok fájl áll rendelkezésre a tömörítéshez, akkor a fájlok az Azure fájlrendszerrel való működésével kapcsolatos további heap-használatot okozhatnak. Így jobb a lehető leggyorsabban végrehajtani a tömörítést. A régebbi fürtök időnként a szabályozáshoz kapcsolódó tömörítési konfigurációk lassabb tömörítési sebességet okozhatnak.

Győződjön meg a konfigurációk `hbase.hstore.compaction.throughput.lower.bound` és a `hbase.hstore.compaction.throughput.higher.bound` . Ha már az 50 m-es és a 100M-es értékre van állítva, hagyja őket. Ha azonban a beállításokat alacsonyabb értékre konfigurálta (amely a régebbi fürtök esetében volt), módosítsa a korlátot 50M-re és 100M-re.

A konfigurációk `hbase.regionserver.thread.compaction.small` és `hbase.regionserver.thread.compaction.large` (az alapértelmezett érték 1).
A konfiguráció maximális értékét 3-nál kisebb értékre korlátozza.

## <a name="full-table-scan"></a>Teljes táblázatos vizsgálat

A teljes tábla-ellenőrzési tanácsadó azt jelzi, hogy a kiállított ellenőrzések több mint 75%-a teljes tábla/régió vizsgálat. A lekérdezési teljesítmény javítása érdekében újra megtekintheti a kód meghívásának módját. Vegye figyelembe az alábbi eljárásokat:

* Állítsa be az egyes ellenőrzések megfelelő indítási és leállítási sorát.

* Használja a **MultiRowRangeFilter** API-t, hogy a különböző tartományokat egy vizsgálati hívásban lehessen lekérdezni. További információ: [MULTIROWRANGEFILTER API-dokumentáció](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* Ha teljes táblázatra vagy régióra vonatkozó vizsgálatra van szüksége, ellenőrizze, hogy van-e lehetőség a gyorsítótár használatának elkerülésére a lekérdezéseknél, így előfordulhat, hogy a gyorsítótárat használó egyéb lekérdezések nem zárhatja ki a melegen lévő blokkokat. Annak biztosítása érdekében, hogy a vizsgálatok ne használják a gyorsítótárat, a kódban a **setCaching (false)** beállítást használja a **Scan** API-val: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Következő lépések

[Apache-HBase optimalizálása a Ambari használatával](../optimize-hbase-ambari.md)
