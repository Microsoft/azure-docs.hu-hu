---
title: Az Azure-méretezés hibaelhárítása
description: A Service Fabric, Virtual Machines, Web Apps és Cloud Services szolgáltatásban használt Azure automatikus skálázással kapcsolatos problémák nyomon követése.
ms.topic: conceptual
ms.date: 11/4/2019
ms.subservice: autoscale
ms.openlocfilehash: a0fed6c2d06edcb2c9eb8d715feb0ef6c6ade46f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101711399"
---
# <a name="troubleshooting-azure-autoscale"></a>Az Azure-méretezés hibaelhárítása
 
Azure Monitor Automatikus méretezéssel a megfelelő mennyiségű erőforrást futtathatja az alkalmazás terhelésének kezeléséhez. Lehetővé teszi erőforrások hozzáadását a terhelés növekedésének kezeléséhez, és pénzt takarít meg a tétlenül ülő erőforrások eltávolításával. A kiválasztható ütemterv, rögzített dátum-idő vagy erőforrás-metrika alapján méretezhető. További információ: az [autoscale áttekintése](autoscale-overview.md).

Az autoskálázási szolgáltatás metrikákat és naplókat biztosít a skálázási műveletek megismeréséhez és a műveletek elvégzéséhez szükséges feltételek kiértékeléséhez. Választ kaphat a következő kérdésekre:

- Miért lett kibővítve a szolgáltatásom?
- Miért nem léptékű a szolgáltatásom?
- Miért nem sikerült az autoskálázási művelet?
- Miért érdemes időt kiméretezni a méretezési műveletek során?
  
## <a name="autoscale-metrics"></a>Metrikák autoskálázása

Az autoscale [négy mérőszámot](../essentials/metrics-supported.md#microsoftinsightsautoscalesettings) biztosít a működésének megismeréséhez. 

- **Megfigyelt metrika értéke** – a skálázási műveletet a méretezési művelet elvégzésére kiválasztott metrika értékeként, az autoskálázási motor által látott vagy számított értékkel. Mivel egy automatikus méretezési beállítás több szabállyal is rendelkezhet, így több metrikai forrás is lehet, a "metrikus forrás" használatával szűrhet dimenzióként.
- **Metrika küszöbértéke** – a méretezési művelet elvégzéséhez beállított küszöbérték. Mivel egy automatikus méretezési beállítás több szabállyal is rendelkezhet, így több metrikai forrás is lehet, a "metrika szabály" használatával szűrheti a dimenziót.
- **Megfigyelt kapacitás** – a célként megadott erőforrás példányainak aktív száma, ahogyan azt az autoskálázási motor látja.
- **Kezdeményezett skálázási műveletek** – Az automatikus skálázási motor által kezdeményezett horizontális fel- és leskálázási műveletek száma. A kibővített és a műveletek méretezése alapján is szűrhet.

Az [Metrikaböngésző](../essentials/metrics-getting-started.md) használatával egyetlen helyen ábrázolhatja a fenti metrikákat. A diagramnak a következőknek kell megjelennie:

  - a tényleges metrika
  - a metrika, amelyet az autoskálázási motor látott/kiszámított
  - a skálázási művelet küszöbértéke
  - a kapacitás változása 

## <a name="example-1---analyzing-a-simple-autoscale-rule"></a>1. példa – egy egyszerű autoskálázási szabály elemzése 

A virtuálisgép-méretezési csoportokhoz egy egyszerű, az alábbiakra vonatkozó alapbeállítás áll rendelkezésre:

- kibővíti a készlet átlagos CPU-százalékos arányát 10 percnél nagyobb 70%-nál 
- a skálán, ha a készlet CPU-százalékos aránya kevesebb mint 5%-kal több, mint 10 perc. 

Tekintsük át az autoskálázási szolgáltatás mérőszámait.
 
![A képernyőfelvételen a virtuálisgép-méretezési csoport százalékos CPU-példája látható.](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-1.png)

![Virtuálisgép-méretezési csoport százalékos CPU-példája](media/autoscale-troubleshoot/autoscale-vmss-CPU-ex-full-2.png)

***1a. ábra – a virtuálisgép-méretezési csoport százalékos CPU-mérőszáma és a megfigyelt metrikus érték mérőszáma az automata skálázási beállításhoz***

![Metrika küszöbértéke és megfigyelt kapacitás](media/autoscale-troubleshoot/autoscale-metric-threshold-capacity-ex-full.png)

***1b. ábra – metrikus küszöbérték és megfigyelt kapacitás***

Az 1b. ábrán a kibővíthető szabály **metrikai küszöbértéke** (világoskék vonal) 70.  A **megfigyelt kapacitás** (sötétkék vonal) az aktív példányok számát jeleníti meg, amely jelenleg 3. 

> [!NOTE]
> A **metrikai küszöbértéket** a metrikai szabály dimenziójának kiskálázása (növelés) szabály alapján kell szűrnie, hogy megjelenjen a kibővíthető küszöbérték és a szabály (csökkenés) skálázása. 

## <a name="example-2---advanced-autoscaling-for-a-virtual-machine-scale-set"></a>2. példa – a virtuálisgép-méretezési csoport speciális automatikus skálázása

Van egy olyan automata méretezési beállítás, amely lehetővé teszi, hogy a virtuálisgép-méretezési csoport erőforrásai a saját metrika **kimenő folyamatai** alapján felskálázásra legyenek kibővítve. Figyelje meg, hogy a metrika küszöbértékének **felosztása példányok száma** beállítás be van jelölve. 

A skálázási művelet szabálya: 

Ha a **kimenő forgalom egy példánya** nagyobb, mint 10, akkor az autoskálázási szolgáltatásnak 1 példányt kell kiméreteznie. 

Ebben az esetben a rendszer kiszámítja az autoskálázási motor megfigyelt metrikai értékét, mivel a tényleges metrika értéke a példányok száma szerint oszlik meg. Ha a megfigyelt metrika értéke kisebb a küszöbértéknél, a rendszer nem kezdeményez Felskálázási műveletet. 
 
![A képernyőképen az átlagos kimenő folyamatok lap látható, amely egy virtuálisgép-méretezési csoportra vonatkozó, az autoscale mérőszámokat tartalmazó diagramokra mutat példát.](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-1.png)

![Virtuálisgép-méretezési csoportra vonatkozó mérőszámok – példa](media/autoscale-troubleshoot/autoscale-vmss-metric-chart-ex-2.png)

***2. ábra – virtuálisgép-méretezési csoport autoskálázási metrikái diagramja – példa***

A 2. ábrán két mérőszámot tartalmazó diagramot láthat. 

A fent látható diagram a **kimenő folyamatok** metrikájának tényleges értékét jeleníti meg. A tényleges érték 6. 

Az alsó diagramon néhány érték látható. 
 - A **megfigyelt metrikai érték** (világoskék) 3, mert 2 aktív példány van, és 6 osztva 2 3. 
 - A **megfigyelt kapacitás** (lila) megjeleníti az autoskálázási motor által látott példányszámot. 
 - A **metrika küszöbértéke** (Világoszöld) 10 értékre van állítva. 

Ha több méretezési műveletre vonatkozó szabály van, akkor a metrikák, illetve a **szűrő hozzáadása** lehetőséggel egy adott forrás vagy szabály alapján megtekintheti a metrikát. A metrikai diagramok felosztásával kapcsolatos további információkért lásd a [metrikus diagramok speciális funkcióit – felosztás](../essentials/metrics-charts.md#apply-splitting)

## <a name="example-3---understanding-autoscale-events"></a>3. példa – az autoscale-események ismertetése

A legutóbbi méretezési műveletek megtekintéséhez a **futtatási előzmények** lapon nyissa meg az alapbeállítások képernyőt. A lapon a **megfigyelt kapacitás** időbeli változása is látható. Ha további részleteket szeretne megtudni az automatikus skálázási műveletekről, beleértve az automatikus méretezési beállítások frissítésére/törlésére szolgáló műveleteket, tekintse meg a tevékenység naplóját, és szűrje az automatikus skálázási műveletek alapján.

![Az autoskálázási beállítások futtatási előzményei](media/autoscale-troubleshoot/autoscale-setting-run-history-smaller.png)

## <a name="autoscale-resource-logs"></a>Erőforrás-naplók autoskálázása

Ugyanaz, mint bármely más Azure-erőforrás esetében, az autoskálázási szolgáltatás [erőforrás-naplókat](../essentials/platform-logs-overview.md)biztosít. A naplóknak két kategóriája van.

- **Autoscale-értékelések** – az autoskálázási motor rögzíti az egyes feltételek kiértékeléséhez szükséges bejegyzéseket minden egyes ellenőrzés alkalmával.  A bejegyzés tartalmazza a metrikák megfigyelt értékeinek részleteit, a kiértékelt szabályokat, valamint azt, hogy a kiértékelés a méretezési műveletet eredményezte-e.

- Automatikus **skálázási műveletek** – a motor az automatikus skálázási szolgáltatás által kezdeményezett, a skálázási műveletek eredményeivel (sikeres, sikertelen, és az automatikus skálázási szolgáltatás által látott méretezési művelettel) kapcsolatos eseményeket rögzíti.

A Azure Monitor támogatott szolgáltatásokhoz hasonlóan a [diagnosztikai beállítások](../essentials/diagnostic-settings.md) segítségével is átirányíthatja ezeket a naplókat:

- részletes elemzés a Log Analytics munkaterülethez
- Event Hubs, majd a nem Azure-eszközökhöz
- Azure Storage-fiók archiváláshoz  

![Diagnosztikai beállítások autoskálázása](media/autoscale-troubleshoot/diagnostic-settings.png)

Az előző képen az Azure Portal autoscale diagnosztikai beállítások láthatók. Itt kiválaszthatja a diagnosztikai/erőforrás-naplók lapot, és engedélyezheti a naplók gyűjtését és útválasztását. Ugyanezt a műveletet a REST API, a CLI, a PowerShell és a Resource Manager-sablonok használatával is végrehajthatja a diagnosztikai beállításokhoz. Ehhez válassza ki az erőforrástípust a *Microsoft. elemzések/AutoscaleSettings* lehetőség kiválasztásával. 

## <a name="troubleshooting-using-autoscale-logs"></a>Hibaelhárítás az autoscale logs használatával 

A legjobb hibaelhárítási élmény érdekében javasoljuk, hogy a naplókat a munkaterületen Azure Monitor naplókra (Log Analytics) az autoskálázási beállítás létrehozásakor. Ez a folyamat az előző szakaszban található képen látható. A Log Analytics használatával hatékonyabban ellenőrizheti az értékeléseket és a méretezési műveleteket.

Miután konfigurálta az autoskálázási naplókat, hogy elküldjék a Log Analytics munkaterületre, végrehajthatja a következő lekérdezéseket a naplók vizsgálatához. 

Első lépésként próbálja ki ezt a lekérdezést a legutóbbi autoscale értékelési naplók megtekintéséhez:

```Kusto
AutoscaleEvaluationsLog
| limit 50
```

Vagy próbálja meg a következő lekérdezést a legutóbbi méretezési műveleti naplók megtekintéséhez:

```Kusto
AutoscaleScaleActionsLog
| limit 50
```

Ezeket a kérdéseket a következő szakaszban találja. 

## <a name="a-scale-action-occurred-that-i-didnt-expect"></a>Skálázási művelet történt, amely nem várt

Először hajtsa végre a méretezési művelet lekérdezését, hogy megkeresse a kívánt méretezési műveletet. Ha ez a legkésőbbi méretezési művelet, használja a következő lekérdezést:

```Kusto
AutoscaleScaleActionsLog
| take 1
```

Válassza ki a CorrelationId mezőt a Scale Actions naplóból. A CorrelationId segítségével keresse meg a megfelelő értékelési naplót. Az alábbi lekérdezés végrehajtása a skálázási művelethez vezető összes szabályt és kikötést megjeleníti.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId>"
```

## <a name="what-profile-caused-a-scale-action"></a>Milyen profilt okozott a méretezési művelet?

Méretezett művelet történt, de átfedésben van a szabályokkal és a profilokkal, és nyomon kell követnie, hogy melyik okozta a műveletet. 

Keresse meg a skálázási művelet correlationId (ahogy az 1. példában is látható), majd hajtsa végre a lekérdezést a kiértékelési naplókon, hogy többet tudjon meg a profilról.

```Kusto
AutoscaleEvaluationsLog
| where CorrelationId = "<correliationId_Guid>"
| where ProfileSelected == true
| project ProfileEvaluationTime, Profile, ProfileSelected, EvaluationResult
```

A teljes profil kiértékelése a következő lekérdezéssel is jobban érthető

```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName contains == "profileEvaluation"
| project OperationName, Profile, ProfileEvaluationTime, ProfileSelected, EvaluationResult
```

## <a name="a-scale-action-did-not-occur"></a>Nem történt méretezési művelet

Vártam egy méretezési műveletet, és nem történt meg. Lehet, hogy nem találhatók méretezési műveletek eseményei vagy naplói.

Ha metrikus skálázási szabályt használ, tekintse át az autoskálázási metrikákat. Lehetséges, hogy a **megfigyelt metrika értéke** vagy a **megfigyelt kapacitás** nem a várt érték, ezért a skálázási szabály nem volt tűz. Továbbra is láthatja az értékeléseket, de nem kibővíthető szabályt. Azt is megteheti, hogy a leállási idő megtartotta a skálázási műveletet. 

 Tekintse át az autoskálázási kiértékelési naplókat abban az időszakban, amikor a méretezési műveletet elvárta. Tekintse át az összes értékelést, és miért úgy döntött, hogy ne indítson el méretezési műveletet.


```Kusto
AutoscaleEvaluationsLog
| where TimeGenerated > ago(2h)
| where OperationName == "MetricEvaluation" or OperationName == "ScaleRuleEvaluation"
| project OperationName, MetricData, ObservedValue, Threshold, EstimateScaleResult
```

## <a name="scale-action-failed"></a>A skálázási művelet nem sikerült

Előfordulhat, hogy az autoskálázási szolgáltatás átvette a méretezési műveletet, de a rendszer úgy döntött, hogy nem méretezi vagy nem tudta befejezni a méretezési műveletet. Ezzel a lekérdezéssel megkeresheti a sikertelen skálázási műveleteket.

```Kusto
AutoscaleScaleActionsLog
| where ResultType == "Failed"
| project ResultDescription
```

Riasztási szabályok létrehozásával értesítést kaphat az autoscale műveletekről vagy hibákról. Riasztási szabályokat is létrehozhat, hogy értesítést kapjon az autoskálázási eseményekről.

## <a name="schema-of-autoscale-resource-logs"></a>Az erőforrás-naplók sémája

További információ: az [erőforrás-naplók autoskálázása](autoscale-resource-log-schema.md)

## <a name="next-steps"></a>Következő lépések
Olvassa el az [autoscale ajánlott eljárásaival](autoscale-best-practices.md)kapcsolatos információkat.