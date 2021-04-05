---
title: Migrálás DTU-ról virtuálismag-alapú modellre
description: Áttelepíthet egy adatbázist Azure SQL Database a DTU modellből a virtuális mag modellbe. A virtuális mag-re való Migrálás hasonló a standard és a prémium szint közötti verziófrissítéshez vagy visszalépéshez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361655"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Azure SQL Database migrálása a DTU-alapú modellből a virtuális mag-alapú modellbe
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez a cikk azt ismerteti, hogyan telepítheti át az adatbázist Azure SQL Database a [DTU-alapú vásárlási modellből](service-tiers-dtu.md) a [virtuális mag-alapú vásárlási modellbe](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Adatbázisok migrálása

A DTU-alapú vásárlási modellből a virtuális mag-alapú vásárlási modellbe való Migrálás hasonló az alapszintű, standard és prémium szintű szolgáltatási célkitűzések közötti skálázáshoz, amely hasonló [időtartamot](single-database-scale.md#latency) és [minimális állásidőt](scale-resources.md) biztosít az áttelepítési folyamat végén. A virtuális mag-alapú vásárlási modellbe áttelepített adatbázisok bármikor áttelepíthetők a DTU-alapú vásárlási modellbe, a [nagy kapacitású](service-tier-hyperscale.md) szolgáltatási szintjére áttelepített adatbázisok kivételével. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>A virtuális mag szolgáltatási szintjeinek és szolgáltatási céljának kiválasztása

Az alapszintű és a standard szintű szolgáltatási csomagokban a legtöbb DTU virtuális mag az áttelepítési forgatókönyvek, az adatbázisok és a rugalmas készletek a [általános célú](service-tier-general-purpose.md) szolgáltatási szintjére lesznek leképezve. A prémium szintű szolgáltatási szinten lévő adatbázisok és rugalmas készletek a [üzletileg kritikus](service-tier-business-critical.md) szolgáltatási szintre lesznek leképezve. Az alkalmazási forgatókönyvtől és a követelményektől függően a [nagy kapacitású](service-tier-hyperscale.md) szolgáltatási réteg gyakran használható áttelepítési célként az önálló adatbázisok számára az összes DTU szolgáltatási szinten.

A szolgáltatási cél vagy a számítási méret kiválasztásához az áttelepített adatbázishoz a virtuális mag modellben egyszerű, de hozzávetőleges szabályt használhat: az alapszintű vagy standard csomagokban 100 DTU *legalább* 1 virtuális mag kell lennie, és a prémium szint 125 DTU *legalább* 1 virtuális mag kell lennie. 

> [!TIP]
> Ez a szabály közelítő, mert nem veszi figyelembe a DTU-adatbázishoz vagy a rugalmas készlethez használt hardver-generációt. 

A DTU modellben az adatbázishoz vagy a rugalmas készlethez bármilyen elérhető [hardveres generáció](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) használható. Emellett a virtuális mag (logikai processzorok) számát csak közvetett módon szabályozhatja, ha magasabb vagy alacsonyabb DTU vagy eDTU értéket választ. 

A virtuális mag-modellel az ügyfeleknek explicit módon kell megadniuk a hardveres generációt és a virtuális mag (logikai processzorok) számát. A DTU modell nem kínálja ezeket a lehetőségeket, azonban a hardver létrehozása és az egyes adatbázisokhoz és rugalmas készletekhez használt logikai processzorok száma dinamikus felügyeleti nézeteken keresztül érhető el. Így pontosabban meghatározható a megfelelő virtuális mag-szolgáltatási cél. 

A következő módszer ezen információk alapján határozza meg a virtuális mag szolgáltatási célkitűzését, amely hasonló erőforrás-elosztással rendelkezik, hogy a virtuális mag-modellbe való Migrálás után hasonló szintű teljesítményt szerezzen.

### <a name="dtu-to-vcore-mapping"></a>DTU a virtuális mag-megfeleltetéshez

Az alábbi T-SQL-lekérdezés az áttelepíteni kívánt DTU-adatbázis környezetében való végrehajtásakor a a virtuális mag-modellben lévő egyes virtuális mag megfelelő (esetleg töredékes) számú értéket ad vissza. Ha ezt a számot az [adatbázisokhoz](resource-limits-vcore-single-databases.md) és [rugalmas készletekhez](resource-limits-vcore-elastic-pools.md) rendelkezésre álló legközelebb virtuális mag-számra kerekíti a virtuális mag modellben, az ügyfelek kiválaszthatják a DTU-adatbázishoz vagy rugalmas készlethez legközelebb eső virtuális mag szolgáltatás célját. 

Az ezt a módszert használó minta-áttelepítési forgatókönyveket a [példák](#dtu-to-vcore-migration-examples) szakasz ismerteti.

Ezt a lekérdezést az áttelepíteni kívánt adatbázis kontextusában hajtsa végre, nem pedig az `master` adatbázisban. Rugalmas készlet áttelepítésekor a lekérdezést a készletben lévő bármely adatbázis környezetében hajtsa végre.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>További tényezők

A virtuális mag (logikai processzorok) és a hardveres létrehozás mellett számos más tényező is befolyásolhatja a virtuális mag-szolgáltatási cél kiválasztását:

- A T-SQL-lekérdezés leképezése a DTU és a virtuális mag szolgáltatási célkitűzésekkel egyezik a CPU-kapacitásuk szempontjából, ezért az eredmények pontosabbak lesznek a PROCESSZORral kötött munkaterhelések esetében.
- Ugyanahhoz a hardveres létrehozáshoz, valamint a virtuális mag-adatbázisok esetében a IOPS és a tranzakciónaplók átviteli sebességére vonatkozó virtuális mag gyakran magasabbak, mint a DTU-adatbázisok esetében. Az IO-kötésű munkaterhelések esetében előfordulhat, hogy a virtuális mag-modellben lévő virtuális mag számát csökkenteni lehet a teljesítmény azonos szintjének elérése érdekében. Az abszolút értékekben lévő DTU-és virtuális mag-adatbázisok erőforrás-korlátai a [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézetben érhetők el. Ha összehasonlítja ezeket az értékeket az áttelepíteni kívánt DTU-adatbázis és egy virtuális mag-adatbázis között, amely egy körülbelül egyező szolgáltatási célkitűzést használ, a virtuális mag-szolgáltatás céljának pontosabb kiválasztását is segíti.
- A leképezési lekérdezés a DTU-adatbázis vagy a rugalmas készlet áttelepítéséhez használt memória mennyiségét, valamint a virtuális mag modell minden egyes hardveres generálását is visszaadja. A hasonló vagy magasabb teljes memória biztosítása a virtuális mag való áttelepítés után fontos a nagy mennyiségű adatgyorsítótárat igénylő munkaterhelések számára a megfelelő teljesítmény eléréséhez, vagy nagy mennyiségű memóriát igénylő munkaterhelések megkövetelése a lekérdezések feldolgozásához. Az ilyen számítási feladatokhoz a tényleges teljesítménytől függően szükség lehet a virtuális mag számának növelésére, hogy elegendő mennyiségű memóriát kapjon.
- A DTU-adatbázis [korábbi erőforrás-kihasználtságát](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) figyelembe kell venni a virtuális mag szolgáltatás céljának kiválasztásakor. A konzisztensen használt CPU-erőforrásokkal rendelkező DTU-adatbázisok esetében kevesebb virtuális mag van szükség a leképezési lekérdezés által visszaadott számnál. Ezzel szemben a DTU-adatbázisok esetében, ahol a következetesen magas CPU-kihasználtság miatt a nem megfelelő munkaterhelés-teljesítmény a lekérdezés által visszaadottnál nagyobb virtuális mag igényel.
- Ha időszakos vagy kiszámíthatatlan használati mintákkal rendelkező adatbázisokat telepít át, vegye figyelembe a [kiszolgáló](serverless-tier-overview.md) nélküli számítási szintek használatát. Vegye figyelembe, hogy a kiszolgáló nélküli egyidejű feldolgozók (kérelmek) maximális száma 75% a kiépített számítási korlát a beállított maximális virtuális mag-értéknél. Továbbá a kiszolgáló nélküli kiszolgálón elérhető maximális memória 3 GB-szor a beállított virtuális mag maximális száma; például a maximális memória 120 GB, ha 40 Max virtuális mag van konfigurálva.   
- A virtuális mag-modellben a támogatott maximális adatbázis-méret eltérő lehet a hardver-létrehozástól függően. Nagyméretű adatbázisok esetében a virtuális mag-modellben található támogatott maximális méretek közül a [különálló adatbázisok](resource-limits-vcore-single-databases.md) és a [rugalmas készletek](resource-limits-vcore-elastic-pools.md)esetében a következőt kell megnéznie:.
- A rugalmas készletek esetében a [DTU](resource-limits-dtu-elastic-pools.md) és a [virtuális mag](resource-limits-vcore-elastic-pools.md) modellek eltérést mutatnak a maximálisan támogatott adatbázisok száma alapján. Ezt figyelembe kell venni, amikor rugalmas készleteket telepít át sok adatbázissal.
- Előfordulhat, hogy egyes hardver-generációk nem érhetők el minden régióban. A rendelkezésre állást a [hardver generációi](service-tiers-vcore.md#hardware-generations)között tekintheti meg.

> [!IMPORTANT]
> A fenti Méretezési irányelvek virtuális mag DTU a célként megadott adatbázis-szolgáltatás céljának kezdeti becslésében nyújtanak segítséget.
>
> A céladatbázis optimális konfigurációja a munkaterhelés függ. Így a Migrálás után az optimális ár/teljesítmény arányt kell kihasználnia a virtuális mag-modell rugalmasságának kihasználásához, hogy beállítsa a virtuális mag, a [hardverek generálását](service-tiers-vcore.md#hardware-generations), a [szolgáltatás](service-tiers-vcore.md#service-tiers) és a [számítási](service-tiers-vcore.md#compute-tiers) rétegek számát, valamint más adatbázis-konfigurációs paraméterek finomhangolását, például a [maximális párhuzamossági fokot](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing).
> 

### <a name="dtu-to-vcore-migration-examples"></a>DTU – virtuális mag áttelepítési példák

> [!NOTE]
> Az alábbi példákban szereplő értékek csak illusztrációs célokat szolgálnak. A leírt helyzetekben visszaadott tényleges értékek eltérőek lehetnek.
> 

**Standard S9-adatbázis áttelepítése**

A leképezési lekérdezés a következő eredményt adja vissza (néhány oszlop nem jelenik meg a rövidség kedvéért):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|Gen5|5,40|16,800|7|24,000|5,05|

Láthatjuk, hogy a DTU-adatbázisnak 24 logikai processzora van (virtuális mag) 5,4, amely virtuális mag GB memóriával rendelkezik, és Gen5 hardvert használ. A közvetlen egyezés, amely egy általános célú 24 virtuális mag-adatbázis a Gen5-hardveren, azaz a **GP_Gen5_24** virtuális mag szolgáltatás célkitűzése.

**Standard S0-adatbázis áttelepítése**

A leképezési lekérdezés a következő eredményt adja vissza (néhány oszlop nem jelenik meg a rövidség kedvéért):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0,42|0,250|7|0,425|5,05|

Azt láthatjuk, hogy a DTU-adatbázis 0,25 logikai CPU-val (virtuális mag) egyenértékű, 0,42 GB memóriával és virtuális mag, és Gen4 hardvert használ. A Gen4 és a Gen5 hardver generációinak legkisebb virtuális mag-szolgáltatási célja, **GP_Gen4_1** és **GP_Gen5_2**, több számítási erőforrást biztosít, mint a szabványos S0-adatbázis, így a közvetlen egyezés nem lehetséges. Mivel a Gen4 hardver [leszerelése](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)folyamatban van, a **GP_Gen5_2** lehetőség javasolt. Emellett, ha a számítási feladat megfelelően van kiszámítva a [kiszolgáló](serverless-tier-overview.md) nélküli számítási réteghez, akkor a **GP_S_Gen5_1** szorosabb egyezés lenne.

**Prémium szintű P15-adatbázis áttelepítése**

A leképezési lekérdezés a következő eredményt adja vissza (néhány oszlop nem jelenik meg a rövidség kedvéért):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|Gen5|4,86|29,400|7|42,000|5,05|

Azt láthatjuk, hogy a DTU-adatbázis 42 logikai CPU-val rendelkezik (virtuális mag), és virtuális mag 4,86 GB memóriával rendelkezik, és Gen5 hardvert használ. Habár a virtuális mag szolgáltatás célja nem 42 mag, a **BC_Gen5_40** szolgáltatási cél nagyon közel van a CPU-és a memória-kapacitáshoz, és jó egyezést biztosít.

**Alapszintű 200 eDTU rugalmas készlet áttelepítése**

A leképezési lekérdezés a következő eredményt adja vissza (néhány oszlop nem jelenik meg a rövidség kedvéért):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|Gen5|5,40|2,800|7|4,000|5,05|

Azt láthatjuk, hogy a DTU rugalmas készlet 4 logikai processzorral (virtuális mag) rendelkezik, virtuális mag 5,4 GB memóriával, és Gen5 hardvert használ. A virtuális mag modell közvetlen egyezése **GP_Gen5_4** rugalmas készlet. Ez a szolgáltatási cél azonban legfeljebb 200 adatbázist támogat, míg az alapszintű 200 eDTU rugalmas készlet legfeljebb 500 adatbázist támogat. Ha az áttelepíteni kívánt rugalmas készlet több mint 200-adatbázissal rendelkezik, akkor a megfelelő virtuális mag szolgáltatási célnak **GP_Gen5_6** kell lennie, amely legfeljebb 500 adatbázist támogat.

## <a name="migrate-geo-replicated-databases"></a>Geo-replikált adatbázisok migrálása

A DTU-alapú modellről a virtuális mag-alapú vásárlási modellre való Migrálás hasonló a standard és prémium szintű szolgáltatási szinten lévő adatbázisok közötti földrajzi replikálási kapcsolatok frissítéséhez vagy visszaminősítéséhez. Az áttelepítés során nem kell leállítania a Geo-replikálást, de a következő sorrendi szabályokat kell követnie:

- A frissítéskor először frissítenie kell a másodlagos adatbázist, majd frissítenie kell az elsődlegest.
- A visszalépéskor a sorrend megfordítása: először az elsődleges adatbázist kell visszaadnia, majd vissza kell állítani a másodlagosat.

Ha a Geo-replikációt két rugalmas készlet között használja, javasoljuk, hogy jelöljön ki egy készletet elsődlegesként, a másikat pedig másodlagosként. Ebben az esetben, ha rugalmas készleteket telepít át, ugyanazt a sorrendi útmutatást kell használnia. Ha azonban olyan rugalmas készletekkel rendelkezik, amelyek elsődleges és másodlagos adatbázisokat is tartalmaznak, akkor a készletet a magasabb kihasználtsággal kell kezelni elsődlegesként, és ennek megfelelően kövesse az előkészítési szabályokat.  

Az alábbi táblázat a speciális áttelepítési forgatókönyvekhez nyújt útmutatást:

|Aktuális szolgáltatási szintek|Cél szolgáltatási szintje|Áttelepítés típusa|Felhasználói műveletek|
|---|---|---|---|
|Standard|Általános célú|Oldalirányú|Az áttelepíthető bármely sorrendben, de biztosítani kell a megfelelő virtuális mag-méretezést a fent leírtak szerint.|
|Prémium|Üzleti szempontból kritikus|Oldalirányú|Az áttelepíthető bármely sorrendben, de biztosítani kell a megfelelő virtuális mag-méretezést a fent leírtak szerint.|
|Standard|Üzleti szempontból kritikus|Frissítés|Először át kell telepítenie a másodlagost|
|Üzleti szempontból kritikus|Standard|Alacsonyabb szintre|Először át kell telepítenie az elsődlegest|
|Prémium|Általános célú|Alacsonyabb szintre|Először át kell telepítenie az elsődlegest|
|Általános célú|Prémium|Frissítés|Először át kell telepítenie a másodlagost|
|Üzleti szempontból kritikus|Általános célú|Alacsonyabb szintre|Először át kell telepítenie az elsődlegest|
|Általános célú|Üzleti szempontból kritikus|Frissítés|Először át kell telepítenie a másodlagost|
||||

## <a name="migrate-failover-groups"></a>Feladatátvételi csoportok áttelepíthetők

A több adatbázissal rendelkező feladatátvételi csoportok áttelepítése az elsődleges és a másodlagos adatbázisok egyedi áttelepítését igényli. A folyamat során ugyanazok a megfontolások és az előkészítési szabályok érvényesek. Miután az adatbázisok át lettek konvertálva a virtuális mag-alapú vásárlási modellre, a feladatátvételi csoport ugyanazokkal a házirend-beállításokkal marad érvényben.

### <a name="create-a-geo-replication-secondary-database"></a>Geo-replikációs másodlagos adatbázis létrehozása

A Geo-replikációs másodlagos adatbázist (a Geo-másodlagost) csak az elsődleges adatbázishoz használt szolgáltatási réteg használatával lehet létrehozni. A nagy log-generálási arányú adatbázisok esetében javasoljuk, hogy a Geo-másodlagost ugyanazzal a számítási mérettel hozza létre, mint az elsődleges.

Ha egyetlen elsődleges adatbázishoz hoz létre egy geo-másodlagost a rugalmas készletben, akkor győződjön meg arról, hogy a `maxVCore` Készlet beállítása megfelel az elsődleges adatbázis számítási méretének. Ha egy másik rugalmas készletben elsődlegesként hoz létre egy geo-másodlagost, akkor azt javasoljuk, hogy a készletek azonos `maxVCore` beállításokkal rendelkezzenek.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Adatbázis-másolat használata a DTU-ből a virtuális mag-be való Migrálás céljából

A DTU-alapú számítási mérettel rendelkező adatbázisok bármely adatbázisba másolhatók egy virtuális mag-alapú számítási mérettel korlátozás vagy speciális sorrendbe rendezés nélkül, feltéve, hogy a célként megadott számítási méret támogatja a forrásadatbázis maximális adatbázis-méretét. Az adatbázis-másolat létrehoz egy pillanatképet az adatokról a másolási művelet kezdési időpontjáról, és nem szinkronizálja az adatokat a forrás és a cél között.

## <a name="next-steps"></a>Következő lépések

- Az önálló adatbázisok számára elérhető számítási méretek és a tárolási méretek tekintetében lásd: [SQL Database virtuális mag-alapú erőforrás-korlátok az önálló adatbázisokhoz](resource-limits-vcore-single-databases.md).
- A rugalmas készletekhez rendelkezésre álló számítási méretek és a tárhelyek méretére vonatkozó választási lehetőségekért tekintse meg a [rugalmas készletek SQL Database virtuális mag-alapú erőforrás-korlátozásait](resource-limits-vcore-elastic-pools.md).