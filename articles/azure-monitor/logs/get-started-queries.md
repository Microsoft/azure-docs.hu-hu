---
title: Ismerkedés a Azure Monitor-naplózási lekérdezésekkel | Microsoft Docs
description: Ez a cikk egy oktatóanyagot tartalmaz a naplók Azure Monitorban való írásának első lépéseihez.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: c87b70c74a5de9cc4b6c48935b93fdd13295f0f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052330"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Ismerkedés az Azure Monitor-naplólekérdezésekkel

> [!NOTE]
> Ezt a gyakorlatot a saját környezetében hajthatja végre, ha legalább egy virtuális gépről gyűjti az adatgyűjtést. Ha nem, akkor használja a [bemutató környezetét](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), amely rengeteg mintavételi adatmennyiséget tartalmaz.  Ha már tudja, hogyan lehet lekérdezni a KQL-ben, de csak az erőforrás típusa (i) alapján kell gyorsan létrehoznia hasznos lekérdezéseket, tekintse meg a [mentett példákat tartalmazó ablaktáblát](../logs/example-queries.md).

Ez az oktatóanyag bemutatja, hogyan írhat napló lekérdezéseket Azure Monitor. A következőket fogja megtanítani:

- A lekérdezési struktúra ismertetése
- Lekérdezési eredmények rendezése
- Lekérdezés eredményeinek szűrése
- Időtartomány megadásának időpontja
- Adja meg, hogy mely mezők szerepeljenek az eredmények között
- Egyéni mezők definiálása és használata
- Összesítés és csoportosítás eredményei

A Azure Portal Log Analytics használatáról szóló oktatóanyagért lásd: az [Azure Monitor log Analytics használatának első lépései](./log-analytics-tutorial.md).<br>
További információ a Azure Monitor naplózott lekérdezésekről: [Azure monitor-lekérdezések áttekintése](../logs/log-query-overview.md).

Kövesse az alábbi oktatóanyag videós verzióját:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Új lekérdezés írása

A lekérdezések a táblanév vagy a *keresési* paranccsal kezdődhetnek. A táblázat nevével kell kezdődnie, mert egyértelmű hatókört határoz meg a lekérdezéshez, és javítja a lekérdezés teljesítményét és az eredmények relevanciáját.

> [!NOTE]
> Az Azure Monitor által használt Kusto-lekérdezési nyelv megkülönbözteti a kis- és nagybetűket. A nyelvi kulcsszavakat általában kisbetűvel kell írni. Ha a lekérdezésben táblák vagy oszlopok nevét használja, ügyeljen arra, hogy a megfelelő esetet használja a séma ablaktáblán látható módon.

### <a name="table-based-queries"></a>Tábla alapú lekérdezések

Az Azure Monitor több oszlopból álló táblákba rendezi a naplóadatokat. Az elemzési portálon az összes tábla és oszlop megjelenik a Log Analytics sémája ablaktáblán. Azonosítson egy olyan táblát, amely érdekli, és tekintse meg a következőt:

```Kusto
SecurityEvent
| take 10
```

A fenti lekérdezés a *SecurityEvent* tábla 10 találatát adja vissza, a megadott sorrendben. Ez egy egyszerű módja annak, hogy áttekintést készítsen egy táblázatról, és megértse a szerkezetét és tartalmát. Vizsgáljuk meg, hogyan épül fel:

* A lekérdezés a tábla neve *SecurityEvent* kezdődik – ez a rész a lekérdezés hatókörét határozza meg.
* A pipe (|) karakter elválasztja a parancsokat, így a kimenete az első a következő parancs bemenetében. Tetszőleges számú vezetékes elemet adhat hozzá.
* A cső után a **Take** parancs, amely egy adott számú tetszőleges rekordot ad vissza a táblából.

Gyakorlatilag még a Hozzáadás nélkül is futtathatjuk a lekérdezést `| take 10` , amely továbbra is érvényes lesz, de akár 10 000 eredményt is visszatérhet.

### <a name="search-queries"></a>Keresési lekérdezések

A keresési lekérdezések kevésbé strukturáltak, és általában alkalmasabbak arra, hogy olyan rekordokat keressenek, amelyek egy adott értéket tartalmaznak a saját oszlopaikban:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

Ez a lekérdezés a "kriptográfia" kifejezést tartalmazó rekordokra keres rá a *SecurityEvent* táblában. Ezekből a rekordokból 10 rekordot ad vissza és jelenít meg. Ha kihagyja a `in (SecurityEvent)` részt, és csak futtatja `search "Cryptographic"` a parancsot, a Keresés az *összes* tábla fölé kerül, ami hosszabb ideig tart, és kevésbé hatékony.

> [!WARNING]
> A keresési lekérdezések általában lassabbak, mint a tábla alapú lekérdezések, mert több adatfeldolgozást kell feldolgozniuk. 

## <a name="sort-and-top"></a>Rendezés és felül
Habár **hasznos** lehet néhány rekord beszerzése, az eredmények ki lesznek választva, és nem jelennek meg külön sorrendben. Rendezett nézet beszerzéséhez az előnyben részesített oszlop szerint **rendezhet** :

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Ez azonban túl sok eredményt adhat vissza, és előfordulhat, hogy hosszabb időt is igénybe vehet. A fenti lekérdezés a TimeGenerated oszlop alapján rendezi *a teljes* SecurityEvent táblát. Az elemzési portál ezt követően korlátozza a megjelenítést, hogy csak a 10 000-es rekordokat jelenítse meg. Ez a megközelítés természetesen nem optimális.

A legjobb megoldás, ha csak a legújabb 10 rekordot szeretné lekérni, a **Top** értéket használja, amely a kiszolgáló oldalán rendezi a teljes táblázatot, majd visszaadja a legfontosabb rekordokat:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

A csökkenő érték az alapértelmezett rendezési sorrend, ezért általában kihagyjuk a **desc** argumentumot. A kimenet így fog kinézni:

![Első 10](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>Where: szűrés egy feltétellel
A szűrők a nevük alapján szűrik az adott feltételt. Ez a leggyakoribb módszer a lekérdezési eredmények megfelelő információkra való korlátozására.

Szűrő lekérdezéshez való hozzáadásához használja a **Where** operátort, majd egy vagy több feltételt. Például a következő lekérdezés csak olyan *SecurityEvent* -rekordokat ad vissza, amelyekben a _szint_ értéke _8_:

```Kusto
SecurityEvent
| where Level == 8
```

A szűrési feltételek írásakor a következő kifejezéseket használhatja:

| Kifejezés | Leírás | Példa |
|:---|:---|:---|
| == | Az egyenlőség ellenõrzése<br>(kis-és nagybetűk megkülönböztetése) | `Level == 8` |
| =~ | Az egyenlőség ellenõrzése<br>(kis-és nagybetűk megkülönböztetése) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Egyenlőtlenségek keresése<br>(mindkét kifejezés azonos) | `Level != 4` |
| *and*, *or* | Feltételek között használandó| `Level == 16 or CommandLine != ""` |

Ha több feltételt szeretne szűrni, **használhatja a következőt:**

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

vagy a cső több elemét, **ahol** egymás után:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Az értékek különböző típusokkal rendelkezhetnek, ezért előfordulhat, hogy a megfelelő típus összehasonlításához el kell végeznie őket. A SecurityEvent *szint* oszlop például karakterlánc típusú, ezért a numerikus operátorok használata előtt át kell őket adni egy numerikus típusba (például *int* vagy *Long*). `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Időtartomány megadásának időpontja

### <a name="time-picker"></a>Időválasztó

Az időválasztó a Futtatás gomb mellett látható, és azt jelzi, hogy az elmúlt 24 órában csak rekordok vannak lekérdezve. Ez az összes lekérdezésre alkalmazott alapértelmezett időtartomány. Ha csak az elmúlt óra rekordjait szeretné lekérni, válassza az _elmúlt óra_ lehetőséget, majd futtassa újra a lekérdezést.

![Időválasztó](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Időszűrő a lekérdezésben

A lekérdezéshez Időszűrő hozzáadásával is megadhatja a saját időtartományát. Az időszűrőt közvetlenül a tábla neve után helyezheti el: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

A fenti időpontnál a  `ago(30m)` "30 perce" kifejezés azt jelenti, hogy ez a lekérdezés csak az elmúlt 30 perc rekordokat adja vissza. Más időegységek közé tartoznak a napok (2D), a Minutes (25m) és a másodperc (10-es).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt és bővítés: Select és számítási oszlopok

A **Project** használatával kiválaszthatja az eredményekbe felvenni kívánt oszlopokat:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Az előző példa ezt a kimenetet hozza létre:

![Projekt eredményeinek lekérdezése](media/get-started-queries/project.png)

A **Project** használatával is átnevezheti az oszlopokat, és újakat is meghatározhat. A következő példában a Project használatával végezheti el a következőket:

* Csak a *számítógép* -és *TimeGenerated* eredeti oszlopainak kiválasztása.
* A *tevékenység* oszlopot jeleníti meg *EventDetails*.
* Hozzon létre egy új, *EventCode* nevű oszlopot. Az **alkarakterlánc ()** függvény csak az első négy karakter beolvasására szolgál a tevékenység mezőből.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

a **kibővítés** megtartja az eredményhalmaz összes eredeti oszlopát, és meghatározza a további beállításokat is. A következő lekérdezés a **kibővítést** használja a *EventCode* oszlop hozzáadásához. Vegye figyelembe, hogy ez az oszlop nem jeleníthető meg a tábla végén, amely esetben a rekordok részleteit ki kell bontani a megtekintéshez.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Összefoglalás: sorok összesített csoportjai
Az **Összefoglalás** használatával azonosíthatja a rekordok csoportjait egy vagy több oszlop szerint, és összesítéseket alkalmazhat rájuk. Az **összegzések** leggyakoribb használata a *Count*, amely az egyes csoportok eredményeinek számát adja vissza.

A következő lekérdezés az elmúlt óra összes *teljesítményszámláló* -rekordját áttekinti, csoportosítja őket *ObjectName* szerint, és megszámolja az egyes csoportok rekordjait: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Időnként érdemes lehet csoportokat definiálni több dimenzió alapján. Az értékek minden egyedi kombinációja egy külön csoportot határoz meg:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Egy másik gyakori használat, hogy matematikai vagy statisztikai számításokat hajtson végre az egyes csoportokon. Például a következő kiszámítja az egyes számítógépek átlagos *kártyabirtokos számlájának megterhelését* :

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Sajnos a lekérdezés eredményei értelmetlenek, mivel összekevertük a különböző teljesítményszámlálókat. A *CounterName* és a *számítógép* minden kombinációja esetében az átlagot külön kell kiszámítani:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Összesítés egy időoszlop alapján
A csoportosítási eredmények egy időoszlopon vagy egy másik folytonos értéken is alapulhatnak. Egyszerűen Összefoglalva `by TimeGenerated` , bár az adott időtartományon belül minden egyes ezredmásodperchez létrehozhatnak csoportokat, mivel ezek egyedi értékek. 

Ha folytonos értékek alapján szeretne csoportokat létrehozni, érdemes lehet a tartományt a **bin** használatával felügyelt egységekre bontani. A következő lekérdezés az adott számítógépen a szabad memóriát (*rendelkezésre álló MB*-ot) mérni kívánó teljesítmény *-rekordokat* elemzi. Kiszámítja az 1 órás időszak átlagos értékét az elmúlt 7 napban:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

A kimeneti világosabb kiválasztásához jelölje ki, ha idődiagramként szeretné megjeleníteni a rendelkezésre álló memóriát:

![Memória lekérdezése az idő függvényében](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Következő lépések

- További információ a sztringek használatával végzett naplózási lekérdezésekben a karakterláncok használata [Azure monitor a naplók lekérdezésében](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- További információ az adatösszesítések naplózási lekérdezésekben való összesítéséről [Azure monitor log-lekérdezések speciális összesítései között](/azure/data-explorer/write-queries#advanced-aggregations).
- Megtudhatja, hogyan csatlakozhat több táblázatból származó adatokhoz [Azure monitor log-lekérdezések](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)összekapcsolásával.
- Kérje le a dokumentációt a teljes Kusto lekérdezési nyelvre a [KQL nyelvi](/azure/kusto/query/)dokumentációjában.