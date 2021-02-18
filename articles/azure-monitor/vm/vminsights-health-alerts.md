---
title: Vendég állapottal kapcsolatos riasztások Azure Monitor for VMs (előzetes verzió)
description: A Azure Monitor for VMs Guest Health által létrehozott riasztásokat ismerteti, beleértve a hogyan engedélyezheti és konfigurálhatja az értesítéseket.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 30025f387768aaf1e4d642292c21d5b15ccc7451
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620628"
---
# <a name="azure-monitor-for-vms-guest-health-alerts-preview"></a>Vendég állapottal kapcsolatos riasztások Azure Monitor for VMs (előzetes verzió)
Azure Monitor for VMs vendég állapota lehetővé teszi egy virtuális gép állapotának megtekintését, amelyet a rendszeres időközönként mintavételnek alávetett teljesítmény-mérések határoznak meg. Egy riasztás akkor hozható létre, ha egy virtuális gép vagy a figyelő nem Kifogástalan állapotra vált. Ezeket a riasztásokat megtekintheti és kezelheti a [riasztási szabályok által létrehozott Azure monitorban](../platform/alerts-overview.md) , és beállíthatja, hogy proaktívan értesítse az új riasztások létrehozásakor.

## <a name="configure-alerts"></a>Riasztások konfigurálása
Nem hozhat létre explicit riasztási szabályt Azure Monitor for VMs vendég állapotához, miközben ez a funkció előzetes verzióban érhető el. Alapértelmezés szerint minden egyes virtuális géphez létrejönnek riasztások, de nem minden egyes figyelő esetében.  Ez azt jelenti, hogy ha egy figyelő olyan állapotra vált, amely nem befolyásolja a virtuális gép aktuális állapotát, akkor nem jön létre riasztás, mert a virtuális gép állapota nem változott. 

A riasztásokat letilthatja egy adott virtuális gép vagy egy virtuális gép egy adott figyelője számára a Azure Portalban található virtuális gép konfigurációjának **riasztás állapota** beállításával. A Azure Portal figyelők konfigurálásával kapcsolatos részletekért tekintse meg a [figyelés konfigurálása Azure monitor for VMS vendég állapota (előzetes verzió)](vminsights-health-configure.md) című témakört. A figyelők virtuális gépeken történő konfigurálásával kapcsolatos részletekért tekintse meg a [figyelés konfigurálása a Azure monitor for VMS vendég állapotához az adatgyűjtési szabályok (előzetes verzió) használatával](vminsights-health-configure-dcr.md) című témakört.

## <a name="alert-severity"></a>A riasztás súlyossága
A vendég állapot által létrehozott riasztás súlyossága közvetlenül a virtuális gép súlyosságára vagy a riasztást kiváltó figyelőre mutat.

| Figyelő állapota | A riasztás súlyossága |
|:---|:---|
| Kritikus | Sev1 |
| Figyelmeztetés  | Sev2 |
| Kifogástalan  | Sev4 |

## <a name="alert-lifecycle"></a>Riasztási életciklus
Minden egyes virtuális géphez létrejön egy [Azure-riasztás](../platform/alerts-overview.md) , bármikor **Figyelmeztetés** vagy **kritikus** állapotba kerül. Megtekintheti a **riasztásokat** a **Azure monitor** menüben vagy a Azure Portal virtuális gép menüjében.

Ha egy riasztás már folyamatban van **, a** virtuális gép állapotának megváltozásakor a rendszer nem hoz létre második riasztást, de az azonos riasztás súlyossága a virtuális gép állapotának megfelelően módosul. Ha például a virtuális gép **kritikus** állapotra vált, ha egy **figyelmeztető** riasztás már **kilőtt** állapotban van, a riasztás súlyossága a **Sev1** értékre változik. Ha a virtuális gép **Figyelmeztetési** állapotba vált, ha egy **Sev1** -riasztás már **tüzelt** állapotban van, akkor a riasztás súlyossága a **Sev2** értékre változik. Ha a virtuális gép **kifogástalan** állapotba kerül, a riasztás súlyossága a **Sev4** értékre módosul.

## <a name="viewing-alerts"></a>Riasztások megtekintése
A Azure Monitor for VMs Guest Health által létrehozott riasztások megtekintése [a Azure Portal többi riasztásával](../platform/alerts-overview.md#alerts-experience). A **Azure monitor** menüben a **riasztások** elemre kattintva megtekintheti az összes figyelt erőforráshoz tartozó riasztásokat, vagy a virtuális gép menüjében a **riasztások** lehetőségre kattintva megtekintheti a virtuális gép riasztásait.

## <a name="alert-properties"></a>Riasztás tulajdonságai

### <a name="properties-in-the-azure-portal"></a>Tulajdonságok a Azure Portal
Az alábbi táblázat ismerteti a riasztás tulajdonságait, amikor megtekinti a Azure Portal.

| Tulajdonság | Leírás |
|:---|:---|
| Állapot figyelése a riasztás létrehozása előtt | A figyelő vagy a virtuális gép állapota, mielőtt a riasztás első alkalommal fordult elő. |
| Állapot figyelése riasztás létrehozásakor | A figyelő vagy a virtuális gép állapota a riasztás első indításakor. Ez az az állapot, amely miatt a riasztás tüzet okozott. |
| Ha többet szeretne megtudni a riasztás létrejöttének állapotáról | Hivatkozás a VM Health (virtuális gép állapota) oldalra, ahol megtekintheti a pontos állapot-átállást. Ez az állapot-változás azt a példányt jelenti, amikor a figyelő először **kifogástalan** állapotról nem Kifogástalan állapotba került. |

Egy figyelő például **kifogástalan** állapotról **kritikusra** vált a t0 időpontban, és egy új riasztást **Sev1**. Ezután **kritikusról** **figyelmeztetésre** kerül a T1 időpontban, a riasztás súlyossága pedig a **Sev2**-re frissül. A rendszer a T2 időpontban **kifogástalan** állapotba kerül, és a riasztás megoldódik.

A riasztás tulajdonságai ezekkel az értékekkel fognak rendelkezni a teljes sorozatban.

- Figyelő állapota a riasztás létrehozása előtt: kifogástalan
- Állapot figyelése riasztás létrehozásakor: kritikus
- Ha többet szeretne megtudni arról, hogy mikor jött létre a riasztás létrehozásakor, az állapot átváltására vonatkozó navigációs hivatkozás a t0 időpontban történt.


### <a name="properties-in-notifications"></a>Tulajdonságok az értesítésekben
Az értesítésekben szereplő riasztások tulajdonságait az alábbi táblázat ismerteti.

| Tulajdonság | Leírás |
|:---|:---|
| Előző figyelő állapota | A figyelő vagy a virtuális gép állapota, mielőtt megváltoztatta az állapotot. Ha a riasztás súlyossági frissítése elindítja ezt az értesítést, ez a tulajdonság azt a állapotot jelöli, amely közvetlenül a súlyossági frissítés előtt volt. |
| Aktuális figyelő állapota | A figyelő vagy a virtuális gép állapota, amikor megváltoztatta az állapotot. Ha a riasztás súlyossági frissítése elindítja ezt az értesítést, ez a tulajdonság az új állapotot jelöli. |
| További információ erről az állapotról való áttérésről | Hivatkozás a VM Health (virtuális gép állapota) oldalra, ahol megtekintheti a pontos állapot-átállást. Ez az állapot-változás azt a példányt jelöli, amikor a figyelő módosult, és ez az értesítés aktiválva lett. |

A fenti példa használatával az értesítések minden alkalommal a következő tulajdonságokkal rendelkezhetnek.

A t0 időpontjában kapott értesítés
- Előző figyelő állapota: kifogástalan
- Aktuális figyelő állapota: kritikus
- Ha többet szeretne megtudni erről az állapot-áttérésről: a t0 időpontnál megjelenő, az állapotra váltásra mutató navigációs hivatkozás.

Értesítés érkezett a T1 időpontban
- Előző figyelő állapota: kritikus
- Aktuális figyelő állapota: figyelmeztetés
- Ha többet szeretne megtudni erről az állapot-átállásról: az állapot átváltására irányuló navigációs hivatkozás a T1 időpontban történt.

Értesítés érkezett a T2 időpontban
- Előző figyelő állapota: figyelmeztetés
- Aktuális figyelő állapota: kifogástalan
- Ha többet szeretne megtudni erről az állapot-átállásról: a T2 időpontban történt navigációs hivatkozás az állapot átváltására.

## <a name="configure-notifications"></a>Értesítések konfigurálása
Ahhoz, hogy proaktív értesítést kapjon a vendég állapota által aktivált riasztásokról, hozzon létre egy [műveleti csoportot](../alerts/action-groups.md) a különböző végrehajtandó műveletek meghatározásához, például SMS-üzenet küldéséhez vagy logikai alkalmazás indításához. Ezután hozzon létre egy [műveleti szabályt](../alerts/alerts-action-rules.md) , amely meghatározza a figyelők és a virtuális gépek hatókörét, és ezt a műveleti csoportot használja.

A Azure Portal **figyelés** menüjében válassza a **riasztások** elemet.  Válassza a **műveletek kezelése** , majd a **műveleti szabályok (előzetes verzió)** lehetőséget. 

![Új műveleti szabály](media/vminsights-health-alerts/action-rule-new.png)

Új szabály létrehozásához kattintson az **új műveleti szabály** elemre. Kattintson a kijelölés elemre a hatókör **területen** , és válassza ki az előfizetést, az erőforráscsoportot vagy egy vagy több megadott virtuális gépet. Az értesítés csak azokra a virtuális gépekre lesz kirúgva, amelyek a hatókörön belül esnek.

![Műveleti szabály hatóköre](media/vminsights-health-alerts/action-rule-scope.png)

A **szűréshez** kattintson a **Hozzáadás** elemre. Hozzon létre egy szűrőt, amelyben a **figyelő szolgáltatás egyenlő a virtuális gépekkel való bepillantások állapotával**. További szűrők hozzáadásával adja meg az értesítéseket kiváltó riasztásokat. Például a **súlyossággal** egyeztetheti az összes olyan figyelő riasztásait, amelyek megfelelnek egy adott súlyosságnak.

![Műveleti szabály szűrője](media/vminsights-health-alerts/action-rule-filter.png)

A **hatókör definiálása** területen válassza a **műveleti csoport** elemet, majd válassza ki a figyelőhöz társítandó műveleti csoportot. Adja meg a szabály nevét, és válassza ki azt az erőforráscsoportot, amelybe menteni kívánja a szabályt. A szabály létrehozásához kattintson a **Létrehozás** gombra.

![Műveleti szabály](media/vminsights-health-alerts/action-rule.png)


## <a name="next-steps"></a>Következő lépések

- [A vendég állapotának engedélyezése Azure Monitor for VMs és bevezetési ügynökökben.](vminsights-health-enable.md)
- [A figyelők konfigurálása a Azure Portal használatával.](vminsights-health-configure.md)
- [Figyelőket konfigurálhat az adatgyűjtési szabályokkal.](vminsights-health-configure-dcr.md)