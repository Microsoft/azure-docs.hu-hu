---
title: A szinapszis-elemzés figyelése Azure Monitor használatával
description: Megtudhatja, hogyan figyelheti a szinapszis Analytics-munkaterületet Azure Monitor metrikák, riasztások és naplók használatával
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: ac5b1df5ff246623b73dbc41d18216396e6ed7ef
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102549855"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Azure Monitor használata az Azure szinapszis Analytics-munkaterülettel

A Felhőbeli alkalmazások összetettek és számos mozgó részből állnak. A figyelők segítségével biztosítható, hogy az alkalmazások kifogástalan állapotban maradjanak és fussanak. A figyelők segítenek elkerülni a lehetséges problémákat és a múltbeli hibák elhárítását is. A monitorozási adataival részletes elemzéseket nyerhet az alkalmazásairól. Ez az információ segít az alkalmazások teljesítményének és karbantartásának javításában. Emellett segít automatizálni azokat a műveleteket, amelyek egyébként manuális beavatkozást igényelnek.

A Azure Monitor a legtöbb Azure-szolgáltatáshoz biztosít alapszintű infrastruktúra-metrikákat, riasztásokat és naplókat. Az Azure diagnosztikai naplókat egy erőforrás bocsátja ki, és az adott erőforrás működésével kapcsolatos gazdag és gyakori információkat biztosít. Az Azure szinapszis Analytics Azure Monitor diagnosztikai naplókat tud írni.

További információ: [Azure monitor Overview (áttekintés](../../azure-monitor/overview.md)).

## <a name="metrics"></a>Mérőszámok

A monitor segítségével megtekintheti az Azure-beli számítási feladatok teljesítményét és állapotát. A monitorozási adatok legfontosabb típusa a metrika, amelyet a teljesítményszámláló is nevez. A metrikákat a legtöbb Azure-erőforrás kibocsátja. A figyelő számos módszert biztosít ezen mérőszámok konfigurálásához és felhasználásához a figyeléshez és a hibaelhárításhoz.

A metrikák eléréséhez hajtsa végre az [Azure monitor adatplatformon](../../azure-monitor/data-platform.md)megjelenő utasításokat.

### <a name="workspace-level-metrics"></a>Munkaterület-szintű mérőszámok

Íme néhány a munkaterületek által kibocsátott mérőszámok közül:

| **Metrika**                           | **Metrika kategóriája, megjelenítendő név**                  | **Unit (Egység)** | **Összesítési típusok** | **Leírás**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integráció, tevékenység-futtatási metrika                     | Darabszám    | Sum (alapértelmezett), darabszám                | Az 1 perces időszakon belül bekövetkezett vagy befejezett tevékenység-futtatások teljes száma. </br></br> A metrika eredmény-dimenziójának használatával szűrheti a sikeres, sikertelen vagy megszakított végleges állapotot.|
| IntegrationPipelineRunsEnded         | Integráció, folyamat-futtatási metrika                     | Darabszám    | Sum (alapértelmezett), darabszám                | Az 1 perces időszakon belül bekövetkezett vagy befejezett folyamat-futtatások teljes száma. </br></br> A metrika eredmény-dimenziójának használatával szűrheti a sikeres, sikertelen vagy megszakított végleges állapotot. |
| IntegrationTriggerRunsEnded          | Integráció, trigger-futtatási metrika                      | Darabszám    | Sum (alapértelmezett), darabszám                | Az összes olyan trigger-Futtatás száma, amely egy 1 perces időszakon belül történt/véget ért. </br></br> A metrika eredmény-dimenziójának használatával szűrheti a sikeres, sikertelen vagy megszakított végleges állapotot. |
| BuiltinSqlPoolDataProcessedBytes     | Beépített SQL-készlet, feldolgozott adatmennyiség (bájt) | Bájt | Sum (alapértelmezett) | A beépített kiszolgáló nélküli SQL-készlet által feldolgozott adatmennyiség. |
| BuiltinSqlPoolLoginAttempts          | Beépített SQL-készlet, bejelentkezési kísérletek | Darabszám | Sum (alapértelmezett) | Bejelentkezési kísérletek száma a beépített kiszolgáló nélküli SQL-készlethez. |
| BuiltinSqlPoolDataRequestsEnded      | Beépített SQL-készlet, befejezett kérelmek (bájt) | Darabszám | Sum (alapértelmezett) | A beépített kiszolgáló nélküli SQL-készlethez tartozó befejezetlen SQL-kérelmek száma. </br></br> A metrika eredmény-dimenziójának használatával a végső állapot alapján szűrhet. |

### <a name="dedicated-sql-pool-metrics"></a>Dedikált SQL Pool-metrikák

Íme néhány a dedikált SQL-készletek által kibocsátott mérőszámok közül:

| **Metrika**                           | **Megjelenített név**                  | **Unit (Egység)** | **Összesítési típusok** | **Leírás**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | DWU korlátja                       | Darabszám   | Max (alapértelmezett), min, átlag | Az SQL-készlet beállított mérete |
| DWUUsed                             | Használt DWU                        | Darabszám   | Max (alapértelmezett), min, átlag | Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. DWU-korláttal mérve * DWU százalék |
| DWUUsedPercent                      | DWU használt százalék             | Százalék | Max (alapértelmezett), min, átlag | Az SQL-készleten belüli használat magas szintű ábrázolását jelöli. A CPU-százalék és az adatio-százalék közötti maximális érték alapján mérhető |
| ConnectionsBlockedByFirewall        | Tűzfal által letiltott kapcsolatok | Darabszám   | Sum (alapértelmezett)   | A tűzfalszabályok által letiltott kapcsolatok száma. Nyissa meg újra az SQL-készlethez tartozó hozzáférés-vezérlési szabályzatokat, és figyelje meg, hogy a kapcsolatok száma magas-e |
| AdaptiveCacheHitPercent             | Adaptív gyorsítótár-találatok százalékos aránya   | Százalék | Max (alapértelmezett), min, átlag | Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár találati százalékos metrikájának használatával határozhatja meg, hogy szeretné-e bővíteni a további kapacitást, vagy futtassa újra a számítási feladatokat a gyorsítótár hidrát |
| AdaptiveCacheUsedPercent            | Adaptív gyorsítótár használt százaléka  | Százalék | Max (alapértelmezett), min, átlag | Méri, hogy a számítási feladatok milyen jól használják az adaptív gyorsítótárat. Ezt a metrikát a gyorsítótár használt százalékos metrikájának használatával határozhatja meg, hogy a további kapacitást szeretné-e méretezni, vagy újra kell futtatnia a számítási feladatokat a gyorsítótár hidrát |
| LocalTempDBUsedPercent              | Helyi tempdb használt százalék    | Százalék | Max (alapértelmezett), min, átlag | Helyi tempdb kihasználtsága az összes számítási csomóponton – az értékek öt percenként vannak kibocsátva |
| MemoryUsedPercent                   | Felhasznált memória százalékos aránya          | Százalék | Max (alapértelmezett), min, átlag | Memória kihasználtsága az SQL-készlet összes csomópontján |
| CPUPercent                          | Felhasznált CPU százalékos aránya             | Százalék | Max (alapértelmezett), min, átlag | CPU-kihasználtság az SQL-készlet összes csomópontja között |
| Kapcsolatok                         | Kapcsolatok                     | Darabszám   | Sum (alapértelmezett)  | Az SQL-készletbe való teljes bejelentkezések száma |
| ActiveQueries                      | Aktív lekérdezések                 | Darabszám   | Sum (alapértelmezett)   | Az aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik. |
| QueuedQueries                      | Várólistán lévő lekérdezések                  | Darabszám   | Sum (alapértelmezett)   | Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát |
| WLGActiveQueries                   | Munkaterhelési csoport aktív lekérdezései   | Darabszám   | Sum (alapértelmezett)   | A munkaterhelés csoporton belüli aktív lekérdezések. Ha ezt a metrikát nem szűri, és a felosztatlan érték jelenik meg, akkor a rendszeren futó összes aktív lekérdezés megjelenik. |
| WLGActiveQueriesTimeouts           | Munkaterhelés-csoport lekérdezési időtúllépései   | Darabszám   | Sum (alapértelmezett)   | Túllépte az időkorlátot tartalmazó munkaterhelési csoport lekérdezéseit. A metrika által jelentett lekérdezési időtúllépések csak a lekérdezés végrehajtásának megkezdése után (nem tartalmazza a várakozási időt a zárolás vagy az erőforrás-várakozás miatt) |
| WLGQueuedQueries                   | Munkaterhelési csoport várólistán lévő lekérdezések   | Darabszám   | Sum (alapértelmezett)   | Elérte a maximális egyidejűségi korlát után várólistára helyezett kérelmek összesített számát |
| WLGAllocationBySystemPercent        | Munkaterhelés-csoport kiosztása rendszerszázalékkal | Százalék | Max (alapértelmezett), minimum, átlag, összeg | Az erőforrások százalékos kiosztása a teljes rendszerrel viszonyítva |
| WLGAllocationByEffectiveCapResourcePercent   | Munkaterhelési csoport lefoglalása az erőforrás maximális százaléka alapján | Százalék | Max (alapértelmezett), min, átlag | Megjeleníti az erőforrások százalékos kiosztását a munkaterhelési csoport tényleges maximális erőforrásához viszonyítva. Ez a mérőszám a munkaterhelés-csoport hatékony kihasználtságát biztosítja |
| WLGEffectiveCapResourcePercent      | Érvényes Cap-erőforrás százaléka  | Százalék | Max (alapértelmezett), min, átlag | A munkaterhelési csoport tényleges Cap-erőforrásának százaléka. Ha más munkaterhelés-csoportok min_percentage_resource > 0, a effective_cap_percentage_resource arányosan csökken |
| WLGEffectiveMinResourcePercent      | Effektív minimális erőforrás százaléka  | Százalék | Max (alapértelmezett), minimum, átlag, összeg | A tényleges minimális erőforrás-százalékos beállítás a szolgáltatási szint és a munkaterhelés-csoport beállításainak figyelembevételével engedélyezett. A tényleges min_percentage_resource magasabb szinten állítható be alacsonyabb szolgáltatási szinteken |

### <a name="apache-spark-pool-metrics"></a>Apache Spark készlet mérőszámai

Íme néhány a Apache Spark készletek által kibocsátott mérőszámok közül:

| **Metrika**                           | **Metrika kategóriája, megjelenítendő név**                  | **Unit (Egység)** | **Összesítési típusok** | **Leírás**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Lejárt Apache Spark alkalmazások  | Darabszám | Sum (alapértelmezett) | Befejezett Apache Spark készlet-alkalmazások száma |
| BigDataPoolAllocatedCores     | A Apache Spark készlethez lefoglalt virtuális mag száma                 | Darabszám | Max (alapértelmezett), min, átlag | Apache Spark készlethez lefoglalt virtuális mag |
| BigDataPoolAllocatedMemory    | A Apache Spark készlethez lefoglalt memória (GB) mennyisége            | Darabszám | Max (alapértelmezett), min, átlag | Lefoglalt memória Apache Spark készlethez (GB) |
| BigDataPoolApplicationsActive | Aktív Apache Spark alkalmazások | Darabszám | Max (alapértelmezett), min, átlag | Aktív Apache Spark készletbeli alkalmazások száma |

## <a name="alerts"></a>Riasztások

Jelentkezzen be a Azure Portalba, és válassza a riasztások **figyelése** lehetőséget a  >   riasztások létrehozásához.

### <a name="create-alerts"></a>Riasztások létrehozása

1. Új riasztás létrehozásához válassza az **+ új riasztási szabály** lehetőséget.

1. Adja meg azt a **riasztási feltételt** , amely megadja, hogy mikor kell tüzet adni a riasztásnak.

    > [!NOTE]
    > Ügyeljen rá, hogy a **szűrés erőforrás típusa** legördülő listában válassza az **összes** lehetőséget.

1. Adja meg a **riasztás részleteit** a riasztás konfigurálásának további megadásához.

1. Adja meg a **műveleti csoportot** annak meghatározásához, hogy ki kapja meg a riasztásokat (és hogyan).

## <a name="logs"></a>Naplók

### <a name="workspace-level-logs"></a>Munkaterület-szintű naplók

Az Azure szinapszis Analytics-munkaterületek által kibocsátott naplók a következők:

| Log Analytics tábla neve | Naplózási kategória neve                 | Leírás |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Azure szinapszis Gateway API-kérelmek. |
| SynapseRbacOperations         | SynapseRbacOperations          | Azure szinapszis szerepköralapú hozzáférés-vezérlési (SRBAC) műveletek. |

### <a name="dedicated-sql-pool-logs"></a>Dedikált SQL Pool-naplók

A dedikált SQL-készletek által kibocsátott naplók a következők:

| Log Analytics tábla neve        | Naplózási kategória neve             | Leírás |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Információk az Azure szinapszis dedikált SQL-készletében található SQL-kérelmekről/lekérdezésekről.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Információ arról, hogy a feldolgozók hogyan fejezik be a DMS lépéseit az Azure szinapszis dedikált SQL-készletéből.
| SynapseSqlPoolRequestSteps  | RequestSteps | Információk a kérés lépéseiről, amelyek egy adott SQL-kérést vagy lekérdezést alkotnak egy Azure szinapszis dedikált SQL-készletben.
| SynapseSqlPoolSqlRequests   | SqlRequests  | Információk az Azure szinapszis dedikált SQL-készletében található SQL-kérelmek/-lekérdezések lépéseinek lekérdezési eloszlásáról.
| SynapseSqlPoolWaits         | Megvárja        | A várakozási állapotokról az Azure szinapszis dedikált SQL-készletében található SQL-kérelem vagy-lekérdezés végrehajtása során felmerülő információk, beleértve a zárolásokat, és megvárja az átviteli várólistákat.

A naplókról további információt a következő információkban talál:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?view=azure-sqldw-latest&preserve-view=true)

### <a name="apache-spark-pool-log"></a>Apache Spark készlet naplója

Apache Spark készletek által kibocsátott napló:

| Log Analytics tábla neve               | Naplózási kategória neve              | Leírás                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | A befejezett Apache Spark alkalmazásokkal kapcsolatos információk |

### <a name="diagnostic-settings"></a>Diagnosztikai beállítások

Diagnosztikai beállítások használatával konfigurálhatja a nem számítási erőforrások diagnosztikai naplóit. Az erőforrás-vezérlő beállításai az alábbi funkciókkal rendelkeznek:

* Megadják a diagnosztikai naplók elküldéseinak helyét. Ilyenek például az Azure Storage-fiók, az Azure Event hub vagy a monitoring-naplók.
* Ezek határozzák meg, hogy mely naplózási kategóriákat küldik el a rendszer.
* Azt határozzák meg, hogy mennyi ideig kell megőrizni az egyes naplók kategóriáit egy Storage-fiókban.
* A nulla napos megőrzés azt jelenti, hogy a naplók örökre meg lesznek őrizve. Ellenkező esetben az érték tetszőleges számú nap lehet, 1-től 2 147 483 647-ig.
* Ha adatmegőrzési házirend van beállítva, de a naplók tárolása egy Storage-fiókban le van tiltva, az adatmegőrzési szabályzatok nem lépnek érvénybe. Ez a feltétel például akkor fordulhat elő, ha csak a Event Hubs vagy a figyelési naplók lehetőség van kiválasztva.
* Az adatmegőrzési szabályzatok naponta lesznek alkalmazva. A tartomány napjai közötti határ éjfélkor, az egyezményes világidő (UTC) szerint történik. A nap végén a rendszer törli az adatmegőrzési házirenden túli napokat. Ha például egy nap adatmegőrzési szabályzattal rendelkezik, a rendszer a tegnapi naptól kezdve törli a naplókat.

Azure Monitor diagnosztikai beállításokkal több különböző célpont számára is átirányíthatja a diagnosztikai naplókat az elemzéshez.

* **Storage-fiók**: a diagnosztikai naplókat a naplózáshoz vagy a manuális ellenőrzéshez mentse egy Storage-fiókba. A diagnosztikai beállítások segítségével megadhatja a megőrzési időt napokban.
* **Event hub**: a naplók továbbítása az Azure Event Hubsba. A naplók bekerülnek egy partneri szolgáltatásba/egyéni analitikai megoldásba, például Power BIba.
* **Log Analytics munkaterület**: a naplók elemzése log Analyticsokkal. Az Azure szinapszis és a Log Analytics integrációja a következő esetekben hasznos:
  * Összetett lekérdezéseket szeretne írni az Azure szinapszis által Log Analyticsba közzétett mérőszámok gazdag készletén. A lekérdezésekhez Azure Monitor használatával hozhat létre egyéni riasztásokat.
  * A munkaterületek között szeretne figyelni. Több munkaterületről is átirányíthat adatait egyetlen Log Analytics munkaterületre.

Használhat olyan Storage-fiókot vagy Event hub-névteret is, amely nem szerepel a naplókat kibocsátó erőforrás előfizetésében. A beállítást konfiguráló felhasználónak megfelelő Azure szerepköralapú hozzáférés-vezérlési (Azure RBAC) hozzáféréssel kell rendelkeznie mindkét előfizetéshez.

#### <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

Hozzon létre vagy adjon hozzá diagnosztikai beállításokat a munkaterülethez, a dedikált SQL-készlethez vagy a Apache Spark készlethez.

1. A portálon lépjen a figyelés elemre. Válassza a **Beállítások**  >  **diagnosztikai beállítások** lehetőséget.

1. Válassza ki azt a szinapszis-munkaterületet, dedikált SQL-készletet vagy Apache Spark-készletet, amelyhez diagnosztikai beállítást kíván létrehozni.

1. Ha nem léteznek diagnosztikai beállítások a kiválasztott munkaterületen, a rendszer kéri, hogy hozzon létre egy beállítást. Kattintson **a diagnosztika bekapcsolása** elemre.

   Ha vannak meglévő diagnosztikai beállítások a munkaterületen, látni fogja az erőforráson már konfigurált beállítások listáját. Válassza a **Diagnosztikai beállítások megadása** lehetőséget.

1. Adja meg a beállítás nevét, válassza a **küldés log Analytics** lehetőséget, majd válasszon ki egy munkaterületet **log Analytics munkaterületről**.

    > [!NOTE]
    > Mivel az Azure-tábla nem rendelkezhet több mint 500 oszloppal **, javasoljuk, hogy az** _erőforrás-specifikus módot_ válassza. További információ: AzureDiagnostics- [naplók referenciája](/azure-monitor/reference/tables/azurediagnostics#additionalfields-column).

1. Kattintson a **Mentés** gombra.

Néhány pillanat elteltével megjelenik az új beállítás a munkaterület, a dedikált SQL-készlet vagy a Apache Spark készlet beállításainak listájában. A rendszer a diagnosztikai naplókat az adott munkaterületre továbbítja, amint új esemény-adatforrások jönnek létre. Akár 15 percig is eltarthat egy esemény kibocsátása, és amikor megjelenik a Log Analyticsban.

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével kapcsolatos további információkért tekintse meg a [folyamatok figyelése a szinapszis Studióban](how-to-monitor-pipeline-runs.md) című cikket. 

A Apache Spark-alkalmazások figyelésével kapcsolatos további információkért lásd: [Apache Spark alkalmazások figyelése a szinapszis Studio-](apache-spark-applications.md) cikkben.

Az SQL-kérelmek figyelésével kapcsolatos további információkért tekintse meg az [SQL-kérelmek figyelése a szinapszis Studióban](how-to-monitor-sql-requests.md) című cikket.
