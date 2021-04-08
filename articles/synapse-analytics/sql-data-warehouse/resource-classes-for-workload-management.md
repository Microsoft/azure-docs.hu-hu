---
title: Erőforrás-osztályok a számítási feladatok kezeléséhez
description: Útmutató az Azure szinapszis Analytics szolgáltatásban található lekérdezések párhuzamosságának és számítási erőforrásainak kezeléséhez az erőforrás-osztályok használatával.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7714ce748eb172565357723924ab2212e9559e1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98685327"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Számítási feladatok kezelése erőforrás-osztályokkal az Azure szinapszis Analyticsben

Útmutató az erőforrás-osztályok használatáról az Azure szinapszisban található szinapszis SQL Pool-lekérdezések memóriájának és egyidejűségének kezeléséhez.  

## <a name="what-are-resource-classes"></a>Mik az erőforrás-osztályok?

A lekérdezés kapacitását a felhasználó erőforrás-osztálya határozza meg.  Az erőforrás-osztályok előre meghatározott erőforrás-korlátokat tartalmaznak a szinapszis SQL-készletben, amelyek a számítási erőforrásokat és a párhuzamosságot használják a lekérdezések végrehajtásához. Az erőforrás-osztályok segítséget nyújthatnak a lekérdezések erőforrásainak konfigurálásához azáltal, hogy korlátozásokat állítanak be a párhuzamosan futó lekérdezések és az egyes lekérdezésekhez hozzárendelt számítási erőforrások számára.  A memória és a Egyidejűség között kompromisszum van.

- A kisebb erőforrás-osztályok csökkentik a maximális memóriát a lekérdezésekben, de növelhetik a párhuzamosságot.
- A nagyobb erőforrás-osztályok a lekérdezésen alapuló maximális memóriát növelhetik, de a párhuzamosságot is csökkenthetik.

Két típusú erőforrás-osztály létezik:

- A statikus erőforrások osztályai, amelyek a rögzített adathalmazok méretének növelésére alkalmasak.
- Dinamikus erőforrás-osztályok, amelyek olyan adatkészletek esetében alkalmasak, amelyek mérete növekszik, és nagyobb teljesítményre van szükség, mint a szolgáltatási szint skálázása.

Az erőforrás-osztályok a párhuzamossági tárolóhelyek használatával mérik az erőforrások felhasználását.  A [párhuzamossági résidőket](#concurrency-slots) a cikk későbbi részében ismertetjük.

- Az erőforrás-osztályok erőforrás-felhasználásának megtekintéséhez tekintse meg a [memória és a Egyidejűség korlátai](memory-concurrency-limits.md)című témakört.
- Az erőforrás osztályának módosításához egy másik felhasználó alatt futtathatja a lekérdezést, vagy [módosíthatja az aktuális felhasználó erőforrás-osztályának](#change-a-users-resource-class) tagságát.

### <a name="static-resource-classes"></a>Statikus erőforrás-osztályok

A statikus erőforrás-osztályok ugyanazt a memóriát foglalják magukban, függetlenül attól, hogy milyen teljesítményszint van megadva az [adatraktár-egységekben](what-is-a-data-warehouse-unit-dwu-cdwu.md). Mivel a lekérdezések ugyanazt a memóriát kapják meg, a teljesítmény szintjétől függetlenül, [az adatraktár horizontális felskálázása](quickstart-scale-compute-portal.md) lehetővé teszi, hogy több lekérdezés fusson egy adott erőforráson belül.  A statikus erőforrás-osztályok ideálisak, ha az adatmennyiség ismert és állandó.

A statikus erőforrás-osztályok a következő előre definiált adatbázis-szerepkörökkel vannak implementálva:

- staticrc10
- staticrc20 erőforrásosztályhoz
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dinamikus erőforrás-osztályok

A dinamikus erőforrás-osztályok változó mennyiségű memóriát foglalnak le az aktuális szolgáltatási szinttől függően. Míg a statikus erőforrás-osztályok a magasabb párhuzamosságok és a statikus adatmennyiségek esetében előnyösek, a dinamikus erőforrás-osztályok a növekvő vagy változó mennyiségű adatokhoz alkalmasabbak.  Ha nagyobb szolgáltatási szintre bővít, a lekérdezések automatikusan több memóriát kapnak.  

A dinamikus erőforrás-osztályok a következő előre definiált adatbázis-szerepkörökkel vannak implementálva:

- smallrc
- mediumrc
- largerc
- xlargerc

Az egyes erőforrás-osztályok memóriájának kiosztása a következő.

| Szolgáltatásszint  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12,5%             | 12,5%                  | 22                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22                    | 70%                    |
| DW400c         | 6,25%             | 10%                    | 22                    | 70%                    |
| DW500c lehetőséget         | 5%                | 10%                    | 22                    | 70%                    |
| DW1000c<br> DW30000c | 3%       | 10%                    | 22                    | 70%                    |

### <a name="default-resource-class"></a>Alapértelmezett erőforrás osztály

Alapértelmezés szerint minden felhasználó a dinamikus erőforrás osztály **smallrc** tagja.

A szolgáltatás-rendszergazda erőforrás-osztálya a smallrc helyen van kijavítva, és nem módosítható.  A szolgáltatás-rendszergazda a kiépítési folyamat során létrehozott felhasználó.  A szolgáltatás rendszergazdája ebben a környezetben a "kiszolgálói rendszergazdai bejelentkezéshez" megadott bejelentkezési azonosítót adja meg új szinapszis SQL-készlet új kiszolgálóval való létrehozásakor.

> [!NOTE]
> A Active Directory rendszergazdaként definiált felhasználók vagy csoportok is szolgáltatás-rendszergazdák.
>
>

## <a name="resource-class-operations"></a>Erőforrás-osztály műveletei

Az erőforrás-osztályok úgy vannak kialakítva, hogy javítsák az adatkezelési és-kezelési tevékenységek teljesítményét. Az összetett lekérdezések a nagyméretű erőforrás-osztályok alatt is kihasználhatják a futtatást. A nagyméretű illesztések és rendezések lekérdezési teljesítménye például javítható, ha az erőforrás-osztály elég nagy ahhoz, hogy a lekérdezés a memóriában is fusson.

### <a name="operations-governed-by-resource-classes"></a>Erőforrás-osztályok alá tartozó műveletek

Ezeket a műveleteket az erőforrás-osztályok szabályozzák:

- BESZÚRÁS – KIJELÖLÉS, FRISSÍTÉS, TÖRLÉS
- SELECT (felhasználói táblák lekérdezésekor)
- ALTER INDEX – Újraépítés vagy átrendezés
- A TÁBLA ÚJRAÉPÍTÉSÉNEK MÓDOSÍTÁSA
- CREATE INDEX
- FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX LÉTREHOZÁSA
- CREATE TABLE A SELECT (CTAS)
- Az adatok betöltése
- Az adatátviteli szolgáltatás (DMS) által végrehajtott adatáthelyezési műveletek

> [!NOTE]  
> A dinamikus felügyeleti nézetekre (DMV) vagy más rendszernézetekre vonatkozó utasításokat nem a párhuzamossági korlátok egyike szabályozza. A rendszer figyelése a rendszeren futtatott lekérdezések számától függetlenül.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Nem erőforrás-osztályokra vonatkozó műveletek

Egyes lekérdezések mindig a smallrc-erőforrás osztályban futnak, még akkor is, ha a felhasználó egy nagyobb erőforrás osztály tagja. Ezek a kivételt képező lekérdezések nem számítanak bele a egyidejűségi korlátba. Ha például a párhuzamossági korlát 16, számos felhasználó kiválasztható a rendszernézetből az elérhető egyidejűségi tárolóhelyek befolyásolása nélkül.

Az alábbi utasítások mentesülnek az erőforrás-osztályoktól, és mindig a smallrc futnak:

- TÁBLA létrehozása vagy eldobása
- TÁBLÁZAT MÓDOSÍTÁSA... PARTÍCIÓ VÁLTása, FELOSZTása vagy EGYESÍTÉSe
- MÓDOSÍTÁSI INDEX LETILTÁSA
- DROP INDEX
- STATISZTIKÁK létrehozása, frissítése vagy eldobása
- TRUNCATE TABLE
- MÓDOSÍTÁS ENGEDÉLYEZÉSE
- CREATE LOGIN
- FELHASZNÁLÓ létrehozása, módosítása vagy eldobása
- ELJÁRÁS létrehozása, módosítása vagy eldobása
- NÉZET létrehozása vagy eldobása
- ÉRTÉKEK BESZÚRÁSA
- Válasszon a rendszernézetek és a DMV közül
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Egyidejűségi tárolóhelyek

A egyidejűségi tárolóhelyek kényelmes megoldást jelentenek a lekérdezés-végrehajtáshoz elérhető erőforrások nyomon követésére. Ilyenek például a megvásárolt jegyek, amelyeket egy koncerten tartanak fenn, mert korlátozott az ülőhelyek száma. Az adatraktárban található egyidejűségi tárolóhelyek teljes számát a szolgáltatási szint határozza meg. Ahhoz, hogy egy lekérdezés el tudja indítani a végrehajtást, képesnek kell lennie elegendő egyidejű tárolóhely fenntartására. Amikor egy lekérdezés befejeződik, felszabadítja a párhuzamossági tárolóhelyeket.  

- A 10 egyidejű tárolóhelyet futtató lekérdezések 5 alkalommal több számítási erőforrást tudnak elérni, mint a 2 egyidejű bővítőhelyen futó lekérdezés.
- Ha minden lekérdezés 10 egyidejű tárolóhelyet igényel, és 40 egyidejűségi tárolóhely van, akkor egyszerre csak 4 lekérdezés futhat.

Csak az erőforrás által szabályozott lekérdezések használják a párhuzamossági tárolóhelyeket. A rendszerlekérdezések és néhány triviális lekérdezés nem használ tárolóhelyet. A felhasználható párhuzamossági résidők pontos számát a lekérdezés erőforrás-osztálya határozza meg.

## <a name="view-the-resource-classes"></a>Az erőforrás-osztályok megtekintése

Az erőforrás-osztályok előre definiált adatbázis-szerepkörökként vannak implementálva. Kétféle erőforrás-osztály létezik: dinamikus és statikus. Az erőforrás-osztályok listájának megtekintéséhez használja a következő lekérdezést:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Felhasználó erőforrás-osztályának módosítása

Az erőforrás-osztályok a felhasználók adatbázis-szerepkörökhöz való hozzárendelésével valósíthatók meg. Amikor egy felhasználó futtat egy lekérdezést, a lekérdezés a felhasználó erőforrás-osztályával fut. Ha például egy felhasználó a staticrc10 adatbázis-szerepkör tagja, a lekérdezések kis mennyiségű memóriával futnak. Ha egy adatbázis-felhasználó tagja a xlargerc vagy a staticrc80 adatbázis-szerepkörnek, a lekérdezések nagy mennyiségű memóriával futnak.

A felhasználó erőforrás-osztályának növeléséhez a [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) használatával adja hozzá a felhasználót egy nagy erőforrás-osztály adatbázis-szerepköréhez.  Az alábbi kód hozzáadja a felhasználót a largerc adatbázis-szerepkörhöz.  Minden kérelem a rendszermemória 22%-át kapja meg.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Az erőforrás osztályának csökkentéséhez használja a [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Ha a "loaduser" nem tag vagy más erőforrás-osztály, az alapértelmezett smallrc-erőforrás osztályba kerül, és 3% memóriát biztosít.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Erőforrás-osztály prioritása

A felhasználók több erőforrás osztály tagjai lehetnek. Ha egy felhasználó több erőforrás osztályhoz tartozik:

- A dinamikus erőforrás-osztályok elsőbbséget élveznek a statikus erőforrások osztályaival szemben. Ha például egy felhasználó mind a mediumrc (dinamikus), mind a staticrc80 (statikus) tagja, a lekérdezések a mediumrc-vel futnak.
- A nagyobb erőforrás-osztályok elsőbbséget élveznek a kisebb erőforrás-osztályokkal szemben. Ha például egy felhasználó tagja a mediumrc és a largerc, a lekérdezések a largerc-mel futnak. Hasonlóképpen, ha a felhasználó mind a staticrc20 erőforrásosztályhoz, mind a statirc80 tagja, akkor a lekérdezések staticrc80 erőforrás-lefoglalásokkal futnak.

## <a name="recommendations"></a>Javaslatok

>[!NOTE]
>Érdemes lehet kihasználni a munkaterhelés-kezelési képességeket (a[munkaterhelés elkülönítését](sql-data-warehouse-workload-isolation.md), a [besorolást](sql-data-warehouse-workload-classification.md) és a [fontosságot](sql-data-warehouse-workload-importance.md)) a számítási feladatok és a kiszámítható teljesítmény szabályozása érdekében.  
>
>

Javasoljuk, hogy hozzon létre egy olyan felhasználót, amely egy adott típusú lekérdezési vagy betöltési művelet futtatására van kijelölve. Ezt a felhasználót állandó erőforrás-osztályként adja meg, és ne módosítsa az erőforrás osztályát gyakori módon. A statikus erőforrás-osztályok nagyobb mértékben szabályozzák a munkaterhelést, ezért javasoljuk, hogy statikus erőforrás-osztályokat használjon a dinamikus erőforrás-osztályok megfontolása előtt.

### <a name="resource-classes-for-load-users"></a>Erőforrás-osztályok a betöltési felhasználók számára

`CREATE TABLE` Alapértelmezés szerint fürtözött oszlopcentrikus indexeket használ. Az adatok oszlopcentrikus-indexbe tömörítése egy memória-igényes művelet, a memória terhelése pedig csökkentheti az index minőségét. A memória-nyomás nagyobb erőforrás-osztályt eredményezhet az adatbetöltése során. Annak érdekében, hogy a terhelések elegendő memóriával rendelkezzenek, létrehozhat egy terhelés futtatására kijelölt felhasználót, és hozzárendelheti a felhasználót egy magasabb szintű erőforrás-osztályhoz.

A terhelések feldolgozásához szükséges memória a betöltött tábla természetétől és az adatok méretétől függ. A memóriával kapcsolatos követelményekről a [sorcsoport minőségének maximalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)című témakörben olvashat bővebben.

Ha meghatározta a memória követelményét, válassza ki, hogy a betöltési felhasználót statikus vagy dinamikus erőforrás-osztályhoz kívánja-e rendelni.

- Statikus erőforrás-osztály használata, ha a tábla memóriájának követelményei egy adott tartományon belül esnek. A terhelés a megfelelő memóriával fut. Az adatraktár skálázásakor a terhelések nem igényelnek további memóriát. A statikus erőforrás osztály használatával a memória foglalása állandó marad. Ez a konzisztencia memóriát takarít meg, és lehetővé teszi, hogy a több lekérdezés egyidejűleg fusson. Javasoljuk, hogy az új megoldások először a statikus erőforrás-osztályokat használják, mivel ezek nagyobb ellenőrzést biztosítanak.
- Dinamikus erőforrás-osztály használata, ha a tábla memóriájának követelményei széles skálán változnak. Előfordulhat, hogy a terhelések nagyobb memóriát igényelnek, mint az aktuális DWU vagy cDWU szint. Az adatraktár skálázása több memóriát ad hozzá a műveletek betöltéséhez, ami lehetővé teszi a terhelések gyorsabb végrehajtását.

### <a name="resource-classes-for-queries"></a>Lekérdezésekhez tartozó erőforrás-osztályok

Egyes lekérdezések nagy számítási igényűek, néhányat nem.  

- Válassza ki a dinamikus erőforrás osztályt, ha a lekérdezések összetettek, de nincs szükség magas egyidejűségre.  Például a napi vagy heti jelentések létrehozása időnként szükség van az erőforrásokra. Ha a jelentések nagy mennyiségű adattal dolgoznak fel, az adatraktár skálázása nagyobb memóriát biztosít a felhasználó meglévő erőforrás-osztályának.
- Válassza ki a statikus erőforrás osztályt, ha az erőforrás-várakozások a nap folyamán változnak. Egy statikus erőforráscsoport például jól működik, ha az adattárházat sok ember kérdezi le. Az adatraktár skálázásakor a felhasználónak kiosztott memória mennyisége nem változik. Ennek következtében a rendszeren párhuzamosan több lekérdezés is végrehajtható.

A megfelelő memória-támogatás számos tényezőtől függ, például a lekérdezett adatok mennyiségétől, a tábla sémáinak természetétől, valamint a különböző illesztési, kiválasztási és csoportosítási beállításoktól. Általánosságban elmondható, hogy több memória kiosztása lehetővé teszi a lekérdezések gyorsabb elvégzését, de csökkenti a teljes párhuzamosságot. Ha a Egyidejűség nem probléma, a túlterhelést okozó memória nem károsítja az átviteli sebességet.

A teljesítmény finomhangolásához használjon különböző erőforrás-osztályokat. A következő szakasz egy tárolt eljárást tartalmaz, amely segít kideríteni a legjobb erőforrás-osztályt.

## <a name="example-code-for-finding-the-best-resource-class"></a>Példa a legjobb erőforrás-osztály megtalálására szolgáló kódra

A következő megadott tárolt eljárással kiderítheti a párhuzamosságokat és a memória-engedélyezési műveleteket egy adott SLO esetében, valamint a legjobb erőforrás-osztályt a memória-igényes CCI-műveletekhez egy adott erőforrás osztályon nem particionált CCI-táblában:

A következő tárolt eljárás célja:

1. Egy adott SLO-beli erőforrás-osztály párhuzamossági és memória-engedélyezésének megtekintéséhez. A felhasználónak NULL értékűnek kell lennie a séma és a táblanév számára, ahogy az ebben a példában is látható.  
2. Ha meg szeretné tekinteni a legjobb erőforrás-osztályt a memória-igényes KKU-műveletekhez (betöltés, tábla másolása, újraépítési index stb.) a nem particionált CCI-táblázatban egy adott erőforrás osztályban. A tárolt proc tábla sémája alapján állapítja meg a szükséges memória-hozzáférést.

### <a name="dependencies--restrictions"></a>Függőségek & korlátozások

- Ez a tárolt eljárás nem úgy van kialakítva, hogy kiszámítsa egy particionált CCI-tábla memóriára vonatkozó követelményét.
- Ez a tárolt eljárás nem veszi figyelembe a memóriára vonatkozó követelményeket az CTAS/INSERT – Select rész kiválasztásához, és feltételezi, hogy a kiválasztott elem.
- Ez a tárolt eljárás egy ideiglenes táblát használ, amely abban a munkamenetben érhető el, ahol a tárolt eljárás létrejött.
- Ez a tárolt eljárás az aktuális ajánlattól függ (például hardverkonfiguráció, DMS-konfiguráció), és ha ezek bármelyike megváltozik, akkor ez a tárolt proc nem fog megfelelően működni.  
- Ez a tárolt eljárás a meglévő egyidejűségi korláttól függ, és ha ezek a változások megváltoznak, akkor ez a tárolt eljárás nem fog megfelelően működni.  
- Ez a tárolt eljárás a meglévő erőforrás-osztályoktól függ, és ha ezek változnak, akkor ez a tárolt eljárás nem fog megfelelően működni.  

>[!NOTE]  
>Ha nem kap kimenetet a megadott paraméterekkel rendelkező tárolt eljárás végrehajtása után, akkor két eset lehet.
>
>1. Vagy a DW paraméter érvénytelen SLO-értéket tartalmaz
>2. Vagy nincs megfelelő erőforrás-osztály a KKU-művelethez a táblában.
>
>A DW100c esetében például a legmagasabb rendelkezésre állású memória 1 GB, a tábla sémája pedig elég nagy ahhoz, hogy az 1 GB-os követelményt átadja.

### <a name="usage-example"></a>Használati példa

Szintaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Adjon meg NULL paramétert az aktuális DWU kinyeréséhez a DW DB-ből, vagy adja meg a támogatott DWU a következő formában: "DW100c".
2. @SCHEMA_NAME: Adja meg a tábla sémájának nevét.
3. @TABLE_NAME: Adja meg a kamat táblanév nevét

Példák a tárolt folyamat végrehajtására:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A következő utasítás az előző példákban használt tábla1 hoz létre.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Tárolt eljárás definíciója

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Következő lépések

Az adatbázis-felhasználók és a biztonság kezelésével kapcsolatos további információkért lásd: [adatbázis biztonságossá tétele a SZINAPSZIS SQL-ben](sql-data-warehouse-overview-manage-security.md). További információ arról, hogy a nagyobb erőforrás-osztályok Hogyan javíthatják a fürtözött oszlopcentrikus index minőségét: [a oszlopcentrikus tömörítésének memória-optimalizálása](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).
