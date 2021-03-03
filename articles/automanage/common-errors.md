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
ms.openlocfilehash: df5133ad4bb3155afdc9d43e595591d9cfda4ea0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644442"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Gyakori autokezelési hibák elhárítása
Előfordulhat, hogy az automanage utasítás nem tud bejelentkezni egy gépet a szolgáltatásba. Ez a dokumentum ismerteti, hogyan lehet elhárítani az üzembe helyezési hibákat, és megoszthatja azokat a gyakori okokat, amelyek miatt előfordulhat, hogy a központi telepítések meghiúsulnak, és leírja a lehetséges

## <a name="troubleshooting-deployment-failures"></a>Telepítési hibák elhárítása
Egy gép az automanage-be történő bevezetéséhez egy Azure Resource Manager központi telepítés jön létre. Ha a bevezetési művelet sikertelen, hasznos lehet a központi telepítéssel kapcsolatos további részletekért tájékozódni, hogy miért nem sikerült. A hiba részletei menüben található központi telepítések hivatkozásai a lenti ábrán láthatók.

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Hiba az automanage művelet részleteinek megjelenítéséhez.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Győződjön meg arról, hogy a meghibásodott virtuális gépet tartalmazó erőforráscsoport központi telepítései vannak
A leállási hiba a (z) rendszer azon erőforráscsoporthoz tartozó központi telepítésekre mutató hivatkozást tartalmaz, amely a sikertelen bevezetést tartalmazó gépet és egy előtag nevét tartalmazza, amellyel szűrheti a központi telepítéseket. A hivatkozásra kattintva megtekintheti a központi telepítések panelt, ahol szűrheti az üzemelő példányokat, és megtekintheti a központi telepítések automatikus kezelését a gépen. Ha több régióban végez üzembe helyezést, győződjön meg arról, hogy a megfelelő régióban található központi telepítésre kattint.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>A meghibásodott virtuális gépet tartalmazó előfizetés központi telepítésének keresése
Ha nem jelenik meg hibák az erőforráscsoport-telepítésben, akkor a következő lépés az előfizetésében található azon központi telepítések áttekintése, amelyek a bevezetést nem okozó virtuális gépet tartalmazzák. Kattintson az **előfizetéshez tartozó központi telepítések** hivatkozásra a hiba legördülő menüjében, és szűrje a központi telepítést az automatikus **felügyelet – DefaultResourceGroup** szűrő használatával. Használja az erőforráscsoport nevét a meghibásodás panelen a központi telepítések szűréséhez. A központi telepítési név utótagja a régió neve lesz. Ha több régióban végez üzembe helyezést, győződjön meg arról, hogy a megfelelő régióban található központi telepítésre kattint.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Log Analytics-munkaterülethez csatolt előfizetésben lévő központi telepítések megtekintése
Ha nem látja a meghibásodott virtuális gépet tartalmazó erőforráscsoport vagy előfizetés hibás központi telepítését, és ha a meghibásodott virtuális gép egy másik előfizetéshez tartozó Log Analytics munkaterülethez csatlakozik, akkor lépjen a Log Analytics munkaterülethez társított előfizetésre, és ellenőrizze a sikertelen telepítéseket.

## <a name="common-deployment-errors"></a>Gyakori telepítési hibák

Hiba |  Kockázatcsökkentés
:-----|:-------------|
Nem megfelelő engedélyekkel rendelkező fiókra vonatkozó engedélyek hibájának kezelése | Ez akkor fordulhat elő, ha nemrég áthelyezett egy új, egy új bérlőbe egy új automanage-fiókot tartalmazó előfizetést. A megoldásának lépései [itt](./repair-automanage-account.md)találhatók.
Munkaterület-régió nem felel meg a régió megfeleltetési követelményeinek | Az automatikus felügyelet nem tudta bekészíteni a gépet, de a Log Analytics munkaterületet, amelyhez a gép jelenleg kapcsolódik, nincs leképezve támogatott Automation-régióra. Győződjön meg arról, hogy a meglévő Log Analytics munkaterület és az Automation-fiók egy [támogatott régió-hozzárendelésben](../automation/how-to/region-mappings.md)található.
"A hozzárendelés sikertelen; nem érhető el további információ " | Nyisson meg egy esetet Microsoft Azure támogatással.

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure automanage szolgáltatásról](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [A virtuális gépek autofelügyeletének engedélyezése a Azure Portalban](quick-create-virtual-machines-portal.md)