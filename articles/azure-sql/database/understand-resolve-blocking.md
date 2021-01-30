---
title: Az Azure SQL-blokkoló problémáinak megismerése és megoldása
titleSuffix: Azure SQL Database
description: Az Azure SQL Database-hez kapcsolódó, a blokkolással és a hibaelhárítással kapcsolatos témakörök áttekintése.
services: sql-database
dev_langs:
- TSQL
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: ''
ms.date: 1/14/2020
ms.openlocfilehash: 1341d0e64a01ff428fe42735d198c5e6b74b0ce8
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093308"
---
# <a name="understand-and-resolve-azure-sql-database-blocking-problems"></a>Azure SQL Database blokkolási problémák ismertetése és megoldása
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="objective"></a>Cél

A cikk az Azure SQL Database-adatbázisok blokkolását ismerteti, és bemutatja, hogyan lehet elhárítani a blokkolást, és elhárítani azokat. 

Ebben a cikkben a kapcsolat kifejezése az adatbázis egy bejelentkezett munkamenetére hivatkozik. Minden kapcsolat munkamenet-AZONOSÍTÓként (SPID) vagy session_id sok DMV jelenik meg. Ezeket az SPID-ket gyakran nevezik folyamatnak, bár a szokásos értelemben nem külön folyamat-környezet. Ehelyett minden SPID olyan kiszolgálói erőforrásokból és adatstruktúrákból áll, amelyek egy adott ügyfél egyetlen kapcsolódási kérelmének kiszolgálásához szükségesek. Egyetlen ügyfélalkalmazás egy vagy több kapcsolattal rendelkezhet. Azure SQL Database szempontjából nincs különbség egyetlen ügyfélalkalmazás egyetlen ügyfélszámítógépről történő több kapcsolata és több ügyfélalkalmazás vagy több ügyfélszámítógép közötti kapcsolat között. atomiak. Egy-egy kapcsolatok letilthatnak egy másikat, függetlenül a forrás ügyfelétől.

> [!NOTE]
> **Ez a tartalom Azure SQL Databasera jellemző.** A Azure SQL Database a Microsoft SQL Server adatbázismotor legújabb stabil verziójára épül, így a tartalom nagy része hasonló, bár a hibaelhárítási lehetőségek és az eszközök eltérőek lehetnek. A SQL Server blokkolásával kapcsolatos további információkért lásd: [SQL Server blokkolási problémák megismerése és megoldása](/troubleshoot/sql/performance/understand-resolve-blocking).

## <a name="understand-blocking"></a>A blokkolás ismertetése 
 
A blokkolás a RDBMS-alapú, zárolási párhuzamosságtal nem elkerülhető és nem megfelelő kialakítási jellemző. Ahogy azt korábban említettük, SQL Serverban a blokkolás akkor történik meg, amikor az egyik munkamenet egy adott erőforrás zárolását tárolja, és egy második SPID megpróbálja ütköző zárolási típust bekérni ugyanazon az erőforráson. Általában az az időtartam, ameddig az első SPID-zárolás az erőforrás kicsi. Ha a tulajdonosi munkamenet felszabadítja a zárolást, a második kapcsolat megszerezheti saját zárolását az erőforráson, és folytathatja a feldolgozást. Ez normális viselkedés, és a nap folyamán többször is előfordulhat, ha a rendszer teljesítménye nem észlelhető.

Egy lekérdezés időtartama és tranzakciós környezete határozza meg, hogy mennyi ideig tart a zárolása, és ezáltal milyen hatással lesznek más lekérdezésekre. Ha a lekérdezés nem kerül végrehajtásra egy tranzakción belül (és nincs szükség zárolási mutatóra), a SELECT utasítások zárolása csak egy erőforráson lesz tárolva, a lekérdezés során nem. INSERT, UPDATE és DELETE utasítások esetén a rendszer a lekérdezés során zárolja a zárolásokat, mind az adatkonzisztencia, mind a lekérdezés visszagörgetéséhez.

A tranzakción belül végrehajtott lekérdezések esetében a zárolások tárolási időtartamát a lekérdezés típusa, a tranzakció elkülönítési szintje, valamint a zárolási javaslatok a lekérdezésben való használata határozza meg. A zárolási, zárolási és a tranzakciók elkülönítési szintjeinek leírását a következő cikkekben találja:

* [Az adatbázismotor által végzett zárolás](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [A zárolás és a sor verziószámozásának testreszabása](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#customizing-locking-and-row-versioning)
* [Zárolási módok](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_modes)
* [Kompatibilitás zárolása](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide#lock_compatibility)
* [Tranzakciók](/sql/t-sql/language-elements/transactions-transact-sql)

Ha a zárolás és a blokkolás nem szűnik meg arra a pontra, ahol a rendszer teljesítménye káros hatással van, az a következő okok egyike miatt fordul elő:

* A SPID a kiszabadítása előtt hosszabb ideig zárolja az erőforrás-készleteket. Ez a blokkolási típus az idő múlásával oldja fel magát, de a teljesítmény romlását okozhatja.

* Az SPID-azonosító egy erőforrás-készleten zárolja a zárolásokat, és soha nem szabadítja fel őket. Ez a típusú blokkolás nem oldja meg önmagát, és határozatlan időre megakadályozza az érintett erőforrásokhoz való hozzáférést.

Az első forgatókönyvben a helyzet nagyon folyadék lehet, mert a különböző SPID-ok a különböző erőforrások blokkolását okozzák az idő múlásával, egy mozgó cél létrehozásával. Ezek a helyzetek nehezen orvosolhatók a [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával, hogy az egyes lekérdezésekre szűkítse a problémát. Ezzel szemben a második helyzet olyan konzisztens állapotot eredményez, amely egyszerűbb lehet a diagnosztizáláshoz.

## <a name="applications-and-blocking"></a>Alkalmazások és blokkolás

Előfordulhat, hogy a probléma elhárítása érdekében a kiszolgálóoldali hangolásra és a platformmal kapcsolatos problémákra kell összpontosítani. Azonban a kizárólag az adatbázisnak fizetett figyelmet nem lehet a megoldáshoz kötni, és jobban befogadni az időt és energiát az ügyfélalkalmazás és az általa benyújtott lekérdezések vizsgálatára. Függetlenül attól, hogy az alkalmazás milyen szintű láthatóságot tesz elérhetővé az adatbázis által kezdeményezett hívásokkal kapcsolatban, a blokkoló probléma mégis gyakran igényli az alkalmazás által küldött pontos SQL-utasítások vizsgálatát, valamint az alkalmazás pontos viselkedését a lekérdezések megszakításával, a kapcsolat kezelésével, az összes eredmény-sorok beolvasásával és így tovább. Ha a fejlesztői eszköz nem engedélyez explicit vezérlést a kapcsolat kezelése, a lekérdezés megszakítása, a lekérdezés időkorlátja, az eredmény beolvasása és így tovább, a blokkoló problémák esetleg nem oldhatók fel. Ezt a potenciált alaposan meg kell vizsgálni, mielőtt kijelöli a Azure SQL Database alkalmazás-fejlesztői eszközét, különösen a OLTP-környezetek esetében. 

Figyeljen az adatbázis teljesítményére az adatbázis és az alkalmazás tervezési és kialakítási fázisában. Az erőforrás-használatot, az elkülönítési szintet és a tranzakció elérési útjának hosszát az egyes lekérdezésekhez kell kiértékelni. Minden lekérdezésnek és tranzakciónak a lehető legkönnyűnek kell lennie. A megfelelő ügyfélkapcsolat-kezelési fegyelem gyakorlása nélkül az alkalmazás úgy tűnhet, hogy a felhasználók nem tudnak elfogadható teljesítményt biztosítani, de a teljesítmény jelentősen csökkentheti a felhasználók számát. 

Az alkalmazások és a lekérdezések megfelelő kialakításával Azure SQL Database több ezer egyidejű felhasználó támogatására képes egyetlen kiszolgálón, kevés blokkolással.

> [!Note]
> További alkalmazás-fejlesztési útmutatásért lásd: [csatlakozási problémák elhárítása és egyéb hibák a Azure SQL Database és az Azure SQL felügyelt példányaival és az](troubleshoot-common-errors-issues.md) [átmeneti hibák kezelésével](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)kapcsolatban.

## <a name="troubleshoot-blocking"></a>Blokkolási hibák

Függetlenül attól, hogy a rendszer milyen blokkolási helyzetbe kerül, a hibaelhárítási módszer ugyanaz. Ezek a logikai elkülönítések a cikk összetételének többi részét diktálják. A koncepció célja, hogy megkeresse a fő blokkot, és azonosítsa a lekérdezés okát, és hogy miért blokkolja. Miután azonosította a problémás lekérdezést (azaz azt, hogy mi tart a hosszabb ideig tartó zárolások tárolásával), a következő lépés a blokkolás okának elemzése és meghatározása. Miután megértette, hogy miért, a lekérdezés és a tranzakció újratervezésével módosíthatjuk a módosításokat.

A hibaelhárítás lépései:

1. A fő blokkolási munkamenet azonosítása (a fej blokkolása)

2. A blokkolást okozó lekérdezés és tranzakció megkeresése (a hosszabb ideig tartó zárolások megtartása)

3. A hosszan tartó blokkolás okának elemzése/megértése

4. Blokkoló probléma megoldása a lekérdezés és a tranzakció újratervezésével

Most nézzük meg, hogyan lehet megbeszélni a fő blokkolási munkamenetet a megfelelő adatrögzítéssel.

## <a name="gather-blocking-information"></a>Blokkoló adatok összegyűjtése

A blokkolási problémák elhárításával kapcsolatos nehézségek ellensúlyozása érdekében az adatbázis rendszergazdája olyan SQL-parancsfájlokat használhat, amelyek folyamatosan figyelik az Azure SQL Database zárolási és blokkolási állapotát. Az adatok összegyűjtéséhez alapvetően két módszer áll rendelkezésre. 

Az első a dinamikus felügyeleti objektumok (DMOs-EK) lekérdezése és az eredmények összehasonlítása az idő múlásával. A cikkben hivatkozott egyes objektumok dinamikus felügyeleti nézetek (DMV), némelyikük pedig dinamikus felügyeleti függvények (DMFs). A második módszer a Xevent típusú eseményekhez használata a végrehajtandó végrehajtás rögzítéséhez. 


## <a name="gather-information-from-dmvs"></a>Információk gyűjtése a DMV

A blokkolási hibákra hivatkozó DMV célja, hogy azonosítsa az SPID-t (munkamenet-azonosítót) a blokkoló lánc és az SQL-utasítás élén. Keresse meg a blokkolt áldozati SPID-ket. Ha bármely SPID-t egy másik SPID blokkol, akkor vizsgálja meg az erőforrást birtokló SPID-t (a blokkolási SPID-t). A tulajdonos SPID is blokkolva van? A láncot úgy érheti el, hogy megkeresi a fő blokkot, majd megvizsgálhatja, hogy miért tartja karban a zárolását.

Ne felejtse el futtatni ezeket a parancsfájlokat a cél Azure SQL Database-ben.

* A sp_who és sp_who2 parancsok régebbi parancsok az összes aktuális munkamenet megjelenítéséhez. A DMV sys.dm_exec_sessions egy olyan eredményhalmaz több olyan értékét adja vissza, amely könnyebben kérdezhető le és szűrhető. A sys.dm_exec_sessions a többi lekérdezés magját fogja találni. 

* Ha már rendelkezik egy meghatározott munkamenettel, `DBCC INPUTBUFFER(<session_id>)` a segítségével megkeresheti a munkamenet által küldött utolsó utasítást. Hasonló eredmények is visszaállíthatók a sys.dm_exec_input_buffer Dynamic Management Function (DMF) használatával egy olyan eredményhalmaz esetében, amely könnyebben kérdezhető le és szűrhető, így biztosítva a session_id és a request_id. Például a session_id 66 és a request_id 0 által küldött legutóbbi lekérdezés visszaküldéséhez:

```sql
SELECT * FROM sys.dm_exec_input_buffer (66,0);
```

* Tekintse át a sys.dm_exec_requests, és hivatkozzon a blocking_session_id oszlopra. Ha blocking_session_id = 0, a rendszer nem blokkolja a munkamenetet. Habár sys.dm_exec_requests csak a jelenleg végrehajtás alatt álló kérelmeket listázza, a rendszer minden olyan (aktív vagy nem) kapcsolódási listát listáz sys.dm_exec_sessions. Ezt a közös összekapcsolást sys.dm_exec_requests és sys.dm_exec_sessions között a következő lekérdezésben építheti ki.

* A minta lekérdezés futtatásával megkeresheti az aktív végrehajtó lekérdezéseket és az aktuális SQL batch-szöveget vagy a bemeneti puffer szövegét a [sys.dm_exec_sql_text](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql) vagy a [sys.dm_exec_input_buffer](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-input-buffer-transact-sql) DMV használatával. Ha a sys.dm_exec_sql_text mezője által visszaadott adatmennyiség `text` Null, a lekérdezés jelenleg nem fut. Ebben az esetben a `event_info` sys.dm_exec_input_buffer mező az SQL-motornak átadott utolsó parancs sztringet fogja tartalmazni. 

```sql
WITH cteBL (session_id, blocking_these) AS 
(SELECT s.session_id, blocking_these = x.blocking_these FROM sys.dm_exec_sessions s 
CROSS APPLY    (SELECT isnull(convert(varchar(6), er.session_id),'') + ', '  
                FROM sys.dm_exec_requests as er
                WHERE er.blocking_session_id = isnull(s.session_id ,0)
                AND er.blocking_session_id <> 0
                FOR XML PATH('') ) AS x (blocking_these)
)
SELECT s.session_id, blocked_by = r.blocking_session_id, bl.blocking_these
, batch_text = t.text, input_buffer = ib.event_info, * 
FROM sys.dm_exec_sessions s 
LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
INNER JOIN cteBL as bl on s.session_id = bl.session_id
OUTER APPLY sys.dm_exec_sql_text (r.sql_handle) t
OUTER APPLY sys.dm_exec_input_buffer(s.session_id, NULL) AS ib
WHERE blocking_these is not null or r.blocking_session_id > 0
ORDER BY len(bl.blocking_these) desc, r.blocking_session_id desc, r.session_id;
```

* A hosszan futó vagy nem véglegesített tranzakciók megjelenítéséhez használjon egy másik DMV az aktuális nyitott tranzakciók megtekintéséhez, beleértve a [sys.dm_tran_database_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql), a [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql), a [sys.dm_exec_connections](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-connections-transact-sql)és a sys.dm_exec_sql_text. A nyomkövetési tranzakciókhoz több DMV is társítva van: további [DMV a tranzakciók](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql) itt. 

```sql
SELECT [s_tst].[session_id],
[database_name] = DB_NAME (s_tdt.database_id),
[s_tdt].[database_transaction_begin_time], 
[sql_text] = [s_est].[text] 
FROM sys.dm_tran_database_transactions [s_tdt]
INNER JOIN sys.dm_tran_session_transactions [s_tst] ON [s_tst].[transaction_id] = [s_tdt].[transaction_id]
INNER JOIN sys.dm_exec_connections [s_ec] ON [s_ec].[session_id] = [s_tst].[session_id]
CROSS APPLY sys.dm_exec_sql_text ([s_ec].[most_recent_sql_handle]) AS [s_est];
```

* Hivatkozás [sys.dm_os_waiting_tasks](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-waiting-tasks-transact-sql) , amely az SQL szál/tevékenység rétegében található. Ezzel a beállítással megtudhatja, hogy a rendszer milyen SQL-várakozási típust észlelt a kérelemben. A sys.dm_exec_requestshoz hasonlóan a sys.dm_os_waiting_tasks csak az aktív kérelmeket adja vissza. 

> [!Note]
> Sokkal több várakozási típus esetén, beleértve az összesített várakozási statisztikát az idő múlásával, tekintse meg a DMV [sys.dm_db_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database). Ez a DMV csak az aktuális adatbázis összesített várakozási statisztikáit adja vissza.

* A [sys.dm_tran_locks](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql) DMV használatával részletesebb információkat talál a lekérdezések által elhelyezett zárolásokról. Ez a DMV nagy mennyiségű adatmennyiséget tud visszaadni egy éles SQL Serverban, és hasznos lehet a jelenleg tárolt zárolások diagnosztizálására. 

A sys.dm_os_waiting_tasks belső ILLESZTÉSe miatt a következő lekérdezés korlátozza a kimenetet sys.dm_tran_locks csak a jelenleg letiltott kérelmekre, a várakozási állapotukra és azok zárolására:

```sql
SELECT table_name = schema_name(o.schema_id) + '.' + o.name
, wt.wait_duration_ms, wt.wait_type, wt.blocking_session_id, wt.resource_description
, tm.resource_type, tm.request_status, tm.request_mode, tm.request_session_id
FROM sys.dm_tran_locks AS tm
INNER JOIN sys.dm_os_waiting_tasks as wt ON tm.lock_owner_address = wt.resource_address
LEFT OUTER JOIN sys.partitions AS p on p.hobt_id = tm.resource_associated_entity_id
LEFT OUTER JOIN sys.objects o on o.object_id = p.object_id or tm.resource_associated_entity_id = o.object_id
WHERE resource_database_id = DB_ID()
AND object_name(p.object_id) = '<table_name>';
```

* A DMV használatával a lekérdezési eredmények időbeli tárolása olyan adatpontokat biztosít, amelyek lehetővé teszik, hogy egy adott időintervallumon belül áttekintse a blokkolást a megőrzött blokkolások vagy trendek azonosításához. 

## <a name="gather-information-from-extended-events"></a>Információk gyűjtése a kiterjesztett eseményekről

A fenti információk mellett gyakran szükség van arra, hogy nyomon kövessék a kiszolgálón található tevékenységek nyomkövetését, hogy alaposan megvizsgálják a blokkoló problémát Azure SQL Database. Ha például egy munkamenet több utasítást hajt végre egy tranzakción belül, a rendszer csak az elküldött utolsó utasítást fogja megjeleníteni. Azonban a korábbi utasítások egyike lehet az, hogy a zárolások továbbra is megmaradnak. A nyomkövetés lehetővé teszi az aktuális tranzakción belüli munkamenet által végrehajtott összes parancs megtekintését.

A SQL Server két módon rögzíthet nyomkövetéseket. Kiterjesztett események (Xevent típusú eseményekhez) és Profiler-Nyomkövetések. [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) azonban elavult nyomkövetési technológia nem támogatott Azure SQL Database esetén. A [kiterjesztett események](/sql/relational-databases/extended-events/extended-events) az újabb nyomkövetési technológia, amely nagyobb sokoldalúságot és kisebb hatást gyakorol a megfigyelt rendszerre, és a felülete integrálva van a SQL Server Management Studioba (SSMS). 

Tekintse meg az SSMS-ben a [kiterjesztett események új munkamenet varázsló](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server) használatát ismertető dokumentumot. Az Azure SQL Database-adatbázisok esetében azonban a SSMS az egyes adatbázisokhoz tartozó kiterjesztett események almappáját nyújtja Object Explorer. A következő hasznos események rögzítéséhez használjon kiterjesztett események munkamenet-varázslót: 

-   Kategória hibái:
    -   Figyelmet
    -   Error_reported  
    -   Execution_warning

-   Kategória figyelmeztetései: 
    -   Missing_join_predicate

-   Kategória-végrehajtás:
    -   Rpc_completed
    -   Rpc_starting 
    -   Sql_batch_completed
    -   Sql_batch_starting

-   Zárolás
    -   Lock_deadlock

-   Munkamenet
    -   Existing_connection
    -   Bejelentkezés
    -   Kijelentkezés

## <a name="identify-and-resolve-common-blocking-scenarios"></a>Gyakori blokkolási forgatókönyvek azonosítása és megoldása

A fenti információk vizsgálatával megállapíthatja a legtöbb blokkolási probléma okát. A cikk további részében megtudhatja, hogyan használhatja ezeket az információkat a gyakori blokkolási forgatókönyvek azonosítására és megoldására. Ez a vita feltételezi, hogy használta a blokkoló (korábban hivatkozott) parancsfájlokat a blokkoló SPID-adatok rögzítéséhez, és XEvent-munkamenet használatával rögzítette az alkalmazási tevékenységet.

## <a name="analyze-blocking-data"></a>Blokkoló adatvesztés elemzése 

* Vizsgálja meg a DMV kimenetét sys.dm_exec_requests és sys.dm_exec_sessions a blokkoló láncok fejeinak meghatározásához blocking_these és session_id használatával. Ez a legpontosabban azonosítja, hogy mely kérelmeket blokkolja a rendszer, és blokkolja azokat. Tekintse meg a letiltott és blokkolt munkameneteket. Létezik-e közös vagy gyökér a blokkoló láncban? Valószínűleg közös táblázatot használnak, és egy vagy több, a blokkoló láncban részt vevő előadás írási műveletet folytat. 

* Vizsgálja meg a DMV kimenetét sys.dm_exec_requests és sys.dm_exec_sessions a blokkoló lánc élén álló SPID-azonosítókkal kapcsolatos információkat. Keresse meg a következő mezőket: 

    -    `sys.dm_exec_requests.status`  
    Ez az oszlop egy adott kérelem állapotát jeleníti meg. Az alvó állapot általában azt jelzi, hogy az SPID befejezte a végrehajtást, és arra vár, hogy az alkalmazás egy másik lekérdezést vagy köteget küldjön. Egy futtatható vagy futó állapot azt jelzi, hogy az SPID jelenleg egy lekérdezést dolgoz fel. Az alábbi táblázat a különböző állapotüzenetek rövid leírását ismerteti.

    | Állapot | Értelmezés |
    |:-|:-|
    | Háttér | Az SPID olyan háttérben futó feladatot futtat, mint például a holtpont észlelése, a napló írója vagy az ellenőrzőpont. |
    | Alvó | Az SPID jelenleg nem fut. Ez általában azt jelzi, hogy az SPID az alkalmazásból érkező parancsra vár. |
    | Futó | Az SPID jelenleg egy ütemező fut. |
    | Futtatható | Az SPID az ütemező futtatható sorában található, és a ütemező időpontjának beolvasására vár. |
    | Felfüggesztve | Az SPID egy erőforrásra vár, például egy zárolásra vagy egy retesz. | 
                       
    -    `sys.dm_exec_sessions.open_transaction_count`  
    Ebben a mezőben a munkamenetben lévő nyitott tranzakciók száma látható. Ha ez az érték nagyobb nullánál, az SPID azonosító egy nyitott tranzakción belül van, és a tranzakción belüli bármely utasítás által beszerzett zárolások tárolásához vezethet.

    -    `sys.dm_exec_requests.open_transaction_count`  
    Hasonlóképpen, ebben a mezőben a kérésben szereplő nyitott tranzakciók száma látható. Ha ez az érték nagyobb nullánál, az SPID azonosító egy nyitott tranzakción belül van, és a tranzakción belüli bármely utasítás által beszerzett zárolások tárolásához vezethet.

    -   `sys.dm_exec_requests.wait_type`, `wait_time` és `last_wait_type`  
    Ha a  `sys.dm_exec_requests.wait_type`   értéke null, a kérelem jelenleg nem vár semmire, és az  `last_wait_type`   érték azt jelzi,  `wait_type`   hogy a kérést utoljára észlelte a rendszer. További információ  `sys.dm_os_wait_stats` a és a leggyakoribb várakozási típusok leírásáról: [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql). Az  `wait_time`   érték segítségével megállapítható, hogy a kérelem folyamatban van-e. Ha a sys.dm_exec_requests tábla egyik lekérdezése olyan értéket ad vissza az  `wait_time`   oszlopban, amely kisebb, mint a  `wait_time`   sys.dm_exec_requests korábbi lekérdezésének értéke, ez azt jelzi, hogy a korábbi zárolást a rendszer megszerezte és felszabadítja, és most egy új zárolásra vár (feltéve, hogy nem nulla `wait_time` ). Ez ellenőrizhető a `wait_resource`   sys.dm_exec_requests kimenetének összevetésével, amely megjeleníti azt az erőforrást, amelyre a kérés várakozik.

    -   `sys.dm_exec_requests.wait_resource` Ez a mező azt az erőforrást jelzi, amelyre a letiltott kérelmek várnak. Az alábbi táblázat a gyakori  `wait_resource`   formátumokat és azok jelentését tartalmazza:

    | Erőforrás | Formátum | Példa | Magyarázat | 
    |:-|:-|:-|:-|
    | Tábla | DatabaseID: ObjectID: IndexID | LAP: 5:261575970:1 | Ebben az esetben az 5. adatbázis-azonosító a pubok mintaadatbázis és a 261575970-ös AZONOSÍTÓJÚ objektum a titles tábla, a pedig 1 a fürtözött index. |
    | Oldal | DatabaseID: FileID: PageID | LAP: 5:1:104 | Ebben az esetben az 5. adatbázis-azonosító a kocsmák, az 1. fájl azonosítója az elsődleges adatfájl, a 104-es oldal pedig a titles táblához tartozó oldal. Az oldalhoz tartozó object_id azonosításához használja a dinamikus felügyeleti függvényt, [sys.dm_db_page_info](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-page-info-transact-sql), amely a DatabaseID, a FileId, a PageId halad át `wait_resource` . | 
    | Kulcs | DatabaseID: Hobt_id (az index kulcsához tartozó kivonatoló érték) | KULCS: 5:72057594044284928 (3300a4f361aa) | Ebben az esetben az 5. adatbázis-azonosító a pubok, Hobt_ID 72057594044284928 a object_id 261575970 (titles tábla) index_id 2 értéknek felel meg. A sys. Partitions Catalog nézet használatával rendelje hozzá a hobt_id egy adott index_idhoz és object_idhoz. Az indexelő kulcs kivonatának kitördelése nem történik meg egy adott kulcs értékére. |
    | Sor | DatabaseID: FileID: PageID: slot (sor) | RID: 5:1:104:3 | Ebben az esetben a (z) 5. adatbázis-azonosító a pubok, az 1. fájl azonosítója az elsődleges adatfájl, az 104-es oldal a titles táblához tartozó oldal, a 3. bővítőhely pedig a sor pozícióját jelzi a lapon. |
    | Lefordítani  | DatabaseID: FileID: PageID: slot (sor) | RID: 5:1:104:3 | Ebben az esetben a (z) 5. adatbázis-azonosító a pubok, az 1. fájl azonosítója az elsődleges adatfájl, az 104-es oldal a titles táblához tartozó oldal, a 3. bővítőhely pedig a sor pozícióját jelzi a lapon. |

    -    `sys.dm_tran_active_transactions` A [sys.dm_tran_active_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql) DMV olyan nyitott tranzakciókra vonatkozó információkat tartalmaz, amelyek más DMV is csatlakoztathatók, hogy teljes képet lehessen adni a tranzakciók végrehajtásáról vagy visszaállításáról. A következő lekérdezéssel adhat vissza információkat a nyitott tranzakciókra vonatkozóan, és más DMV is kapcsolódhat, beleértve a [sys.dm_tran_session_transactions](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-session-transactions-transact-sql). Fontolja meg egy tranzakció aktuális állapotát, `transaction_begin_time` valamint az egyéb szituációs adatmennyiségeket annak kiértékeléséhez, hogy az lehet-e blokkoló forrás.

    ```sql
    SELECT tst.session_id, [database_name] = db_name(s.database_id)
    , tat.transaction_begin_time
    , transaction_duration_s = datediff(s, tat.transaction_begin_time, sysdatetime()) 
    , transaction_type = CASE tat.transaction_type  WHEN 1 THEN 'Read/write transaction'
                                                    WHEN 2 THEN 'Read-only transaction'
                                                    WHEN 3 THEN 'System transaction'
                                                    WHEN 4 THEN 'Distributed transaction' END
    , input_buffer = ib.event_info, tat.transaction_uow     
    , transaction_state  = CASE tat.transaction_state    
                WHEN 0 THEN 'The transaction has not been completely initialized yet.'
                WHEN 1 THEN 'The transaction has been initialized but has not started.'
                WHEN 2 THEN 'The transaction is active - has not been committed or rolled back.'
                WHEN 3 THEN 'The transaction has ended. This is used for read-only transactions.'
                WHEN 4 THEN 'The commit process has been initiated on the distributed transaction.'
                WHEN 5 THEN 'The transaction is in a prepared state and waiting resolution.'
                WHEN 6 THEN 'The transaction has been committed.'
                WHEN 7 THEN 'The transaction is being rolled back.'
                WHEN 8 THEN 'The transaction has been rolled back.' END 
    , transaction_name = tat.name, request_status = r.status
    , azure_dtc_state = CASE tat.dtc_state 
                        WHEN 1 THEN 'ACTIVE'
                        WHEN 2 THEN 'PREPARED'
                        WHEN 3 THEN 'COMMITTED'
                        WHEN 4 THEN 'ABORTED'
                        WHEN 5 THEN 'RECOVERED' END
    , tst.is_user_transaction, tst.is_local
    , session_open_transaction_count = tst.open_transaction_count  
    , s.host_name, s.program_name, s.client_interface_name, s.login_name, s.is_user_process
    FROM sys.dm_tran_active_transactions tat 
    INNER JOIN sys.dm_tran_session_transactions tst  on tat.transaction_id = tst.transaction_id
    INNER JOIN Sys.dm_exec_sessions s on s.session_id = tst.session_id 
    LEFT OUTER JOIN sys.dm_exec_requests r on r.session_id = s.session_id
    CROSS APPLY sys.dm_exec_input_buffer(s.session_id, null) AS ib;
    ```

    -   További oszlopok

        [Sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql) és [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) többi oszlopa is betekintést nyújt a probléma gyökerébe. Hasznosságuk a probléma körülményeitől függően változhat. Meghatározhatja például, hogy a probléma csak bizonyos ügyfelektől (állomásnév), bizonyos hálózati könyvtáraknál (net_library) történik-e, amikor az SPID által beküldött utolsó köteg `last_request_start_time` sys.dm_exec_sessions, hogy mennyi ideig futott a kérelem a (z) `start_time` sys.dm_exec_requests használatával, és így tovább.


## <a name="common-blocking-scenarios"></a>Gyakori blokkolási forgatókönyvek

Az alábbi táblázat a leggyakoribb tüneteket mutatja be a lehetséges okok miatt.  

A `wait_type` , a `open_transaction_count` és az oszlopok a `status` [sys.dm_exec_request](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql)által visszaadott adatokra hivatkoznak, és [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql)más oszlopokat is visszaadhatnak. A "feloldja?" az oszlop azt jelzi, hogy a blokkolás saját maga oldja-e fel a zárolást, vagy azt, hogy a munkamenetet le kell-e ölni a `KILL` parancs használatával. További információ: [kill (Transact-SQL)](/sql/t-sql/language-elements/kill-transact-sql).

| Eset | Waittype | Open_Tran | Állapot | Oldja fel? | Egyéb tünetek |  
|:-|:-|:-|:-|:-|:-|--|
| 1 | NEM NULL | >= 0 | futtatható | Igen, a lekérdezés befejeződése után. | Sys.dm_exec_sessions az **olvasások**, **cpu_time** és/vagy **memory_usage** oszlopok idővel növekednek. A lekérdezés időtartama magas, ha a művelet befejeződött. |
| 2 | NULL | \>0 | alvó | Nem, de az SPID lehet leállítani. | A kiterjesztett esemény-munkamenetben láthatja az SPID-t, jelezve a lekérdezés időtúllépését vagy megszakítását. |
| 3 | NULL | \>= 0 | futtatható | Nem. Nem oldja fel a rendszer, amíg az ügyfél az összes sort le nem hívja, vagy nem zárja be a kapcsolatokat. Az SPID lehet, de akár 30 másodpercig is eltarthat. | Ha open_transaction_count = 0, és az SPID-azonosítók zárolva vannak, miközben a tranzakció elkülönítési szintje alapértelmezett (OLVASÁSI COMMMITTED), ez valószínűleg oka. |  
| 4 | Változó | \>= 0 | futtatható | Nem. Nem oldja fel a rendszer, amíg az ügyfél nem szakítja meg a lekérdezéseket, vagy bezárja a kapcsolatokat Az SPID-azonosítók elmenthetők, de akár 30 másodpercig is eltarthat. | A blokkoló lánc élén lévő SPID azonosítóhoz tartozó sys.dm_exec_sessions **állomásnév** oszlopa ugyanaz lesz, mint az egyik SPID azonosító, amely blokkolja. |  
| 5 | NULL | \>0 | visszaállítási | Igen. | Az ehhez az SPID-hez tartozó bővített események munkamenetében a rendszer figyelemmel kíséri a lekérdezés időkorlátját vagy megszakítását, vagy egyszerűen csak egy visszaállítási utasítást adott ki. |  
| 6 | NULL | \>0 | alvó | Végül. Ha a Windows NT meghatározza, hogy a munkamenet már nem aktív, a Azure SQL Database kapcsolat megszakad. | `last_request_start_time`Sys.dm_exec_sessions értéke jóval korábbi az aktuális időpontnál. |

Ezeket a forgatókönyveket a következő forgatókönyvek fogják kibővíteni. 

1.  A szokásosan futó lekérdezés hosszú végrehajtási idővel okozott blokkolása

    **Megoldás: az** ilyen típusú blokkolási probléma megoldása a lekérdezés optimalizálási módszereinek megkeresése. Valójában ez az osztály blokkolja a problémát, és azt igényli, hogy ezt elvégzi. A lassú futtatású lekérdezések hibaelhárításával kapcsolatos információkért lásd: a [lassú futtatású lekérdezések hibaelhárítása SQL Serveron](/troubleshoot/sql/performance/troubleshoot-slow-running-queries). További információ: [a teljesítmény figyelése és finomhangolása](/sql/relational-databases/performance/monitor-and-tune-for-performance). 

    A SSMS található [lekérdezési tárolóból](/sql/relational-databases/performance/best-practice-with-the-query-store) származó jelentések is kifejezetten ajánlott és értékes eszköz a legköltségesebb lekérdezések azonosítására. Tekintse át az Azure SQL Database Azure Portaljának [intelligens teljesítménnyel](intelligent-insights-overview.md) foglalkozó szakaszát is, beleértve a [lekérdezési terheléselemzőt](query-performance-insight-use.md)is.

    Ha olyan hosszan futó lekérdezést használ, amely blokkolja a többi felhasználót, és nem optimalizálható, érdemes áthelyeznie egy OLTP-környezetből egy dedikált jelentési rendszerbe, amely [az adatbázis szinkron írásvédett replikája](read-scale-out.md).

1.  Nem véglegesített tranzakcióval rendelkező alvó SPID-azonosító által okozott blokkolás

    Ezt a blokkolási típust gyakran olyan SPID azonosítóval lehet azonosítani, amely alvó állapotba kerül, vagy egy parancsra vár, de a tranzakció beágyazási szintje ( `@@TRANCOUNT` `open_transaction_count` sys.dm_exec_requests) nagyobb nullánál. Ez akkor fordulhat elő, ha az alkalmazás lekérdezi az időkorlátot, vagy a Mégse gombra kattintva kiadja a szükséges számú VISSZAÁLLÍTÁSi és/vagy végrehajtási utasítást is. Ha egy SPID lekérdezési időkorlátot vagy megszakítást kap, a leállítja az aktuális lekérdezést és a köteget, de nem állítja be automatikusan a tranzakciót, és nem véglegesíti azt. Az alkalmazás felelős azért, mert Azure SQL Database nem feltételezhető, hogy egy teljes tranzakciót vissza kell állítani egy lekérdezés megszakítása miatt. A lekérdezés időkorlátja vagy megszakítása a kiterjesztett esemény-munkamenetben lévő SPID-azonosítóra vonatkozó FIGYELEMmel jelzett eseményként fog megjelenni.

    A nem véglegesített explicit tranzakció bemutatásához adja ki a következő lekérdezést:

    ```sql
    CREATE TABLE #test (col1 INT);
    INSERT INTO #test SELECT 1;
    BEGIN TRAN
    UPDATE #test SET col1 = 2 where col1 = 1;
    ```

    Ezt követően hajtsa végre a lekérdezést ugyanabban az ablakban:
    ```sql
    SELECT @@TRANCOUNT;
    ROLLBACK TRAN
    DROP TABLE #test;
    ```

    A második lekérdezés kimenete azt jelzi, hogy a tranzakció beágyazási szintje egy. A tranzakcióban beszerzett összes zárolás továbbra is a tranzakció véglegesítése vagy visszaállítása után marad. Ha az alkalmazások explicit módon megnyitják és véglegesítik a tranzakciókat, a kommunikáció vagy más hiba nyitott állapotban hagyhatja a munkamenetet és a tranzakciót. 

    Használja a fenti parancsfájlt sys.dm_tran_active_transactions alapján a jelenleg nem véglegesített tranzakciók azonosításához.

    **Megoldások**:

     -   Emellett a blokkoló probléma ezen osztálya teljesítményproblémák is lehetnek, és szükség esetén azt is meg kell tennie. Ha a lekérdezés végrehajtási ideje csökkenhet, a lekérdezés időtúllépése vagy megszakítása nem történik meg. Fontos, hogy az alkalmazás képes legyen kezelni az időtúllépési vagy megszakítási forgatókönyveket, de a lekérdezés teljesítményének megvizsgálása is hasznos lehet. 
     
     -    Az alkalmazásoknak megfelelően kell kezelnie a tranzakciók beágyazási szintjét, vagy a lekérdezés megszakítását követően blokkoló problémát okozhatnak. A következőket ajánljuk figyelmébe:  

            *    Az ügyfélalkalmazás hibájában hajtsa végre a hibát `IF @@TRANCOUNT > 0 ROLLBACK TRAN` , még akkor is, ha az ügyfélalkalmazás nem azt hiszi, hogy a tranzakció nyitva van. A nyitott tranzakciók ellenőrzése kötelező, mert a köteg során meghívott tárolt eljárás elindíthat egy tranzakciót az ügyfélalkalmazás ismerete nélkül. Bizonyos feltételek, például a lekérdezés megszakítása, megakadályozhatja az aktuális utasítás lejártának végrehajtását, így még akkor is, ha az eljárás logikával rendelkezik `IF @@ERROR <> 0` a tranzakció vizsgálatához és megszakításához, ez a visszaállítási kód ilyen esetekben nem lesz végrehajtva.  
            *    Ha a kapcsolat készletezését egy olyan alkalmazás használja, amely megnyitja a kapcsolódást, és kis számú lekérdezést futtat, mielőtt visszaadná a kapcsolatnak a készletbe, például egy webalapú alkalmazásba, átmenetileg letilthatja a kapcsolatok készletezését, amíg az ügyfélalkalmazás nem módosul a hibák megfelelő kezelése érdekében. A kapcsolatok készletezésének letiltásával a kapcsolat felszabadítása a Azure SQL Database kapcsolat fizikai leválasztását eredményezi, ami azt eredményezi, hogy a kiszolgáló visszaállítja a nyitott tranzakciókat.  
            *    Használhatja `SET XACT_ABORT ON` a-t a kapcsolatok esetében, vagy bármely tárolt eljárásban, amely megkezdi a tranzakciókat, és a hiba után nem takarít meg. Futásidejű hiba esetén ez a beállítás megszakítja a nyitott tranzakciókat, és visszaadja a vezérlést az ügyfélnek. További információkért tekintse át a [SET XACT_ABORT (Transact-SQL)](/sql/t-sql/statements/set-xact-abort-transact-sql)című témakört.
    > [!NOTE]
    > A rendszer nem állítja alaphelyzetbe a kapcsolódást, amíg újra nem használják a kapcsolódási készletből, így előfordulhat, hogy egy felhasználó megnyithat egy tranzakciót, majd felszabadítja a kapcsolódást a kapcsolódási készlethez, de nem lesz újra felhasználva több másodpercig, amíg a tranzakció nyitva marad. Ha a rendszer nem használja újra a kapcsolódást, a rendszer megszakítja a tranzakciót, amikor a kapcsolódás időtúllépéssel megszűnik, és el lett távolítva a kapcsolódási készletből. Így az ügyfélalkalmazás számára optimális, hogy megszakítsa a hibákkal kapcsolatos tranzakciókat a hibakezelő vagy a használatával, `SET XACT_ABORT ON` hogy elkerülje a lehetséges késleltetést.

    > [!CAUTION]
    > Az alábbi `SET XACT_ABORT ON` , T-SQL-utasítások egy hibát okozó utasítást követve nem lesznek végrehajtva. Ez befolyásolhatja a meglévő kód tervezett folyamatát.

1.  Olyan SPID által okozott blokkolás, amelynek a megfelelő ügyfélalkalmazás nem tudta beolvasni az összes eredményhalmaz befejezését

    Miután lekérdezést küldött a kiszolgálónak, minden alkalmazásnak azonnal le kell kérnie az összes eredményhalmaz befejezését. Ha egy alkalmazás nem kérdezi le az összes eredményt, a zárolások megmaradhatnak a táblákon, és blokkolhatja a többi felhasználót. Ha olyan alkalmazást használ, amely transzparens módon küldi el az SQL-utasításokat a kiszolgálónak, az alkalmazásnak be kell olvasnia az összes eredmény sort. Ha nem, akkor előfordulhat, hogy nem tudja feloldani a blokkolási problémát. A probléma elkerülése érdekében a rosszul viselkedő alkalmazások jelentéskészítésre vagy döntés-támogatási adatbázisra korlátozhatók.
    
    > [!NOTE]
    > A Azure SQL Databasehoz csatlakozó alkalmazások esetében lásd: [útmutatás az újrapróbálkozási logikához](./troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) . 
    
    **Megoldás**: az alkalmazásnak újra kell írnia, hogy beolvassa az eredmény összes sorát a befejezéshez. Ez nem zárja ki az eltolás és a FETCH használatát egy lekérdezés [Order by záradékában](/sql/t-sql/queries/select-order-by-clause-transact-sql#using-offset-and-fetch-to-limit-the-rows-returned) a kiszolgálóoldali lapozás végrehajtásához.

1.  A visszaállítási állapotban lévő SPID azonosító által okozott blokkolás

    Egy, a felhasználó által meghatározott tranzakción kívül törölt vagy megszakított adatmódosítási lekérdezés vissza lesz állítva. Ez az ügyfél hálózati munkamenetének leválasztását, illetve a holtpont áldozatainak kiválasztásakor is megtörténhet. Ez gyakran azonosítható a sys.dm_exec_requests kimenetének megfigyelésével, amely a VISSZAÁLLÍTÁSi **parancsra** utalhat, és a **percent_complete oszlop** előrehaladást eredményezhet. 

    Az 2019-es verzióban bevezetett [gyorsított adatbázis-helyreállítási funkciónak](../accelerated-database-recovery.md) köszönhetően a hosszadalmas visszaállításoknak ritkanak kell lenniük.
    
    **Megoldás**: Várjon, amíg az SPID befejeződik az elvégzett módosítások visszaállítása. 

    Ezen helyzet elkerülése érdekében ne végezzen nagy kötegelt írási műveleteket vagy index-létrehozási vagy-karbantartási műveleteket a OLTP rendszerekben foglalt órákban. Ha lehetséges, végezze el az ilyen műveleteket az alacsony tevékenységű időszakok során.

1.  Árva kapcsolatok által okozott blokkolás

    Ha az ügyfélalkalmazás hibákat észlel, vagy ha az ügyfél munkaállomása újraindul, előfordulhat, hogy a kiszolgáló hálózati munkamenete nem lesz azonnal megszakítva bizonyos körülmények között. Az Azure SQL Database perspektívában az ügyfél továbbra is jelen lesz, és a beszerzett zárolások továbbra is megmaradnak. További információ: az [árva kapcsolatok hibakeresése SQL Serverban](/sql/t-sql/language-elements/kill-transact-sql#remarks). 

    **Megoldás**: Ha az ügyfélalkalmazás leválasztása anélkül történt meg, hogy a megfelelő módon megtisztítja az erőforrásait, a parancs használatával leállíthatja az SPID-t `KILL` . A `KILL` parancs bemenetként veszi át az SPID értéket. Ha például az SPID 99-et szeretné megölni, adja ki a következő parancsot:

    ```sql
    KILL 99
    ```

## <a name="see-also"></a>Lásd még

* [Monitorozás és a teljesítmény finomhangolása az Azure SQL Database-ben és a felügyelt Azure SQL-példányban](/azure/azure-sql/database/monitor-tune-overview)
* [Teljesítmény figyelése a lekérdezési tároló használatával](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [Tranzakciók zárolása és a sorok verziókezelése – útmutató](/sql/relational-databases/sql-server-transaction-locking-and-row-versioning-guide)
* [TRANZAKCIÓ ELKÜLÖNÍTÉSI SZINTJÉNEK BEÁLLÍTÁSA](/sql/t-sql/statements/set-transaction-isolation-level-transact-sql)
* [Gyorsútmutató: Bővített események az SQL Serveren](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
* [Intelligent Insights AI használata az adatbázis teljesítményének figyeléséhez és hibakereséséhez](intelligent-insights-overview.md)

## <a name="learn-more"></a>Tudjon meg többet

* [Azure SQL Database: a teljesítmény finomhangolásának javítása automatikus hangolással](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Improving-Performance-Tuning-with-Automatic-Tuning)
* [Azure SQL Database teljesítmény javítása automatikus hangolással](https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning)
* [Egyenletes teljesítmény biztosítása az Azure SQL-lel](/learn/modules/azure-sql-performance/)
* [A kapcsolódási problémák és a Azure SQL Database és az Azure SQL felügyelt példányával kapcsolatos egyéb hibák elhárítása](troubleshoot-common-errors-issues.md)
* [Átmeneti hibák kezelésére](/aspnet/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling)