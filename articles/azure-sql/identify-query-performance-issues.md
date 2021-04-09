---
title: A lekérdezés teljesítményével kapcsolatos problémák típusai Azure SQL Database
description: Ebből a cikkből megismerheti a Azure SQL Database lekérdezési teljesítményével kapcsolatos problémákat, és megtudhatja, hogyan azonosíthatja és elháríthatja a lekérdezéseket ezekkel a problémákkal
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: troubleshooting
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: 4d0f5404a64eae99ced0dd797954ba042b50060f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217226"
---
# <a name="detectable-types-of-query-performance-bottlenecks-in-azure-sql-database"></a>A lekérdezési teljesítményt érintő szűk keresztmetszetek típusai az Azure SQL Database-ben
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

A teljesítmény szűk keresztmetszetének feloldására tett kísérlet során először határozza meg, hogy a szűk keresztmetszet bekövetkezik-e, miközben a lekérdezés futó állapotban van vagy várakozási állapotú. A döntéstől függően különböző felbontások érvényesek. A következő ábrán megismerheti azokat a tényezőket, amelyek egy futó probléma vagy egy várakozással kapcsolatos probléma miatt lehetnek. Ebben a cikkben az egyes típusú problémákkal kapcsolatos problémákat és megoldásokat tárgyaljuk.

Az ilyen típusú teljesítménybeli szűk keresztmetszetek észleléséhez Azure SQL Database [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#detectable-database-performance-patterns) vagy SQL Server [DMV](database/monitoring-with-dmvs.md) is használhat.

![Munkaterhelési állapotok](./media/identify-query-performance-issues/workload-states.png)

**Futó problémák**: a futtatással kapcsolatos problémák általában olyan fordítási problémákkal kapcsolatosak, amelyek a nem elégséges vagy túlhasználatú erőforrásokkal kapcsolatos, a legoptimálisabb lekérdezési tervet vagy végrehajtási problémákat eredményeznek.
**Várakozással kapcsolatos problémák**: a várakozással kapcsolatos problémák általában a következőkhöz kapcsolódnak:

- Zárolások (blokkolás)
- I/O
- TempDB-használattal kapcsolatos tartalom
- Memória-engedélyezési várakozások

## <a name="compilation-problems-resulting-in-a-suboptimal-query-plan"></a>Az optimálisnál rosszabb lekérdezési terv eredményeképpen felmerülő fordítási problémák

Az SQL-lekérdezés-optimalizáló által generált inoptimalizált terv a lassú lekérdezési teljesítmény oka lehet. Az SQL-lekérdezés optimalizálása a hiányzó index, az elavult statisztika, a feldolgozandó sorok számának vagy a szükséges memória pontatlan becslése miatt nem megfelelő előfizetési tervet eredményezhet. Ha tudja, hogy a lekérdezés gyorsabban lett végrehajtva a múltban vagy egy másik példányon, hasonlítsa össze a tényleges végrehajtási terveket, és ellenőrizze, hogy azok eltérnek-e.

- Azonosítsa a hiányzó indexeket az alábbi módszerek egyikével:

  - [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#missing-index)használata.
  - Az önálló és a készletezett adatbázisok [Database Advisor](database/database-advisor-implement-performance-recommendations.md) .
  - DMV. Ez a példa egy hiányzó index hatását mutatja be, a hiányzó indexek észlelésének módját a [DMV használatával,](database/performance-guidance.md#identifying-and-adding-missing-indexes) valamint a hiányzó index-javaslatok megvalósításának következményeit.
- Próbálkozzon a [lekérdezési útmutatók](/sql/t-sql/queries/hints-transact-sql-query), a [frissítési statisztika](/sql/t-sql/statements/update-statistics-transact-sql)vagy az [indexek újraépítésével](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes) a jobb terv beszerzéséhez. A problémák automatikus mérsékléséhez engedélyezze a Azure SQL Database [automatikus tervének javítását](../azure-sql/database/automatic-tuning-overview.md) .

  Ebben a [példában](database/performance-guidance.md#query-tuning-and-hinting) egy paraméteres lekérdezés, a feltétel észlelése és a megoldáshoz tartozó lekérdezési mutató használata okozza a legoptimálisabb lekérdezési terv hatását.

- Próbálja meg módosítani az adatbázis kompatibilitási szintjét, és implementálja az intelligens lekérdezések feldolgozását. Az SQL-lekérdezés-optimalizáló az adatbázis kompatibilitási szintjétől függően eltérő lekérdezési tervet hozhat. A magasabb kompatibilitási szintek [intelligens lekérdezés-feldolgozási képességeket](/sql/relational-databases/performance/intelligent-query-processing)biztosítanak.

  - További információ a lekérdezések feldolgozásáról: a [lekérdezés-feldolgozási architektúra útmutatója](/sql/relational-databases/query-processing-architecture-guide).
  - Az adatbázis-kompatibilitási szintek módosításához és a kompatibilitási szintek közötti különbségekről bővebben lásd: [adatbázis módosítása](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).
  - A kardinális becsléssel kapcsolatos további információkért lásd: a [kardinális becslése](/sql/relational-databases/performance/cardinality-estimation-sql-server)

## <a name="resolving-queries-with-suboptimal-query-execution-plans"></a>Lekérdezések feloldása az optimálisnál rosszabb lekérdezés-végrehajtási tervekkel

Az alábbi szakaszban megtudhatja, Hogyan oldhatók fel a lekérdezések az optimálisnál rosszabb lekérdezés-végrehajtási terv használatával.

### <a name="queries-that-have-parameter-sensitive-plan-psp-problems"></a><a name="ParamSniffing"></a> A paraméterekre érzékeny terv (PSP) problémákkal rendelkező lekérdezések

Ha a lekérdezés-optimalizáló olyan lekérdezés-végrehajtási tervet hoz létre, amely csak az adott paraméterérték (vagy az értékek halmaza) számára érhető el, és a gyorsítótárazott csomag nem optimális, akkor az egymást követő végrehajtásokban használt paraméterek értékei esetében nem optimális. Azok a csomagok, amelyek nem optimálisak, a lekérdezési teljesítménnyel kapcsolatos problémákat okozhatnak, és csökkenthetik a munkaterhelés teljes átviteli sebességét.

További információ a paraméterek elemzéséről és a lekérdezések feldolgozásáról: a [lekérdezés-feldolgozási architektúra útmutatója](/sql/relational-databases/query-processing-architecture-guide#ParamSniffing).

Több megkerülő megoldás is csökkentheti a PSP-problémákat. Minden megkerülő megoldáshoz kapcsolódó kompromisszumok és hátrányok tartoznak:

- A lekérdezés végrehajtásához használja az [ÚJRAfordítási](/sql/t-sql/queries/hints-transact-sql-query) lekérdezési célzást. Ez a megkerülő megoldás a minőség javítása érdekében a fordítási időt és a CPU-t növeli. A `RECOMPILE` beállítás gyakran nem lehetséges olyan munkaterhelések esetén, amelyeknél magas átviteli sebesség szükséges.
- Használja a [(z) (optimalizálás a...)](/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatót a tényleges paraméterérték felülbírálására egy jellemző paraméter értékkel, amely egy olyan tervet hoz létre, amely elég jó a legtöbb paraméter-érték lehetőséghez. Ehhez a beállításhoz jól ismerni kell az optimális paramétereket és a társított terv jellemzőit.
- A [(z) (ismeretlen)](/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatóval felülbírálhatja a tényleges paraméter értékét, és Ehelyett használhatja a sűrűség vektor átlagát. Ezt úgy is megteheti, hogy beírja a bejövő paraméterek értékét a helyi változók között, majd a predikátumok helyi változóit használja a paraméterek használata helyett. Ennél a javításnál az átlagos sűrűségnek *elég jónak* kell lennie.
- Tiltsa le a paraméterek teljes elemzését a [DISABLE_PARAMETER_SNIFFING](/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutató használatával.
- A gyorsítótárban lévő újrafordítások elkerülése érdekében használja a [KEEPFIXEDPLAN](/sql/t-sql/queries/hints-transact-sql-query) lekérdezési mutatóját. Ez a megkerülő megoldás azt feltételezi, hogy a megfelelő közös csomag már a gyorsítótárban van. Az automatikus statisztikákat is letilthatja, hogy csökkentse a jó terv kizárásának esélyét, és egy új, rossz tervet fog lefordítani.
- A tervet úgy kényszerítheti, hogy explicit módon a [használati terv](/sql/t-sql/queries/hints-transact-sql-query) lekérdezése célzást használja a lekérdezés újraírásával és a lekérdezés szövegének hozzáadásával. Vagy állítson be egy adott csomagot a Query Store használatával vagy az [automatikus hangolás](../azure-sql/database/automatic-tuning-overview.md)engedélyezésével.
- Cserélje le az egyetlen eljárást egy beágyazott eljárásokra, amelyek mindegyike felhasználható a feltételes logika és a hozzá tartozó paraméterek értékei alapján.
- Dinamikus karakterlánc-végrehajtási alternatívák létrehozása statikus eljárás definíciójában.

A PSP-problémák megoldásával kapcsolatos további információkért tekintse meg ezeket a blogbejegyzéseket:

- [Egy paramétert érzek](/archive/blogs/queryoptteam/i-smell-a-parameter)
- [A (z) és a dinamikus SQL vs. eljárások és a paraméteres lekérdezések minőségének megtervezése](/archive/blogs/conor_cunningham_msft/conor-vs-dynamic-sql-vs-procedures-vs-plan-quality-for-parameterized-queries)
- [SQL-lekérdezés optimalizálási módszerei SQL Server: paraméter-elemzés](https://www.sqlshack.com/query-optimization-techniques-in-sql-server-parameter-sniffing/)

### <a name="compile-activity-caused-by-improper-parameterization"></a>Helytelen paraméterezés által okozott tevékenységek fordítása

Ha egy lekérdezés literálokat tartalmaz, vagy az adatbázismotor automatikusan felparaméterezi az utasítást, vagy a felhasználó explicit módon felparaméterezi az utasítást, hogy csökkentse a fordítások számát. A lekérdezések nagy számú fordítása ugyanazzal a mintával, de a különböző literális értékek magas CPU-használatot eredményezhetnek. Hasonlóképpen, ha csak részben parametrizálja egy olyan lekérdezést, amely továbbra is literálokkal rendelkezik, akkor az adatbázismotor még nem parametrizálja a lekérdezést.

Példa egy részleges paraméteres lekérdezésre:

```sql
SELECT *
FROM t1 JOIN t2 ON t1.c1 = t2.c1
WHERE t1.c1 = @p1 AND t2.c2 = '961C3970-0E54-4E8E-82B6-5545BE897F8F';
```

Ebben a példában a `t1.c1` veszi `@p1` , de `t2.c2` továbbra is a GUID-ot használja literálként. Ebben az esetben, ha megváltoztatja a értékét `c2` , a lekérdezés más lekérdezésként lesz kezelve, és új fordítás fog történni. Ebben a példában a fordítások csökkentése érdekében parametrizálja a GUID azonosítót is.

Az alábbi lekérdezés a lekérdezési kivonatok számát jeleníti meg, hogy a lekérdezés megfelelően van-e konfigurálva:

```sql
SELECT TOP 10
  q.query_hash
  , count (distinct p.query_id ) AS number_of_distinct_query_ids
  , min(qt.query_sql_text) AS sampled_query_text
FROM sys.query_store_query_text AS qt
  JOIN sys.query_store_query AS q
     ON qt.query_text_id = q.query_text_id
  JOIN sys.query_store_plan AS p
     ON q.query_id = p.query_id
  JOIN sys.query_store_runtime_stats AS rs
     ON rs.plan_id = p.plan_id
  JOIN sys.query_store_runtime_stats_interval AS rsi
     ON rsi.runtime_stats_interval_id = rs.runtime_stats_interval_id
WHERE
  rsi.start_time >= DATEADD(hour, -2, GETUTCDATE())
  AND query_parameterization_type_desc IN ('User', 'None')
GROUP BY q.query_hash
ORDER BY count (distinct p.query_id) DESC;
```

### <a name="factors-that-affect-query-plan-changes"></a>A lekérdezési terv módosítását befolyásoló tényezők

Egy lekérdezés-végrehajtási terv újrafordítása olyan generált lekérdezési tervet eredményezhet, amely eltér az eredeti gyorsítótárazott tervtől. Lehetséges, hogy egy meglévő eredeti csomagot is automatikusan újrafordítanak a különböző okok miatt:

- A séma módosításait a lekérdezés hivatkozik
- A táblákban lévő adatváltozásokra a lekérdezés hivatkozik
- A lekérdezési környezet beállításai módosultak

A lefordított tervet különböző okok miatt lehet kiadni a gyorsítótárból, például:

- Példányok újraindítása
- Adatbázis hatókörű konfigurációjának módosításai
- Memóriaterhelés
- Explicit kérelmek a gyorsítótár törléséhez

Ha újrafordítási emlékeztetőt használ, a rendszer nem gyorsítótárazza a csomagot.

Az újrafordítás (vagy a gyorsítótár kiürítése utáni friss fordítás) továbbra is az eredetivel megegyező lekérdezés-végrehajtási terv létrehozását eredményezheti. Ha a terv a korábbi vagy az eredeti csomag alapján változik, a következő magyarázatok valószínűek:

- **Megváltozott fizikai kialakítás**: például az újonnan létrehozott indexek hatékonyabban fedik le a lekérdezés követelményeit. Az új indexeket új fordításban lehet használni, ha a lekérdezés-optimalizáló úgy dönt, hogy az új indexet használja, mint az eredetileg a lekérdezés-végrehajtás első verziójára kiválasztott adatstruktúra használata. A hivatkozott objektumok fizikai változásai a fordítási idő új tervének megválasztásához vezethetnek.

- **Kiszolgáló erőforrásainak eltérései**: Ha egy rendszer egyik csomagja eltér a tervtől, akkor az erőforrás rendelkezésre állása, például az elérhető processzorok száma, befolyásolhatja, hogy melyik tervet hozza létre a rendszer. Ha például egy rendszer több processzorral rendelkezik, lehetséges, hogy egy párhuzamos tervet választ ki.

- **Különböző statisztikák**: a hivatkozott objektumokhoz társított statisztikák változhattak, vagy az eredeti rendszer statisztikái lényegesen eltérőek lehetnek. Ha a statisztikai változás és az újrafordítás történik, a lekérdezés-optimalizáló a változástól kezdődő statisztikát használja. Az átdolgozott statisztikai adatok eloszlása és gyakorisága eltérő lehet az eredeti fordítástól. Ezek a változások a kardinális becslések létrehozásához használatosak. (A *kardinális becslések* a logikai lekérdezési fában áthaladó sorok száma.) A kardinális becslések változásai a különböző fizikai operátorok és a hozzájuk tartozó műveletek elvégzésére is kiválaszthatók. A statisztikában még kisebb változások is megváltoztathatják a lekérdezés végrehajtási tervét.

- **Módosult az adatbázis-kompatibilitási szint vagy a Cardinals-kalkulátor verziója**: az adatbázis kompatibilitási szintjének módosítása lehetővé teszi, hogy az új stratégiák és szolgáltatások egy másik lekérdezés-végrehajtási tervet eredményeznek. Az adatbázis-kompatibilitási szinten kívül a letiltott vagy engedélyezett nyomkövetési jelző 4199 vagy az adatbázis hatókörű QUERY_OPTIMIZER_HOTFIXES konfigurációjának módosult állapota is befolyásolhatja a lekérdezés végrehajtási tervének választási lehetőségeit a fordítás ideje alatt. A nyomkövetési jelzők 9481 (örökölt CE) és 2312 (az alapértelmezett CE kényszerített) a tervet is érintik.

## <a name="resource-limits-issues"></a>Erőforrás-korlátokkal kapcsolatos problémák

A lassú lekérdezési teljesítmény nem kapcsolódik az optimálisnál rosszabb lekérdezési csomagokhoz, és a hiányzó indexek általában nem elégséges vagy túlhasználatú erőforrásokhoz kapcsolódnak. Ha a lekérdezési terv optimális, akkor előfordulhat, hogy a lekérdezés (és az adatbázis) nem éri el az adatbázis, a rugalmas készlet vagy a felügyelt példány erőforrás-korlátozásait. Előfordulhat például, hogy a szolgáltatás szintjén a napló írási sebessége felesleges.

- Az erőforrásokkal kapcsolatos problémák észlelése a Azure Portal használatával: Ha szeretné megtekinteni, hogy az erőforrás-korlátok a probléma, tekintse meg az [erőforrás-figyelés SQL Database](database/monitor-tune-overview.md#azure-sql-database-and-azure-sql-managed-instance-resource-monitoring) Önálló adatbázisok és rugalmas készletek esetén lásd: [Database Advisor teljesítményre vonatkozó javaslatok](database/database-advisor-implement-performance-recommendations.md) és a [lekérdezési teljesítmény](database/query-performance-insight-use.md).
- Erőforrás-korlátok észlelése [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) használatával
- Erőforrásokkal kapcsolatos problémák észlelése a [DMV](database/monitoring-with-dmvs.md)használatával:

  - A [sys.dm_db_resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV a CPU-t, az I/O-t és a memória-használatot adja vissza az adatbázishoz. Egy sor minden 15 másodperces intervallumban létezik, még akkor is, ha nincs tevékenység az adatbázisban. A korábbi adatait egy óráig őrzi meg a rendszer.
  - A [sys.resource_stats](database/monitoring-with-dmvs.md#monitor-resource-use) DMV visszaadja a CPU-használati és tárolási adatokat Azure SQL Database számára. Az adatok gyűjtése és összesítése öt percenként történik.
  - [Sok egyéni lekérdezés, amely összesíti a magas CPU-t](database/monitoring-with-dmvs.md#many-individual-queries-that-cumulatively-consume-high-cpu)

Ha nem elegendő erőforrással azonosítja a problémát, az erőforrások frissítésével növelheti az adatbázis kapacitását, hogy felvegye a CPU-követelményeket. További információkért lásd: [önálló adatbázis-erőforrások méretezése Azure SQL Database](database/single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése a Azure SQL Databaseban](database/elastic-pool-scale.md). A felügyelt példányok skálázásával kapcsolatos információkért lásd: [szolgáltatási rétegbeli erőforrás-korlátok](managed-instance/resource-limits.md#service-tier-characteristics)

## <a name="performance-problems-caused-by-increased-workload-volume"></a>Megnövekedett munkaterhelés-mennyiség által okozott teljesítménnyel kapcsolatos problémák

Az alkalmazások forgalmának növekedése és a számítási feladatok mennyisége nagyobb CPU-használatot eredményezhet. A probléma megfelelő diagnosztizálásához azonban körültekintően kell eljárnia. Ha nagy CPU-problémát lát, válaszoljon ezekre a kérdésekre annak megállapítására, hogy a növekedés a munkaterhelés kötetének változásai miatt következik-e be:

- Az alkalmazás lekérdezései okozzák a nagy CPU-problémát?
- A [legfontosabb CPU-fogyasztási lekérdezések](database/monitoring-with-dmvs.md#the-cpu-issue-occurred-in-the-past)esetében:

  - Több végrehajtási terv lett társítva ugyanahhoz a lekérdezéshez? Ha igen, miért?
  - Ugyanazzal a végrehajtási tervvel rendelkező lekérdezések esetében a végrehajtási idők konzisztensek voltak? Növekedett a végrehajtások száma? Ha igen, a munkaterhelés növekedése valószínűleg teljesítménnyel kapcsolatos problémákat okoz.

Összefoglalva, ha a lekérdezés végrehajtási terve nem eltérően lett végrehajtva, de a CPU-használat a végrehajtások számával együtt növekedett, a teljesítménnyel kapcsolatos probléma valószínűleg a munkaterhelés növekedésével lesz összefüggésben.

Nem mindig könnyű megállapítani a CPU-problémát okozó számítási feladatok kötetének változását. Vegye figyelembe a következő tényezőket:

- **Megváltoztatott erőforrás-használat**: Vegyünk például egy olyan forgatókönyvet, amelyben a CPU-használat hosszabb ideig 80%-ra nőtt. A CPU-használat önmagában nem jelenti azt, hogy a munkaterhelés mennyisége megváltozott. A lekérdezés-végrehajtási terv és az adateloszlás változásainak regressziói is hozzájárulhatnak az erőforrás-használathoz, még akkor is, ha az alkalmazás ugyanazt a munkaterhelést hajtja végre.

- **Új lekérdezés megjelenése**: az alkalmazás különböző időpontokban lehet új lekérdezéseket vezetni.

- **A kérelmek számának növelése vagy csökkenése**: Ez a forgatókönyv a számítási feladatok legnyilvánvalóbb mérőszáma. A lekérdezések száma nem mindig felel meg az erőforrás-felhasználásnak. Ez a metrika azonban még mindig jelentős jel, feltéve, hogy más tényezők nem változnak.

Intelligent Insights használata a [munkaterhelés növekedésének](database/intelligent-insights-troubleshoot-performance.md#workload-increase) észleléséhez és a [regressziók tervezéséhez](database/intelligent-insights-troubleshoot-performance.md#plan-regression).

## <a name="waiting-related-problems"></a>Várakozással kapcsolatos problémák

Miután megnyitotta a legoptimálisabb tervet, és a *várakozással kapcsolatos* problémák a végrehajtási problémákkal kapcsolatosak, a teljesítményproblémák általában a lekérdezések valószínűleg egy adott erőforrásra várnak. A várakozással kapcsolatos problémákat az alábbiak okozhatják:

- **Blokkolás**:

  Előfordulhat, hogy egy lekérdezés zárolja az adatbázisban lévő objektumokat, míg mások megpróbálnak hozzáférni ugyanahhoz az objektumhoz. A blokkoló lekérdezések a [DMV](database/monitoring-with-dmvs.md#monitoring-blocked-queries) vagy a [Intelligent Insights](database/intelligent-insights-troubleshoot-performance.md#locking)használatával azonosíthatók. További információ: az [Azure SQL-blokkoló problémáinak megismerése és megoldása](database/understand-resolve-blocking.md).
- **IO-problémák**

  Előfordulhat, hogy a lekérdezések a lapokat az adatvagy a naplófájlba való írásra várnak. Ebben az esetben a, a `INSTANCE_LOG_RATE_GOVERNOR` `WRITE_LOG` vagy a `PAGEIOLATCH_*` WAIT statisztikát a DMV-ben találja. Lásd: a DMV használata az [IO-teljesítménnyel kapcsolatos problémák azonosításához](database/monitoring-with-dmvs.md#identify-io-performance-issues).
- **TempDB kapcsolatos problémák**

  Ha a munkaterhelés ideiglenes táblákat használ, vagy a TempDB kifolyások vannak, akkor előfordulhat, hogy a lekérdezések TempDB átviteli sebességgel rendelkeznek. Lásd: a DMV használata az [Identity tempdb-problémákhoz](database/monitoring-with-dmvs.md#identify-tempdb-performance-issues).
- **Memóriával kapcsolatos problémák**

  Ha a munkaterhelés nem rendelkezik elegendő memóriával, az oldal várható élettartama csökkenhet, vagy előfordulhat, hogy a lekérdezések kevesebb memóriát igényelnek. Bizonyos esetekben a lekérdezés-optimalizáló beépített intelligencia javítja a memóriával kapcsolatos problémákat. Lásd: a DMV használata a [memória-engedélyezési problémák azonosításához](database/monitoring-with-dmvs.md#identify-memory-grant-wait-performance-issues).

### <a name="methods-to-show-top-wait-categories"></a>A felső várakozási kategóriák megjelenítésének módszerei

Ezeket a metódusokat általában a várakozási típusok leggyakoribb kategóriáinak megjelenítésére használják:

- A Intelligent Insights használatával azonosíthatja a teljesítmény romlásával kapcsolatos lekérdezéseket a [megnövekedett várakozások](database/intelligent-insights-troubleshoot-performance.md#increased-wait-statistic) miatt
- A [lekérdezési tároló](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) használatával megkeresheti az egyes lekérdezések várakozási statisztikáit az idő múlásával. A lekérdezési tárolóban a várakozási típusok a várakozási kategóriákba vannak összevonva. Megkeresheti a várakozási kategóriák hozzárendelését a [sys.query_store_wait_stats](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql#wait-categories-mapping-table)a várakozási típusoknál.
- A [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) használatával a lekérdezési művelet során végrehajtott szálak összes várakozásával kapcsolatos információkat adhat vissza. Ennek az összesített nézetnek a segítségével diagnosztizálhatja a teljesítménnyel kapcsolatos problémákat Azure SQL Database, valamint adott lekérdezésekkel és kötegekkel is. A lekérdezések erőforrásokra, várakozási sorokra vagy külső várakozásokra is várnak.
- A [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) használatával adatokat adhat vissza az egyes erőforrásokra várakozó feladatok várólistáján.

A nagy CPU-forgatókönyvek esetében előfordulhat, hogy a Query Store és a wait statisztikája nem tükrözi a CPU-használatot, ha:

- A nagy CPU-fogyasztású lekérdezések végrehajtása még folyamatban van.
- A nagy CPU-fogyasztásra felhasználható lekérdezések akkor futnak, amikor feladatátvétel történt.

A lekérdezési tárolót nyomon követő DMV és a várakozási statisztikának csak a sikeresen befejezett és időkorlátú lekérdezések eredményei láthatók. Nem jelenítenek meg adatokat a jelenleg végrehajtás alatt álló utasításokhoz, amíg az utasítások befejeződik. A dinamikus felügyeleti nézet [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) a jelenleg végrehajtás alatt álló lekérdezések és a hozzájuk tartozó munkavégző idő nyomon követésére használható.

> [!TIP]
> További eszközök:
>
> - [TigerToolbox várakozások és zárak](https://github.com/Microsoft/tigertoolbox/tree/master/Waits-and-Latches)
> - [TigerToolbox usp_whatsup](https://github.com/Microsoft/tigertoolbox/tree/master/usp_WhatsUp)

## <a name="next-steps"></a>Következő lépések

[SQL Database monitorozás és hangolás áttekintése](database/monitor-tune-overview.md)