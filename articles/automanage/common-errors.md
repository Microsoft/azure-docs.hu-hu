---
title: Az Azure-beli alapkezeléssel kapcsolatos gyakori hibák elhárítása
description: Bevezetési hibák általános kezelése és hibaelhárítása
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 3c9f1b76bb707a296da00ac503482efe6a22385b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278337"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Gyakori autokezelési hibák elhárítása
Előfordulhat, hogy az automanage utasítás nem tud bejelentkezni egy gépet a szolgáltatásba. Ez a dokumentum ismerteti, hogyan lehet elhárítani az üzembe helyezési hibákat, és megoszthatja azokat a gyakori okokat, amelyek miatt előfordulhat, hogy a központi telepítések meghiúsulnak, és leírja a lehetséges

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása
Egy gép az automanage-be történő bevezetéséhez egy Azure Resource Manager központi telepítés jön létre. További információ: a központi telepítés további részleteket tartalmaz, hogy miért nem sikerült. A hiba részletei menüben található központi telepítések hivatkozásai a lenti ábrán láthatók.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Hiba az automanage művelet részleteinek megjelenítéséhez.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Győződjön meg arról, hogy a meghibásodott virtuális gépet tartalmazó erőforráscsoport központi telepítései vannak
A leállási hiba a bevezetést nem okozó gépet tartalmazó erőforráscsoport hivatkozását tartalmazza. A menü egy előtag-nevet is tartalmaz, amellyel szűrheti a (z) rendszerű központi telepítéseket. Ha az üzembe helyezés hivatkozásra kattint, a központi telepítések panelre kerül, ahol szűrheti a központi telepítéseket, és megtekintheti a központi telepítések automatikus kezelését a gépen. Ha több régióban végez üzembe helyezést, győződjön meg arról, hogy a megfelelő régióban található központi telepítésre kattint.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>A meghibásodott virtuális gépet tartalmazó előfizetés központi telepítésének keresése
Ha nem jelenik meg hibák az erőforráscsoport-telepítésben, akkor a következő lépés az előfizetésében található azon központi telepítések áttekintése, amelyek a bevezetést nem okozó virtuális gépet tartalmazzák. Kattintson az **előfizetéshez tartozó központi telepítések** hivatkozásra a hiba legördülő menüjében, és szűrje a központi telepítést az automatikus **felügyelet – DefaultResourceGroup** szűrő használatával. Használja az erőforráscsoport nevét a meghibásodás panelen a központi telepítések szűréséhez. A központi telepítési név utótagja a régió neve lesz. Ha több régióban végez üzembe helyezést, győződjön meg arról, hogy a megfelelő régióban található központi telepítésre kattint.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics-munkaterülethez csatolt előfizetésben lévő központi telepítések megtekintése
Ha nem látja a meghibásodott virtuális gépet tartalmazó erőforráscsoport vagy előfizetés hibás központi telepítését, és ha a meghibásodott virtuális gép egy másik előfizetéshez tartozó Log Analytics munkaterülethez csatlakozik, akkor lépjen a Log Analytics munkaterülethez társított előfizetésre, és ellenőrizze a sikertelen telepítéseket.

## <a name="common-deployment-errors"></a>Gyakori telepítési hibák

Hiba |  Kockázatcsökkentés
:-----|:-------------|
Nem megfelelő engedélyekkel rendelkező fiókra vonatkozó engedélyek hibájának kezelése | Ez a hiba akkor fordulhat elő, ha nemrég áthelyezett egy új, egy új bérlőbe egy új automanage-fiókot tartalmazó előfizetést. A hiba megoldásának lépései [itt](./repair-automanage-account.md)találhatók.
Munkaterület-régió nem felel meg a régió megfeleltetési követelményeinek | Az automatikus felügyelet nem tudta bekészíteni a gépet, mert az Log Analytics munkaterület, amelyhez a gép jelenleg kapcsolódik, nincs leképezve támogatott Automation-régióra. Győződjön meg arról, hogy a meglévő Log Analytics munkaterület és az Automation-fiók egy [támogatott régió-hozzárendelésben](../automation/how-to/region-mappings.md)található.
"A hozzáférés megtagadva a (z)" {0} "nevű, felügyelt alkalmazás által létrehozott rendszer-megtagadási hozzárendelés miatt | Egy [denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) lett létrehozva az erőforráson, ami megakadályozta, hogy az automanage hozzáférjen az erőforráshoz. Lehetséges, hogy ezt a denyAssignment egy [tervezet](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) vagy egy [felügyelt alkalmazás](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview)hozta létre.
"Operációs rendszer adatai: Name =" (null) ", ver =" (null) ", ügynök állapota =" nem üzemkész "." | Győződjön meg arról, hogy a [támogatott ügynök minimális verziója](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)fut, az ügynök fut ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) és [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), és hogy az ügynök naprakész ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) és [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"Nem lehet meghatározni a virtuális gép operációs rendszerének neve:, ver. Ellenőrizze, hogy a virtuálisgép-ügynök fut-e, és hogy az aktuális állapot elkészült-e. " | Győződjön meg arról, hogy a [támogatott ügynök minimális verziója](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)fut, az ügynök fut ([Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) és [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)), és hogy az ügynök naprakész ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) és [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).

"A virtuális gép hibát jelzett a következő bővítmény feldolgozásakor:" IaaSAntimalware "| Győződjön meg arról, hogy a virtuális gépen már telepítve van egy másik antimalware/Antivirus-ajánlat. Ha ez nem sikerül, forduljon az ügyfélszolgálathoz.
ASC munkaterület: az automanage jelenleg nem támogatja a Log Analytics szolgáltatást a _helyen_. | Győződjön meg arról, hogy a virtuális gép egy [támogatott régióban](./automanage-virtual-machines.md#supported-regions)található.
A sablon központi telepítése a szabályzat megsértése miatt nem sikerült. További információért tekintse meg a részleteket. | Létezik egy szabályzat, amely megakadályozza a virtuális gép bevezetését. Tekintse át az előfizetésre vagy az erőforrás-csoportra alkalmazott szabályzatokat, amelyek a bevezetéshez használni kívánt virtuális gépet tartalmazzák.
"A hozzárendelés sikertelen; nem érhető el további információ "| Nyisson meg egy esetet Microsoft Azure támogatással.

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure automanage szolgáltatásról](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)