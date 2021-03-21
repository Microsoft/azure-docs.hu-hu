---
title: Azure Automation Change Tracking és leltár engedélyezése Azure-beli virtuális gépről
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a Change Tracking és a leltár egy Azure-beli virtuális gépről.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 61b45d6f6414b1e8e1f48f6d46957b21b9b8c58b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052498"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>A Change Tracking és az Inventory engedélyezése Azure-beli virtuális gépről

Ez a cikk azt ismerteti, hogyan használható egy Azure-beli virtuális gép a [change Tracking és a leltár](overview.md) más gépeken való engedélyezéséhez. Az Azure-beli virtuális gépek méretének engedélyezéséhez Change Tracking és leltár használatával engedélyeznie kell egy meglévő virtuális gépet.

> [!NOTE]
> A Change Tracking és a leltár engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Egy [Automation-fiók](../automation-security-overview.md) a gépek kezeléséhez.
* Egy [virtuális gép](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="enable-change-tracking-and-inventory"></a>A Change Tracking és az Inventory engedélyezése

1. A [Azure Portal](https://portal.azure.com)válassza a **virtuális gépek** lehetőséget, vagy keresse meg és válassza ki a **virtuális gépek** lehetőséget a kezdőlapon.

2. Válassza ki azt a virtuális gépet, amelynek engedélyezni szeretné a Change Tracking és a leltárt. A virtuális gépek bármelyik régióban létezhetnek, az Automation-fiókja helyétől függetlenül.

3. A virtuális gép lapon válassza a **leltár** vagy a **változások követése** lehetőséget a **konfiguráció** felügyelete alatt.

4. Rendelkeznie kell `Microsoft.OperationalInsights/workspaces/read` engedéllyel annak megállapításához, hogy a virtuális gép engedélyezve van-e a munkaterületen. További információ a szükséges engedélyekről: [szolgáltatások telepítési engedélyei](../automation-role-based-access-control.md#feature-setup-permissions). Ha többet szeretne megtudni arról, hogyan engedélyezheti egyszerre több gépet, tekintse meg az [Automation-fiók Change Tracking és leltározásának engedélyezése](enable-from-automation-account.md)című témakört.

5. Válassza ki az Log Analytics munkaterületet és Automation-fiókot, majd kattintson az **Engedélyezés** lehetőségre a virtuális gép Change Trackingának és leltározásának engedélyezéséhez. A telepítés elvégzése akár 15 percet is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

* A szolgáltatással kapcsolatos további részletekért tekintse meg a [change Tracking kezelése](manage-change-tracking.md) és a [leltár kezelése](manage-inventory-vms.md)című témakört.

* A szolgáltatással kapcsolatos általános problémák elhárításához tekintse meg a [change Tracking és a leltárral kapcsolatos problémák elhárítása](../troubleshoot/change-tracking.md)című témakört.