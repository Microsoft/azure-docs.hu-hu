---
title: Metrikák és erőforrás-naplók adatfolyam-exportálásának konfigurálása
description: Megtudhatja, hogyan állíthatja be a metrikák és erőforrás-naplók adatfolyam-exportálását, beleértve az Azure SQL Database és az Azure SQL felügyelt példányának intelligens diagnosztikai elemzését, hogy az Ön által választott célra az erőforrás-kihasználtsággal és a lekérdezés-végrehajtási statisztikákkal kapcsolatos információkat tárolja.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: seoapril2019
ms.devlang: sqldbrb=2
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 04/06/2020
ms.openlocfilehash: 1de2c1ff02c799d04f2ab2c81e83dda5001a531f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592722"
---
# <a name="configure-streaming-export-of-azure-sql-database-and-sql-managed-instance-diagnostic-telemetry"></a>Azure SQL Database és SQL felügyelt példányok diagnosztikai telemetria adatfolyam-exportálásának konfigurálása
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ebből a cikkből megtudhatja, hogy Azure SQL Database milyen teljesítmény-metrikákat és erőforrás-naplókat tud exportálni az elemzéshez több célhelyre. Megtudhatja, hogyan konfigurálhatja a diagnosztikai telemetria adatfolyam-exportálását a Azure Portal, a PowerShell, az Azure CLI, a REST API és a Azure Resource Manager sablonok segítségével.

Emellett megismerheti azokat a célhelyeket is, amelyekkel a diagnosztikai telemetria továbbíthatja, és ezek közül választhat. A cél beállításai a következők:

- [Log Analytics és SQL Analytics](#stream-into-sql-analytics)
- [Event Hubs](#stream-into-event-hubs)
- [Azure Storage](#stream-into-azure-storage)

## <a name="diagnostic-telemetry-for-export"></a>Az Exportálás diagnosztikai telemetria

Az exportálható diagnosztikai telemetria közül a legfontosabb a Intelligent Insights (SQLInsights) napló. A [Intelligent Insights](intelligent-insights-overview.md) a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot a mesterséges intelligencián, és felismeri a gyenge teljesítményt okozó zavaró eseményeket. Az észlelés után a rendszer részletes elemzést végez, amely egy Intelligent Insights naplót hoz létre a probléma intelligens értékelésével. Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma okának elemzését, és ahol lehetséges, a teljesítménnyel kapcsolatos javításokra vonatkozó javaslatokat tartalmaz. A napló adatfolyam-exportálását úgy kell konfigurálni, hogy megtekinthesse annak tartalmát.

A Intelligent Insights napló exportálásán kívül számos teljesítmény-mérőszámot és további adatbázis-naplókat is exportálhat. A következő táblázat azokat a teljesítménymutatókat és erőforrás-naplókat ismerteti, amelyeket a különböző célhelyek egyikére konfigurálhat a streaming-exportáláshoz. Ez a diagnosztikai telemetria konfigurálható önálló adatbázisok, rugalmas készletek és készletezett adatbázisok, valamint felügyelt példányok és példány-adatbázisok számára.

| Diagnosztikai telemetria adatbázisok számára | Azure SQL Database támogatás | Az Azure SQL felügyelt példányainak támogatása |
| :------------------- | ----- | ----- |
| [Alapszintű mérőszámok](#basic-metrics): tartalmazza a DTU/CPU-arányt, a DTU/CPU-korlátot, a fizikai adatok olvasási százalékos arányát, a napló írási százalékos arányát, a sikeres/sikertelen/letiltott/blokkolt, a munkamenetek százalékos arányát, a dolgozók százalékos arányát, a tárterületet | Igen | Nem |
| [A példány és az alkalmazás speciális](#advanced-metrics): tartalmazza a tempdb rendszeradatbázis-információit és a naplófájlok méretét, valamint a tempdb százalékos naplófájlját. | Igen | Nem |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): a lekérdezési futtatókörnyezet statisztikáit, például a CPU-használat és a lekérdezés időtartamára vonatkozó statisztikai adatokat tartalmazza. | Igen | Yes |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): a lekérdezési várakozási statisztikával kapcsolatos információkat tartalmaz (a lekérdezéseket várta), például a processzort, a naplót és a zárolást. | Igen | Yes |
| [Hibák](#errors-dataset): az adatbázis SQL-hibáiról tartalmaz információkat. | Igen | Yes |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): információ arról, hogy mennyi időt töltöttek egy adatbázis a különböző várakozási típusoknál. | Igen | Nem |
| [Időtúllépések](#time-outs-dataset): az adatbázis-időtúllépésekkel kapcsolatos információkat tartalmaz. | Igen | Nem |
| [Blokkok](#blockings-dataset): az adatbázis-események blokkolásával kapcsolatos információkat tartalmaz. | Igen | Nem |
| [Holtpontok](#deadlocks-dataset): az adatbázis holtpont eseményeivel kapcsolatos információkat tartalmaz. | Igen | Nem |
| [AutomaticTuning](#automatic-tuning-dataset): az adatbázis Automatikus hangolási javaslataival kapcsolatos információkat tartalmaz. | Igen | Nem |
| [SQLInsights](#intelligent-insights-dataset): Intelligent Insightst tartalmaz egy adatbázis teljesítményében. További információ: [Intelligent Insights](intelligent-insights-overview.md). | Igen | Yes |

> [!NOTE]
> A diagnosztikai beállítások nem konfigurálhatók a **rendszeradatbázisokhoz**, például a Master, a msdb, a Model, az Resource és a tempdb adatbázisokhoz.

## <a name="streaming-export-destinations"></a>Adatfolyam-exportálási célhelyek

Ez a diagnosztikai telemetria az alábbi Azure-erőforrások egyikére továbbítható elemzés céljából.

- **[Log Analytics munkaterület](#stream-into-sql-analytics)**:

  Az [SQL Analytics](../../azure-monitor/insights/azure-sql.md)az [log Analytics-munkaterületre](../../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) továbbított adatfolyamokat is felhasználhatja. Az SQL Analytics egy Felhőbeli figyelési megoldás, amely a teljesítményadatokat, a riasztásokat és a kockázatcsökkentő javaslatokat tartalmazó adatbázisok intelligens figyelését teszi lehetővé. A Log Analytics munkaterületre továbbított adatok elemezhetők más megfigyelési adatokkal, és lehetővé teszik más Azure Monitor funkciók, például riasztások és vizualizációk kihasználása
- **[Azure Event Hubs](#stream-into-event-hubs)**:

  Az [Azure Event hub](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)-ba továbbított adatfolyamok a következő funkciókat biztosítják:

  - **Stream-naplók harmadik féltől származó naplózási és telemetria rendszerekhez**: az összes metrika és erőforrás-napló továbbítása egyetlen esemény központba az adatcsatornán keresztül, harmadik féltől származó Siem-vagy log Analytics-eszközre.
  - **Hozzon létre egy egyéni telemetria-és naplózási platformot**: az Event hubok nagymértékben méretezhető közzétételi és előfizetési felépítése lehetővé teszi, hogy rugalmasan betöltse a metrikákat és az erőforrás-naplókat egy egyéni telemetria platformra. A részletekért lásd: [globális méretű telemetria platform tervezése és méretezése az Azure Event Hubsban](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) .
  - A **szolgáltatás állapotának megtekintése az adattovábbítás Power bi**: Event Hubs, Stream Analytics és Power bi használatával alakítsa át a diagnosztikai adatait az Azure-szolgáltatások közel valós idejű elemzéséhez. A megoldás részleteiért tekintse meg a [stream Analytics és Power bi: valós idejű elemzési irányítópultot a folyamatos adattovábbításhoz](../../stream-analytics/stream-analytics-power-bi-dashboard.md) .
- **[Azure Storage](#stream-into-azure-storage)**:

  Az [Azure Storage](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) -ba továbbított adatok lehetővé teszik, hogy nagy mennyiségű diagnosztikai telemetria archiváljon az előző két folyamatos átviteli lehetőségért járó költségek töredékéért.

Ez a diagnosztikai telemetria a következő célhelyek egyikére is felhasználható az erőforrás-kihasználtság és a lekérdezés-végrehajtási statisztika méréséhez, így egyszerűbbé válik a teljesítmény monitorozása.

![A diagram számos SQL-adatbázist és-adatbázist mutat be a felügyelt példányokban, amelyek telemetria küldenek a Azure Diagnosticsnak, amelyek információkat küldenek a Azure SQL Analytics, az Event hub és a Storage szolgáltatásnak.](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/architecture.png)

## <a name="enable-and-configure-the-streaming-export-of-diagnostic-telemetry"></a>A diagnosztikai telemetria adatfolyam-exportálásának engedélyezése és konfigurálása

Az alábbi módszerek valamelyikével engedélyezheti és felügyelheti a metrikák és a diagnosztikai telemetria naplózását:

- Azure Portal
- PowerShell
- Azure CLI
- Azure Monitor REST API
- Azure Resource Manager-sablon

> [!NOTE]
> A biztonsági telemetria naplózási naplózásának engedélyezéséhez tekintse [meg az adatbázis naplózásának beállítása](./auditing-overview.md#setup-auditing) és a [naplók naplózása Azure monitor naplókban és az Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242)című témakört.

## <a name="configure-the-streaming-export-of-diagnostic-telemetry"></a>A diagnosztikai telemetria adatfolyam-exportálásának konfigurálása

A Azure Portal **diagnosztikai beállítások** menüjében engedélyezheti és konfigurálhatja a diagnosztikai telemetria folyamatos átvitelét. Emellett a PowerShell, az Azure CLI, a [REST API](/rest/api/monitor/diagnosticsettings)és a [Resource Manager-sablonok](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md) használatával is konfigurálhatja a diagnosztikai telemetria streamingjét. A következő célhelyek megadásával továbbíthatja a diagnosztikai telemetria: Azure Storage, Azure Event Hubs és Azure Monitor naplók.

> [!IMPORTANT]
> A diagnosztikai telemetria adatfolyam-exportálásának alapértelmezett értéke nincs engedélyezve.

A következő lapok egyikének kiválasztásával részletes útmutatást találhat a diagnosztikai telemetria adatfolyam-exportálásának konfigurálásához a Azure Portalban, valamint parancsfájlokat a PowerShell és az Azure CLI használatával történő végrehajtásához.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

### <a name="elastic-pools-in-azure-sql-database"></a>Rugalmas készletek Azure SQL Database

A következő diagnosztikai telemetria gyűjtéséhez beállíthatja a rugalmas készlet erőforrásait:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Rugalmas készlet** | Az [alapszintű mérőszámok](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) a EDTU/CPU-hányadot, a EDTU/CPU-korlátot, a fizikai adatok olvasási százalékos arányát, a napló írási arányát, a munkamenetek százalékos arányát, a feldolgozók százalékos arányát, a tárterületet, a tárolási |

A rugalmas készletek és a készletezett adatbázisok diagnosztikai telemetria folyamatos konfigurálásához külön kell konfigurálnia az egyeseket:

- Diagnosztikai telemetria a rugalmas készlethez való továbbításának engedélyezése
- Diagnosztikai telemetria folyamatos átvitelének engedélyezése a rugalmas készletben található minden adatbázishoz

A rugalmas készlet tárolója saját telemetria rendelkezik, amely külön az egyes készletezett adatbázisok telemetria.

Az alábbi lépéseket követve engedélyezheti a diagnosztikai telemetria folyamatos átvitelét a rugalmas készlet erőforrásaihoz:

1. Nyissa meg a **rugalmas készlet** erőforrását Azure Portal.
2. Válassza a **diagnosztikai beállítások** lehetőséget.
3. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.

   ![Diagnosztika engedélyezése rugalmas készletekhez](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-enable.png)

4. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
5. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub** felé, vagy **Küldés log Analytics**.
6. A log Analytics esetében válassza a **Konfigurálás** lehetőséget, és hozzon létre egy új munkaterületet az **+ Új munkaterület létrehozása** lehetőség kiválasztásával, vagy válasszon egy meglévő munkaterületet.
7. Jelölje be a rugalmas készlet diagnosztikai telemetria jelölőnégyzetét: **alapszintű** mérőszámok.
   ![Diagnosztika konfigurálása rugalmas készletekhez](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-elasticpool-selection.png)

8. Kattintson a **Mentés** gombra.
9. Emellett a következő szakaszban ismertetett lépések végrehajtásával konfigurálhatja a diagnosztikai telemetria a figyelni kívánt rugalmas készletben található minden adatbázishoz.

> [!IMPORTANT]
> A rugalmas készlet diagnosztikai telemetria konfigurálása mellett a rugalmas készletben lévő minden adatbázishoz is be kell állítania a diagnosztikai telemetria.

### <a name="databases-in-azure-sql-database"></a>Adatbázisok Azure SQL Database

Beállíthat egy adatbázis-erőforrást a következő diagnosztikai telemetria gyűjtéséhez:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Egy vagy készletezett adatbázis** | Az [alapszintű mérőszámok](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#basic-metrics) a DTU százalékos arányát, a felhasznált DTU, a DTU korlátot, a CPU-százalékot, a fizikai adatok olvasási százalékos arányát, a napló írási százalékát, a sikeres/sikertelen/letiltott tűzfal-kapcsolatok, a munkamenetek százalékos arányát, a feldolgozók százalékos arányát, a tárterületet |

Az alábbi lépéseket követve engedélyezheti a diagnosztikai telemetria egyetlen vagy készletezett adatbázishoz való továbbítását:

1. Nyissa meg az Azure **SQL Database** -erőforrást.
2. Válassza a **diagnosztikai beállítások** lehetőséget.
3. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez. Legfeljebb három párhuzamos kapcsolatot hozhat létre a stream diagnosztikai telemetria.
4. Válassza a **diagnosztikai beállítás hozzáadása** lehetőséget a diagnosztikai információk párhuzamos átvitelének több erőforráshoz való konfigurálásához.

   ![Diagnosztika engedélyezése egyetlen és készletezett adatbázisokhoz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-enable.png)

5. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
6. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub** felé, vagy **Küldés log Analytics**.
7. A standard, eseményvezérelt figyelési felület esetén jelölje be a következő jelölőnégyzeteket az adatbázis-diagnosztikai naplók telemetria: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **hibák**, **DatabaseWaitStatistics**, **időtúllépések**, **blokkok** és **holtpontok**.
8. A speciális, egyperces figyelési élmény érdekében jelölje be az **alapszintű** mérőszámok jelölőnégyzetét.

   ![Diagnosztika konfigurálása Azure SQL Databasehoz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-sql-selection.png)
9. Kattintson a **Mentés** gombra.
10. Ismételje meg ezeket a lépéseket minden figyelni kívánt adatbázis esetében.

> [!TIP]
> Ismételje meg ezeket a lépéseket minden figyelni kívánt önálló és készletezett adatbázisra vonatkozóan.

### <a name="instances-in-azure-sql-managed-instance"></a>Példányok az Azure SQL felügyelt példányában

A következő diagnosztikai telemetria gyűjtéséhez beállíthatja a felügyelt példányok erőforrásait:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Felügyelt példány** | A [ResourceUsageStats](#resource-usage-stats-for-managed-instances) virtuális mag-darabszámot, átlagos CPU-százalékot, i/o-kérelmeket, írási/olvasási, foglalt tárolóhelyet és felhasznált tárolóhelyet tartalmaz. |

A felügyelt példányok és példányok adatbázisaihoz tartozó diagnosztikai telemetria konfigurálásához külön kell konfigurálnia a következőket:

- Diagnosztikai telemetria folyamatos átvitelének engedélyezése felügyelt példányhoz
- Diagnosztikai telemetria az egyes példány-adatbázisokhoz való továbbításának engedélyezése

A felügyelt példány tárolójának saját telemetria kell elkülönítenie az egyes példányok adatbázisának telemetria.

A felügyelt példányok erőforrásának diagnosztikai telemetria engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg Azure Portal **felügyelt példány** erőforrását.
2. Válassza a **diagnosztikai beállítások** lehetőséget.
3. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.

   ![Diagnosztika engedélyezése felügyelt példányhoz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-enable.png)

4. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
5. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub** felé, vagy **Küldés log Analytics**.
6. A log Analytics esetében válassza a **Konfigurálás** lehetőséget, és hozzon létre egy új munkaterületet az **+ Új munkaterület létrehozása** lehetőség kiválasztásával, vagy használjon egy meglévő munkaterületet.
7. Jelölje be a példány diagnosztikai telemetria: **ResourceUsageStats** jelölőnégyzetet.

   ![Diagnosztika konfigurálása felügyelt példányhoz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-container-mi-selection.png)

8. Kattintson a **Mentés** gombra.
9. Emellett a következő szakaszban ismertetett lépéseket követve konfigurálja a diagnosztikai telemetria adatfolyam-továbbítását a felügyelt példányban található minden példány-adatbázishoz.

> [!IMPORTANT]
> A felügyelt példányok diagnosztikai telemetria konfigurálása mellett az egyes példány-adatbázisok diagnosztikai telemetria is konfigurálnia kell.

### <a name="databases-in-azure-sql-managed-instance"></a>Az Azure SQL felügyelt példányaiban található adatbázisok

Beállíthat egy példány-adatbázis-erőforrást a következő diagnosztikai telemetria gyűjtéséhez:

| Erőforrás | Telemetria figyelése |
| :------------------- | ------------------- |
| **Példány-adatbázis** | A [ResourceUsageStats](#resource-usage-stats-for-managed-instances) virtuális mag-darabszámot, átlagos CPU-százalékot, i/o-kérelmeket, írási/olvasási, foglalt tárolóhelyet és felhasznált tárolóhelyet tartalmaz. |

Az alábbi lépéseket követve engedélyezheti a diagnosztikai telemetria a példány-adatbázishoz való továbbítását:

1. Nyissa meg a **példány adatbázis** -erőforrását a felügyelt példányon belül.
2. Válassza a **diagnosztikai beállítások** lehetőséget.
3. Jelölje be **a diagnosztika bekapcsolása** , ha nem létezik korábbi beállítás, vagy válassza a **beállítás szerkesztése** lehetőséget egy korábbi beállítás szerkesztéséhez.
   - Akár három (3) párhuzamos kapcsolatot is létrehozhat a stream diagnosztikai telemetria.
   - Válassza a **+ diagnosztikai beállítások hozzáadása** lehetőséget a diagnosztikai információk párhuzamos átvitelének több erőforráshoz való konfigurálásához.

   ![Diagnosztika engedélyezése példány-adatbázisokhoz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-enable.png)

4. Adja meg a saját hivatkozáshoz tartozó beállítás nevét.
5. Válasszon ki egy célként megadott erőforrást a folyamatos átviteli diagnosztikai adatokhoz: **archiválás a Storage-fiókba**, adatfolyam küldése az **Event hub** felé, vagy **Küldés log Analytics**.
6. Jelölje be az adatbázis-diagnosztikai telemetria: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** és **hibák** jelölőnégyzetét.
   ![Diagnosztika konfigurálása példány-adatbázisokhoz](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/diagnostics-settings-database-mi-selection.png)
7. Kattintson a **Mentés** gombra.
8. Ismételje meg ezeket a lépéseket minden figyelni kívánt példány-adatbázis esetében.

> [!TIP]
> Ismételje meg ezeket a lépéseket minden figyelni kívánt példány-adatbázis esetében.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatott, de a jövőbeli fejlesztés az az. SQL modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A metrikák és a diagnosztika naplózása a PowerShell használatával engedélyezhető.

- A metrikák és erőforrás-naplók tárolási fiókban való tárolásának engedélyezéséhez használja a következő parancsot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
  ```

  A Storage-fiók azonosítója a cél Storage-fiók erőforrás-azonosítója.

- A metrikák és az erőforrás-naplók egy Event hubhoz való továbbításának engedélyezéséhez használja a következő parancsot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
  ```

  Az Azure Service Bus szabályazonosítója (rule ID) egy sztring az alábbi formátumban:

  ```powershell
  {service bus resource ID}/authorizationrules/{key name}
  ```

- A metrikák és az erőforrás-naplók Log Analytics munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

  ```powershell
  Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
  ```

- A Log Analytics-munkaterület erőforrás-azonosítóját (ResourceId) az alábbi paranccsal kérheti le:

  ```powershell
  (Get-AzOperationalInsightsWorkspace).ResourceId
  ```

Ezeket a paramétereket kombinálhatja is, ha több kimeneti eredményt szeretne kapni.

**Több Azure-erőforrás konfigurálása**

Több előfizetés támogatásához használja a PowerShell-parancsfájlt az [Azure erőforrás-metrikák naplózásának engedélyezése a PowerShell használatával](/archive/blogs/msoms/enable-azure-resource-metrics-logging-using-powershell).

Adja meg a munkaterület erőforrás-AZONOSÍTÓját \<$WSID\> paraméterként a parancsfájl végrehajtásához `Enable-AzureRMDiagnostics.ps1` , hogy több erőforrásról küldje el a munkaterületre a diagnosztikai adatok küldését.

- A diagnosztikai adatai célhelyének a munkaterület-AZONOSÍTÓjának lekéréséhez \<$WSID\> használja a következő parancsfájlt:

  ```powershell
  $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
  .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
  ```

  Cserélje le az értékét \<subID\> az előfizetés-azonosítóra, \<RG_NAME\> az erőforráscsoport nevére és a \<WS_NAME\> munkaterület nevére.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI használatával engedélyezheti a metrikákat és a diagnosztikai naplózást.

> [!IMPORTANT]
> A diagnosztikai naplózást engedélyező parancsfájlok az Azure CLI 1.0-s verziójában támogatottak. Az Azure CLI 2.0-s verzió jelenleg nem támogatott.

- Ha engedélyezni szeretné a metrikák és erőforrás-naplók tárolását egy Storage-fiókban, használja a következő parancsot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
  ```

  A Storage-fiók azonosítója a cél Storage-fiók erőforrás-azonosítója.

- Ha engedélyezni szeretné a metrikák és erőforrás-naplók továbbítását az Event hub-ba, használja a következő parancsot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
  ```

  A Service Bus szabály azonosítója a következő formátumú karakterlánc:

  ```azurecli-interactive
  {service bus resource ID}/authorizationrules/{key name}
  ```

- A metrikák és az erőforrás-naplók Log Analytics munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

  ```azurecli-interactive
  azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
  ```

Ezeket a paramétereket kombinálhatja is, ha több kimeneti eredményt szeretne kapni.

---

## <a name="stream-into-sql-analytics"></a>Stream az SQL Analytics szolgáltatásba

A Azure SQL Database és az Azure SQL felügyelt példányának mérőszámait és az Log Analytics munkaterületre továbbított erőforrás-naplókat Azure SQL Analytics használatával lehet használni. A Azure SQL Analytics egy felhőalapú megoldás, amely az önálló adatbázisok, a rugalmas készletek és a készletezett adatbázisok, valamint a felügyelt példányok és példány-adatbázisok teljesítményét figyeli nagy léptékű és több előfizetésen keresztül. Segítséget nyújt a teljesítmény-mérőszámok gyűjtéséhez és megjelenítéséhez, és beépített intelligenciával rendelkezik a teljesítménnyel kapcsolatos hibaelhárításhoz.

![Azure SQL Analytics áttekintése](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/azure-sql-sol-overview.png)

### <a name="installation-overview"></a>A telepítés áttekintése

Az alábbi lépések végrehajtásával figyelheti az adatbázisok és az adatbázis-gyűjtemények gyűjteményét az Azure SQL Analytics segítségével:

1. Hozzon létre egy Azure SQL Analytics megoldást az Azure Marketplace-en.
2. Hozzon létre egy Log Analytics munkaterületet a megoldásban.
3. Konfigurálja az adatbázisokat a telemetria a munkaterületre.

A diagnosztikai telemetria adatfolyam-exportálását a Azure Portal diagnosztikai beállítások lapján található beépített **küldés log Analytics** lehetőséggel állíthatja be. Az adatfolyamot Log Analytics munkaterületre is engedélyezheti, ha a diagnosztikai beállításokat a [PowerShell-parancsmagok](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-powershell#configure-the-streaming-export-of-diagnostic-telemetry), az [Azure CLI](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-cli#configure-the-streaming-export-of-diagnostic-telemetry), a [Azure monitor REST API](/rest/api/monitor/diagnosticsettings)vagy a [Resource Manager-sablonok](../../azure-monitor/essentials/resource-manager-diagnostic-settings.md)segítségével engedélyezi.

### <a name="create-an-azure-sql-analytics-resource"></a>Azure SQL Analytics erőforrás létrehozása

1. Keresse meg Azure SQL Analytics az Azure Marketplace-en, és válassza ki.

   ![Azure SQL Analytics keresése a portálon](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-in-marketplace.png)

2. Válassza a **Létrehozás** lehetőséget a megoldás áttekintés képernyőjén.

3. Töltse ki a Azure SQL Analytics űrlapot a szükséges további információkkal: munkaterület neve, előfizetés, Erőforráscsoport, hely és árképzési szintje.

   ![Azure SQL Analytics konfigurálása a portálon](./media/metrics-diagnostic-telemetry-logging-streaming-export-configure/sql-analytics-configuration-blade.png)

4. A megerősítéshez kattintson **az OK gombra** , majd válassza a **Létrehozás** lehetőséget.

### <a name="configure-the-resource-to-record-metrics-and-resource-logs"></a>Az erőforrás konfigurálása mérőszámok és erőforrás-naplók rögzítésére

Külön kell konfigurálnia a diagnosztikai telemetria streaminget önálló és készletezett adatbázisok, rugalmas készletek, felügyelt példányok és példány-adatbázisok számára. A legkönnyebben konfigurálhatja, hogy az erőforrások hol vannak a Azure Portal használatával. Részletes lépések: [a diagnosztikai telemetria adatfolyam-exportálásának konfigurálása](metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal#configure-the-streaming-export-of-diagnostic-telemetry).

### <a name="use-azure-sql-analytics-for-monitoring-and-alerting"></a>Azure SQL Analytics használata figyeléshez és riasztáshoz

Az SQL Analytics hierarchikus irányítópultként is használható az adatbázis-erőforrások megtekintéséhez.

- A Azure SQL Analytics használatának megismeréséhez tekintse meg az [SQL Analytics használatával történő figyelést](../../azure-monitor/insights/azure-sql.md)ismertető témakört.
- Ha meg szeretné tudni, hogyan állíthatja be a riasztásokat az SQL Analyticsben, olvassa el a [riasztások létrehozása adatbázishoz, rugalmas készletekhez és felügyelt példányokhoz](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)című témakört.

## <a name="stream-into-event-hubs"></a>Streamelés az Event Hubsba

Az Azure SQL Database és az Azure SQL felügyelt példányok metrikáit és erőforrás-naplóit a Azure Portal **egy Event hub** -beállításához a beépített stream használatával továbbíthatja Event Hubsba. A Service Bus-szabály AZONOSÍTÓját a diagnosztikai beállításokkal is engedélyezheti a PowerShell-parancsmagok, az Azure CLI vagy a Azure Monitor REST API használatával. Ügyeljen arra, hogy az Event hub ugyanabban a régióban legyen, mint az adatbázis és a kiszolgáló.

### <a name="what-to-do-with-metrics-and-resource-logs-in-event-hubs"></a>Mi a teendő a metrikákkal és az erőforrás-naplókkal Event Hubs

A kiválasztott adatoknak a Event Hubsba való továbbítása után egy lépéssel közelebb kerül a speciális figyelési forgatókönyvek engedélyezéséhez. Event Hubs az esemény-adatcsatorna bejárati ajtaja. Az adatok egy esemény központba való gyűjtése után a rendszer átalakíthatja és tárolhatja a valós idejű elemzési szolgáltató vagy a Storage-adapter használatával. Event Hubs leválasztja az események adatfolyamának előállítását az események fogyasztása alapján. Így a felhasználók a saját ütemterv szerint férhetnek hozzá az eseményekhez. További információ a Event Hubsről:

- [Mi az Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Bevezetés az Event Hubs használatába](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

A Event Hubs a következő stream-metrikákat használhatja:

- **A szolgáltatás állapotának megtekintése a gyors elérésű adatoknak a Power BIba való továbbításával**

  Event Hubs, Stream Analytics és Power BI használatával könnyedén átalakíthatja a metrikákat és a diagnosztikai adatokat az Azure-szolgáltatások közel valós idejű elemzéséhez. Az Event hub beállításával, az Stream Analyticsával történő adatfeldolgozással és a Power BI kimenetként való használatával kapcsolatos információkért tekintse meg a [stream Analytics és a Power bi](../../stream-analytics/stream-analytics-power-bi-dashboard.md)című témakört.

- **Stream-naplók harmadik féltől származó naplózási és telemetria streamek számára**

  A Event Hubs streaming használatával különböző külső monitorozási és log Analytics-megoldásokhoz juthat a metrikák és az erőforrások naplóiba.

- **Egyéni telemetria- és naplózási platformot hozhat létre**

  Már rendelkezik egy egyéni kialakítású telemetria-platformmal, vagy felépíti az egyiket? Az Event Hubs rugalmasan méretezhető közzétételi és előfizetési természete lehetővé teszi a metrikák és erőforrás-naplók rugalmas betöltését. Tekintse [meg a Dan Rosanova útmutatóját, hogy a Event Hubst egy globális méretű telemetria platformon használja](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Stream az Azure Storage-ba

A metrikákat és az erőforrás-naplókat az Azure Storage-ban tárolhatja, ha a beépített **archívumot egy Storage-fiók** lehetőségre használja a Azure Portal. A tárolókat a PowerShell-parancsmagok, az Azure CLI vagy a Azure Monitor REST API használatával is engedélyezheti a diagnosztikai beállításokkal.

### <a name="schema-of-metrics-and-resource-logs-in-the-storage-account"></a>Metrikák és erőforrás-naplók sémája a Storage-fiókban

A metrikák és az erőforrás-naplók gyűjteményének beállítása után a rendszer létrehoz egy tárolót a kiválasztott Storage-fiókban, amikor az adatok első sora elérhető. A Blobok szerkezete:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Vagy egyszerűen:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Az alapszintű metrikák blobjának neve például a következő lehet:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

A rugalmas készlet adatainak tárolására szolgáló blob neve a következőképpen néz ki:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Adatmegőrzési szabályzat és díjszabás

Ha Event Hubs vagy egy Storage-fiókot választ, megadhat egy adatmegőrzési szabályt. Ez a szabályzat törli a megadott időszaknál régebbi adatértékeket. Ha Log Analytics ad meg, a megőrzési szabály a kiválasztott díjszabási szinttől függ. Ebben az esetben a megadott ingyenes adategységek lehetővé teszik, hogy havonta több adatbázist is ingyenesen lehessen figyelni. A diagnosztikai telemetria az ingyenes egységeken felüli felhasználásának költsége is felmerülhet.

> [!IMPORTANT]
> A nehezebb számítási feladatokkal rendelkező aktív adatbázisok több adatot töltenek fel, mint az üresjárati adatbázisok. További információ: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Ha Azure SQL Analytics használ, a Azure SQL Analytics navigációs menüjében válassza a **OMS munkaterület** lehetőséget, majd a **használat** és a **becsült költségek** elemet.

## <a name="metrics-and-logs-available"></a>Elérhető metrikák és naplók

Az önálló adatbázisok, a készletezett adatbázisok, a rugalmas készletek, a felügyelt példányok és a példány-adatbázisok figyelésére szolgáló telemetria dokumentációja a cikk jelen szakasza tartalmazza. Az SQL Analytics szolgáltatásban összegyűjtött figyelési telemetria a saját egyéni elemzéséhez és az alkalmazások fejlesztéséhez [Azure monitor log lekérdezési](../../azure-monitor/logs/get-started-queries.md) nyelvet használva.

### <a name="basic-metrics"></a>Alapszintű mérőszámok

Az alapszintű mérőszámok erőforrás alapján történő részletes ismertetését az alábbi táblázatokban találja.

> [!NOTE]
> Az alapszintű mérőszámok beállítás korábbi nevén az összes mérőszám. A módosítás csak a névadásra történt, és nem történt változás a figyelt metrikák esetében. Ezt a változást kezdeményezték a további metrikai kategóriák jövőbeli bevezetésének engedélyezéséhez.

#### <a name="basic-metrics-for-elastic-pools"></a>Rugalmas készletek alapszintű mérőszámai

|**Erőforrás**|**Metrikák**|
|---|---|
|Rugalmas készlet|eDTU százalék, felhasznált eDTU, eDTU korlát, CPU-százalék, fizikai adatolvasási százalék, napló írási aránya, munkamenetek százalékos aránya, munkavégzők százalékos aránya, tárolás, tárolási arány, tárolási korlát, XTP tárolási százalék |

#### <a name="basic-metrics-for-single-and-pooled-databases"></a>Az önálló és a készletezett adatbázisok alapvető mérőszámai

|**Erőforrás**|**Metrikák**|
|---|---|
|Egy és készletezett adatbázis|DTU százalék, felhasznált DTU, DTU korlát, CPU-százalék, fizikai adatolvasási százalék, napló írási százaléka, sikeres/sikertelen/letiltott tűzfal-kapcsolatok, munkamenetek százalékos aránya, munkavégzők százalékos aránya, tárterület, tárolási százalék, XTP tárolási százalék és holtpont |

### <a name="advanced-metrics"></a>Speciális mérőszámok

A speciális mérőszámokkal kapcsolatos részletekért tekintse meg a következő táblázatot.

|**Metrika**|**Metrika megjelenítendő neve**|**Leírás**|
|---|---|---|
|sqlserver_process_core_percent<sup>1</sup>|SQL Process Core százalék|Az SQL-folyamat CPU-kihasználtságának százalékos értéke az operációs rendszer által mért mértékben.|
|sqlserver_process_memory_percent<sup>1</sup> |SQL-folyamat memória százaléka|Az SQL-folyamat memóriahasználat százalékát, amelyet az operációs rendszer mérnek.|
|tempdb_data_size<sup>2</sup>| Tempdb adatfájl mérete (kilobájt) |Tempdb az adatfájl mérete kilobájtban.|
|tempdb_log_size<sup>2</sup>| Tempdb-naplófájl mérete (kilobájt) |Tempdb-naplófájl mérete (kilobájt)|
|tempdb_log_used_percent<sup>2</sup>| Tempdb százalékos naplója használatban |A tempdb százalékos naplója használatban van.|

<sup>1</sup> ez a metrika a virtuális mag beszerzési modellt használó adatbázisok számára érhető el, 2 virtuális mag és magasabb, vagy 200 DTU, valamint a DTU-alapú vásárlási modellek esetében magasabb.

<sup>2</sup> ez a metrika a virtuális mag beszerzési modellt használó adatbázisok számára érhető el, 2 virtuális mag és magasabb, vagy 200 DTU és magasabb DTU-alapú vásárlási modellekhez. Ez a metrika jelenleg nem érhető el nagy kapacitású-adatbázisokhoz vagy adatraktárokhoz.

### <a name="basic-logs"></a>Alapszintű naplók

Az összes naplóhoz elérhető telemetria részletei a következő táblázatokban vannak dokumentálva. További információ: [támogatott diagnosztikai telemetria](#diagnostic-telemetry-for-export).

#### <a name="resource-usage-stats-for-managed-instances"></a>A felügyelt példányok erőforrás-használati statisztikái

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure|
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: ResourceUsageStats |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: MANAGEDINSTANCES |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|A felügyelt példány neve |
|ResourceId|Erőforrás URI-ja |
|SKU_s|SQL felügyelt példány termék SKU |
|virtual_core_count_s|Rendelkezésre álló virtuális mag száma |
|avg_cpu_percent_s|Átlagos CPU-százalék |
|reserved_storage_mb_s|Lefoglalt tárolási kapacitás a felügyelt példányon |
|storage_space_used_mb_s|Használt tároló a felügyelt példányon |
|io_requests_s|IOPS száma |
|io_bytes_read_s|IOPS bájtok olvasása |
|io_bytes_written_s|IOPS bájtok írása |

#### <a name="query-store-runtime-statistics"></a>Lekérdezési tár futásidejű statisztikái

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: QueryStoreRuntimeStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreRuntimeStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|query_hash_s|Lekérdezés kivonata |
|query_plan_hash_s|Lekérdezési terv kivonata |
|statement_sql_handle_s|Utasítás SQL-leírója |
|interval_start_time_d|A 1900-1-1-es órajelek számának intervallumának megkezdése DateTimeOffset |
|interval_end_time_d|A 1900-1-1-es órajelek számának záró DateTimeOffset |
|logical_io_writes_d|Logikai IO-írások száma összesen |
|max_logical_io_writes_d|Logikai i/o-írási műveletek maximális száma végrehajtás szerint |
|physical_io_reads_d|Fizikai IO-olvasások száma összesen |
|max_physical_io_reads_d|Logikai i/o-olvasások maximális száma végrehajtáskor |
|logical_io_reads_d|Logikai IO-olvasások száma összesen |
|max_logical_io_reads_d|Logikai i/o-olvasások maximális száma végrehajtáskor |
|execution_type_d|Végrehajtás típusa |
|count_executions_d|A lekérdezés végrehajtásának száma |
|cpu_time_d|A lekérdezés által a másodpercenként felhasznált CPU-idő összesen |
|max_cpu_time_d|A CPU-idő maximális kihasználtsága a másodpercenkénti egyszeri végrehajtással |
|dop_d|Párhuzamossági fok összege |
|max_dop_d|Az egyszeri végrehajtáshoz használt maximális párhuzamossági fok |
|rowcount_d|Visszaadott sorok száma összesen |
|max_rowcount_d|Egyetlen végrehajtásban visszaadott sorok maximális száma |
|query_max_used_memory_d|A KB-ban használt memória teljes mennyisége |
|max_query_max_used_memory_d|Maximális mennyiségű memória, amelyet egyetlen végrehajtás használ a KB-ban |
|duration_d|Összes végrehajtási idő a másodpercenként |
|max_duration_d|Egyetlen végrehajtás maximális végrehajtási ideje |
|num_physical_io_reads_d|Fizikai olvasások száma összesen |
|max_num_physical_io_reads_d|Fizikai olvasások maximális száma végrehajtáskor |
|log_bytes_used_d|Az összes felhasznált naplózási bájt mennyisége |
|max_log_bytes_used_d|A másodpercenként felhasznált naplózási bájtok maximális mennyisége |
|query_id_d|A lekérdezés azonosítója a lekérdezési tárban |
|plan_id_d|A csomag azonosítója a lekérdezési tárolóban |

További információ a [lekérdezési tár futásidejű statisztikáinak adatairól](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

#### <a name="query-store-wait-statistics"></a>Lekérdezési tároló várakozási statisztikája

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: QueryStoreWaitStatistics |
|OperationName|A művelet neve. Mindig: QueryStoreWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|wait_category_s|A várakozás kategóriája |
|is_parameterizable_s|A lekérdezés parametrizálható |
|statement_type_s|Az utasítás típusa |
|statement_key_hash_s|Utasítás kulcsának kivonata |
|exec_type_d|Végrehajtás típusa |
|total_query_wait_time_ms_d|Az adott várakozási kategória lekérdezésének teljes várakozási ideje |
|max_query_wait_time_ms_d|A lekérdezés maximális várakozási ideje az egyes végrehajtásokban az adott várakozási kategóriában |
|query_param_type_d|0 |
|query_hash_s|Lekérdezési kivonat a lekérdezési tárban |
|query_plan_hash_s|Lekérdezési terv kivonata a lekérdezési tárban |
|statement_sql_handle_s|Utasítás leírója a lekérdezési tárolóban |
|interval_start_time_d|A 1900-1-1-es órajelek számának intervallumának megkezdése DateTimeOffset |
|interval_end_time_d|A 1900-1-1-es órajelek számának záró DateTimeOffset |
|count_executions_d|A lekérdezés végrehajtásának száma |
|query_id_d|A lekérdezés azonosítója a lekérdezési tárban |
|plan_id_d|A csomag azonosítója a lekérdezési tárolóban |

További információ a [lekérdezési tár várakozási statisztikáinak adatairól](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

#### <a name="errors-dataset"></a>Hibák adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: hibák |
|OperationName|A művelet neve. Mindig: ErrorEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|Üzenet|Egyszerű szöveges üzenet hibaüzenete |
|user_defined_b|A felhasználó által definiált bit |
|error_number_d|Hibakód |
|Súlyosság|A hiba súlyossága |
|state_d|A hiba állapota |
|query_hash_s|Sikertelen lekérdezés kivonata, ha elérhető |
|query_plan_hash_s|A sikertelen lekérdezéshez tartozó lekérdezési terv kivonata, ha elérhető |

További információ az [SQL-hibaüzenetekről](/sql/relational-databases/errors-events/database-engine-events-and-errors).

#### <a name="database-wait-statistics-dataset"></a>Adatbázis várakozási statisztikájának adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: DatabaseWaitStatistics |
|OperationName|A művelet neve. Mindig: DatabaseWaitStatisticsEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|wait_type_s|A várakozási típus neve |
|start_utc_date_t [UTC]|Mért időszak kezdő időpontja |
|end_utc_date_t [UTC]|Mért időszak befejezési időpontja |
|delta_max_wait_time_ms_d|Maximális várakozási idő/végrehajtás |
|delta_signal_wait_time_ms_d|Összes jel várakozási ideje |
|delta_wait_time_ms_d|Teljes várakozási idő az időszakban |
|delta_waiting_tasks_count_d|Várakozási feladatok száma |

További információ az [adatbázis-várakozási statisztikákról](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

#### <a name="time-outs-dataset"></a>Időtúllépési adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: időtúllépések |
|OperationName|A művelet neve. Mindig: TimeoutEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|error_state_d|Hiba állapotának kódja |
|query_hash_s|Lekérdezési kivonat, ha elérhető |
|query_plan_hash_s|Lekérdezési terv kivonata, ha elérhető |

#### <a name="blockings-dataset"></a>Blokkolási adatkészlet

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: blokkok |
|OperationName|A művelet neve. Mindig: BlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|lock_mode_s|A lekérdezés által használt zárolási mód |
|resource_owner_type_s|A zárolás tulajdonosa |
|blocked_process_filtered_s|Letiltott folyamat jelentésének XML-fájlja |
|duration_d|A zárolás időtartama (mp) |

#### <a name="deadlocks-dataset"></a>Holtpontok adatkészlete

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC] |A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: holtpontok |
|OperationName|A művelet neve. Mindig: DeadlockEvent |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|deadlock_xml_s|Holtpont-jelentés XML-fájlja |

#### <a name="automatic-tuning-dataset"></a>Adatkészlet automatikus finomhangolása

|Tulajdonság|Leírás|
|---|---|
|TenantId|A bérlő azonosítója |
|SourceSystem|Mindig: Azure |
|TimeGenerated [UTC]|A napló rögzítésekor megjelenő időbélyegző |
|Típus|Mindig: AzureDiagnostics |
|ResourceProvider|Az erőforrás-szolgáltató neve. Mindig: MICROSOFT. SQL |
|Kategória|A kategória neve. Mindig: AutomaticTuning |
|Erőforrás|Az erőforrás neve |
|ResourceType|Az erőforrástípus neve. Mindig: KISZOLGÁLÓK/adatbázisok |
|SubscriptionId|Az adatbázis előfizetési GUID azonosítója |
|ResourceGroup|Az adatbázis erőforráscsoport neve |
|LogicalServerName_s|Az adatbázis-kiszolgáló neve |
|LogicalDatabaseName_s|Az adatbázis neve |
|ElasticPoolName_s|Az adatbázis rugalmas készletének neve, ha van ilyen |
|DatabaseName_s|Az adatbázis neve |
|ResourceId|Erőforrás URI-ja |
|RecommendationHash_s|Automatikus hangolási javaslat egyedi kivonata |
|OptionName_s|Automatikus hangolási művelet |
|Schema_s|Adatbázis-séma |
|Table_s|Érintett tábla |
|IndexName_s|Index neve |
|IndexColumns_s|Oszlop neve |
|IncludedColumns_s|Tartalmazott oszlopok |
|EstimatedImpact_s|Az Automatikus hangolási javaslatok JSON-ra gyakorolt becsült hatása |
|Event_s|Az Automatikus hangolási esemény típusa |
|Timestamp_t|Utolsó frissítés időbélyege |

#### <a name="intelligent-insights-dataset"></a>Intelligent Insights adatkészlet

További információ a [Intelligent Insights napló formátumáról](intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan engedélyezheti a naplózást, és megismerheti a különböző Azure-szolgáltatások által támogatott mérőszámokat és naplózási kategóriákat, tekintse meg a következőt:

- [A Microsoft Azure metrikáinak áttekintése](../../azure-monitor/data-platform.md)
- [Az Azure-platform naplóinak áttekintése](../../azure-monitor/essentials/platform-logs-overview.md)

A Event Hubsről a következő témakörben olvashat bővebben:

- [Mi az Azure Event Hubs?](../../event-hubs/event-hubs-about.md)
- [Bevezetés az Event Hubs használatába](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

A riasztások a log Analytics telemetria alapján történő beállításával kapcsolatos további információkért lásd:

- [Riasztások létrehozása Azure SQL Database és az Azure SQL felügyelt példányaihoz](../../azure-monitor/insights/azure-sql.md#analyze-data-and-create-alerts)