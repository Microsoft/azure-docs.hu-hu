---
title: Azure Database for PostgreSQL – rugalmas kiszolgáló
description: Áttekintést nyújt Azure Database for PostgreSQL rugalmas kiszolgálóról.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 03/03/2021
ms.openlocfilehash: d36dcfccb6843947991b2548af6dd98b81adb40e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049508"
---
# <a name="azure-database-for-postgresql---flexible-server"></a>Azure Database for PostgreSQL – rugalmas kiszolgáló

A PostgreSQL Community Edition által működtetett [Azure Database for PostgreSQL](../overview.md) három üzembe helyezési módban érhető el:

- [Önálló kiszolgáló](../overview-single-server.md)
- Rugalmas kiszolgáló (előzetes verzió)
- Rugalmas skálázás (Citus)

Ebben a cikkben áttekintjük és bemutatjuk a rugalmas kiszolgáló üzembe helyezési modelljének alapvető fogalmait.

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

## <a name="overview"></a>Áttekintés

Azure Database for PostgreSQL – a rugalmas kiszolgáló egy teljes körűen felügyelt adatbázis-szolgáltatás, amely részletesebb szabályozást és rugalmasságot biztosít az adatbázis-felügyeleti funkciók és a konfigurációs beállítások felett. Általánosságban elmondható, hogy a szolgáltatás nagyobb rugalmasságot és kiszolgáló-konfigurációt biztosít a felhasználói követelmények alapján. A rugalmas kiszolgáló architektúrája lehetővé teszi a felhasználók számára, hogy az rézvezetékes végezhet az alacsonyabb késés érdekében, a magas rendelkezésre állást egyetlen rendelkezésre állási zónában és több rendelkezésre állási zónában is kiválasszák. A rugalmas kiszolgálók jobb költség-optimalizálási szabályozást is biztosítanak, amely lehetővé teszi a kiszolgáló és a kihasználható számítási szint leállítását és elindítását, amely ideális olyan munkaterhelésekhez, amelyeknek nincs szükségük teljes számítási kapacitásra. A szolgáltatás jelenleg a PostgreSQL 11 és 12 közösségi verzióját támogatja. A szolgáltatás jelenleg előzetes verzióban érhető el, és ma már számos  [Azure-régióban](https://azure.microsoft.com/global-infrastructure/services/)elérhető.

![Rugalmas kiszolgáló – áttekintés](./media/overview/overview-flexible-server.png)


A rugalmas kiszolgálók a legmegfelelőbbek a következőhöz:

- Hatékonyabb vezérlést és testreszabást igénylő alkalmazások fejlesztése.
- Zóna redundáns magas rendelkezésre állása
- Felügyelt karbantartási időszakok
  
## <a name="high-availability"></a>Magas rendelkezésre állás

A rugalmas kiszolgálói üzemi modell úgy lett kialakítva, hogy támogassa a magas rendelkezésre állást az önálló rendelkezésre állási zónában és több rendelkezésre állási zónában. Az architektúra elkülöníti a számítási és a tárolási kapacitást. Az adatbázismotor Linux rendszerű virtuális gépen fut, míg az adatfájlok az Azure Storage-ban találhatók. A tároló három helyileg redundáns szinkron példányt tart fenn az adatbázisfájlok számára az adattartósság biztosítása érdekében.

Tervezett vagy nem tervezett feladatátvételi események esetén, ha a kiszolgáló leáll, a szolgáltatás a következő automatizált eljárással biztosítja a kiszolgálók magas rendelkezésre állását:

1. Új számítási linuxos virtuális gép van kiépítve.
2. Az adatfájlokkal rendelkező tárterület az új virtuális gépre van leképezve
3. A PostgreSQL-adatbázismotor online állapotba kerül az új virtuális gépen.

Az alábbi ábrán látható a virtuális gép és a tárolási hiba átmenete.

 :::image type="content" source="./media/overview/overview-azure-postgres-flex-virtualmachine.png" alt-text="Rugalmas kiszolgáló – virtuális gépek és tárolási hibák":::

Ha a zóna redundáns magas rendelkezésre állása be van állítva, a szolgáltatás kiépíti és karbantartja a gyors készenléti kiszolgálót a rendelkezésre állási zónán belül ugyanazon az Azure-régióban. A forráskiszolgálón lévő adatváltozásokat a rendszer szinkron módon replikálja a készenléti kiszolgálóra, így biztosítva a nulla adatvesztést. A zóna redundáns, magas rendelkezésre állása esetén a tervezett vagy nem tervezett feladatátvételi esemény elindítása után a készenléti kiszolgáló azonnal online állapotba kerül, és elérhető a bejövő tranzakciók feldolgozásához. Ez lehetővé teszi a szolgáltatás rugalmasságát a rendelkezésre állási zóna meghibásodása esetén egy olyan Azure-régióban, amely támogatja a több rendelkezésre állási zónát, ahogy az alábbi képen is látható.

 :::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Zóna redundáns magas rendelkezésre állása":::

 További részletekért tekintse meg a [magas rendelkezésre állásról szóló dokumentumot](./concepts-high-availability.md) .

## <a name="automated-patching-with-managed-maintenance-window"></a>Automatizált javítás a felügyelt karbantartási időszakmal

A szolgáltatás a mögöttes hardver, az operációs rendszer és az adatbázis motorjának automatizált javítását végzi. A javítás biztonsági és szoftverfrissítéseket is tartalmaz. A PostgreSQL motor esetében az alverziók frissítései a tervezett karbantartási kiadás részeként is szerepelnek. A felhasználók úgy konfigurálhatják a javítási ütemtervet, hogy a rendszer felügyelhető legyen, vagy definiálja az egyéni ütemtervet. A karbantartási ütemterv során a rendszer a javítást alkalmazza, és előfordulhat, hogy a frissítés befejezéséhez újra kell indítani a kiszolgálót a javítási folyamat részeként. Az egyéni ütemterv segítségével a felhasználók előre jelezheti a javítási ciklust, és olyan karbantartási időszakot választhatnak, amelynek minimális hatása van a vállalatra. Általánosságban elmondható, hogy a szolgáltatás a folyamatos integráció és a kiadás részeként havi kiadási ütemtervet követ.

## <a name="automatic-backups"></a>Automatikus biztonsági mentések

A rugalmas kiszolgáló szolgáltatás automatikusan létrehozza a kiszolgáló biztonsági másolatait, és a helyileg konfigurált, a felesleges zónában (ZRS) tárolja azokat. A biztonsági mentések segítségével a kiszolgáló bármely időpontra visszaállítható a biztonsági mentés megőrzési ideje alatt. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Az adatmegőrzés opcionálisan 35 napig is konfigurálható. Minden biztonsági mentés AES 256 bites titkosítással van titkosítva. További részletekért tekintse meg a [biztonsági mentéseket](./concepts-backup-restore.md) .

## <a name="adjust-performance-and-scale-within-seconds"></a>Teljesítmény módosítása és skálázása másodperceken belül

A rugalmas kiszolgáló szolgáltatás három számítási szinten érhető el: feltört, általános célú és memória optimalizálva. A feltört réteg az alacsony költséghatékonyságú fejlesztési és alacsony párhuzamosságú munkaterhelésekhez ideális megoldás, amelyeknek nincs szükségük teljes számítási kapacitásra. Az optimalizált általános célú és memória jobban alkalmazkodik a magas párhuzamosságot, méretezést és kiszámítható teljesítményt igénylő éles munkaterhelésekhez. Az első alkalmazását egy kis adatbázison hozhatja létre havonta néhány dollárért, majd zökkenőmentesen módosíthatja a méretezést, hogy megfeleljen a megoldás igényeinek.

## <a name="stopstart-server-to-lower-tco"></a>A kiszolgáló leállítása/elindítása a TCO alsó szintjén

A rugalmas kiszolgálói szolgáltatás lehetővé teszi a kiszolgáló igény szerinti leállítását és elindítását a TCO csökkentése érdekében. A számítási szintek számlázása azonnal leáll, amikor a kiszolgáló leáll. Ezzel jelentős költségmegtakarítást érhet el a fejlesztés, a tesztelés és az időkorlátos kiszámítható éles számítási feladatok esetében. A kiszolgáló hét napig leállított állapotban marad, kivéve, ha hamarabb újraindul.

## <a name="enterprise-grade-security"></a>Nagyvállalati szintű biztonság

A rugalmas kiszolgáló szolgáltatás az FIPS 140-2 ellenőrzött titkosítási modult használja a REST-alapú adatok tárolási titkosításához. Az adatokat, beleértve a biztonsági másolatokat, valamint a lekérdezések futtatása közben létrehozott ideiglenes fájlokat is titkosítja a rendszer. A szolgáltatás az Azure Storage-titkosításban található AES 256 bites titkosítást használja, a kulcsokat pedig rendszerfelügyelheti (alapértelmezett). A szolgáltatás alapértelmezés szerint a Transport Layer Security (SSL/TLS) által kényszerített mozgásban lévő adatforgalom titkosítását végzi. A szolgáltatás csak a 1,2-es TLS-verziót kényszeríti és támogatja.

A rugalmas kiszolgálók lehetővé teszik a kiszolgálók teljes magánhálózati elérését az Azure Virtual Network (VNet Integration) használatával. Az Azure virtuális hálózatban található kiszolgálók csak magánhálózati IP-címeken keresztül érhetők el és csatlakoztathatók. A VNet-integrációval a nyilvános hozzáférés megtagadva, és a kiszolgálók nem érhetők el nyilvános végpontok használatával.

## <a name="monitoring-and-alerting"></a>Figyelés és riasztás

A rugalmas kiszolgáló szolgáltatás beépített teljesítmény-figyelési és riasztási funkciókkal rendelkezik. Minden Azure-metrika egyperces gyakorisággal rendelkezik, és minden metrika 30 napos előzményt biztosít. A mérőszámokra vonatkozó riasztásokat is beállíthat. A szolgáltatás elérhetővé teszi a gazdagép-kiszolgáló metrikáit az erőforrások kihasználtságának figyelésére, valamint a lassú lekérdezési naplók konfigurálására. Ezen eszközök használatával gyorsan optimalizálhatja a számítási feladatokat, és konfigurálhatja a kiszolgálót a legjobb teljesítmény érdekében.

## <a name="azure-regions"></a>Azure-régiók

A számítási feladatok Azure-ban való futtatásának egyik előnye, hogy globálisan elérhető. A rugalmas kiszolgáló jelenleg a következő Azure-régiókban érhető el:

| Region | Rendelkezésre állás | Zóna redundáns HA | 
| --- | --- | --- |
| Nyugat-Európa | :heavy_check_mark: | :heavy_check_mark: |
| Észak-Európa | :heavy_check_mark: | :heavy_check_mark: |
| Az Egyesült Királyság déli régiója | :heavy_check_mark: | :heavy_check_mark: | 
| USA 2. keleti régiója | :heavy_check_mark: | :heavy_check_mark: |
| USA 2. nyugati régiója | :heavy_check_mark: | :heavy_check_mark: |
| Az USA középső régiója | :heavy_check_mark: | :heavy_check_mark: | 
| USA keleti régiója | :heavy_check_mark: | :heavy_check_mark: | 
| Délkelet-Ázsia | :heavy_check_mark: | :heavy_check_mark: |
| Kelet-Japán | :heavy_check_mark: | :heavy_check_mark: | 

Továbbra is új régiókat adunk hozzá. 

## <a name="migration"></a>Áttelepítés

A szolgáltatás a PostgreSQL közösségi verzióját futtatja. Ez lehetővé teszi az alkalmazások teljes kompatibilitását, és minimális újraszámítási költségeket igényel a PostgreSQL-motoron kifejlesztett meglévő alkalmazások rugalmas kiszolgálóra történő áttelepítéséhez. 

- **Memóriakép és visszaállítás** – kapcsolat nélküli áttelepítések esetén, ahol a felhasználók bizonyos állásidőt, kiírást és visszaállítást biztosíthatnak olyan közösségi eszközök használatával, mint a pg_dump és az pg_restore a leggyorsabb áttelepítési módot biztosítanak. A részletekért lásd: az [áttelepítés a dump és a Restore használatával](../howto-migrate-using-dump-and-restore.md) .
- **Azure Database Migration Service** – a rugalmas kiszolgáló zökkenőmentes és egyszerűbb áttelepítésének minimális állásidővel való kihasználása érdekében Azure Database Migration Service használható. Lásd a DMS-t a [portálon](../../dms/tutorial-postgresql-azure-postgresql-online-portal.md) és a DMS-n [keresztül a parancssori](../../dms/tutorial-postgresql-azure-postgresql-online.md)felületen Áttelepítheti az Azure Database for PostgreSQL – egyetlen kiszolgálóról a rugalmas kiszolgálóra. További részletekért tekintse meg ezt a [DMS-cikket](../../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) .

## <a name="next-steps"></a>Következő lépések

Most, hogy elolvasta a Azure Database for PostgreSQL rugalmas kiszolgáló üzembe helyezési módjának bevezetését, készen áll az első kiszolgáló létrehozására: [hozzon létre egy Azure Database for PostgreSQL-rugalmas kiszolgálót a Azure Portal használatával](./quickstart-create-server-portal.md)