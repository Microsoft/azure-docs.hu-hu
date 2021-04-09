---
title: Azure Automation Update Management engedélyezése a runbook-ből
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management egy runbook.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 11/24/2020
ms.custom: mvc
ms.openlocfilehash: 5377a3ff7ef7033b57f8785baa615a717ef7fa0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575877"
---
# <a name="enable-update-management-from-a-runbook"></a>Az Update Management engedélyezése runbookból

Ez a cikk azt ismerteti, hogyan használható a runbook a virtuális gépek [Update Management](overview.md) funkciójának engedélyezésére a környezetben. Az Azure-beli virtuális gépek méretének engedélyezéséhez engedélyeznie kell egy meglévő virtuális gépet Update Management.

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

Ez a módszer két runbookok használ:

* **Enable-MultipleSolution** – a konfigurációs adatokat kérő elsődleges runbook, a lekérdezi a megadott virtuális gépet, és más ellenőrzési ellenőrzéseket hajt végre, majd meghívja az **enable-AutomationSolution** runbook, hogy a megadott erőforráscsoporthoz tartozó összes virtuális gép számára konfigurálja a Update Management.
* **Enable-AutomationSolution – engedélyezi** a Update Management a célként megadott erőforráscsoport egy vagy több virtuális gépe számára. Ellenőrzi az előfeltételeket, ellenőrzi, hogy telepítve van-e a Log Analytics virtuálisgép-bővítmény, és telepíti, ha nem található, és hozzáadja a virtuális gépeket az Automation-fiókhoz társított, megadott Log Analytics munkaterület hatókör-konfigurációjához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../automation-security-overview.md) a gépek kezeléséhez.
* [Log Analytics munkaterület](../../azure-monitor/logs/design-logs-deployment.md)
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).
* Két Automation-eszköz, amelyet az **enable-AutomationSolution** runbook használ. Ez a runbook, ha még nem létezik az Automation-fiókjában, automatikusan importálja az **enable-MultipleSolution** runbook az első futtatásakor.
    * *LASolutionSubscriptionId*: az előfizetés azonosítója, ahol a log Analytics munkaterület található.
    * *LASolutionWorkspaceId*: az Automation-fiókhoz társított log Analytics munkaterület munkaterület-azonosítója.

    Ezek a változók a befoglalt virtuális gép munkaterületének konfigurálására szolgálnak, és ezeket manuálisan kell létrehozni. Ha ezek nincsenek megadva, a parancsfájl először az előfizetésében Update Management előfizetett virtuális gépet keres, amelyet az Automation-fiókhoz tartozó előfizetés követ, majd ezt követően minden más előfizetés, amelyhez a felhasználói fiók hozzáfér. Ha nem megfelelően van konfigurálva, ez azt eredményezheti, hogy a gépek bekerülnek egy véletlenszerű Log Analytics munkaterületre.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-update-management"></a>Az Update Management engedélyezése

1. A Azure Portal navigáljon az **Automation-fiókokhoz**. Az **Automation-fiókok** lapon válassza ki a fiókját a listából.

2. Az Automation-fiókjában válassza a **Update Management** lehetőséget **Update Management** alatt.

3. Válassza ki a Log Analytics munkaterületet, majd kattintson az **Engedélyezés** gombra. Amíg a Update Management engedélyezve van, megjelenik egy szalagcím.

    ![Az Update Management engedélyezése](media/enable-from-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>Modulok telepítése és frissítése

Frissíteni kell a legújabb Azure-modulokra, és importálnia kell a [AzureRM. OperationalInsights](/powershell/module/azurerm.operationalinsights) modult, hogy sikeresen engedélyezze Update Management a virtuális gépek számára a runbook.

1. Az Automation-fiókban válassza a **megosztott erőforrások** alatt található **modulok** elemet.

2. Az Azure-modulok a legújabb verzióra frissítéséhez válassza az **Azure-modulok frissítése** lehetőséget.

3. Az **Igen** gombra kattintva frissítheti az összes meglévő Azure-modult a legújabb verzióra.

    ![Modulok frissítése](media/enable-from-runbook/update-modules.png)

4. Visszatérés a **modulokhoz** a **megosztott erőforrások** területen.

5. Válassza a **Tallózás** katalógus lehetőséget a modul-gyűjtemény megnyitásához.

6. Keresse meg `AzureRM.OperationalInsights` és importálja a modult az Automation-fiókjába.

    ![Az OperationalInsights modul importálása](media/enable-from-runbook/import-operational-insights-module-azurerm.png)

## <a name="select-azure-vm-to-manage"></a>Válassza ki a kezelni kívánt Azure-beli virtuális gépet

Ha a Update Management engedélyezve van, hozzáadhat egy Azure-beli virtuális gépet a frissítések fogadásához.

1. Az Automation-fiókban válassza a **frissítés** kezelése lehetőséget az **Update Management szakaszban.**

2. Válassza az Azure-beli **virtuális gépek hozzáadása** lehetőséget a virtuális gép hozzáadásához.

3. Válassza ki a virtuális gépet a listából, és kattintson az **Engedélyezés** gombra a virtuális gép felügyelethez való konfigurálásához.

   ![Virtuális gép Update Managementának engedélyezése](media/enable-from-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Ha a Update Management telepítésének befejezése előtt megpróbál engedélyezni egy másik szolgáltatást, a következő üzenet jelenik meg: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>Runbook importálása a Update Management engedélyezéséhez

1. Az Automation-fiókban válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.

2. Kattintson a **Böngészés a katalógusban** gombra.

3. Keressen rá az **Update és a Change Tracking** kifejezésre.

4. Válassza ki a runbook, és kattintson az **Importálás** elemre a **Forrás megtekintése** lapon.

5. A runbook az Automation-fiókba történő importálásához kattintson **az OK** gombra.

   ![Runbook importálása a telepítőhöz](media/enable-from-runbook/import-from-gallery.png)

6. A **Runbook** lapon jelölje be az **enable-MultipleSolution** Runbook, majd kattintson a **Szerkesztés** gombra. A szöveges szerkesztőben válassza a  **Közzététel** lehetőséget.

7. Amikor a rendszer rákérdez a megerősítésre, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="start-the-runbook"></a>A runbook indítása

A runbook elindításához engedélyezve kell lennie Update Management egy Azure-beli virtuális gépen. Szükség van egy meglévő virtuális gépre és erőforráscsoporthoz, amelynek a funkciója engedélyezve van ahhoz, hogy egy vagy több virtuális gépet konfiguráljon a cél erőforráscsoporthoz.

1. Nyissa meg az **enable-MultipleSolution** runbook.

   ![Több megoldás runbook](media/enable-from-runbook/runbook-overview.png)

2. Kattintson a Start gombra, és adja meg a paraméterek értékét a következő mezőkben:

   * **VMNAME** – egy meglévő virtuális gép neve Update Managementhoz való hozzáadáshoz. Hagyja üresen ezt a mezőt az erőforráscsoporthoz tartozó összes virtuális gép hozzáadásához.
   * **VMRESOURCEGROUP** – az engedélyezni kívánt virtuális gépek erőforráscsoport neve.
   * **SUBSCRIPTIONID** – az engedélyezni kívánt új virtuális gép előfizetés-azonosítója. A munkaterület előfizetésének használatához hagyja üresen ezt a mezőt. Ha más előfizetés-azonosítót használ, adja hozzá az Automation-fiókjához tartozó futtató fiókot az előfizetés közreműködőiként.
   * **ALREADYONBOARDEDVM** – annak a virtuális gépnek a neve, amely már manuálisan engedélyezve van a frissítésekhez.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** – annak az erőforráscsoportnak a neve, amelyhez a virtuális gép tartozik.
   * **Megoldástípusa** – adja meg a **frissítéseket**.

   ![Az Enable-MultipleSolution runbook paraméterei](media/enable-from-runbook/runbook-parameters.png)

3. A runbookfeladat indításához kattintson az **OK** gombra.

4. A runbook feladat és a **feladatok** lapról fellépő hibák állapotának figyelése.

## <a name="next-steps"></a>Következő lépések

* A Update Management virtuális gépekhez való használatához lásd: [a virtuális gépek frissítéseinek és javításának kezelése](manage-updates-for-vm.md).

* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).
