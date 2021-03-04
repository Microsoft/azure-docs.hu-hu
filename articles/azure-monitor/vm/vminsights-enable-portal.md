---
title: Azure Monitor engedélyezése egyetlen virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal
description: Megtudhatja, hogyan engedélyezheti a VM-információkat egyetlen Azure-beli virtuális gépen vagy virtuálisgép-méretezési csoporton a Azure Portal használatával.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035585"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Azure Monitor engedélyezése egyetlen virtuális gép vagy virtuálisgép-méretezési csoport számára a Azure Portal
Ez a cikk azt ismerteti, hogyan engedélyezhető a virtuális gép vagy virtuálisgép-méretezési csoport virtuálisgép-bepillantást a Azure Portal használatával. Ez az eljárás a következő módon használható:

- Azure virtuális gép
- Azure virtuálisgép-méretezési csoport
- Az Azure arc-hoz csatlakoztatott hibrid virtuális gép

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre és konfiguráljon egy log Analytics munkaterületet](./vminsights-configure-workspace.md). Másik lehetőségként új munkaterületet is létrehozhat a folyamat során.
- A [támogatott operációs rendszerekkel](./vminsights-enable-overview.md#supported-operating-systems) biztosíthatja, hogy a virtuális gép vagy a virtuálisgép-méretezési csoport operációs rendszere támogatott legyen. 

## <a name="enable-vm-insights"></a>A virtuális gépekkel való adatfelismerés engedélyezése

A Azure Portal válassza a **virtuális gépek**, **virtuálisgép-méretezési** csoportok vagy **kiszolgálók – Azure arc** lehetőséget, és válasszon ki egy erőforrást a listából. A menü **figyelés** területén válassza az **eredmények lehetőséget, majd** **engedélyezze** a beállítást. Az alábbi példa egy Azure-beli virtuális gépet mutat be, de a menü hasonló az Azure virtuálisgép-méretezési csoporthoz vagy az Azure-archoz.

![VM-alapú adatáttekintések engedélyezése virtuális géphez](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Ha a virtuális gép még nincs csatlakoztatva Log Analytics munkaterülethez, a rendszer kérni fogja, hogy válasszon egyet. Ha korábban még nem [hozott létre munkaterületet](../logs/quick-create-workspace.md), akkor kiválaszthatja azt a helyet, ahol a virtuális gép vagy a virtuálisgép-méretezési csoport telepítve van az előfizetésben. A rendszer létrehozza és konfigurálja ezt a munkaterületet, ha még nem létezik. Ha egy meglévő munkaterületet választ ki, akkor a rendszer a virtuálisgép-megállapításokhoz konfigurálja, ha még nem tette meg.

> [!NOTE]
> Ha olyan munkaterületet választ ki, amely korábban nem volt konfigurálva a virtuális gépekhez, a rendszer hozzáadja a *VMInsights* felügyeleti csomagot ehhez a munkaterülethez. Ezt a rendszer a munkaterülethez már csatlakoztatott összes ügynökre alkalmazza, függetlenül attól, hogy engedélyezve van-e a virtuális gépekkel kapcsolatos megállapítások számára. A teljesítményadatokat ezekből a virtuális gépekből gyűjti a rendszer, és a *InsightsMetrics* táblában tárolja.

![Munkaterület kiválasztása](media/vminsights-enable-portal/select-workspace.png)

Az állapotüzenetek a konfiguráció elvégzése után kapják meg az üzeneteket.

>[!NOTE]
>Ha a virtuálisgép-méretezési csoporthoz manuális frissítési modellt használ, frissítse a példányokat a telepítés befejezéséhez. A frissítések elindításához a **példányok** lapon, a **Beállítások** szakaszban olvashat.

![A virtuális gépek vizsgálatának engedélyezése az üzembe helyezés feldolgozásához](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Következő lépések

* A felderített alkalmazások függőségeinek megtekintéséhez lásd a virtuálisgép-információk [leképezésének használata](vminsights-maps.md) című témakört. 
* Lásd: az Azure-beli [virtuális gépek teljesítményének megtekintése](vminsights-performance.md) a szűk keresztmetszetek, a teljes kihasználtság és a virtuális gép teljesítményének azonosításához.
