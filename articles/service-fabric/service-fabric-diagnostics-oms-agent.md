---
title: Teljesítményfigyelés Azure Monitor naplókkal
description: Ismerje meg, hogyan állíthatja be a Log Analytics ügynököt az Azure Service Fabric-fürtökhöz tartozó tárolók és teljesítményszámlálók figyeléséhez.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 47017fdb5f1fbaba800e71dea21afe2c39bd91e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570140"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Teljesítményfigyelés Azure Monitor naplókkal

Ez a cikk a Log Analytics-ügynök virtuálisgép-méretezési csoportként való hozzáadásának lépéseit ismerteti a fürthöz, és a meglévő Azure Log Analytics-munkaterülethez csatlakoztatható. Ez lehetővé teszi a tárolók, alkalmazások és Teljesítményfigyelés diagnosztikai adatainak gyűjtését. Ha hozzáad egy bővítményt a virtuálisgép-méretezési csoport erőforrásához, Azure Resource Manager biztosítja, hogy az minden csomóponton telepítve legyen, még a fürt skálázásakor is.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy már be van állítva egy Azure Log Analytics-munkaterület. Ha nem, a [Azure monitor naplók beállításához](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Az ügynök bővítmény hozzáadása az Azure CLI-n keresztül

Az Log Analytics-ügynök fürthöz való hozzáadásának legjobb módja az Azure CLI-vel elérhető virtuálisgép-méretezési csoport API-k használatával. Ha még nem állította be az Azure CLI-t, látogasson el Azure Portalre, és nyisson meg egy [Cloud Shell](../cloud-shell/overview.md) példányt, vagy [telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.

1. A Cloud Shell kérése után ellenőrizze, hogy az erőforrással megegyező előfizetésben dolgozik-e. Jelölje be ezt a `az account show` elemet, és győződjön meg arról, hogy a "Name" (név) érték megegyezik a fürt előfizetésével.

2. A portálon navigáljon ahhoz az erőforráscsoporthoz, ahol a Log Analytics munkaterület található. Kattintson a log Analytics-erőforrásra (az erőforrás típusa Log Analytics munkaterület lesz). Az erőforrás-Áttekintés lapon kattintson a **Speciális beállítások** elemre a bal oldali menüben a beállítások szakaszban.

    ![Log Analytics-Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Ha **Windows-fürtöt vagy Linux** -fürtöt hoz létre, kattintson a **Windows-kiszolgálók** lehetőségre. Ezen az oldalon megtekintheti `workspace ID` és `workspace key` (az elsődleges kulcsként jelenik meg a portálon). A következő lépéshez mindkettőre szüksége lesz.

4. Futtassa a parancsot a Log Analytics-ügynök a fürtre való telepítéséhez az `vmss extension set` API használatával:

    Windows-fürt esetén:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Linux-fürtök esetén:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Íme egy példa arra, hogy a Windows-fürthöz milyen Log Analytics-ügynök van hozzáadva.

    ![Log Analytics Agent CLI-parancs](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Ennek elvégzéséhez kevesebb, mint 15 percnek kell lennie ahhoz, hogy sikeresen hozzáadja az ügynököt a csomópontokhoz. A (z) API használatával ellenőrizheti, hogy az ügynökök hozzá lettek-e adva `az vmss extension list` :

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök hozzáadása a Resource Manager-sablon használatával

Az Azure Log Analytics-munkaterületet üzembe helyező Resource Manager-sablonok, valamint az egyes csomópontokhoz tartozó ügynökök [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) vagy [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)rendszeren is elérhetők.

A sablont letöltheti és módosíthatja úgy, hogy olyan fürtöt helyezzen üzembe, amely megfelel az igényeinek.

## <a name="view-performance-counters"></a>Teljesítményszámlálók megtekintése

Most, hogy hozzáadta a Log Analytics-ügynököt, a Log Analytics portálra bejelentkezve kiválaszthatja, hogy mely teljesítményszámlálókat szeretné összegyűjteni.

1. A Azure Portal keresse meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics megoldást. Válassza **a \<nameOfLog AnalyticsWorkspace\> ServiceFabric** lehetőséget.

2. Kattintson a **Log Analytics** elemre.

3. Kattintson a **Speciális beállítások** elemre.

4. Kattintson **az adat**, majd a **Windows-vagy Linux-teljesítményszámlálók** elemre. Az alapértelmezett számlálók listája lehetővé teszi az engedélyezést, és beállíthatja a gyűjtemény intervallumát is. [További teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) is hozzáadhat a gyűjtéshez. Ennek a [cikknek](/windows/win32/perfctrs/specifying-a-counter-path)a megfelelő formátumra hivatkozik.

5. Kattintson a **Mentés**, majd **az OK** gombra.

6. A speciális beállítások panel bezárásához.

7. Az általános fejléc alatt kattintson a **munkaterület összegzése** lehetőségre.

8. A csempék egy gráf formájában jelennek meg az összes engedélyezett megoldáshoz, beleértve az egyiket a Service Fabric. A Service Fabric Analytics megoldás folytatásához kattintson a **Service Fabric** gráfra.

9. Az operatív csatornán és a megbízható szolgáltatások eseményein grafikonokkal ellátott csempék jelennek meg. A kiválasztott számlálóhoz tartozó adatok grafikus ábrázolása a csomópont Metrikái alatt fog megjelenni.

10. A további részletek megtekintéséhez kattintson a Container metrika gráfra. A teljesítményszámláló adataihoz hasonlóan is lekérdezheti a fürt eseményeinek adatait, és szűrheti a csomópontok, a teljesítményszámláló nevét és az értékeket a Kusto lekérdezési nyelv használatával.

![Log Analytics teljesítményszámláló-lekérdezés](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Következő lépések

* A megfelelő [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md)összegyűjtése. Ha a Log Analytics-ügynököt egy adott teljesítményszámlálók gyűjtésére szeretné beállítani, tekintse át az [adatforrások konfigurálását ismertető témakört](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources)
* Azure Monitor naplók konfigurálása az [automatizált riasztások](../azure-monitor/alerts/alerts-overview.md) beállításához az észlelés és a diagnosztika támogatásához
* Alternatív megoldásként összegyűjtheti a teljesítményszámlálók [Azure Diagnostics bővítmény használatával, és elküldheti őket Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
