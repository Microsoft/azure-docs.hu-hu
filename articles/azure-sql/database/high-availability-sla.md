---
title: Magas rendelkezésre állás
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Ismerkedjen meg a Azure SQL Database és az SQL felügyelt példányok szolgáltatás magas rendelkezésre állási képességeivel és funkcióival
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
ms.openlocfilehash: 9e292aeb116bd33ce935c951b39015c54dd1a8bc
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486692"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Magas rendelkezésre állás Azure SQL Database és SQL felügyelt példányhoz
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az SQL felügyelt példány magas rendelkezésre állású architektúrájának célja annak biztosítása, hogy az adatbázis legalább 99,99%-ban elérhető legyen (a különböző szintekre vonatkozó SLA-val kapcsolatos további információkért tekintse meg az [Azure SQL Database és az SQL felügyelt példányra vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/sql-database/)-t), anélkül, hogy aggódnia kellene a karbantartási műveletek és kimaradások következményeivel kapcsolatban. Az Azure automatikusan kezeli a kritikus karbantartási feladatokat, például a javításokat, a biztonsági mentéseket, a Windows-és az Azure SQL-frissítéseket, valamint a nem tervezett eseményeket, például a mögöttes hardvereket, szoftvereket vagy hálózati hibákat.  Ha az alapul szolgáló adatbázis Azure SQL Database a javított vagy feladatátvételt hajt végre, az állásidő nem észlelhető, ha az alkalmazásban [újrapróbálkozási logikát alkalmaz](develop-overview.md#resiliency) . A SQL Database és az SQL felügyelt példány gyorsan helyreállítható még a legfontosabb körülmények között is, így biztosítva, hogy az adatok mindig rendelkezésre álljanak.

A magas rendelkezésre állású megoldás úgy van kialakítva, hogy a hibák miatt soha ne vesszenek el az véglegesített adatok, hogy a karbantartási műveletek ne befolyásolják a munkaterhelést, és hogy az adatbázis nem lesz egyetlen meghibásodási pont a szoftver architektúrájában. Nincsenek olyan karbantartási időszakok vagy állásidők, amelyeknek az adatbázis frissítése vagy karbantartása közben le kell állítania a munkaterhelést.

Két magas rendelkezésre állású építészeti modell létezik:

- A **standard rendelkezésre állási modell** , amely a számítás és a tárolás elkülönítésén alapul.  A távoli tárolási rétegek magas rendelkezésre állása és megbízhatósága alapján működik. Ez az architektúra olyan költségvetési alapú üzleti alkalmazásokat céloz meg, amelyek bizonyos teljesítménybeli romlást tudnak elviselni a karbantartási tevékenységek során.
- **Prémium rendelkezésre állási modell** , amely az adatbázismotor folyamatainak egy fürtjén alapul. Arra támaszkodik, hogy a rendelkezésre álló adatbázismotor-csomópontok kvóruma mindig fennáll. Ez az architektúra olyan kritikus fontosságú alkalmazásokat céloz meg, amelyek magas i/o-teljesítménnyel, magas tranzakciós sebességgel rendelkeznek, és a karbantartási tevékenységek során minimális teljesítményt nyújtanak a számítási feladathoz.

A SQL Database és az SQL felügyelt példánya is a SQL Server adatbázismotor és a Windows operációs rendszer legújabb stabil verzióján fut, és a legtöbb felhasználó nem észleli, hogy a frissítések folyamatosan lesznek elvégezve.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Alapszintű, standard és általános célú szolgáltatási szint helyi redundáns rendelkezésre állása

Az alapszintű, standard és általános célú szolgáltatási szintek a szabványos rendelkezésre állási architektúrát használják a kiszolgáló nélküli és a kiépített számítási feladatokhoz. Az alábbi ábra négy különböző csomópontot mutat be a elkülönített számítási és tárolási rétegekkel együtt.

![A számítás és a tárolás elkülönítése](./media/high-availability-sla/general-purpose-service-tier.png)

A standard rendelkezésre állási modell két réteget tartalmaz:

- Állapot nélküli számítási réteg, amely futtatja a `sqlservr.exe` folyamatot, és csak átmeneti és gyorsítótárazott adatokból áll, például a tempdb, a csatolt SSD modell-adatbázisaival, valamint a gyorsítótár, a puffer-készlet és a oszlopcentrikus-készlet memóriában történő megtervezésével. Ezt az állapot nélküli csomópontot az Azure Service Fabric működteti, amely inicializálja `sqlservr.exe` , szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik csomóponton.
- Az Azure Blob Storage-ban tárolt adatbázis-fájlokkal (. MDF/. ldf) rendelkező állapot-nyilvántartó adatréteg. Az Azure Blob Storage beépített adatelérhetőségi és redundancia-funkcióval rendelkezik. Ez garantálja, hogy az adatfájlban lévő naplófájl vagy oldal minden rekordja megmarad, még akkor is, ha a `sqlservr.exe` folyamat összeomlik.

Ha az adatbázismotor vagy az operációs rendszer frissítése megtörtént, vagy ha hibát észlel, az Azure Service Fabric az állapot nélküli `sqlservr.exe` folyamatot egy másik, elegendő szabad kapacitással rendelkező állapot nélküli számítási csomópontra helyezi át. Az áthelyezés nem érinti az Azure Blob Storage-beli adatátvitelt, és az adatfájlok és naplófájlok az újonnan inicializált folyamathoz vannak csatolva `sqlservr.exe` . Ez a folyamat 99,99%-os rendelkezésre állást garantál, de a nagy számítási feladatok némi teljesítménybeli romlást tapasztalhatnak az átállás során, mivel az új `sqlservr.exe` folyamat a hideg gyorsítótárral kezdődik.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Általános célú a szolgáltatási szintek zónájának redundáns elérhetősége (előzetes verzió)

Az általános célú szolgáltatási szint zóna redundáns konfigurációja [Azure Availability Zones](../../availability-zones/az-overview.md)   az adatbázisok replikálását az Azure-régión belüli több fizikai helyszín között.A zóna redundancia lehetőség kiválasztásával az új és meglévő általános célú önálló adatbázisok és rugalmas készletek rugalmasan kezelhetők a hibák sokkal nagyobb készletével, beleértve a katasztrofális adatközpont leállását az alkalmazás logikájának módosítása nélkül.

Az általános célú réteg zónájának redundáns konfigurációja két réteget tartalmaz:  

- Egy állapot-nyilvántartó adatréteget, amely a ZRS PFS-ben (. MDF/. ldf) tárolt adatbázis-fájlokat (a zóna redundáns [tárolási prémium fájlmegosztást](../../storage/files/storage-how-to-create-file-share.md)) tárolja. A [zóna-redundáns tárolás](../../storage/common/storage-redundancy.md) használatával az adatfájlok és a naplófájlok szinkronban vannak másolva három fizikailag elkülönített Azure rendelkezésre állási zónában.
- Az sqlservr.exe folyamatot futtató állapot nélküli számítási réteg, amely csak átmeneti és gyorsítótárazott adatokból áll, például a TempDB, a csatolt SSD modell-adatbázisainak, valamint a gyorsítótár, a puffer-készlet és a oszlopcentrikus-készletnek a memóriában történő megtervezésére. Ezt az állapot nélküli csomópontot az Azure Service Fabric üzemelteti, amely inicializálja sqlservr.exe, szabályozza a csomópont állapotát, és szükség esetén feladatátvételt hajt végre egy másik csomóponton. A felesleges általános célú adatbázisok esetében a tartalék kapacitású csomópontok más Availability Zonesban azonnal elérhetők a feladatátvételhez.

Az általános célú szolgáltatási rétegek magas rendelkezésre állású architektúrájának zóna redundáns verzióját az alábbi ábra szemlélteti:

![Zóna redundáns konfigurációja általános célú](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> A zóna redundáns konfigurációja csak akkor érhető el, ha a Gen5 számítási hardver van kiválasztva. Ez a funkció nem érhető el az SQL felügyelt példányában. Az általános célú zóna redundáns konfigurációja csak a következő régiókban érhető el: USA keleti régiója, USA 2. keleti régiója, USA 2. nyugati régiója, Észak-Európa, Nyugat-Európa, Délkelet-Ázsia, Kelet-Ausztrália, Kelet-Japán, Egyesült Királyság déli régiója és Közép-Németország.

> [!NOTE]
> A 80 virtuális mag méretű adatbázisok általános célú a zóna redundáns konfigurációjával a teljesítmény romlását tapasztalhatja. Emellett az olyan műveletek, mint például a biztonsági mentés, a visszaállítás, az adatbázis-másolás és a Geo-DR kapcsolatok beállítása, az 1 TB-nál nagyobb méretű önálló adatbázisok esetében lassabb teljesítményt tapasztalhatnak. 
> 
> [!NOTE]
> Az előzetes verzió nem vonatkozik a fenntartott példányra

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Prémium és üzletileg kritikus szolgáltatási szint helyi redundáns rendelkezésre állása

A prémium és üzletileg kritikus szolgáltatási szintek kihasználják a prémium rendelkezésre állási modellt, amely egyetlen csomóponton integrálja a számítási erőforrásokat ( `sqlservr.exe` folyamat) és a tárterületet (helyileg csatlakoztatott SSD-ket). A magas rendelkezésre állást úgy érheti el, hogy a számítási és a tárolási tárterületet további csomópontokra replikálja, amelyek három-négy csomópontos fürtöt hoznak létre.

![Az adatbázismotor csomópontjainak fürtje](./media/high-availability-sla/business-critical-service-tier.png)

Az alapul szolgáló adatbázisfájlok (. MDF/. ldf) a csatlakoztatott SSD-tárolóba kerülnek, így nagyon kis késleltetésű IO-t biztosítanak a számítási feladatok számára. A magas rendelkezésre állás a SQL Server [Always On rendelkezésre állási csoportokhoz](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)hasonló technológia használatával valósítható meg. A fürt egyetlen elsődleges replikát tartalmaz, amely elérhető az írási és olvasási ügyfelek számítási feladataihoz, és legfeljebb három másodlagos replikát (számítás és tárolás), amely az adatok másolatait tartalmazza. Az elsődleges csomópont folyamatosan leküldi a változásokat a másodlagos csomópontokra, és gondoskodik arról, hogy az egyes tranzakciók véglegesítése előtt legalább egy másodlagos replikához szinkronizálja az adatokat. Ez a folyamat garantálja, hogy ha az elsődleges csomópont valamilyen okból összeomlik, mindig van egy teljesen szinkronizált csomópont a feladatátvételhez. A feladatátvételt az Azure Service Fabric kezdeményezi. Miután a másodlagos replika az új elsődleges csomópont lesz, létrejön egy másik másodlagos replika, amely biztosítja, hogy a fürt elegendő csomóponttal (kvórum készlettel) rendelkezik. A feladatátvétel befejezése után a rendszer automatikusan átirányítja az Azure SQL-kapcsolatokat az új elsődleges csomópontra.

További előnyként a prémium rendelkezésre állási modell lehetővé teszi az írásvédett Azure SQL-kapcsolatok átirányítását az egyik másodlagos replikára. Ezt a funkciót az [olvasási Felskálázásnak](read-scale-out.md)nevezzük. A szolgáltatás 100%-os további számítási kapacitást biztosít külön díj nélkül a kikapcsolt írásvédett műveletekhez, például az analitikai számítási feladatokhoz az elsődleges replikából.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Prémium és üzletileg kritikus szolgáltatási szint zóna redundáns rendelkezésre állása 

Alapértelmezés szerint a prémium rendelkezésre állási modell csomópontjainak fürtje ugyanabban az adatközpontban jön létre. A [Azure Availability Zones](../../availability-zones/az-overview.md)bevezetésével a SQL Database a üzletileg kritikus adatbázis különböző replikáit különböző rendelkezésre állási zónákba helyezheti ugyanabban a régióban. Egyetlen meghibásodási pont kizárása érdekében a vezérlő gyűrűt több zónában is duplikálja három átjárós gyűrűként (GW). Egy adott átjáró-gyűrű útválasztását az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM) vezérli. Mivel a zóna redundáns konfigurációja a prémium vagy üzletileg kritikus szolgáltatási szinten nem hoz létre további adatbázis-redundanciát, külön díj nélkül is engedélyezheti. A zóna redundáns konfigurációjának kiválasztásával a prémium vagy üzletileg kritikus-adatbázisok rugalmasan állíthatók be a hibák sokkal nagyobb részére, beleértve a katasztrofális adatközpont leállását, az alkalmazás logikájának módosítása nélkül. A meglévő prémium vagy üzletileg kritikus adatbázisok vagy készletek a zóna redundáns konfigurációjához is átalakíthatók.

Mivel a zóna redundáns adatbázisai rendelkeznek a különböző adatközpontokban lévő replikákkal, és ezek között a távolságuk, a megnövelt hálózati késés növelheti a bevezetési időt, és így hatással lehet bizonyos OLTP-számítási feladatok teljesítményére. Bármikor visszatérhet az egyzónás konfigurációhoz a zóna redundancia beállítás letiltásával. Ez a folyamat egy olyan online művelet, amely a szolgáltatási csomag szokásos verziófrissítéséhez hasonló. A folyamat végén a rendszer áttelepíti az adatbázist vagy a készletet egy zónába tartozó redundáns gyűrűből egyetlen zónába, vagy fordítva.

> [!IMPORTANT]
> A üzletileg kritikusi szintű használata esetén a zóna redundáns konfigurációja csak akkor érhető el, ha a Gen5 számítási hardver van kiválasztva. A zóna redundáns adatbázisokat támogató régiókkal kapcsolatos naprakész információ: [szolgáltatások támogatása régiónként](../../availability-zones/az-region.md).

> [!NOTE]
> Ez a funkció nem érhető el az SQL felügyelt példányában.

A magas rendelkezésre állású architektúra zóna redundáns verzióját a következő ábra szemlélteti:

![magas rendelkezésre állású architektúra zóna redundáns](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Nagy kapacitású szolgáltatási rétegek rendelkezésre állása

Az nagy kapacitású szolgáltatási rétegek architektúrája az [elosztott függvények architektúrájában](./service-tier-hyperscale.md#distributed-functions-architecture) található, és jelenleg csak SQL Database esetében érhető el, nem SQL felügyelt példány esetén.

![Nagy kapacitású funkcionális architektúra](./media/high-availability-sla/hyperscale-architecture.png)

A nagy kapacitású rendelkezésre állási modellje négy réteget tartalmaz:

- Állapot nélküli számítási réteg, amely futtatja a `sqlservr.exe` folyamatokat, és csak átmeneti és gyorsítótárazott adatokkal rendelkezik, például a nem a RBPEX cache, a tempdb, a Model Database stb., a csatlakoztatott SSD esetében, valamint a gyorsítótár, a puffer készlet és a oszlopcentrikus-készlet memóriában történő megtervezése. Ez az állapot nélküli réteg tartalmazza az elsődleges számítási replikát és opcionálisan számos másodlagos számítási replikát, amelyek feladatátvételi célként szolgálhatnak.
- A Page Servers által létrehozott állapot nélküli tárolási réteg. Ez a réteg a `sqlservr.exe` számítási replikán futó folyamatok elosztott tárolási motorja. Minden oldal-kiszolgáló csak átmeneti és gyorsítótárazott adatmennyiséget tartalmaz, például a csatlakoztatott SSD-RBPEX gyorsítótárát, valamint a memóriában gyorsítótárazott adatlapokat. Az egyes lapozófájlok egy aktív-aktív konfigurációban található párosított kiszolgálóoldali kiszolgálóval biztosítják a terheléselosztást, a redundanciát és a magas rendelkezésre állást.
- Egy állapot-nyilvántartó tranzakciós napló tárolási rétege, amely a naplózási szolgáltatás folyamatát, a tranzakciós napló lejárati zónáját és a tranzakciónapló hosszú távú tárolását futtató számítási csomópont által lett létrehozva. A kirakodási zóna és a hosszú távú tárolás az Azure Storage-t használja, amely rendelkezésre állást és [redundanciát](../../storage/common/storage-redundancy.md) biztosít a tranzakciónapló számára, és biztosítja az adattartósságot a Véglegesített tranzakciók esetében
- Állapot-nyilvántartó adattárolási réteg, amely az Azure Storage-ban tárolt adatbázisfájlok (. MDF/. ndf) használatával frissül. Ez a réteg az Azure Storage adatelérhetőségi és [redundancia](../../storage/common/storage-redundancy.md) -funkcióit használja. Ez garantálja, hogy az adatfájlok minden lapja megmaradjon, még akkor is, ha a nagy kapacitású-architektúra összeomlik, vagy ha a számítási csomópontok sikertelenek lesznek.

Az összes nagy kapacitású-rétegben futtatott számítási csomópontok az Azure Service Fabric futnak, amely az egyes csomópontok állapotát vezérli, és szükség szerint végrehajtja a feladatátvételt az elérhető kifogástalan állapotú csomópontokon.

További információ a nagy kapacitású magas rendelkezésre állásáról: [adatbázis magas rendelkezésre állása a nagy kapacitású-ben](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Gyorsított adatbázis-helyreállítás (ADR)

A [gyorsított adatbázis-helyreállítás (ADR)](../accelerated-database-recovery.md) egy új adatbázismotor-szolgáltatás, amely nagy mértékben javítja az adatbázisok rendelkezésre állását, különösen a hosszú ideig futó tranzakciók jelenlétében. Az ADR jelenleg a Azure SQL Database, az Azure SQL felügyelt példánya és az Azure szinapszis Analytics számára érhető el.

## <a name="testing-application-fault-resiliency"></a>Az alkalmazás hibatűrési rugalmasságának tesztelése

A magas rendelkezésre állás az SQL Database és felügyelt SQL-példány platform alapvető részét képezi, amely átláthatóan működik az adatbázis-alkalmazás tekintetében. Felismertük azonban, hogy érdemes lehet tesztelnie, hogy a tervezett vagy nem tervezett események során kezdeményezett automatikus feladatátvételi műveletek milyen hatással lennének az alkalmazásokra, mielőtt üzembe helyezné őket az éles környezetben. A feladatátvételt manuálisan is aktiválhatja, ha egy speciális API meghívásával újraindít egy adatbázist, egy rugalmas készletet vagy egy felügyelt példányt. Egy zóna redundáns adatbázis vagy rugalmas készlet esetén az API-hívás eredményeképpen az ügyfélkapcsolatok átirányítása egy olyan rendelkezésre állási zónában lévő új elsődlegesre, amely eltér a régi elsődleges hely rendelkezésre állási zónájától. Így azt is megvizsgálhatja, hogy a feladatátvétel hogyan befolyásolja a meglévő adatbázis-munkameneteket, azt is ellenőrizheti, hogy a hálózati késés változása miatt a végpontok közötti teljesítményt módosítja-e. Mivel az újraindítási művelet zavaró, és nagy számú közülük a platform kihangsúlyozása, csak egy feladatátvételi hívás engedélyezett 15 percenként minden adatbázishoz, rugalmas készlethez vagy felügyelt példányhoz.

A feladatátvétel a PowerShell, a REST API vagy az Azure CLI használatával indítható el:

|Központi telepítés típusa|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|Adatbázis|[Meghívás – AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Adatbázis-feladatátvétel](/rest/api/sql/databases/failover)|[az az Rest](/cli/azure/reference-index#az-rest) felhasználható az Azure CLI REST API hívásának meghívására|
|Rugalmas készlet|[Meghívás – AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Rugalmas készlet feladatátvétele](/rest/api/sql/elasticpools(failover)/failover/)|[az az Rest](/cli/azure/reference-index#az-rest) felhasználható az Azure CLI REST API hívásának meghívására|
|Felügyelt példány|[Meghívás – AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Felügyelt példányok – feladatátvétel](/rest/api/sql/managed%20instances%20-%20failover/failover)|[az SQL mi feladatátvétel](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> A feladatátvételi parancs nem érhető el a nagy kapacitású-adatbázisok olvasható másodlagos replikáinak esetében.

## <a name="conclusion"></a>Összegzés

A Azure SQL Database és az Azure SQL felügyelt példánya beépített, magas rendelkezésre állású megoldást kínál, amely szorosan integrálva van az Azure platformmal. Service Fabrictól függ a hibák észlelése és helyreállítása, az Azure Blob Storage az adatvédelem érdekében, valamint a Availability Zones a nagyobb hibatűrés érdekében (ahogy azt korábban említettük, nem alkalmazható az Azure SQL felügyelt példánya esetében). Emellett a SQL Database és az SQL felügyelt példánya is kihasználja az Always On rendelkezésre állási csoport technológiáját a SQL Server példányból a replikáláshoz és a feladatátvételhez. Ezeknek a technológiáknak a kombinációja lehetővé teszi, hogy az alkalmazások teljes mértékben felismerje a vegyes tárolási modell előnyeit, és támogassa a legigényesebb SLA-kat.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Azure Availability Zones](../../availability-zones/az-overview.md)
- Tudnivalók a [Service Fabric](../../service-fabric/service-fabric-overview.md)
- További tudnivalók az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Megtudhatja [, hogyan indíthat manuális feladatátvételt az SQL felügyelt példányain](../managed-instance/user-initiated-failover.md)
- A magas rendelkezésre állással és a vész-helyreállítással kapcsolatos további lehetőségekért lásd: [Üzletmenet-folytonosság](business-continuity-high-availability-disaster-recover-hadr-overview.md)
