---
title: Áttekintés – Azure Database for MySQL – rugalmas kiszolgáló
description: Ismerkedjen meg az Azure Database for MySQL rugalmas kiszolgálóval, amely a Microsoft Cloud-on alapuló, a MySQL Community Edition rendszerre épülő, összehasonlítható adatbázis-szolgáltatás.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: 46b056c22a6d1c70a729f5b9558f53c685a878f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732853"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL – rugalmas kiszolgáló (előzetes verzió)

A MySQL Community Edition Azure Database for MySQL a két üzembe helyezési módban érhető el:
- Önálló kiszolgáló 
- Rugalmas kiszolgáló (előzetes verzió)

Ebben a cikkben áttekintjük és bemutatjuk a rugalmas kiszolgáló üzembe helyezési modelljének alapvető fogalmait. A számítási feladatokhoz megfelelő központi telepítési lehetőség eldöntéséről további információt a [megfelelő MySQL-kiszolgáló kiválasztása az Azure-ban](./../select-right-deployment-type.md)című témakörben talál.

## <a name="overview"></a>Áttekintés

Azure Database for MySQL rugalmas kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások felett. Általánosságban elmondható, hogy a szolgáltatás nagyobb rugalmasságot és kiszolgáló-konfigurációt biztosít a felhasználói követelmények alapján. A rugalmas kiszolgáló architektúrája lehetővé teszi a felhasználók számára, hogy az egyetlen rendelkezésre állási zónában és több rendelkezésre állási zónában is magas rendelkezésre állást engedélyezzenek A rugalmas kiszolgálók jobb költség-optimalizálási ellenőrzéseket is biztosítanak, amelyek lehetővé teszik a kiszolgáló és a feltört SKU-hálózat leállítását/elindítását, ideális olyan számítási feladatokhoz, amelyeknek nincs szükségük teljes számítási kapacitás A szolgáltatás jelenleg a MySQL 5,7 és a 8,0 közösségi verzióját támogatja. A szolgáltatás jelenleg előzetes verzióban érhető el, és ma már számos [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/)elérhető.

A rugalmas kiszolgálók a legmegfelelőbbek a következőhöz: 
- Hatékonyabb vezérlést és testreszabást igénylő alkalmazások fejlesztése.
- Zóna redundáns magas rendelkezésre állása
- Felügyelt karbantartási időszakok

![Rugalmas kiszolgáló fogalmi diagramja](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Magas rendelkezésre állás a rendelkezésre állási zónákon belül és azok között

A rugalmas kiszolgálói üzemi modell úgy lett kialakítva, hogy támogassa a magas rendelkezésre állást az önálló rendelkezésre állási zónában és több rendelkezésre állási zónában. Az architektúra elkülöníti a számítási és a tárolási kapacitást. Az adatbázismotor egy virtuális gépen fut, míg az adatfájlok az Azure Storage-ban találhatók. A tároló három helyileg redundáns szinkron példányt tart fenn az adatbázisfájlok számára az adattartósság biztosításához. 

Egyetlen rendelkezésre állási zónán belül, ha a kiszolgáló tervezett vagy nem tervezett események miatt leáll, a szolgáltatás a következő automatizált eljárással biztosítja a kiszolgálók magas rendelkezésre állását:

1. Új számítási virtuális gép lett kiépítve.
2. Az adatfájlokkal rendelkező tárterület az új virtuális gépre van leképezve
3. A MySQL-adatbázismotor online állapotba kerül az új virtuális gépen.
4. Az ügyfélalkalmazások újra csatlakozhatnak, ha a kiszolgáló készen áll a kapcsolatok fogadására.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Önálló zóna – magas rendelkezésre állás fogalmi diagramja"::: 

Ha a zóna redundáns magas rendelkezésre állása be van állítva, a szolgáltatás kiépíti és karbantartja a gyors készenléti kiszolgálót a rendelkezésre állási zónán belül ugyanazon az Azure-régióban. A forráskiszolgálón lévő adatváltozásokat a rendszer szinkron módon replikálja a készenléti kiszolgálóra, így biztosítva a nulla adatvesztést. A zóna redundáns, magas rendelkezésre állása esetén a tervezett vagy nem tervezett feladatátvételi esemény elindítása után a készenléti kiszolgáló azonnal online állapotba kerül, és elérhető a bejövő tranzakciók feldolgozásához. A tipikus feladatátvételi idő 60-120 másodperc. Ez lehetővé teszi, hogy a szolgáltatás támogassa a magas rendelkezésre állást, és nagyobb rugalmasságot biztosítson egy adott Azure-régióban az egyes rendelkezésre állási zónák hibáinak tűréshatárával. 

További részletekért tekintse meg a [magas rendelkezésre állással kapcsolatos fogalmakat](concepts-high-availability.md) .

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Zone redundáns magas rendelkezésre állás fogalmi diagramja"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatizált javítás a felügyelt karbantartási időszakmal

A szolgáltatás a mögöttes hardver, az operációs rendszer és az adatbázis motorjának automatizált javítását végzi. A javítás biztonsági és szoftverfrissítéseket is tartalmaz. A MySQL motor esetében az alverziók frissítései a tervezett karbantartási kiadás részeként is szerepelnek. A felhasználók úgy konfigurálhatják a javítási ütemtervet, hogy a rendszer felügyelhető legyen, vagy definiálja az egyéni ütemtervet. A karbantartási ütemterv során a javítás érvénybe lép, és a kiszolgáló a javítási folyamat részeként újraindítást igényelhet a frissítés befejezéséhez. Az egyéni ütemterv segítségével a felhasználók előre jelezheti a javítási ciklust, és olyan karbantartási időszakot választhatnak, amelynek minimális hatása van a vállalatra. Általánosságban elmondható, hogy a szolgáltatás a folyamatos integráció és a kiadás részeként havi kiadási ütemtervet követ.

További részletekért tekintse meg az [ütemezett karbantartást](concepts-maintenance.md) . 

## <a name="automatic-backups"></a>Automatikus biztonsági mentések

A rugalmas kiszolgáló szolgáltatás automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja azokat a felhasználó által helyileg redundáns vagy földrajzilag redundáns tárolóban. A biztonsági mentések segítségével a kiszolgáló bármely időpontra visszaállítható a biztonsági mentés megőrzési ideje alatt. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Az adatmegőrzés opcionálisan 35 napig is konfigurálható. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva. 

További információért lásd a [biztonsági mentési fogalmakat](concepts-backup-restore.md) ismertető témakört.

## <a name="network-isolation"></a>Hálózatelkülönítés

Két hálózati lehetőség közül választhat a Azure Database for MySQL rugalmas kiszolgálóhoz való kapcsolódáshoz. A lehetőségek a következők: **privát hozzáférés (VNet-integráció)** és **nyilvános hozzáférés (engedélyezett IP-címek)**. 

* **Privát hozzáférés (VNet-integráció)** – a rugalmas kiszolgálót üzembe helyezheti az [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Az Azure Virtual Network privát és biztonságos hálózati kommunikációt biztosít. A virtuális hálózatok erőforrásai privát IP-címeken keresztül kommunikálhatnak.

   Ha a következő képességeket szeretné használni, válassza a VNet-integráció lehetőséget:
   * Kapcsolódás az azonos virtuális hálózatban lévő Azure-erőforrásokhoz a rugalmas kiszolgálóhoz magánhálózati IP-címek használatával
   * A VPN vagy a ExpressRoute használatával csatlakozhat a nem Azure-erőforrásokról a rugalmas kiszolgálóhoz
   * Nincs nyilvános végpont

* **Nyilvános hozzáférés (engedélyezett IP-címek)** – a rugalmas kiszolgálót nyilvános végponttal is üzembe helyezheti. A nyilvános végpont egy nyilvánosan feloldható DNS-címe. Az "engedélyezett IP-címek" kifejezés számos olyan IP-címet jelöl, amelyet a kiszolgáló eléréséhez engedélyt ad. Ezeket az engedélyeket **Tűzfalszabályok** nevezzük.

További információért lásd a [hálózatkezelési fogalmakat](concepts-networking.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

A rugalmas kiszolgáló szolgáltatás három SKU-szinten érhető el: feltört, általános célú és memória-optimalizálva. A feltört réteg az alacsony költséghatékonyságú fejlesztési és alacsony párhuzamosságú munkaterhelésekhez ideális megoldás, amelyeknek nincs szükségük teljes számítási kapacitásra. Az optimalizált általános célú és memória jobban alkalmazkodik a magas párhuzamosságot, méretezést és kiszámítható teljesítményt igénylő éles munkaterhelésekhez. Az első alkalmazást egy kis adatbázison hozhatja létre havonta néhány dollárért, majd zökkenőmentesen módosíthatja a méretezést a megoldás igényeinek megfelelően. A tárolási skálázás online állapotban van, és támogatja a tárterület automatikus növekedését. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak a felhasznált erőforrásokért kell fizetnie. 

További információért lásd a [számítási és tárolási fogalmakat](concepts-compute-storage.md) .

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Az olvasási munkaterhelés felskálázása legfeljebb 10 olvasási replikával

A MySQL az egyik népszerű adatbázismotor a webes és mobil alkalmazások futtatásához. Számos ügyfelünk a saját online oktatási szolgáltatásaihoz, a video streaming-szolgáltatásokhoz, a digitális fizetési megoldásokhoz, az e-kereskedelmi platformokhoz, a szerencsejáték-szolgáltatásokhoz, a hírek portálokhoz, a kormányzati és az egészségügyi webhelyekhez használható. Ezek a szolgáltatások a webes vagy mobil alkalmazások forgalmának növeléséhez és méretezéséhez szükségesek.

Az alkalmazások oldalon az alkalmazás általában Java vagy PHP nyelven lett kifejlesztve, és át lett telepítve az [Azure-beli virtuálisgép-méretezési csoportokon](../../virtual-machine-scale-sets/overview.md)vagy az Azure app Services-on való futtatásra,   vagy az [](../../app-service/overview.md)    [Azure Kubernetes szolgáltatásban (ak)](../../aks/intro-kubernetes.md)való futtatásra. A virtuálisgép-méretezési csoport, a App Service vagy az AK az alapul szolgáló infrastruktúrának megfelelően egyszerűsíti az alkalmazások skálázását azáltal, hogy azonnal kiépíti az új virtuális gépeket, és replikálja az alkalmazás állapot nélküli összetevőit, hogy azok a kérések ellátására szolgálnak, de gyakran az adatbázis a központosított állapot-nyilvántartó összetevőként végződik.

Az olvasási replika funkció lehetővé teszi az adatok replikálását egy Azure Database for MySQL rugalmas kiszolgálóról egy írásvédett kiszolgálóra. A forrás-kiszolgálóról **legfeljebb 10 replikára** lehet replikálni. A replikák aszinkron módon frissülnek a MySQL motor natív [bináris naplójának (BinLog) fájljának pozíció-alapú replikációs technológiájának](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)használatával. A terheléselosztó proxy-megoldásait, például a [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) -t használva zökkenőmentesen kibővítheti az alkalmazás számítási feladatait a replikák olvasására az alkalmazás újraszámítási díja nélkül. 

További információért olvassa el a [replika-fogalmak beolvasása](concepts-read-replicas.md) című témakört. 


## <a name="stopstart-server-to-optimize-cost"></a>A kiszolgáló leállítása/elindítása a Cost optimalizálásához

A rugalmas kiszolgálói szolgáltatás lehetővé teszi a kiszolgáló igény szerinti leállítását és elindítását a Cost optimalizálása érdekében. A számítási szintek számlázása azonnal leáll, amikor a kiszolgáló leáll. Ezzel jelentős költségmegtakarítást érhet el a fejlesztés, a tesztelés és az időkorlátos kiszámítható éles számítási feladatok esetében. A kiszolgáló hét napig leállított állapotban marad, kivéve, ha hamarabb újraindul. 

További információért lásd a [kiszolgálói fogalmakat](concept-servers.md) . 

## <a name="enterprise-grade-security-and-privacy"></a>Nagyvállalati szintű biztonság és adatvédelem

A rugalmas kiszolgáló szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adatok tárolási titkosításához. Az adatokat, beleértve a biztonsági másolatokat, valamint a lekérdezések futtatása közben létrehozott ideiglenes fájlokat is titkosítja a rendszer. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, a kulcsokat pedig rendszerfelügyelheti (alapértelmezett). 

A szolgáltatás alapértelmezés szerint titkosítja a mozgásban lévő adatátviteli réteget. A rugalmas kiszolgálók csak a Transport Layer Security (TLS 1,2) használatával titkosított kapcsolatokat támogatnak, és a TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata meg lesz tagadva. 

További információért lásd: [titkosított kapcsolatok használata rugalmas kiszolgálókhoz](https://docs.mongodb.com/manual/tutorial/configure-ssl) .

A rugalmas kiszolgálók lehetővé teszik a kiszolgálók teljes magánhálózati elérését az [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) (VNet) integrációjának használatával. Az Azure virtuális hálózatban található kiszolgálók csak magánhálózati IP-címeken keresztül érhetők el és csatlakoztathatók. A VNet-integrációval a nyilvános hozzáférés megtagadva, és a kiszolgálók nem érhetők el nyilvános végpontok használatával. 

További információért lásd a [hálózatkezelési fogalmakat](concepts-networking.md) .


## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A rugalmas kiszolgáló szolgáltatás beépített teljesítmény-figyelési és riasztási funkciókkal rendelkezik. Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. A szolgáltatás elérhetővé teszi a gazdagép-kiszolgáló metrikáit az erőforrások kihasználtságának figyelésére, valamint a lassú lekérdezési naplók konfigurálására. Ezen eszközök használatával gyorsan optimalizálhatja a számítási feladatokat, és konfigurálhatja a kiszolgálót a legjobb teljesítmény érdekében. 

További információért tekintse meg a [figyelési fogalmakat](concepts-monitoring.md) .

## <a name="migration"></a>Áttelepítés

A szolgáltatás a MySQL közösségi verzióját futtatja. Ez lehetővé teszi az alkalmazások teljes kompatibilitását, és minimális újrabontási költségeket igényel a MySQL-motoron az egykiszolgálós szolgáltatáshoz fejlesztett meglévő alkalmazások áttelepítéséhez. Az egyetlen kiszolgálóra történő áttelepítés a következő lehetőségek egyikével végezhető el:

- **Memóriakép és visszaállítás** – kapcsolat nélküli áttelepítések esetén, ahol a felhasználók bizonyos állásidőt, kiírást és visszaállítást biztosíthatnak olyan közösségi eszközökkel, mint a mysqldump/mydumper, a lehető leggyorsabb módon telepíthetik az áttelepítést. A részletekért lásd: az áttelepítés a dump és a Restore használatával. 
- **Azure Database Migration Service** – az egykiszolgálós, minimális állásidővel rendelkező, zökkenőmentes és egyszerűsített áttelepítéshez [Azure Database Migration Service](../../dms/tutorial-mysql-azure-mysql-online.md) is kihasználható. 

## <a name="azure-regions"></a>Azure-régiók

A számítási feladatok Azure-ban való futtatásának egyik előnye, hogy globálisan elérhető. A Azure Database for MySQL rugalmas kiszolgálója jelenleg a következő Azure-régiókban érhető el:

| Region | Rendelkezésre állás | Zóna redundáns HA | 
| --- | --- | --- |
| Nyugat-Európa | :heavy_check_mark: | :heavy_check_mark: |
| Észak-Európa | :heavy_check_mark: | :heavy_check_mark: |
| Az Egyesült Királyság déli régiója | :heavy_check_mark: | x | 
| USA 2. keleti régiója | :heavy_check_mark: | :heavy_check_mark: |
| USA 2. nyugati régiója | :heavy_check_mark: | :heavy_check_mark: |
| Az USA középső régiója | :heavy_check_mark: | x | 
| USA keleti régiója | :heavy_check_mark: | :heavy_check_mark: |
| Közép-Kanada | :heavy_check_mark: | x | 
| Délkelet-Ázsia | :heavy_check_mark: | :heavy_check_mark: |
| Dél-Korea középső régiója | :heavy_check_mark: | x | 
| Kelet-Japán | :heavy_check_mark: | x | 
| Kelet-Ausztrália | :heavy_check_mark: | :heavy_check_mark: |

Hamarosan új régiókat fogunk hozzáadni.

## <a name="contacts"></a>Kapcsolattartók
Ha bármilyen kérdése vagy javaslata van Azure Database for MySQL rugalmas kiszolgálón, küldjön e-mailt a Azure Database for MySQL csapatnak ([ @Ask Az Azure db for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)-nek). Ez az e-mail-cím nem technikai támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Következő lépések
Most, hogy elolvasta Azure Database for MySQL egykiszolgálós üzembe helyezési mód bevezetését, készen áll a következőre:

- Hozza létre az első kiszolgálót. 
  - [Azure Database for MySQL rugalmas kiszolgáló létrehozása Azure Portal használatával](quickstart-create-server-portal.md)
  - [Azure Database for MySQL rugalmas kiszolgáló létrehozása az Azure CLI-vel](quickstart-create-server-cli.md)
  - [Azure Database for MySQL rugalmas kiszolgáló kezelése az Azure CLI használatával](how-to-manage-server-portal.md)

- Hozza létre első alkalmazását a kívánt nyelv használatával:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
