---
title: Áttekintés – Azure Database for MySQL – Rugalmas kiszolgáló
description: Ismerje meg Azure Database for MySQL rugalmas kiszolgálót, amely egy relációsadatbázis-szolgáltatás a Microsoft-felhőben a MySQL Community Edition alapján.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/21/2020
ms.openlocfilehash: e3259cddc3790f92f56d7f59783cd66a2502b704
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813325"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL – Rugalmas kiszolgáló (előzetes verzió)

Azure Database for MySQL MySQL Community Edition által működtetett alkalmazás két üzembe helyezési módban érhető el:
- Önálló kiszolgáló 
- Rugalmas kiszolgáló (előzetes verzió)

Ebben a cikkben áttekintjük és bemutatjuk a rugalmas kiszolgálótelepítési modell alapvető fogalmait. A számítási feladathoz megfelelő üzembe helyezési lehetőség kiválasztásával kapcsolatos információkért lásd: A megfelelő MySQL-kiszolgáló kiválasztása [az Azure-ban.](./../select-right-deployment-type.md)

## <a name="overview"></a>Áttekintés

Azure Database for MySQL rugalmas kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb vezérlést és rugalmasságot biztosít az adatbázis-kezelési funkciókhoz és a konfigurációs beállításokhoz. Általánosságban elmondható, hogy a szolgáltatás a felhasználói követelmények alapján nagyobb rugalmasságot és kiszolgálókonfiguráció-testreszabást biztosít. A rugalmas kiszolgálói architektúra lehetővé teszi, hogy a felhasználók magas rendelkezésre állást válasszanak az egy rendelkezésre állási zónán belül és több rendelkezésre állási zónában. A rugalmas kiszolgálók emellett jobb költségoptimalizálási vezérlőket is biztosítanak, amelyek képesek leállítani/elindítani a kiszolgálót és a kihasználtságú skusokat, ideálisak olyan számítási feladatokhoz, amelyek nem rendelkeznek folyamatosan teljes számítási kapacitással. A szolgáltatás jelenleg a MySQL 5.7-es és 8.0-s közösségi verzióját támogatja. A szolgáltatás jelenleg előzetes verzióban érhető el, és számos [különböző Azure-régióban érhető el.](https://azure.microsoft.com/global-infrastructure/services/)

A rugalmas kiszolgálók a legmegfelelőbbek a 
- Az alkalmazásfejlesztések jobb vezérlést és testreszabást követelnek meg.
- Zónaredundáns magas rendelkezésre állás
- Felügyelt karbantartási időszak

![Rugalmas kiszolgáló fogalmi diagramja](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="high-availability-within-and-across-availability-zones"></a>Magas rendelkezésre állás a rendelkezésre állási zónákon belül és között

A rugalmas kiszolgálótelepítési modell úgy lett kialakítva, hogy támogassa a magas rendelkezésre állást egy rendelkezésre állási zónán belül és több rendelkezésre állási zónában. Az architektúra elkülöníti a számítást és a tárolást. Az adatbázismotor egy virtuális gépen fut, az adatfájlok pedig az Azure Storage-ban találhatók. A tároló három helyileg redundáns szinkron másolatot tart fenn az adatbázisfájlokról, így mindig biztosíthatja az adatok tartósságát. 

Ha a kiszolgáló tervezett vagy nem tervezett események miatt leáll egy rendelkezésre állási zónán belül, a szolgáltatás a következő automatizált eljárással tartja fenn a kiszolgálók magas rendelkezésre állását:

1. Új számítási virtuális gép van kiépítve.
2. Az adatfájlokat tároló tároló az új virtuális gépre van leképezve
3. A MySQL adatbázismotor online állapotba lesz hozva az új virtuális gépen.
4. Az ügyfélalkalmazások újracsatlakoznak, ha a kiszolgáló készen áll a kapcsolatok elfogadására.
   
:::image type="content" source="media/overview/2-flexible-server-architecture.png" alt-text="Az egyzónás magas rendelkezésre állás fogalmi diagramja"::: 

Ha zónaredundáns magas rendelkezésre állás van konfigurálva, a szolgáltatás egy készenléti kiszolgálót hoz ki és tart fenn egy rendelkezésre állási zónában ugyanabban az Azure-régióban. A forráskiszolgálón végrehajtott adatváltozásokat a rendszer szinkron módon replikálja a készenléti kiszolgálóra, így biztosítja az adatvesztést. Zónaredundáns magas rendelkezésre állás esetén a tervezett vagy nem tervezett feladatátvételi esemény aktiválása után a készenléti kiszolgáló azonnal online állapotba kerül, és elérhető a bejövő tranzakciók feldolgozásához. A feladatátvétel jellemző ideje 60–120 másodperc. Ez lehetővé teszi, hogy a szolgáltatás támogassa a magas rendelkezésre állást, és továbbfejlesztett rugalmasságot biztosítson az egy rendelkezésre állási zóna meghibásodásai esetén egy adott Azure-régióban. 

További [részletekért tekintse](concepts-high-availability.md) meg a magas rendelkezésre állás fogalmait.

:::image type="content" source="media/overview/3-flexible-server-overview-zone-redundant-ha.png" alt-text="Zónaredundáns magas rendelkezésre állás fogalmi diagramja"::: 

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatikus javítás felügyelt karbantartási időszakkal

A szolgáltatás elvégzi a mögöttes hardver, az operációs rendszer és az adatbázismotor automatikus javítását. A javítás biztonsági és szoftverfrissítéseket is tartalmaz. A MySQL-motor esetén az alverziófrissítések a tervezett karbantartási kiadás részét is tartalmazzák. A felhasználók rendszer által felügyeltként konfigurálhatja a javítási ütemezést, vagy meghatározhatja az egyéni ütemezésüket. A karbantartási ütemezés során a rendszer alkalmazza a javítást, és előfordulhat, hogy a kiszolgáló újraindítást igényel a frissítési folyamat részeként a frissítés befejezéséhez. Az egyéni ütemezéssel a felhasználók kiszámíthatóvá teheti a javítási ciklusukat, és kiválaszthatja a karbantartási időszakokat, amelyek minimális hatással vannak az üzletre. A szolgáltatás általában a havi kiadási ütemezést követi a folyamatos integráció és kiadás részeként.

További [részletekért lásd:](concepts-maintenance.md) Ütemezett karbantartás. 

## <a name="automatic-backups"></a>Automatikus biztonsági mentések

A rugalmas kiszolgálószolgáltatás automatikusan biztonsági másolatokat készít a kiszolgálóról, és a felhasználó által konfigurált helyileg redundáns vagy georedundáns tárolóban tárolja azokat. A biztonsági másolatokkal a kiszolgáló bármely időpontra visszaállítható a biztonsági másolatok megőrzési időszakán belül. A biztonsági másolatok alapértelmezett megőrzési ideje hét nap. Az adatmegőrzés igény szerint 35 napig is konfigurálható. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva. 

További [információ: Biztonsági mentéssel kapcsolatos](concepts-backup-restore.md) fogalmak.

## <a name="network-isolation"></a>Hálózatelkülönítés

A rugalmas kiszolgálóhoz való csatlakozáshoz két hálózati Azure Database for MySQL van. A lehetőségek a **privát hozzáférés (VNet-integráció)** és a **nyilvános hozzáférés (engedélyezett IP-címek).** 

* **Privát hozzáférés (VNet-integráció)** – Rugalmas kiszolgálóját üzembe helyezheti az [Azure Virtual Network.](../../virtual-network/virtual-networks-overview.md) Az Azure-beli virtuális hálózatok privát és biztonságos hálózati kommunikációt biztosítanak. A virtuális hálózatok erőforrásai magánhálózati IP-címeken keresztül kommunikálhatnak.

   Válassza a VNet-integráció lehetőséget, ha a következő képességeket szeretné:
   * Csatlakozás az azonos virtuális hálózatban lévő Azure-erőforrásokról a rugalmas kiszolgálóhoz magánhálózati IP-címek használatával
   * VPN vagy ExpressRoute használata a nem Azure-erőforrások és a rugalmas kiszolgáló csatlakoztatása érdekében
   * Nincs nyilvános végpont

* **Nyilvános hozzáférés (engedélyezett IP-címek)** – Rugalmas kiszolgálóját nyilvános végponttal helyezheti üzembe. A nyilvános végpont egy nyilvánosan feloldható DNS-cím. Az "engedélyezett IP-címek" kifejezés ip-címek egy olyan tartományát jelenti, amelyről ön engedélyt ad a kiszolgáló eléréséhez. Ezeket az engedélyeket **tűzfalszabályoknak nevezzük.**

További [információ:](concepts-networking.md) Hálózati fogalmak.

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

A rugalmas kiszolgálói szolgáltatás három termékváltozat-szinten érhető el: Adatlökhető, általános célú memóriaoptimal van optimalizálva. A Burstable csomag az alacsony költségű fejlesztéshez és az alacsony egyidejűségi szintű számítási feladatokhoz a legalkalmasabb, amelyekhez nem szükséges folyamatosan teljes számítási kapacitás. A általános célú és a Memóriaoptimalált jobban megfelel a magas egyidejűséget, skálázhatóságot és kiszámítható teljesítményt igénylő éles számítási feladatokhoz. Havonta néhány dollárban felépítheti első alkalmazását egy kisméretű adatbázison, majd zökkenőmentesen módosíthatja a méretet a megoldás igényeinek megfelelően. A tárolóméretezés online állapotban van, és támogatja a tároló automatikus növekedését. A dinamikus méretezhetőség révén az adatbázis átlátható módon reagál a gyorsan változó erőforrásigényekre. Csak a felhasznált erőforrásokért kell fizetnie. 

További [információ: Számítási és](concepts-compute-storage.md) tárolási fogalmak.

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Skálázhatja fel horizontálisan az olvasási számítási feladatot akár 10 olvasási replikával

A MySQL az internetes webes és mobilalkalmazások futtatására használható népszerű adatbázismotorok egyike. Számos ügyfelünk használja online oktatási szolgáltatásaihoz, videóstreamelési szolgáltatásaihoz, digitális fizetési megoldásokhoz, e-kereskedelmi platformokhoz, játékszolgáltatásokhoz, hírportálokhoz, kormányzati és egészségügyi webhelyekhez. Ezek a szolgáltatások a webes vagy mobilalkalmazások forgalmának növekedésével egyre nagyobb mértékben kiszolgálják és skálázják a szolgáltatást.

Az alkalmazások oldalán az alkalmazást általában Java- vagy PHP-környezetben fejlesztik, és azure-beli virtuálisgép-méretezési készleteken vagy [Azure](../../virtual-machine-scale-sets/overview.md)App Services-ben való futtatás érdekében migrálják, vagy tárolóba vannak helyezni, hogy az Azure Kubernetes Service    [](../../app-service/overview.md)    [(AKS) szolgáltatáson](../../aks/intro-kubernetes.md)fusson. A virtuálisgép-méretezési készlet, az App Service vagy az AKS mint mögöttes infrastruktúra révén az alkalmazások skálázása egyszerűbbé válik az új virtuális gépek azonnali kiépítése és az alkalmazások állapot nélküli összetevőinek replikálása a kérések ellátásához, de az adatbázis végül a központosított állapot-igénylési összetevő szűk keresztmetszete lesz.

Az olvasási replika funkcióval egy rugalmas Azure Database for MySQL kiszolgálóról replikálhatja az adatokat egy csak olvasható kiszolgálóra. A forráskiszolgálóról legfeljebb **10** replikára replikálhat. A replikák aszinkron módon frissülnek a MySQL-motor natív bináris [naplójának (binlog) fájlpozíció-alapú replikációs technológiájával.](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) A [ProxySQL-hez](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) hasonló terheléselosztási proxymegoldással zökkenőmentesen horizontálisan felskálolhatja az alkalmazás számítási feladatait a replikák olvasásához anélkül, hogy az alkalmazás újrafactorolási költsége lenne. 

További [információ: Read Replica concepts](concepts-read-replicas.md) (A replikákkal kapcsolatos fogalmak olvasása). 


## <a name="stopstart-server-to-optimize-cost"></a>Kiszolgáló leállítása/indítása a költségek optimalizálása

A rugalmas kiszolgálószolgáltatással leállíthatja és elindíthatja a kiszolgálót igény szerint a költségek optimalizálása érdekében. A számítási szint számlázása a kiszolgáló leálltát követően azonnal leáll. Ez jelentős költségmegtakarítást tesz lehetővé a fejlesztés, a tesztelés és az időkorrekta kiszámítható éles számítási feladatok esetében. A kiszolgáló hét napig leállított állapotban marad, kivéve, ha korábban újra elindítják. 

További [információ: Kiszolgálói](concept-servers.md) fogalmak. 

## <a name="enterprise-grade-security-and-privacy"></a>Vállalati szintű biztonság és adatvédelem

A rugalmas kiszolgálószolgáltatás a FIPS 140-2 ellenőrzött titkosítási modult használja az adatok tárolásra való titkosítására az aktuálisan használt adatokhoz. Az adatok, beleértve a biztonsági másolatokat és a lekérdezések futtatása közben létrehozott ideiglenes fájlokat titkosítva vannak. A szolgáltatás az Azure Storage-titkosításban található 256 bites AES-titkosítást használja, és a kulcsok rendszer által felügyeltek (alapértelmezés). 

A szolgáltatás alapértelmezés szerint az átviteli réteg biztonságával titkosítja az adatokat a mozgásban. A rugalmas kiszolgálók csak a Transport Layer Security (TLS 1.2) használó titkosított kapcsolatokat támogatják, és minden TLS 1.0-val és TLS 1.1-sel bejövő kapcsolat le lesz tiltva. 

További [információért tekintse](https://docs.mongodb.com/manual/tutorial/configure-ssl) meg, hogyan használhat titkosított kapcsolatokat rugalmas kiszolgálókkal.

A rugalmas kiszolgálók teljes körű privát hozzáférést szolgáltatásokat szolgáltatásokat kínálnak a kiszolgálókhoz [Azure-beli virtuális](../../virtual-network/virtual-networks-overview.md) hálózat (VNet) integrációjával. Az Azure-beli virtuális hálózat kiszolgálói csak magánhálózati IP-címeken keresztül csatlakoztathatóak és csatlakoztathatóak. VNet-integráció esetén a nyilvános hozzáférés le van tiltva, és a kiszolgálók nem érhetőek el nyilvános végpontok használatával. 

További [információ:](concepts-networking.md) Hálózati fogalmak.


## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A rugalmas kiszolgálószolgáltatás beépített teljesítményfigyelési és riasztási funkciókkal rendelkezik. Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 nap előzményt biztosít. A metrikákhoz riasztásokat konfigurálhat. A szolgáltatás elérhetővé teszi a gazdakiszolgáló metrikákat az erőforrások kihasználtságának monitorozása érdekében, és lehetővé teszi a lassú lekérdezési naplók konfigurálását. Ezekkel az eszközökkel gyorsan optimalizálhatja a számítási feladatokat, és a legjobb teljesítmény érdekében konfigurálhatja a kiszolgálót. 

További [információ: Figyelési](concepts-monitoring.md) fogalmak.

## <a name="migration"></a>Áttelepítés

A szolgáltatás a MySQL közösségi verzióját futtatja. Ez teljes körű alkalmazáskompatibilitást tesz lehetővé, és minimális újrafactorolási költségeket igényel a MySQL-motoron fejlesztett meglévő alkalmazások egykiszolgálós szolgáltatásba való áttelepítéséhez. Az áttelepítés egyetlen kiszolgálóra az alábbi lehetőségek egyikével hajtható végre:

- **Memóriakép és** visszaállítás – Az offline migrálások esetén, amelyekben a felhasználók megengedhetnek némi állásidőt, a memóriaképet és a visszaállítást olyan közösségi eszközökkel, mint a mysqldump/mydumper, a migrálás leggyorsabb módját biztosítják. A részletekért lásd: Áttelepítés memóriakép és visszaállítás használatával. 
- **Azure Database Migration Service** – Az egyetlen kiszolgálóra való zökkenőmentes és egyszerűsített, [](../../dms/tutorial-mysql-azure-mysql-online.md) minimális állásidővel Azure Database Migration Service áttelepítések kihasználhatóak. 

## <a name="azure-regions"></a>Azure-régiók

A számítási feladatok Azure-ban való futtatásának egyik előnye, hogy globálisan elérhető. A rugalmas kiszolgáló Azure Database for MySQL jelenleg a következő Azure-régiókban érhető el:

| Region | Rendelkezésre állás | Zónaredundáns hatékony | 
| --- | --- | --- |
| Nyugat-Európa | :heavy_check_mark: | :heavy_check_mark: |
| Észak-Európa | :heavy_check_mark: | :heavy_check_mark: |
| Az Egyesült Királyság déli régiója | :heavy_check_mark: | :heavy_check_mark: | 
| USA 2. keleti régiója | :heavy_check_mark: | :heavy_check_mark: |
| USA 2. nyugati régiója | :heavy_check_mark: | :heavy_check_mark: |
| Az USA középső régiója | :heavy_check_mark: | :x: | 
| USA keleti régiója | :heavy_check_mark: | :heavy_check_mark: |
| Közép-Kanada | :heavy_check_mark: | :x: | 
| Délkelet-Ázsia | :heavy_check_mark: | :heavy_check_mark: |
| Dél-Korea középső régiója | :heavy_check_mark: | :x: | 
| Kelet-Japán | :heavy_check_mark: | :heavy_check_mark: | 
| Kelet-Ausztrália | :heavy_check_mark: | :heavy_check_mark: |

Hamarosan új régiók hozzáadásán dolgozunk.

## <a name="contacts"></a>Kapcsolattartók
Ha bármilyen kérdése vagy javaslata van a rugalmas Azure Database for MySQL kapcsolatban, küldjön egy e-mailt a Azure Database for MySQL csapatának[ @Ask (Azure DB for MySQL).](mailto:AskAzureDBforMySQL@service.microsoft.com) Ez az e-mail-cím nem műszaki támogatási alias.

Ezenkívül a következő elérhetőségeken léphet velünk kapcsolatba:

- Ha az Azure-támogatással szeretne kapcsolatba lépni, [nyújtson be igénylést az Azure Portalon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Ha a fiókjával van probléma, nyújtson be [támogatási kérelmet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) az Azure Portalon.
- Ha visszajelzést szeretne küldeni vagy új szolgáltatásokat kérne, hozzon létre egy bejegyzést a [UserVoice-on](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Következő lépések
Most, hogy elolvasta az egykiszolgálós Azure Database for MySQL mód bevezetését, készen áll a következőre:

- Hozza létre az első kiszolgálóját. 
  - [Rugalmas Azure Database for MySQL kiszolgáló létrehozása Azure Portal](quickstart-create-server-portal.md)
  - [Rugalmas Azure Database for MySQL létrehozása az Azure CLI használatával](quickstart-create-server-cli.md)
  - [Rugalmas Azure Database for MySQL kezelése az Azure CLI használatával](how-to-manage-server-portal.md)

- Készítse el első alkalmazását az Ön által választott nyelven:
  - [Python](connect-python.md)
  - [Php](connect-php.md)
