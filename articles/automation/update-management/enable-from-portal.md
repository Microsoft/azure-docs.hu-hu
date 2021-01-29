---
title: Azure Automation Update Management engedélyezése a Azure Portal
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management a Azure Portal.
services: automation
ms.subservice: update-management
ms.date: 01/07/2021
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 089c5fea6ac4a6fc4fb25af2d631335ef51cf4cc
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054906"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Az Update Management engedélyezése az Azure Portalról

Ez a cikk azt ismerteti, hogyan engedélyezheti a virtuális gépek [Update Management](overview.md) funkcióját a Azure Portal tallózásával. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő Azure-beli virtuális gépet.

A virtuális gépek kezeléséhez használható erőforráscsoportok számát a Resource Manager-alapú üzemelő [példányok](../../azure-resource-manager/templates/deploy-to-resource-group.md)korlátozzák. A Resource Manager-alapú üzemelő példányok, amelyek nem tévesztendő össze a frissítési környezetekkel, üzembe helyezésük legfeljebb öt erőforráscsoport lehet. Ezen erőforráscsoportok közül kettő a Log Analytics munkaterület, az Automation-fiók és a kapcsolódó erőforrások konfigurálására van fenntartva. Ez három erőforráscsoportot hagy el, amelyekkel kiválaszthatja a Update Management általi felügyeletet. Ez a korlát csak egyidejű telepítésre vonatkozik, nem az Automation szolgáltatással felügyelhető erőforráscsoportok számára.

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../automation-security-overview.md) a gépek kezeléséhez.
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. A Azure Portal navigáljon a **virtuális gépekhez**.

2. A **virtuális gépek** lapon jelölje be a jelölőnégyzeteket a Update Management hozzáadni kívánt virtuális gépek kiválasztásához. Egyszerre legfeljebb három különböző erőforráscsoport számára adhat hozzá gépeket. Az Azure-beli virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül.

    ![Virtuális gépek listája](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > A szűrési vezérlők használatával különböző előfizetések, helyszínek és erőforráscsoportok közül választhat ki virtuális gépeket. A felső jelölőnégyzetre kattintva kiválaszthatja a listában szereplő összes virtuális gépet.

    [![Update Management engedélyezése](./media/enable-from-portal/onboard-feature.png)](./media/enable-from-portal/onboard-feature-expanded.png#lightbox)

3. Válassza a **szolgáltatások** lehetőséget, és válassza a **Update Management** lehetőséget a Update Management funkcióhoz.

4. A virtuális gépek listája úgy van szűrve, hogy csak az azonos előfizetésben és helyen lévő virtuális gépeket jelenítse meg. Ha a virtuális gépek több mint három erőforráscsoporthoz vannak, akkor az első három erőforráscsoport van kiválasztva.

5. Alapértelmezés szerint egy meglévő Log Analytics munkaterület és Automation-fiók van kiválasztva. Ha más Log Analytics-munkaterületet és Automation-fiókot szeretne használni, válassza az **Egyéni** lehetőséget az egyéni konfiguráció lapon való kiválasztásához. Ha Log Analytics munkaterületet választ, ellenőrizze, hogy van-e egy Automation-fiókkal társítva. Ha a rendszer csatolt Automation-fiókot talál, a következő képernyő jelenik meg. Ha elkészült, kattintson az **OK** gombra.

    [![Munkaterület és fiók kiválasztása](./media/enable-from-portal/select-workspace-and-account.png)](./media/enable-from-portal/select-workspace-and-account-expanded.png#lightbox)

6. Ha a kiválasztott munkaterület nincs Automation-fiókhoz kapcsolva, a következő képernyő jelenik meg. Válasszon ki egy Automation-fiókot, és ha elkészült, kattintson **az OK gombra** .

    ![Nincs munkaterület](media/enable-from-portal/no-workspace.png)

7. Törölje az összes olyan virtuális gépet, amelyet nem kíván engedélyezni. A nem engedélyezhető virtuális gépek már ki vannak választva.

8. A funkció engedélyezéséhez válassza az **Engedélyezés** lehetőséget. A Update Management engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a frissítések értékelését.

## <a name="next-steps"></a>Következő lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](manage-updates-for-vm.md).
* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).
* A Windows Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Windows Update Agent problémáinak elhárítása](../troubleshoot/update-agent-issues.md)című témakört.
* A Linux Update agenttel kapcsolatos problémák elhárításához tekintse meg a [Linux frissítési ügynökkel kapcsolatos problémák elhárítása](../troubleshoot/update-agent-issues-linux.md)című témakört.