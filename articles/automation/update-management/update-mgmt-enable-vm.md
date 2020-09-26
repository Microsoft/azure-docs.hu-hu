---
title: Azure Automation Update Management engedélyezése Azure-beli virtuális gépről
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Update Management egy Azure-beli virtuális gépről.
services: automation
ms.date: 09/16/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: dead4649635bebd9f73f79937dea1d24565f273e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327796"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Az Update Management engedélyezése Azure-beli virtuális gépről

Ez a cikk azt ismerteti, hogyan használható egy Azure-beli virtuális gép a [Update Management](update-mgmt-overview.md) funkció más gépeken való engedélyezéséhez. Az Azure-beli virtuális gépek méretének engedélyezéséhez Update Management használatával engedélyeznie kell egy meglévő virtuális gépet.

> [!NOTE]
> A Update Management engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../index.yml) a gépek kezeléséhez.
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>A szolgáltatás engedélyezése a központi telepítéshez

1. A [Azure Portal](https://portal.azure.com)válassza a **virtuális gépek** lehetőséget, vagy keresse meg és válassza ki a **virtuális gépek** lehetőséget a kezdőlapon.

2. Válassza ki azt a virtuális gépet, amelynek engedélyezni szeretné a Update Management. A virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül. Ön

3. A virtuális gép lap **műveletek**területén válassza a **vendég + gazdagép frissítései**lehetőséget.

    ![Vendég + gazdagép frissítéseinek kiválasztása a bal oldali panelen](media/update-mgmt-enable-vm/select-guest-and-os-updates.png)

4. Rendelkeznie kell `Microsoft.OperationalInsights/workspaces/read` engedéllyel annak megállapításához, hogy a virtuális gép engedélyezve van-e a munkaterületen. További információ a szükséges engedélyekről: a [gépek engedélyezéséhez szükséges engedélyek](../automation-role-based-access-control.md#feature-setup-permissions). Ha többet szeretne megtudni arról, hogyan engedélyezheti egyszerre több gépet, tekintse meg az [Automation-fiók Update Managementának engedélyezése](update-mgmt-enable-automation-account.md)című témakört.

5. A Update Management engedélyezése lapon válassza a Log Analytics munkaterület és az Automation-fiók elemet, és kattintson az **Engedélyezés** gombra a Update Management engedélyezéséhez. A Update Management engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a frissítés értékelését a virtuális gépről.

    ![Az Update Management engedélyezése](media/update-mgmt-enable-vm/enable-update-management.png)

## <a name="next-steps"></a>Következő lépések

* Az Update Management virtuális gépekhez való használatához lásd: [Az Azure-beli virtuális gépek frissítéseinek és javításának kezelése](update-mgmt-manage-updates-for-vm.md).

* Az általános Update Management hibák elhárításával kapcsolatban lásd: [Update Management problémák elhárítása](../troubleshoot/update-management.md).
