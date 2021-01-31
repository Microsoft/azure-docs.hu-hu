---
title: Azure Storage Analytics metrikák (klasszikus) engedélyezése és kezelése | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti, szerkesztheti és tekintheti meg Azure Storage Analytics metrikákat.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 784929e50d25a07ae92cf388be5ac14f6fa820a6
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221574"
---
# <a name="enable-and-manage-azure-storage-analytics-metrics-classic"></a>Azure Storage Analytics metrikák (klasszikus) engedélyezése és kezelése

[Azure Storage Analytics](storage-analytics.md) metrikákat biztosít a blobokhoz, várólistákhoz és táblákhoz tartozó összes tárolási szolgáltatáshoz. A [Azure Portal](https://portal.azure.com) segítségével beállíthatja, hogy mely metrikák legyenek rögzítve a fiókjához, és hogyan konfigurálhat olyan diagramokat, amelyek vizuális ábrázolást biztosítanak a metrikák adataihoz. Ez a cikk bemutatja, hogyan engedélyezheti és kezelheti a metrikákat. A naplók engedélyezésével kapcsolatos további információkért lásd: [Azure Storage Analytics naplók engedélyezése és kezelése (klasszikus)](manage-storage-analytics-logs.md).

Javasoljuk, hogy tekintse át [Azure monitor a Storage szolgáltatáshoz](../../azure-monitor/insights/storage-insights-overview.md) (előzetes verzió). Ez az Azure Monitor szolgáltatása, amely átfogóan figyeli az Azure Storage-fiókokat az Azure Storage-szolgáltatások teljesítményének, kapacitásának és rendelkezésre állásának egységes áttekintésével. Nem igényli, hogy bármit engedélyezzen vagy konfiguráljan, és azonnal megtekintheti ezeket a metrikákat az előre definiált interaktív diagramokon és egyéb vizualizációkban is.

> [!NOTE]
> A Azure Portal figyelési adataival kapcsolatos költségek vannak kivizsgálva. További információiért tekintse meg a [Storage Analyticset](storage-analytics.md) bemutató cikket.
>
> A prémium szintű teljesítmény-blokkoló blob Storage-fiókok nem támogatják Storage Analytics metrikákat. Ha a prémium szintű teljesítmény-blokkoló blob Storage-fiókokkal rendelkező mérőszámokat szeretné megtekinteni, érdemes lehet [Az Azure Storage-metrikákat használni a Azure monitorban](../blobs/monitor-blob-storage.md).
>
> Az Azure Storage szolgáltatással kapcsolatos problémák azonosítására, diagnosztizálására és hibaelhárítására vonatkozó részletes útmutató a Storage Analytics és egyéb eszközök használatáról: [Microsoft Azure Storage figyelése, diagnosztizálása és hibaelhárítása](storage-monitoring-diagnosing-troubleshooting.md).
>

<a id="Enable-metrics"></a>

## <a name="enable-metrics"></a>Metrikák engedélyezése

### <a name="portal"></a>[Portál](#tab/azure-portal)

1. A [Azure Portal](https://portal.azure.com)válassza ki a **Storage-fiókok** elemet, majd a Storage-fiók nevét a fiók irányítópultjának megnyitásához.

2. Válassza a **diagnosztikai beállítások (klasszikus)** lehetőséget a menü panel **monitorozás (klasszikus)** szakaszában.
   
   ![Képernyőfelvétel: a diagnosztikai beállítások (klasszikus) lehetőség a figyelés (klasszikus) szakaszban.](./media/manage-storage-analytics-metrics/storage-enable-metrics-00.png)

3. Válassza ki **a figyelni** kívánt **szolgáltatás** metrikájának adatait, valamint az adatok **megőrzési szabályát** . A figyelést le is tilthatja, ha az **állapot** beállítás **ki** értékre van állítva.

   > [!div class="mx-imgBorder"]
   > ![Konfigurálja a naplózást a Azure Portalban.](./media/manage-storage-analytics-logs/enable-diagnostics.png) 

   Az adatmegőrzési szabály beállításához helyezze át a **megőrzés (nap)** csúszkát, vagy adja meg a megőrizni kívánt adatnapok számát 1 és 365 között. Az új Storage-fiókok alapértelmezett értéke hét nap. Ha nem szeretne adatmegőrzési szabályt beállítani, adja meg a nulla értéket. Ha nincs adatmegőrzési szabály, akkor a figyelési adatok törlése is megtörténik.

   > [!WARNING]
   > A metics a fiókban tárolt adatként vannak tárolva. A metrikai adatok idővel felhalmozódnak a fiókban, így növelhetik a tárterület költségeit. Ha csak kis időre van szüksége a metrikus adatokra, csökkentheti az adatmegőrzési házirend módosításával járó költségeket. A rendszer törli az elavult metrikai adatokat (az adatmegőrzési szabályzatnál régebbi adatokat). Azt javasoljuk, hogy állítsa be az adatmegőrzési szabályzatot azon alapul, hogy mennyi ideig szeretné megőrizni a fiók metrikájának adatait. További információért lásd a [Storage-metrikák számlázása](storage-analytics-metrics.md#billing-on-storage-metrics) című témakört.
   >

4. A figyelési konfiguráció befejezése után válassza a **Mentés** lehetőséget.

A mérőszámok alapértelmezett készlete az **Áttekintés** panelen, valamint a **metrikák (klasszikus)** panel diagramokban jelenik meg. Miután engedélyezte a metrikákat a szolgáltatásokhoz, akár egy óráig is eltarthat, amíg az adatok megjelennek a diagramokban. Bármelyik metrikai diagramon kiválaszthatja a **Szerkesztés** lehetőséget a diagramon megjelenítendő mérőszámok konfigurálásához.

A metrikák gyűjtését és naplózását letilthatja, ha az **állapot** beállítás **ki** értékre van állítva.

> [!NOTE]
> Az Azure Storage a [Table Storage](storage-introduction.md#table-storage) használatával tárolja a Storage-fiók metrikáit, és a fiókban lévő táblákban tárolja a metrikákat. További információk: [A metrikák tárolása](storage-analytics-metrics.md#how-metrics-are-stored)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nyisson meg egy Windows PowerShell-parancssori ablakot.

2. Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` paranccsal, és kövesse a képernyőn megjelenő útmutatásokat.

   ```powershell
   Connect-AzAccount
   ```

3. Ha az identitása egynél több előfizetéshez van társítva, akkor állítsa be az aktív előfizetését.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Cserélje le a `<subscription-id>` helyőrző értékét az előfizetés azonosítójával.

5. Szerezze be a használni kívánt Storage-fiókot meghatározó Storage-fiók környezetét.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Cserélje le a `<resource-group-name>` helyőrző értékét az erőforráscsoport nevére.

   * Cserélje le a `<storage-account-name>` helyőrző értékét a Storage-fiók nevére. 

6. A helyi gépen a PowerShell használatával konfigurálhatja a tárolási mérőszámokat a Storage-fiókban. Az aktuális beállítások módosításához használja a **set-AzStorageServiceMetricsProperty** Azure PowerShell parancsmagot. 

   A következő parancs a Storage-fiókban lévő blob-szolgáltatás perc mérőszámait kapcsolja be, a megőrzési időtartam pedig öt napra van állítva.

   ```powershell
   Set-AzStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5 -Context $ctx
   ```   

   Ez a parancsmag a következő paramétereket használja:  

   - **ServiceType**: a lehetséges értékek a **blob**, **üzenetsor**, **tábla** és **fájl**.
   - **MetricsType**: a lehetséges értékek **óra** és **perc**.  
   - **MetricsLevel**: a lehetséges értékek a következők:
      - **Nincs**: a figyelés kikapcsolása.
      - **Szolgáltatás**: olyan mérőszámokat gyűjt, mint a bejövő és a kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi arány, amely a blob, a várólista, a tábla és a Fájlszolgáltatások esetében összesítve van.
      - **ServiceAndApi**: a szolgáltatási metrikák mellett az Azure Storage szolgáltatás API-ban minden tárolási művelethez ugyanazt a metrikát gyűjti.

   A következő parancs lekérdezi a blob szolgáltatás aktuális óránkénti metrikáit és megőrzési napjait az alapértelmezett Storage-fiókban:  

   ```powershell
   Get-AzStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob -Context $storagecontext.Context
   ```  

   Az Azure PowerShell-parancsmagoknak az Azure-előfizetéssel való használatához való konfigurálásával és a használni kívánt alapértelmezett Storage-fiók kiválasztásával kapcsolatos információkért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).  

### <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

További információ a tárolási metrikák konfigurálásáról .NET-nyelv használatával: [Azure Storage ügyféloldali kódtárak a .net-hez](/dotnet/api/overview/azure/storage).  

A tárolási metrikák REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

### <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.HourMetrics.MetricsLevel = MetricsLevel.Service;  
serviceProperties.HourMetrics.RetentionDays = 10;  

queueClient.SetServiceProperties(serviceProperties);  
```  

További információ a tárolási metrikák konfigurálásáról .NET-nyelv használatával: [Azure Storage ügyféloldali kódtárak a .net-hez](/dotnet/api/overview/azure/storage).  

A tárolási metrikák REST API használatával történő konfigurálásával kapcsolatos általános információkért lásd: [Storage Analytics engedélyezése és konfigurálása](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics). 

---

<a id="view-metrics"></a>

## <a name="view-metrics-in-a-chart"></a>Diagram metrikáinak megtekintése

Miután konfigurálta Storage Analytics mérőszámokat a Storage-fiók figyeléséhez, Storage Analytics rögzíti a metrikákat a Storage-fiók jól ismert tábláiban. A diagramokat konfigurálhatja a [Azure Portal](https://portal.azure.com)óránkénti metrikáinak megtekintéséhez.

A következő eljárással kiválaszthatja, hogy mely tárolási metrikákat szeretné megtekinteni egy mérőszámok diagramján.

1. Először jelenítse meg a tárolási metrika diagramot a Azure Portal. Diagramokat a **Storage-fiók** panelen és a **metrikák (klasszikus)** panelen találhatja meg.

   Ebben a példában a következő, a **Storage-fiók** panelen megjelenő diagramot használja:

   ![Diagram kijelölése Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-00.png)

2. A diagram szerkesztéséhez kattintson bárhová a diagramon belül.

3. Ezután válassza ki a diagramon megjelenítendő mérőszámok **időtartományát** , valamint azt a **szolgáltatást** (blob, üzenetsor, tábla, fájl), amelynek metrikáit meg szeretné megjeleníteni. Itt az előző heti mérőszámok vannak kiválasztva a blob szolgáltatás megjelenítéséhez:

   ![Időtartomány és szolgáltatás kiválasztása a diagram szerkesztése panelen](./media/manage-storage-analytics-metrics/storage-edit-metric-time-range.png)

4. Válassza ki a diagramon megjeleníteni kívánt egyéni **metrikákat** , majd kattintson az **OK** gombra.

   ![Egyéni metrika kiválasztása a diagram szerkesztése panelen](./media/manage-storage-analytics-metrics/storage-edit-metric-selections.png)

A diagram beállításai nem érintik a tárolási fiókban lévő figyelési adatgyűjtést, összesítést vagy tárolást.

#### <a name="metrics-availability-in-charts"></a>Metrikák rendelkezésre állása a diagramokon

Az elérhető mérőszámok listája azon alapul, hogy a legördülő listában kiválasztott szolgáltatás és a szerkesztett diagram egység típusa alapján változik-e meg. Kiválaszthatja például, hogy a százalékos mérőszámok, például a *percentnetworkerror értéket mutatnak* és a *percentthrottlingerror értéket mutatnak* csak akkor legyenek, ha olyan diagramot szerkeszt, amely százalékban jeleníti meg az egységeket:

![Lekérési hiba százalékos diagramja a Azure Portal](./media/manage-storage-analytics-metrics/stg-customize-chart-04.png)

#### <a name="metrics-resolution"></a>Metrikák feloldása

A **diagnosztika** kiválasztott mérőszámai határozzák meg a fiókhoz elérhető metrikák feloldását:

* Az **összesített** figyelés olyan mérőszámokat biztosít, mint a bejövő/kimenő forgalom, a rendelkezésre állás, a késés és a sikerességi arány. Ezek a metrikák a blob-, tábla-, fájl-és üzenetsor-szolgáltatásokból vannak összesítve.
* Az API-k **egy** finomabb felbontást biztosítanak, és a szolgáltatási szint összesítései mellett egyéni tárolási műveletekhez elérhető metrikák is rendelkezésre állnak.

## <a name="download-metrics-to-archive-or-analyze-locally"></a>Mérőszámok letöltése helyi archiválásra vagy elemzésre

Ha le szeretné tölteni a mérőszámokat a hosszú távú tároláshoz, vagy helyileg kívánja elemezni őket, egy eszközt kell használnia, vagy írnia kell egy kódot a táblák olvasásához. A táblák nem jelennek meg, ha a Storage-fiókban lévő összes táblát listázza, de közvetlenül a név alapján érheti el őket. Számos Storage-szemöldök eszköz ismeri ezeket a táblázatokat, és lehetővé teszi, hogy közvetlenül megtekintse őket. Az elérhető eszközök listáját az [Azure Storage-ügyféleszközök](./storage-explorers.md)című témakörben tekintheti meg.

|Mérőszámok|Táblák nevei|Jegyzetek| 
|-|-|-|  
|Óránkénti mérőszámok|$MetricsHourPrimaryTransactionsBlob<br /><br /> $MetricsHourPrimaryTransactionsTable<br /><br /> $MetricsHourPrimaryTransactionsQueue<br /><br /> $MetricsHourPrimaryTransactionsFile|A 2013. augusztus 15. előtti verziókban ezek a táblázatok a következőképpen ismertek:<br /><br /> $MetricsTransactionsBlob<br /><br /> $MetricsTransactionsTable<br /><br /> $MetricsTransactionsQueue<br /><br /> A file Service metrikái a 2015-as verziótól kezdődően érhetők el.|  
|Perc mérőszámok|$MetricsMinutePrimaryTransactionsBlob<br /><br /> $MetricsMinutePrimaryTransactionsTable<br /><br /> $MetricsMinutePrimaryTransactionsQueue<br /><br /> $MetricsMinutePrimaryTransactionsFile|Csak PowerShell használatával vagy programozott módon engedélyezhető.<br /><br /> A file Service metrikái a 2015-as verziótól kezdődően érhetők el.|  
|Kapacitás|$MetricsCapacityBlob|Csak Blob service.|  

A táblázatok sémáinak részletes ismertetését lásd: [Storage Analytics mérőszámok tábla sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema). A következő minta sorok csak az elérhető oszlopok egy részhalmazát jelenítik meg, de a tárolási metrikák egyes fontos funkcióit illusztrálják a metrikák mentéséhez:  

|PartitionKey|RowKey|Timestamp|TotalRequests|TotalBillableRequests|TotalIngress|TotalEgress|Rendelkezésre állás|AverageE2ELatency|AverageServerLatency|PercentSuccess| 
|-|-|-|-|-|-|-|-|-|-|-|  
|20140522T1100|felhasználói Összes|2014-05-22T11:01:16.7650250 Z|7|7|4003|46801|100|104,4286|6,857143|100|  
|20140522T1100|felhasználói QueryEntities|2014-05-22T11:01:16.7640250 Z|5|5|2694|45951|100|143,8|7,8|100|  
|20140522T1100|felhasználói QueryEntity|2014-05-22T11:01:16.7650250 Z|1|1|538|633|100|3|3|100|  
|20140522T1100|felhasználói UpdateEntity|2014-05-22T11:01:16.7650250 Z|1|1|771|217|100|9|6|100|  

Ebben a példában a perc mérőszámok adatainál a partíciós kulcs a percenkénti felbontásban használja az időt. A sor kulcsa azonosítja a sorban tárolt információ típusát. Az információ a hozzáférési típustól és a kérelem típusától tevődik össze:  

-   A hozzáférési típus vagy **felhasználó** vagy **rendszer**, ahol a **felhasználó** a tárolási szolgáltatáshoz tartozó összes felhasználói kérésre hivatkozik, és a **rendszer** a Storage Analytics által benyújtott kérelmekre hivatkozik.  
-   A kérelem típusa vagy **mind**, ebben az esetben ez egy összesítő sor, vagy azonosítja az adott API-t (például **QueryEntity** vagy **UpdateEntity**).  

Ez a mintaadatok egy percen belüli összes rekordot jeleníti meg (a 11-es naptól számítva), így a **QueryEntities** -kérések száma, valamint a **QueryEntity** -kérések száma és a **UpdateEntity** -kérelmek száma akár hét is felveszi. Ez az összeg a **felhasználó: minden** sorban látható. Hasonlóképpen, az átlagos végpontok közötti késés 104,4286 a **felhasználónál: az összes** sor kiszámításával ((143,8 * 5) + 3 + 9)/7.  

## <a name="view-metrics-data-programmatically"></a>Metrikák adatait programozott módon tekintheti meg

Az alábbi lista a C#-kódot mutatja be, amely egy percen belül fér hozzá a perc mérőszámokhoz, és megjeleníti az eredményeket egy konzolablak-ablakban. A kód minta az Azure Storage ügyféloldali kódtára 4. x vagy újabb verzióját használja, amely a **CloudAnalyticsClient** osztályt is tartalmazza, amely leegyszerűsíti a tárolóban lévő metrikai táblák elérését. 

> [!NOTE]
> A **CloudAnalyticsClient** osztály nem szerepel a .net-hez készült Azure Blob Storage ügyféloldali kódtáraban. Az **2023-as augusztus 31-** én Storage Analytics metrikákat, más néven a *klasszikus metrikákat* is kivezetjük. További információért tekintse meg a [hivatalos bejelentést](https://azure.microsoft.com/updates/azure-storage-classic-metrics-will-be-retired-on-31-august-2023/). Ha klasszikus metrikákat használ, javasoljuk, hogy az adott dátum előtt Azure Monitor metrikára váltson. 

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)  
{  
 // Convert the dates to the format used in the PartitionKey.  
 var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  
 var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");  

 var services = Enum.GetValues(typeof(StorageService));  
 foreach (StorageService service in services)  
 {  
     Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);  
     var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);  
     var t = analyticsClient.GetMinuteMetricsTable(service);  
     var opContext = new OperationContext();  
     var query =  
             from entity in metricsQuery  
             // Note, you can't filter using the entity properties Time, AccessType, or TransactionType  
             // because they are calculated fields in the MetricsEntity class.  
             // The PartitionKey identifies the DataTime of the metrics.  
             where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0   
             select entity;  

     // Filter on "user" transactions after fetching the metrics from Azure Table storage.  
     // (StartsWith is not supported using LINQ with Azure Table storage.)  
     var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));  
     var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();  
     Console.WriteLine(resultString);  
 }  
}  

private static string MetricsString(MetricsEntity entity, OperationContext opContext)  
{  
 var entityProperties = entity.WriteEntity(opContext);  
 var entityString =  
         string.Format("Time: {0}, ", entity.Time) +  
         string.Format("AccessType: {0}, ", entity.AccessType) +  
         string.Format("TransactionType: {0}, ", entity.TransactionType) +  
         string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));  
 return entityString;  
}  
```  

<a id="add-metrics-to-dashboard"></a>

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Metrikai diagramok hozzáadása a portál irányítópulthoz

Bármelyik Storage-fiókhoz hozzáadhat Azure Storage metrikai diagramokat a portál irányítópultján.

1. Válassza az **irányítópult szerkesztése** lehetőséget az irányítópult megtekintésekor a [Azure Portal](https://portal.azure.com).
1. A csempe- **gyűjteményben** válassza a **csempék keresése**  >  **típus** szerint lehetőséget.
1. Válassza a **típus**  >  **Storage-fiókok** lehetőséget.
1. Az **erőforrások** területen válassza ki azt a Storage-fiókot, amelynek metrikáit hozzá szeretné adni az irányítópulthoz.
1. Válassza ki a **Kategóriák**  >  **figyelése** elemet.
1. Húzza a diagram csempét az Irányítópultra a megjelenő mérőszámhoz. Ismételje meg az összes olyan mérőszámot, amelyet meg szeretne jeleníteni az irányítópulton. A következő ábrán a "Blobok – összes kérelem" diagram ki van emelve példaként, de az irányítópulton az összes diagram elérhetővé válik.

   ![Csempe-gyűjtemény Azure Portal](./media/manage-storage-analytics-metrics/storage-customize-dashboard.png)
1. Ha elkészült a diagramok hozzáadásával, válassza az irányítópult tetején található **Testreszabás kész** lehetőséget.

Miután hozzáadta a diagramokat az irányítópulthoz, tovább testreszabhatja azokat a következő témakörben leírtak szerint: metrika-diagramok testreszabása.

## <a name="next-steps"></a>Következő lépések

* További információ a Storage Analyticsről: [Storage Analytics](storage-analytics.md) Storage Analytics.
* [Storage Analytics naplók konfigurálása](manage-storage-analytics-logs.md).
* További információ a metrikák sémáról. Lásd: [Storage Analytics mérőszámok táblázatának sémája](/rest/api/storageservices/storage-analytics-metrics-table-schema).