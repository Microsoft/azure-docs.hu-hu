---
title: Azure Automation Change Tracking és leltár engedélyezése egy runbook
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Change Tracking és a leltár egy runbook.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: e5b42d6102737b778ea5d19cd7da3c2f64881b1b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585925"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>A Change Tracking és az Inventory engedélyezése runbookból

Ez a cikk azt ismerteti, hogyan használható a runbook a virtuális gépek [change Tracking és leltározásának](overview.md) engedélyezéséhez a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet.

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

Ez a módszer két runbookok használ:

* **Enable-MultipleSolution** – a konfigurációs adatokat kérő elsődleges runbook, a lekérdezi a megadott virtuális gépet, és más ellenőrzési ellenőrzéseket hajt végre, majd meghívja az **enable-AutomationSolution** runbook, hogy a megadott erőforráscsoporthoz tartozó minden virtuális gép számára konfigurálja a Change Tracking és a leltárt.
* **Enable-AutomationSolution – engedélyezi** a Change Tracking és a leltárt a célként megadott erőforráscsoport egy vagy több virtuális gépe számára. Ellenőrzi az előfeltételeket, ellenőrzi, hogy telepítve van-e a Log Analytics virtuálisgép-bővítmény, és telepíti, ha nem található, és hozzáadja a virtuális gépeket az Automation-fiókhoz társított, megadott Log Analytics munkaterület hatókör-konfigurációjához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../automation-security-overview.md) a gépek kezeléséhez.
* [Log Analytics munkaterület](../../azure-monitor/logs/design-logs-deployment.md)
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).
* Két Automation-eszköz, amelyet az **enable-AutomationSolution** runbook használ. Ez a runbook, ha még nem létezik az Automation-fiókjában, automatikusan importálja az **enable-MultipleSolution** runbook az első futtatásakor.
    * *LASolutionSubscriptionId*: az előfizetés azonosítója, ahol a log Analytics munkaterület található.
    * *LASolutionWorkspaceId*: az Automation-fiókhoz társított log Analytics munkaterület munkaterület-azonosítója.

    Ezek a változók a befoglalt virtuális gép munkaterületének konfigurálására szolgálnak. Ha ezek nincsenek megadva, a parancsfájl először megkeresi az előfizetésében Change Tracking és leltárba bevont virtuális gépeket, majd az Automation-fiók előfizetését, majd a felhasználói fiókhoz hozzáférő összes többi előfizetést. Ha nem megfelelően van konfigurálva, ez azt eredményezheti, hogy a gépek bekerülnek egy véletlenszerű Log Analytics munkaterületre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

1. A Azure Portal navigáljon az **Automation-fiókokhoz**. Az **Automation-fiókok** lapon válassza ki a fiókját a listából.

1. Az Automation-fiókban válassza a **leltár** vagy **change Tracking** lehetőséget a **konfiguráció kezelése** területen.

1. Válassza ki a Log Analytics munkaterületet, majd kattintson az **Engedélyezés** gombra. A leltár vagy Change Tracking engedélyezésekor megjelenik egy szalagcím.

    ![A Change Tracking és az Inventory engedélyezése](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Modulok telepítése és frissítése

Frissíteni kell a legújabb Azure-modulokra, és importálnia kell az az [. OperationalInsights](/powershell/module/az.operationalinsights) modult, hogy sikeresen engedélyezze Update Management a virtuális gépek számára a runbook.

1. Az Automation-fiókban válassza a **megosztott erőforrások** alatt található **modulok** elemet.

2. Az Azure-modulok a legújabb verzióra frissítéséhez válassza az **Azure-modulok frissítése** lehetőséget.

3. Az **Igen** gombra kattintva frissítheti az összes meglévő Azure-modult a legújabb verzióra.

    ![Modulok frissítése](media/enable-from-runbook/update-modules.png)

4. Visszatérés a **modulokhoz** a **megosztott erőforrások** területen.

5. Válassza a **Tallózás** katalógus lehetőséget a modul-gyűjtemény megnyitásához.

6. Keresse meg `Az.OperationalInsights` és importálja a modult az Automation-fiókjába.

    ![Az OperationalInsights modul importálása](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Válassza ki a kezelni kívánt Azure-beli virtuális gépet

Ha a Change Tracking és a leltár engedélyezve van, hozzáadhat egy Azure-beli virtuális gépet a szolgáltatás általi felügyelethez.

1. Az Automation-fiókban válassza a **változások követése** vagy **leltározás** elemet a **konfiguráció** felügyelete alatt.

2. Kattintson az Azure-beli **virtuális gépek hozzáadása** lehetőségre a virtuális gép hozzáadásához.

3. Válassza ki a virtuális gépet a listából, és kattintson az **Engedélyezés** gombra. Ez a művelet engedélyezi a virtuális gép Change Tracking és leltározását.

   ![Virtuális gép Change Trackingának és leltározásának engedélyezése](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Ha a Change Tracking és a leltár befejezése előtt megpróbál engedélyezni egy másik szolgáltatást, a következő üzenet jelenik meg: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Runbook importálása a Change Tracking és a leltár engedélyezéséhez

1. Az Automation-fiókban válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

2. Kattintson a **Böngészés a katalógusban** gombra.

3. Keressen rá az **Update és a Change Tracking** kifejezésre.

4. Válassza ki a runbook, és kattintson az **Importálás** elemre a **Forrás megtekintése** lapon.

5. A runbook az Automation-fiókba történő importálásához kattintson **az OK** gombra.

   ![Runbook importálása a telepítőhöz](media/enable-from-runbook/import-from-gallery.png)

6. A **Runbook** lapon jelölje be az **enable-MultipleSolution** Runbook, majd kattintson a **Szerkesztés** gombra. A szöveges szerkesztőben válassza a  **Közzététel** lehetőséget.

7. Amikor a rendszer rákérdez a megerősítésre, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="start-the-runbook"></a>A runbook indítása

A runbook elindításához engedélyeznie kell az Azure-beli virtuális gép Change Tracking és leltárát. Szükség van egy meglévő virtuális gépre és erőforráscsoporthoz, amelynek a funkciója engedélyezve van ahhoz, hogy egy vagy több virtuális gépet konfiguráljon a cél erőforráscsoporthoz.

1. Nyissa meg az **enable-MultipleSolution** runbook.

   ![Több megoldás runbook](media/enable-from-runbook/runbook-overview.png)

2. Kattintson a Start gombra, és adja meg a paraméterek értékét a következő mezőkben:

   * **VMNAME** – egy meglévő virtuális gép neve, amelyet hozzá szeretne adni a Change Trackinghoz és a leltárhoz. Hagyja üresen ezt a mezőt az erőforráscsoporthoz tartozó összes virtuális gép hozzáadásához.
   * **VMRESOURCEGROUP** – az engedélyezni kívánt virtuális gépek erőforráscsoport neve.
   * **SUBSCRIPTIONID** – az engedélyezni kívánt új virtuális gép előfizetés-azonosítója. A munkaterület előfizetésének használatához hagyja üresen ezt a mezőt. Ha más előfizetés-azonosítót használ, adja hozzá az Automation-fiókjához tartozó futtató fiókot az előfizetés közreműködőiként.
   * **ALREADYONBOARDEDVM** – annak a virtuális gépnek a neve, amely már manuálisan engedélyezve van a frissítésekhez.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – annak az erőforráscsoportnak a neve, amelyhez a virtuális gép tartozik.
   * **Megoldástípusa** – adja meg a **változáskövetési**.

   ![Az Enable-MultipleSolution runbook paraméterei](media/enable-from-runbook/runbook-parameters.png)

3. A runbookfeladat indításához kattintson az **OK** gombra.

4. A runbook feladat és a **feladatok** lapról fellépő hibák állapotának figyelése.

## <a name="next-steps"></a>Következő lépések

* A runbook a következő témakörben tekintheti meg: [az ütemtervek kezelése Azure Automationban](../shared-resources/schedules.md).

* A szolgáltatással kapcsolatos további részletekért tekintse meg a [change Tracking kezelése](manage-change-tracking.md) és a [leltár kezelése](manage-inventory-vms.md)című témakört.

* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](../troubleshoot/change-tracking.md)című témakört.


