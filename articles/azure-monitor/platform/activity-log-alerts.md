---
title: Műveletnapló riasztásai Azure Monitor
description: Értesítés küldése SMS-ben, webhookon, SMS-ben, e-mailben és más üzenetekben, ha bizonyos események történnek a tevékenység naplójában.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 1a932aba55ec9bd5d92c60338a3c1fc4bb481c1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137705"
---
# <a name="alerts-on-activity-log"></a>Riasztások a tevékenység naplójában

## <a name="overview"></a>Áttekintés

A műveletnapló riasztásai olyan riasztások, amelyek akkor [jelentkeznek](activity-log-schema.md) be, amikor a riasztásban megadott feltételeknek megfelelő új műveletnapló-esemény következik be. Az Azure-beli [tevékenység naplójában](platform-logs-overview.md)rögzített események sorrendje és mennyisége alapján a riasztási szabály tüzet fog eredményezni. A műveletnapló riasztási szabályai Azure-erőforrások, így a Azure Resource Manager sablon segítségével hozhatók létre. Emellett a Azure Portal is létrehozhatók, frissíthetők és törölhetők. Ez a cikk a tevékenység naplójának riasztásai mögötti fogalmakat ismerteti. A műveletnapló riasztási szabályainak létrehozásával és használatával kapcsolatos további információkért lásd: [műveletnapló riasztások létrehozása és kezelése](alerts-activity-log.md).

> [!NOTE]
> **Nem** hozhatók létre riasztások a tevékenység naplójának riasztási kategóriájában lévő eseményekhez.

Az értesítések fogadásához általában a következő esetekben kell létrehoznia a tevékenység naplójának riasztásait:

* Az Azure-előfizetésében lévő erőforrásokra adott műveletek vonatkoznak, gyakran adott erőforrás-csoportokra vagy erőforrásokra is kiterjednek. Előfordulhat például, hogy értesítést szeretne kapni, ha a myProductionResourceGroup valamelyik virtuális gépe törölve van. Vagy előfordulhat, hogy értesítést szeretne kapni, ha az előfizetésben lévő felhasználóhoz új szerepkörök vannak rendelve.
* A szolgáltatás állapotára vonatkozó esemény történik. A szolgáltatás állapotával kapcsolatos események az előfizetésben lévő erőforrásokra vonatkozó incidensek és karbantartási események értesítését tartalmazzák.

A tevékenység naplójában a riasztási szabályok létrehozására szolgáló feltételek értelmezése egyszerű hasonlóságot jelent, ha az eseményeket a [Azure Portal tevékenység naplóján](activity-log-view.md#azure-portal)keresztül vizsgálja vagy szűri. A Azure Monitor-Activity naplóban egy szűrő vagy megkeresheti a szükséges eseményt, majd létrehozhat egy riasztást a **tevékenység naplójának hozzáadása riasztás** gomb használatával.

Mindkét esetben a műveletnapló riasztása csak az előfizetésben lévő azon eseményekhez figyeli a riasztást, amelyekben a riasztás létrejött.

Egy műveletnapló-eseményen a JSON-objektum minden legfelső szintű tulajdonsága alapján beállíthat egy tevékenység naplójának riasztását. További információ: Kategóriák a [tevékenység naplójában](activity-log-view.md#categories-in-the-activity-log). További információ a szolgáltatás állapotával kapcsolatos eseményekről: [fogadási tevékenység naplójának riasztásai a szolgáltatási értesítéseken](alerts-activity-log-service-notifications.md). 

A műveletnapló riasztásai néhány gyakori lehetőséggel rendelkeznek:

- **Kategória**: adminisztráció, Service Health, autoskálázás, biztonság, házirend és javaslat. 
- **Hatókör**: azon erőforrások vagy erőforrások csoportja, amelyekhez a riasztás a tevékenység naplójában meg van határozva. A tevékenység naplójának riasztási hatóköre különböző szinteken határozható meg:
    - Erőforrás szintje: például egy adott virtuális gép esetében
    - Erőforráscsoport szintje: például egy adott erőforráscsoport összes virtuális gépe
    - Előfizetési szint: például az előfizetésben lévő összes virtuális gép (vagy) az előfizetés összes erőforrása
- **Erőforráscsoport**: alapértelmezés szerint a rendszer a riasztási szabályt a hatókörben definiált célként megadott erőforrás-csoportba menti. A felhasználó megadhatja azt az erőforráscsoportot is, amelyben a riasztási szabályt tárolni szeretné.
- **Erőforrás típusa**: erőforrás-kezelő által definiált névtér a riasztás céljához.
- **Művelet neve**: a [Azure Resource Manager művelet](../../role-based-access-control/resource-provider-operations.md) neve a szerepköralapú Access Controlhoz. A Azure Resource Managerban nem regisztrált műveletek nem használhatók a műveletnapló riasztási szabályaiban.
- **Szint**: az esemény súlyossági szintje (tájékoztató, figyelmeztetés, hiba vagy kritikus).
- **Állapot**: az esemény állapota, általában elindítva, sikertelen, vagy sikeres.
- A (z) **által kezdeményezett esemény**, más néven "hívó". A műveletet végrehajtó felhasználó e-mail-címe vagy Azure Active Directory azonosítója.

> [!NOTE]
> Egy előfizetésben akár 100 riasztási szabály is létrehozható egy hatóköri tevékenységhez: egyetlen erőforrás, az erőforráscsoport összes erőforrása (vagy) a teljes előfizetési szint.

Ha egy műveletnapló-riasztás aktiválva van, a műveletek vagy értesítések létrehozásához egy műveleti csoportot használ. A műveleti csoport az értesítési fogadók újrafelhasználható készlete, például e-mail-címek, webhook URL-címek vagy SMS-telefonszámok. A fogadók több riasztásból is hivatkozhatnak az értesítési csatornák központosítására és csoportosítására. A műveletnapló riasztásának meghatározásakor két lehetőség közül választhat. A következőket teheti:

* Meglévő műveleti csoport használata a tevékenység naplójának riasztásában.
* Hozzon létre egy új műveleti csoportot.

A műveleti csoportokkal kapcsolatos további információkért lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](action-groups.md).


## <a name="next-steps"></a>További lépések

- [A riasztások áttekintése](alerts-overview.md).
- Tudnivalók a [műveletnapló-riasztások létrehozásáról és módosításáról](alerts-activity-log.md).
- Tekintse át a [tevékenység naplójának riasztása webhook sémáját](activity-log-alerts-webhook.md).
- Tudnivalók a [szolgáltatás állapotával kapcsolatos értesítésekről](service-notifications.md).
