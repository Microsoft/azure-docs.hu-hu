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
ms.openlocfilehash: 9faaf79958443c252a8d913fbd7448389c610e09
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628577"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Felügyelt Azure SQL-példányra vonatkozó gyakori kérdések (GYIK)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ez a cikk az [Azure SQL felügyelt példányának](sql-managed-instance-paas-overview.md)leggyakoribb kérdéseit tartalmazza.

## <a name="supported-features"></a>Támogatott funkciók

**Hol találhatom meg a felügyelt SQL-példányok által támogatott szolgáltatások listáját?**

A felügyelt SQL-példány támogatott funkcióinak listáját lásd: az [Azure SQL felügyelt példányának funkciói](../database/features-comparison.md).

A felügyelt Azure SQL-példány és a SQL Server szintaxisának és viselkedésének különbségeit lásd: a [T-SQL eltérései SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="technical-specification-resource-limits-and-other-limitations"></a>Technikai specifikáció, erőforrás-korlátozások és egyéb korlátozások
 
**Hol találhatók a felügyelt SQL-példányok technikai jellemzői és erőforrás-korlátai?**

Az elérhető hardver-létrehozási jellemzőkkel kapcsolatban lásd: [technikai különbségek a hardveres generációkban](resource-limits.md#hardware-generation-characteristics).
A rendelkezésre álló szolgáltatási szintek és azok jellemzői a [szolgáltatási szintek közötti technikai különbségek](resource-limits.md#service-tier-characteristics)című részben olvashatók.

**Milyen szolgáltatási szintet vehetek igénybe?**

Bármely ügyfél bármely szolgáltatási szinten jogosult. Ha azonban a [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)használatával szeretné kicserélni a meglévő licenceket az Azure SQL felügyelt példányának kedvezményes díjszabására, vegye figyelembe, hogy a frissítési garanciával rendelkező SQL Server Enterprise Edition-ügyfelek jogosultak a [általános célú](../database/service-tier-general-purpose.md) vagy [üzletileg kritikus](../database/service-tier-business-critical.md) teljesítményi szintjeire, valamint a frissítési garanciával rendelkező SQL Server Standard Edition ügyfelekre csak a általános célú teljesítmény szintjére jogosultak. További részletekért tekintse meg [a AHB adott jogosultságait](../azure-hybrid-benefit.md?tabs=azure-powershell#what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server).

**Milyen előfizetési típusokat támogat az SQL felügyelt példánya?**

A támogatott előfizetési típusok listáját a [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types)című részben tekintheti meg. 

**Mely Azure-régiók támogatottak?**

A felügyelt példányok a legtöbb Azure-régióban létrehozhatók; Lásd: [az SQL felügyelt példányainak támogatott régiói](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Ha olyan régióban felügyelt példányra van szüksége, amely jelenleg nem támogatott, [küldjön egy támogatási kérést a Azure Portalon keresztül](../database/quota-increase-request.md).

**Van-e kvóta-korlátozás a felügyelt SQL-példányok üzembe helyezéséhez?**

A felügyelt példány két alapértelmezett korláttal rendelkezik: a használható alhálózatok számának korlátozása és a kiépíthető virtuális mag számának korlátozása. A korlátok az előfizetési típusok és régiók között változnak. Az előfizetés típusa szerint a regionális erőforrásokra vonatkozó korlátozások listáját a következő témakörben tekintheti meg: táblázat a [regionális erőforrások korlátozása](resource-limits.md#regional-resource-limitations)alapján. Ezek olyan enyhe korlátok, amelyek igény szerint növelhetők. Ha az aktuális régiókban több felügyelt példányt kell kiépíteni, küldjön egy támogatási kérést a kvóta növeléséhez a Azure Portal használatával. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](../database/quota-increase-request.md).

**Megnövelhető az adatbázis-korlát (100) száma a felügyelt példányon igény szerint?**

Nem, és jelenleg nincsenek véglegesítve a felügyelt SQL-példányon található adatbázisok számának növelésére vonatkozó véglegesített csomagok. 

**Hol lehet migrálni, ha több mint 8TB tárhely van?**
Érdemes lehet áttelepíteni más Azure-beli ízekre, amelyek megfelelnek a számítási feladatnak: [Azure SQL Database nagy kapacitású](../database/service-tier-hyperscale.md) vagy [SQL Server az Azure Virtual Machineson](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

**Hol lehet migrálni, ha bizonyos hardverkövetelmények, például nagyobb RAM-virtuális mag arány vagy több processzor?**
Érdemes lehet áttelepíteni [SQL Serverre az Azure Virtual Machines](../virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) vagy az optimalizált memória/CPU [Azure SQL Database](../database/sql-database-paas-overview.md) .

## <a name="known-issues-and-defects"></a>Ismert problémák és hibák

**Hol találhatók az ismert problémák és hibák?**

A termékkel kapcsolatos hibák és ismert problémák esetén lásd: [ismert problémák](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Új funkciók

**Hol találhatók a legújabb funkciók és a szolgáltatások a nyilvános előzetes verzióban?**

Az új és az előzetes verziójú funkciókhoz lásd: [kibocsátási megjegyzések](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="create-update-delete-or-move-sql-managed-instance"></a>SQL felügyelt példány létrehozása, frissítése, törlése vagy áthelyezése

**Hogyan lehet SQL felügyelt példányt kiépíteni?**

A [Azure Portal](instance-create-quickstart.md), a [PowerShell](scripts/create-configure-managed-instance-powershell.md), az [Azure CLI](https://techcommunity.microsoft.com/t5/azure-sql-database/create-azure-sql-managed-instance-using-azure-cli/ba-p/386281) és az [ARM sablonokból](/archive/blogs/sqlserverstorageengine/creating-azure-sql-managed-instance-using-arm-templates)is kiépítheti a példányokat.

**Létrehozhatok felügyelt példányokat egy meglévő előfizetésben?**

Igen, egy meglévő előfizetésben is kiépítheti a felügyelt példányokat, ha az előfizetés a [támogatott előfizetési típusok](resource-limits.md#supported-subscription-types)közé tartozik.

**Miért nem sikerült olyan felügyelt példányt kiépíteni az alhálózatban, amelyben a név számjegytel kezdődik?**

Ez az alapul szolgáló összetevő aktuális korlátozása, amely ellenőrzi az alhálózat nevét a regex ^ [a-za-Z_] [^ \\ \/ \: \* \? \" \<\> \| \` \' \^ ] * (? <! [ \. \s]) $. A regexnek átadott nevek és az érvényes alhálózatok nevei jelenleg támogatottak.

**Hogyan lehet méretezni a felügyelt példányt?**

Felügyelt példányát [Azure Portal](../database/service-tiers-vcore.md?tabs=azure-portal#selecting-a-hardware-generation), a [PowerShell](/archive/blogs/sqlserverstorageengine/change-size-azure-sql-managed-instance-using-powershell), az [Azure CLI](/cli/azure/sql/mi#az-sql-mi-update) vagy az [ARM sablonok](/archive/blogs/sqlserverstorageengine/updating-azure-sql-managed-instance-properties-using-arm-templates)segítségével méretezheti.

**Áthelyezhetem a felügyelt példányt az egyik régióból a másikba?**

Igen, írhat. Útmutatásért lásd: [erőforrások áthelyezése régiók között](../database/move-resources-across-regions.md).

**Hogyan törölhetem a felügyelt példányt?**

A felügyelt példányok a Azure Portal, a [PowerShell](/powershell/module/az.sql/remove-azsqlinstance), az [Azure CLI](/cli/azure/sql/mi#az-sql-mi-delete) vagy a [Resource Manager REST API](/rest/api/sql/managedinstances/delete)-k használatával törölhetők.

**Mennyi ideig tart egy példány létrehozása vagy frissítése, vagy egy adatbázis visszaállítása?**

Az új felügyelt példány létrehozásához vagy a szolgáltatási szintek (virtuális mag, tárolás) módosításához szükséges idő számos tényezőtől függ. Lásd: [felügyeleti műveletek](sql-managed-instance-paas-overview.md#management-operations).
 
## <a name="naming-conventions"></a>Elnevezési konvenciók

**A felügyelt példányok neve megegyezik egy SQL Server helyszíni példánnyal?**

A felügyelt példány nevének módosítása nem támogatott.

**Módosíthatom a DNS-zóna előtagját?**

Igen, felügyelt példány alapértelmezett DNS-zónája *. a Database.Windows.net* módosítható. 

Ha egy másik DNS-zónát szeretne használni az alapértelmezett helyett, például: *. contoso.com*: 
- Alias definiálása CliConfig használatával. Az eszköz csak a beállításjegyzék-beállítások burkolója, így a csoportházirend vagy egy parancsfájl használatával is elvégezhető.
- Használjon *CNAME* -t a *TrustServerCertificate = True* kapcsolóval.

## <a name="migration-options"></a>Migrálási lehetőségek

**Hogyan telepíthetek át Azure SQL Database egy vagy rugalmas készletről az SQL-alapú felügyelt példányra?**

A felügyelt példányok a számítási és a tárolási méretnél ugyanazok a teljesítményszint, mint a Azure SQL Database egyéb központi telepítési lehetőségei. Ha egyetlen példányon szeretné összevonni az adatait, vagy egyszerűen csak felügyelt példányban támogatott szolgáltatást kell használnia, az adatait Exportálás/Importálás (BACPAC) funkcióval is áttelepítheti. Az alábbi módokon érdemes megfontolni SQL Database áttelepítését az SQL felügyelt példányára: 
- [Külső adatforrás](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-129-using-data-source-external-from-azure-sql/ba-p/1443210) használata
- A [SQLPackage](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-migrate-azure-sql-database-to-azure-sql-managed-instance/ba-p/369182) használata
- A [BCP](https://medium.com/azure-sqldb-managed-instance/migrate-from-azure-sql-managed-instance-using-bcp-674c92efdca7) használata

**Hogyan telepíthetem át a példány-adatbázist egyetlen Azure SQL Databasere?**

Az egyik lehetőség, hogy [exportál egy adatbázist a BACPAC](../database/database-export.md) -be, majd [importálja a BACPAC-fájlt](../database/database-import.md). Ez az ajánlott módszer, ha az adatbázis 100 GB-nál kisebb.

A [tranzakciós replikáció](replication-two-instances-and-sql-server-configure-tutorial.md) akkor használható, ha az adatbázis minden táblája rendelkezik *elsődleges* kulccsal, és nincsenek memóriában tárolt OLTP objektumok az adatbázisban.

A felügyelt példányból származó natív COPY_ONLY biztonsági másolatok nem állíthatók vissza SQL Server, mert a felügyelt példány magasabb verziójú adatbázis-verzióval rendelkezik, mint SQL Server. További részletekért lásd: [csak másolatot tartalmazó biztonsági mentés](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15).

**Hogyan telepíthetem át a SQL Server példányt az SQL felügyelt példányára?**

SQL Server példány áttelepítéséhez lásd: [SQL Server példány áttelepítése az Azure SQL felügyelt példányára](migrate-to-instance-from-sql-server.md).

**Hogyan válthatok át más platformokról az SQL felügyelt példányaira?**

A más platformokról való áttelepítéssel kapcsolatos információkért lásd: az [Azure Database áttelepítési útmutatója](https://datamigration.microsoft.com/).

## <a name="switch-hardware-generation"></a>Hardver generálásának váltása 

**Átválthatom a felügyelt példányok hardveres generációját a Gen 4 és a Gen 5 online között?**

A Gen4-ről Gen5-re történő automatikus online váltás akkor lehetséges, ha a Gen5 hardver abban a régióban érhető el, ahol a felügyelt példány üzembe lett helyezve. Ebben az esetben megtekintheti a [virtuális mag-modell áttekintését ismertető oldalt](../database/service-tiers-vcore.md) , amely ismerteti, hogyan válthat a hardveres generációk között.

Ez egy hosszan futó művelet, mivel új felügyelt példány lesz kiépítve a háttérben, és az adatbázisok automatikusan átkerülnek a régi és az új példány között a folyamat végén található gyors feladatátvételsel.

Megjegyzés: a Gen4 hardvereket a rendszer fokozatosan kivezeti, és már nem érhető el az új üzemelő példányokhoz. Minden új adatbázist telepíteni kell a Gen5 hardveren. Nem érhető el a Gen5 és a Gen4 közötti váltás is.

## <a name="performance"></a>Teljesítmény 

**Hogyan lehet összehasonlítani a felügyelt példányok teljesítményét, hogy SQL Server a teljesítményt?**

A felügyelt példány és a SQL Server közötti teljesítmény-összehasonlításhoz jó kiindulási pont az [Azure SQL felügyelt példánya és a SQL Server cikk teljesítményének összehasonlítására szolgáló ajánlott eljárások](https://techcommunity.microsoft.com/t5/azure-sql-database/the-best-practices-for-performance-comparison-between-azure-sql/ba-p/683210) .

**Mi okoz teljesítménybeli különbségeket a felügyelt példányok és a SQL Server között?**

Tekintse meg [az SQL felügyelt példány és a SQL Server közötti teljesítménybeli különbségek fő okait](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/). A naplófájl méretének általános célú felügyelt példány teljesítményére gyakorolt hatásával kapcsolatos további információkért lásd: a [naplófájl méretének hatása a általános célú](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

**Hogyan a felügyelt példány teljesítményének finomhangolását?**

A felügyelt példány teljesítményének optimalizálása a alábbiak szerint végezhető el:
- [Automatikus hangolás](../database/automatic-tuning-overview.md) , amely az AI és a gépi tanulás alapján biztosítja a teljesítményt és a stabil munkaterheléseket a folyamatos teljesítmény-hangolással.
-    [Memóriabeli OLTP](../in-memory-oltp-overview.md) , amely javítja a tranzakciós feldolgozási számítási feladatok átviteli sebességét és késését, és gyorsabb üzleti eredményeket biztosít. 

Ha még tovább szeretné hangolni a teljesítményt, érdemes lehet az [alkalmazás-és adatbázis-hangolás](../database/performance-guidance.md#tune-your-database) *ajánlott eljárásait* alkalmaznia.
Ha a munkaterhelés sok kis tranzakcióból áll, érdemes lehet [átváltani a proxyról átirányítani a kapcsolódási típust](connection-types-overview.md#changing-connection-type) az alacsonyabb késés és a nagyobb átviteli sebesség érdekében.

## <a name="monitoring-metrics-and-alerts"></a>Figyelés, mérőszámok és riasztások

**Milyen lehetőségek vannak a felügyelt példányok figyelésére és riasztására?**

Az SQL felügyelt példányok felhasználásának és teljesítményének figyelésére és riasztására vonatkozó lehetséges lehetőségekért lásd: az [Azure SQL felügyelt példányok figyelési beállításai blogbejegyzése](https://techcommunity.microsoft.com/t5/azure-sql-database/monitoring-options-available-for-azure-sql-managed-instance/ba-p/1065416). Az SQL MI valós idejű teljesítményének figyeléséhez lásd: [valós idejű Teljesítményfigyelés az Azure SQL db felügyelt példányaihoz](/archive/blogs/sqlcat/real-time-performance-monitoring-for-azure-sql-database-managed-instance).

**Használhatom az SQL Profilert a teljesítmény nyomon követéséhez?**

Igen, az SQL Profiler támogatott vagy SQL felügyelt példány. További részletek: [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15).

**Database Advisor és Lekérdezési terheléselemző támogatottak a felügyelt példányok adatbázisaiban?**

Nem, nem támogatottak. Az adatbázisok figyeléséhez használhatja az [DMV](../database/monitoring-with-dmvs.md) és a [query Store](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store?preserve-view=true&view=sql-server-ver15) -t az [SQL Profiler](/sql/tools/sql-server-profiler/sql-server-profiler?preserve-view=true&view=sql-server-ver15) és a [xevent típusú eseményekhez](/sql/relational-databases/extended-events/extended-events?preserve-view=true&view=sql-server-ver15) használatával.

**Létrehozhatok metrikai riasztásokat a felügyelt SQL-példányok esetén?**

Igen. Útmutatásért lásd: [riasztások létrehozása SQL felügyelt példányhoz](alerts-create.md).

**Létrehozhatok metrikai riasztásokat egy felügyelt példányon található adatbázison?**

Nem, a metrikák riasztása csak felügyelt példány esetén érhető el. A felügyelt példányokban lévő egyes adatbázisok esetében nem érhetők el riasztási mérőszámok.

## <a name="storage-size"></a>Tárterület mérete

**Mekkora a maximális tárolási méret az SQL felügyelt példány esetében?**

Az SQL felügyelt példányának tárolási mérete a kiválasztott szolgáltatási szinttől (általános célú vagy üzletileg kritikus) függ. A szolgáltatási szintek tárolási korlátozásai a [szolgáltatási szintek jellemzői](../database/service-tiers-general-purpose-business-critical.md)című részben olvashatók.

**A felügyelt példányok számára mekkora a minimális tárterület?**

Egy példányban rendelkezésre álló tárterület minimális mérete 32 GB. A tárterület 32 GB-os növekményekben adható hozzá a tárterület maximális méretéhez. Az első 32GB díjmentes.

**Megnövelhető a példányhoz rendelt tárolóhely a számítási erőforrásoktól függetlenül?**

Igen, a számítási kapacitástól függetlenül vásárolhat kiegészítő tárhelyet. Lásd: a *példányok maximális száma fenntartott tároló* a [táblában](resource-limits.md#hardware-generation-characteristics).

**Hogyan optimalizálható a tárolási teljesítmény általános célú szolgáltatási szinten?**

A tárolási teljesítmény optimalizálása érdekében tekintse [meg a általános célú-tároló ajánlott eljárásai](https://techcommunity.microsoft.com/t5/datacat/storage-performance-best-practices-and-considerations-for-azure/ba-p/305525)című témakört.

## <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás

**A biztonsági mentési tárterület le lett vonva a felügyelt példányok tárterületéről?**

Nem, a biztonsági mentési tár nem vonható le a felügyelt példány tárolóhelyéről. A biztonsági mentési tár a példány tárolóhelytől független, és nem korlátozódik a méretre. A biztonsági mentési tárterületet a példány-adatbázisok biztonsági mentésének időtartamára korlátozza, amely akár 35 napig is konfigurálható. Részletekért lásd: [automatizált biztonsági mentések](../database/automated-backups-overview.md).

**Hogyan tekinthetem meg, ha automatizált biztonsági mentés készül a felügyelt példányon?**

Ha nyomon szeretné követni, hogy mikor hajtottak végre automatizált biztonsági mentéseket a felügyelt példányon, tekintse meg [Az Azure SQL felügyelt példányának automatizált biztonsági mentését](https://techcommunity.microsoft.com/t5/azure-database-support-blog/lesson-learned-128-how-to-track-the-automated-backup-for-an/ba-p/1442355)ismertető témakört.

**Támogatott az igény szerinti biztonsági mentés?**

Igen, létrehozhat egy csak másolatot tartalmazó teljes biztonsági mentést az Azure Blob Storageban, de csak felügyelt példányban lehet helyreállítani. Részletekért lásd: [csak másolási biztonsági mentés](/sql/relational-databases/backup-restore/copy-only-backups-sql-server?preserve-view=true&view=sql-server-ver15). Azonban a csak másolásra alkalmas biztonsági mentés nem lehetséges, ha az adatbázist a szolgáltatás által felügyelt TDE titkosítja, mivel a titkosításhoz használt tanúsítvány nem érhető el. Ilyen esetben használja az időponthoz való visszaállítás funkciót, hogy az adatbázist másik SQL felügyelt példányra helyezze át, vagy váltson az ügyfél által felügyelt kulcsra.

**A felügyelt példányok natív visszaállítása (a. bak fájlokból) támogatott?**

Igen, a SQL Server 2005 + verziók esetében támogatott és elérhető.  A natív visszaállítás használatához töltse fel a. bak fájlt az Azure Blob Storage-ba, és hajtsa végre a T-SQL-parancsokat. További részletekért lásd: [natív visszaállítás az URL-](./migrate-to-instance-from-sql-server.md#native-restore-from-url)címről.

## <a name="business-continuity"></a>Az üzletmenet folytonossága

**Replikálódnak a rendszeradatbázisok a feladatátvételi csoport másodlagos példányára?**

A rendszeradatbázisokat a rendszer nem replikálja a feladatátvételi csoport másodlagos példányára. Ezért előfordulhat, hogy a rendszeradatbázisok objektumaitól függő forgatókönyvek a másodlagos példányon nem lesznek lehetségesek, kivéve, ha az objektumokat manuálisan hozták létre a másodlagos kiszolgálón. A megkerülő megoldásért lásd: [az objektumtól függő forgatókönyvek engedélyezése a rendszeradatbázisokból](../database/auto-failover-group-overview.md?tabs=azure-powershell#enable-scenarios-dependent-on-objects-from-the-system-databases).
 
## <a name="networking-requirements"></a>Hálózati követelmények 

**Mik a jelenlegi bejövő/kimenő NSG megkötései a felügyelt példány alhálózatán?**

A szükséges NSG-és UDR-szabályokat [itt](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration)dokumentálja, és automatikusan beállítja a szolgáltatás.
Ne feledje, hogy ezek a szabályok csak a szolgáltatás karbantartásához szükségesek. A felügyelt példányhoz való kapcsolódáshoz és a különböző funkciók használatához további, a szolgáltatásra vonatkozó szabályokat kell megadnia, amelyeket meg kell őriznie.

**Hogyan állíthatok be bejövő NSG szabályokat a felügyeleti portokon?**

A felügyeleti portok szabályainak beállítása a felügyelt SQL-példány feladata. Ez a [szolgáltatás által támogatott alhálózat-konfiguráció](connectivity-architecture-overview.md#service-aided-subnet-configuration)nevű funkcióval érhető el.
Ezzel biztosítható, hogy a felügyeleti forgalom zavartalanul elérhető legyen az SLA teljesítése érdekében.

**Lekérhetem a bejövő felügyeleti forgalomhoz használt forrás IP-tartományokat?**

Igen. A hálózati biztonsági csoporton keresztül érkező forgalmat a [Network Watcher flow-naplók konfigurálásával](../../network-watcher/network-watcher-monitoring-overview.md#analyze-traffic-to-or-from-a-network-security-group)elemezheti.

**Beállíthat NSG az adatvégponthoz való hozzáférés szabályozásához (1433-es port)?**

Igen. Egy felügyelt példány üzembe helyezése után beállíthat olyan NSG, amelyek a 1433-es porthoz való bejövő hozzáférést vezérlik. Javasoljuk, hogy a lehető legnagyobb mértékben Szűkítse az IP-tartományt.

**Beállítható a NVA vagy a helyszíni tűzfal a kimenő felügyeleti forgalom szűrésére a teljes tartománynevek alapján?**

Nem. Ez számos okból nem támogatott:
-    A bejövő felügyeleti kérelemre válaszként szolgáló útválasztási forgalom aszimmetrikus, és nem működik.
-    A tárterületre kerülő útválasztási forgalmat a rendszer az átviteli korlátok és a késés miatt befolyásolja, így a várt szolgáltatás minősége és rendelkezésre állása nem lesz elérhető.
-    A tapasztalatok alapján ezek a konfigurációk a hibás és nem támogatottak.

**Megadhatom a NVA vagy a tűzfalat a kimenő nem felügyeleti forgalomhoz?**

Igen. Ennek a legegyszerűbb módja, ha 0/0 szabályt ad hozzá a felügyelt példány alhálózatához társított UDR, hogy átirányítsa a forgalmat a NVA-on keresztül.
 
**Hány IP-címre van szükség egy felügyelt példányhoz?**

Az alhálózatnak elegendő számú elérhető [IP-címmel](connectivity-architecture-overview.md#network-requirements)kell rendelkeznie. A felügyelt példányhoz tartozó VNet-alhálózat méretének meghatározásához lásd: a [szükséges alhálózat méretének és tartományának meghatározása a felügyelt példányhoz](./vnet-subnet-determine-size.md). 

**Mi a teendő, ha nincs elegendő IP-cím a példány-frissítési művelet végrehajtására?**

Abban az esetben, ha nincs elegendő [IP-cím](connectivity-architecture-overview.md#network-requirements) abban az alhálózatban, amelyben a felügyelt példányt kiépíti, létre kell hoznia egy új alhálózatot és egy új felügyelt példányt. Azt is javasoljuk, hogy az új alhálózatot több lefoglalt IP-címmel hozza létre, hogy a jövőbeli frissítési műveletek során ne merüljenek fel hasonló helyzetek. Az új példány üzembe helyezése után manuálisan végezheti el az adatok biztonsági mentését és visszaállítását a régi és az új példányok között, vagy elvégezheti a példányok közötti [időpontra történő visszaállítást](point-in-time-restore.md?tabs=azure-powershell).

**Szükség van egy üres alhálózatra a felügyelt példány létrehozásához?**

Nem. Használhat egy üres alhálózatot vagy egy olyan alhálózatot, amely már tartalmazza a felügyelt példány (oka) t. 

**Módosíthatom az alhálózat címtartományt?**

Nem, ha vannak felügyelt példányok belül. Ez egy Azure hálózati infrastruktúra korlátozása. Csak [egy üres alhálózathoz adhat hozzá további címterület-helyet](../../virtual-network/virtual-network-manage-subnet.md#change-subnet-settings). 

**Áthelyezhetem a felügyelt példányt egy másik alhálózatra?**

Nem. Ez a felügyelt példányok jelenlegi tervezési korlátozása. Létrehozhat azonban egy új példányt egy másik alhálózatban, és manuálisan is elvégezheti az adatok biztonsági mentését és visszaállítását a régi és az új példány között, vagy egy példányon belüli [időpontra történő visszaállítást](point-in-time-restore.md?tabs=azure-powershell)végezhet.

**A felügyelt példány létrehozásához szükség van egy üres virtuális hálózatra?**

Ez nem kötelező. [Létrehozhat egy virtuális hálózatot az Azure SQL felügyelt példányához](./virtual-network-subnet-create-arm-template.md) , vagy [beállíthat egy meglévő virtuális hálózatot az Azure SQL felügyelt példányaihoz](./vnet-existing-add-subnet.md).

**Helyezhetek felügyelt példányt más szolgáltatásokkal egy alhálózaton?**

Nem. Jelenleg nem támogatott olyan felügyelt példány elhelyezése olyan alhálózatban, amely már tartalmaz más típusú erőforrásokat.

## <a name="connectivity"></a>Kapcsolatok 

**Tudok csatlakozni a felügyelt példányhoz IP-cím használatával?**

Nem, ez nem támogatott. A felügyelt példány állomásneve a felügyelt példány virtuális fürtje előtt leképezi a terheléselosztó nevét. Mivel egy virtuális fürt több felügyelt példányt is tárolhat, a kapcsolat nem irányítható át a megfelelő felügyelt példányra a név megadása nélkül.
További információ az SQL felügyelt példányának virtuális fürtjének architektúráról: [virtuális fürt kapcsolati architektúrája](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Használhatom a felügyelt példányom statikus IP-címet?**

Ez jelenleg nem támogatott.

Ritka, de szükséges helyzetekben előfordulhat, hogy egy felügyelt példány online áttelepítését egy új virtuális fürtre kell elvégezni. Ha szükséges, az áttelepítés a technológiai verem változásai miatt a szolgáltatás biztonságának és megbízhatóságának javítását célozza. Az új virtuális fürtre való Migrálás a felügyelt példány állomásneve számára leképezett IP-cím módosítását eredményezi. A felügyelt példány szolgáltatás nem igényel statikus IP-cím támogatását, és fenntartja a jogot arra, hogy a rendszeres karbantartási ciklusok részeként előzetes értesítés nélkül módosítsa.

Ezért erősen visszatartjuk az IP-cím módosíthatatlansági, mivel ez szükségtelen állásidőt eredményezhet.

**A felügyelt példány nyilvános végponttal rendelkezik?**

Igen. A felügyelt példány olyan nyilvános végponttal rendelkezik, amelyet alapértelmezés szerint csak a Service Management szolgáltatáshoz használ, de az ügyfél is engedélyezheti az adatelérést. További részletek: [SQL felügyelt példány használata nyilvános végpontokkal](./public-endpoint-overview.md). A nyilvános végpont konfigurálásához nyissa meg a [nyilvános végpont konfigurálása az SQL felügyelt példányban című részt](public-endpoint-configure.md).

**Hogyan szabályozza a felügyelt példány a nyilvános végponthoz való hozzáférést?**

A felügyelt példány a hálózat és az alkalmazás szintjén szabályozza a nyilvános végpont elérését.

A felügyeleti és központi telepítési szolgáltatások egy felügyelt példányhoz csatlakoznak egy olyan [felügyeleti végpont](./connectivity-architecture-overview.md#management-endpoint) használatával, amely egy külső terheléselosztó számára van leképezve. A rendszer csak akkor irányítja a forgalmat a csomópontokhoz, ha egy előre meghatározott porton érkezik, amely csak a felügyelt példány felügyeleti összetevői által használt. A csomópontokon a beépített tűzfal úgy van beállítva, hogy csak a Microsoft IP-címtartományok forgalmát engedélyezze. A tanúsítványok kölcsönösen hitelesítik a felügyeleti összetevők és a felügyeleti sík közötti összes kommunikációt. További részletek: [az SQL felügyelt példányának kapcsolati architektúrája](./connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Használhatom a nyilvános végpontot a felügyelt példányok adatbázisaiban lévő adathozzáféréshez?**

Igen. Az ügyfélnek engedélyeznie kell a nyilvános végponti adatok elérését [Azure Portal](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-in-the-azure-portal)  /  [PowerShell](public-endpoint-configure.md#enabling-public-endpoint-for-a-managed-instance-using-powershell) /ARM-ből, és konfigurálnia kell a NSG az adatporthoz való hozzáférés zárolásához (3342-es portszám). További információkért lásd: [nyilvános végpont konfigurálása az Azure SQL felügyelt példányában](public-endpoint-configure.md) , és az [Azure SQL felügyelt példány biztonságos használata nyilvános végponttal](public-endpoint-overview.md). 

**Megadhatok egyéni portot az SQL-adatvégpont (ok) hoz?**

Nem, ez a lehetőség nem érhető el.  A privát adatvégpont esetében a felügyelt példány a 1433 alapértelmezett portszámot használja, a nyilvános adatvégpont esetében pedig a felügyelt példány a 3342-as alapértelmezett portszámot használja.

**Mi a javasolt módszer a különböző régiókba helyezett felügyelt példányok összekapcsolására?**

Az Express Route Circuit-társítás az előnyben részesített módszer. A globális virtuális hálózati társítást az alábbi megjegyzésben ismertetett korlátozás támogatja.  

> [!IMPORTANT]
> [9/22/2020-ben bejelentettük a globális virtuális hálózati társítást az újonnan létrehozott virtuális fürtökhöz](https://azure.microsoft.com/en-us/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Ez azt jelenti, hogy a globális virtuális hálózati társítás támogatott a bejelentési dátum után üres alhálózatokban létrehozott SQL felügyelt példányok esetében, valamint az ezen alhálózatokban létrehozott összes további felügyelt példány esetében is. A többi SQL felügyelt példányok társításának támogatása az azonos régióban található hálózatokra korlátozódik a [globális virtuális hálózati társítás korlátai](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)miatt. További részletekért tekintse meg az [Azure Virtual Networks – gyakori kérdések](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) című cikket is. 

Ha az expressz útvonal-összekapcsolási és a globális virtuális hálózati társítás nem lehetséges, akkor az egyetlen lehetőség, ha helyek közötti VPN-kapcsolatot ([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [POWERSHELL](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)) hoz létre.

## <a name="mitigate-data-exfiltration-risks"></a>Az adatkiszűrése kockázatok enyhítése  

**Hogyan csökkentheti az adatkiszűrése kockázatait?**

A kiszűrése kockázatok enyhítése érdekében az ügyfeleknek ajánlott biztonsági beállításokat és vezérlőket alkalmaznia:

- [Transzparens adattitkosítás (TDE)](../database/transparent-data-encryption-tde-overview.md) bekapcsolása az összes adatbázison.
- A közös nyelvi futtatókörnyezet (CLR) kikapcsolása. Ez ajánlott a helyszínen is.
- Csak Azure Active Directory (Azure AD-) hitelesítést használjon.
- A példányt egy alacsony jogosultsági szintű DBA-fiókkal érheti el.
- Állítsa be az JIT Jumpbox hozzáférését a sysadmin fiókhoz.
- Kapcsolja be az [SQL-naplózást](/sql/relational-databases/security/auditing/sql-server-audit-database-engine), és integrálja azt a riasztási mechanizmusokkal.
- A [veszélyforrások észlelésének](../database/threat-detection-configure.md) bekapcsolása az [Azure Defender for SQL Suite szolgáltatásban](../database/azure-defender-for-sql.md) .

## <a name="dns"></a>DNS

**Beállíthat egyéni DNS-t az SQL felügyelt példányaihoz?**

Igen. Lásd: [Egyéni DNS konfigurálása az Azure SQL felügyelt példányaihoz](./custom-dns-configure.md).

**Használhatok DNS-frissítést?**

Igen. Lásd: [a virtuális hálózat DNS-kiszolgálóinak szinkronizálása beállítás az SQL felügyelt példányának virtuális fürtjén](./synchronize-vnet-dns-servers-setting-on-virtual-cluster.md).

## <a name="change-time-zone"></a>Időzóna módosítása

**Módosíthatom egy meglévő felügyelt példány időzónáját?**

Az időzóna-konfiguráció akkor állítható be, ha a felügyelt példányok első alkalommal lettek kiépítve. Egy meglévő felügyelt példány időzónájának módosítása nem támogatott. Részletekért lásd: [időzóna korlátozásai](timezones-overview.md#limitations).

A megkerülő megoldások közé tartozik egy új felügyelt példány létrehozása a megfelelő időzónával, majd manuális biztonsági mentés és visszaállítás végrehajtása, vagy az általunk ajánlott megoldás, amely egy [példányos időpontra történő visszaállítást](/archive/blogs/sqlserverstorageengine/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance)végez.


## <a name="security-and-database-encryption"></a>Biztonság és adatbázis-titkosítás

**Elérhető-e az SQL felügyelt példányának sysadmin kiszolgálói szerepköre?**

Igen, az ügyfelek létrehozhatnak olyan bejelentkezéseket, amelyek a sysadmin (rendszergazda) szerepkör tagjai.  A sysadmin (rendszergazda) jogosultságot feltételező ügyfelek a példány üzemeltetésének felelősségét is feltételezik, amely negatív hatással lehet az SLA-ra. A sysadmin (rendszergazda) kiszolgálói szerepkörbe való bejelentkezéshez lásd: [Azure ad-hitelesítés](./aad-security-configure-tutorial.md#azure-ad-authentication).

**Támogatott az SQL felügyelt példánya transzparens adattitkosítás?**

Igen, transzparens adattitkosítás támogatott az SQL felügyelt példányain. Részletekért lásd: [transzparens adattitkosítás a felügyelt SQL-példányhoz](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal).

**Kihasználhatom a "saját kulcs használata" modellt a TDE?**

Igen, a BYOK forgatókönyvhöz Azure Key Vault érhető el az Azure SQL felügyelt példányaihoz. Részletekért lásd: [transzparens adattitkosítás ügyfél által felügyelt kulccsal](../database/transparent-data-encryption-tde-overview.md?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key).

**Áttelepíthetek egy titkosított SQL Server adatbázist?**

Igen, írhat. Titkosított SQL Server-adatbázis átirányításához exportálnia és importálnia kell a meglévő tanúsítványokat a felügyelt példányba, majd készítsen egy teljes adatbázis biztonsági mentést, és állítsa vissza a felügyelt példányban. 

[Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/) használatával is áttelepítheti a TDE titkosított adatbázisait.

**Hogyan állíthatom be a TDE Protector rotációját az SQL felügyelt példányaihoz?**

Azure Cloud Shell használatával elforgathatja a felügyelt példányhoz tartozó TDE-védőt. Útmutatásért lásd: [transzparens adattitkosítás az SQL felügyelt példányában a saját kulcsának használatával Azure Key Vault](scripts/transparent-data-encryption-byok-powershell.md).

**Visszaállíthatom a titkosított adatbázist a felügyelt SQL-példányra?**

Igen, nem kell visszafejtenie az adatbázist az SQL felügyelt példányra való visszaállításhoz. Meg kell adnia egy tanúsítványt/kulcsot, amelyet titkosítási kulcsként kell használni a forrásrendszer SQL felügyelt példánya számára, hogy el tudja olvasni az adatait a titkosított biztonságimásolat-fájlból. Két lehetséges módszer:

- *Tanúsítvány feltöltése – a felügyelt SQL-példányra*. Ezt csak a PowerShell használatával lehet elvégezni. A [minta parancsfájl](./tde-certificate-migrate.md) a teljes folyamatot ismerteti.
- *Töltse fel az aszimmetrikus kulcs-védőt, hogy Azure Key Vault, és irányítsa az SQL felügyelt példányát*. Ez a megközelítés hasonlít a saját kulcsú (BYOK) TDE használatára, amely a titkosítási kulcs tárolásához a Key Vault-integrációt is használja. Ha nem szeretné a kulcsot titkosítási kulcsként használni, és csak azt szeretné, hogy elérhető legyen az SQL felügyelt példánya a titkosított adatbázis (ok) visszaállításához, kövesse az BYOK- [TDE beállításához](../database/transparent-data-encryption-tde-overview.md#manage-transparent-data-encryption)szükséges utasításokat, és ne jelölje be a jelölőnégyzetet, **hogy a kijelölt kulcs legyen az alapértelmezett TDE-védő**.

Miután a titkosítási védő elérhetővé vált az SQL felügyelt példánya számára, folytathatja a szabványos adatbázis-visszaállítási eljárást.

## <a name="purchasing-models-and-benefits"></a>Modellek és előnyök vásárlása

**Milyen beszerzési modellek érhetők el az SQL felügyelt példányaihoz?**

Az SQL felügyelt példánya [virtuális mag-alapú vásárlási modellt](sql-managed-instance-paas-overview.md#vcore-based-purchasing-model)kínál.

**Milyen költségadatok érhetők el az SQL felügyelt példányaihoz?**

A költségeket az alábbi módokon mentheti az Azure SQL-előnyökkel:
-    Maximalizálja a meglévő beruházásokat a helyszíni licencekben, és akár 55%-ot is megtakaríthat [Azure Hybrid Benefit](../azure-hybrid-benefit.md?tabs=azure-powershell). 
-    Véglegesítse a számítási erőforrások foglalását, és akár 33%-ot is megtakaríthat a [fenntartott példányok előnyeivel](../database/reserved-capacity-overview.md). Az Azure Hybrid benefittel akár 82%-os megtakarítást is használhat. 
-    Az [Azure dev/test díjszabási juttatásával](https://azure.microsoft.com/pricing/dev-test/) akár 55%-ot is megtakaríthat, amely kedvezményes díjszabást biztosít a folyamatos fejlesztési és tesztelési feladatokhoz.

**Ki jogosult a fenntartott példányokra?**

Ahhoz, hogy jogosult legyen a fenntartott példányok juttatására, az előfizetés típusának nagyvállalati szerződésnek (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy az utólagos elszámolású díjszabással rendelkező egyedi szerződésnek (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P) kell lennie. A foglalásokkal kapcsolatos további információkért lásd a [fenntartott példányok juttatása](../database/reserved-capacity-overview.md)című témakört. 

**Lehetséges a foglalások megszakítása, cseréje vagy visszatérítése?**

Bizonyos korlátozásokkal megszakíthatja, kicserélheti vagy visszafizetheti a foglalásokat. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="billing-for-managed-instance-and-backup-storage"></a>A felügyelt példány és a biztonsági mentési tár számlázása

**Mik az SQL felügyelt példány díjszabási lehetőségei?**

A felügyelt példány díjszabási lehetőségeinek megismeréséhez tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/).

**Hogyan követhetem nyomon a felügyelt példányom számlázási költségeit?**

Ezt a [Azure Cost Management megoldás](../../cost-management-billing/index.yml)használatával teheti meg. Navigáljon a [Azure Portal](https://portal.azure.com) **előfizetésekhez** , és válassza a **Cost Analysis** lehetőséget. 

Használja a **halmozott költségek** lehetőséget, majd az erőforrástípus szerint szűrje az **értéket** `microsoft.sql/managedinstances` .

**Mennyibe kerül az automatizált biztonsági mentés?**

A biztonsági mentési tárolóhelyek mennyiségét a megvásárolt fenntartott tárterületnek megfelelően kapja meg, a biztonsági másolatok megőrzési időszaka beállítástól függetlenül. Ha a biztonsági mentési tárhely a lefoglalt ingyenes biztonsági mentési tárolóhelyen belül van, akkor a felügyelt példányok automatizált biztonsági mentései nem lesznek további költségek, ezért díjmentes. A biztonsági mentési tár a szabad terület feletti használatának meghaladása esetén az USA-beli régiókban körülbelül $0,20 – $0,24 GB/hó összegű költséget eredményez, vagy a régió részleteiért tekintse meg a díjszabási oldalt. További részletekért lásd: a [biztonsági másolatok tárolási felhasználásának magyarázata](https://techcommunity.microsoft.com/t5/azure-sql-database/backup-storage-consumption-on-managed-instance-explained/ba-p/1390923).

**Hogyan figyelhető meg a biztonsági mentési tárhely-felhasználás számlázási díja?**

A biztonsági mentési tár költségeit a Azure Portal használatával figyelheti. Útmutatásért lásd: [az automatikus biztonsági mentések költségeinek figyelése](../database/automated-backups-overview.md?tabs=managed-instance#monitor-costs). 

**Hogyan optimalizálható a biztonsági másolatok tárolási költségei a felügyelt példányon?**

A biztonsági másolatok tárolási költségeinek optimalizálásához tekintse [meg a biztonsági mentés finomhangolása az SQL felügyelt példányon](https://techcommunity.microsoft.com/t5/azure-sql-database/fine-tuning-backup-storage-costs-on-managed-instance/ba-p/1390935)című témakört.

## <a name="cost-saving-use-cases"></a>Költségtakarékos használati esetek

**Hol találom a használati eseteket és a költségmegtakarítást az SQL felügyelt példányával?**

SQL felügyelt példányok esettanulmányai:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

Ha jobban meg szeretné ismerni az Azure SQL felügyelt példányának üzembe helyezéséhez kapcsolódó előnyöket, költségeket és kockázatokat, akkor is van egy Forrester-tanulmány: [Microsoft Azure SQL Database felügyelt példány teljes gazdasági hatása](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).

## <a name="password-policy"></a>Jelszóházirend 

**Milyen jelszavas szabályzatok vonatkoznak az SQL felügyelt példány SQL-bejelentkezésekre?**

Az SQL-bejelentkezések SQL-alapú felügyelt példányának jelszavas szabályzata örökli a felügyelt példányt tároló virtuális fürtöket alkotó virtuális gépekre alkalmazott Azure platform-házirendeket. Jelenleg nem lehet módosítani ezeket a beállításokat, mivel ezeket a beállításokat az Azure definiálja, és a felügyelt példány örökli.

 > [!IMPORTANT]
 > Az Azure platform megváltoztathatja a házirendre vonatkozó követelményeket anélkül, hogy a szolgáltatásokat a szabályzatokra támaszkodva értesíti.

**Mik a jelenlegi Azure platform-szabályzatok?**

Minden bejelentkezéskor be kell állítania a jelszavát a bejelentkezés után, és módosítania kell a jelszavát, miután elérte a maximális kort.

| **Szabályzat** | **Biztonsági beállítás** |
| --- | --- |
| Jelszó maximális kora | 42 nap |
| Jelszó minimális kora | 1 nap |
| Jelszó minimális hossza | 10 karakter |
| A jelszónak meg kell felelnie a bonyolultsági követelményeknek | Engedélyezve |

**Le lehet tiltani a jelszó bonyolultságát és a lejáratot az SQL felügyelt példányain a bejelentkezési szinten?**

Igen, a bejelentkezési szinten CHECK_POLICY és CHECK_EXPIRATION mezőket is szabályozhatja. Az aktuális beállításokat a következő T-SQL-parancs végrehajtásával tekintheti meg:

```sql
SELECT *
FROM sys.sql_logins
```

Ezt követően a következő végrehajtásával módosíthatja a megadott bejelentkezési beállításokat:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(cserélje le a "test" értéket a kívánt bejelentkezési névvel, és állítsa be a szabályzatot és a lejárati értékeket)


## <a name="service-updates"></a>Szolgáltatási hírek

**Mi a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ változása Azure SQL Database & SQL felügyelt példánynál?**

Lásd: [Azure SQL Database & SQL felügyelt példányának tanúsítvány-elforgatása](../updates/ssl-root-certificate-expiring.md). 

**Mi az SQL felügyelt példányának tervezett karbantartási eseménye?**

Lásd: [Az Azure karbantartási eseményeinek tervezése az SQL felügyelt példányában](../database/planned-maintenance.md). 


## <a name="azure-feedback-and-support"></a>Azure-visszajelzés és-támogatás

**Honnan hagyhatom el az SQL felügyelt példányok tökéletesítésével kapcsolatos ötleteket?**

Szavazzon egy új felügyelt példány-szolgáltatásra, vagy tegyen fel új fejlesztési elképzelést az [SQL felügyelt példányok visszajelzési fórumának](https://feedback.azure.com/forums/915676-sql-managed-instance)szavazásakor. Ily módon hozzájárulhat a termék fejlesztéséhez, és segítheti a lehetséges fejlesztések rangsorolását.

**Hogyan hozhatok létre Azure-támogatási kérést?**

Az Azure-támogatási kérelem létrehozásáról az Azure- [támogatási kérelem létrehozása](../../azure-portal/supportability/how-to-create-azure-support-request.md)című témakörben olvashat bővebben.
