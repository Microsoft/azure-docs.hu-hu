---
title: HDInsight interaktív lekérdezési fürt (LLAP) méretezési útmutatója
description: LLAP méretezési útmutató
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 7df75077785c66215008e045ef0b1e451ba29f57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931112"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Az Azure HDInsight interaktív lekérdezési fürt (kaptár LLAP) méretezési útmutatója

Ez a dokumentum ismerteti a HDInsight interaktív lekérdezési fürt (kaptár LLAP-fürt) méretezését egy tipikus számítási feladathoz, amely ésszerű teljesítmény elérését biztosítja. Vegye figyelembe, hogy az ebben a dokumentumban szereplő javaslatok általános irányelvek, és konkrét számítási feladatok szükségesek a hangoláshoz.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Az Azure alapértelmezett virtuálisgép-típusai a HDInsight interaktív lekérdezési fürthöz (LLAP)**

| Csomópont típusa      | Példány | Méret     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 vCPU, 56 GB RAM, 400 GB SSD   |
| Feldolgozói   | **D14 v2**        | **16 vCPU, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 v2        | 4 vCPU, 8 GB RAM, 40 GB SSD       |

**_Megjegyzés: az összes ajánlott konfigurációs érték a D14 v2 Type Worker Node_* _-re épül.  

### <a name="_configuration"></a>_ *Konfiguráció:**    
| Konfigurációs kulcs      | Javasolt érték  | Description |
| :---        |    :----:   | :---     |
| fonal. nodemanager. Resource. Memory – MB | 102400 (MB) | Teljes memória (MB-ban megadva) a csomóponton lévő összes fonal-tárolóhoz | 
| fonal. Scheduler. maximális kiosztása – MB | 102400 (MB) | Az RM-on lévő összes Container-kérelem maximális kiosztása (MB). Az ennél az értéknél nagyobb memória-kérelmek nem lépnek érvénybe |
| fonal. Scheduler. maximális – foglalás – virtuális mag | 12 |A CPU-magok maximális száma a Resource Manager összes tároló-kérelméhez. Az ennél az értéknél nagyobb kérelmek nem lépnek érvénybe. |
| fonal. nodemanager. Resource. CPU – virtuális mag | 12 | A tárolók számára kiosztható CPU-magok száma NodeManager. |
| fonál. Scheduler. Capacity. root. llap. Capacity | 85 (%) | A llap-várólista fonal-kapacitásának kiosztása  |
| Tez. am. Resource. Memory. MB | 4096 (MB) | A TEZ AppMaster által használandó memória mérete (MB) |
| kaptár. Kiszolgáló2. TEZ. Sessions. per. default. üzenetsor | <number_of_worker_nodes> |A kaptár. Kiszolgáló2. TEZ. default. Queues nevű üzenetsor-munkamenetek száma. Ez a szám a lekérdezési koordinátorok számának felel meg (TEZ AMs) |
| kaptár. TEZ. Container. size | 4096 (MB) | Megadott TEZ-tároló mérete (MB) |
| hive.llap.daemon.num.executors | 19 | Végrehajtók száma LLAP démonban | 
| kaptár. llap. IO. szálkészlet munkaszála belépett. size | 19 | Végrehajtók szál-készletének mérete |
| kaptár. llap. Daemon. fonal. Container. MB | 81920 (MB) | Az egyes LLAP-démonok által használt teljes memória (MB) (memória/démon)
| kaptár. llap. IO. Memory. size | 242688 (MB) | A gyorsítótár mérete (MB/LLAP) démon által megadott SSD-gyorsítótár engedélyezve |
| kaptár. Auto. convert. JOIN. noconditionaltask. size | 2048 (MB) | a memória mérete MB-ban a Térkép csatlakoztatásához |

### <a name="llap-architecturecomponents"></a>**LLAP architektúra/összetevők:**  

!["LLAP architektúra/összetevők"](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP architektúra/összetevők")

### <a name="llap-daemon-size-estimations"></a>**LLAP démonok méretének becslése:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. a teljes fonal-memória kiosztásának meghatározása a csomóponton lévő összes tárolóhoz**    
Konfiguráció: **_fonál. nodemanager. Resource. memória – MB_* _  

Ez az érték azt jelzi, hogy a memória maximális mérete MB-ban, amelyet a szálak tárolói használhatnak az egyes csomópontokon. A megadott értéknek kisebbnek kell lennie, mint a csomóponton lévő fizikai memória teljes mennyisége.   
Egy csomóponton lévő összes fonal-tároló teljes memóriája (teljes fizikai memória – az operációs rendszer + más szolgáltatások esetében a memória)  
Állítsa ezt az értéket az elérhető RAM-méret ~ 90%-ában.  
A D14 v2 esetében a javasolt érték a következő: _ * 102400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. a memória maximális mennyiségének meghatározása a FONALas tárolóra vonatkozó kérelem esetében**  
Konfiguráció: **_fonal. Scheduler. maximális – foglalás – MB_* _

Ez az érték azt jelzi, hogy a Resource Manager összes tároló-kérelmének maximális kiosztása MB-ban. A megadott értéknél nagyobb memória-kérelmek nem lépnek érvénybe. A Resource Manager a tárolók számára biztosít memóriát _yarn. Scheduler. minimum-kiosztási MB *-onként, és nem lépheti túl a fonalak által megadott méretet. *ütemező maximális kiosztása (MB*). A megadott érték nem lehet nagyobb, mint a *szálak. nodemanager. Resource. Memory-MB-* ban megadott csomópont összes tárolójának teljes megadott memóriája.    
A D14 v2 munkavégző csomópontok esetében az ajánlott érték **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. a virtuális mag maximális mennyiségének meghatározása a FONALas tárolóra vonatkozó kérelem esetében**  
Konfiguráció: **_fonal. Scheduler. maximum-foglalás-virtuális mag_* _  

Ez az érték jelzi a virtuális CPU-magok maximális számát a Resource Manager összes tárolóra vonatkozó kérelme esetében. Ennél az értéknél nagyobb virtuális mag kérése nem lép érvénybe. Ez a fonal-ütemező globális tulajdonsága. A LLAP Daemon-tároló esetében ez az érték az összes rendelkezésre álló virtuális mag 75%-ában állítható be. A fennmaradó 25%-ot le kell foglalni a munkavégző csomópontokon futó NodeManager, DataNode és más szolgáltatásokhoz.  
A LLAP Daemon-tárolóban 16 virtuális mag van a D14 v2 virtuális gépeken, és a 16 virtuális mag 75%-a is használható.  
A D14 v2 esetében az ajánlott érték a következő: _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. egyidejű lekérdezések száma**  
Configuration: **_kaptár. Kiszolgáló2. TEZ. Sessions. per. default. üzenetsor_* _

Ez a konfigurációs érték határozza meg a párhuzamosan indítható TEZ-munkamenetek számát. Ezek a TEZ-munkamenetek a "kaptár. Kiszolgáló2. TEZ. default. Queues" által meghatározott várólistákhoz lesznek elindítva. A TEZ AMs (lekérdezési koordinátorok) számának felel meg. A munkavégző csomópontok számának meg kell egyeznie. Az AMs TEZ száma nagyobb lehet, mint a LLAP démon-csomópontok száma. A TEZ elsődleges feladata a lekérdezés végrehajtásának koordinálása és a lekérdezési terv töredékek kiosztása a megfelelő LLAP-démonokhoz a végrehajtáshoz. Ezt az értéket több LLAP Daemon-csomópont többszörösére is megtarthatja a nagyobb átviteli sebesség elérése érdekében.  

Az alapértelmezett HDInsight-fürt négy LLAP-démont futtat négy munkavégző csomóponton, ezért az ajánlott érték a _ * 4 * *.  

**Ambari felhasználói felületi csúszka a kaptár konfigurációs változóhoz `hive.server2.tez.sessions.per.default.queue` :**

!["Az egyidejű lekérdezések maximális száma" LLAP](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "Az egyidejű lekérdezések maximális száma LLAP")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. TEZ-tároló és TEZ-alkalmazás fő mérete**    
Konfiguráció: **_TEZ. am. Resource. Memory. MB, kaptár. TEZ. Container. size_* _  

_tez. am. Resource. Memory. MB * – a TEZ-alkalmazás fő méretének meghatározása.  
Az ajánlott érték **4096 MB**.
   
*kaptár. TEZ. Container. size* – a TEZ-tárolóhoz megadott memória mennyiségét határozza meg. Ezt az értéket meg kell adni a fonal minimális mérete (*fonál. ütemező. minimális kiosztási MB*) és a fonalak maximális mérete (*fonal. ütemező, maximális kiosztási MB*) között. A LLAP démon végrehajtói ezt az értéket használják a memóriahasználat korlátozására a végrehajtón.  
Az ajánlott érték **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. LLAP-várólista kapacitásának kiosztása**   
Konfiguráció: **_fonal. Scheduler. Capacity. root. llap. Capacity_* _  

Ez az érték a llap-várólista számára megadott kapacitás százalékos arányát jelzi. A kapacitás kiosztása eltérő értékekkel rendelkezhet a különböző számítási feladatokhoz, attól függően, hogy a rendszer hogyan konfigurálja a szál-várólistákat. Ha a munkaterhelés írásvédett művelet, akkor a kapacitás 90%-ában kell működnie. Ha azonban a számítási feladat a felügyelt táblákat használó frissítési/törlési/egyesítési műveletek keveréke, akkor azt javasoljuk, hogy a llap-várólista kapacitásának 85%-át adja meg. A fennmaradó 15%-os kapacitást más feladatok is felhasználhatják, például a tömörítést, stb. a tárolók alapértelmezett várólistából való lefoglalására. Az alapértelmezett várólistán lévő feladatok nem fogják megfossni a FONALak erőforrásait.    

A D14v2 Worker-csomópontok esetében a llap-várólista javasolt értéke a következő: _ * 85 * *.     
(ReadOnly számítási feladatokhoz akár 90-ig is növelhető.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP Daemon-tároló mérete**    
Konfiguráció: **_kaptár. llap. Daemon. fonal. Container. MB_* _  
   
A LLAP démon minden munkavégző csomóponton szál-tárolóként fut. A LLAP Daemon-tároló teljes memóriájának mérete az alábbi tényezőktől függ:    
– A FONALak tárolójának mérete (fonál. Scheduler. minimum-kiosztási MB, fonal. Scheduler. maximális kiosztási MB, fonal. nodemanager. Resource. Memory-MB)
*  A csomóponton található AMs TEZ száma
*  A csomópont összes tárolója és a LLAP-várólista kapacitása számára konfigurált teljes memória  

Az TEZ Application Masters (TEZ AM) által igényelt memória a következőképpen számítható ki:.  
A TEZ lekérdezési koordinátorként működik, és az AMs-TEZ számát az egyidejű lekérdezések száma alapján kell konfigurálni. Elméletileg egy TEZ-t is megvizsgálhat munkavégző csomópontként. Előfordulhat azonban, hogy egy munkavégző csomóponton több TEZ is látható. Számítás céljára a TEZ AMs egységes eloszlását feltételezzük az összes LLAP Daemon-csomópont/feldolgozó csomóponton.
Ajánlott, hogy TEZ 4 GB memóriával rendelkezzen.  

Az AMS = a kaptár konfiguráció ***kaptár. Kiszolgáló2. TEZ. Sessions./. default. üzenetsor** _ TEZ megadott értéke.  
A LLAP démon-csomópontok száma = a Ambari felhasználói felületén a env változó _*_num_llap_nodes_for_llap_daemons_*_ megadva.  
Tez AM Container size = a TEZ config _*_TEZ. am. Resource. Memory. MB_*_ által megadott érték.  

Tez am memória/Node = _ *(** ceil **(** **/** LLAP-démoni csomópontok száma TEZ **)** **x** TEZ am tároló mérete **)**  
A D14 v2 esetében az alapértelmezett konfiguráció négy TEZ AMs-t és négy LLAP démon-csomópontot tartalmaz.  
Tez AM memória/node = (ceil (4/4) x 4 GB) = 4 GB

A LLAP-várólista számára elérhető teljes memória munkavégző csomóponton a következőképpen számítható ki:  
Ez az érték a csomóponton lévő összes fonal-tároló számára elérhető memória teljes mennyiségétől (fonál.*nodemanager. Resource. Memory-MB*) és a llap-várólista számára konfigurált kapacitás százalékával (*fonál. Scheduler. Capacity. root. llap. Capacity*) függ.  
A LLAP-várólista teljes memóriája a munkavégző csomóponton = teljes memória az összes fonal-tároló számára a LLAP-várólista kapacitása x százalékában.  
A D14 v2 esetében ez az érték (100 GB x 0,85) = 85 GB.

A LLAP Daemon-tároló mérete a következőképpen számítható ki:

**LLAP Daemon Container Size = (a LLAP-várólista teljes memóriája egy workernode) – (TEZ AM memória/csomópont) – (a szolgáltatás fő tárolójának mérete)**  
Az egyik munkavégző csomóponton csak egy szolgáltatási főkiszolgáló (LLAP szolgáltatás) található a fürtön. Számítás céljára egy munkavégző csomóponton egy szolgáltatási főkiszolgálót kell figyelembe venni.  
A D14 v2 Worker csomópontnál a HDI 4,0 – a javasolt érték (85 GB-4 GB-1 GB)) = **80 GB**   
(A HDI 3,6 esetében az ajánlott érték a **79 GB** , mert a Slider am esetében további ~ 2 GB-ot kell lefoglalni.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. a végrehajtók számának meghatározása LLAP démon alapján**  
Konfiguráció: **_hive.llap.daemon.num.executors_* _, _*_kaptár. llap. IO. szálkészlet munkaszála belépett. size_*_

_*_hive.llap.daemon.num.executors_*_:   
Ez a konfiguráció szabályozza azon végrehajtók számát, akik a feladatokat párhuzamosan futtathatják LLAP Daemon-ban. Ez az érték a virtuális mag számától, a végrehajtó által használt memória mennyiségétől és a LLAP Daemon-tárolóhoz elérhető teljes memóriától függ.    A végrehajtók száma feldolgozható a rendelkezésre álló virtuális mag 120%-ában a munkavégző csomóponton. Ezt azonban úgy kell módosítani, hogy az nem felel meg a memóriára vonatkozó követelményeknek a végrehajtó és a LLAP Daemon-tároló mérete alapján.

Minden végrehajtó egyenértékű egy TEZ-tárolóval, és a memória 4GB (TEZ-tároló mérete) használatával használható. A LLAP démon összes végrehajtója ugyanazt a halom memóriát használja. Feltételezve, hogy nem minden végrehajtó hajt végre memória-igényű műveletet egyszerre, a TEZ-tároló méretének 75%-át (4 GB) kell megfontolnia végrehajtóként. Így növelheti a végrehajtók számát úgy, hogy az egyes végrehajtók számára kevesebb memóriát (például 3 GB) biztosít a párhuzamosságok megnövekedéséhez. Javasoljuk azonban, hogy ezt a beállítást a cél számítási feladathoz hangolja.

A D14 v2 virtuális gépeken 16 virtuális mag van.
A D14 v2 esetében a (z) számú végrehajtó javasolt értéke (16 virtuális mag x 120%) ~ = _ *19** minden munkavégző csomóponton, figyelembe véve a következőt: 3gb/végrehajtó.

**_kaptár. llap. IO. szálkészlet munkaszála belépett. size_*_: ez az érték határozza meg a szálak készletének méretét a végrehajtók számára. Mivel a végrehajtók a megadott módon vannak rögzítve, a végrehajtók száma a LLAP démonnál megegyező lesz. A D14 v2 esetében az ajánlott érték a _* 19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. a LLAP Daemon-gyorsítótár méretének meghatározása**  
Konfiguráció: **_kaptár. llap. IO. Memory. size_* _

A LLAP Daemon-tároló memóriája a következő összetevőkből áll: _ Fő helyiség
*  A végrehajtók által használt halom memória (xmx)
*  Memóriában tárolt gyorsítótár (a terhelés nélküli memória mérete, nem alkalmazható, ha az SSD-gyorsítótár engedélyezve van)
*  Memóriában tárolt gyorsítótár metaadatainak mérete (csak akkor alkalmazható, ha engedélyezve van az SSD-gyorsítótár)

**Belmagasság mérete**: Ez a méret a Java virtuális gép terhelésének (metaspace, szálak verem, GC-adatstruktúrák stb.) használatos memóriában tárolt részét jelzi. Ez a terhelés általában a halom méretének (xmx) 6%-a. A biztonságosabb oldalon ez az érték a LLAP teljes memória-méretének 6%-ában számítható ki.  
A D14 v2 esetében a javasolt érték a ceil (80 GB x 0,06) ~ = **4 GB**.  

**Halom mérete (xmx)**: az összes végrehajtó számára elérhető halom memória mennyisége.
Halom teljes mérete = a végrehajtók száma x 3 GB  
A D14 v2 esetében ez az érték 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

!["LLAP halom mérete"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "LLAP halom mérete")

Ha az SSD-gyorsítótár le van tiltva, a memórián belüli gyorsítótár olyan memória mennyisége, amely a LLAP Daemon-tároló méretétől számítva a belmagasság méretének és a halom méretének megkezdése után marad.

A gyorsítótár méretének kiszámítása eltérő, ha engedélyezve van az SSD-gyorsítótár.
A *kaptár. llap. IO. lefoglalása. mmap* = True beállítása lehetővé teszi az SSD gyorsítótárazást.
Ha engedélyezve van az SSD-gyorsítótár, a rendszer a memória egy részét fogja használni az SSD-gyorsítótár metaadatainak tárolására. A metaadatok tárolása a memóriában történik, és az SSD-gyorsítótár mérete várhatóan ~ 8% lesz.   
SSD-gyorsítótár memóriában tárolt metaadatainak mérete = LLAP Daemon-tároló mérete – (fő helyiség + halom mérete)  
A D14 v2 esetében, a HDI 4,0, az SSD gyorsítótár memóriában tárolt metaadatainak mérete = 80 GB-(4 GB + 57 GB) = **19 GB**  
A D14 v2 esetében, a HDI 3,6, az SSD gyorsítótár memóriában tárolt metaadatainak mérete = 79 GB-(4 GB + 57 GB) = **18 GB**

Az SSD gyorsítótár metaadatainak tárolásához rendelkezésre álló memória mérete miatt a támogatott SSD-gyorsítótár mérete kiszámítható.  
Memóriabeli metaadatok mérete az SSD-gyorsítótárban = LLAP Daemon-tároló mérete – (fő szoba + halom mérete) = 19 GB     
SSD-gyorsítótár mérete = memóriabeli metaadatok mérete SSD-gyorsítótárhoz (19 GB)/0,08 (8 százalék)  

A D14 v2 és a HDI 4,0 esetében az ajánlott SSD-gyorsítótár mérete = 19 GB/0,08 ~ = **237 GB**  
A D14 v2 és a HDI 3,6 esetében az ajánlott SSD-gyorsítótár mérete = 18 GB/0,08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. a térképhez való csatlakozás memóriájának módosítása**   
Konfiguráció: **_kaptár. Auto. convert. JOIN. noconditionaltask. size_* _

Győződjön meg arról, hogy a paraméter érvénybe léptetéséhez _hive. Auto. convert. JOIN. noconditionaltask * engedélyezve van.
Ez a konfiguráció határozza meg a kaptár-optimalizáló által a MapJoin kiválasztásának küszöbértékét, amely úgy véli, hogy a többi végrehajtótól származó memória előfizetése nagyobb mozgásteret biztosít a memóriában tárolt kivonatoló táblák számára, így több térképhez csatlakozhat. Ha a végrehajtói 3GB-t használ, ez a méret a 3GB-re is felhasználható, de a többi művelet alapján a rendezési pufferek, a véletlenszerű pufferek stb. is használhatók.   
Így a D14 v2 esetében a végrehajtónál 3 GB memóriával kell beállítani ezt az értéket **2048 MB**-ra.  

(Megjegyzés: ennek az értéknek szüksége lehet a munkaterheléshez megfelelő módosításokra. Az érték túl alacsonyra állítása nem használható az autoconvert funkció használata esetén. Ha túl magasra állítja a beállítást, a memóriához tartozó kivételek vagy a GC-felfüggesztések miatt nem lehet negatív teljesítményt okozni.)  

#### <a name="11-number-of-llap-daemons"></a>**11. LLAP-démonok száma**
Ambari környezeti változók: **_num_llap_nodes, num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes** – meghatározza a kaptár llap szolgáltatás által használt csomópontok számát, ez magában foglalja a llap Daemon, a llap Service Master és a TEZ Application Master (TEZ am) rendszerű csomópontokat is.  

!["A LLAP szolgáltatás csomópontjainak száma"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "A LLAP szolgáltatás csomópontjainak száma")  

**num_llap_nodes_for_llap_daemons** – a csak a llap démonokhoz használt csomópontok megadott száma. A LLAP Daemon-tároló méretei a maximálisan illeszkedő csomópontra vannak állítva, így az egyes csomópontokon egy LLAP démont fog eredményezni.

!["A LLAP-démonok száma"](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "LLAP-démonok csomópontjainak száma")

Az interaktív lekérdezési fürtben a munkavégző csomópontok számával megegyező értékeket érdemes megtartani.

### <a name="considerations-for-workload-management"></a>**A számítási feladatok kezelésével kapcsolatos megfontolások**  
Ha engedélyezni szeretné a LLAP számítási feladatait, győződjön meg arról, hogy elegendő kapacitással rendelkezik a számítási feladatok kezeléséhez, hogy az a várt módon működjön. A számítási feladatok kezelése megköveteli az egyéni FONALak várólistájának konfigurálását, amely a `llap` várólista mellett található. Ügyeljen arra, hogy a számítási feladatok követelményeinek megfelelően a teljes fürterőforrás-kapacitást a llap-várólista és a munkaterhelés-felügyeleti várólista között osztja el.
A számítási feladatok kezelése egy erőforrás-csomag aktiválása esetén TEZ (TEZ AMs).
Ne feledje:

* Az TEZ egy erőforrásterv aktiválásával elindított AMs az által megadott módon használja az erőforrásokat a munkaterhelés-felügyeleti várólistából `hive.server2.tez.interactive.queue` .  
* Az AMs TEZ száma az erőforrás-csomagban megadott értéktől függ `QUERY_PARALLELISM` .  
* Ha a munkaterhelés-kezelés aktív, a rendszer nem fogja használni a TEZ AMs-t a llap-várólistán. A lekérdezési koordinációhoz csak az TEZ AMs-t használja a rendszer a munkaterhelés-felügyeleti várólistából. Ha a munkaterhelés-kezelés le van tiltva, a rendszer az TEZ AMs-t `llap` használja a várólistán.
 
Például: teljes szektorcsoport kapacitása = 100 GB memória, osztva a LLAP, a számítási feladatok kezelésével és az alapértelmezett várólistákkal a következőképpen:
 - llap-várólista kapacitása = 70 GB
 - Munkaterhelés-kezelési várólista kapacitása = 20 GB
 - Alapértelmezett várólista kapacitása = 10 GB

A számítási feladatok felügyeleti várólistájának kapacitása 20 GB, az erőforrás-csomag `QUERY_PARALLELISM` öt értéket határozhat meg, ami azt jelenti, hogy a számítási feladatok kezelése öt TEZ AMs-t indít el egyenként 4 GB méretű tárolóval. Ha `QUERY_PARALLELISM` a nagyobb a kapacitásnál, előfordulhat, hogy néhány TEZ AMs-stop válaszol `ACCEPTED` . A Hiveserver2 Interactive nem tud beküldeni lekérdezési töredékeket a nem állapotú TEZ AMs-nek `RUNNING` .


#### <a name="next-steps"></a>**További lépések**
Ha ezek az értékek nem oldják meg a problémát, látogasson el az alábbiak egyikére...

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Csatlakozás az [@AzureSupport](https://twitter.com/azuresupport) Azure-Közösség a megfelelő erőforrásokhoz való csatlakoztatásával – a hivatalos Microsoft Azure fiókkal – a felhasználói élmény javítása érdekében: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.  

* ##### <a name="other-references"></a>**Egyéb referenciák:**
  * [Egyéb LLAP tulajdonságainak konfigurálása](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [A kaptár-kiszolgáló halom méretének konfigurálása](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Csatlakoztatási memória méretezése a LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez-végrehajtó motor tulajdonságai](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [A kaptár LLAP](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)