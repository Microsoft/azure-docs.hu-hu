---
title: Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.
description: Tekintse át, hogy mit tehet a metrikus riasztásokkal, és hogyan működnek Azure Monitorban.
ms.date: 01/19/2021
ms.topic: conceptual
ms.openlocfilehash: 03968b3aa3eaf8f32019cfc5e9e2430c71419f62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038135"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>A metrikariasztások működése az Azure Monitorban

A Azure Monitor metrikus riasztások a többdimenziós mérőszámok felett működnek. Ezek a metrikák lehetnek [platform-metrikák](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [Egyéni metrikák](../essentials/metrics-custom-overview.md), [népszerű naplók Azure monitor konvertálva mérőszámokra](./alerts-metric-logs.md) és Application Insights mérőszámokra. A metrikai riasztások rendszeres időközönként értékelik ki, hogy egy vagy több metrikai idősorozatra vonatkozó feltételek igazak-e, és értesítjük, ha teljesülnek az értékelések. A metrikai riasztások állapota, azaz csak akkor küldenek értesítéseket, ha az állapot megváltozik.

## <a name="how-do-metric-alerts-work"></a>Hogyan működnek a metrikus riasztások?

Meghatározhatja a metrika riasztási szabályát úgy, hogy megadhatja a figyelni kívánt cél erőforrást, a metrika nevét, a feltétel típusát (statikus vagy dinamikus), valamint a feltételt (egy operátort és egy küszöbértéket/érzékenységet), valamint egy, a riasztási szabály által kiváltott műveleti csoportot. A feltételtípusok befolyásolják a küszöbértékek meghatározási módját. [További információ a dinamikus küszöbértékek feltételének típusáról és az érzékenységi lehetőségekről](../alerts/alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Riasztási szabály statikus feltétel típussal

Tegyük fel, hogy létrehozott egy egyszerű statikus küszöbérték-metrika riasztási szabályt a következőképpen:

- Célként megadott erőforrás (a figyelni kívánt Azure-erőforrás): myVM
- Metrika: százalékos CPU
- Feltétel típusa: statikus
- Összesítési típus (olyan statisztika, amely nyers metrikai értékeken fut. A [támogatott összesítési típusok](../essentials/metrics-aggregation-explained.md#aggregation-types) a következők: minimum, maximum, átlag, összeg, darabszám): átlag
- Időtartam (a visszatekintő ablak, amelyen a metrika értékei be vannak jelölve): az elmúlt 5 percben
- Gyakoriság (az a gyakoriság, amellyel a metrika riasztása ellenőrzi, hogy teljesülnek-e a feltételek): 1 perc
- Operátor: nagyobb, mint
- Küszöbérték: 70

A riasztási szabály létrehozásakor a figyelő 1 percenként fut, és az elmúlt 5 perc metrikai értékeit vizsgálja, és ellenőrzi, hogy az értékek átlaga meghaladja-e a 70 értéket. Ha a feltétel teljesül, az utolsó 5 perc átlagos CPU-értéke meghaladja az 70-as értéket, a riasztási szabály aktivált értesítést indít el. Ha e-mailt vagy webhook-műveletet konfigurált a riasztási szabályhoz társított műveleti csoportban, akkor mindkettőn aktiválva lesz egy értesítés.

Ha egy szabályban több feltételt használ, a "and" szabály együttesen szerepel a feltételekben. Ez azt eredményezi, hogy a riasztási szabály minden feltétele igaz értékre van kiértékelve, és ha az egyik feltétel már nem igaz, akkor a riasztás megoldódik. Ilyen típusú riasztási szabály például egy Azure-beli virtuális gép figyelése és riasztás, ha a "százalék CPU értéke nagyobb, mint 90%", a "várólista hossza több mint 300 elem".

### <a name="alert-rule-with-dynamic-condition-type"></a>Riasztási szabály dinamikus feltétel típussal

Tegyük fel, hogy létrehozott egy egyszerű dinamikus küszöbértékek mérőszámának riasztási szabályát az alábbiak szerint:

- Célként megadott erőforrás (a figyelni kívánt Azure-erőforrás): myVM
- Metrika: százalékos CPU
- Feltétel típusa: dinamikus
- Összesítési típus (olyan statisztika, amely nyers metrikai értékeken fut. A [támogatott összesítési típusok](../essentials/metrics-aggregation-explained.md#aggregation-types) a következők: minimum, maximum, átlag, összeg, darabszám): átlag
- Időtartam (a visszatekintő ablak, amelyen a metrika értékei be vannak jelölve): az elmúlt 5 percben
- Gyakoriság (az a gyakoriság, amellyel a metrika riasztása ellenőrzi, hogy teljesülnek-e a feltételek): 1 perc
- Operátor: nagyobb, mint
- Érzékenység: közepes
- Visszatérési időszakok: 4
- Szabálysértések száma: 4

A riasztási szabály létrehozása után a dinamikus küszöbértékek gépi tanulási algoritmusa a rendelkezésre álló korábbi adatokat fogja beszerezni, kiszámítja a metrikus adatsorozat viselkedési mintájának legjobban megfelelő küszöbértéket, és az új adatok alapján folyamatosan tanul, hogy a küszöbérték pontosabb legyen.

A riasztási szabály létrehozásának időpontjában a figyelő 1 percenként fut, és az elmúlt 20 percben 5 perces időszakra csoportosított metrikus értékeket keres, és ellenőrzi, hogy az egyes 4 időszakokban mért időszakok átlaga meghaladja-e a várt küszöbértéket. Ha a feltétel teljesült, az elmúlt 20 percben (4 5 perc) az átlagos CPU-érték négyszer eltér a várt viselkedéstől, a riasztási szabály aktivált értesítést indít el. Ha e-mailt vagy webhook-műveletet konfigurált a riasztási szabályhoz társított műveleti csoportban, akkor mindkettőn aktiválva lesz egy értesítés.

### <a name="view-and-resolution-of-fired-alerts"></a>Kilőtt riasztások megtekintése és megoldása

A riasztási szabályok elégetésének fenti példái a **minden riasztás** panel Azure Portal is megtekinthetők.

Tegyük fel, hogy a "myVM" használata a későbbi ellenőrzések során továbbra is a küszöbérték felett marad, a riasztási szabály a feltételek feloldása előtt nem indul újra.

Egy kis idő elteltével a "myVM" használata visszakerül a normál értékre (a küszöbérték alá esik). A riasztási szabály két alkalommal figyeli a feltételt, hogy elküldjék a megoldott értesítéseket. A riasztási szabály elküld egy megoldott/inaktivált üzenetet, ha a riasztási feltétel nem teljesül három egymást követő időszakon belül a zaj csökkentése érdekében.

Mivel a megoldott értesítést webhookok vagy e-mailek útján küldi el a rendszer, a riasztási példány állapota (figyelő állapota) a Azure Portalban is megoldottra van állítva.

> [!NOTE]
>
> Ha egy riasztási szabály több feltételt figyel, akkor a rendszer egy kilőtt riasztást fog feloldani, ha a feltételek legalább egyike már nem teljesül három egymást követő időszakban.

### <a name="using-dimensions"></a>Méretek használata

A Azure Monitor metrikai riasztások is támogatják a több dimenziós érték kombinációjának figyelését egyetlen szabállyal. Lássuk, miért érdemes több dimenzió kombinációt használni egy példa segítségével.

Tegyük fel, hogy van egy App Service terve a webhelyhez. Szeretné figyelni a CPU-használatot a webhelyet vagy alkalmazást futtató több példányon. Ezt egy metrikus riasztási szabály használatával teheti meg a következőképpen:

- Cél erőforrás: myAppServicePlan
- Metrika: százalékos CPU
- Feltétel típusa: statikus
- Dimenziók
  - Példány = InstanceName1, InstanceName2
- Összesítés típusa: átlag
- Időszak: az elmúlt 5 percben
- Gyakoriság: 1 perc
- Operátor: GreaterThan
- Küszöbérték: 70

Mint korábban, ez a szabály figyeli, hogy az elmúlt 5 percben az átlagos CPU-használat meghaladja-e a 70%-ot. Ugyanakkor ugyanazzal a szabállyal két, a webhelyét futtató példányt is figyelheti. Minden példány külön lesz megfigyelve, és a rendszer külön értesítést fog kapni.

Tegyük fel, hogy van egy webalkalmazása, amely nagy mennyiségű igényt lát, és további példányokat kell hozzáadnia. A fenti szabály továbbra is csak két példányt figyeli. Azonban a következőképpen hozhat létre szabályt:

- Cél erőforrás: myAppServicePlan
- Metrika: százalékos CPU
- Feltétel típusa: statikus
- Dimenziók
  - Példány = *
- Összesítés típusa: átlag
- Időszak: az elmúlt 5 percben
- Gyakoriság: 1 perc
- Operátor: GreaterThan
- Küszöbérték: 70

Ez a szabály automatikusan figyeli a példány összes értékét, azaz a példányokat úgy figyelheti, hogy a rendszer a metrikus riasztási szabály újbóli módosítása nélkül is feldolgozza őket.

Több dimenzió figyelése esetén a dinamikus küszöbértékek riasztási szabálya egyszerre több száz metrikus adatsorozatra szabott küszöbértékeket hozhat létre. A dinamikus küszöbértékek kevesebb riasztási szabályt eredményeznek a felügyelethez és a riasztási szabályok létrehozásához szükséges jelentős idő megtakarításához.

Tegyük fel, hogy van egy webalkalmazása, amely sok példánnyal rendelkezik, és nem tudja, mi a legmegfelelőbb küszöbérték. A fenti szabályok mindig a 70%-os küszöbértéket fogják használni. Azonban a következőképpen hozhat létre szabályt:

- Cél erőforrás: myAppServicePlan
- Metrika: százalékos CPU
- Feltétel típusa: dinamikus
- Dimenziók
  - Példány = *
- Összesítés típusa: átlag
- Időszak: az elmúlt 5 percben
- Gyakoriság: 1 perc
- Operátor: GreaterThan
- Érzékenység: közepes
- Visszatérési időszakok: 1
- Szabálysértések száma: 1

Ez a szabály figyeli, hogy az elmúlt 5 percben az átlagos CPU-használat meghaladja-e az egyes példányok várt viselkedését. Ugyanezt a szabályt követheti a példányok figyelése, anélkül, hogy újra kellene módosítania a metrikus riasztási szabályt. Az egyes példányok egy küszöbértéket kapnak, amely megfelel a metrikus adatsorozat viselkedési mintájának, és folyamatosan változik az új adatok alapján, hogy a küszöbérték pontosabb legyen. A fentiekhez hasonlóan az egyes példányok külön lesznek figyelve, és a rendszer külön értesítést fog kapni.

A visszapillantási időszakok és a szabálysértések számának növelése is lehetővé teszi a riasztások szűrését, hogy csak a jelentős eltérés definíciójában figyelmeztessen. [További információ a dinamikus küszöbértékekkel kapcsolatos speciális beállításokról](../alerts/alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

> [!NOTE]
>
> Javasoljuk, hogy a *kiértékelés gyakorisága* nagyobb mértékű *összesítési részletességet (időszakot)* válasszon, hogy csökkentse a hozzáadott idősorozat első kiértékelésének valószínűségét a következő esetekben:
> - Metrikus riasztási szabály, amely több dimenziót figyel – új dimenzió érték-kombináció hozzáadásakor
> - Metrikus riasztási szabály, amely több erőforrást figyel – új erőforrás a hatókörhöz való hozzáadásakor
> - Metrikus riasztási szabály, amely nem folyamatosan kibocsátott mérőszámot figyel (ritka metrika) – Ha a mérőszámot 24 óránál hosszabb időt követően bocsátják ki a rendszer, amelyben nem lett kibocsátva



## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Méretezés a metrikus riasztások használatával Azure Monitor

Eddig azt tapasztalta, hogy egyetlen metrikai riasztást is használhat egy vagy több, egyetlen Azure-erőforráshoz kapcsolódó metrikai idősorozat figyelésére. Sokszor előfordulhat, hogy ugyanaz a riasztási szabály sok erőforrásra vonatkozik. A Azure Monitor az ugyanazon az Azure-régióban található erőforrások esetében is támogatja több erőforrás (azonos típusú) figyelését egy metrikai riasztási szabállyal. 

Ez a funkció jelenleg a platform metrikái (nem egyéni metrikák) esetén támogatott a következő szolgáltatásokhoz a következő Azure-felhőkben:

| Szolgáltatás | Nyilvános Azure | Államigazgatás | Kína |
|:--------|:--------|:--------|:--------|
| Virtuális gépek<sup>1</sup>  | **Igen** | **Igen** | Nem |
| SQL Server-adatbázisok | **Igen** | **Igen** | **Igen** |
| SQL Server rugalmas készletek | **Igen** | **Igen** | **Igen** |
| NetApp-fájlok kapacitásának készletei | **Igen** | **Igen** | **Igen** |
| NetApp-fájlok kötetei | **Igen** | **Igen** | **Igen** |
| Kulcstartók | **Igen** | **Igen** | **Igen** |
| Azure Cache for Redis | **Igen** | **Igen** | **Igen** |
| Adatmező peremhálózati eszközei | **Igen** | **Igen** | **Igen** |

<sup>1</sup> nem támogatott a virtuálisgép-hálózati metrikák esetében (összesen a hálózat, a hálózat teljes száma, a bejövő folyamatok, a kimenő folyamatok, a bejövő folyamatok maximális létrehozási sebessége, a kimenő forgalom maximális száma).

A figyelés hatókörét a három módszer egyikével adhatja meg egyetlen metrikai riasztási szabály segítségével. A Virtual Machines használatával például a következő módon adhatja meg a hatókört:  

- egy előfizetésen belüli virtuális gépek listája (egy Azure-régióban)
- az előfizetésben lévő egy vagy több erőforráscsoport összes virtuális gépe (egy Azure-régióban)
- az előfizetésben lévő összes virtuális gép (egy Azure-régióban)

> [!NOTE]
>
> A többerőforrásos metrika riasztási szabályának hatókörének tartalmaznia kell legalább egy erőforrást a kiválasztott erőforrástípus számára.

Több erőforrást figyelő metrikai riasztási szabályok létrehozása olyan, mint [bármely más metrikai riasztás](../alerts/alerts-metric.md) , amely egyetlen erőforrás figyelésére szolgál. Csak a különbség, hogy az összes figyelni kívánt erőforrást kijelöli. Ezeket a szabályokat [Azure Resource Manager sablonokon](./alerts-metric-create-templates.md#template-for-a-metric-alert-that-monitors-multiple-resources)keresztül is létrehozhatja. Minden egyes megfigyelt erőforráshoz külön értesítést fog kapni.

> [!NOTE]
>
> A több erőforrást figyelő metrikai riasztási szabályban csak egy feltétel engedélyezett.

## <a name="typical-latency"></a>Jellemző késés

A metrikákhoz kapcsolódó riasztások esetében általában 5 percen belül értesítést fog kapni, ha a riasztási szabály gyakoriságát 1 percre állítja be. Ha az értesítési rendszerek nagyon leterheltek, nagyobb késést tapasztalhat.

## <a name="supported-resource-types-for-metric-alerts"></a>A metrikus riasztások által támogatott erőforrástípusok

A támogatott erőforrástípusok teljes listáját ebben a [cikkben](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported)találja.


## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan hozhat létre, tekinthet meg és kezelhet metrikus riasztásokat az Azure-ban](../alerts/alerts-metric.md)
- [Ismerje meg, hogyan hozhat létre riasztásokat az Azure Monti Metrikaböngésző](../essentials/metrics-charts.md#alert-rules)
- [Megtudhatja, hogyan helyezhet üzembe metrikus riasztásokat Azure Resource Manager sablonok használatával](./alerts-metric-create-templates.md)
- [További információ a műveleti csoportokról](./action-groups.md)
- [További információ a dinamikus küszöbértékek feltételének típusáról](../alerts/alerts-dynamic-thresholds.md)
- [További információ a metrikus riasztásokkal kapcsolatos problémák elhárításáról](alerts-troubleshoot-metric.md)