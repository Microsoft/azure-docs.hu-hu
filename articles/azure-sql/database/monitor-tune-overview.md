---
title: Monitorozás és teljesítmény-finomhangolás
description: A Azure SQL Database és az Azure SQL felügyelt példányának monitorozási és teljesítmény-hangolási képességeinek és módszertanának áttekintése.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 09/30/2020
ms.openlocfilehash: 6b56da68b10bc40304097fbe9eeaf200d422b663
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592698"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database-and-azure-sql-managed-instance"></a>Monitorozás és a teljesítmény finomhangolása az Azure SQL Database-ben és a felügyelt Azure SQL-példányban
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példányában lévő adatbázisok teljesítményének figyeléséhez először figyelje a számítási feladatok által használt CPU-és IO-erőforrásokat az adott szolgáltatási szint és teljesítmény szintjének kiválasztása során kiválasztott adatbázis-teljesítmény szintjéhez képest. Ennek elvégzéséhez Azure SQL Database és az Azure SQL felügyelt példánya olyan erőforrás-metrikákat bocsát ki, amelyek megtekinthetők a Azure Portal vagy a következő SQL Server felügyeleti eszközök valamelyikével: [Azure Data Studio](/sql/azure-data-studio/what-is) vagy [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

Azure SQL Database számos adatbázis-tanácsadót biztosít az intelligens teljesítmény-hangolási javaslatok és az Automatikus hangolási beállítások biztosításához a teljesítmény javítása érdekében. Emellett Lekérdezési terheléselemző megjeleníti a legtöbb CPU-és IO-használatért felelős lekérdezések részleteit az önálló és a készletezett adatbázisok esetében.

A Azure SQL Database és az Azure SQL felügyelt példánya fejlett monitorozási és hangolási képességeket biztosít a mesterséges intelligencia számára, hogy segítséget nyújtson az adatbázisok és megoldások teljesítményének elhárításában és maximalizálásában. Dönthet úgy, hogy a [Intelligent Insights](intelligent-insights-overview.md) és más adatbázis-erőforrás-naplók és-metrikák [adatfolyam-exportálását](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) több, a felhasználás és az elemzés céljára szolgáló célhelyre konfigurálja, különösen az [SQL Analytics](../../azure-monitor/insights/azure-sql.md)használatával. A Azure SQL Analytics egy fejlett felhőalapú figyelési megoldás, amely az összes adatbázis teljesítményének figyelésére használható nagy léptékű és több előfizetésben egyetlen nézetben. Az exportálható naplók és metrikák listáját az [Exportálás diagnosztikai telemetria](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#diagnostic-telemetry-for-export) című témakörben tekintheti meg.

A SQL Server saját monitorozási és diagnosztikai funkciókkal rendelkezik, amelyek SQL Database és az SQL felügyelt példányainak kiaknázását, például a [lekérdezési tárolót](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) és a [dinamikus felügyeleti nézeteket (DMV)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)használják. A különböző teljesítménnyel kapcsolatos problémák figyeléséhez tekintse meg a [figyelés DMV használatával](monitoring-with-dmvs.md) szkripteket.

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>A Azure Portal funkcióinak figyelése és finomhangolása

A Azure Portal Azure SQL Database és az Azure SQL felügyelt példánya biztosítja az erőforrás-metrikák figyelését. A Azure SQL Database adatbázis-tanácsadókat biztosít, és Lekérdezési terheléselemző a lekérdezés-hangolási javaslatokat és a lekérdezési teljesítmény elemzését nyújtja. A Azure Portalban engedélyezheti az automatikus finomhangolást a [logikai SQL-kiszolgálók](logical-servers.md) és az önálló és készletezett adatbázisok számára.

> [!NOTE]
> A rendkívül alacsony használatú adatbázisok a portálon kevesebb, mint tényleges használattal rendelkezhetnek. A telemetria kiváltásának módja, ha egy dupla értéket a legközelebbi egész számra konvertál, a 0,5-nál kisebb használati összegek pedig 0-ra lesznek kerekítve, ami a kibocsátott telemetria részletességének csökkenését eredményezi. Részletekért lásd: [az alacsony adatbázis és a rugalmas készlet mérőszámai nullára kerekítve](#low-database-and-elastic-pool-metrics-rounding-to-zero).

### <a name="azure-sql-database-and-azure-sql-managed-instance-resource-monitoring"></a>Azure SQL Database és az Azure SQL felügyelt példányának erőforrás-figyelése

A **mérőszámok** nézetben gyorsan figyelheti a Azure Portal különböző erőforrás-metrikákat. Ezek a metrikák lehetővé teszik annak megtekintését, hogy egy adatbázis eléri-e a processzor, a memória vagy az IO-erőforrások 100%-át. A magas DTU vagy processzor-százalékos érték, valamint a magas i/o-érték azt jelzi, hogy a számítási feladatokhoz több CPU-vagy IO-erőforrásra lehet szükség. Azt is jelezheti, hogy milyen lekérdezéseket kell optimalizálni.

  ![Erőforrás-metrikák](./media/monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors-in-azure-sql-database"></a>Adatbázis-tanácsadók a Azure SQL Database

A Azure SQL Database olyan [adatbázis-tanácsadókat](database-advisor-implement-performance-recommendations.md) tartalmaz, amelyek teljesítmény-hangolási javaslatokat biztosítanak az önálló és a készletezett adatbázisokhoz. Ezek a javaslatok a Azure Portal és a [PowerShell](/powershell/module/az.sql/get-azsqldatabaseadvisor)használatával is elérhetők. Az [automatikus hangolást](automatic-tuning-overview.md) is engedélyezheti, hogy a Azure SQL Database automatikusan implementálja ezeket a hangolási javaslatokat.

### <a name="query-performance-insight-in-azure-sql-database"></a>Lekérdezési terheléselemző a Azure SQL Database

A [lekérdezési terheléselemző](query-performance-insight-use.md) megjeleníti a teljesítményt a leggyakrabban használt és a leghosszabb ideig futó lekérdezések Azure Portalában az önálló és a készletezett adatbázisok esetében.

### <a name="low-database-and-elastic-pool-metrics-rounding-to-zero"></a>Alacsony adatbázis-és rugalmas készlet-mérőszámok nullára kerekítve

A rendkívül alacsony kihasználtságú adatbázisok a portálon kevesebb, mint tényleges használattal rendelkezhetnek. 2020 Mivel a telemetria-t egy dupla értéknek a legközelebbi egész számra való konvertálásakor kell kiállítani, a 0,5-nál kisebb használati összegek pedig 0-ra lesznek kerekítve, ami a kibocsátott telemetria részletességének csökkenését eredményezi.

Például: Vegyünk egy 1 perces ablakot a következő négy adatponttal: 0,1, 0,1, 0,1, 0,1, ezek az alacsony értékek 0, 0, 0, 0 értékre vannak kerekítve, és átlagosan 0 értéket mutatnak. Ha az adatpontok bármelyike meghaladja a 0,5-as értéket, például: 0,1, 0,1, 0,9, 0,1, azok 0, 0, 1, 0 értékre vannak kerekítve, és egy átlagos 0,25 mutatnak.

Érintett adatbázis-metrikák:
- cpu_percent
- log_write_percent
- workers_percent
- sessions_percent
- physical_data_read_percent
- dtu_consumption_percent2
- xtp_storage_percent

Érintett rugalmas készlet mérőszámai:
- cpu_percent
- physical_data_read_percent
- log_write_percent
- memory_usage_percent
- data_storage_percent
- peak_worker_percent
- peak_session_percent
- xtp_storage_percent
- allocated_data_storage_percent


## <a name="generate-intelligent-assessments-of-performance-issues"></a>Teljesítménybeli problémák intelligens értékelésének előállítása

A Azure SQL Database és az Azure SQL felügyelt példányának [Intelligent Insights](intelligent-insights-overview.md) a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligencia használatával, és felismeri a gyenge teljesítményt okozó zavaró eseményeket. A Intelligent Insights automatikusan észleli a teljesítménnyel kapcsolatos problémákat az adatbázisokban a lekérdezés végrehajtásának várakozási ideje, hibája vagy időtúllépése alapján. Az észlelést követően a rendszer részletes elemzést végez, amely egy erőforrás-naplót (SQLInsights) hoz létre a [problémák intelligens értékelésével](intelligent-insights-troubleshoot-performance.md). Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma okának elemzését, és ahol lehetséges, a teljesítménnyel kapcsolatos javításokra vonatkozó javaslatokat tartalmaz.

A Intelligent Insights az Azure beépített intelligenciának egyedülálló funkciója, amely a következő értéket biztosítja:

- Proaktív figyelés
- Testreszabott teljesítmény-felismerés
- Az adatbázis-teljesítmény romlásának korai észlelése
- Az észlelt problémák kiváltó okának elemzése
- Teljesítmény-javító javaslatok
- Több százezer adatbázisra kibővíthető képesség
- Pozitív hatással van a DevOps-erőforrásokra és a teljes tulajdonlási díjakra

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>Metrikák és erőforrás-naplók adatfolyam-exportálásának engedélyezése

Engedélyezheti és konfigurálhatja a [diagnosztikai telemetria adatfolyam-exportálását](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) több célhelyre, beleértve a Intelligent Insights erőforrás-naplót is. Az [SQL Analytics](../../azure-monitor/insights/azure-sql.md) és egyéb funkciók használatával felhasználhatja ezt a további diagnosztikai telemetria a teljesítményproblémák azonosításához és megoldásához.

A diagnosztikai beállításokat a mérőszámok és az erőforrás-naplók a következő Azure-erőforrások egyikére történő továbbításához kell konfigurálni: önálló adatbázisok, készletezett adatbázisok, rugalmas készletek, felügyelt példányok és példány-adatbázisok.

### <a name="log-analytics-workspace-in-azure-monitor"></a>Log Analytics munkaterület a Azure Monitor

A metrikák és az erőforrás-naplók a [Azure Monitor log Analytics munkaterületére](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)is továbbíthatók. Az itt továbbított adatfolyamokat az [SQL Analytics](../../azure-monitor/insights/azure-sql.md)is felhasználhatja, ami egy kizárólag Felhőbeli figyelési megoldás, amely a teljesítményadatokat, a riasztásokat és a kockázatcsökkentő ajánlásokat tartalmazó adatbázisok intelligens figyelését teszi lehetővé. A Log Analytics munkaterületre továbbított adatok elemezhetők más figyelési adatokkal, és lehetővé teszik más Azure Monitor funkciók, például a riasztások és a vizualizációk kihasználása is.

### <a name="azure-event-hubs"></a>Azure Event Hubs

A metrikákat és az erőforrás-naplókat továbbíthatja az [Azure Event Hubsba](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs). Adatfolyam-diagnosztikai telemetria az Event hubok számára a következő funkciók biztosításához:

- **Naplókat streamelhet külső naplózási és telemetriarendszerekbe**

  Továbbítsa az összes mérőszámot és erőforrás-naplót egy adott esemény központba egy harmadik féltől származó SIEM-vagy log Analytics-eszközre.
- **Egyéni telemetria- és naplózási platformot hozhat létre**

  Az Event hubok nagymértékben méretezhető közzétételi-előfizetési természete lehetővé teszi a metrikák és erőforrás-naplók rugalmas betöltését egy egyéni telemetria platformra. A részletekért lásd: [globális méretű telemetria platform tervezése és méretezése az Azure Event Hubsban](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
- **Megtekintheti a szolgáltatások állapotát az adatoknak a Power BI-ba történő streamelésével**

  Event Hubs, Stream Analytics és Power BI használatával alakítsa át diagnosztikai adatait az Azure-szolgáltatások közel valós idejű elemzéséhez. A megoldás részleteiért tekintse meg a [stream Analytics és Power bi: valós idejű elemzési irányítópultot a folyamatos adattovábbításhoz](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .

### <a name="azure-storage"></a>Azure Storage

Stream-metrikák és erőforrás-naplók az [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)-ba. Az Azure Storage használatával nagy mennyiségű diagnosztikai telemetria archiválható az előző két folyamatos átviteli lehetőség díjainak töredékéért.

## <a name="use-extended-events"></a>Kiterjesztett események használata 

Emellett a speciális figyeléshez és hibaelhárításhoz is használhatja a SQL Server [kiterjesztett eseményeit](/sql/relational-databases/extended-events/extended-events) . A kiterjesztett események architektúrája lehetővé teszi a felhasználók számára, hogy a teljesítménnyel kapcsolatos problémák elhárításához vagy azonosításához szükséges mennyiségű vagy kevés adatot gyűjtsenek. További információ a Azure SQL Database kiterjesztett eseményeinek használatáról: [kiterjesztett események a Azure SQL Databaseban](xevent-db-diff-from-svr.md).

## <a name="next-steps"></a>Következő lépések

- Az önálló és a készletezett adatbázisokra vonatkozó intelligens teljesítménnyel kapcsolatos javaslatokról az [adatbázis-tanácsadó teljesítményével kapcsolatos javaslatok](database-advisor-implement-performance-recommendations.md)című témakörben olvashat bővebben.
- Az adatbázis teljesítményének automatikus és a teljesítménnyel kapcsolatos problémák kiváltó okának elemzésével kapcsolatos további információkért lásd: [Azure SQL Intelligent Insights](intelligent-insights-overview.md).