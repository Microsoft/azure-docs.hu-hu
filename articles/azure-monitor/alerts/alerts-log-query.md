---
title: A log Alert-lekérdezések optimalizálása | Microsoft Docs
description: Javaslatok a hatékony riasztási lekérdezések írásához
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/22/2020
ms.subservice: alerts
ms.openlocfilehash: cfe6aa489bcc771213ec04ca9cddd1267ccf1338
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616285"
---
# <a name="optimizing-log-alert-queries"></a>A naplók riasztási lekérdezéseinek optimalizálása
Ez a cikk azt ismerteti, hogyan írhat és alakíthat át [riasztási](../platform/alerts-unified-log.md) lekérdezéseket az optimális teljesítmény érdekében. Az optimalizált lekérdezések csökkentik a késést és a riasztások terhelését, ami gyakran fut.

## <a name="how-to-start-writing-an-alert-log-query"></a>Riasztási napló lekérdezésének megkezdése

A riasztási lekérdezések [a log Analytics napló adatainak lekérdezésével](alerts-log.md#create-a-log-alert-rule-with-the-azure-portal) kezdődnek, amely a problémát jelzi. A [riasztási lekérdezés példái című témakörből](../log-query/example-queries.md) megtudhatja, hogy mit tud felderíteni. Ön is [megkezdheti a saját lekérdezés megírását](../log-query/log-analytics-tutorial.md). 

### <a name="queries-that-indicate-the-issue-and-not-the-alert"></a>A problémát jelző lekérdezések és nem a riasztás

A riasztási folyamat úgy lett felépítve, hogy átalakítsa a riasztási hibát jelző eredményeket. Például a következőhöz hasonló lekérdezés esetén:

``` Kusto
SecurityEvent
| where EventID == 4624
```

Ha a felhasználó szándéka, hogy riasztást küldjön, ha az esemény típusa bekövetkezik, a riasztási logika hozzáfűzi `count` a lekérdezést. A futtatandó lekérdezés a következőket eredményezi:

``` Kusto
SecurityEvent
| where EventID == 4624
| count
```

Nem kell riasztási logikát felvennie a lekérdezésbe, és ennek következtében problémákhoz is vezethet. A fenti példában, ha belefoglalja a `count` lekérdezésbe, az mindig az 1 értéket fogja eredményezni, mivel a riasztási szolgáltatás végrehajtja a szolgáltatást `count` `count` .

### <a name="avoid-limit-and-take-operators"></a>Elkerülés `limit` és `take` operátorok

`limit`A és `take` a lekérdezésekben való használata növelheti a késést és a riasztások terhelését, mivel az eredmények nem konzisztensek az idő múlásával. A használata javasolt, ha szükséges.

## <a name="log-query-constraints"></a>Lekérdezési korlátozások naplózása
A [Azure monitor](../log-query/log-query-overview.md) a (z) táblával, [`search`](/azure/kusto/query/searchoperator) vagy operátorral kezdődnek a lekérdezések [`union`](/azure/kusto/query/unionoperator) .

A naplózási riasztási szabályok lekérdezéseit mindig egy táblázattal kell kezdeni, hogy egyértelmű hatókört határozzon meg, ami javítja a lekérdezési teljesítményt és az eredmények relevanciáját. A riasztási szabályokban lévő lekérdezések gyakran futnak, így a `search` és `union` a használatával túlzott mértékű terhelést eredményezhet a riasztáshoz, mivel több táblára történő vizsgálatra is szükség van. Ezen operátorok csökkentik a riasztási szolgáltatás képességét is a lekérdezés optimalizálására.

Nem támogatjuk az olyan naplózási riasztási szabályok létrehozását vagy módosítását `search` `union` , amelyek a vagy az operátort használják az erőforrások közötti lekérdezésekhez.

A következő riasztási lekérdezés például a _SecurityEvent_ táblára terjed ki, és megkeresi az adott eseményazonosító. Ez az egyetlen tábla, amelyet a lekérdezésnek fel kell dolgoznia.

``` Kusto
SecurityEvent
| where EventID == 4624
```

Ez a változás nem érinti a riasztási szabályokat a [több erőforrást használó lekérdezések](../log-query/cross-workspace-query.md) használatával, mivel a több erőforrást használó lekérdezések a típust használják `union` , amely a lekérdezési hatókört meghatározott erőforrásokra korlátozza. A következő példa egy érvényes napló-riasztási lekérdezés:

```Kusto
union
app('Contoso-app1').requests,
app('Contoso-app2').requests,
workspace('Contoso-workspace1').Perf 
```

>[!NOTE]
> Az [erőforrások közötti lekérdezések](../log-query/cross-workspace-query.md) az új [scheduledQueryRules API](/rest/api/monitor/scheduledqueryrules)-ban támogatottak. Ha továbbra is az [örökölt log Analytics riasztási API](../platform/api-alerts.md) -t használja a naplózási riasztások létrehozásához, megismerheti a váltást [itt](../alerts/alerts-log-api-switch.md).

## <a name="examples"></a>Példák
Az alábbi példákban a és a által használt naplózási lekérdezések szerepelnek, valamint azokat a lépéseket, amelyek segítségével `search` `union` módosíthatja ezeket a lekérdezéseket a riasztási szabályokban való használatra.

### <a name="example-1"></a>1\. példa
A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt a teljesítményadatok lekéréséhez a használatával `search` : 

``` Kusto
search *
| where Type == 'Perf' and CounterName == '% Free Space'
| where CounterValue < 30
```

A lekérdezés módosításához Kezdje a következő lekérdezéssel annak a táblának az azonosításához, amelyhez a tulajdonságok tartoznak:

``` Kusto
search *
| where CounterName == '% Free Space'
| summarize by $table
```

A lekérdezés eredménye azt mutatja, hogy a _CounterName_ tulajdonság a _perf_ táblából származik.

Ezt az eredményt használhatja a riasztási szabályhoz használni kívánt következő lekérdezés létrehozásához:

``` Kusto
Perf
| where CounterName == '% Free Space'
| where CounterValue < 30
```

### <a name="example-2"></a>2\. példa
A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt a teljesítményadatok lekéréséhez a használatával `search` : 

``` Kusto
search ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage =avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)  
```

A lekérdezés módosításához Kezdje a következő lekérdezéssel annak a táblának az azonosításához, amelyhez a tulajdonságok tartoznak:

``` Kusto
search ObjectName=="Memory" and CounterName=="% Committed Bytes In Use"
| summarize by $table
```

A lekérdezés eredménye azt mutatja, hogy a _ObjectName_ és a _CounterName_ tulajdonság a _perf_ táblából származik.

Ezt az eredményt használhatja a riasztási szabályhoz használni kívánt következő lekérdezés létrehozásához:

``` Kusto
Perf
| where ObjectName =="Memory" and CounterName=="% Committed Bytes In Use"
| summarize Avg_Memory_Usage=avg(CounterValue) by Computer
| where Avg_Memory_Usage between(90 .. 95)
```

### <a name="example-3"></a>3\. példa

A következő lekérdezéssel kell létrehoznia egy naplózási riasztási szabályt, amely a (z) és a (z) `search` és `union` a teljesítményadatok lekérésére használható: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| where Computer !in (
    union *
    | where CounterName == "% Processor Utility"
    | summarize by Computer)
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
```

A lekérdezés módosításához Kezdje a következő lekérdezéssel annak a táblának az azonosításához, amely a lekérdezés első részében szereplő tulajdonságok tartoznak: 

``` Kusto
search (ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total")
| summarize by $table
```

A lekérdezés eredménye azt mutatja, hogy ezek a tulajdonságok a _perf_ táblából származnak.

Most `union` a with `withsource` paranccsal azonosíthatja, hogy melyik forrástábla járult hozzá az egyes sorokhoz.

``` Kusto
union withsource=table *
| where CounterName == "% Processor Utility"
| summarize by table
```

A lekérdezés eredménye azt mutatja, hogy ezek a tulajdonságok a _perf_ táblából is származnak.

Ezeket az eredményeket a riasztási szabályhoz használni kívánt következő lekérdezés létrehozásához használhatja: 

``` Kusto
Perf
| where ObjectName == "Processor" and CounterName == "% Idle Time" and InstanceName == "_Total"
| where Computer !in (
    (Perf
    | where CounterName == "% Processor Utility"
    | summarize by Computer))
| summarize Avg_Idle_Time = avg(CounterValue) by Computer
``` 

### <a name="example-4"></a>4\. példa
A következő lekérdezéssel szeretne létrehozni egy naplózási riasztási szabályt, amely összekapcsolja a két lekérdezés eredményét `search` :

```Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    search in (Heartbeat) OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

A lekérdezés módosításához Kezdje a következő lekérdezéssel a csatlakozás bal oldalán található tulajdonságokat tartalmazó tábla azonosításához: 

``` Kusto
search Type == 'SecurityEvent' and EventID == '4625'
| summarize by $table
```

Az eredmény azt jelzi, hogy az illesztés bal oldalán lévő tulajdonságok a _SecurityEvent_ táblához tartoznak. 

Most a következő lekérdezéssel azonosítsa az illesztés jobb oldalán található tulajdonságokat tartalmazó táblát: 
 
``` Kusto
search in (Heartbeat) OSType == 'Windows'
| summarize by $table
```
 
Az eredmény azt jelzi, hogy az illesztés jobb oldalán lévő tulajdonságok a _szívverési_ táblához tartoznak.

Ezeket az eredményeket a riasztási szabályhoz használni kívánt következő lekérdezés létrehozásához használhatja: 

``` Kusto
SecurityEvent
| where EventID == '4625'
| summarize by Computer, Hour = bin(TimeGenerated, 1h)
| join kind = leftouter (
    Heartbeat
    | where OSType == 'Windows'
    | summarize arg_max(TimeGenerated, Computer) by Computer , Hour = bin(TimeGenerated, 1h)
    | project Hour , Computer
) on Hour
```

## <a name="next-steps"></a>Következő lépések
- További információ a Azure Monitor [naplózási értesítéseiről](alerts-log.md) .
- További információ a [naplók lekérdezéséről](../log-query/log-query-overview.md).