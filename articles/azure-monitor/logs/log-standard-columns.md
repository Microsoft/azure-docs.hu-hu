---
title: A Azure Monitor naplóbejegyzések szabványos oszlopai | Microsoft Docs
description: Azokat az oszlopokat ismerteti, amelyek közösek Azure Monitor-naplók több adattípusához.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2021
ms.openlocfilehash: 5b906bdbd07d59d2acc88f6b30f0db6b6cbc961a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562246"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Szabványos oszlopok a Azure Monitor-naplókban
Azure Monitor naplókban lévő adatokat egy [log Analytics munkaterületen vagy Application Insights alkalmazásban található rekordok halmaza tárolja](../logs/data-platform-logs.md), amelyek mindegyike egy adott adattípussal rendelkezik, amely egyedi oszlopokkal rendelkezik. Számos adattípus szabványos oszlopokat tartalmaz, amelyek több típusra is jellemzőek. Ez a cikk ezeket az oszlopokat ismerteti, és példákat tartalmaz arra, hogyan használhatja őket a lekérdezésekben.

A Application Insights munkaterület-alapú alkalmazásai Log Analytics munkaterületen tárolják az állapotukat, és ugyanazokat a szabványos oszlopokat használják, mint a munkaterületen lévő többi tábla. A klasszikus alkalmazások külön tárolják az adattárolást, és eltérő szabványos oszlopokkal rendelkeznek a jelen cikkben megadott módon.

> [!NOTE]
> A standard oszlopok némelyike nem jelenik meg a séma nézetben vagy az IntelliSense Log Analyticsban, és a lekérdezési eredményekben nem jelennek meg, kivéve, ha explicit módon megadja az oszlopot a kimenetben.
> 

## <a name="tenantid"></a>TenantId
A **TenantId** oszlop a log Analytics munkaterület munkaterületének azonosítóját tárolja.

## <a name="timegenerated-and-timestamp"></a>TimeGenerated és időbélyeg
A **TimeGenerated** (log Analytics munkaterület) és az **időbélyeg** (Application Insights alkalmazás) oszlopai azt a dátumot és időpontot tartalmazzák, ameddig a rekordot az adatforrás hozta létre. További részletekért lásd: a [naplózási adatok betöltési ideje Azure monitorban](../logs/data-ingestion-time.md) .

A **TimeGenerated** és az **időbélyeg** közös oszlopot biztosít a szűréshez, illetve az időbeli összesítéshez. Amikor kiválaszt egy időtartományt egy nézethez vagy egy irányítópulthoz a Azure Portalban, a TimeGenerated vagy időbélyeg használatával szűri az eredményeket. 

### <a name="examples"></a>Példák

A következő lekérdezés az előző hét egyes napjaihoz létrehozott hibák eseményeinek számát adja vissza.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```

A következő lekérdezés az előző hét egyes napjaihoz létrehozott kivételek számát adja vissza.

```Kusto
exceptions
| where timestamp between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by timestamp asc 
```

## <a name="_timereceived"></a>\_TimeReceived
A **\_ TimeReceived** oszlop azt a dátumot és időpontot tartalmazza, ameddig a rekord az Azure-felhőben lévő Azure monitor betöltési ponttól érkezett. Ez hasznos lehet az adatforrás és a felhő közötti késési problémák azonosításához. Ilyen például egy hálózati probléma, ami késlelteti az ügynöktől küldött adatok késését. További részletekért lásd: a [naplózási adatok betöltési ideje Azure monitorban](../logs/data-ingestion-time.md) .

> [!NOTE]
> A **\_ TimeReceived** oszlop minden használatkor ki van számítva. Ez a folyamat erőforrás-igényes. Pontosítsa a használatát a nagy számú rekord szűréséhez. A függvény ismétlődő használata a lekérdezés végrehajtásának nagyobb időtartamára vezethet.


Az alábbi lekérdezés átlagos késést biztosít egy ügynöktől származó esemény-rekordok esetében óránként. Ez magában foglalja az ügynök és a felhő közötti időt, valamint azt, hogy a rekord teljes ideje elérhető legyen a naplók lekérdezéséhez.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type-and-itemtype"></a>Típus-és itemType
A **típus** (log Analytics munkaterület) és a **itemtype** (Application Insights alkalmazás) oszlopaiban szerepel annak a táblának a neve, amelyből a rekordot beolvasták, amelyről a rekord típusaként is gondolni lehet. Ez az oszlop olyan lekérdezésekben hasznos, amelyek több tábla rekordjait egyesítik, például a kezelőt használó rekordokat a `search` különböző típusú rekordok megkülönböztetéséhez. a **$Table** egyes helyeken a **típus** helyett lehet használni.

### <a name="examples"></a>Példák
A következő lekérdezés a rekordok számát adja vissza az elmúlt órában összegyűjtött típusok alapján.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_Elemazonosító
A **\_ elemazonosító** oszlop a rekord egyedi azonosítóját tartalmazza.


## <a name="_resourceid"></a>\_ResourceId
A **\_ ResourceId** oszlop az erőforrás egyedi azonosítóját tartalmazza, amelyhez a rekord társítva van. Ez egy szabványos oszlopot biztosít a lekérdezés hatóköréhez, amely csak egy adott erőforrás rekordjaira vonatkozik, vagy a kapcsolódó adatok több táblán való összekapcsolására.

Az Azure-erőforrások esetében **_ResourceId** értéke az [Azure-erőforrás azonosítójának URL-címe](../../azure-resource-manager/templates/template-functions-resource.md). Az oszlop az Azure-erőforrások, például az [Azure arc](../../azure-arc/overview.md) -erőforrások, illetve az erőforrás-azonosítót a betöltés során jelzett egyéni naplókra korlátozódik.

> [!NOTE]
> Egyes adattípusok már rendelkeznek olyan mezőkkel, amelyek tartalmazzák az Azure Resource ID-t, vagy legalább egy részét, például az előfizetés-azonosítót. Habár ezek a mezők a visszamenőleges kompatibilitás érdekében is megmaradnak, javasoljuk, hogy a _ResourceId használatával végezze el a korrelációt, mivel az konzisztensebb lesz.

### <a name="examples"></a>Példák
A következő lekérdezés összekapcsolja az egyes számítógépek teljesítmény-és esemény-adatait. Az _101_ -es azonosítójú és a processzor kihasználtsága 50%-nál több eseményt mutat be.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

A következő lekérdezés összekapcsolja a _AzureActivity_ -rekordokat a _SecurityEvent_ -rekordokkal. Megjeleníti az összes olyan tevékenység-műveletet, amelynek a felhasználóinak bejelentkezett a gépre.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

A következő lekérdezés elemzi a **_ResourceId** és összesíti az Azure-erőforráscsoport által számlázott adatmennyiséget.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Ezeket a `union withsource = tt *` lekérdezéseket takarékosan használhatja az adattípusok megkereséséhez.

Mindig hatékonyabb a \_ SubscriptionId oszlop használata, mint a kinyerése a \_ ResourceId oszlop elemzésével.

## <a name="_subscriptionid"></a>\_SubscriptionId
A **\_ SubscriptionId** oszlop tartalmazza annak az erőforrásnak az előfizetési azonosítóját, amelyhez a rekord társítva van. Ez egy szabványos oszlopot biztosít, amellyel a lekérdezés hatókörét csak egy adott előfizetésből származó rekordokra vagy a különböző előfizetések összehasonlítására használhatja.

Az Azure-erőforrások esetében **__SubscriptionId** értéke az [Azure Resource id URL-cím](../../azure-resource-manager/templates/template-functions-resource.md)előfizetés része. Az oszlop az Azure-erőforrások, például az [Azure arc](../../azure-arc/overview.md) -erőforrások, illetve az erőforrás-azonosítót a betöltés során jelzett egyéni naplókra korlátozódik.

> [!NOTE]
> Egyes adattípusok esetében már van olyan mező, amely tartalmazza az Azure-előfizetés AZONOSÍTÓját. Habár ezek a mezők a visszamenőleges kompatibilitás érdekében is megmaradnak, ajánlott a \_ SubscriptionId oszlopot használni a kereszthivatkozások végrehajtásához, mivel az konzisztens lesz.
### <a name="examples"></a>Példák
Az alábbi lekérdezés egy adott előfizetéshez tartozó számítógépek teljesítményadatokat vizsgálja. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "57366bcb3-7fde-4caf-8629-41dc15e3b352"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

Az alábbi lekérdezés az Azure-előfizetések által **_ResourceId** és összesített számlázott adatmennyiséget elemez.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Ezeket a `union withsource = tt *` lekérdezéseket takarékosan használhatja az adattípusok megkereséséhez.


## <a name="_isbillable"></a>\_Számlázható
A **\_ számlázható** oszlop meghatározza, hogy a betöltött adatmennyiség számlázható-e. A **\_ számlázható** egyenlő mennyiségű adatok `false` gyűjtése ingyenesen történik, és nem számítjuk fel az Azure-fiókját.

### <a name="examples"></a>Példák
A számlázott adattípusokat küldő számítógépek listájának lekéréséhez használja a következő lekérdezést:

> [!NOTE]
> A lekérdezések használatával `union withsource = tt *` takarékosan végezheti el az adattípusok megkeresését. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Ezt kiterjesztheti a számlázott adattípusokat küldő számítógépek száma óránként:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
A **\_ BilledSize** oszlop az Azure-fiókba felszámított adatmennyiség bájtban kifejezett méretét adja meg, ha a **\_ számlázható** értéke igaz.


### <a name="examples"></a>Példák
Ha meg szeretné tekinteni a számítógépen betöltött számlázható események méretét, használja a `_BilledSize` bájtban megadott méretet tartalmazó oszlopot:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Az előfizetéshez tartozó számlázható események méretének megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Ha szeretné megtekinteni, hogy mekkora a számlázható események száma egy erőforráscsoport esetében, használja a következő lekérdezést:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


A számítógépen betöltött események számának megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

A számítógépeken betöltött számlázható események számának megtekintéséhez használja a következő lekérdezést: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Egy adott számítógépről származó számlázható adattípusok számának megtekintéséhez használja a következő lekérdezést:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure monitor naplózási adatainak tárolásáról](./log-query-overview.md).
- Vegyen fel egy leckét a [naplók írásához](./get-started-queries.md).
- Bemutatjuk [, hogyan csatlakozhatnak táblákhoz a naplók lekérdezésében](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
