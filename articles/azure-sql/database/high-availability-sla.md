---
title: Magas rendelkezésre állás
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Megismeri a Azure SQL Database és SQL Managed Instance magas rendelkezésre állású képességeket és funkciókat
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 21ac73b461ebcb171f48621aa27a16dfc0e8c936
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781714"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Magas rendelkezésre állás a Azure SQL Database és SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A magas rendelkezésre állású architektúra Azure SQL Database-ben és SQL Managed Instance-ben az a cél, hogy az adatbázis az idő legalább 99,99%-ában elérhető legyen (a különböző szintekre vonatkozó konkrét SLA-val kapcsolatos további információkért tekintse meg az [SLA-t](https://azure.microsoft.com/support/legal/sla/sql-database/)az Azure SQL Database és az SQL Managed Instance esetében) anélkül, hogy aggódnia kellene a karbantartási műveletek és kimaradások hatásával kapcsolatban. Az Azure automatikusan kezeli a kritikus karbantartási feladatokat, például a javításokat, a biztonsági mentéseket, a Windows- és Azure SQL-frissítéseket, valamint a nem tervezett eseményeket, például a mögöttes hardver-, szoftver- vagy hálózati hibákat.  Ha a mögöttes adatbázis Azure SQL Database vagy a rendszer átveszi a munkát, az állásidő nem észrevehető, ha újrapróbálkozási logikát alkalmaz az alkalmazásban. [](develop-overview.md#resiliency) SQL Database és SQL Managed Instance még a legkritikusabb körülmények között is gyorsan helyreállíthatja az adatokat, így biztosítva az adatok folyamatos rendelkezésre állát.

A magas rendelkezésre állású megoldás úgy lett kialakítva, hogy a hibák miatt a lekötött adatok soha ne essnek el, hogy a karbantartási műveletek ne befolyásolják a számítási feladatokat, és hogy az adatbázis ne legyen rendszerkomplikáns a szoftverarchitektúra meghibásodási pontjaként. Nincsenek karbantartási ablakok vagy állásidők, amelyek miatt le kell állítania a számítási feladatot az adatbázis frissítése vagy karbantartása közben.

Két magas rendelkezésre állású architekturális modell áll rendelkezésre:

- **Standard rendelkezésre állási modell,** amely a számítási és tárolási erőforrások elkülönítésére épül.  A távoli tárolási szint magas rendelkezésre állására és megbízhatóságára támaszkodik. Ez az architektúra olyan költségvetés-orientált üzleti alkalmazásokat célozza meg, amelyek a karbantartási tevékenységek során bizonyos teljesítménycsökkenést tolerálnak.
- **Prémium rendelkezésre állási modell,** amely adatbázismotor-folyamatok fürtjére épül. Arra a tényre támaszkodik, hogy mindig rendelkezésre áll az adatbázismotor-csomópontok kvóruma. Ez az architektúra olyan kritikus fontosságú alkalmazásokat célozza meg, amelyek nagy I/O-teljesítményt és magas tranzakciós sebességet nyújtanak, és minimális teljesítményt garantálnak a számítási feladatokra a karbantartási tevékenységek során.

SQL Database és SQL Managed Instance a SQL Server adatbázismotor legújabb, stabil verzióján és a Windows operációs rendszeren is futnak, és a legtöbb felhasználó nem veszi észre, hogy a frissítések folyamatosan futnak.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Alapszintű, Standard és általános célú szintű helyileg redundáns rendelkezésre állás

Az Alapszintű, Standard és általános célú szolgáltatási szintek a standard rendelkezésre állási architektúrát kihasználják a kiszolgáló nélküli és a kiépített számításhoz is. Az alábbi ábra négy különböző csomópontot mutat be a külön számítási és tárolási rétegekkel.

![A számítás és a tárolás elkülönítése](./media/high-availability-sla/general-purpose-service-tier.png)

A standard rendelkezésre állási modell két réteget tartalmaz:

- Állapot nélküli számítási réteg, amely futtatja a folyamatot, és csak átmeneti és gyorsítótárazott adatokat tartalmaz, például TempDB- és modelladatbázisokat a csatlakoztatott SSD-n, valamint megtervzi a gyorsítótárat, a pufferkészletet és az oszlopcentrikus készletet a `sqlservr.exe` memóriában. Ezt az állapot nélküli csomópontot az Azure Service Fabric, amely inicializálja a csomópont állapotát, és szükség esetén feladatátvételt hajt `sqlservr.exe` végre egy másik csomópontra.
- Állapot-alapú adatréteg az Azure Blob Storage-ban tárolt adatbázisfájlokkal (.mdf/.ldf). Az Azure Blob Storage beépített adat rendelkezésre állási és redundancia funkcióval rendelkezik. Ez garantálja, hogy az adatfájl naplófájljában vagy oldalán található összes rekord megmarad a folyamat `sqlservr.exe` összeomlása esetén is.

Amikor az adatbázismotort vagy az operációs rendszert frissíti, vagy hibát észlel, az Azure Service Fabric áthelyezi az állapot nélküli folyamatot egy másik, megfelelő szabad kapacitással működő, állapot nélküli számítási `sqlservr.exe` csomópontra. Az Áthelyezés nem érinti az Azure Blob Storage-ban lévő adatokat, és az adatok/naplófájlok az újonnan inicializált folyamathoz vannak `sqlservr.exe` csatolva. Ez a folyamat 99,99%-os rendelkezésre állást garantál, de egy nagy számítási feladat teljesítménycsökkenést tapasztalhat az átállás során, mivel az új folyamat hideg `sqlservr.exe` gyorsítótárral kezdődik.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>általános célú szintű zónaredundáns rendelkezésre állás (előzetes verzió)

Az általános célú szolgáltatási szint zónaredundáns konfigurációja kiszolgáló nélküli és kiépített számítási feladatokhoz is elérhető. Ez a konfiguráció [Azure Availability Zones](../../availability-zones/az-overview.md) adatbázisokat replikál több fizikai helyre egy   Azure-régión belül.A zónaredundanika kiválasztásával az új és meglévő kiszolgáló nélküli, illetve kiépített általános célú egyedi adatbázisokat és rugalmas készleteket sokkal nagyobb hibákkal, például katasztrofális adatközpont-kimaradásokkal szemben teszi rugalmassá az alkalmazáslogika változtatása nélkül.

Az általános célú szint zónaredundáns konfigurációja két rétegből áll:  

- Állapot-figyelő adatréteg, amely tartalmazza a ZRS-ben (zónaredundáns tárolás) tárolt adatbázisfájlokat (.mdf/.ldf). A [ZRS](../../storage/common/storage-redundancy.md) használatával az adatokat és a naplófájlokat a rendszer szinkron módon átmásolja három fizikailag elkülönített Azure rendelkezésre állási zónába.
- Állapot nélküli számítási réteg, amely az sqlservr.exe-folyamatot futtatja, és csak átmeneti és gyorsítótárazott adatokat tartalmaz, például TempDB- és modelladatbázisokat a csatlakoztatott SSD-n, valamint a memóriában megtervzi a gyorsítótárat, a pufferkészletet és az oszlopcentrikus készletet. Ezt az állapot nélküli csomópontot az Azure Service Fabric üzemelteti, amely inicializálja sqlservr.exe csomópont állapotát, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik csomópontra. A zónaredundáns kiszolgáló nélküli és kiépített általános célú adatbázisokhoz a tartalék kapacitású csomópontok más kiszolgálókon is elérhetők Availability Zones feladatátvételhez.

Az általános célú szolgáltatási szint magas rendelkezésre állású architektúrájának zónaredundáns verzióját az alábbi ábra szemlélteti:

![Zónaredundáns konfiguráció általános célú](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> A zónaredundáns konfiguráció csak akkor érhető el, ha a Gen5 számítási hardver van kiválasztva. Ez a funkció nem érhető el a SQL Managed Instance. A kiszolgáló nélküli és kiépített általános célú szint zónaredundáns konfigurációja csak a következő régiókban érhető el: USA keleti régiója, USA 2. keleti régiója, USA 2. nyugati régiója, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Ausztrália keleti régiója, Kelet-Japán, Egyesült Királyság déli régiója és Közép-Franciaország.

> [!NOTE]
> általános célú 80 virtuális magos adatbázisok teljesítménye a zónaredundáns konfigurációval teljesítménycsökkenést tapasztalhat. Emellett az olyan műveletek, mint a biztonsági mentés, a visszaállítás, az adatbázis másolása, a Geo-DR-kapcsolatok beállítása és a zónaredundáns adatbázisok üzletileg kritikus-ről általános célú-re való visszalépése lassabb teljesítményt tapasztalhat az 1 TB-osnál nagyobb méretű adatbázisoknál. További [információért tekintse](single-database-scale.md) meg az adatbázis méretezésével kapcsolatos késési dokumentációt.
> 
> [!NOTE]
> Az előzetes verzióra nem vonatkozik a Fenntartott példány

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Prémium és üzletileg kritikus szolgáltatásszint helyileg redundáns rendelkezésre állása

A Prémium és üzletileg kritikus szolgáltatásszintek a prémium rendelkezésre állási modellt kihasználják, amely egyetlen csomóponton integrálja a számítási erőforrásokat (folyamatot) és `sqlservr.exe` a tárolást (helyileg csatlakoztatott SSD).. A magas rendelkezésre állás úgy érhető el, hogy a számítási és a tárolási kapacitást is replikálja további csomópontokra egy három–négy csomópontos fürt létrehozásával.

![Adatbázismotor-csomópontok fürte](./media/high-availability-sla/business-critical-service-tier.png)

A mögöttes adatbázisfájlok (.mdf/.ldf) a csatolt SSD-tárolóra kerülnek, így nagyon kis késésű I/O-t biztosítanak a számítási feladatnak. A magas rendelkezésre állás az Always On rendelkezésre állási csoportokhoz SQL Server [hasonló technológiával valósítható meg.](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) A fürt egyetlen elsődleges replikát tartalmaz, amely elérhető az olvasási-írási ügyfél számítási feladatokhoz, és legfeljebb három másodlagos replikát (számítási és tárolási) tartalmaz, amelyek adatmásolatokat tartalmaznak. Az elsődleges csomópont sorrendben folyamatosan leküldi a módosításokat a másodlagos csomópontokra, és gondoskodik arról, hogy az adatok szinkronizálva vannak legalább egy másodlagos replikával az egyes tranzakciók véglegesítése előtt. Ez a folyamat garantálja, hogy ha az elsődleges csomópont valamilyen okból összeomlik, mindig van egy teljesen szinkronizált csomópont a feladatátvételhez. A feladatátvételt az Azure-beli virtuális Service Fabric. Ha a másodlagos replika lesz az új elsődleges csomópont, létrejön egy másik másodlagos replika, amely biztosítja, hogy a fürt elegendő csomóponttal (kvórumkészlet) rendelkezik. A feladatátvétel befejezése után a Azure SQL a rendszer automatikusan átirányítja a kapcsolatokat az új elsődleges csomópontra.

További előny, hogy a prémium rendelkezésre állási modell lehetővé teszi a csak olvasási Azure SQL kapcsolatoknak az egyik másodlagos replikára való átirányítását. Ezt a funkciót [olvasásos felskálának nevezzük.](read-scale-out.md) 100%-os további számítási kapacitást biztosít díjmentesen a csak olvasható műveletek, például az elemzési számítási feladatok elsődleges replikából való kiterhelése esetén.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Prémium és üzletileg kritikus szintű zónaredundáns rendelkezésre állás 

Alapértelmezés szerint a prémium rendelkezésre állási modell csomópontfürtjéhez ugyanabban az adatközpontban jön létre. A Azure Availability Zones [bevezetésével](../../availability-zones/az-overview.md)SQL Database adatbázis különböző replikáit üzletileg kritikus különböző rendelkezésre állási zónákba ugyanabban a régióban. Az egyetlen meghibásodási pont kiiktatása érdekében a vezérlőkarikát több zónában is duplikáljuk három átjárókarika (GW)ként. Az adott átjárós körre [](../../traffic-manager/traffic-manager-overview.md) való útválasztást a Azure Traffic Manager (ATM) vezérli. Mivel a zónaredundáns konfiguráció a Prémium vagy üzletileg kritikus szolgáltatási szinteken nem hoz létre további adatbázis-redundanciát, további költségek nélkül engedélyezheti. A zónaredundáns konfiguráció kiválasztásával a Prémium vagy üzletileg kritikus-adatbázisok sokkal nagyobb számú meghibásodással, például katasztrofális adatközpont-kimaradásokkal szemben is ellenállóvá tehetőek az alkalmazáslogika bármilyen változtatása nélkül. A meglévő Prémium vagy üzletileg kritikus adatbázisokat vagy készleteket zónaredundáns konfigurációvá is átalakíthatja.

Mivel a zónaredundáns adatbázisok replikái különböző adatközpontokban vannak, amelyek kis távolságra vannak egymástól, a megnövekedett hálózati késés növelheti a véglegesítési időt, ami hatással lehet egyes OLTP számítási feladatok teljesítményére. A zónaredundania beállítás letiltásával mindig visszatérhet az egyzónás konfigurációhoz. Ez a folyamat egy online művelet, amely hasonló a normál szolgáltatásiszint-frissítéshez. A folyamat végén az adatbázis vagy a készlet migrálva lesz egy zónaredundáns körről egy zónás körre, vagy fordítva.

> [!IMPORTANT]
> A üzletileg kritikus esetén a zónaredundáns konfiguráció csak akkor érhető el, ha a Gen5 számítási hardver van kiválasztva. A zónaredundáns adatbázisokat támogató régiókkal kapcsolatos naprakész információkért lásd: [Szolgáltatások támogatása régiónként.](../../availability-zones/az-region.md)

> [!NOTE]
> Ez a funkció nem érhető el a SQL Managed Instance.

A magas rendelkezésre állású architektúra zónaredundáns verzióját az alábbi ábra szemlélteti:

![magas rendelkezésre állású architektúra zónaredundáns](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>A magas skálázású szolgáltatási szint rendelkezésre állása

A skálázású szolgáltatási szint [](./service-tier-hyperscale.md#distributed-functions-architecture) architektúráját az Elosztott függvények architektúra ismerteti, és jelenleg csak az SQL Database érhető el, nem SQL Managed Instance.

![A skálázás funkcionális architektúrája](./media/high-availability-sla/hyperscale-architecture.png)

A hyperscale rendelkezésre állási modellje négy réteget tartalmaz:

- Állapot nélküli számítási réteg, amely a folyamatokat futtatja, és csak átmeneti és gyorsítótárazott adatokat tartalmaz, például nem lefedő `sqlservr.exe` RBPEX-gyorsítótárat, TempDB-t, modelladatbázist stb. a csatlakoztatott SSD-n, valamint a memóriában lévő tervgyorsítótárat, pufferkészletet és oszlopcentrikus készletet. Ez az állapot nélküli réteg tartalmazza az elsődleges számítási replikát és opcionálisan több másodlagos számítási replikát, amelyek feladatátvételi célként szolgálhatnak.
- Lapkiszolgálók által létrehozott állapot nélküli tárolási réteg. Ez a réteg a számítási replikákon futó folyamatok elosztott `sqlservr.exe` tárolási motorja. Minden lapkiszolgáló csak átmeneti és gyorsítótárazott adatokat tartalmaz, például lefedi az RBPEX-gyorsítótárat a csatlakoztatott SSD-n, és a memóriában gyorsítótárazott adatlapokat. Minden lapkiszolgáló rendelkezik egy aktív-aktív konfigurációval párosított lapkiszolgálóval, amely terheléselosztást, redundanciát és magas rendelkezésre állást biztosít.
- A naplószolgáltatás folyamatát, a tranzakciós naplók kezdőlapját és a tranzakciós napló hosszú távú tárolását futtató számítási csomópont által létrehozott állapotnapló-tárolási réteg. A célzóna és a hosszú távú tárolás az Azure Storage-et használja, amely rendelkezésre állást és [redundanciát](../../storage/common/storage-redundancy.md) biztosít a tranzakciónaplók számára, és biztosítja az adatok tartósságát a lekötött tranzakciók számára.
- Állapot-alapú adattárolási réteg az Azure Storage-ban tárolt és lapkiszolgálók által frissített adatbázisfájlokkal (.mdf/.ndf). Ez a réteg az Azure Storage adat rendelkezésre [állási és redundancia-funkcióit](../../storage/common/storage-redundancy.md) használja. Ez garantálja, hogy az adatfájl minden lapja megmarad, még akkor is, ha a hiperméretű architektúra más rétegeiben lévő folyamatok összeomlannak, vagy ha a számítási csomópontok leállnak.

Az összes hiperméretű réteg számítási csomópontjai az Azure Service Fabric-ban futnak, amely szabályozza az egyes csomópontok állapotát, és szükség szerint feladatátvételt hajt végre az elérhető kifogástalan csomópontokon.

A hyperscale magas rendelkezésre állásával kapcsolatos további információkért lásd: Database High Availability in Hyperscale (Adatbázis magas rendelkezésre [állása a hyperscale-ban).](./service-tier-hyperscale.md#database-high-availability-in-hyperscale)


## <a name="accelerated-database-recovery-adr"></a>gyorsított adatbázis-helyreállítás (ADR)

[gyorsított adatbázis-helyreállítás (ADR)](../accelerated-database-recovery.md) egy új adatbázismotor-funkció, amely nagy mértékben javítja az adatbázis rendelkezésre állását, különösen hosszú ideig futó tranzakciók esetén. Az ADR jelenleg Azure SQL Database, Azure SQL Managed Instance és Azure Synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Alkalmazáshibák rugalmasságának tesztelése

A magas rendelkezésre állás az SQL Database és felügyelt SQL-példány platform alapvető részét képezi, amely átláthatóan működik az adatbázis-alkalmazás tekintetében. Felismertük azonban, hogy érdemes lehet tesztelnie, hogy a tervezett vagy nem tervezett események során kezdeményezett automatikus feladatátvételi műveletek milyen hatással lennének az alkalmazásokra, mielőtt üzembe helyezné őket az éles környezetben. Manuálisan is elindíthatja a feladatátvételt, ha egy speciális API-t hív meg egy adatbázis, egy rugalmas készlet vagy egy felügyelt példány újraindításához. Zónaredundáns kiszolgáló nélküli vagy kiépített általános célú-adatbázis vagy rugalmas készlet esetén az API-hívás az ügyfélkapcsolatokat a régi elsődleges adatbázis rendelkezésre állási zónától eltérő rendelkezésre állási zónában található új elsődleges adatbázisra irányítja át. Így amellett, hogy teszteli, hogyan befolyásolja a feladatátvétel a meglévő adatbázis-munkameneteket, azt is ellenőrizheti, hogy a hálózati késés változásai miatt módosítja-e a végpontok közötti teljesítményt. Mivel az újraindítási művelet tolakodó, és sok esetben nagy terhelést okoz a platform, 15 percenként csak egy feladatátvételi hívás engedélyezett minden adatbázishoz, rugalmas készlethez vagy felügyelt példányhoz.

A feladatátvétel kezdeményezhető a PowerShell, a REST API vagy az Azure CLI használatával:

|Központi telepítési típus|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|Adatbázis|[Invoke-AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Adatbázis feladatátvétele](/rest/api/sql/databases/failover)|[Az az rest](/cli/azure/reference-index#az_rest) használatával meghívhat egy REST API Azure CLI-ről|
|Rugalmas készlet|[Invoke-AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Rugalmas készlet feladatátvétele](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[Az az rest](/cli/azure/reference-index#az_rest) használatával meghívhat egy REST API Azure CLI-ről|
|Felügyelt példány|[Invoke-AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Felügyelt példányok – Feladatátvétel](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az sql mi failover](/cli/azure/sql/mi/#az_sql_mi_failover)|

> [!IMPORTANT]
> A feladatátvételi parancs nem érhető el a hiperméretű adatbázisok olvasható másodlagos replikáihoz.

## <a name="conclusion"></a>Összegzés

Azure SQL Database és Azure SQL Managed Instance beépített magas rendelkezésre állású megoldást kínálnak, amely mélyen integrálva van az Azure platformmal. A hibatűrés Service Fabric a hibák észlelése és helyreállítása, az Azure Blob Storage az adatvédelem érdekében, az Availability Zones függ a hibatűréstől (ahogy azt a dokumentum korábbi, még nem Azure SQL Managed Instance). Emellett a SQL Database és SQL Managed Instance az Always On rendelkezésre állási csoport technológiáját a SQL Server replikációhoz és feladatátvételhez. Ezeknek a technológiáknak a kombinációja lehetővé teszi, hogy az alkalmazások teljes mértékben ki tudják használják a vegyes tárolási modell előnyeit, és támogassák a legigényesebb SZOLGÁLTATÁS-okat.

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Availability Zones](../../availability-zones/az-overview.md)
- További információ a [Service Fabric](../../service-fabric/service-fabric-overview.md)
- További információ a [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Útmutató [manuális feladatátvétel kezdeményezése a](../managed-instance/user-initiated-failover.md) SQL Managed Instance
- További lehetőségek a magas rendelkezésre álláshoz és a vészhelyreállításhoz: [Üzletmenet-folytonosság](business-continuity-high-availability-disaster-recover-hadr-overview.md)
