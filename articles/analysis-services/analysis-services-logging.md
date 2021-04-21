---
title: Diagnosztikai naplózás Azure Analysis Services | Microsoft Docs
description: A cikk azt ismerteti, hogyan lehet beállítani a naplózást a Azure Analysis Services monitorozásához.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8ede7572079b6a54672234cbf9fe1445dafbad7b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769210"
---
# <a name="setup-diagnostic-logging"></a>Diagnosztikai naplózás beállítása

A megoldás fontos része Analysis Services a kiszolgálók végrehajtásához szükséges teljesítmény figyelése. Az Azure Analysis Services integrálva van a Azure Monitor. Az [Azure Monitor-naplók használatával](../azure-monitor/essentials/platform-logs-overview.md)figyelheti és elküldheti a naplókat az [Azure Storage-ba,](https://azure.microsoft.com/services/storage/)streamelheti őket az [Azure Event Hubs-ba,](https://azure.microsoft.com/services/event-hubs/)majd exportálhatja őket Azure Monitor [naplókba.](../azure-monitor/overview.md)

![Erőforrás-naplózás a Storage-, Event Hubs- vagy Azure Monitor naplókba](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Mi kerül naplózásra?

Kiválaszthatja a **Motor,** **a Szolgáltatás** és a **Metrikák kategóriát.**

### <a name="engine"></a>Motor

A **Motor kiválasztása** naplózza az összes [xEvents et.](/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) Az egyes események nem választhatók ki. 

|XEvent-kategóriák |Esemény neve  |
|---------|---------|
|Biztonsági naplózás    |   Naplózás bejelentkezése      |
|Biztonsági naplózás    |   Naplózás kijelentkezés      |
|Biztonsági naplózás    |   A naplózási kiszolgáló elindul és leáll      |
|Előrehaladási jelentések     |   Előrehaladási jelentés kezdete      |
|Előrehaladási jelentések     |   Előrehaladási jelentés vége      |
|Előrehaladási jelentések     |   Aktuális állapotjelentés      |
|Lekérdezések     |  Lekérdezés kezdete       |
|Lekérdezések     |   Lekérdezés vége      |
|Parancsok     |  Parancs kezdete       |
|Parancsok     |  Parancs vége       |
|Hibák & figyelmeztetésekkel     |   Hiba      |
|Felderítés     |   A vége felderítése      |
|Értesítés     |    Értesítés     |
|Munkamenet     |  A munkamenet inicializálása       |
|Zárolások    |  Holtpont       |
|Lekérdezésfeldolgozás     |   VertiPaq SE-lekérdezés kezdete      |
|Lekérdezésfeldolgozás     |   VertiPaq SE-lekérdezés vége      |
|Lekérdezésfeldolgozás     |   VertiPaq SE lekérdezési gyorsítótár egyezése      |
|Lekérdezésfeldolgozás     |   Direct Query Begin      |
|Lekérdezésfeldolgozás     |  Közvetlen lekérdezés vége       |

### <a name="service"></a>Szolgáltatás

|Művelet neve  |Akkor fordul elő, ha  |
|---------|---------|
|ResumeServer (Folytatási kiszolgáló)     |    Kiszolgáló folytatása     |
|SuspendServer (Kiszolgáló felfüggesztése)    |   Kiszolgáló szüneteltetése      |
|DeleteServer (Kiszolgáló törlése)     |    Kiszolgáló törlése     |
|RestartServer (Újraindítási kiszolgáló)    |     A felhasználó újraindít egy kiszolgálót az SSMS vagy a PowerShell használatával    |
|GetServerLogFiles    |    A felhasználó exportálja a kiszolgálónaplót a PowerShellen keresztül     |
|ExportModel (Exportálási modell)     |   A felhasználó az Open in Visual Studio     |

### <a name="all-metrics"></a>Minden metrika

A Metrics (Metrikák) kategória ugyanezeket a [Kiszolgálómetrikákat naplózza](analysis-services-monitor.md#server-metrics) az AzureMetrics táblában. Ha lekérdezési felskálát használ, és minden olvasási replikához külön metrikákat kell használnia, használja helyette az AzureDiagnostics táblát, ahol az **OperationName** értéke **LogMetric.** [](analysis-services-scale-out.md)

## <a name="setup-diagnostics-logging"></a>Diagnosztikai naplózás beállítása

### <a name="azure-portal"></a>Azure Portal

1. A [Azure Portal](https://portal.azure.com) > kattintson a  Diagnosztikai beállítások elemre a bal oldali navigációs sávon, majd kattintson **a Diagnosztika bekapcsolás parancsra.**

    ![Kapcsolja be az erőforrás-Azure Cosmos DB a Azure Portal](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. A **diagnosztikai beállítások** területen adja meg a következő beállításokat: 

    * **Név**. Adja meg a létrehozni szükséges naplók nevét.

    * **Archiválás tárfiókba.** A beállításhoz egy meglévő tárfiókra van szüksége, amelyhez csatlakozni tud. Lásd: [Tárfiók létrehozása.](../storage/common/storage-account-create.md) Kövesse az utasításokat egy Resource Manager általános célú fiók létrehozásához, majd válassza ki a tárfiókot a portál ezen oldalára visszatérve. Néhány percet igénybe vehet, hogy az újonnan létrehozott tárfiók megjelenjen a legördülő menüben.
    * **Streamelés eseményközpontba.** A beállítás csak akkor használható, ha egy meglévő eseményközpont-névtérre és eseményközpontra van szüksége a csatlakozáshoz. További információt az [Event Hubs-névtér és eseményközpont létrehozása az Azure Portal használatával](../event-hubs/event-hubs-create.md) című témakörben talál. Ezután térjen vissza erre az oldalra a portálon, és válassza ki az eseményközpont-névteret és a szabályzat nevét.
    * **Küldje el a Azure Monitor (Log Analytics-munkaterület) számára.** Ennek a lehetőségnek a használata esetén használjon egy meglévő munkaterületet, vagy [hozzon](../azure-monitor/logs/quick-create-workspace.md) létre egy új munkaterületi erőforrást a portálon. További információ a naplók megtekintéséről: Naplók megtekintése [a Log Analytics-munkaterületen](#view-logs-in-log-analytics-workspace) ebben a cikkben.

    * **Engine**. Válassza ezt a lehetőséget az xEvents naplózáshoz. Ha tárfiókba archivál, kiválaszthatja az erőforrásnaplók megőrzési időszakát. A megőrzési időszak lejárta után a naplók automatikusan törlődnek.
    * **Szolgáltatás.** Válassza ezt a lehetőséget a szolgáltatásszintű események naplózására. Ha tárfiókba archivál, kiválaszthatja az erőforrásnaplók megőrzési időszakát. A megőrzési időszak lejárta után a naplók automatikusan törlődnek.
    * **Metrikák**. Ezzel a beállítással részletes adatokat tárol a [Metrikákban.](analysis-services-monitor.md#server-metrics) Ha tárfiókba archivál, kiválaszthatja az erőforrásnaplók megőrzési időszakát. A megőrzési időszak lejárta után a naplók automatikusan törlődnek.

3. Kattintson a **Mentés** gombra.

    Ha a következő hibaüzenet jelenik meg: "Nem sikerült frissíteni a diagnosztikát a \<workspace name> következőre: . Az előfizetés \<subscription id> nincs regisztrálva a microsoft.insights használatára." A fiók [regisztrálása Azure Diagnostics](../azure-monitor/essentials/resource-logs.md) kövesse a Hibaelhárítási útmutatót, majd próbálja újra ezt az eljárást.

    Ha a jövőben módosítani szeretné az erőforrásnaplók mentésének a mikéntét, visszatérhet erre az oldalra a beállítások módosításához.

### <a name="powershell"></a>PowerShell

A következő alapszintű parancsokkal ismereti a rendszer. Ha lépésenként szeretne segítséget kérni a tárfiókba való naplózás PowerShell-sel való beállításával, tekintse meg a cikk későbbi, oktatóanyagát.

A metrikák és az erőforrás-naplózás PowerShell használatával való engedélyezéséhez használja a következő parancsokat:

- Az erőforrásnaplók tárfiókban való tárolásának engedélyezéséhez használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   A Storage-fiók azonosítója (storage account ID) annak a tárfióknak az erőforrás-azonosítója, amelybe a naplókat szeretné küldeni.

- Az erőforrásnaplók eseményközpontba való streamelésének engedélyezéséhez használja a következő parancsot:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   Az Azure Service Bus szabályazonosítója (rule ID) egy sztring az alábbi formátumban:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Az erőforrásnaplók Log Analytics-munkaterületre való küldésének engedélyezéséhez használja a következő parancsot:

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

A naplók általában a naplózás beállítását követő néhány órán belül érhetők el. A naplókat a tárfiókban kezelheti:

* A standard Azure-beli hozzáférés-vezérlési módszerekkel szabályozhatja, hogy kik férhetnek hozzá a naplókhoz.
* Ha már nincs szüksége a tárfiókban tárolt naplókra, törölje azokat.
* Mindenképpen állítson be megőrzési megőrzési időszakot, hogy a régi naplók törölve lesznek a tárfiókból.

## <a name="view-logs-in-log-analytics-workspace"></a>Naplók megtekintése a Log Analytics-munkaterületen

A metrikák és a kiszolgálóesemények integrálva vannak az xEvents eseményekkel a Log Analytics-munkaterület erőforrásában a egymás mellett végzett elemzéshez. A Log Analytics-munkaterület úgy is konfigurálható, hogy más Azure-szolgáltatások eseményeit fogadja, így átfogó képet kaphat a diagnosztikai naplózási adatokról az architektúrában.

A diagnosztikai adatok megtekintéséhez nyissa meg  a Naplók elemet a bal oldali menüben a Log Analytics-munkaterületen.

![Naplókeresési lehetőségek a Azure Portal](./media/analysis-services-logging/aas-logging-open-log-search.png)

A lekérdezésszerkesztőben bontsa ki a **LogManagement**  >  **AzureDiagnostics szolgáltatásokat.** Az AzureDiagnostics motor- és szolgáltatáseseményeket is tartalmaz. Figyelje meg, hogy a lekérdezések a gépről is létrejönnek. Az EventClass mező xEvent neveket tartalmaz, amelyek ismerősek lehetnek, ha használta az \_ xEvents-t a helyszíni naplózáshoz. Kattintson **az EventClass \_ (Eseményosztály) vagy** az egyik esemény nevére, és a Log Analytics-munkaterület folytatja a lekérdezések felépítését. A lekérdezéseket ajánlatos menteni későbbi használatra.

### <a name="example-queries"></a>Példa a lekérdezésekre

#### <a name="example-1"></a>1\. példa

A következő lekérdezés a modelladatbázis és -kiszolgáló minden egyes lekérdezésvégi/frissítési záró eseményének időtartamát adja vissza. Horizontális felskálás esetén az eredmények replika szerint vannak fel bontva, mert a replikaszám szerepel a ServerName_s. A RootActivityId_g alapján való csoportosítás csökkenti a jelentésből lekért sorok számát Azure Diagnostics REST API és segít a Log Analytics sebességkorlátaiban [leírt korlátokon belül maradni.](https://dev.loganalytics.io/documentation/Using-the-API/Limits)

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

A következő lekérdezés egy kiszolgáló memória- és QPU-fogyasztását adja vissza. Horizontális felskálás esetén az eredmények replika szerint vannak fel bontva, mert a replikaszám szerepel a ServerName_s.

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

A következő lekérdezés visszaadja a Analysis Services-motor teljesítményszámlálóit a sorok másodpercenkénti száma alapján.

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

Több száz lekérdezést használhat. További információ a lekérdezésekkel kapcsolatban: Ismerkedés a Azure Monitor [naplólekérdezésekkel.](../azure-monitor/logs/get-started-queries.md)


## <a name="turn-on-logging-by-using-powershell"></a>Naplózás bekapcsolása a PowerShell használatával

Ebben a rövid oktatóanyagban egy tárfiókot hoz létre ugyanabban az előfizetésben és erőforráscsoportban, mint az Analysis Service-kiszolgáló. Ezután a Set-AzDiagnosticSetting bekapcsolhatja a diagnosztikai naplózást, és elküldhatja a kimenetet az új tárfióknak.

### <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag befejezéséhez a következő erőforrásokkal kell rendelkezik:

* Egy meglévő Azure Analysis Services kiszolgáló. A kiszolgálói erőforrások létrehozásával kapcsolatos utasításokért [lásd: Kiszolgáló](analysis-services-create-server.md)létrehozása a Azure Portal-ban, vagy Create an Azure Analysis Services server by using PowerShell (Kiszolgáló létrehozása a Azure Portal-ban) vagy Create an Azure Analysis Services server using PowerShell (Új kiszolgáló létrehozása [a PowerShell használatával).](analysis-services-create-powershell.md)

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

Ezután a naplózó fiókhoz társított előfizetés Azure Analysis Services írja be a következőt:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Ha a fiókjához több előfizetés is társítva van, fontos megadni az előfizetést.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Új tárfiók létrehozása a naplóknak

A naplókhoz meglévő tárfiókot is használhat, ha az ugyanabban az előfizetésben található, mint a kiszolgáló. Ebben az oktatóanyagban egy új, dedikált tárfiókot hoz létre a Analysis Services számára. Az egyszerű feladat érdekében a tárfiók adatait egy sa nevű változóban **tárolja.**

Ugyanazt az erőforráscsoportot használja, mint a saját kiszolgálóját Analysis Services erőforráscsoport. A , és `awsales_resgroup` `awsaleslogs` értékeket `West Central US` helyettesítse be a saját értékeivel:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>A naplók kiszolgálófiókjának azonosítása

Állítsa be a fiók nevét egy **account** nevű változóra, ahol a ResourceName a fiók neve.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Naplózás engedélyezése

A naplózás engedélyezéséhez használja a Set-AzDiagnosticSetting parancsmagot az új tárfiók, a kiszolgálófiók és a kategória változóival együtt. Futtassa a következő parancsot, és az **-Enabled jelzőt** a **következőre**$true:

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

Ez a kimenet megerősíti, hogy a naplózás engedélyezve van a kiszolgálón, és adatokat ment a tárfiókba.

Megőrzési szabályzatot is állíthat be a naplókhoz, hogy a régebbi naplók automatikusan törlődnek. A **-RetentionEnabled** jelzővel például állítsa be a megőrzési szabályzatot **a $true,** a **-RetentionInDays** paramétert pedig **állítsa 90-re.** A 90 napnál régebbi naplók automatikusan törlődnek.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Következő lépések

További információ az [Azure Monitor naplózásról.](../azure-monitor/essentials/platform-logs-overview.md)

Lásd: [Set-AzDiagnosticSetting a](/powershell/module/az.monitor/set-azdiagnosticsetting) PowerShell súgóban.
