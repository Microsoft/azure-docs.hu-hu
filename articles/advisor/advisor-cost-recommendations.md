---
title: A szolgáltatási költségek csökkentése Azure Advisor használatával
description: A Azure Advisor használatával optimalizálhatja az Azure-beli üzemelő példányok költségeit.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 346b790c6970abc8670661e1ec180662957af47d
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503374"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>A szolgáltatási költségek csökkentése Azure Advisor használatával

A Azure Advisor a tétlen és a kihasználatlan erőforrások azonosításával megkönnyíti az Azure-kiadások optimalizálását és csökkentését. A Cost javaslatait az Advisor irányítópultjának **Cost (Cost** ) lapján érheti el.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimalizálja virtuálisgép-költségeit az alacsony kihasználtságú példányok átméretezésével vagy leállításával 

Bár bizonyos alkalmazási forgatókönyvek alacsony kihasználtságot eredményezhetnek a tervezéssel, gyakran pénzt takaríthat meg a virtuális gépek méretének és számának kezelésével. 

Az ajánlott műveletek leállítása vagy átméretezése a kiértékelt erőforrásra jellemző.

Az Advisor Advanced értékelési modellje a virtuális gépek leállítását veszi figyelembe, ha az összes ilyen utasítás igaz: 
- A CPU-kihasználtság maximális értéke P95th kisebb, mint 3%. 
- A hálózati kihasználtság kevesebb, mint 2% a hét napos időszakban.
- A memória nyomása kisebb a küszöbértéknél

Az Advisor úgy véli, hogy átméretezi a virtuális gépeket, amikor lehetséges, hogy egy kisebb SKU-ban (ugyanabban az SKU-családban) vagy kisebb számú példányon elfér az aktuális terhelésnél:
- A jelenlegi terhelés nem haladja meg az 80%-os kihasználtságot azon munkaterhelések esetében, amelyek nem a felhasználó felé irányulnak. 
- A terhelés nem haladja meg a 40%-ot a felhasználók felé irányuló munkaterhelések esetében. 

Itt az Advisor meghatározza a számítási feladatok típusát a számítási feladatok CPU-kihasználtsági jellemzőinek elemzésével.

Az Advisor a javasolt művelet becsült költségmegtakarítását mutatja: átméretezni vagy leállítani. Az átméretezéshez az Advisor az aktuális és a célként megadott SKU-információkat biztosítja.

Ha szeretné, hogy a kihasználatlan virtuális gépek azonosíthatók legyenek, a CPU-kihasználtsági szabályt előfizetéses alapon is beállíthatja.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optimalizálás a MariaDB, a MySQL és a PostgreSQL-kiszolgálók számára a megfelelő méretezéssel 
Az Advisor elemzi a használatot, és kiértékeli, hogy a MariaDB, a MySQL vagy a PostgreSQL adatbázis-kiszolgálói erőforrásai kihasználva lettek-e egy hosszabb ideig az elmúlt hét napban. Az alacsony erőforrás-használat olyan nemkívánatos kiadásokat eredményez, amelyek jelentős teljesítménybeli hatás nélkül javíthatók. A költségek csökkentése és az erőforrások hatékony kezelése érdekében javasoljuk, hogy a számítási méretet (virtuális mag) csökkentse a felére.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>A költségek csökkentése a nem kiépített ExpressRoute-áramkörök eltávolításával

Az Advisor olyan Azure ExpressRoute-áramköröket azonosít, amelyek szolgáltatói állapotukban még **nem lett kiépítve** , mint egy hónap. Az áramkör törlését javasolja, ha nem tervezi az áramkör kiépítését a kapcsolati szolgáltatóval.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Költségek csökkentése az inaktív virtuális hálózati átjárók törlésével vagy újrakonfigurálásával

Az Advisor olyan virtuális hálózati átjárókat azonosít, amelyek több mint 90 napja inaktívak. Mivel az átjárók számlázása óránként történik, érdemes átkonfigurálni vagy törölni őket, ha már nem kívánja használni őket. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Vásároljon fenntartott virtuálisgép-példányokat a használatalapú fizetéshez képest elért pénzmegtakarítás érdekében

Az Advisor az elmúlt 30 napban ellenőrzi a virtuális gépek használatát, hogy megőrizze-e pénzét egy Azure-foglalás megvásárlásával. Az Advisor megjeleníti azokat a régiókat és méreteket, ahol a megtakarítás a legmagasabb, és a megvásárolt foglalások becsült megtakarítása. Az Azure-foglalások segítségével előre megvásárolhatja a virtuális gépek alapdíjait. A kedvezmények automatikusan érvényesek olyan új vagy meglévő virtuális gépekre, amelyek mérete és régiója megegyezik a foglalásokkal. [További információ a Azure Reserved VM Instancesról.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Az Advisor emellett értesíti Önt a fenntartott példányokról, amelyek a következő 30 napban lejárnak. Azt javasolja, hogy az utólagos elszámolású díjszabás elkerülése érdekében új fenntartott példányokat vásároljon.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Fenntartott példányok vásárlása több erőforrástípus számára az utólagos elszámolású költségek megtakarítása érdekében

Az Advisor elemzi a használati mintákat az elmúlt 30 napban a következő erőforrások esetében, és a fenntartott kapacitások vásárlását javasolja a költségek optimalizálása érdekében.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB fenntartott kapacitás
Az Advisor elemzi a Azure Cosmos DB használati mintáit az elmúlt 30 napban, és javaslatot tesz a fenntartott kapacitás vásárlására a költségek optimalizálása érdekében. A fenntartott kapacitás használatával előre megvásárolhatja Azure Cosmos DB óránkénti használatot, és az utólagos elszámolású költségek megtakarítását is elvégezheti. A fenntartott kapacitás számlázási juttatás, és automatikusan érvényes az új és a meglévő központi telepítésekre. Az Advisor 3 éves foglalási díjszabással és az elmúlt 30 napban megfigyelt használati minták kivetítésével számítja ki az egyéni előfizetések megtakarítási becsléseit. A megosztott hatókörre vonatkozó javaslatok elérhetők a fenntartott kapacitású vásárlásokhoz, és növelhetik a megtakarítást.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>SQL Database és az SQL felügyelt példányának fenntartott kapacitása
Az Advisor a SQL Database és az SQL felügyelt példányok használati mintáit elemzi az elmúlt 30 napban. Ezután javaslatot tesz a fenntartott kapacitások vásárlására a költségek optimalizálása érdekében. A fenntartott kapacitás használatával előre megvásárolhatja az SQL-adatbázis óránkénti használatát, és mentheti az SQL-számítási költségeket. Az SQL-licencet külön kell fizetni, és a foglalás nem kedvezményes. A fenntartott kapacitás számlázási juttatás, és automatikusan érvényes az új és a meglévő központi telepítésekre. Az Advisor 3 éves foglalási díjszabással és az elmúlt 30 napban megfigyelt használati minták kivetítésével számítja ki az egyéni előfizetések megtakarítási becsléseit. A megosztott hatókörre vonatkozó javaslatok elérhetők a fenntartott kapacitású vásárlásokhoz, és növelhetik a megtakarítást. Részletekért lásd: [Azure SQL Database & SQL felügyelt példány fenntartott kapacitása](../azure-sql/database/reserved-capacity-overview.md).

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service Stamp díj számára fenntartott kapacitás
Az Advisor az elmúlt 30 napban a Azure App Service izolált környezethez tartozó Stamp díjas használati mintát elemzi, és a fenntartott kapacitások vásárlását javasolja a költségek optimalizálása érdekében. A fenntartott kapacitás használatával előre megvásárolható óránkénti használat az elkülönített környezet pecsétje díj ellenében, és az utólagos elszámolású költségek megtakarítása is elérhető. Vegye figyelembe, hogy a fenntartott kapacitás csak a Stamp díjra vonatkozik, és nem App Service példányokra. A fenntartott kapacitás számlázási juttatás, és automatikusan érvényes az új és a meglévő központi telepítésekre. Az Advisor a használati minták alapján az elmúlt 30 napban kiszámítja az egyéni előfizetésekre vonatkozó becslések megtakarítását.

### <a name="blob-storage-reserved-capacity"></a>BLOB Storage fenntartott kapacitása
Az Advisor az elmúlt 30 napban elemzi az Azure Blob Storage-t és Azure Data Lake tárterület-használatot. Ezután kiszámítja a fenntartott kapacitások vásárlásait, amelyek optimalizálja a költségeket. A fenntartott kapacitással előre megvásárolhatja az óránkénti használatot, és megtakaríthatja az aktuálisan igénybe veheti a költségeket. A blob Storage szolgáltatás számára fenntartott kapacitás csak az Azure Blob általános célú v2-ben és Azure Data Lake Storage Gen2 fiókokban tárolt adatra vonatkozik. A fenntartott kapacitás számlázási juttatás, és automatikusan érvényes az új és a meglévő központi telepítésekre. Az Advisor 3 éves foglalási díjszabás és az elmúlt 30 nap során megfigyelt használati minták alapján számítja ki az egyéni előfizetések megtakarítási becsléseit. A megosztott hatókörre vonatkozó javaslatok elérhetők a fenntartott kapacitású vásárlásokhoz, és növelhetik a megtakarítást.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>A MariaDB, a MySQL és a PostgreSQL fenntartott kapacitása
Az Advisor az elmúlt 30 napban Azure Database for MariaDB, Azure Database for MySQL és Azure Database for PostgreSQL használati mintáit elemzi. Ezután javaslatot tesz a fenntartott kapacitások vásárlására a költségek optimalizálása érdekében. A fenntartott kapacitás használatával előre megvásárolhatja a MariaDB, a MySQL és a PostgreSQL óradíjas használatát, és megtakaríthatja az aktuális költségeket. A fenntartott kapacitás számlázási juttatás, és automatikusan érvényes az új és a meglévő központi telepítésekre. Az Advisor 3 éves foglalási díjszabás és az elmúlt 30 nap során megfigyelt használati minták alapján számítja ki az egyéni előfizetések megtakarítási becsléseit. A megosztott hatókörre vonatkozó javaslatok elérhetők a fenntartott kapacitású vásárlásokhoz, és növelhetik a megtakarítást.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Az Azure szinapszis Analytics számára fenntartott kapacitása
Az Advisor az elmúlt 30 nap során elemzi az Azure szinapszis Analytics használati mintáit, és a fenntartott kapacitások vásárlását javasolja a költségek optimalizálása érdekében. A fenntartott kapacitás használatával előre megvásárolhatja a szinapszis Analytics óránkénti használatát, és megtakaríthatja az igény szerinti költségeket. A fenntartott kapacitás számlázási juttatás, és automatikusan érvényes az új és a meglévő központi telepítésekre. Az Advisor 3 éves foglalási díjszabás és az elmúlt 30 nap során megfigyelt használati minták alapján számítja ki az egyéni előfizetések megtakarítási becsléseit. A megosztott hatókörre vonatkozó javaslatok elérhetők a fenntartott kapacitású vásárlásokhoz, és növelhetik a megtakarítást.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>A nem társított nyilvános IP-címek törlése a pénz megtakarítása érdekében

Az Advisor olyan nyilvános IP-címeket azonosít, amelyek nem tartoznak olyan Azure-erőforrásokhoz, mint a terheléselosztó és a virtuális gépek. Ezekhez a nyilvános IP-címekhez egy névleges díj tartozik. Ha nem tervezi használni őket, a törléssel pénzt takaríthat meg.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Meghiúsult Azure Data Factory-folyamatok törlése

Az Advisor észleli Azure Data Factory folyamatokat, amelyek többször meghiúsulnak. Azt javasolja, hogy hárítsa el a problémákat, vagy törölje a folyamatokat, ha nincs rá szükség. Ezeket a folyamatokat akkor is számlázza, ha nem az Ön szolgálatában maradnak.

## <a name="use-standard-snapshots-for-managed-disks"></a>Szabványos Pillanatképek használata a felügyelt lemezekhez
A 60%-os megtakarítás érdekében javasoljuk, hogy a pillanatképeket a normál tárolóban tárolja, a szülő lemez tárolási típusától függetlenül. Ez a beállítás az alapértelmezett beállítás a felügyelt lemezes Pillanatképek számára. Az Advisor a Premium Storage-ban tárolt pillanatképeket azonosítja, és a prémiumról standard szintű tárterületre való áttérést javasolja. [További információ a felügyelt lemez díjszabásáról.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Életciklus-kezelés használata
Az Azure Blob Storage-objektumok számával, a teljes mérettel és a tranzakciókkal kapcsolatos intelligenciával az Advisor észleli, hogy engedélyezni kell-e az életciklus-kezelést egy vagy több Storage-fiókra vonatkozóan. Arra kéri, hogy hozzon létre életciklus-kezelési szabályokat, hogy a tárolási költségek optimalizálása érdekében automatikusan leszűkítse az adatait a tárhelyre, miközben az adatok az Azure Blob Storage-ban való megőrzése mellett az alkalmazások kompatibilitását is megőrzi.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Javasolt a rövid élettartamú operációsrendszer-lemezek létrehozása
Az [ideiglenes operációsrendszer-lemez](../virtual-machines/ephemeral-os-disks.md) a következőket teszi lehetővé: 
- Az operációsrendszer-lemezek tárolási költségeinek mentése. 
- Csökkentse az írási/olvasási késleltetést az operációsrendszer-lemezekre. 
- Az operációs rendszer (és az ideiglenes lemez) alaphelyzetbe állításával gyorsabbá teheti a virtuális gépek lemezkép-rendszerképének működését.

A rövid élettartamú IaaS-alapú virtuális gépekhez és az állapot nélküli számítási feladatokhoz ajánlott virtuális gépekhez érdemes elmúló operációsrendszer-lemezt használni. Az Advisor olyan erőforrásokra vonatkozó ajánlásokat tartalmaz, amelyek kihasználhatják az elmúló operációsrendszer-lemezt.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Az Azure Adatkezelő Table cache-időszak (Policy) csökkentése a fürt költségtakarékos optimalizálásához (előzetes verzió)
Az Advisor azonosítja azokat az erőforrásokat, amelyekben a tábla-gyorsítótárazási házirend csökkenti az Azure Adatkezelő-fürtcsomópontok alacsony CPU-kihasználtságát, memóriáját és nagy gyorsítótár-méret konfigurációját.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>A Cost javaslatokhoz való hozzáférés Azure Advisor

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg és válassza ki az [**Advisor**](https://aka.ms/azureadvisordashboard) elemet bármelyik oldalon.

1. Az **Advisor** irányítópulton válassza a **Cost (Cost** ) lapot.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:
* [Az Advisor bemutatása](advisor-overview.md)
* [Advisor-pontszám](azure-advisor-score.md)
* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor – magas rendelkezésre állási javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor működési kiválósági javaslatok](advisor-operational-excellence-recommendations.md)
