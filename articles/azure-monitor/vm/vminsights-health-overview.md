---
title: A VM bepillantást a vendég állapotára (előzetes verzió)
description: A virtuálisgép-információk állapotának áttekintése, beleértve a virtuális gépek állapotának megtekintését és a riasztások fogadását, ha a virtuális gép nem megfelelő állapotba kerül.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/27/2020
ms.openlocfilehash: 2922ca4068531c45e6acad0ce54aa96624c6238e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052126"
---
# <a name="vm-insights-guest-health-preview"></a>A VM bepillantást a vendég állapotára (előzetes verzió)
A virtuális gépek bepillantást nyerhetnek, és a vendég operációs rendszertől rendszeres időközönként mintát vett teljesítmény-mérési készlet alapján megtekintheti a virtuális gépek állapotát. Gyorsan megtekintheti az előfizetésben vagy az erőforráscsoporthoz tartozó összes virtuális gép állapotát, részletezheti az adott virtuális gép részletes állapotát, vagy proaktívan értesítheti, ha a virtuális gép nem Kifogástalan állapotba kerül. 

## <a name="enable-virtual-machine-health"></a>Virtuális gép állapotának engedélyezése
A vendég állapot szolgáltatás és a bevezetési virtuális gépek engedélyezésével kapcsolatos részletekért lásd: a [VM bepillantást a vendég állapotának engedélyezése (előzetes verzió)](vminsights-health-enable.md) .

## <a name="pricing"></a>Díjszabás
A vendég állapot szolgáltatásnak nincs közvetlen díja, de az állapotadatok betöltésére és tárolására a Log Analytics munkaterületen van lehetőség. Az összes adattal a *HealthStateChangeEvent* tábla tárolja. A díjszabással és a költségekkel kapcsolatos részletekért tekintse meg a [használat és a költségek kezelése Azure monitor naplókkal](../logs/manage-cost-storage.md) című témakört.

## <a name="view-virtual-machine-health"></a>Virtuális gép állapotának megtekintése
Az **első lépések** oldalon található **vendég virtuális gép állapota** oszlop gyors áttekintést nyújt egy adott előfizetéshez vagy erőforráscsoporthoz tartozó virtuális gépek állapotáról. Az egyes virtuális gépek aktuális állapota megjelenik, miközben az egyes csoportok ikonjai megjelenítik az adott csoportba tartozó virtuális gépek számát.

[![Első lépések oldal a vendég virtuális gép állapotával](media/vminsights-health-overview/get-started-page.png)](media/vminsights-health-overview/get-started-page.png#lightbox)


## <a name="monitors"></a>Monitorozások
Kattintson a virtuális gép állapotára az egyes figyelők részletes állapotának megtekintéséhez. Mindegyik figyelő egy adott összetevő állapotát méri. A virtuális gép általános állapotát az egyes figyelők állapota határozza meg. 

![Figyelők – példa](media/vminsights-health-overview/monitors.png)

A következő táblázat felsorolja az egyes virtuális gépekhez jelenleg elérhető aggregált és egység figyelőket. 

| Monitor | Típus | Leírás |
|:---|:---|:---|
| Processzorhasználat | Unit (Egység) | A processzor kihasználtsága százalékban kifejezve. |
| Fájlrendszerek | Összesítés | A Linux rendszerű virtuális gépen lévő összes fájlrendszer állapotának összesítése. |
| Fájlrendszer  | Összesítés | A Linux rendszerű virtuális gépen található egyes fájlrendszerek állapota. A figyelő neve a fájlrendszer neve. |
| Szabad terület | Unit (Egység) | A fájlrendszerben lévő szabad terület százalékos aránya. |
| Logikai lemezek | Összesítés | A Windows rendszerű virtuális gépeken található összes logikai lemez állapotának összesítése. |
| Logikai lemez  | Összesítés | A Windows rendszerű virtuális gépeken található egyes logikai lemezek állapota. A figyelő neve a lemez neve. |
| Memória | Összesítés | A virtuális gép memóriájának összesített állapota. |
| Igénybe vehető memória | Unit (Egység) | A virtuális gépen rendelkezésre álló megabájtok. |

## <a name="health-states"></a>Állapotok
Minden egyes figyelő minden percben megkeresi az ügynök értékeit, és összehasonlítja a mintavételi értékeket az egyes állapotokhoz tartozó feltételekkel. Ha az adott állapotra vonatkozó feltételek igazak, akkor a figyelő erre az állapotra van beállítva. Ha egyik feltétel sem teljesül, akkor a figyelő Kifogástalan állapotra van állítva. Az ügynöktől az adatok elküldése három percenként Azure Monitor, vagy azonnal, ha van ilyen állapotú változás.

Minden figyelő rendelkezik egy lookback-ablaktal, és elemzi az adott időn belül összegyűjtött mintákat. Előfordulhat például, hogy egy figyelő 240 másodperces lookback-ablakban keresi a maximális értéket legalább 2 mintavételi érték között, de nem több, mint az utolsó 3. Míg az értékek normál arányban szerepelnek, az adott ablakban megjelenő szám az ügynök működésének megszakadásakor némileg eltérő lehet.

A figyelők mindegyike rendelkezik a lehetséges állapottal az alábbi táblázatban, és egy adott időpontban csak egy és csak egy lesz. Egy figyelő inicializálásakor a rendszer Kifogástalan állapotba kezd.

| Állapot | Leírás |
|:---|:---|
| Kifogástalan  | A figyelő jelenleg nem lépi túl a figyelmeztetési vagy a kritikus küszöbértéket. |
| Figyelmeztetés  | A figyelő túllépte a figyelmeztetési küszöbértéket (ha meg van adva). |
| Kritikus | A figyelő túllépte a kritikus küszöbértéket (ha meg van adva). |
| Ismeretlen  | Nincs elegendő adatgyűjtési állapot az állapot megállapításához. |
| Disabled (Letiltva) | A figyelő jelenleg le van tiltva. |
| Nincs     | A figyelő éppen elindult, és még nincs kiértékelve, vagy a figyelt objektum már nem létezik. |



Az alábbi táblázatban két típusú figyelő látható:

| Monitor | Leírás |
|:---|:---|
| Egységmonitor | Egy erőforrás vagy alkalmazás valamilyen jellemzőjét méri. Ez lehet egy teljesítményszámláló ellenőrzése az erőforrás teljesítményének vagy rendelkezésre állásának meghatározása céljából. |
| Összesített monitor | Több monitort csoportosít egyetlen összesített állapot biztosításához. Egy összesített monitor egy vagy több egységmonitort és más összesített monitorokat tartalmazhat. |


  
### <a name="health-rollup-policy"></a>Állapot-összesítési szabályzat
A virtuális gép állapotát az egység és az összesítő figyelők állapotának összesítése határozza meg. Mindegyik összesített figyelő egy legrosszabb állapot-összesítési házirendet használ, ahol az összesített figyelő állapota megegyezik a gyermek figyelő állapotával a legrosszabb állapottal.  

A következő példában a **szabad terület** figyelője olyan kritikus állapotban van, amely a példánya, majd a **fájlrendszerek** összesítéseit is felveszi. Bár a **CPU-kihasználtság** figyelmeztetési állapotban van, a virtuális gép a kritikus értékre van beállítva, mivel ez a legrosszabb állapot alá esik. Ha a szabad terület Kifogástalan állapotba tért vissza, akkor a virtuális gép figyelmeztetési állapotba vált, mivel a CPU-kihasználtság ezután a legrosszabb állapotú figyelő lesz.

![Állapot kumulatív példája](media/vminsights-health-overview/health-rollup-example.png)


## <a name="monitor-details"></a>A figyelő részletei
Válasszon ki egy figyelőt a részletek megtekintéséhez, amely a következő lapokat tartalmazza.

Az **Áttekintés** a figyelő leírását, a legutóbbi kiértékelés időpontját, valamint az aktuális állapot meghatározására szolgáló értékeket tartalmazza. A minták száma a figyelő definíciója és az értékek volatilitása alapján változhat.

[![Figyelő részletei – áttekintés](media/vminsights-health-overview/monitor-details-overview.png)](media/vminsights-health-overview/monitor-details-overview.png#lightbox)

Az **Előzmények** a figyelő állapot változásainak előzményeit listázza. Az állapot változásainak kibontásával megtekintheti a kiértékelt értékeket az állapot meghatározásához. Kattintson a **konfiguráció megtekintése** elemre, hogy megtekinthesse a figyelő konfigurációját az állapot módosításának időpontjában.



[![Figyelő részleteinek előzményei](media/vminsights-health-overview/monitor-details-history.png)](media/vminsights-health-overview/monitor-details-history.png#lightbox)

### <a name="configuration"></a>Konfiguráció
A kiválasztott virtuális géphez tartozó figyelő konfigurációjának megtekintése és módosítása. A részletekért lásd: [a virtuális gépek betekintő szolgáltatásában a figyelés konfigurálása (előzetes verzió)](vminsights-health-enable.md) .

[![A részleteket figyelő konfiguráció figyelése](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)




## <a name="next-steps"></a>Következő lépések

- [A vendég állapotának engedélyezése a virtuális gépek és a bevezetési ügynökök számára.](vminsights-health-enable.md)
- [A figyelők konfigurálása a Azure Portal használatával.](vminsights-health-configure.md)
- [Figyelőket konfigurálhat az adatgyűjtési szabályokkal.](vminsights-health-configure-dcr.md)