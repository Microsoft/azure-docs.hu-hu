---
title: üzletileg kritikus szolgáltatási szintet
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ismerje meg az üzleti szempontból kritikus szolgáltatási szintet Azure SQL Database és az Azure SQL felügyelt példányát.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: 830ecc44d0def13e51cb06704bef429bb8860cd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780223"
---
# <a name="business-critical-tier---azure-sql-database-and-azure-sql-managed-instance"></a>Üzletileg kritikus réteg-Azure SQL Database és az Azure SQL felügyelt példánya 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> A DTU-vásárlási modellben a üzletileg kritikus-csomag prémium szintűnek kell lennie. A virtuális mag-alapú vásárlási modellnek a DTU-alapú vásárlási modellel való összehasonlítását lásd: [Azure SQL Database vásárlási modellek és erőforrások](purchasing-models.md).

A Azure SQL Database és az Azure SQL felügyelt példánya a felhőalapú környezethez igazított SQL Server adatbázismotor-architektúrán alapul, hogy az infrastruktúra meghibásodása esetén is biztosítson 99,99%-os rendelkezésre állást. Három építészeti modell van használatban:
- Általános célú/standard 
- Üzletileg kritikus/prémium
- Rugalmas skálázás

A prémium/üzletileg kritikus szolgáltatási szint modellje az adatbázismotor folyamatainak egyik fürtjén alapul. Ez az építészeti modell arra támaszkodik, hogy mindig a rendelkezésre álló adatbázismotor-csomópontok kvóruma van, és a karbantartási tevékenységek során a számítási feladatok minimális teljesítményére is hatással van. Az nagy kapacitású szolgáltatási réteg jelenleg csak Azure SQL Database (nem SQL felügyelt példány esetében érhető el), és egy rugalmasan méretezhető tárolási és számítási teljesítményi réteg, amely az Azure-architektúrát kihasználva kibővíti az adatbázis tárolási és számítási erőforrásait Azure SQL Database az általános célú és üzletileg kritikus szolgáltatási szintjein elérhető korlátokon túl.

Az Azure-ban az operációs rendszer, az illesztőprogramok és a SQL Server adatbázis-motorja transzparens módon, a végfelhasználók minimális lejárati ideje alatt is átlátható. 

A prémium szintű rendelkezésre állást a prémium és üzletileg kritikus szolgáltatási szinteken engedélyezjük, és olyan nagy teljesítményű számítási feladatokra tervezték, amelyek a folyamatban lévő karbantartási műveletek miatt nem tolerálják a teljesítményt.

A számítás és a tárolás a prémium modell egyetlen csomópontján van integrálva. Ebben az építészeti modellben a magas rendelkezésre állást a számítási (SQL Server adatbázismotor-folyamat) és a (helyileg csatlakoztatott SSD-k) a négy csomópontos fürtre telepített, a SQL Server [Always On rendelkezésre állási csoportokhoz](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával lehet megvalósítani.

![Az adatbázismotor csomópontjainak fürtje](./media/service-tier-business-critical/business-critical-service-tier.png)

A SQL Server adatbázismotor és a mögöttes. mdf-és. ldf-fájlok is ugyanarra a csomópontra kerülnek, mint a helyileg csatlakoztatott SSD-tároló, amely kis késleltetést biztosít a munkaterhelés számára. A magas rendelkezésre állás a SQL Server [Always On rendelkezésre állási csoportokhoz](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával valósul meg. Minden adatbázis olyan adatbázis-csomópontok fürtje, amelyek egy elsődleges adatbázissal elérhetők az ügyfelek munkaterhelései számára, és három másodlagos folyamat, amely az adatok másolatait tartalmazza. Az elsődleges csomópont folyamatosan leküldi a változásokat a másodlagos csomópontokon annak érdekében, hogy az adat elérhető legyen a másodlagos replikán, ha az elsődleges csomópont valamilyen okból meghiúsul. A feladatátvételt az SQL Server adatbázismotor kezeli – az egyik másodlagos replika lesz az elsődleges csomópont, és létrejön egy új másodlagos replika, amely biztosítja, hogy elegendő csomópont legyen a fürtben. A rendszer automatikusan átirányítja a munkaterhelést az új elsődleges csomópontra.

Emellett üzletileg kritikus-fürt beépített [olvasási Felskálázási](read-scale-out.md) képességgel rendelkezik, amely ingyenes, beépített írásvédett csomópontot biztosít, amely csak olvasható lekérdezések (például jelentések) futtatására használható, amelyek nem befolyásolhatják az elsődleges munkaterhelés teljesítményét.

## <a name="when-to-choose-this-service-tier"></a>Mikor válassza ezt a szolgáltatási szintet

Üzletileg kritikus szolgáltatási szintet olyan alkalmazások számára tervezték, amelyek alacsony késleltetésű válaszokat igényelnek a mögöttes SSD-tárolóból (átlagosan 1-2 MS), a gyors helyreállítást, ha az alapul szolgáló infrastruktúra meghibásodik, vagy a jelentések, elemzések és csak olvasható lekérdezések az elsődleges adatbázis díjmentesen olvasható másodlagos replikáját igénylik.

A legfontosabb ok, amiért érdemes kiválasztania üzletileg kritikus szolgáltatási szintet általános célú szintje helyett:
-   **Alacsony I/O-késési követelmények** – a tárolási rétegből gyors választ igénylő munkaterhelések (átlagosan 1-2 ezredmásodperc) a üzletileg kritikus szintet kell használniuk. 
-   **Gyakori kommunikáció az alkalmazás és az adatbázis között**. Azok üzletileg kritikus az alkalmazások, amelyek nem tudják kihasználni az alkalmazás-réteg gyorsítótárazását, vagy [megkérik a kötegelt feldolgozást](../performance-improve-use-batching.md) , és sok olyan SQL-lekérdezést kell elküldeniük, amelyet gyorsan fel kell dolgozni, jó jelöltek
-   **Nagy számú frissítés** – az INSERT, a Update és a DELETE művelet módosítja a memóriában (piszkos oldal) található adatlapokat, amelyeket a művelettel rendelkező adatfájlba kell menteni `CHECKPOINT` . A lehetséges adatbázismotor-folyamatok összeomlása vagy az adatbázis nagy mennyiségű piszkos oldallal rendelkező feladatátvétele általános célú szinten növelheti a helyreállítási időt. Üzletileg kritikus réteg használata, ha olyan számítási feladattal rendelkezik, amely sok memóriában végzett módosítást okoz. 
-   **Az adatmódosítást követően hosszú ideig futó tranzakciók**. A több időt megnyitó tranzakciók meggátolják a naplófájlok csonkítása, ami növelheti a napló méretét és a [virtuális naplófájlok számát (VLF)](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). A feladatátvétel után a VLFs nagy száma lelassíthatja az adatbázis helyreállítását.
-   **Munkaterhelések jelentési és elemzési lekérdezésekkel** , amelyek átirányíthatók a díjmentes másodlagos írásvédett replikára.
- **Nagyobb rugalmasság és gyorsabb helyreállítás a hibáktól**. Rendszerhiba esetén az elsődleges példányon lévő adatbázis le lesz tiltva, és az egyik másodlagos replika azonnal új írható-olvasható elsődleges adatbázis lesz, amely készen áll a lekérdezések feldolgozására. Az adatbázismotor nem kell elemezni és megismételni a tranzakciókat a naplófájlból, és be kell töltenie a memória pufferében lévő összes adatmennyiséget.
- **Speciális adatsérülés elleni védelem**. A üzletileg kritikus szintű adatbázis-replikákat használ az üzletmenet folytonossága érdekében, így a szolgáltatás automatikusan kihasználja az oldal javítását, ami ugyanaz, mint a SQL Server adatbázis- [tükrözési és rendelkezésre állási csoportok](/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)esetében. Abban az esetben, ha egy replika nem tud beolvasni egy oldalt egy adatintegritási probléma miatt, a lap egy másik replikából lesz beolvasva, és az adatvesztés vagy az ügyfél leállása nélkül nem olvasható oldal jelenik meg. Ez a funkció általános célú szinten alkalmazható, ha az adatbázis rendelkezik geo-másodlagos replikával.
- **Magasabb rendelkezésre állás** – a többszintű konfigurációban üzletileg kritikus szint az 99,995%-os rendelkezésre állást garantálja, az általános célú szint 99,99%-ában.
- A Geo-replikációval konfigurált **gyors geo-helyreállítási** üzletileg kritikusi réteg 5 sec-os garantált helyreállítási időcélkitűzéssel (RPO) rendelkezik, és 30 mp-es helyreállítási időszakot (RTO) biztosít a telepített órák 100%-ában.

## <a name="next-steps"></a>Következő lépések

- Megtalálhatja az erőforrás jellemzőit (magok, I/O, memória) üzletileg kritikus szinten az [SQL felügyelt példányában](../managed-instance/resource-limits.md#service-tier-characteristics), az önálló adatbázist a [virtuális mag-modellben](resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen4) vagy a [DTU modellben](resource-limits-dtu-single-databases.md#premium-service-tier), vagy a rugalmas készletet a [virtuális mag-modellben](resource-limits-vcore-elastic-pools.md#business-critical---provisioned-compute---gen4) és a [DTU-modellben](resource-limits-dtu-elastic-pools.md#premium-elastic-pool-limits).
- Ismerkedjen meg [általános célú](service-tier-general-purpose.md) -és [nagy kapacitású](service-tier-hyperscale.md) -szintekkel.
- A [Service Fabric](../../service-fabric/service-fabric-overview.md)megismerése.
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd az [üzletmenet folytonosságát](business-continuity-high-availability-disaster-recover-hadr-overview.md)ismertető témakört.