---
title: A virtuális gépekkel kapcsolatos vendég állapottal kapcsolatos riasztások (előzetes verzió)
description: Ismerteti a virtuális gépek bepillantást a vendég állapotával létrehozott riasztásokat, beleértve az engedélyezésük és az értesítések konfigurálásának módját.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: a32ba9f1c4cf5d6bb9de69e1a6860c858e3ee2a6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101707506"
---
# <a name="vm-insights-guest-health-alerts-preview"></a>A virtuális gépekkel kapcsolatos vendég állapottal kapcsolatos riasztások (előzetes verzió)
A virtuális gép elemzése lehetővé teszi a virtuális gépek állapotának megtekintését olyan teljesítmény-mérési készlet alapján, amely rendszeres időközönként mintavételt végez. Egy riasztás akkor hozható létre, ha egy virtuális gép vagy a figyelő nem Kifogástalan állapotra vált. Ezeket a riasztásokat megtekintheti és kezelheti a [riasztási szabályok által létrehozott Azure monitorban](../alerts/alerts-overview.md) , és beállíthatja, hogy proaktívan értesítse az új riasztások létrehozásakor.

## <a name="configure-alerts"></a>Riasztások konfigurálása
Nem hozhat létre explicit riasztási szabályt a virtuálisgép-betekintő vendég állapotához, miközben ez a funkció előzetes verzióban érhető el. Alapértelmezés szerint minden egyes virtuális géphez létrejönnek riasztások, de nem minden egyes figyelő esetében.  Ez azt jelenti, hogy ha egy figyelő olyan állapotra vált, amely nem befolyásolja a virtuális gép aktuális állapotát, akkor nem jön létre riasztás, mert a virtuális gép állapota nem változott. 

A riasztásokat letilthatja egy adott virtuális gép vagy egy virtuális gép egy adott figyelője számára a Azure Portalban található virtuális gép konfigurációjának **riasztás állapota** beállításával. A Azure Portal figyelők konfigurálásával kapcsolatos részletekért tekintse meg a [figyelés konfigurálása a virtuális gépekhez (előzetes verzió)](vminsights-health-configure.md) című témakört. A figyelők virtuális gépeken történő konfigurálásával kapcsolatos részletekért tekintse meg a [figyelés konfigurálása a VM-elemzésekben a vendégek állapotát az adatgyűjtési szabályok (előzetes verzió) használatával](vminsights-health-configure-dcr.md) című témakört.

## <a name="alert-severity"></a>A riasztás súlyossága
A vendég állapot által létrehozott riasztás súlyossága közvetlenül a virtuális gép súlyosságára vagy a riasztást kiváltó figyelőre mutat.

| Figyelő állapota | A riasztás súlyossága |
|:---|:---|
| Kritikus | Sev1 |
| Figyelmeztetés  | Sev2 |
| Kifogástalan  | Sev4 |

## <a name="alert-lifecycle"></a>Riasztási életciklus
Minden egyes virtuális géphez létrejön egy [Azure-riasztás](../alerts/alerts-overview.md) , bármikor **Figyelmeztetés** vagy **kritikus** állapotba kerül. Megtekintheti a **riasztásokat** a **Azure monitor** menüben vagy a Azure Portal virtuális gép menüjében.

Ha egy riasztás már folyamatban van **, a** virtuális gép állapotának megváltozásakor a rendszer nem hoz létre második riasztást, de az azonos riasztás súlyossága a virtuális gép állapotának megfelelően módosul. Ha például a virtuális gép **kritikus** állapotra vált, ha egy **figyelmeztető** riasztás már **kilőtt** állapotban van, a riasztás súlyossága a **Sev1** értékre változik. Ha a virtuális gép **Figyelmeztetési** állapotba vált, ha egy **Sev1** -riasztás már **tüzelt** állapotban van, akkor a riasztás súlyossága a **Sev2** értékre változik. Ha a virtuális gép **kifogástalan** állapotba kerül, a riasztás súlyossága a **Sev4** értékre módosul.

## <a name="viewing-alerts"></a>Riasztások megtekintése
Megtekintheti a virtuális gépek által [a Azure Portalban található egyéb riasztásokkal](../platform/alerts-overview.md#alerts-experience)létrehozott riasztásokat. A **Azure monitor** menüben a **riasztások** elemre kattintva megtekintheti az összes figyelt erőforráshoz tartozó riasztásokat, vagy a virtuális gép menüjében a **riasztások** lehetőségre kattintva megtekintheti a virtuális gép riasztásait.

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

- [A vendég állapotának engedélyezése a virtuális gépek és a bevezetési ügynökök számára.](vminsights-health-enable.md)
- [A figyelők konfigurálása a Azure Portal használatával.](vminsights-health-configure.md)
- [Figyelőket konfigurálhat az adatgyűjtési szabályokkal.](vminsights-health-configure-dcr.md)