---
title: Diagnosztikai naplózás a Azure Analysis Serviceshoz | Microsoft Docs
description: Leírja, hogyan kell beállítani a naplózást a Azure Analysis Services-kiszolgáló figyelésére.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2bee856adef1208aabbe65ecd5fd11235579bb82
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100582706"
---
# <a name="setup-diagnostic-logging"></a>Diagnosztikai naplózás beállítása

Minden Analysis Services megoldás fontos része a kiszolgálók végrehajtásának figyelése. Az Azure Analysis Services integrálva van Azure Monitor. A [Azure monitor erőforrás-naplók](../azure-monitor/essentials/platform-logs-overview.md)segítségével figyelheti és elküldheti a naplókat az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja azokat az [Azure-Event Hubsba](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [Azure monitor naplókba](../azure-monitor/overview.md).

![Erőforrás-naplózás tárolási, Event Hubs vagy Azure Monitor naplókba](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Mi kerül naplózásra?

Kiválaszthatja a **motor**, a **szolgáltatás** és a **metrikák** kategóriáit.

### <a name="engine"></a>Motor

A **motor** kiválasztásával naplózza az összes [xevent típusú eseményekhez](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Az egyes események nem választhatók ki. 

|XEvent-kategóriák |Esemény neve  |
|---------|---------|
|Biztonsági naplózás    |   Naplózás bejelentkezése      |
|Biztonsági naplózás    |   Naplózás kijelentkezés      |
|Biztonsági naplózás    |   A naplózási kiszolgáló elindul és leáll      |
|Folyamatjelző jelentések     |   Előrehaladási jelentés kezdete      |
|Folyamatjelző jelentések     |   Az állapotjelző jelentés vége      |
|Folyamatjelző jelentések     |   Aktuális helyzetjelentés      |
|Lekérdezések     |  Lekérdezés kezdete       |
|Lekérdezések     |   Lekérdezés vége      |
|Parancsok     |  A parancs kezdete       |
|Parancsok     |  Parancs vége       |
|Hibák & figyelmeztetésekkel     |   Hiba      |
|Felderítés     |   Felderítés vége      |
|Értesítés     |    Értesítés     |
|Munkamenet     |  A munkamenet inicializálása       |
|Zárolások    |  Holtpont       |
|Lekérdezés feldolgozása     |   VertiPaq SE-lekérdezés kezdete      |
|Lekérdezés feldolgozása     |   VertiPaq SE lekérdezés vége      |
|Lekérdezés feldolgozása     |   VertiPaq SE lekérdezés-gyorsítótár egyezése      |
|Lekérdezés feldolgozása     |   A közvetlen lekérdezés kezdete      |
|Lekérdezés feldolgozása     |  Közvetlen lekérdezés vége       |

### <a name="service"></a>Szolgáltatás

|Művelet neve  |Akkor következik be, amikor  |
|---------|---------|
|ResumeServer     |    Kiszolgáló folytatása     |
|SuspendServer    |   Kiszolgáló szüneteltetése      |
|DeleteServer     |    Kiszolgáló törlése     |
|RestartServer    |     A felhasználó a SSMS vagy a PowerShell használatával újraindítja a kiszolgálót    |
|GetServerLogFiles    |    A felhasználó exportálja a kiszolgáló naplóját a PowerShell használatával     |
|ExportModel     |   A felhasználó egy modellt exportál a portálon a Megnyitás a Visual Studióban való használatával     |

### <a name="all-metrics"></a>Összes metrika

A metrikák kategória ugyanazokat a [kiszolgálói metrikákat](analysis-services-monitor.md#server-metrics) naplózza a AzureMetrics táblába. Ha a lekérdezési [felskálázást](analysis-services-scale-out.md) használja, és el kell különíteni a metrikákat az egyes olvasási replikák számára, használja helyette a AzureDiagnostics táblát, ahol a **OperationName** egyenlő a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Diagnosztikai naplózás beállítása

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com) >-kiszolgáló területen kattintson a **diagnosztikai beállítások** elemre a bal oldali navigációs sávon, majd kattintson a **diagnosztika bekapcsolása** elemre.

    ![A Azure Portal Azure Cosmos DB erőforrás-naplózás bekapcsolása](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. A **diagnosztikai beállítások** területen adja meg a következő beállításokat: 

    * **Név**. Adja meg a létrehozandó naplók nevét.

    * **Archiválás egy Storage-fiókba**. Ha ezt a beállítást szeretné használni, egy meglévő Storage-fiókra van szüksége a kapcsolódáshoz. Lásd: [Storage-fiók létrehozása](../storage/common/storage-account-create.md). Az utasításokat követve hozzon létre egy Resource Managert, általános célú fiókot, majd válassza ki a Storage-fiókját, és térjen vissza erre az oldalra a portálon. Néhány percet igénybe vehet, hogy az újonnan létrehozott tárfiók megjelenjen a legördülő menüben.
    * **Adatfolyam küldése az Event hub-** nak. Ennek a lehetőségnek a használatához egy meglévő Event hub-névtérre és egy Event hub-ra van szükség a kapcsolódáshoz. További információt az [Event Hubs-névtér és eseményközpont létrehozása az Azure Portal használatával](../event-hubs/event-hubs-create.md) című témakörben talál. Ezután térjen vissza ehhez az oldalhoz a portálon, és válassza ki az Event hub névterét és a házirend nevét.
    * **Küldés Azure monitor (log Analytics munkaterületre)**. Ha ezt a beállítást szeretné használni, használjon egy meglévő munkaterületet, vagy [hozzon létre egy új munkaterület](../azure-monitor/logs/quick-create-workspace.md) -erőforrást a portálon. A naplók megtekintésével kapcsolatos további információkért tekintse meg a jelen cikk [naplók megtekintése log Analytics munkaterületen](#view-logs-in-log-analytics-workspace) című témakörét.

    * **Motor**. Válassza ezt a lehetőséget a Xevent típusú eseményekhez naplózásához. Ha egy Storage-fiókba végez archiválást, kiválaszthatja az erőforrás-naplók megőrzési időtartamát. A naplók a megőrzési időszak lejárta után törlődnek.
    * **Szolgáltatás**. Válassza ezt a lehetőséget a szolgáltatási szint eseményeinek naplózásához. Ha Storage-fiókba végez archiválást, kiválaszthatja az erőforrás-naplók megőrzési időtartamát. A naplók a megőrzési időszak lejárta után törlődnek.
    * **Metrikák**. Válassza ezt a lehetőséget, ha részletes adatokat szeretne tárolni a [mérőszámokban](analysis-services-monitor.md#server-metrics). Ha Storage-fiókba végez archiválást, kiválaszthatja az erőforrás-naplók megőrzési időtartamát. A naplók a megőrzési időszak lejárta után törlődnek.

3. Kattintson a **Mentés** gombra.

    Ha a következő hibaüzenet jelenik meg: "a diagnosztika frissítése sikertelen \<workspace name> . Az előfizetés nincs \<subscription id> regisztrálva a Microsoft. bepillantások használatára. " Kövesse a fiók regisztrálásához [Azure Diagnostics](../azure-monitor/essentials/resource-logs.md) útmutatást, majd próbálja megismételni a műveletet.

    Ha módosítani szeretné, hogy az erőforrás-naplók hogyan legyenek mentve a jövő bármely pontjára, térjen vissza erre a lapra a beállítások módosításához.

### <a name="powershell"></a>PowerShell

Itt találja az alapszintű parancsokat, amelyekkel elvégezheti a munkát. Ha azt szeretné, hogy a PowerShell használatával hogyan állíthatja be a naplózást egy Storage-fiókba, tekintse meg a cikk későbbi, című szakaszát.

A metrikák és az erőforrások naplózásának a PowerShell használatával történő engedélyezéséhez használja a következő parancsokat:

- Ha engedélyezni szeretné az erőforrás-naplók tárolását egy Storage-fiókban, használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A Storage-fiók azonosítója (storage account ID) annak a tárfióknak az erőforrás-azonosítója, amelybe a naplókat szeretné küldeni.

- Ha engedélyezni szeretné az erőforrás-naplók adatfolyam-továbbítását az Event hub-ban, használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus szabályazonosítója (rule ID) egy sztring az alábbi formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Az erőforrás-naplók Log Analytics munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- A Log Analytics-munkaterület erőforrás-azonosítóját (ResourceId) az alábbi paranccsal kérheti le:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Ezeket a paramétereket kombinálhatja is, ha több kimeneti eredményt szeretne kapni.

### <a name="rest-api"></a>REST API

Ismerje meg, [hogyan módosíthatja a diagnosztikai beállításokat az Azure Monitor REST API használatával](/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Resource Manager-sablon

Ismerje meg, [hogyan engedélyezheti a diagnosztikai beállításokat az erőforrás létrehozásánál a Resource Manager-sablon használatával](../azure-monitor/essentials/resource-manager-diagnostic-settings.md). 

## <a name="manage-your-logs"></a>Naplók kezelése

A naplók általában néhány órán belül elérhetők a naplózás beállításával. A naplókat a tárfiókban kezelheti:

* A standard Azure-beli hozzáférés-vezérlési módszerekkel szabályozhatja, hogy kik férhetnek hozzá a naplókhoz.
* Ha már nincs szüksége a tárfiókban tárolt naplókra, törölje azokat.
* Ügyeljen arra, hogy megőrzési időtartamot állítson be, hogy a régi naplók törlődjenek a Storage-fiókból.

## <a name="view-logs-in-log-analytics-workspace"></a>Naplók megtekintése Log Analytics munkaterületen

A metrikák és a kiszolgálói események integrálva vannak a Log Analytics munkaterület-erőforrás Xevent típusú eseményekhez, egymás melletti elemzés céljából. A Log Analytics munkaterület úgy is konfigurálható, hogy más Azure-szolgáltatásokból származó eseményeket fogadjon, és átfogó képet kapjon a diagnosztikai naplózási adatokról az architektúrán belül.

A diagnosztikai adatok megtekintéséhez Log Analytics munkaterületen nyissa meg a **naplók**  elemet a bal oldali menüben.

![A Azure Portal keresési beállításainak naplózása](./media/analysis-services-logging/aas-logging-open-log-search.png)

A lekérdezés-szerkesztőben bontsa ki a **LogManagement**  >  **AzureDiagnostics** elemet. A AzureDiagnostics motor-és szolgáltatási eseményeket tartalmaz. Figyelje meg, hogy a rendszer menet közben hozza létre a lekérdezést. A EventClass \_ s mező xEvent neveket tartalmaz, amelyek ismerősek lehetnek, ha a helyszíni naplózáshoz xevent típusú eseményekhez használta. Kattintson a **EventClass \_ s** vagy az egyik esemény nevére, és log Analytics munkaterület folytatja a lekérdezés összeállítását. A lekérdezéseket ajánlatos menteni későbbi használatra.

### <a name="example-queries"></a>Példa a lekérdezésekre

#### <a name="example-1"></a>1\. példa

A következő lekérdezés a modell-adatbázis és-kiszolgáló minden lekérdezési End/Fresh End eseményének időtartamát adja vissza. Ha a méretezés ki van bontva, az eredmények a replika szerint vannak kiosztva, mivel a replika száma szerepel a ServerName_sban. Az RootActivityId_g csoportosítása csökkenti a Azure Diagnostics REST API lekért sorszámokat, és az [log Analytics díjszabásában](https://dev.loganalytics.io/documentation/Using-the-API/Limits)leírtaknak megfelelően segít a korlátokon belül maradni.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>2\. példa

A következő lekérdezés egy kiszolgáló memória-és QPU-felhasználását adja vissza. Ha a méretezés ki van bontva, az eredmények a replika szerint vannak kiosztva, mivel a replika száma szerepel a ServerName_sban.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>3\. példa

A következő lekérdezés visszaadja a-kiszolgáló másodpercenkénti olvasási/mp Analysis Services motorjának teljesítményszámlálói értékét.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Több száz lekérdezést használhat. A lekérdezésekkel kapcsolatos további tudnivalókért tekintse meg [az Ismerkedés a Azure monitor log lekérdezésekkel](../azure-monitor/logs/get-started-queries.md)című témakört.


## <a name="turn-on-logging-by-using-powershell"></a>A naplózás bekapcsolása a PowerShell használatával

Ebben a gyors oktatóanyagban egy olyan Storage-fiókot hoz létre, amely ugyanabban az előfizetésben és erőforráscsoporthoz van, mint az Analysis Service-kiszolgáló. Ezután a Set-AzDiagnosticSetting használatával bekapcsolhatja a diagnosztikai naplózást, és elküldheti a kimenetet az új Storage-fiókba.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következő erőforrásokkal kell rendelkeznie:

* Egy meglévő Azure Analysis Services-kiszolgáló. A kiszolgálói erőforrások létrehozásával kapcsolatos utasításokért lásd: [kiszolgáló létrehozása Azure Portalban](analysis-services-create-server.md), vagy [Azure Analysis Services kiszolgáló létrehozása a PowerShell használatával](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Csatlakozás az előfizetésekhez

Indítson el egy Azure PowerShell-munkamenetet, és jelentkezzen be az Azure-fiókjába az alábbi paranccsal:  

```powershell
Connect-AzAccount
```

Az előugró böngészőablakban adja meg az Azure-fiókja felhasználónevét és jelszavát. Az Azure PowerShell megkeresi az összes olyan előfizetést, amely ehhez a fiókhoz van rendelve, és alapértelmezés szerint kiválasztja az elsőt.

Ha több előfizetése van, előfordulhat, hogy meg kell adnia azt, amelyikkel az Azure Key Vault tárolóját létrehozta. Írja be az alábbi parancsot a fiókhoz tartozó előfizetések megjelenítéséhez:

```powershell
Get-AzSubscription
```

Ezután adja meg a naplózni kívánt Azure Analysis Services-fiókhoz társított előfizetést:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha több előfizetése van társítva a fiókjához, fontos, hogy adja meg az előfizetést.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Új tárfiók létrehozása a naplóknak

A naplókhoz meglévő Storage-fiókot is használhat, feltéve, hogy az a kiszolgálóval megegyező előfizetésben található. Ebben az oktatóanyagban egy új Storage-fiókot hoz létre, amely Analysis Services naplókhoz van hozzárendelve. Az egyszerűvé tétel érdekében a Storage-fiók adatait egy **sa** nevű változóban tárolja.

Ugyanezt az erőforráscsoportot is használja, mint amely a Analysis Services-kiszolgálót tartalmazza. A, a és a értékének helyettesítő értékei `awsales_resgroup` `awsaleslogs` `West Central US` a saját értékeivel:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>A naplókhoz tartozó kiszolgálói fiók azonosítása

Állítsa a fiók nevét egy **fiók** nevű változóra, ahol a resourcename a fiók neve.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez használja az Set-AzDiagnosticSetting parancsmagot az új Storage-fiók, a kiszolgálói fiók és a kategória változóinak együttes használatával. Futtassa a következő parancsot, amely az **-enabled** jelzőt **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

Az alábbi példában látható kimenethez hasonló eredményt kell kapnia:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Ez a kimenet megerősíti, hogy a naplózás mostantól engedélyezve van a kiszolgálón, és az adatok mentése a Storage-fiókba.

Megadhatja a naplók adatmegőrzési szabályzatát is, így a régebbi naplók automatikusan törlődnek. Például állítsa be az adatmegőrzési szabályzatot a **-RetentionEnabled** jelzővel **$truere**, és állítsa a **-RetentionInDays** paramétert **90**-re. A 90 napnál régebbi naplók automatikusan törlődnek.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Következő lépések

További információ a [Azure monitor erőforrás-naplózásról](../azure-monitor/essentials/platform-logs-overview.md).

Lásd: [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) a PowerShell súgójában.