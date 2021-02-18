---
title: Azure Monitor engedélyezése egyetlen virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal
description: Megtudhatja, hogyan engedélyezheti a Azure Monitor for VMst egyetlen Azure-beli virtuális gépen vagy virtuálisgép-méretezési csoporton a Azure Portal használatával.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba075930aa3541d0453b678c7d654635ae20da58
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100619798"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Azure Monitor engedélyezése egyetlen virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal
Ez a cikk azt ismerteti, hogyan engedélyezhető a Azure Monitor for VMs egy virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal használatával. Ez az eljárás a következő módon használható:

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport
- Az Azure arc-hoz csatlakoztatott hibrid virtuális gép

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre és konfiguráljon egy log Analytics munkaterületet](../insights/vminsights-configure-workspace.md). Másik lehetőségként új munkaterületet is létrehozhat a folyamat során.
- A [támogatott operációs rendszerekkel](../insights/vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport operációs rendszere támogatott legyen. 

## <a name="enable-azure-monitor-for-vms"></a>Azure Monitor for VMs engedélyezése

A Azure Portal válassza a **virtuális gépek**, **virtuálisgép-méretezési** csoportok vagy **kiszolgálók – Azure arc** lehetőséget, és válasszon ki egy erőforrást a listából. A menü **figyelés** területén válassza az **eredmények lehetőséget, majd** **engedélyezze** a beállítást. Az alábbi példa egy Azure-beli virtuális gépet mutat be, de a menü hasonló az Azure virtuálisgép-méretezési csoporthoz vagy az Azure-archoz.

![Virtuális gép Azure Monitor for VMsának engedélyezése](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Ha a virtuális gép még nincs csatlakoztatva Log Analytics munkaterülethez, a rendszer kérni fogja, hogy válasszon egyet. Ha korábban még nem [hozott létre munkaterületet](../../azure-monitor/learn/quick-create-workspace.md), akkor kiválaszthatja azt a helyet, ahol a virtuális gép vagy a virtuálisgép-méretezési csoport telepítve van az előfizetésben. A rendszer létrehozza és konfigurálja ezt a munkaterületet, ha még nem létezik. Ha kijelöl egy meglévő munkaterületet, akkor az Azure Monitor for VMs lesz konfigurálva, ha még nem történt meg.

> [!NOTE]
> Ha olyan munkaterületet választ ki, amely korábban nem lett konfigurálva a Azure Monitor for VMshoz, a rendszer hozzáadja a *VMInsights* felügyeleti csomagot ehhez a munkaterülethez. Ezt a rendszer a munkaterülethez már csatlakoztatott összes ügynökre alkalmazza, függetlenül attól, hogy engedélyezve van-e a Azure Monitor for VMs. A teljesítményadatokat ezekből a virtuális gépekből gyűjti a rendszer, és a *InsightsMetrics* táblában tárolja.

![Munkaterület kiválasztása](media/vminsights-enable-portal/select-workspace.png)

Az állapotüzenetek a konfiguráció elvégzése után kapják meg az üzeneteket.

>[!NOTE]
>Ha a virtuálisgép-méretezési csoporthoz manuális frissítési modellt használ, frissítse a példányokat a telepítés befejezéséhez. A frissítések elindításához a **példányok** lapon, a **Beállítások** szakaszban olvashat.

![Azure Monitor for VMs figyelés telepítésének engedélyezése](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Következő lépések

* Lásd: [Azure monitor for VMS leképezés használata](vminsights-maps.md) a felderített alkalmazások függőségeinek megtekintéséhez. 
* Lásd: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md) a szűk keresztmetszetek, a teljes kihasználtság és a virtuális gép teljesítményének azonosításához.
