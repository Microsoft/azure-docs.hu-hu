---
title: Az Azure-alkalmazások teljesítményének javítása az Advisor szolgáltatással
description: Az üzleti szempontból kritikus fontosságú alkalmazások sebességének és reagálásának javítása érdekében Azure Advisor teljesítményre vonatkozó javaslatokat használhat.
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 69fd86226375073c71903af5704ef3a635eeaffb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579924"
---
# <a name="improve-the-performance-of-azure-applications-by-using-azure-advisor"></a>Az Azure-alkalmazások teljesítményének növelése Azure Advisor használatával

A Azure Advisor teljesítményével kapcsolatos javaslatok segíthetnek az üzleti szempontból kritikus fontosságú alkalmazások sebességének és reagálásának javításában. Az Advisor teljesítményére vonatkozó javaslatokat az Advisor irányítópult **teljesítmény** lapján érheti el.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Csökkentse a DNS-alapú élettartamot a Traffic Manager-profilon, hogy gyorsabban átadja a feladatokat a gyors végpontoknak

Az Azure Traffic Manager profiljában használhat [élettartam (TTL) beállításokat](../traffic-manager/traffic-manager-performance-considerations.md) annak megadásához, hogy a végpontok milyen gyorsan legyenek átváltva, ha egy adott végpont nem válaszol a lekérdezésekre. Ha csökkenti a TTL-értékeket, az ügyfelek gyorsabban lesznek irányítva a működő végpontokra.

A Azure Advisor olyan Traffic Manager-profilokat azonosít, amelyekhez már van beállítva TTL. Azt javasolja, hogy az ÉLETTARTAMot 20 másodpercre vagy 60 másodpercre konfigurálja, attól függően, hogy a profil konfigurálva van-e a [Fast Failoverra](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-by-using-sql-database-advisor-temporarily-disabled"></a>Az adatbázis teljesítményének javítása SQL Database Advisor használatával (átmenetileg letiltva)

Azure Advisor egységes, összevont áttekintést nyújt az összes Azure-erőforrásra vonatkozó javaslatokról. A SQL Database Advisor integrálható az adatbázisok teljesítményének javítására vonatkozó javaslatok bevezetéséhez. SQL Database Advisor a használati előzmények elemzésével értékeli az adatbázisok teljesítményét. Ezután ajánlásokat nyújt az adatbázis tipikus számítási feladatainak futtatásához.

> [!NOTE]
> A javaslatok beszerzése előtt az adatbázist körülbelül egy hétig kell használni, és ezen a héten belül bizonyos konzisztens tevékenységre van szükség. A SQL Database Advisor könnyebben optimalizálható a konzisztens lekérdezési mintáknál, mint a tevékenységek véletlenszerű kitörése esetén.

További információ: [SQL Database Advisor](../azure-sql/database/database-advisor-implement-performance-recommendations.md).

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>A jobb megbízhatóság és teljesítmény érdekében frissítse a Storage ügyféloldali kódtárat a legújabb verzióra

A Storage ügyféloldali kódtár SDK legújabb verziója az ügyfelek által jelentett problémákra vonatkozó javításokat tartalmaz, és proaktív módon azonosítja a MINŐSÉGBIZTOSÍTÁSi folyamaton keresztül. A legújabb verzió megbízhatóságot és teljesítmény-optimalizálást is biztosít az Azure Storage használatának általános élményét javító új funkciókkal együtt. Az Advisor olyan javaslatokat és lépéseket tartalmaz, amelyek szükségesek ahhoz, hogy az SDK legújabb verziójára frissítsen, ha elavult verziót használ. A javaslatok a következő támogatott nyelvekre vonatkoznak: C++ és .NET.

## <a name="improve-app-service-performance-and-reliability"></a>A App Service teljesítményének és megbízhatóságának javítása

A Azure Advisor a App Service élményének javítására és a kapcsolódó platform képességeinek felfedezésére vonatkozó javaslatokat integrál. Példák App Service javaslatokra:
* Azon példányok észlelése, amelyekben a memória-vagy a CPU-erőforrások kimerültek az alkalmazás-futtatókörnyezetek által, a kockázatcsökkentő beállításokkal.
* Azon példányok észlelése, amelyekben az erőforrások, például a webalkalmazások és az adatbázisok közös elhelyezésével javíthatja a teljesítményt és csökkentheti a költségeket.

További információ: [ajánlott eljárások Azure app Servicehoz](../app-service/app-service-best-practices.md).

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Felügyelt lemezek használata a lemez I/O-szabályozásának megakadályozása érdekében

Az Advisor azokat a virtuális gépeket azonosítja, amelyek egy olyan Storage-fiókhoz tartoznak, amely eléri a méretezhetőségi célját. Ez az állapot az I/O-szabályozásra fogékony virtuális gépeket tesz elérhetővé. Az Advisor azt javasolja, hogy felügyelt lemezeket használjanak a teljesítmény romlásának megelőzése érdekében.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>A virtuális gépek lemezeinek teljesítményének és megbízhatóságának javítása Premium Storage használatával

Az Advisor a szabványos lemezekkel rendelkező virtuális gépeket azonosítja, amelyek nagy mennyiségű tranzakcióval rendelkeznek a Storage-fiókban. Azt javasolja, hogy a prémium szintű lemezekre frissítsen. 

Az Azure Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az I/O-igényű számítási feladatokat futtató virtuális gépekhez. A Premium Storage fiókokat használó virtuális gépek lemezei SSD-meghajtókon tárolják az adattárolást. Az alkalmazás legjobb teljesítményének érdekében javasoljuk, hogy telepítse át a Premium Storage magas IOPS igénylő virtuálisgép-lemezeket.

## <a name="remove-data-skew-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>A lekérdezési teljesítmény növeléséhez távolítsa el az adattorzítást az Azure szinapszis Analytics-tábláiban

Az adatok eldöntése szükségtelen adatáthelyezést vagy erőforrás-szűk keresztmetszetet okozhat a számítási feladatok futtatásakor. Az Advisor több mint 15%-os eloszlási adatmennyiséget észlel. Azt javasolja, hogy terjessze újra az adatait, és nyissa meg újra a tábla terjesztési kulcsának beállításait. Ha többet szeretne megtudni a ferdeség azonosításáról és eltávolításáról, tekintse meg a következő témakört: [Hibaelhárítás](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-distribute.md#how-to-tell-if-your-distribution-column-is-a-good-choice).

## <a name="create-or-update-outdated-table-statistics-in-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Elavult tábla-statisztikák létrehozása vagy frissítése az Azure szinapszis Analytics-táblázatokban a lekérdezési teljesítmény növeléséhez

Az Advisor olyan táblákat azonosít, amelyek nem rendelkeznek naprakész [táblázat statisztikával](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-statistics.md) , és a statisztikák létrehozását és frissítését javasolja. Az Azure szinapszis Analytics lekérdezés-optimalizálási szolgáltatása naprakész statisztikákat használ a lekérdezési eredményekben szereplő sorok többségének vagy számának becsléséhez. Ezek a becslések lehetővé teszik a lekérdezés-optimalizáló számára, hogy lekérdezési tervet hozzon létre a leggyorsabb teljesítmény biztosításához.

## <a name="improve-mysql-connection-management"></a>A MySQL-kapcsolatkezelés fejlesztése

Az Advisor Analysis azt jelezheti, hogy az alkalmazás MySQL-kiszolgálóhoz való csatlakozása nem hatékony a kapcsolatok kezeléséhez. Ez az állapot szükségtelen erőforrás-felhasználást és általános, magasabb alkalmazások késését eredményezheti. A kapcsolatkezelés javítása érdekében javasoljuk, hogy csökkentse a rövid élettartamú kapcsolatok számát, valamint szüntesse meg a felesleges tétlen kapcsolatokat. Ezeket a funkciókat kiszolgálóoldali kapcsolatok Pooler, például a ProxySQL konfigurálásával végezheti el.


## <a name="scale-up-to-optimize-cache-utilization-on-your-azure-synapse-analytics-tables-to-increase-query-performance"></a>Vertikális felskálázás a gyorsítótár kihasználtságának optimalizálásához az Azure szinapszis Analytics-táblázatokban a lekérdezési teljesítmény növeléséhez

Azure Advisor észleli, hogy az Azure szinapszis Analytics-táblái magas gyorsítótár-használatot és alacsony találati arányt használnak-e. Ez az állapot nagy gyorsítótár-kizárást jelez, ami hatással lehet az Azure szinapszis Analytics-példány teljesítményére. Az Advisor azt javasolja, hogy az Azure szinapszis Analytics-példány vertikális felskálázásával biztosítsa, hogy elegendő gyorsítótári kapacitást foglaljon le a munkaterhelés számára.

## <a name="convert-azure-synapse-analytics-tables-to-replicated-tables-to-increase-query-performance"></a>Az Azure szinapszis Analytics-táblázatok replikált táblázatokra konvertálása a lekérdezési teljesítmény növeléséhez

Az Advisor olyan táblákat azonosít, amelyek nem replikálnak táblákat, de a konverzió hasznát vennék. Azt javasolja, hogy konvertálja ezeket a táblákat. A javaslatok alapja:
- A replikált tábla mérete 
- Az oszlopok száma. 
- A tábla terjesztési típusa 
- Az Azure szinapszis Analytics-táblázat partícióinak száma. 

További heurisztikus információk is megadhatók a környezethez tartozó javaslatban. További információ a javaslat meghatározásáról: [Azure szinapszis Analytics-javaslatok](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-concept-recommendations.md#replicate-tables). 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-the-latest-azure-features"></a>Telepítse át a Storage-fiókját Azure Resource Managerre a legújabb Azure-funkciók beszerzéséhez

Telepítse át a Storage-fiók központi telepítési modelljét, hogy Azure Resource Manager a következő előnyök kihasználásához:
- Sablon központi telepítései.
- További biztonsági beállítások.
- Lehetőség a GPv2-fiókra való frissítésre, hogy használhassa a legújabb Azure Storage-funkciókat. 

Az Advisor azonosítja a klasszikus üzemi modellt használó önálló tárolási fiókokat, és javasolja a Resource Manager-alapú üzemi modellbe való áttelepítést.

> [!NOTE]
> A klasszikus riasztások Azure Monitor augusztusban 2019 augusztusában lettek kivonva. Javasoljuk, hogy frissítse a klasszikus Storage-fiókját a Resource Manager használatára, hogy megőrizze a riasztási funkciókat az új platformmal. További információ: [klasszikus riasztások nyugdíjazása](../azure-monitor/alerts/monitoring-classic-retirement.md#retirement-of-classic-monitoring-and-alerting-platform).

## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Tervezze meg a Storage-fiókokat, hogy megakadályozza a maximális előfizetési korlát elérését

Az Azure-régiók előfizetése legfeljebb 250 Storage-fiókot támogat. Ha elérte ezt a korlátot, a rendszer nem tudja létrehozni a tárolási fiókokat az adott régióban/előfizetési kombinációban. Az Advisor ellenőrzi az előfizetéseket, és javaslatokat tesz arra, hogy kevesebb Storage-fiókot hozzon igénybe olyan előfizetések/régiók esetében, amelyek közel állnak a maximális korlát eléréséhez.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-p2s-use"></a>Érdemes megfontolni a VPN Gateway SKU méretének növelését a magas P2S-használat érdekében

Minden Azure VPN Gateway SKU csak a megadott számú egyidejű P2S-kapcsolatot képes támogatni. Ha a kapcsolatok száma megközelíti az átjáró korlátját, a további kapcsolódási kísérletek sikertelenek lehetnek. Az átjáró méretének növelésével több párhuzamos P2S-felhasználót is támogatni tud. Az Advisor ajánlásokat és útmutatást nyújt az átjáró méretének növeléséhez.

## <a name="consider-increasing-the-size-of-your-vpn-gateway-sku-to-address-high-cpu"></a>Vegye fontolóra VPN Gateway SKU méretének növelését a magas CPU-ra

A nagy forgalmú terhelés alatt a VPN-átjáró magas CPU miatt eldobást eredményezhet a csomagok számára. Érdemes lehet frissíteni a VPN Gateway SKU-t. A VPN-átjáró méretének növelésével biztosíthatja, hogy a kapcsolatok ne legyenek elvetve a magas CPU miatt. Az Advisor ajánlásokat biztosít a probléma proaktív megoldásához. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>A köteg méretének betöltéskor történő növelése a terhelésátvitel, az adattömörítés és a lekérdezési teljesítmény maximalizálása érdekében

Az Advisor észleli, hogy növelhető-e a terhelési teljesítmény és az átviteli sebesség a Batch méretének növelésével az adatbázisba való betöltéskor. Érdemes lehet a COPY utasítást használni. Ha nem tudja használni a COPY utasítást, érdemes lehet növelni a köteg méretét, ha olyan betöltési segédprogramokat használ, mint például a SQLBulkCopy API vagy a BCP. Az általános szabály a 100 000 és 1 000 000 közötti méretű kötegek használata. A Batch méretének növelése növeli a terhelési sebességet, az adattömörítést és a lekérdezési teljesítményt.

## <a name="co-locate-the-storage-account-in-the-same-region-to-minimize-latency-when-loading"></a>Az azonos régióban található Storage-fiók megkeresése a késés minimalizálásához a betöltéskor

Az Advisor észleli, hogy egy olyan régióból tölt be, amely eltér a dedikált SQL-készlettől. Érdemes lehet betölteni egy olyan Storage-fiókból, amely ugyanabban a régióban található, mint a dedikált SQL-készlet, hogy az adatok betöltése során csökkentse a késést. Ez a változás segít csökkenteni a késést, és növeli a terhelési teljesítményt.

## <a name="use-a-supported-kubernetes-version"></a>Támogatott Kubernetes-verzió használata

Az Advisor észleli a Kubernetes nem támogatott verzióit.

## <a name="optimize-the-performance-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers"></a>Azure Database for MySQL, Azure Database for PostgreSQL és Azure Database for MariaDB kiszolgálók teljesítményének optimalizálása

### <a name="fix-the-cpu-pressure-of-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-with-cpu-bottlenecks"></a>Javítsa ki a processzor szűk keresztmetszeteit a Azure Database for MySQL, Azure Database for PostgreSQL és Azure Database for MariaDB-kiszolgálók CPU-terhelésével
A CPU hosszú időn keresztül történő magas kihasználtsága lassú lekérdezési teljesítményt eredményezhet a munkaterhelés számára. A CPU méretének növelésével optimalizálható az adatbázis-lekérdezések futtatókörnyezete, és javítható a teljes teljesítmény. Az Advisor magas CPU-kihasználtsággal rendelkező kiszolgálókat azonosít, amelyek valószínűleg a CPU által korlátozott munkaterheléseket futtatják, és a számítási feladatok skálázását ajánlják.

### <a name="reduce-memory-constraints-on-your-azure-database-for-mysql-azure-database-for-postgresql-and-azure-database-for-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Csökkentse a memória korlátozásait a Azure Database for MySQL-, Azure Database for PostgreSQL-és Azure Database for MariaDB-kiszolgálókon, vagy térjen át a memóriára optimalizált SKU-ra.
Az alacsony gyorsítótár-találatok aránya lassabb lekérdezési teljesítményt és nagyobb IOPS eredményezhet. Ezt az állapotot egy hibás lekérdezési csomag vagy egy nagy mennyiségű számítási feladat okozhatja. A lekérdezési terv kijavítása vagy a Azure Database for PostgreSQL, Azure Database for MySQL vagy Azure Database for MariaDB kiszolgáló [memóriájának növelésével](../postgresql/concepts-pricing-tiers.md) optimalizálható az adatbázis-számítási feladatok végrehajtása. A Azure Advisor azonosítja a nagy puffer-készlet által érintett kiszolgálókat. Azt javasolja, hogy a következő műveletek egyikét végezze el: 
- A lekérdezési terv kijavítása
- Olyan SKU-ra váltson, amely több memóriát tartalmaz 
- Növelje a tárterület méretét, hogy minél több IOPS.

### <a name="use-an-azure-database-for-mysql-or-azure-database-for-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Azure Database for MySQL vagy Azure Database for PostgreSQL olvasási replika használata az olvasási igényű számítási feladatok felskálázásához
Az Advisor munkaterhelés-alapú heurisztikus adatokat használ, például az olvasások arányát a kiszolgálón az elmúlt hét napban az olvasási igényű számítási feladatok azonosítására. A magas olvasási/írási aránnyal rendelkező Azure Database for PostgreSQL vagy Azure Database for MySQL erőforrás CPU-vagy memória-tartalmakat eredményezhet, és lassú lekérdezési teljesítményt eredményezhet. A [replika](../postgresql/howto-read-replicas-portal.md) hozzáadásával kibővítheti a replika-kiszolgáló olvasási felskálázását, és megakadályozhatja a CPU-vagy memória-korlátozásokat az elsődleges kiszolgálón. Az Advisor az olvasási igényű munkaterhelésekkel rendelkező kiszolgálókat azonosítja, és azt javasolja, hogy adjon hozzá egy [olvasási replikát](../postgresql/concepts-read-replicas.md) a beolvasott számítási feladatok kiszervezéséhez.


### <a name="scale-your-azure-database-for-mysql-azure-database-for-postgresql-or-azure-database-for-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Azure Database for MySQL, Azure Database for PostgreSQL vagy Azure Database for MariaDB kiszolgáló méretezése magasabb SKU-ra a kapcsolódási kényszerek megelőzése érdekében
Az adatbázis-kiszolgáló minden új kapcsolódása memóriát foglal le. Az adatbázis-kiszolgáló teljesítménye romlik, ha a kiszolgálóhoz való kapcsolódás a memória [felső korlátja](../postgresql/concepts-limits.md) miatt meghiúsul. A Azure Advisor számos sikertelen kapcsolatfelvételi hibát használó kiszolgálókat azonosít. Azt javasolja, hogy a kiszolgáló kapcsolódási korlátait úgy frissítse, hogy több memóriát biztosítson a kiszolgálónak a következő műveletek egyikének megkezdésével:
- A számítási kapacitás vertikális felskálázása. 
- Használjon a memóriához optimalizált SKU-ket, amelyek több számítási egységenként vannak.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>A gyorsítótár és az alkalmazások teljesítményének növelése érdekében méretezheti a gyorsítótárat egy másik méretre vagy SKU-ra.

A cache-példányok akkor működnek a legjobban, ha nem a nagy memória, a kiszolgáló terhelése vagy a nagy hálózati sávszélesség alatt futnak. Ezek a feltételek nem válaszolnak, az adatvesztés vagy elérhetetlenné válnak. Az Advisor ezekben a feltételekben azonosítja a gyorsítótár-példányokat. Azt javasolja, hogy a következő műveletek egyikét végezze el:
- Az ajánlott eljárások alkalmazásával csökkentheti a memória terhelését, a kiszolgáló terhelését vagy a hálózati sávszélességet.
- Méretezés más méretre vagy SKU-ra, amely nagyobb kapacitással rendelkezik.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Régiókat adhat hozzá a Azure Cosmos DB-fiókjához tartozó adatforgalomhoz

Az Advisor olyan Azure Cosmos DB-fiókokat észlel, amelyeknek jelenleg nincs konfigurált régiója a forgalma. A régió hozzáadását javasolja. Ezzel növeli az adott régiótól érkező kérelmek késését, és biztosítja a rendelkezésre állást a régiók kiesése esetén. [További információ a globális adatterjesztésről Azure Cosmos DB.](../cosmos-db/distribute-data-globally.md)

## <a name="configure-your-azure-cosmos-db-indexing-policy-by-using-custom-included-or-excluded-paths"></a>Az Azure Cosmos DB indexelési házirend konfigurálása egyéni, belefoglalt vagy kizárt elérési utak használatával

Az Advisor azonosítja Azure Cosmos DB tárolókat, amelyek az alapértelmezett indexelési házirendet használják, de az egyéni indexelési házirendek előnyeit is kihasználhatják. Ez a meghatározás a számítási feladatok mintáján alapul. Az alapértelmezett indexelési házirend indexeli az összes tulajdonságot. A lekérdezési szűrőben használt explicit belefoglalt vagy kizárt elérési úttal rendelkező egyéni indexelési házirend csökkentheti az indexeléshez felhasznált RUs-t és tárterületet. [További információ az index-házirendek módosításáról.](../cosmos-db/index-policy.md)

## <a name="set-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB lekérdezési oldal méretének (MaxItemCount) beállítása a-1 értékre 

A Azure Advisor a 100-es lekérdezési oldal méretét használó Azure Cosmos DB tárolókat azonosítja. Azt javasolja, hogy az oldal mérete (1) legyen a gyorsabb keresésekhez. [További információ a MaxItemCount.](../cosmos-db/sql-api-query-metrics.md)

## <a name="consider-using-accelerated-writes-feature-in-your-hbase-cluster-to-improve-cluster-performance"></a>A fürt teljesítményének növelése érdekében érdemes lehet gyorsított írási funkciót használni a HBase-fürtben
Azure Advisor elemzi a rendszernaplókat az elmúlt 7 napban, és megállapítja, hogy a fürt a következő helyzetekben észlelt-e:
1. A WAL-szinkronizálás időbeli késése magas 
2. Az írási kérelmek száma magas (legalább 3 db egyórás ablak több mint 1000 átlagos_írási_kérelem/másodperc/csomópont aránnyal)

Ezek a feltételek azt jelzik, hogy a fürtön nagy írási késés tapasztalható. Ennek oka lehet a fürtön végrehajtott nagy terhelés. A fürt teljesítményének növeléséhez érdemes lehet az Azure HDInsight HBase által biztosított gyorsított írási funkciót használni. A HDInsight Apache HBase-fürtök gyorsított írási funkciója felhőalapú tároló használata helyett prémium szintű, SSD-alapú felügyelt lemezeket csatol az egyes régiók kiszolgálóihoz (feldolgozó csomópontjához), ezáltal pedig alacsony írási késést és nagyobb rugalmasságot biztosít alkalmazásai számára. További információk a szolgáltatásról további [információt itt](../hdinsight/hbase/apache-hbase-accelerated-writes.md#how-to-enable-accelerated-writes-for-hbase-in-hdinsight) olvashat

## <a name="review-azure-data-explorer-table-cache-period-policy-for-better-performance-preview"></a>A jobb teljesítmény érdekében tekintse át az Azure Adatkezelő Table cache – időszak (házirend) című szakaszát (előzetes verzió)
Ez az ajánlás megmutatja azokat az Azure Data Explorer-táblákat, amelyekben sok olyan lekérdezés található, amely visszatekint a konfigurált gyorsítótárazási időszakon (szabályzaton) túlra. (A lekérdezések százalékos aránya alapján az a leggyakoribb 10 tábla jelenik meg, amely a gyorsítótáron kívüli adatokhoz fér hozzá.) A fürt teljesítményének javítására javasolt művelet a következő: Korlátozza a lekérdezéseket a táblában a minimálisan szükséges időtartományra (a megadott szabályzaton belül). Vagy ha a teljes időtartományból szükség van adatokra, növelje a gyorsítótárazási időszakot az ajánlott értékre.

## <a name="improve-performance-by-optimizing-mysql-temporary-table-sizing"></a>A teljesítmény javítása a MySQL ideiglenestábla-méretének optimalizálásával
Az Advisor Analysis azt jelzi, hogy a MySQL-kiszolgáló szükségtelen I/O-terhelést okozhat az alacsony tábla-paraméter beállításai miatt. Ez felesleges lemezalapú tranzakciókat és alacsonyabb teljesítményt eredményezhet. Javasoljuk, hogy növelje a tmp_table_size és a max_heap_table_size paraméter értékét, ezzel csökkentve a lemezalapú tranzakciók számát. [További információ](https://aka.ms/azure_mysql_tmp_table)

## <a name="distribute-data-in-server-group-to-distribute-workload-among-nodes"></a>Kiszolgálócsoportban tárolt adatok kiosztása a számítási feladat csomópontok közötti elosztásához
Az Advisor azon kiszolgálócsoportok azonosítására szolgál, amelyekben az adat nem lett elosztva, de a koordinátoron marad. Ennek alapján az Advisor azt javasolja, hogy a teljes nagy kapacitású (Citus) előnyök a kiszolgálói csoportok munkavégző csomópontjain legyenek kiterjesztve. Ez javítja a lekérdezési teljesítményt úgy, hogy kihasználja a kiszolgálócsoport egyes csomópontjainak erőforrásait. [További információ](https://go.microsoft.com/fwlink/?linkid=2135201) 

## <a name="improve-user-experience-and-connectivity-by-deploying-vms-closer-to-windows-virtual-desktop-deployment-location"></a>A virtuális gépek a Windows rendszerű virtuális asztali környezetbe való központi telepítésével növelheti a felhasználói élményt és a kapcsolatokat.
Megállapítottuk, hogy a virtuális gépek más régióban vagy messze vannak attól a régiótól, ahonnan a felhasználók a Windows Virtual Desktop (WVD) használatával csatlakoznak. Ez hosszabb kapcsolódási válaszidőkhöz vezethet és hatással van a WVD általános felhasználói élményére. Amikor virtuális gépeket hoz létre a gazdagépkészleteihez, próbáljon meg a felhasználóhoz közelebbi régiót használni. A közelség biztosítja a WVD szolgáltatás megfelelő működését és a jobb általános élményt. [További információ a kapcsolatok késéséről](../virtual-desktop/connection-latency.md).

## <a name="upgrade-to-the-latest-version-of-the-immersive-reader-sdk"></a>Frissítsen a Modern olvasó SDK legfrissebb verziójára.
Azt észleltük, hogy az előfizetésben lévő erőforrások a Modern olvasó elavult SDK-verzióit használják. A Modern olvasó legújabb SDK-verzióját javított biztonság és teljesítmény mellett használhatja, továbbá bővített funkciókészlettel rendelkezik az integráció testre szabása és fejlesztése érdekében.
További információ a [magával ejtő olvasói SDK](../cognitive-services/immersive-reader/index.yml)-ról.

## <a name="improve-vm-performance-by-changing-the-maximum-session-limit"></a>A virtuális gépek teljesítményének növelése a munkamenet maximális korlátjának módosításával

Az Advisor azt észleli, hogy rendelkezik egy, a terheléselosztási algoritmust elsőként tartalmazó gazdagép-készlettel, és az alkalmazáskészlet maximális munkamenet-korlátja nagyobb vagy egyenlő, mint 999999. A mélységi terheléselosztás a maximális munkamenetkorlát alapján határozza meg, hogy legfeljebb hány felhasználó futtathat egyidejű munkameneteket egy adott munkamenet-gazdagépen. Ha a maximális munkamenet-korlát túl magas, a rendszer az összes felhasználói munkamenetet ugyanarra a munkamenet-gazdagépre irányítja, és ez a teljesítménnyel és a megbízhatósággal kapcsolatos problémákat okoz. Ezért, amikor a gazdagépet úgy állítja be, hogy az első terheléselosztással rendelkezzen, a telepítés és a virtuális gépek kapacitása alapján kell beállítania a megfelelő maximális munkamenet-korlátot. 

A Windows rendszerű virtuális asztali terheléselosztással kapcsolatos további tudnivalókért tekintse meg [a Windows rendszerű virtuális asztali terheléselosztási módszer konfigurálása](../virtual-desktop/troubleshoot-set-up-overview.md)című témakört.

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Teljesítménnyel kapcsolatos javaslatok elérése az Advisorban

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg az [Advisor alkalmazást](https://aka.ms/azureadvisordashboard).

2.  Az Advisor Irányítópultján kattintson a **teljesítmény** fülre.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:

* [Az Advisor bemutatása](advisor-overview.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Advisor-pontszám](azure-advisor-score.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Advisor megbízhatósági javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)