---
title: Interaktív lekérdezési fürt méretezési útmutatója az Azure HDInsight
description: Interaktív lekérdezés-méretezési útmutató az Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933066"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Interaktív lekérdezési fürt méretezési útmutatója az Azure HDInsight

Ez a dokumentum ismerteti a HDInsight interaktív lekérdezési fürt (LLAP) méretezését egy tipikus számítási feladathoz az ésszerű teljesítmény elérése érdekében. Az ebben a dokumentumban ismertetett javaslatok általános és konkrét számítási feladatokhoz speciális hangolásra lehet szükség.

## <a name="default-vm-types-for-interactive-query"></a>Alapértelmezett VM-típusok interaktív lekérdezéshez

| Csomópont típusa | Példány | Méret |
|---|---|---|
| Head | D13 v2 | 8 VCPU, 56 – GB RAM, 400 GB SSD |
| Feldolgozói | D14 v2 | 16 VCPU, 112 – GB RAM, 800 GB SSD |
| ZooKeeper | A4 v2 | 4 VCPU, 8 GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Ajánlott konfigurációk

Az ajánlott konfigurációk értéke a D14 v2 típusú feldolgozó csomóponton alapul.

| Kulcs | Érték | Leírás |
|---|---|---|
| fonal. nodemanager. Resource. Memory – MB | 102400 (MB) | Teljes memória (MB-ban megadva) a csomóponton lévő összes fonal-tárolóhoz. |
| fonal. Scheduler. maximális kiosztása – MB | 102400 (MB) | Az RM-on lévő összes Container-kérelem maximális kiosztása (MB). Az ennél az értéknél nagyobb memória-kérelmek nem lépnek érvénybe. |
| fonal. Scheduler. maximális – foglalás – virtuális mag | 12 |A CPU-magok maximális száma a Resource Manager összes tároló-kérelméhez. Az ennél az értéknél nagyobb kérelmek nem lépnek érvénybe. |
| fonál. Scheduler. Capacity. root. llap. Capacity | 90% | A LLAP-várólista szál-kapacitásának kiosztása.  |
| kaptár. Kiszolgáló2. TEZ. Sessions. per. default. üzenetsor | number_of_worker_nodes |A kaptár. Kiszolgáló2. TEZ. default. Queues nevű üzenetsor-munkamenetek száma. Ez a szám a lekérdezési koordinátorok (TEZ AMs) számának felel meg. |
| Tez. am. Resource. Memory. MB | 4096 (MB) | A TEZ-AppMaster által használandó memória mennyisége (MB). |
| kaptár. TEZ. Container. size | 4096 (MB) | A TEZ-tároló mérete MEGABÁJTban megadva. |
| hive.llap.daemon.num.executors | 12 | A végrehajtók száma LLAP démonban. |
| kaptár. llap. IO. szálkészlet munkaszála belépett. size | 12 | A szálak készletének mérete a végrehajtók számára. |
| kaptár. llap. Daemon. fonal. Container. MB | 86016 (MB) | Az egyes LLAP-démonok által használt teljes memória (MB) (memória/démon).|
| kaptár. llap. IO. Memory. size | 409600 (MB) | A gyorsítótár mérete (MB/LLAP Daemon) engedélyezve van az SSD-gyorsítótár. |
| kaptár. Auto. convert. JOIN. noconditionaltask. size | 2048 (MB) | a memória mérete MB-ban a térképhez való csatlakozáshoz. |

## <a name="llap-daemon-size-estimations"></a>LLAP démonok méretének becslése  

### <a name="yarnnodemanagerresourcememory-mb"></a>fonal. nodemanager. Resource. Memory – MB

Ez az érték azt jelzi, hogy az egyes csomópontokon a FONALas tárolók legfeljebb MB memóriát használnak. Meghatározza, hogy a memória FONALa milyen mértékben használható ezen a csomóponton, így ennek az értéknek kisebbnek kell lennie az adott csomóponton lévő teljes memóriánál.

Állítsa be ezt az értéket = [teljes fizikai memória a csomóponton] – [memória az operációs rendszer + egyéb szolgáltatások számára].

Ezt az értéket a rendelkezésre álló RAM 90%-ában ajánlott beállítani. A D14 v2 esetében az ajánlott érték **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>fonal. Scheduler. maximális kiosztása – MB

Ez az érték azt jelzi, hogy a Resource Manager összes tároló-kérelmének maximális kiosztása MB-ban. A megadott értéknél nagyobb memória-kérelmek nem lépnek érvénybe. A Resource Manager csak a tárolók számára biztosít memóriát, `yarn.scheduler.minimum-allocation-mb` és nem lépheti túl a által megadott méretet `yarn.scheduler.maximum-allocation-mb` . Ez az érték nem lehet nagyobb, mint a csomópont teljes megadott memóriája, amelyet a ad meg `yarn.nodemanager.resource.memory-mb` .

A D14 v2 munkavégző csomópontok esetében az ajánlott érték **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>fonal. Scheduler. maximális – foglalás – virtuális mag

Ez a konfiguráció jelzi a virtuális CPU-magok maximális számát a Resource Manager összes tárolóra vonatkozó kérelme esetében. Az ennél a konfigurációnál magasabb érték kérése nem lép érvénybe. Ez a konfiguráció a fonal-ütemező globális tulajdonsága. A LLAP Daemon-tároló esetében ez az érték az összes rendelkezésre álló virtuális mag (virtuális mag) 75%-ában állítható be. A fennmaradó 25%-ot le kell foglalni a munkavégző csomópontokon futó NodeManager, DataNode és más szolgáltatásokhoz.  

A D14 v2 munkavégző csomópontok esetében 16 virtuális mag és 16 virtuális mag 75% adható meg. Ezért a LLAP Daemon-tároló ajánlott értéke **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>kaptár. Kiszolgáló2. TEZ. Sessions. per. default. üzenetsor

Ez a konfigurációs érték határozza meg, hogy hány TEZ-munkamenetet kell elindítani párhuzamosan a által megadott várólistákhoz `hive.server2.tez.default.queues` . Az érték a TEZ AMs (lekérdezési koordinátorok) számának felel meg. Azt javasoljuk, hogy a munkavégző csomópontok számának meg kell egyeznie egy TEZ-csomóponttal. Az AMs TEZ száma nagyobb lehet, mint a LLAP démon-csomópontok száma. Elsődleges feladata a lekérdezés végrehajtásának koordinálása és a lekérdezési terv töredékek kiosztása a megfelelő LLAP-démonokhoz a végrehajtáshoz. Azt javasoljuk, hogy több LLAP Daemon-csomópont többszörösét használja a nagyobb átviteli sebesség eléréséhez.  

Az alapértelmezett HDInsight-fürt négy LLAP-démont futtat négy munkavégző csomóponton, ezért az ajánlott érték **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>Tez. am. Resource. Memory. MB, kaptár. TEZ. Container. size

`tez.am.resource.memory.mb` meghatározza a TEZ-alkalmazás fő méretét.  
Az ajánlott érték **4096 MB**.

`hive.tez.container.size` meghatározza a TEZ-tárolóhoz megadott memória mennyiségét. Ezt az értéket meg kell adni a fonal minimális mérete ( `yarn.scheduler.minimum-allocation-mb` ) és a fonal maximális mérete () között `yarn.scheduler.maximum-allocation-mb` .  
Ajánlott **4096 MB**-ra beállítani.  

Az általános szabály, hogy a processzorok mennyisége meghaladja a processzorok mennyiségét, a tárolók esetében pedig egy processzort kell figyelembe venni. `Rreserve` a LLAP démon memóriájának megadása előtt a csomóponton található AMs-TEZ számának memóriája. Ha például két TEZ AMs-t használ (4 GB-ot) egy csomóponton, 82 GB-ot kell kiadnia a 90 GB-nál a LLAP démon számára, és 8 GB-ot kell kiszolgálni két TEZ AMs-hez.

### <a name="yarnschedulercapacityrootllapcapacity"></a>fonál. Scheduler. Capacity. root. llap. Capacity

Ez az érték a LLAP-várólista számára megadott kapacitás százalékos arányát jelzi. A Hdinsight interaktív lekérdezési fürt a LLAP-várólista teljes kapacitásának 90%-át, a fennmaradó 10%-ot pedig az alapértelmezett üzenetsor értékre állítja a többi tároló kiosztása esetén.  
A D14v2 Worker-csomópontok esetében a javasolt érték a LLAP-várólista esetében **90** .

### <a name="hivellapdaemonyarncontainermb"></a>kaptár. llap. Daemon. fonal. Container. MB

A LLAP-démon teljes memóriájának mérete a következő összetevőktől függ:

* A fonal-tároló méretének ( `yarn.scheduler.maximum-allocation-mb` , `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` ) konfigurálása

* A végrehajtók által használt halom memória (xmx)

    A teljes belmagasság mérete után rendelkezésre álló RAM mennyisége.  
    D14 v2, HDI 4,0 – ez az érték (86 GB-6 GB) = 80 GB  
    D14 v2, HDI 3,6 – Ez az érték (84 GB-6 GB) = 78 GB

* Memóriában tárolt memóriabeli gyorsítótár (kaptár. llap. IO. Memory. size)

* Replikahelyek

    Ez a Java-alapú virtuális gépek terhelésének (metaspace, szálak verem, GC-adatstruktúrák stb.) része. Ez a rész a halom méretének (xmx) körülbelül 6%-át fogja észlelni. Ahhoz, hogy a biztonságosabb oldalon legyen, a LLAP-démon teljes méretének 6%-ában lehet kiszámítani. Mivel lehetséges, hogy az SSD-gyorsítótár engedélyezve van, lehetővé teszi, hogy a LLAP démon az összes elérhető memóriában lévő lemezterületet használja csak a halom számára.  
    A D14 v2 esetében az ajánlott érték a ceil (86 GB x 0,06) ~ = **6 GB**.  

Memória/démon = [memóriában tárolt gyorsítótár mérete] + [halom mérete] + [belmagasság].

A következőképpen számítható ki:

Tez AM memória/node = [(TEZ AMs száma/LLAP démon-csomópontok száma) * TEZ AM size].
LLAP Daemon-tároló mérete = [90% a fonal maximális tárolójának memóriája] – [TEZ AM memória/csomópont].

A D14 v2 Worker csomópontnál a HDI 4,0 – a javasolt érték a következő: (90-(1/1 * 4 GB)) = **86 GB**.
(A HDI 3,6 esetében az ajánlott érték a **84 GB** , mert a csúszka esetében 2 GB-ot kell lefoglalni.)  

### <a name="hivellapiomemorysize"></a>kaptár. llap. IO. Memory. size

Ez a konfiguráció a LLAP Daemon gyorsítótárként elérhető memória mennyisége. A LLAP démonok használhatnak SSD-t gyorsítótárként. A Setting `hive.llap.io.allocator.mmap` = True érték lehetővé teszi az SSD gyorsítótárazását. A D14 v2 verziója ~ 800 GB SSD, az SSD-gyorsítótárazás pedig alapértelmezés szerint engedélyezve van az interaktív lekérdezési fürt (LLAP) számára. Úgy van konfigurálva, hogy az SSD-terület 50%-át használja a nem halom-gyorsítótárhoz.

A D14 v2 esetében az ajánlott érték **409600 MB**.  

Más virtuális gépek esetében, amelyeken nincs engedélyezve az SSD-gyorsítótárazás, hasznos lehet a rendelkezésre álló RAM része a LLAP gyorsítótárazáshoz a jobb teljesítmény érdekében. Állítsa be a memória teljes méretét a LLAP démonhoz a következőképpen:  

LLAP démon teljes memóriája = [LLAP gyorsítótár mérete] + [Heap size] + [belmagasság].

Ajánlott a gyorsítótár méretének és a munkaterhelés méretének legmegfelelőbbnek lennie.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Ez a konfiguráció szabályozza azon végrehajtók számát, akik a feladatokat párhuzamosan futtathatják LLAP Daemon-ban. Ez az érték a rendelkezésre álló virtuális mag számának, a végrehajtó által megadott memória mennyiségének, valamint a LLAP démonok által elérhető teljes memóriának a száma. Általában azt szeretnénk, hogy ez az érték minél közelebb legyen a magok számához.

A D14 v2 esetében 16 virtuális mag érhető el, de nem adható meg az összes virtuális mag. A feldolgozó csomópontok más szolgáltatásokat is futtatnak, például a NodeManager, a DataNode és a metrikák figyelőt, amely a rendelkezésre álló virtuális mag bizonyos részeit igényli. Ezt az értéket az adott csomóponton elérhető teljes virtuális mag 75%-ában lehet konfigurálni.

A D14 v2 esetében a javasolt érték a következő: (. 75 X 16) = **12**

Azt javasoljuk, hogy a végrehajtónál ~ 6 GB tárterületet foglaljon le. Állítsa be a végrehajtók számát az elérhető LLAP-démon méretétől és a rendelkezésre álló virtuális mag száma alapján.  

### <a name="hivellapiothreadpoolsize"></a>kaptár. llap. IO. szálkészlet munkaszála belépett. size

Ez az érték határozza meg a végrehajtók szál-készletének méretét. Mivel a végrehajtók a megadott módon vannak rögzítve, a végrehajtók száma a LLAP démonnál megegyező lesz.

A D14 v2 esetében ajánlott ezt az értéket **12**-re beállítani.

Ez a konfiguráció nem lehet nagyobb az `yarn.nodemanager.resource.cpu-vcores` értéknél.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>kaptár. Auto. convert. JOIN. noconditionaltask. size

Győződjön meg arról, hogy a `hive.auto.convert.join.noconditionaltask` paraméter érvénybe léptetése engedélyezve van. Ez a konfiguráció lehetővé teszi, hogy a felhasználó megadhatja a memóriában elférő táblázatok méretét a csatlakoztatáshoz. Ha az n-Way csatlakozás n-1 értékének mérete `tables/partitions` kisebb, mint a beállított érték, akkor a térképhez való csatlakozás lesz kiválasztva. Az LLAP-végrehajtó memória méretének kiszámításához a csatlakozás leképezéséhez az autoconvert küszöbértékét kell használni.

A D14 v2 esetében ajánlott ezt az értéket **2048 MB**-ra beállítani.

Javasoljuk, hogy a számítási feladatokhoz megfelelő értéket állítsa be úgy, hogy ez az érték túl alacsony legyen, nem használhatja az autoconvert funkciót. Ha túl magasra állítja a beállítást, a GC-szüneteltetést eredményezhet, ami hátrányosan befolyásolhatja a lekérdezési teljesítményt.

## <a name="next-steps"></a>Következő lépések

* [Átjáróval kapcsolatos irányelvek](gateway-best-practices.md)
* [Magabiztosabbá válni Apache TEZ Memory tuning – lépésről lépésre](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Csatlakoztatási memória méretezése a LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP – egyoldalas architektúra áttekintése](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [A kaptár LLAP](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
