---
title: Azure-metrikákra vonatkozó riasztások hibaelhárítása
description: Gyakori problémák a Azure Monitor metrikák riasztásaival és a lehetséges megoldásokkal kapcsolatban.
author: harelbr
ms.author: harelbr
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: fc9af94b07add5728201baaa8fa6992728a60a8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786008"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Az Azure Monitor metrikaalapú riasztásaival kapcsolatos hibák elhárítása 

Ez a cikk a metrikás riasztások Azure Monitor [gyakori problémáit](alerts-metric-overview.md) és azok hibaelhárítását ismerteti.

Azure Monitor riasztások proaktívan értesítik, ha fontos feltételek találhatók a monitorozási adatokban. Lehetővé teszik a problémák azonosítását és megoldását, mielőtt a felhasználók észreveik őket. A riasztásokkal kapcsolatos további információkért lásd: A riasztások [áttekintése a Microsoft Azure.](./alerts-overview.md)

## <a name="metric-alert-should-have-fired-but-didnt"></a>A metrikariasztásnak el kellett volna lennie, de nem kellett volna 

Ha úgy véli, hogy egy metrikariasztásnak kellett volna elriasztva lennie, de nem jött létre, és nem található a Azure Portal, próbálkozzon a következő lépésekkel:

1. **Konfiguráció** – Tekintse át a metrikák riasztási szabályának konfigurációját, és ellenőrizze, hogy megfelelően van-e konfigurálva:
    - Ellenőrizze, hogy az **Összesítés típusa** és az Összesítés **részletessége (időszak)** az elvárt módon van-e konfigurálva. **Az** aggregáció típusa határozza meg a metrikaértékek összesítésének (további információ [itt),](../essentials/metrics-aggregation-explained.md#aggregation-types)az Összesítés részletessége **(időszak)** pedig azt határozza meg, hogy a kiértékelés milyen messze összesíti a metrikaértékeket a riasztási szabály futtatásakor.
    -  Ellenőrizze, hogy **a Küszöbérték vagy** a **Érzékenység** a várt módon van-e konfigurálva.
    - Dinamikus küszöbértékeket használó riasztási szabály esetén ellenőrizze, hogy  speciális beállítások vannak-e  konfigurálva, mivel a Szabálysértések száma szűrheti a riasztásokat, az Adatok mellőzése pedig hatással lehet a küszöbértékek kiszámítására.

       > [!NOTE] 
       > A dinamikus küszöbértékek legalább 3 napot és 30 metrikamintát igényelnek az aktívvá válás előtt.

2. **Elbocsátva, de értesítés nélkül** – Tekintse át az riasztások [listáját,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) és ellenőrizze, hogy megtalálja-e az elbocsátott riasztást. Ha látja a riasztást a listában, de probléma van néhány műveletével vagy értesítésével, további információt itt [láthat.](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)

3. **Már aktív** – Ellenőrizze, hogy van-e már aktivált riasztás abban a metrika-idősorozatban, amelyről riasztást várt. A metrikariasztások állapotalapúak, ami azt jelenti, hogy a riasztások egy adott metrika-idősorozatra való aktiválását követően az adott idősorozatra vonatkozó további riasztások addig nem lesznek aktiválva, amíg a probléma meg nem szűnik. Ez a kialakítás csökkenti a zajt. A riasztás automatikusan megoldódik, ha a riasztási feltétel három egymást követő értékelés során nem teljesül.

4. **Használt dimenziók** – Ha kiválasztott néhány dimenzióértéket egy metrika számára, [a](./alerts-metric-overview.md#using-dimensions)riasztási szabály minden egyes metrikaidősort figyel (a dimenzióértékek kombinációja által meghatározottak szerint) egy küszöbérték túllépése esetén. Az összesített metrikaidősorok figyelése (kiválasztott dimenziók nélkül) egy további riasztási szabályt konfigurál a metrikán dimenziók kiválasztása nélkül.

5. **Összesítés és idő részletessége** – Ha metrikadiagramokkal vizualizálja a metrikát, [](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)győződjön meg a következőről:
    * A  metrikadiagramon kiválasztott Összesítés  ugyanaz, mint a riasztási szabályban megadott Összesítés típusa
    * A kiválasztott **Idő részletessége** megegyezik a riasztási szabályban az Összesítés **részletessége (időszak)** beállításával (és nem automatikusra állítva)

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>Metrikariasztás akkor lett volna elbocsátva, amikor nem kellett volna

Ha úgy véli, hogy a metrikariasztásnak nem kellett volna elriasztva, de mégis így történt, az alábbi lépések segíthetnek a probléma megoldásában.

1. Tekintse át [az adott riasztások listáját,](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) és keresse meg az adott riasztást, és kattintson rá a részleteinek megtekintéséhez. Tekintse át a **Miért aktiválódott** ez a riasztás? alatt található  információkat, és tekintse meg a metrikák diagramját, a Metrikaértéket és a Küszöbértéket a riasztás aktiválódásakor. 

    > [!NOTE] 
    > Ha dinamikus küszöbértékek feltételtípust használ, és úgy gondolja, hogy a használt küszöbértékek helytelenek voltak, akkor a rosszalló ikon használatával adjon visszajelzést. Ez a visszajelzés hatással lesz a gépi tanulási algoritmusok kutatására, és segít a jövőbeli észlelések javításában.

2. Ha több dimenzióértéket választott ki egy metrikhoz,  a riasztás akkor aktiválódik, ha a metrikaidősorok bármelyike (a dimenzióértékek kombinációja által meghatározott módon) túllépi a küszöbértéket. A méretek metrikariasztásokban való használatáról [itt](./alerts-metric-overview.md#using-dimensions) talál további információt.

3. Tekintse át a riasztási szabály konfigurációját, és ellenőrizze, hogy megfelelően van-e konfigurálva:
    - Ellenőrizze, hogy az **Összesítés típusa,** Az összesítés **részletessége (időszak)** és **a Küszöbérték** vagy az **Érzékenység** a vártnak megfelelően van-e konfigurálva
    - Dinamikus küszöbértékeket használó riasztási szabály esetén ellenőrizze, hogy  vannak-e speciális beállítások  konfigurálva, mivel a Szabálysértések száma szűrheti a riasztásokat, és az Adatok mellőzése az előtt hatással lehet a küszöbértékek kiszámítására

   > [!NOTE]
   > A dinamikus küszöbértékek legalább 3 napot és 30 metrikamintát igényelnek az aktívvá válás előtt.

4. Ha a metrikát Metrikák diagram használatával vizualizálja, győződjön meg [a](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)következőről:
    - A metrikadiagramon kiválasztott Összesítés  ugyanaz, mint a riasztási szabályban megadott Összesítés típusa 
    - A kiválasztott **Idő részletessége** megegyezik a riasztási szabályban az Összesítés **részletessége (időszak)** beállításával (és nem automatikusra állítva)

5. Ha a riasztás akkor lett bekapcsolva, amikor már vannak olyan riasztások, amelyek ugyanezeket a feltételeket figyelik (amelyek nem oldódnak meg), ellenőrizze, hogy a riasztási szabály *autoMitigate* tulajdonsága **false** (hamis) értékkel lett-e konfigurálva (ez a tulajdonság csak REST/PowerShell/PARANCSSORi felület használatával konfigurálható, ezért ellenőrizze a riasztási szabály üzembe helyezéséhez használt szkriptet). Ebben az esetben a riasztási szabály nem oldja fel automatikusan az elbocsátott riasztásokat, és nem követeli meg az éles riasztások feloldását az újra elvégződés előtt.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>Nem találom a metrikát, amely alapján riasztást lehet kapni – virtuális gépek vendégmetrikák

A virtuális gépek vendég operációs rendszeri metrikákra (például memória, lemezterület) vonatkozó riasztásához győződjön meg arról, hogy telepítette az adatok gyűjtéséhez szükséges ügynököt a Azure Monitor Metricsbe:
- [Windows rendszerű virtuális gépek esetén](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux rendszerű virtuális gépek esetén](../essentials/collect-custom-metrics-linux-telegraf.md)

A virtuális gépek vendég operációs rendszerből való adatgyűjtésével kapcsolatos további információkért lásd [itt:](../vm/monitor-vm-azure.md#guest-operating-system).

> [!NOTE] 
> Ha vendégmetrikákat konfigurált egy Log Analytics-munkaterületre való küldéshez, a metrikák  a Log Analytics-munkaterület erőforrása alatt jelennek meg, és csak azután kezdik meg az adatok megjelenítését, hogy létrehoz egy riasztási szabályt, amely figyeli őket. Ehhez kövesse a [naplók metrikariasztásának konfigurálására](./alerts-metric-logs.md#configuring-metric-alert-for-logs) szolgáló lépéseket.

> [!NOTE] 
> A metrikákra vonatkozó riasztások jelenleg nem támogatják a több virtuális géphez egyetlen riasztási s szabálysal rendelkező vendégmetrikák monitorozását. Ezt a naplóriasztás [szabályával érheti el.](./alerts-unified-log.md) Győződjön meg arról, hogy a vendégmetrikák egy Log Analytics-munkaterületre vannak gyűjtve, és hozzon létre egy naplóriasztás-szabályt a munkaterületen.

## <a name="cant-find-the-metric-to-alert-on"></a>Nem található a metrikát, amely alapján riasztást küld

Ha egy adott metrikához szeretne riasztást létrehozni, de nem látja a metrikát a riasztási szabály létrehozásakor, ellenőrizze az alábbiakat:
- Ha nem lát az erőforrásra vonatkozó metrikákat, [ellenőrizze, hogy a metrikaalapú riasztások támogatják-e az erőforrás típusát](./alerts-metric-near-real-time.md).
- Ha lát az erőforrásra vonatkozó metrikákat, de egy adott metrikát nem talál, [ellenőrizze, hogy a metrika elérhető-e](../essentials/metrics-supported.md), és ha igen, a leírásában ellenőrizze, hogy nem csak az erőforrás bizonyos verzióiban vagy kiadásában érhető-e el.
- Ha a metrika nem érhető el az erőforráshoz, előfordulhat, hogy az erőforrásnaplókban elérhető lesz, és naplóriasztásokkal monitorozható. Itt tekinthet meg további információt az [erőforrásnaplóknak az Azure-erőforrásokból történő gyűjtésével és elemzésével](../essentials/tutorial-resource-logs.md) kapcsolatban.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>Nem található az a metrikadimenzió, amelyről riasztást küld

Ha egy metrika [](./alerts-metric-overview.md#using-dimensions)adott dimenzióértékeivel kapcsolatos riasztást keres, de nem találja ezeket az értékeket, vegye figyelembe a következőket:

1. Eltarthat egy pár percig, amíg a dimenzióértékek megjelennek a **Dimenzióértékek** listában
2. A megjelenített dimenzióértékek az elmúlt napban gyűjtött metrikaadatokon alapulnak
3. Ha a dimenzió értéke még nincs kibocsátva, vagy nem jelenik meg, az „Egyéni érték hozzáadása” lehetőséggel adhat hozzá egyéni dimenzió értéket
4. Ha egy dimenzió összes lehetséges értékére (a jövőbeli értékeket is beleértve) szeretne riasztást kapni, válassza az "Összes aktuális és jövőbeli érték kiválasztása" lehetőséget
5. Az egyes erőforrások Application Insights egyéni metrikadimenziói alapértelmezés szerint ki vannak kapcsolva. Ezen egyéni metrikák dimenziógyűjteményének bekapcsolásért lásd [itt:](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>A metrikariasztás szabályai továbbra is definiálva vannak egy törölt erőforráson 

Amikor töröl egy Azure-erőforrást, az ahhoz társított metrikariasztási szabályok nem törlődnek automatikusan. Törölt erőforráshoz tartozó riasztási szabályok törlése:

1. Nyissa meg az erőforráscsoportot, amelyben a törölt erőforrás volt
1. Az erőforrások listájában jelölje be a **Rejtett típusok megjelenítése** jelölőnégyzetet
1. Szűrje a listát típus szerint = **microsoft.insights/metricalerts**
1. Válassza ki a megfelelő riasztási szabályokat, majd válassza a **Törlés lehetőséget**

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>Metrikákra vonatkozó riasztások beállítása minden alkalommal, amikor teljesül a feltételem

A metrikariasztás alapértelmezés szerint állapot-figyelő, ezért a rendszer nem küld további riasztásokat, ha egy adott idősoron már létezik egy riasztás. Ha egy adott metrikariasztás-szabályt állapot nélkülire szeretne beállítani, és riasztást szeretne kapni minden olyan értékelésről, amelyben a riasztási feltétel teljesül, hozza létre a riasztási szabályt programozott módon (például [a Resource Manager,](./alerts-metric-create-templates.md) [a PowerShell,](/powershell/module/az.monitor/) [a REST](/rest/api/monitor/metricalerts/createorupdate), [a PARANCSSORi](/cli/azure/monitor/metrics/alert)felület használatával), és állítsa az *autoMitigate* tulajdonságot "False" (Hamis) állapotra.

> [!NOTE] 
> A metrikariasztás szabályának állapot nélküli beállítása megakadályozza az aktív riasztások megoldását, így az aktív riasztások a 30 napos megőrzési időszakig még akkor is aktív állapotban maradnak, ha a feltétel már nem teljesül.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>Riasztási szabály meghatározása még nem kibocsátott egyéni metrikákhoz

Metrikariasztás-szabály létrehozásakor a rendszer ellenőrzi a metrika nevét a [Metric Definitions API-n,](/rest/api/monitor/metricdefinitions/list) és ellenőrzi, hogy létezik-e. Bizonyos esetekben még a kibocsátás előtt is létre szeretne hozni egy riasztási szabályt egy egyéni metrikán. Ha például egy egyéni metrikát kibocsátó Resource Manager-erőforrást hoz létre (Resource Manager Application Insights-sablon használatával), akkor a metrikát figyelő riasztási s szabálysal együtt.

Ha el akarja kerülni, hogy az üzembe helyezés meghiúsuljon az egyéni metrika definíciójának ellenőrzésekor, használhatja a *skipMetricValidation* paramétert a riasztási szabály feltételek szakaszában, ami kihagyja a metrikaérvényesítést. Az alábbi példa bemutatja, hogyan használhatja ezt a paramétert egy Resource Manager sablonban. További információkért tekintse meg a metrikák [riasztási Resource Manager létrehozásához szükséges teljes sablonmintákat.](./alerts-metric-create-templates.md)

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                "name" : "condition1",
                "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                "dimensions":[],
                "operator": "GreaterThan",
                "threshold" : 10,
                "timeAggregation": "Average",
                "skipMetricValidation": true
            }
        ]
    }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Egy metrikariasztás szabály Azure Resource Manager sablonjának exportálása a Azure Portal

A Resource Manager riasztási szabály sablonjának exportálása segít megérteni annak JSON-szintaxisát és tulajdonságait, és a későbbi üzembe helyezések automatizálását is lehetővé teszi.
1. A Azure Portal nyissa meg a riasztási szabályt a részleteinek megtekintéséhez.
2. Kattintson a **Tulajdonságok** elemre.
3. Az **Automation alatt** válassza a Sablon **exportálása lehetőséget.**

## <a name="metric-alert-rules-quota-too-small"></a>Túl kicsi metrikariasztás-szabályok kvótája

Az előfizetésenkénti metrikariasztás-szabályok engedélyezett száma a [kvótakorlátok hatálya alá esik.](../service-limits.md)

Ha elérte a kvótakorlátot, az alábbi lépések segíthetnek a probléma megoldásában:
1. Próbálja meg törölni vagy letiltani a már nem használt metrikariasztás-szabályokat.

2. Váltson olyan metrikariasztási szabályok használatára, amelyek több erőforrást monitoroznak. Ezzel a képességgel egyetlen riasztási szabály több erőforrást is figyelhet a kvótába beleszámolt egyetlen riasztási szabály használatával. A képességről és a támogatott erőforrástípusokról [itt](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor) talál további információt.

3. Ha növelni kell a kvótakorlátot, nyisson egy támogatási kérést, és adja meg a következő információkat:

    - Előfizetés-azonosító(k), amelyekhez növelni kell a kvótakorlátot
    - A kvóta növeléséhez szükséges erőforrástípus: **Metrikákra vonatkozó riasztások** vagy Metrikák **riasztásai (klasszikus)**
    - Kért kvótakorlát

## <a name="check-total-number-of-metric-alert-rules"></a>A metrikák riasztási szabályainak teljes számának ellenőrzése

A metrikákra vonatkozó riasztási szabályok aktuális használatának ellenőrzéshez kövesse az alábbi lépéseket.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Nyissa meg a **Riasztások** képernyőt, és kattintson a **Riasztási szabályok kezelése** elemre
2. Szűrhet a megfelelő előfizetésre az Előfizetés legördülő **vezérlő** használatával
3. Ügyeljen arra, hogy NE szűrje egy adott erőforráscsoportra, erőforrástípusra vagy erőforrásra
4. A Jel **típusa legördülő** vezérlőben válassza a **Metrikák lehetőséget**
5. Ellenőrizze, hogy az **Állapot** legördülő vezérlő engedélyezve **van-e**
6. A metrikákra vonatkozó riasztási szabályok teljes száma a riasztási szabályok listája felett jelenik meg

### <a name="from-api"></a>Küldő API

- PowerShell – [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2)
- REST API – [List by subscription](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI – [az monitor metrics alert list](/cli/azure/monitor/metrics/alert#az_monitor_metrics_alert_list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>Riasztási szabályok kezelése Resource Manager, REST API, PowerShell vagy Azure CLI használatával

Ha problémái vannak a metrikariasztás létrehozása, frissítése, leolvasása vagy törlése során Resource Manager-sablonok, az REST API, a PowerShell vagy az Azure parancssori felület (CLI) használatával, az alábbi lépések segíthetnek a probléma megoldásában.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

- Tekintse át az [Azure-beli üzemelő példányok gyakori hibáinak](../../azure-resource-manager/templates/common-deployment-errors.md) listáját, és ez alapján végezze el a hibaelhárítást
- Tekintse meg a [metrikakriasztásokat Azure Resource Manager sablonpéékban,](./alerts-metric-create-templates.md) és ellenőrizze, hogy minden paramétert megfelelően adott-e meg

### <a name="rest-api"></a>REST API

Tekintse át [REST API útmutatót,](/rest/api/monitor/metricalerts/) és ellenőrizze, hogy minden paramétert megfelelően adott-e meg

### <a name="powershell"></a>PowerShell

Győződjön meg arról, hogy a megfelelő PowerShell-parancsmagokat használja a metrikák riasztásaihoz:

- A metrikariasztások PowerShell-parancsmagjai az [Az.Monitor modulban](/powershell/module/az.monitor/) érhetők el
- Győződjön meg arról, hogy az új (nem klasszikus) metrikák riasztásai esetében a "V2" végződésű parancsmagokat használja (például [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2))

### <a name="azure-cli"></a>Azure CLI

Győződjön meg arról, hogy a megfelelő PARANCSSORi felületi parancsokat használja a metrikák riasztásaihoz:

- A metrikaalapú riasztásokra vonatkozó parancssori felületi parancsok a következőképpen kezdődnek: `az monitor metrics alert` Az [Azure CLI-referenciából](/cli/azure/monitor/metrics/alert) többet tudhat meg a szintaxisról.
- Itt talál egy [példát a metrikaalapú riasztások parancssori felülettel történő használatára](./alerts-metric.md#with-azure-cli)
- Ha egyéni metrikához szeretne riasztást beállítani, ügyeljen arra, hogy a metrika nevéhez a megfelelő metrikanévtér előtagját adja meg: NÉVTÉR.METRIKA

### <a name="general"></a>Általános kérdések

- Ha hibaüzenetet `Metric not found` kap:

   - Platformmetrika esetén: Győződjön meg arról, hogy a metrika nevét használja a Azure Monitor [metrikák](../essentials/metrics-supported.md)oldalán, és nem a metrika **megjelenítendő nevét** 

   - Egyéni metrika esetén: Győződjön meg arról, hogy a metrika már ki van bocsátva (nem hozhat létre riasztási szabályt olyan egyéni metrikákhoz, amelyek még nem léteznek), és hogy az egyéni metrika névterét biztosítja -e (lásd az Resource Manager-sablonra vonatkozó példát [itt)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)

- Ha metrikakriasztásokat [hoz létre a naplókhoz,](./alerts-metric-logs.md)győződjön meg arról, hogy a megfelelő függőségek is szerepelnek benne. Itt találhat egy [mintasablont](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs).

- Ha több feltételt tartalmazó riasztási szabályt hoz létre, vegye figyelembe a következő korlátozásokat:

   - Dimenziónként csak egy érték választható ki minden feltételben
   - A(z) „\*” nem használható dimenzióértékként
   - Ha a különböző feltételekben konfigurált metrikák ugyanazt a dimenziót támogatják, akkor a konfigurált dimenzióértéket explicit módon kell beállítani az összes metrika számára (lásd az Resource Manager-példasablont [itt)](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)


## <a name="no-permissions-to-create-metric-alert-rules"></a>Nincs engedélye metrikariasztás-szabályok létrehozásához

Metrikákra vonatkozó riasztási szabály létrehozásához a következő engedélyekkel kell rendelkeznie:

- Olvasási engedély a riasztási szabály célerőforrásán
- Írási engedély arra az erőforráscsoportra, amelyben a riasztási szabály létrejön (ha a riasztási szabályt a Azure Portal-ból hozta létre, a riasztási szabály alapértelmezés szerint ugyanabban az erőforráscsoportban jön létre, amelyben a célerőforrás található)
- Olvasási engedély a riasztási szabályhoz társított bármely műveletcsoporthoz (ha van)


## <a name="naming-restrictions-for-metric-alert-rules"></a>A metrikák riasztási szabályainak elnevezési korlátozásai

Vegye figyelembe a metrikariasztás szabálynevének alábbi korlátozásait:

- A metrikák riasztási szabályának nevei létrehozás után nem módosíthatók (átnevezhetők)
- A metrikariasztás szabálynevének egyedinek kell lennie egy erőforráscsoporton belül
- A metrikák riasztási szabályának neve nem tartalmazhatja a következő karaktereket: * # & + : < > ? @ % { } \ / 
- A metrikák riasztási szabályának neve nem végződhet szóközre vagy pontra

> [!NOTE] 
> Ha a riasztási szabály neve nem alfabetikus vagy numerikus karaktereket (például szóközöket, írásjeleket vagy szimbólumokat) tartalmaz, előfordulhat, hogy ezek a karakterek URL-kódolással vannak kódolva, amikor bizonyos ügyfelek lekérik őket.

## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>A dimenziók több feltételt használó metrikák riasztási szabályában való használatának korlátozásai

A metrikák riasztásai támogatják a többdimenziós metrikákra vonatkozó riasztásokat, valamint támogatják több feltétel meghatározását (riasztási szabályonként legfeljebb 5 feltétel).

Ha több feltételt tartalmazó riasztási szabályban dimenziókat használ, vegye figyelembe a következő korlátozásokat:
- Dimenziónként csak egy értéket választhat ki az egyes feltételeken belül.
- A "Select all current and future values" (Az összes aktuális és jövőbeli érték kiválasztása) lehetőséget nem használhatja (Select \* ).
- Ha a különböző feltételekben konfigurált metrikák ugyanazt a dimenziót támogatják, akkor explicit módon kell beállítani egy konfigurált dimenzióértéket az összes metrika számára (a megfelelő feltételek mellett).
Például:
    - Vezesszünk egy metrikákra vonatkozó riasztási szabályt, amely egy tárfiókban van meghatározva, és két feltételt figyel:
        * Összes **tranzakció >** 5
        * Átlagos **successE2ELatency** > 250 ms
    - Frissíteni szeretném az első feltételt, és csak olyan tranzakciókat monitor szeretnék figyelni, **amelyeknél** az ApiName dimenzió *értéke "GetBlob"*
    - Mivel a **Transactions** és **a SuccessE2ELatency** metrikák is támogatnak egy **ApiName** dimenziót, mindkét feltételt frissíteni kell, és mindkettőnek meg kell adnia az **ApiName** dimenziót egy *"GetBlob" értékkel.*

## <a name="setting-the-alert-rules-period-and-frequency"></a>A riasztási szabály időtartamának és gyakoriságának beállítása

Javasoljuk, hogy olyan összesítési *részletességet (Period)* válasszon, amely nagyobb a kiértékelési *gyakoriságnál,* hogy a következő esetekben csökkentse annak valószínűségét, hogy hiányzik a hozzáadott idősorok első kiértékelése:
-   Metrikákra vonatkozó riasztási szabály, amely több dimenziót figyel – Új dimenzióérték-kombináció hozzáadásakor
-   Metrikákra vonatkozó riasztási szabály, amely több erőforrást figyel – Új erőforrás hatókörbe való hozzáadásakor
-   Metrikariasztás szabálya, amely monitorozza a nem folyamatosan kibocsátott metrikát (ritka metrika) – Ha a metrika 24 óránál hosszabb idő után van kiadva, amikor nem lett kiadva

## <a name="the-dynamic-thresholds-borders-dont-seem-to-fit-the-data"></a>Úgy tűnik, hogy a dinamikus küszöbértékek szegélyei nem illeszkednek az adatokhoz

Ha egy metrika viselkedése a közelmúltban változott, a módosítások nem feltétlenül jelennek meg azonnal a dinamikus küszöbértékek határaiban (a felső és az alsó határértéken), mivel ezek számítása az elmúlt 10 nap metrikaadatai alapján történik. Egy adott metrika dinamikus küszöbérték-határainak megtekintésekor ügyeljen arra, hogy az elmúlt héten ne csak az elmúlt órákra vagy napokra vonatkozó metrikák trendjét ellenőrizze.

## <a name="why-is-weekly-seasonality-not-detected-by-dynamic-thresholds"></a>Miért nem észleli a dinamikus küszöbértékek a heti szezonalitást?

A heti szezonalitás azonosításához a Dinamikus küszöbértékek modell legalább három hét előzményadatot igényel. Ha elegendő előzményadat áll rendelkezésre, a rendszer azonosítja a metrikaadatokban megtalálható heti szezonalitásokat, és ennek megfelelően módosítja a modellt. 

## <a name="dynamic-thresholds-shows-a-negative-lower-bound-for-a-metric-even-though-the-metric-always-has-positive-values"></a>A dinamikus küszöbértékek negatív alsó határértéket mutatnak a metrikákhoz, annak ellenére, hogy a metrika mindig pozitív értékekkel rendelkezik

Ha egy metrika nagy ingadozást mutat, a dinamikus küszöbértékek szélesebb modellt építenek a metrikaértékek köré, ami azt eredményezheti, hogy az alsó szegély nullánál kevesebb lesz. Ez a következő esetekben fordulhat elő:
1. A bizalmasság alacsonyra van állítva 
2. A mediánértékek nullához közeliek
3. A metrika szokatlan viselkedést mutat nagy varianciával (kiugró vagy mélyedés van az adatokban)

Ha az alsó határ negatív értékkel rendelkezik, az azt jelenti, hogy a metrika valószínű, hogy nulla értéket ér el a metrika szabálytalan viselkedése miatt. Érdemes lehet nagyobb bizalmasságot vagy nagyobb összesítési részletességet *(Period)* választani a modell  kevésbé bizalmassága érdekében, vagy az Adatok mellőzése az előtt beállítás használatával kizárni a legutóbbi integrációs adatokat a modell felépítéséhez használt előzményadatokból.

## <a name="next-steps"></a>Következő lépések

- A riasztásokkal és értesítésekkel kapcsolatos általános hibaelhárítási információkért lásd: Hibaelhárítási problémák a Azure Monitor [riasztások esetén.](alerts-troubleshoot.md)
