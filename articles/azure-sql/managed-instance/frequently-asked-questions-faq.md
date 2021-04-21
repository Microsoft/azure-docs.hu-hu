---
title: Gyakori kérdések (GYIK)
titleSuffix: Azure SQL Managed Instance
description: Felügyelt Azure SQL-példányra vonatkozó gyakori kérdések (GYIK)
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 09/21/2020
ms.openlocfilehash: 16f937286b967aaea8ec6a16e97835b2de5a0331
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765496"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Felügyelt Azure SQL-példányra vonatkozó gyakori kérdések (GYIK)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk a fájlokkal kapcsolatos [leggyakoribb Azure SQL Managed Instance.](sql-managed-instance-paas-overview.md)

## <a name="supported-features"></a>Támogatott funkciók

**Hol található a támogatott szolgáltatások listája a SQL Managed Instance?**

A szolgáltatás támogatott szolgáltatásainak listáját a SQL Managed Instance a [Azure SQL Managed Instance tartalmazza.](../database/features-comparison.md)

A szintaxis és a viselkedés közötti különbségeket Azure SQL Managed Instance és SQL Server A [T-SQL](transact-sql-tsql-differences-sql-server.md)és a SQL Server.


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Műszaki specifikáció, erőforráskorlátok és egyéb korlátozások
 
**Hol találom a műszaki jellemzőket és az erőforráskorlátokat a SQL Managed Instance?**

A hardvergeneráció rendelkezésre álló jellemzőiért lásd: [Technikai különbségek a hardvergenerációkban.](resource-limits.md#hardware-generation-characteristics)
Az elérhető szolgáltatásszintekkel és azok jellemzőivel kapcsolatos információkért lásd: A szolgáltatási szintek [közötti műszaki különbségek.](resource-limits.md#service-tier-characteristics)

**Milyen szolgáltatási szintre jogosult vagyok?**

Bármely ügyfél bármely szolgáltatási szintre jogosult. Ha azonban a Azure Hybrid Benefit használatával kedvezményes díjszabásra szeretné cserélni a meglévő licenceket a Azure SQL Managed Instance [használatával,](https://azure.microsoft.com/pricing/hybrid-benefit/)ne feledje, hogy az SQL Server Enterprise Edition Frissítési Garancia-val [](../database/service-tier-business-critical.md) rendelkező ügyfelei csak az általános célú- vagy üzletileg kritikus-teljesítményszintre, az SQL Server Standard Edition Frissítési Garancia-ügyfelek pedig csak az általános célú teljesítményszintre jogosultak. [](../database/service-tier-general-purpose.md) További részletekért lásd: [A AHB konkrét jogosultsága.](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server)

**Milyen előfizetési típusok támogatottak a SQL Managed Instance?**

A támogatott előfizetés-típusok listájáért lásd: [Támogatott előfizetési típusok.](resource-limits.md#supported-subscription-types) 

**Mely Azure-régiók támogatottak?**

A felügyelt példányok a legtöbb Azure-régióban létrehozhatóak; Lásd: [Támogatott régiók a SQL Managed Instance.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Ha olyan régióban van szüksége felügyelt példányra, amely jelenleg nem támogatott, küldjön támogatási kérést [a Azure Portal.](../database/quota-increase-request.md)

**Vonatkoznak kvótakorlátozások a SQL Managed Instance esetében?**

A felügyelt példányra két alapértelmezett korlát vonatkozik: a használható alhálózatok számára és a kiépíthet virtuális magok számára vonatkozó korlát. A korlátok eltérőek lehetnek az előfizetések típusaiban és régióiban. A regionális erőforrás-korlátozások előfizetéstípus szerint való listáját a regionális erőforrás-korlátozásokat [felsoroló táblázatban talál.](resource-limits.md#regional-resource-limitations) Ezek olyan soft korlátok, amelyek igény szerint növelhetők. Ha több felügyelt példányt kell kiépítene az aktuális régióban, küldjön támogatási kérést a kvóta növeléséhez a Azure Portal. További információ: [Kvótaemelés kérése a Azure SQL Database.](../database/quota-increase-request.md)

**Megnövelheti az adatbázisok számának korlátját (100) a felügyelt példányomon igény szerint?**

Nem, és jelenleg nincsenek tervek az adatbázisok számának növelésére a SQL Managed Instance. 

**Hol lehet átemelni, ha több mint 8TB adatom van?**
Fontolja meg a számítási feladatnak megfelelő egyéb Azure-ízekbe való mibrálást: [](../database/service-tier-hyperscale.md) a Azure SQL Database skálázást vagy SQL Server Azure [Virtual Machines.](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)

**Hol lehet átemelni, ha olyan konkrét hardverkövetelményekkel rendelkezik, mint a nagyobb RAM–virtuális mag arány vagy több processzor?**
Fontolja meg az [Azure-beli](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) SQL Server vagy Virtual Machines vagy [](../database/sql-database-paas-overview.md) Azure SQL Database memória/processzor optimalizálását.

## <a name="known-issues-and-defects"></a>Ismert problémák és hibák

**Hol találom az ismert problémákat és hibákat?**

Termékhibák és ismert problémák esetén lásd: [Ismert problémák.](../database/doc-changes-updates-release-notes.md#known-issues)

## <a name="new-features"></a>Új funkciók

**Hol találom a legújabb funkciókat és a nyilvános előzetes verzióban elérhető funkciókat?**

Az új és előzetes verziójú funkciókért lásd a kibocsátási [megjegyzéseket.](../database/doc-changes-updates-release-notes.md?tabs=managed-instance)

## <a name="create-update-delete-or-move-sql-managed-instance"></a>Létrehozási, frissítési, törlési vagy áthelyezési SQL Managed Instance

**Hogyan lehet üzembe SQL Managed Instance?**

A példányokat kiépítheti [a](instance-create-quickstart.md)Azure Portal, [a PowerShell,](scripts/create-configure-managed-instance-powershell.md) [az Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) és az [ARM-sablonok használatával.](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)

**Kiépíthetek felügyelt példányokat egy meglévő előfizetésben?**

Igen, kiépíthet egy felügyelt példányt egy meglévő előfizetésben, ha az előfizetés a támogatott [előfizetési típusokhoz tartozik.](resource-limits.md#supported-subscription-types)

**Miért nem sikerült kiépítenem egy felügyelt példányt az alhálózaton, amelynek a neve számjegyekkel kezdődik?**

Ez a mögöttes összetevő aktuális korlátozása, amely ellenőrzi az alhálózat nevét a regex ^[a-zA-Z_][^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ]*(?<![ \. \s])$. A regexen áteső és érvényes alhálózati nevek jelenleg támogatottak.

**Hogyan skálázhatja a felügyelt példányt?**

A felügyelt példány [skálázhatja](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation)a Azure Portal, [PowerShell,](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell)Azure [CLI](/cli/azure/sql/mi#az_sql_mi_update) vagy [ARM-sablonok használatával.](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)

**Áthelyeztetheti a felügyelt példányt az egyik régióból a másikba?**

Igen, írhat. Útmutatásért lásd: [Erőforrások áthelyezése régiók között.](../database/move-resources-across-regions.md)

**Hogyan törölhetem a felügyelt példányt?**

A felügyelt példányok a(z) Azure Portal, [a PowerShell,](/powershell/module/az.sql/remove-azsqlinstance) [az Azure CLI](/cli/azure/sql/mi#az_sql_mi_delete) vagy a Resource Manager REST [API-k használatával törölhetők.](/rest/api/sql/managedinstances/delete)

**Mennyi ideig tart egy példány létrehozása vagy frissítése, vagy egy adatbázis visszaállítása?**

Az új felügyelt példány létrehozásához vagy a szolgáltatásszintek (virtuális magok, tárterület) változásának várható ideje több tényezőtől függ. Lásd: [Felügyeleti műveletek.](sql-managed-instance-paas-overview.md#management-operations)
 
## <a name="naming-conventions"></a>Elnevezési konvenciók

**Lehet a felügyelt példány neve megegyezik a helyszíni SQL Server nevével?**

A felügyelt példány nevének módosítása nem támogatott.

**Módosítanom kell a DNS-zóna előtagját?**

Igen, a felügyelt példány alapértelmezett DNS-zóna *.database.windows.net* módosítható. 

Másik DNS-zóna használata az alapértelmezett helyett, például *.contoso.com:* 
- Alias definiálás a CliConfig használatával. Az eszköz csupán egy beállításjegyzék-burkoló, így csoportházirend vagy szkript használatával is használhatja.
- Használja *a CNAME paramétert* *a TrustServerCertificate=true beállítással.*

## <a name="migration-options"></a>Migrálási lehetőségek

**Hogyan lehet áttűnni egy Azure SQL Database rugalmas készletből egy SQL Managed Instance?**

A felügyelt példány számítási és tárolási méretenként ugyanazt a teljesítményszintet kínálja, mint az egyéb üzembe helyezési Azure SQL Database. Ha egyetlen példányon szeretné konszolidálni az adatokat, vagy csak egy kizárólag felügyelt példányban támogatott funkcióra van szüksége, az adatokat exportálási/importálási (BACPAC) funkcióval miheti át. Az áttelepítés további módjai a SQL Database a SQL Managed Instance: 
- Külső [adatforrás használata](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210)
- Az [SQLPackage használata](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182)
- A [BCP használata](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7)

**Hogyan mirateálhatja a példány-adatbázisomat egyetlen Azure SQL Database?**

Az egyik lehetőség az [adatbázis exportálása a BACPAC-be,](../database/database-export.md) majd a [BACPAC-fájl importálása.](../database/database-import.md) Ez az ajánlott módszer, ha az adatbázis kisebb, mint 100 GB.

[Tranzakciós replikáció akkor](replication-two-instances-and-sql-server-configure-tutorial.md) használható, ha az  adatbázis összes táblája tartalmaz elsődleges kulcsokat, és nincsenek memóriában lévő OLTP-objektumok az adatbázisban.

A COPY_ONLY példányról készült natív biztonsági másolatok nem állíthatók vissza SQL Server-be, mert a felügyelt példány adatbázis-verziója magasabb a SQL Server. További részletekért lásd: Csak [másolásos biztonsági mentés.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15)

**Hogyan mi tudom átemelni a SQL Server-példányt a SQL Managed Instance?**

A saját SQL Server áttelepítése: SQL Server példány áttelepítése a [Azure SQL Managed Instance.](migrate-to-instance-from-sql-server.md)

**Hogyan lehet más platformokról átállni a SQL Managed Instance?**

A más platformokról való migrálással kapcsolatos információkért lásd: [Azure Database Migration Guide (Az Azure Database migrálási útmutatója).](https://datamigration.microsoft.com/)

## <a name="switch-hardware-generation"></a>Hardvergeneráció váltása 

**Online válthatok a felügyelt példány hardvergenerációjaim között a Gen 4 és a Gen 5 között?**

A Gen4-ről Gen5-re történő automatikus online váltás akkor lehetséges, ha a Gen5-hardver elérhető abban a régióban, ahol a felügyelt példány üzembe van stb. Ebben az esetben tekintse meg a virtuálismag-alapú modell áttekintő [oldalát,](../database/service-tiers-vcore.md) amely a hardvergenerációk közötti váltást ismerteti.

Ez egy hosszú ideig futó művelet, mivel egy új felügyelt példány lesz kiépítve a háttérben, és az adatbázisok automatikusan át lesznek adva a régi és az új példány között a folyamat végén egy gyors feladatátvételsel.

Megjegyzés: A Gen4 hardvert jelenleg nem lehet üzembe helyezés alatt tartani, és már nem érhető el az új üzemelő példányok számára. Minden új adatbázist Gen5 hardveren kell üzembe helyezni. A Gen5-ről a Gen4-re való váltás szintén nem érhető el.

## <a name="performance"></a>Teljesítmény 

**Hogyan hasonlítható össze a felügyelt példány teljesítménye a SQL Server teljesítményével?**

A felügyelt példányok és a felügyelt példányok SQL Server összehasonlításához jó kiindulási pont a felügyelt példányok és Azure SQL-példányok teljesítmény-összehasonlításának SQL Server [ajánlott](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) eljárások.

**Mi okozza a felügyelt példány és a felügyelt példány SQL Server?**

Lásd: [A felügyelt SQL-példány és](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)a felügyelt SQL-példány közötti teljesítménybeli különbségek fő okai SQL Server. A naplófájlok méretének a felügyelt példányok teljesítményére gyakorolt hatásának általános célú lásd: A naplófájl méretének hatása a [általános célú.](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e)

**Hogyan a felügyelt példányom teljesítményét?**

A felügyelt példány teljesítményét a következővel optimalizálhatja:
- [Automatikus hangolás,](../database/automatic-tuning-overview.md) amely csúcsteljesítményt és stabil számítási feladatokat biztosít a folyamatos, AI- és gépi tanuláson alapuló teljesítmény-finomhangolás révén.
-    [Memória-OLTP,](../in-memory-oltp-overview.md) amely javítja a tranzakciófeldolgozási számítási feladatok átviteli sebességét és késését, és gyorsabb üzleti elemzéseket biztosít. 

A teljesítmény további finomhangolása érdekében  fontolja meg néhány ajánlott alkalmazás- és [adatbázis-finomhangolási eljárás alkalmazását.](../database/performance-guidance.md#tune-your-database)
Ha a számítási feladat sok kis tranzakciót tartalmaz, fontolja meg a kapcsolattípus [proxyról](connection-types-overview.md#changing-connection-type) átirányítási módra váltását a kisebb késés és a nagyobb átviteli sebesség érdekében.

## <a name="monitoring-metrics-and-alerts"></a>Monitorozás, metrikák és riasztások

**Milyen lehetőségek állnak rendelkezésre a felügyelt példány monitorozási és riasztási beállításaihoz?**

Az összes lehetséges monitorozási és riasztási lehetőségről a SQL Managed Instance és teljesítményéről a monitorozási lehetőségekről Azure SQL Managed Instance [blogbejegyzésben olvashat.](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416) Az SQL MI valós idejű teljesítményfigyeléséről a felügyelt adatbázispéldány valós idejű [teljesítményfigyelés Azure SQL nyújt.](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance)

**Használhatom az SQL Profilert a teljesítménykövetéshez?**

Igen, az SQL Profiler támogatott vagy SQL Managed Instance. További részletekért lásd: [SQL Profiler.](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15)

**A Database Advisor példányok Lekérdezési terheléselemző támogatottak a felügyelt példányok adatbázisai?**

Nem, nem támogatottak. A [DMV-ket és](../database/monitoring-with-dmvs.md) a lekérdezéstárat az [SQL Profilerrel](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) és az [XEvents-ekkel](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) együtt használhatja az adatbázisok monitorozásához. [](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15)

**Létrehozhatok metrikariasztásokat a SQL Managed Instance?**

Igen. Útmutatásért lásd: [Riasztások létrehozása a SQL Managed Instance.](alerts-create.md)

**Létrehozhatok metrikariasztásokat egy felügyelt példányban egy adatbázishoz?**

Nem, a riasztási metrikák csak felügyelt példányhoz érhetők el. A felügyelt példány egyes adatbázisaihoz nem érhetők el riasztási metrikák.

## <a name="storage-size"></a>Tárterület mérete

**Mi a maximális tárterületméret a SQL Managed Instance?**

A tárterület SQL Managed Instance a kiválasztott szolgáltatási szinttől (általános célú vagy üzletileg kritikus). A szolgáltatási szintek tárolási korlátozásaiért lásd: [Szolgáltatási szint jellemzői.](../database/service-tiers-general-purpose-business-critical.md)

**Mi a felügyelt példányok minimálisan elérhető tárterületmérete?**

Az egy példányban elérhető minimális tárterület 32 GB. A tárterület 32 GB-os növekményekben, a maximális tárterületméretig hozzáadható. Az első 32 GB ingyenes.

**Megnövelhető a példányhoz rendelt tárterület a számítási erőforrásoktól függetlenül?**

Igen, bizonyos mértékig a számítási erőforrásoktól függetlenül vásárolhat bővítménytárat. Lásd: *Példány számára fenntartott tár maximális tárterülete* a [táblázatban.](resource-limits.md#hardware-generation-characteristics)

**Hogyan optimalizálható a tárolási teljesítmény általános célú szolgáltatási szinten?**

A tárolási teljesítmény optimalizálásához tekintse meg a Storage [ajánlott eljárásait](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)a általános célú.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

**A biztonsági másolat tárterülete le van vonva a felügyelt példány tárterületéből?**

Nem, a biztonsági mentési tárterület nem lesz levonva a felügyelt példány tárterületéből. A biztonsági másolat tárterülete független a példány tárterületétől, és nincs korlátozva a mérete. A biztonsági másolatok tárolásának időtartama a példányadatbázisok biztonsági másolatának legfeljebb 35 napig tartó megőrzésére van korlátozva. Részletekért lásd: [Automatikus biztonsági mentések.](../database/automated-backups-overview.md)

**Hogyan tudom megnézni, hogy mikor készül automatikus biztonsági mentés a felügyelt példányomon?**

Annak nyomon követéséhez, hogy mikor történt automatikus biztonsági mentés a felügyelt példányon, tekintse meg a következőt: How to track the automated [backup for an Azure SQL Managed Instance.](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)

**Támogatott az igény szerinti biztonsági mentés?**

Igen, létrehozhat csak másolásos teljes biztonsági mentést a Azure Blob Storage, de az csak felügyelt példányon lesz visszaállítható. Részletekért lásd: Csak [másolásos biztonsági mentés.](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15) A csak másolásos biztonsági mentés azonban nem lehetséges, ha az adatbázist szolgáltatás által felügyelt TDE titkosítja, mivel a titkosításhoz használt tanúsítvány nem érhető el. Ebben az esetben az időponthoz időben helyreállítható szolgáltatással helyezze át az adatbázist egy másik SQL Managed Instance, vagy váltson az ügyfél által felügyelt kulcsra.

**Támogatott a natív visszaállítás (.bak fájlokból) felügyelt példányra?**

Igen, a 2005-ös vagy újabb SQL Server támogatott és elérhető.  Natív visszaállításhoz töltse fel a .bak fájlt az Azure Blob Storage-ba, és hajtson végre T-SQL-parancsokat. További részletekért lásd: Natív [visszaállítás URL-címről.](./migrate-to-instance-from-sql-server.md#native-restore-from-url)

## <a name="business-continuity"></a>Az üzletmenet folytonossága

**A rendszeradatbázisok replikálva vannak a feladatátvételi csoportban a másodlagos példányra?**

A rendszeradatbázisok nem replikálódnak a feladatátvételi csoportban a másodlagos példányra. Ezért a rendszeradatbázisok objektumaitól függő forgatókönyvek lehetetlenek lesznek a másodlagos példányon, kivéve, ha az objektumok manuálisan vannak létrehozva a másodlagos példányon. Áthidaló megoldásként [lásd: Enable scenarios dependent on the object from the system databases (A](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases)rendszeradatbázisok objektumtól függő forgatókönyvek engedélyezése).
 
## <a name="networking-requirements"></a>Hálózati követelmények 

**Mik a felügyelt példány alhálózatának aktuális bejövő/kimenő NSG-megkötései?**

A szükséges NSG- és UDR-szabályokat itt dokumentálja, és a szolgáltatás automatikusan beállítja. [](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)
Ne feledje, hogy ezek a szabályok csak azok, amelyek a szolgáltatás karbantartásához szükségesek. A felügyelt példányhoz való csatlakozáshoz és a különböző funkciók használatához további, funkcióspecifikus szabályokat kell beállítania, amelyek karbantartásra vannak szükség.

**Hogyan állíthatok be bejövő NSG-szabályokat a felügyeleti portokon?**

SQL Managed Instance a felügyeleti portokra vonatkozó szabályok beállításáért felelős. Ez a szolgáltatás által segített [alhálózat-konfiguráció nevű funkcióval érhető el.](connectivity-architecture-overview.md#service-aided-subnet-configuration)
Ez biztosítja a felügyeleti forgalom folyamatos áramlását az SLA teljesítéséhez.

**Le tudom szerezni a bejövő felügyeleti forgalomhoz használt forrás IP-címtartományokat?**

Igen. A hálózati biztonsági csoporton áthaladó forgalmat a forgalomnaplók konfigurálásával [Network Watcher elemezheti.](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group)

**Beállíthatjom az NSG-t az adatvégponthoz való hozzáférés szabályozására (1433-as port)?**

Igen. A felügyelt példány kiépítése után beállíthatja az 1433-as port bejövő hozzáférését vezérlő NSG-t. Javasolt a lehető legnagyobb mértékben leszűkíteni az IP-címtartományt.

**Beállíthatjom az NVA-t vagy a helyszíni tűzfalat a kimenő felügyeleti forgalom FQDN-ek alapján való szűréséhez?**

Nem. Ez több okból sem támogatott:
-    A bejövő felügyeleti kérelmekre adott választ jelképező forgalom útválasztása aszimmetrikus lenne, és nem működne.
-    A tárolóba kimenő forgalomra hatással lenne az átviteli sebességre vonatkozó korlátozások és a késés, így nem tudjuk biztosítani a szolgáltatás elvárt minőségét és rendelkezésre állását.
-    A tapasztalatok alapján ezek a konfigurációk hibaeseteket jeleznek, és nem támogatottak.

**Beállíthatjom az NVA-t vagy a tűzfalat a kimenő, nem felügyeleti forgalomhoz?**

Igen. Ennek legegyszerűbb módja, ha hozzáad egy 0/0 szabályt egy felügyelt példány alhálózatával társított UDR-hez, hogy az NVA-n keresztül irányítsa a forgalmat.
 
**Hány IP-címre van szükségem egy felügyelt példányhoz?**

Az alhálózatnak elegendő számú elérhető [IP-címmel kell lennie.](connectivity-architecture-overview.md#network-requirements) A virtuális hálózat alhálózati méretének meghatározásához SQL Managed Instance felügyelt példányhoz szükséges alhálózatméret és [-tartomány meghatározása.](./vnet-subnet-determine-size.md) 

**Mi történik, ha nincs elég IP-cím a példányfrissítési művelet végrehajtásához?**

Ha nincs elég [IP-cím](connectivity-architecture-overview.md#network-requirements) abban az alhálózatban, ahol a felügyelt példány ki van építve, létre kell hoznia egy új alhálózatot és egy új felügyelt példányt a hálózaton belül. Azt is javasoljuk, hogy az új alhálózatot több lefoglalt IP-címmel hozza létre, hogy a jövőbeli frissítési műveletek során ne merüljenek fel hasonló helyzetek. Az új példány kiépítése után manuálisan biztonsági mentést végezhet és visszaállíthatja az adatokat a régi és az új példányok között, vagy végrehajthatja a példányközi időponthoz [igazítható visszaállítást.](point-in-time-restore.md?tabs=azure-powershell)

**Szükség van üres alhálózatra egy felügyelt példány létrehozásához?**

Nem. Használhat üres alhálózatot vagy egy olyan alhálózatot, amely már tartalmaz felügyelt példányt(ok). 

**Módosítható az alhálózat címtartománya?**

Nem, ha felügyelt példányok vannak benne. Ez az Azure hálózati infrastruktúrájának korlátozása. Csak az üres alhálózathoz adhat hozzá további [címtéreket.](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings) 

**Áthelyezhetim a felügyelt példányomat egy másik alhálózatra?**

Nem. Ez a felügyelt példányok jelenlegi tervezési korlátozása. Azonban kiépíthet egy új példányt egy másik alhálózaton, manuálisan biztonsági mentést végezhet a régi és az új példány közötti adatokról, vagy végrehajthatja a példányközi időponthoz [igazítható visszaállítást.](point-in-time-restore.md?tabs=azure-powershell)

**Szükségem van üres virtuális hálózatra egy felügyelt példány létrehozásához?**

Ez nem kötelező. Létrehozhat egy [virtuális hálózatot a](./virtual-network-subnet-create-arm-template.md) Azure SQL Managed Instance vagy konfigurálhat egy meglévő virtuális hálózatot a [Azure SQL Managed Instance.](./vnet-existing-add-subnet.md)

**Helyezzek el egy felügyelt példányt más szolgáltatásokkal egy alhálózaton?**

Nem. A felügyelt példányok más erőforrástípusokat már tartalmazó alhálózatba való elhelyezése jelenleg nem támogatott.

## <a name="connectivity"></a>Kapcsolatok 

**Csatlakozhatok a felügyelt példányomhoz IP-cím használatával?**

Nem, ez nem támogatott. A felügyelt példány gazdagépneve leképeződik a terheléselosztásra a felügyelt példány virtuális fürtje előtt. Mivel egy virtuális fürt több felügyelt példányt is képes kezelni, a kapcsolat nem irányítható a megfelelő felügyelt példányhoz a név megadása nélkül.
További információ a virtuális SQL Managed Instance architektúráról: [Virtuális fürt kapcsolati architektúrája.](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**A felügyelt példányomnak lehet statikus IP-címe?**

Ez jelenleg nem támogatott.

Ritka, de szükséges helyzetekben előfordulhat, hogy egy felügyelt példány online migrálását kell egy új virtuális fürtre. Szükség esetén ez a migrálás a szolgáltatás biztonságának és megbízhatóságának javítását célzó technológiai verem változásai miatt történik. Az új virtuális fürtre való áttelepítés a felügyelt példány gazdagépnevére leképezett IP-cím megváltoztatását jelenti. A felügyelt példány szolgáltatás nem igényel statikus IP-címtámogatást, és fenntartja a jogot, hogy a rendszeres karbantartási ciklusok részeként értesítés nélkül módosítsa.

Ezért erősen nem javasoljuk, hogy az IP-cím megváltoztathatatlanságára támaszkodjon, mivel ez szükségtelen állásidőt okozhat.

**A felügyelt példány rendelkezik nyilvános végponttal?**

Igen. A felügyelt példány rendelkezik egy nyilvános végponttal, amely alapértelmezés szerint csak a Szolgáltatáskezeléshez van használva, de az ügyfél adatelérést is engedélyezhet. További részletekért lásd: [SQL Managed Instance használata nyilvános végpontokkal.](./public-endpoint-overview.md) A nyilvános végpont konfiguráláshoz használja a Nyilvános végpont [konfigurálása](public-endpoint-configure.md)a SQL Managed Instance.

**Hogyan szabályozhatja a felügyelt példány a nyilvános végponthoz való hozzáférést?**

A felügyelt példány a nyilvános végponthoz való hozzáférést a hálózat és az alkalmazás szintjén is szabályozza.

A felügyeleti és üzembe helyezési szolgáltatások egy külső terheléselosztásra leképező felügyeleti végpont [használatával](./connectivity-architecture-overview.md#management-endpoint) csatlakoznak egy felügyelt példányhoz. A rendszer csak akkor irányít forgalmat a csomópontokra, ha olyan előre meghatározott portkészleten érkezik, amit csak a felügyelt példány felügyeleti összetevői használhatnak. A csomópontok beépített tűzfala úgy van beállítva, hogy csak a Microsoft IP-címtartományaiból származó forgalmat engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti összes kommunikációt. További részletekért lásd: [Kapcsolati architektúra a SQL Managed Instance.](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture)

**Használhatom a nyilvános végpontot a felügyelt példányok adatbázisai adatainak eléréséhez?**

Igen. Az ügyfélnek engedélyeznie kell a nyilvános végpontok adatelérését [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)PowerShellből/ARM-ről, és úgy kell konfigurálnia az NSG-t, hogy zárolja az adatporthoz való hozzáférést  /  [](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) (3342-es portszám). További információ: [Nyilvános](public-endpoint-configure.md) végpont konfigurálása a Azure SQL Managed Instance és biztonságos Azure SQL Managed Instance [nyilvános végponttal.](public-endpoint-overview.md) 

**Megadhatok egyéni portot az SQL-adatvégpont(ak)hoz?**

Nem, ez a lehetőség nem érhető el.  A privát adatvégpont esetén a felügyelt példány az alapértelmezett 1433-as portszámot használja, nyilvános adatvégpontként pedig az alapértelmezett 3342-es portszámot.

**Mi a különböző régiókban található felügyelt példányok csatlakoztatásának ajánlott módja?**

Ennek előnyben részesített módja az Express Route-kapcsolathálózatok társviszonyának létesítés. A globális virtuális hálózatok közötti társviszony-létesítés az alábbi megjegyzésben ismertetett korlátozással támogatott.  

> [!IMPORTANT]
> [2020. 09. 22-én bejelentettük a globális virtuális hálózatok közötti társviszony-létesítést az újonnan létrehozott virtuális fürtökhöz](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Ez azt jelenti, hogy a bejelentés dátumától a globális virtuális hálózatok közötti társviszony-létesítés támogatott az üres alhálózatokon létrehozott felügyelt SQL-példányok, valamint az ezeken az alhálózatokon létrehozott további felügyelt példányok esetén. A felügyelt SQL-példányok egyéb társviszony-létesítés támogatása az azonos régióban lévő hálózatokra korlátozódik a globális virtuális hálózatok közötti társviszony-létesítés [korlátai miatt.](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) További részletekért tekintse meg az [Azure-beli](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) virtuális hálózatokra vonatkozó gyakori kérdések című cikk megfelelő szakaszát is. 

Ha az Express Route-kapcsolatcsoport és a globális virtuális hálózatok közötti társviszony-létesítés nem lehetséges, az egyetlen másik lehetőség a[hely–hely](../../vpn-gateway/tutorial-site-to-site-portal.md)VPN-kapcsolat létrehozása ( Azure Portal , [PowerShell,](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md) [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)).

## <a name="mitigate-data-exfiltration-risks"></a>Adatkiszivárgási kockázatok mérséklése  

**Hogyan mérsékelhetők az adatkiszivárgási kockázatok?**

Az adatszivárgási kockázatok csökkentése érdekében javasoljuk az ügyfeleknek, hogy alkalmazzanak biztonsági beállításokat és vezérlőket:

- Kapcsolja be [transzparens adattitkosítás (TDE)](../database/transparent-data-encryption-tde-overview.md) az összes adatbázison.
- Kapcsolja ki a Közös nyelvi futásidőt (CLR). Ez a helyszíni környezetben is ajánlott.
- Csak Azure Active Directory (Azure AD) hitelesítést használjon.
- Hozzáférés a példányhoz alacsony jogosultsági szintű DBA-fiókkal.
- Konfigurálja a JIT jumpbox-hozzáférést a sysadmin-fiókhoz.
- Kapcsolja be az [SQL-naplózást,](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)és integrálja a riasztási mechanizmusokkal.
- Kapcsolja be [a Fenyegetésészlelés](../database/threat-detection-configure.md) az [SQL Azure Defender-csomagból.](../database/azure-defender-for-sql.md)

## <a name="dns"></a>DNS

**Konfigurálható egyéni DNS a SQL Managed Instance?**

Igen. Lásd: [Egyéni DNS konfigurálása a Azure SQL Managed Instance.](./custom-dns-configure.md)

**Frissíthetők a DNS-adatok?**

Igen. Lásd: [Virtuális hálózati DNS-kiszolgálók szinkronizálása SQL Managed Instance virtuális fürtön.](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md)

## <a name="change-time-zone"></a>Időzóna módosítása

**Módosítom egy meglévő felügyelt példány időzónát?**

Az időzóna-konfigurációt a felügyelt példány első kiépítésekor lehet beállítani. Egy meglévő felügyelt példány időzónája nem támogatott. Részletekért lásd: [Időzóna-korlátozások.](timezones-overview.md#limitations)

Megkerülő megoldásként hozzon létre egy új felügyelt példányt a megfelelő időzónával, majd hajtson végre manuális biztonsági mentést és visszaállítást, vagy amit javasoljuk, hajtson végre egy példányközi, időponthoz időben visszaállító [visszaállítást.](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)


## <a name="security-and-database-encryption"></a>Biztonság és adatbázis-titkosítás

**Elérhető a rendszergazdai kiszolgálói szerepkör a SQL Managed Instance?**

Igen, az ügyfelek létrehozhatnak olyan bejelentkezéseket, amelyek a rendszergazdai szerepkör tagjai.  Azok az ügyfelek, akik a rendszergazdai jogosultságot feltételezik, szintén felelősséget vállalnak a példány üzemeltetéseért, ami negatív hatással lehet az SLA-kötelezettségvállalásra. A rendszergazdai kiszolgálói szerepkörbe való bejelentkezés hozzáadásához lásd: [Azure AD-hitelesítés.](./aad-security-configure-tutorial.md#azure-ad-authentication)

**Támogatott transzparens adattitkosítás a SQL Managed Instance?**

Igen, transzparens adattitkosítás támogatott a SQL Managed Instance. Részletekért lásd: [transzparens adattitkosítás a SQL Managed Instance.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal)

**Kihasználható a "saját kulcs" modell a TDE-hez?**

Igen, Azure Key Vault BYOK-forgatókönyvhöz elérhető a Azure SQL Managed Instance. Részletekért lásd a transzparens adattitkosítás [által felügyelt kulccsal való adatokat.](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

**Át tudok miulálni egy titkosított SQL Server adatbázisba?**

Igen, írhat. Titkosított adatbázis SQL Server áttelepítéséhez exportálja és importálja a meglévő tanúsítványokat a felügyelt példányra, majd állítsa vissza az adatbázis teljes biztonsági másolatát a felügyelt példányban. 

A [TDE Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) adatbázisok áttelepítése is használható.

**Hogyan konfigurálható a TDE-védő rotációja a SQL Managed Instance?**

A felügyelt példányok TDE-védője a Azure Cloud Shell. Útmutatásért tekintse meg [a transzparens adattitkosítás a SQL Managed Instance a saját kulcsának használatával a Azure Key Vault.](scripts/transparent-data-encryption-byok-powershell.md)

**Visszaállítható a titkosított adatbázisom SQL Managed Instance?**

Igen, nem kell visszafejtenie az adatbázist ahhoz, hogy visszaállítsa SQL Managed Instance. Meg kell adnia a forrásrendszer titkosítási kulcsvédőjeként használt tanúsítványt/kulcsot SQL Managed Instance hogy be tudja olvasni a titkosított biztonságimásolat-fájl adatait. Ezt kétféleképpen lehet megtenni:

- *Töltse fel a tanúsítványvédőt a SQL Managed Instance.* Ez csak a PowerShell használatával használhatja. A [mintaszk](./tde-certificate-migrate.md) szkript a teljes folyamatot ismerteti.
- *Töltse fel az aszimmetrikus kulcsvédőt a Azure Key Vault, és SQL Managed Instance rá.* Ez a megközelítés hasonlít a byok (bring-your-own-key, byok) TDE használati esethez, amely szintén Key Vault a titkosítási kulcs tárolására. Ha nem szeretné titkosítási kulcsvédőként használni a kulcsot, és csak szeretné elérhetővé tenni a kulcsot a SQL Managed Instance számára a titkosított adatbázisok visszaállításához, kövesse a [BYOK TDE](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)beállítására vonatkozó utasításokat, és ne jelölje be A kiválasztott kulcs legyen az alapértelmezett **TDE-védő.**

Miután elérhetővé teszi a titkosítási SQL Managed Instance számára, folytathatja a szabványos adatbázis-visszaállítási eljárással.

## <a name="purchasing-models-and-benefits"></a>Vásárlási modellek és előnyök

**Milyen vásárlási modellek érhetők el SQL Managed Instance?**

SQL Managed Instance [virtuálismag-alapú vásárlási modellt kínál.](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)

**Milyen költségmegtakarítások érhetők el a SQL Managed Instance?**

A következő módokon takaríthat meg Azure SQL előnyöket:
-    Maximalizálhatja a helyszíni licencek meglévő befektetéseit, és akár 55%-ot is megtakaríthatja a [Azure Hybrid Benefit.](../azure-hybrid-benefit.md?tabs=azure-powershell) 
-    Foglaljon le foglalást a számítási erőforrásokhoz, és akár 33%-ot is megtakaríthat a [Reserved Instance Benefitben.](../database/reserved-capacity-overview.md) Kombinálhatja ezt az Azure Hybrid Benefit megoldással, akár 82%-os megtakarítást is megtakaríthat. 
-    Az [Azure Dev/Test pricing Benefit](https://azure.microsoft.com/pricing/dev-test/) kedvezményes díjszabást kínál a folyamatban lévő fejlesztési és tesztelési számítási feladatokhoz, így akár 55%-kal is alacsonyabb díjszabást biztosít a listához való árhoz.

**Ki jogosult fenntartott példányok juttatásra?**

A fenntartott példányok juttatására való jogosultsághoz az előfizetés típusának nagyvállalati szerződésnek (ajánlatszám: MS-AZR-0017P vagy MS-AZR-0148P) vagy használat alapján fizetett díjszabással (ajánlatszám: MS-AZR-0003P vagy MS-AZR-0023P) kell lennie. További információ a foglalásokkal kapcsolatban: [Reserved Instance Benefit.](../database/reserved-capacity-overview.md) 

**Lehetséges a foglalások lemondása, cseréje vagy visszatérítése?**

Bizonyos korlátozásokkal megszüntetheti, kicserélheti vagy visszatérítheti a foglalásokat. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>Felügyelt példány és biztonsági mentési tár számlázása

**Mik a SQL Managed Instance díjszabási lehetőségek?**

A felügyelt példányok díjszabási lehetőségeinek felfedezéséhez tekintse meg [a díjszabási oldalt.](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

**Hogyan követem nyomon a felügyelt példányom számlázási költségeit?**

Ezt a következő [megoldással Azure Cost Management:](../../cost-management-billing/index.yml). Lépjen az **Előfizetések lapra** [a Azure Portal](https://portal.azure.com) válassza a **Költségelemzés lehetőséget.** 

Használja a **Halmozott költségek** lehetőséget, majd szűrje az **erőforrástípust a következőre:** `microsoft.sql/managedinstances` .

**Mennyibe kerül az automatikus biztonsági mentés?**

A biztonsági másolatok beállított megőrzési időtartamától függetlenül a fenntartott adattárolási területével megegyező mennyiségű szabad biztonsági mentési tárterületet kap. Ha a biztonsági másolatok tárhelyfelhasználása a lefoglalt szabad biztonsági mentési tárterületen belül van, a felügyelt példányon történő automatikus biztonsági mentések nem lesznek további költségek, ezért ingyenesek lesznek. Ha túllépi a biztonsági másolatok tárhelyhasználatát a szabad terület fölött, az az EGYESÜLT Államok régióiban körülbelül 0,20 –0,24 USD/GB/hó költséget eredményez, vagy a régió díjszabását tartalmazó oldalon talál további információt. További részletekért lásd a [Biztonsági másolatok tárterület-használatának magyarázatát.](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923)

**Hogyan monitorom a biztonsági másolatok tárolási fogyasztásának számlázási költségeit?**

A biztonsági mentési tárterület költségeit a tárfiókon keresztül Azure Portal. Útmutatásért lásd: [Az automatikus biztonsági mentések költségeinek figyelése.](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs) 

**Hogyan optimalizálhatóak a biztonsági másolatok tárolási költségei a felügyelt példányon?**

A biztonsági másolatok tárolási költségeinek optimalizálásához lásd: A biztonsági mentés [finomhangolása a SQL Managed Instance.](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)

## <a name="cost-saving-use-cases"></a>Költségmegtakarítási esetek

**Hol találom a használatot és a költségmegtakarítást a SQL Managed Instance?**

SQL Managed Instance esettanulmányok:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

A felügyelt példány üzembe helyezésével járó előnyök, költségek és kockázatok Azure SQL Managed Instance egy Forrester-tanulmány is rendelkezésre Microsoft Azure SQL Database: A felügyelt példányok teljes gazdasági [hatása.](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance)

## <a name="password-policy"></a>Jelszó-szabályzat 

**Milyen jelszó-szabályzatok vonatkoznak az SQL SQL Managed Instance való bejelentkezésre?**

SQL Managed Instance SQL-bejelentkezések jelszó-szabályzata örökli a felügyelt példányt futtató virtuális fürtöt alkotó virtuális gépekre alkalmazott Azure platform-szabályzatokat. Jelenleg ezek a beállítások nem változtathatóak meg, mivel ezeket a beállításokat az Azure határozza meg, és a felügyelt példány örökli őket.

 > [!IMPORTANT]
 > Az Azure platform anélkül módosíthatja a szabályzatok követelményeit, hogy értesítse a szolgáltatásokat a szabályzatok alapján.

**Mik az Azure platform jelenlegi szabályzatai?**

Minden bejelentkezésnek be kell állítania a jelszavát a bejelentkezéskor, és módosítania kell a jelszavát, miután elérte a maximális életkort.

| **Szabályzat** | **Biztonsági beállítás** |
| --- | --- |
| Jelszó maximális kora | 42 nap |
| Jelszó minimális kora | 1 nap |
| Jelszó minimális hossza | 10 karakter |
| A jelszónak meg kell felelnie az összetettségi követelményeknek | Engedélyezve |

**Le lehet tiltani a jelszó összetettségét és lejáratát SQL Managed Instance bejelentkezési szinten?**

Igen, a bejelentkezési szint CHECK_POLICY és CHECK_EXPIRATION is szabályozható. Az aktuális beállításokat a következő T-SQL-parancs végrehajtásával ellenőrizheti:

```sql
SELECT *
FROM sys.sql_logins
```

Ezt követően a parancs végrehajtásával módosíthatja a megadott bejelentkezési beállításokat:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(cserélje le a "test" helyére a kívánt bejelentkezési nevet, és módosítsa a szabályzat és a lejárat értékeit)


## <a name="service-updates"></a>Szolgáltatási hírek

**Mi a legfelső szintű hitelesítésszolgáltató módosítása a Azure SQL Database & SQL Managed Instance?**

Lásd: [Tanúsítványrotáció a Azure SQL Database & SQL Managed Instance.](../updates/ssl-root-certificate-expiring.md) 

**Mi a tervezett karbantartási esemény SQL Managed Instance?**

Lásd: [Plan for Azure maintenance events in SQL Managed Instance](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Azure-visszajelzés és -támogatás

**Hol hagyhatom az ötleteimet a SQL Managed Instance javítására?**

Szavazhat egy új felügyeltpéldány-funkcióra, vagy új fejlesztési ötletet adhat a visszajelzési fórumon [SQL Managed Instance fórumon.](https://feedback.azure.com/forums/915676-sql-managed-instance) Így ön hozzájárulhat a termékfejlesztéshez, és rangsorolhatja a lehetséges fejlesztéseket.

**Hogyan hozhatok létre Azure-támogatás kérést?**

A kérések létrehozásáról Azure-támogatás [lásd: How to create Azure-támogatás request](../../azure-portal/supportability/how-to-create-azure-support-request.md)(Kérés Azure-támogatás létrehozása).
