---
title: Tárolók figyelése Azure Monitor naplókkal
description: Azure Monitor naplókat használhat az Azure Service Fabric-fürtökön futó tárolók figyelésére.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: c1d118fa1fb65663f37e33f739bf4a72a14e1c52
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998217"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Tárolók figyelése Azure Monitor naplókkal
 
Ez a cikk ismerteti azokat a lépéseket, amelyek szükségesek a Azure Monitor naplók tároló-figyelési megoldás beállításához a tároló eseményeinek megtekintéséhez. Ha a fürtöt tároló események gyűjtésére szeretné beállítani, tekintse meg ezt a [lépésenkénti útmutatót](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>A tároló-figyelési megoldás beállítása

> [!NOTE]
> Azure Monitor naplókat kell beállítania a fürthöz, valamint rendelkeznie kell a csomópontokon telepített Log Analytics-ügynökkel. Ha nem, kövesse a [Azure monitor-naplók beállítása](service-fabric-diagnostics-oms-setup.md) és [a log Analytics-ügynök hozzáadása a fürthöz](service-fabric-diagnostics-oms-agent.md) című témakör lépéseit.

1. Miután a fürt be van állítva Azure Monitor naplókkal és a Log Analytics ügynökkel, helyezze üzembe a tárolókat. Várjon, amíg a tárolók üzembe helyezhetők, mielőtt továbblép a következő lépésre.

2. Az Azure Marketplace-en keresse meg a *tároló-figyelési megoldást* , és kattintson a monitoring és felügyelet kategória alatt látható **Container monitoring Solution** erőforrásra.

    ![Tárolómegoldások hozzáadása](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Hozza létre a megoldást egy olyan munkaterületen belül, amely már létre van hozva a fürthöz. Ez a módosítás automatikusan elindítja az ügynököt a Docker-információk tárolókban való összegyűjtésének megkezdéséhez. Körülbelül 15 perc múlva látnia kell a megoldást a bejövő naplók és statisztikák alapján, ahogy az alábbi képen is látható.

    ![Alapszintű Log Analytics irányítópult](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Az ügynök lehetővé teszi több, a tárolóra vonatkozó naplók begyűjtését Azure Monitor naplókban, illetve a teljesítménymutatók megjelenítéséhez. A gyűjtött naplózási típusok a következők:

* ContainerInventory: a tároló helyének, nevének és képeinek információit jeleníti meg
* ContainerImageInventory: a telepített lemezképekkel kapcsolatos információk, beleértve az azonosítókat és a méreteket
* ContainerLog: megadott hibanapló, Docker-naplók (StdOut stb.) és egyéb bejegyzések
* ContainerServiceLog: a futtatott Docker Daemon-parancsok
* Perf: teljesítményszámlálók, beleértve a tároló CPU-t, a memóriát, a hálózati forgalmat, a lemez i/o-t és az egyéni metrikákat a gazdagépekről



## <a name="next-steps"></a>További lépések
* További információ a [Azure monitor naplók tárolói megoldásáról](../azure-monitor/insights/containers.md).
* További információ a Service Fabric- [Service Fabric és-tárolók](service-fabric-containers-overview.md) tároló-előkészítéséről
* Ismerkedjen meg az Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../azure-monitor/log-query/log-query-overview.md) funkciókkal
* Azure Monitor naplók konfigurálása [automatizált riasztási](../azure-monitor/platform/alerts-overview.md) szabályok beállításához az észlelés és a diagnosztika támogatásához
