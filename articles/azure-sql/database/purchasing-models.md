---
title: Vásárlási modellek
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Ismerje meg a Azure SQL Database és az Azure SQL felügyelt példányaihoz elérhető vásárlási modelleket.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/28/2020
ms.openlocfilehash: 8883263d6ddb2fb8ddc809f464288fcd282531bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92788825"
---
# <a name="choose-between-the-vcore-and-dtu-purchasing-models---azure-sql-database-and-sql-managed-instance"></a>Választás a virtuális mag és a DTU beszerzési modelljei közül – Azure SQL Database és az SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure SQL Database és az Azure SQL felügyelt példányaival egyszerűen vásárolhat egy teljes körűen felügyelt, szolgáltatásként nyújtott platformot, amely megfelel a teljesítménynek és a költséghatékonyságnak. A Azure SQL Database kiválasztott üzemi modelltől függően kiválaszthatja az Önnek megfelelő vásárlási modellt:

- [Virtual Core (virtuális mag)-alapú beszerzési modell](service-tiers-vcore.md) (ajánlott). Ez a vásárlási modell a kiépített számítási szintek és a kiszolgáló nélküli számítási szintek közötti választást biztosítja. A kiépített számítási szinten kiválaszthatja a számítási erőforrások pontos mennyiségét, amelyek mindig kiépítve vannak a munkaterheléshez. A kiszolgáló nélküli számítási szinten megadhatja a számítási erőforrások automatikus skálázását egy konfigurálható számítási tartományon. Ezzel a számítási szinten automatikusan szüneteltetheti és folytathatja az adatbázist a munkaterhelés-tevékenység alapján. A virtuális mag egység ára (egységenként) alacsonyabb a kiépített számítási szinten, mint a kiszolgáló nélküli számítási szinten.
- [Adatbázis-tranzakciós egység (DTU) alapú beszerzési modell](service-tiers-dtu.md). Ez a beszerzési modell a közös számítási feladatokhoz kiegyensúlyozott, kötegelt számítási és tárolási csomagokat biztosít.

Két beszerzési modell létezik:

- a [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md) a [Azure SQL Database](sql-database-paas-overview.md) és az [Azure SQL felügyelt példányaihoz](../managed-instance/sql-managed-instance-paas-overview.md)egyaránt elérhető. A [nagy kapacitású szolgáltatási réteg](service-tier-hyperscale.md) a [virtuális mag-alapú vásárlási modellt](service-tiers-vcore.md)használó önálló adatbázisokhoz érhető el.
- A [DTU-alapú vásárlási modell](service-tiers-dtu.md) a [Azure SQL Database](single-database-manage.md)számára érhető el.

A következő táblázat és diagram hasonlítja össze a virtuális mag-alapú és a DTU-alapú vásárlási modelleket:

|**Vásárlási modell**|**Leírás**|**A következőkre alkalmas**|
|---|---|---|
|DTU-alapú|Ez a modell a számítási, tárolási és I/O-erőforrások kötegelt mérőszámán alapul. A számítási méretek az önálló adatbázisok esetében adatbázis-tranzakciós egységben (DTU), a rugalmas készletek esetében pedig rugalmas adatbázis-tranzakciós egységben (eDTU) vannak megadva. További információ a DTU-kkal és eDTU-kkal kapcsolatban: [Mik azok a DTU-k és eDTU-k?](purchasing-models.md#dtu-based-purchasing-model)|Azok az ügyfelek, akik egyszerű, előre konfigurált erőforrás-beállításokat szeretnének|
|Virtuális mag-alapú|Ez a modell lehetővé teszi a számítási és tárolási erőforrások egymástól független kiválasztását. A virtuálismag-alapú vásárlási modell lehetővé teszi az [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) SQL Serverrel való használatát a költségek megtakarítása érdekében.|Rugalmasságot, irányítást és átlátszóságot biztosító ügyfelek|
||||  

![Díjszabási modell összehasonlítása](./media/purchasing-models/pricing-model.png)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="compute-costs"></a>Számítási költségek

### <a name="provisioned-compute-costs"></a>Kiépített számítási költségek

A kiépített számítási szinten a számítási díj az alkalmazás számára kiépített teljes számítási kapacitást tükrözi.

A üzletileg kritikus szolgáltatási szinten a rendszer automatikusan legalább három replikát foglal le. A számítási erőforrások ezen további kiosztásának megfelelően a virtuális mag-alapú vásárlási modell ára körülbelül 2,7-szor magasabb a üzletileg kritikus szolgáltatási szinten, mint a általános célú szolgáltatási szinten. Hasonlóképpen, a üzletileg kritikus szolgáltatási szinten lévő GB-nál nagyobb tárolási díj a magasabb i/o-korlátokat és az SSD-tároló alacsonyabb késését tükrözi.

A biztonsági mentési tár díja ugyanaz, mint a üzletileg kritikus szolgáltatási szint és a általános célú szolgáltatási szint esetében, mivel mindkét szint szabványos tárolót használ a biztonsági mentésekhez.

### <a name="serverless-compute-costs"></a>Kiszolgáló nélküli számítási költségek

A számítási kapacitás meghatározásának és a költségeknek a kiszolgáló nélküli számítási szinten való kiszámításának leírását lásd: [SQL Database kiszolgáló](serverless-tier-overview.md)nélküli szinten.

## <a name="storage-costs"></a>Tárolási költségek

A különböző típusú tárolók számlázása másképp történik. Az adattárolás esetében a kiosztott tárterületért kell fizetnie, amely a kiválasztott adatbázis vagy készlet maximális mérete alapján történik. A díj nem változik, hacsak nem csökkenti vagy nem emeli a maximális értéket. A biztonsági mentési tár a példányának automatizált biztonsági másolatához van társítva, és dinamikusan van lefoglalva. A biztonsági mentés megőrzési időtartamának növelése növeli a példány által felhasznált biztonsági mentési tárterületet.

Alapértelmezés szerint a rendszer az adatbázisok hét napos automatikus biztonsági mentését másolja át egy olvasási hozzáférésű geo-redundáns tárolási (RA-GRS) standard blob Storage-fiókba. Ezt a tárolót a heti teljes biztonsági mentések, a napi különbözeti biztonsági másolatok, valamint a tranzakciós naplók biztonsági másolatai használják, amelyek öt percenként másolódnak át. A tranzakciós naplók mérete az adatbázis változásának mennyiségétől függ. Az adatbázis méretének 100%-ának megfelelő minimális tárterület külön díj nélkül elérhető. A biztonsági mentési tár további felhasználásának díja GB/hó.

A tárolási díjszabással kapcsolatos további információkért tekintse meg az [árképzést](https://azure.microsoft.com/pricing/details/sql-database/single/) ismertető oldalt.

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

A virtuális mag (virtuális mag) a logikai CPU-t jelöli, és lehetőséget nyújt a hardver és a hardver fizikai jellemzőinek (például a magok, a memória és a tárterület méretének) a kiválasztására. A virtuális mag-alapú vásárlási modell rugalmasságot, irányítást, átláthatóságot biztosít az egyes erőforrások fogyasztása terén, és egy egyszerű módszert kínál a helyszíni munkaterhelés-követelmények felhőbe való lefordítására. Ez a modell lehetővé teszi a számítási, a memória-és a tárolási erőforrások kiválasztását a munkaterhelés igényei alapján.

A virtuális mag-alapú vásárlási modellben az SQL Database és az SQL felügyelt példányának [általános célú](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability) és [üzletileg kritikus](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) szolgáltatási szintjei közül választhat.  Önálló adatbázisok esetén a [nagy kapacitású szolgáltatási szintet](service-tier-hyperscale.md)is kiválaszthatja.

A virtuális mag-alapú vásárlási modell lehetővé teszi a számítási és tárolási erőforrások egymástól független kiválasztását, a helyszíni teljesítmény egyeztetését és az árak optimalizálását. A virtuális mag-alapú vásárlási modellben a következőket kell fizetnie:

- Számítási erőforrások (a szolgáltatásszint + a virtuális magok száma és a memória mennyisége + hardvergeneráció).
- Az adatok típusa és mennyisége, valamint naplótárolás.
- Biztonsági mentési tár (RA-GRS).

> [!IMPORTANT]
> A számítási erőforrások, az I/O-műveletek, valamint az adatok és a naplók tárolása adatbázis vagy rugalmas készlet alapján történik. A biztonsági mentési tárterületet minden adatbázis alapján számítjuk fel. További információ az SQL felügyelt példányának díjairól: [SQL felügyelt példány](../managed-instance/sql-managed-instance-paas-overview.md).
> **Régióra vonatkozó korlátozások:** A támogatott régiók aktuális listájáért lásd: [régiók által elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Ha felügyelt példányt szeretne létrehozni olyan régióban, amely jelenleg nem támogatott, [küldjön egy támogatási kérést a Azure Portalon keresztül](quota-increase-request.md).

Ha az adatbázis több mint 300 DTU használ, akkor a virtuális mag-alapú vásárlási modellre való áttérés csökkentheti a költségeket. A konverziót a választható API-k használatával vagy a Azure Portal használatával végezheti el állásidő nélkül. Azonban a konverzió nem szükséges, és nem történik meg automatikusan. Ha a DTU-alapú vásárlási modell megfelel a teljesítményre és az üzleti követelményekre, akkor továbbra is használja.

A DTU-alapú vásárlási modellről a virtuális mag-alapú vásárlási modellre való áttérésért lásd: [áttelepítés a DTU-ről a virtuális mag-re](migrate-dtu-to-vcore.md).

## <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

Az adatbázis-tranzakciós egység (DTU) a processzor, a memória, az olvasás és az írás kevert mértékét jelöli. A DTU-alapú vásárlási modell a számítási erőforrások előre konfigurált kötegeit és az alkalmazások különböző szintjeinek megadását is biztosítja. Ha az előre konfigurált kötegek és a havonta rögzített kifizetések egyszerűségét részesíti előnyben, a DTU-alapú modell megfelelőbb lehet az Ön igényeinek megfelelően.

A DTU-alapú vásárlási modellben választhat a Azure SQL Database alapszintű, standard és prémium szolgáltatási szintjei közül. A DTU-alapú vásárlási modell nem érhető el az Azure SQL felügyelt példányaihoz.

### <a name="database-transaction-units-dtus"></a>Adatbázis-tranzakciós egységek (DTU)

Az Azure a [szolgáltatási](single-database-scale.md)szinten meghatározott számítási méretekben egyetlen adatbázis esetében garantálja az adott adatbázishoz tartozó erőforrások bizonyos szintjét (amely független az Azure-felhőben található többi adatbázistól). Ez a garancia kiszámítható szintű teljesítményt nyújt. Az adatbázishoz lefoglalt erőforrások mennyisége DTU számú, és a számítási, tárolási és I/O-erőforrások kötegelt mérőszáma.

Az ilyen erőforrások arányát eredetileg egy [online tranzakció-feldolgozási (OLTP) teljesítményteszt-számítási](service-tiers-dtu.md) feladat határozza meg, amely a valós OLTP munkaterhelésekre jellemző. Ha a munkaterhelés meghaladja az ilyen erőforrások mennyiségét, az átviteli sebesség szabályozása megtörténik, ami lassabb teljesítményt és időtúllépést eredményez.

A munkaterhelés által használt erőforrások nem érintik az Azure-felhőben lévő más adatbázisok számára elérhető erőforrásokat. Hasonlóképpen, a más munkaterhelések által használt erőforrások nem érintik az adatbázis számára elérhető erőforrásokat.

![Határolókeret](./media/purchasing-models/bounding-box.png)

A DTU leghasznosabb a különböző számítási méretek és szolgáltatási szintek adatbázisai számára lefoglalt relatív erőforrások megismerése. Például:

- A DTU megkettőzése az adatbázis számítási méretének növelésével egyenlő az adatbázis számára elérhető erőforrások készletének megkettőzésével.
- A prémium szintű szolgáltatási szint P11-adatbázisa a 1750 DTU 350-szor több DTU számítási teljesítményt biztosít, mint az alapszintű szolgáltatási szint adatbázisa 5 DTU.  

A számítási feladatok erőforrás-(DTU-) felhasználásának mélyebb megismeréséhez használja a [lekérdezési teljesítmény](query-performance-insight-use.md) elemzését a következőre:

- A processzor/időtartam/végrehajtás száma alapján azonosíthatja a leggyakoribb lekérdezéseket, amelyek a jobb teljesítmény érdekében megadhatók. Az I/O-igényes lekérdezések például hasznosak lehetnek a [memórián belüli optimalizálási technikákban](../in-memory-oltp-overview.md) , hogy a rendelkezésre álló memória jobban használható legyen bizonyos szolgáltatási szinten és számítási méretekben.
- A lekérdezés részleteinek részletezése a szöveg és az erőforrás-használat előzményeinek megtekintéséhez.
- A [SQL Database Advisor](database-advisor-implement-performance-recommendations.md)által végrehajtott műveleteket bemutató teljesítmény-hangolási javaslatok elérése.

### <a name="elastic-database-transaction-units-edtus"></a>Rugalmas adatbázis-tranzakciós egységek (Edtu)

Olyan adatbázisok esetén, amelyek mindig elérhetők, és nem biztosítanak olyan dedikált erőforrásokat (DTU), amelyek esetleg nem mindig szükségesek, egy [rugalmas készletbe](elastic-pool-overview.md)helyezheti ezeket az adatbázisokat. A rugalmas készletben lévő adatbázisok egyetlen kiszolgálón találhatók, és megoszthatják az erőforrások készletét.

A rugalmas készletben lévő megosztott erőforrásokat rugalmas adatbázis-tranzakciós egységekkel (Edtu) mérjük. A rugalmas készletek egyszerű, költséghatékony megoldást biztosítanak a teljesítménybeli célok kezelésére több, széles körben változó és kiszámíthatatlan használati mintázattal rendelkező adatbázis esetében. A rugalmas készlet garantálja, hogy az összes erőforrást nem lehet felhasználni a készlet egyik adatbázisa, miközben gondoskodik arról, hogy a készletben lévő összes adatbázishoz mindig rendelkezésre álljon legalább egy szükséges erőforrás.

Egy készlethez megadott számú Edtu van megadva. A rugalmas készletben az egyes adatbázisok a beállított határokon belül is méretezhetők. A nehezebb terhelésű adatbázisok több Edtu használnak az igények kielégítése érdekében. Az öngyújtós terhelés alatt álló adatbázisok kevesebb Edtu fognak használni. A terhelés nélküli adatbázisok nem fognak Edtu használni. Mivel az erőforrások a teljes készlethez vannak kiépítve, nem pedig adatbázis helyett, a rugalmas készletek egyszerűbbé teszik a felügyeleti feladatokat, és kiszámítható költségvetést biztosítanak a készlet számára.

Hozzáadhat további Edtu az adatbázis-leállás nélküli meglévő készletekhez, és nem befolyásolhatja a készletben lévő adatbázisokat. Hasonlóképpen, ha már nincs szüksége további Edtu, távolítsa el őket egy meglévő készletből. Az adatbázisokat a készletből bármikor hozzáadhatók vagy kivonhatók. A Edtu más adatbázisokhoz való lefoglalásához korlátozza az adatbázisok nagy terhelés alatt használható Edtu számát. Ha egy adatbázis következetesen erőforrásokat használ, helyezze át a készletből, és konfigurálja egyetlen adatbázisként a szükséges erőforrások kiszámítható mennyiségével.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>A munkaterhelés által igényelt DTU számának meghatározása

Ha meglévő helyszíni vagy SQL Server virtuális gépek munkaterhelését szeretné áttelepíteni SQL Databasere, a [DTU-kalkulátor](https://dtucalculator.azurewebsites.net/) használatával közelítse meg a szükséges DTU számát. Meglévő SQL Database számítási feladatokhoz használja a [lekérdezési teljesítménnyel](query-performance-insight-use.md) kapcsolatos elemzéseket az adatbázis-erőforrás-felhasználás (DTU) megismeréséhez, és mélyebb elemzéseket kaphat a számítási feladatok optimalizálásához. A [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dinamikus felügyeleti nézet (DMV) lehetővé teszi az elmúlt órában az erőforrás-felhasználás megtekintését. Az [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) katalógus nézet az elmúlt 14 napban az erőforrás-felhasználást jeleníti meg, de az átlagosan öt perces átlagot biztosít.

### <a name="determine-dtu-utilization"></a>DTU kihasználtságának meghatározása

Egy adatbázis vagy egy rugalmas készlet DTU/eDTU-korlátjának (DTU/eDTU-kihasználtság) átlagos százalékos arányának meghatározásához használja a következő képletet:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

A képlet bemeneti értékei [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)és [sys.elastic_pool_resource_stats](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMV szerezhetők be. Más szóval, a DTU/eDTU-kihasználtság százalékos arányának meghatározása egy adatbázis vagy egy rugalmas készlet DTU/eDTU-korlátja felé, a legnagyobb százalékos értéket kell választania a következők közül: `avg_cpu_percent` , `avg_data_io_percent` és `avg_log_write_percent` egy adott időpontban.

> [!NOTE]
> Az adatbázisok DTU-korlátját CPU, olvasás, írás és memória határozza meg az adatbázis számára. Mivel azonban a SQL Database motor általában az adatgyorsítótárhoz tartozó összes rendelkezésre álló memóriát használja a teljesítmény javítása érdekében, az `avg_memory_usage_percent` érték általában 100 százalék lesz, az aktuális adatbázis-terheléstől függetlenül. Ezért annak ellenére, hogy a memória indirekt módon befolyásolja a DTU korlátot, a rendszer nem használja a DTU-kihasználtsági képletben.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Rugalmas készletből kihasználható munkaterhelések

A készletek kiválóan alkalmasak az alacsony erőforrás-kihasználtságú és viszonylag ritka kihasználtsági tüskéket tartalmazó adatbázisok számára. További információ: Mikor érdemes [SQL Database rugalmas készletet figyelembe venni?](elastic-pool-overview.md)

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Hardveres generációk a DTU-alapú vásárlási modellben

A DTU-alapú vásárlási modellben az ügyfelek nem választhatják ki az adatbázisaikban használt hardver-generációt. Habár egy adott adatbázis hosszú ideje (általában több hónapig) egy adott hardveres generáción marad, bizonyos események miatt előfordulhat, hogy az adatbázisok áthelyezhetők egy másik hardveres generációba.

Egy adatbázis például áthelyezhető más hardveres generációba, ha azt egy másik szolgáltatási célnak megfelelően felhasználják, vagy ha egy adatközpontban az aktuális infrastruktúra megközelíti a kapacitási korlátokat, vagy ha a jelenleg használt hardvert az élettartama miatt leszerelik.

Ha egy adatbázist más hardverre helyez át, a számítási feladatok teljesítménye változhat. A DTU-modell garantálja, hogy a [DTU teljesítményteszt](./service-tiers-dtu.md#dtu-benchmark) számítási feladatának átviteli sebessége és válaszideje lényegesen azonos marad, mivel az adatbázis egy másik hardveres generációra kerül át, feltéve, hogy a szolgáltatási cél (a DTU száma) ugyanaz marad.

Azonban a Azure SQL Database futó ügyfelek számítási feladatainak széles spektrumán az is előfordulhat, hogy a különböző hardverek ugyanazon szolgáltatási célkitűzéshez való használatának következményei is erősebbek. A különböző számítási feladatok előnyeit a különböző hardverkonfiguráció és funkciók teszik ki. Ezért a DTU-teljesítményteszttől eltérő számítási feladatoknál láthatja a teljesítménybeli különbségeket, ha az adatbázis az egyik hardveres generációról a másikra lép.

Például a hálózati késésre érzékeny alkalmazások jobb teljesítményt láthatnak a Gen5 hardveren és a Gen4-ben, a gyorsított hálózatkezelés a Gen5-ben való használata miatt, de az intenzív olvasási IO-t használó alkalmazások jobb teljesítményt biztosíthatnak a Gen4 hardver és a Gen5 miatt, mivel a Gen4 nagyobb memória érhető el.

Azok az ügyfelek, akik a hardveres változásokra érzékeny munkaterhelésekkel vagy olyan ügyfelekkel foglalkoznak, akik szeretnék szabályozni a hardveres létrehozást a saját adatbázisaik számára, a [virtuális mag](service-tiers-vcore.md) modell használatával választhatják ki az előnyben részesített hardveres generációkat az adatbázisok létrehozásakor Az egyes virtuális mag-modellekben az egyes szolgáltatási célok erőforrás-korlátozásai dokumentálva vannak, mind az [önálló adatbázisok](resource-limits-vcore-single-databases.md) , mind a [rugalmas készletek](resource-limits-vcore-elastic-pools.md)esetében. A virtuális mag-modellben található hardveres generációkkal kapcsolatos további információkért lásd: [Hardware Generations](./service-tiers-vcore.md#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Szükség van-e az alkalmazás offline állapotba való átalakítására egy DTU-alapú szolgáltatási rétegről egy virtuális mag-alapú szolgáltatási rétegre?

Nem. Nem kell offline állapotba hoznia az alkalmazást. Az új szolgáltatási rétegek egy egyszerű online átalakítási módszert kínálnak, amely hasonló a standard és a prémium szolgáltatási szint adatbázisainak frissítéséhez, és fordítva. A konverziót a Azure Portal, a PowerShell, az Azure CLI, a T-SQL vagy a REST API használatával indíthatja el. Lásd az [önálló adatbázisok kezelését](single-database-scale.md) és a [rugalmas készletek kezelését](elastic-pool-overview.md) ismertető részt.

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Átválthatok egy adatbázist a virtuális mag-alapú vásárlási modellben egy szolgáltatási rétegre a DTU-alapú vásárlási modellben?

Igen, a Azure Portal, a PowerShell, az Azure CLI, a T-SQL vagy a REST API használatával könnyedén átalakíthatja az adatbázist bármely támogatott teljesítménybeli célkitűzésre. Lásd az [önálló adatbázisok kezelését](single-database-scale.md) és a [rugalmas készletek kezelését](elastic-pool-overview.md) ismertető részt.

## <a name="next-steps"></a>Következő lépések

- A virtuális mag-alapú vásárlási modellel kapcsolatos további információkért lásd: [virtuális mag-alapú vásárlási modell](service-tiers-vcore.md).
- A DTU-alapú vásárlási modellel kapcsolatos további információkért lásd: [DTU-alapú vásárlási modell](service-tiers-dtu.md).