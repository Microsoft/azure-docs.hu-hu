---
title: Vásárlási modellek az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg a vásárlási modellek modell, amelyek az elérhető adatbázisok az Azure SQL Database szolgáltatásban.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/19/2018
ms.openlocfilehash: 9dc993b6ba9b90dfa61660df004ef7dae8b93fa7
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49470954"
---
# <a name="azure-sql-database-purchasing-models"></a>Az Azure SQL Database vásárlási modellek

Az Azure SQL Database lehetővé teszi, hogy teljes körűen felügyelt PaaS adatbázismotor, válassza ki a teljesítménnyel és költségekkel igényeinek megfelelő könnyű vásárlás. Az Azure SQL Database üzembe helyezési modelltől, függően válassza ki a vásárlási modell, amely a legjobban az igényeinek:

- [Logikai kiszolgálók](sql-database-logical-servers.md) a [Azure SQL Database](sql-database-technical-overview.md) számítási, tárolási és i/o-erőforrások két vásárlási modellt kínál: egy [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md) és a egy [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md). A beszerzési modell választhat [önálló adatbázisok](sql-database-single-databases-manage.md) vagy [rugalmas készletek](sql-database-elastic-pool.md).
- [Felügyelt példányok](sql-database-managed-instance.md) az egyetlen Azure SQL Database-ajánlatok a [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md).

> [!IMPORTANT]
> [Nagy kapacitású adatbázisok (előzetes verzió)](sql-database-service-tier-hyperscale.md) csak az önálló adatbázisok használata a virtuális mag modell megvásárlása nyilvános előzetes verzióban érhetők el.

Az alábbi táblázatos és hasonlítsa össze két vásárlási modell.

|**Beszerzési modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell csomagolt méri, számítási, tárolási és i/o-erőforrások alapján. A számítási méret az önálló adatbázisok adatbázis-tranzakciós egységek (dtu-k) és a rugalmas Database Transaction Unitok (Edtu) rugalmas készletek vannak kifejezve. További információ a dtu-król és Edtu-: [Mik a dtu-król és edtu-k](sql-database-service-tiers.md#dtu-based-purchasing-model)?|A legjobb erőforrás egyszerű, előre konfigurált beállítások használni.|
|vCore-alapú modell|Ez a modell lehetővé teszi a számítási és tárolási erőforrások egymástól függetlenül kiválasztását. Lehetővé teszi az SQL Server Azure Hybrid Benefit használatával költséget takaríthat meg.|Legjobb ügyfelek esetében, akik a rugalmasságot, a szabályozhatóság, és értékét.|
||||  

![díjszabási modell](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell

Egy virtuális magot és hardver generációja és fizikai jellemzők hardver (például a magok, memória, tároló mérete száma) közül választhat lehetőség elérhető logikai CPU jelöli. A Virtuálismag-alapú vásárlási modell biztosítja a rugalmasságot, a vezérlő, az egyéni erőforrás-használat átláthatósága és közvetlen módon lefordítani a helyszíni tevékenységprofil követelményeinek a felhőben. Ez a modell lehetővé teszi a számítási, memória és a tárolási számítási feladatok igényeik alapján kiválaszthatja. A Virtuálismag-alapú vásárlási modell, választhat [általános célú](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) és [üzletileg kritikus](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) szolgáltatásszintek egyaránt [önálló adatbázisok](sql-database-single-database-scale.md), [ felügyelt példányok](sql-database-managed-instance.md), és [rugalmas készletek](sql-database-elastic-pool.md). Az önálló adatbázisokhoz is választhat a [nagy kapacitású (előzetes verzió)](sql-database-service-tier-hyperscale.md) szolgáltatási szint.

A Virtuálismag-alapú vásárlási modell lehetővé teszi, hogy egymástól függetlenül válassza ki a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár. A Virtuálismag-alapú vásárlási modell, az ügyfeleknek kell fizetniük:

- COMPUTE (szolgáltatási szint + virtuális magok száma és memória + hardvertől mennyisége)
- Típus és az adat- és naplófájlok tárterület mérete
- Biztonsági mentési tárolás (RA-GRS)

> [!IMPORTANT]
> Számítási, IOs-, adat, és a naplók tárolásához díját adatbázis vagy a rugalmas készletet. Biztonsági másolatok tárolási számlázása az egyes adatbázisokhoz. További részleteket a felügyelt példány díjak, [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md).
> **Régió korlátozások:** a Virtuálismag-alapú vásárlási modell még nem áll rendelkezésre a következő régiókban: Nyugat-Európa, közép-Franciaország, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója és Délkelet-Ausztrália.

Ha az adatbázis vagy a rugalmas készlet dtu-k több mint 300 átalakítása virtuális mag fogyaszt csökkentheti költségeit. A választott API-val vagy az Azure Portallal, leállás nélkül konvertálhatja. Azonban átalakítás, nem szükséges. Ha a DTU-vásárlási modell felel meg a teljesítmény- és üzleti követelményeinek, akkor továbbra is használja azt. Ha úgy dönt, hogy a DTU-modellből átalakítása virtuális mag – modell, választhat a számítási méret használatával a következő tapasztalatok: minden egyes 100 DTU standard szintű csomag szükséges általános célú csomagban; legalább 1 virtuális mag minden egyes 125 DTU prémium szintű üzletileg kritikus legalább 1 virtuális mag szükséges.

## <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

Az adatbázis tranzakciós egység (DTU) a CPU összesített mérésén jelöli, memória, olvas és ír. A DTU-alapú vásárlási modell előre konfigurált kötegek számítási erőforrások olyan készletét kínálja, és belefoglalt tárterület teljesítményszintek különböző alkalmazásteljesítmény. Ügyfelek, akik inkább az egyszerűség, rögzített nagyságú befizetések és a egy előre konfigurált csomag havonta, előfordulhat, hogy keresse meg a DTU-alapú modell jobban megfelel az igényeiknek. A DTU-alapú vásárlási modell, az ügyfelek választhatnak, hogy **alapszintű**, **Standard**, és **prémium** szolgáltatásszintek egyaránt [önálló adatbázisok](sql-database-single-database-scale.md) és [rugalmas készletek](sql-database-elastic-pool.md). A beszerzési modell nem érhető el a [felügyelt példányai](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Adatbázis-tranzakciós egységek (Dtu)

Egyetlen Azure SQL-adatbázishoz egy adott számítási méret belül egy [szolgáltatásszint](sql-database-single-database-scale.md), így előre jelezhető garantálja a Microsoft bizonyos fokú erőforrásokat az adatbázishoz (amely független az Azure-felhőben más adatbázisok), teljesítmény szintjét. Erőforrás-mennyiséget egy adatbázis-tranzakciós egységek vagy a dtu-k számát számítjuk, és a egy csomagolt számítási, tárolási és i/o-erőforrások mértékegysége. E három erőforrás aránya eredetileg által meghatározott egy [OLTP-számításifeladat módszertanával](sql-database-benchmark-overview.md), tervezett, amely valós OLTP számítási feladatok. Ha a számítási feladat a következő erőforrások mennyisége meghaladja, az átviteli sebesség, szabályozott – a lassabb teljesítménye és időtúllépések eredményül kapott. A számítási feladatok által használt erőforrások nem érinti a többi SQL-adatbázisok Azure-felhőszolgáltatás számára elérhető erőforrások és egyéb számítási feladatok által használt erőforrások nem érinti az SQL-adatbázis számára elérhető erőforrások.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu-k leghasznosabbak megértéséhez a relatív különböző számítási méretekre: Azure SQL Database-adatbázisok és a szolgáltatási szintek közötti erőforrás-mennyiséggel. Ha például a Dtu megduplázása egy adatbázis számítási méretének növelését-adatbázisnak felel meg az adatbázis számára elérhető erőforrások készletét kétszeresére. Például egy 1750 DTU-val rendelkező Prémium P11 adatbázis 350x több DTU számítási teljesítményt nyújt, mint egy 5 DTU-val rendelkező Alapszintű adatbázis.  

Mélyebb betekintést nyerhet a (DTU) erőforrás-használat, a számítási feladat, használja a [Azure SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) való:

- A leggyakoribb lekérdezések azonosíthatja a CPU/időtartama/végrehajtások száma, amely potenciálisan hangolásával a jobb teljesítmény érdekében. Például az intenzív i/o-lekérdezések használata előnyös lehet [memórián belüli optimalizálási technikákat](sql-database-in-memory.md) , hogy jobban kihasználja a rendelkezésre álló memória, egy adott szolgáltatásszintet, a számítási méret.
- A lekérdezés részleteinek feltárásához, megtekintheti az szöveg- és erőforrás-használat előzményeit.
- Hozzáférés teljesítmény-finomhangolási ajánlásait, amely által végrehajtott műveletek megjelenítése [az SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Rugalmas Database Transaction Unitok (edtu-k)

Helyett adja meg a dedikált erőforráskészletek (dtu-k), előfordulhat, hogy nem mindig lehet szükséges, amely mindig elérhető SQL-adatbázishoz, mint helyezheti őket egy [rugalmas készlet](sql-database-elastic-pool.md) egy SQL Database-kiszolgálón, amely erőforráskészletekbe szervezhető között megosztja Ezeket az adatbázisokat. A rugalmas készletek megosztott erőforrásainak rugalmas adatbázis-tranzakciós egységek vagy edtu-k vannak megadva. Rugalmas készletek több adatbázis, amelyek felhasználási módja nagy mértékben és kiszámíthatatlanul teljesítménybeli céljainak kezelésére egy egyszerű és költséghatékony megoldást kínálnak. Rugalmas készlet garantálja, hogy a készletben egy adatbázis nem felhasznált erőforrásokat, amíg a készletben lévő minden egyes adatbázis biztosítása minden esetben rendelkezik egy minimálisan szükséges erőforrások rendelkezésre.

A készlethez adott számú edtu-k rögzített áron. A rugalmas készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek a konfigurált határok között. Egy adatbázis nagyobb terhelés alatt további edtu-t, igény szerint fog felhasználni. Világosabb terhelések adatbázisoknak kevesebb edtu-k. Semmilyen terhelést az adatbázisok edtu-k nem használnak fel. Az erőforrásoknak a teljes készlet számára, helyett adatbázisonként, felügyeleti feladatok egyszerűsítettek, így kiszámítható költségekkel a készlet.

A meglévő készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a meglévő készletekből. Adhat hozzá vagy kivonása a készlethez adatbázisok vagy korlátot az edtu-k egy adatbázis mennyiségét használatával nagy terhelés alatt fenntartott edtu-kat más adatbázisok számára. Ha egy adatbázist a kiszámítható erőforrásokat, kiveheti a készlet szabadon azt konfigurálja a szükséges erőforrások kiszámítható mennyiségű egy önálló adatbázist.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>A munkaterhelés szerint szükséges dtu-k számának meghatározása

Ha meglévő helyszíni vagy SQL-kiszolgálói virtuális gépeken futó számítási feladatokat kíván áthelyezni az Azure SQL Database-be, a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges DTU-k számát. Használhat meglévő Azure SQL Database számítási feladat [SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) tudni, hogy az adatbázis erőforrás-használat (dtu-k) mélyebb betekintést a számítási feladatok optimalizálásához. Is használhatja a [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV megtekintése az erőforrás-használat az elmúlt egy óra. Másik lehetőségként a katalógusnézet [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) erőforrás-használat az elmúlt 14 napra vonatkozó, de átlagosan öt perces átlagolással jeleníti meg.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Számítási feladatok, amelyek rugalmas készletek az erőforrások

A készleteket nagy számú, speciális felhasználási mintákkal rendelkező adatbázishoz tervezték. Egy adott adatbázis esetében ez a minta egy viszonylag rendszertelen kiugró kihasználtság jellemzi az alacsony kihasználtságú átlagos jellemzi. Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. További információkért lásd: [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool.md)

## <a name="next-steps"></a>További lépések

- Virtuálismag-alapú vásárlási modell, lásd: [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)
- A DTU-alapú vásárlási modell, lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
