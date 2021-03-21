---
title: Figyelés beállítása Azure Monitor naplókkal
description: Ismerje meg, hogyan állíthat be Azure Monitor naplókat az Azure Service Fabric-fürtök monitorozásához és az események elemzéséhez.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba62ac80b2f8d318d0d13e81e88cc63a8d893a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570353"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Azure Monitor naplók beállítása fürthöz

Azure Monitor naplókat ajánljuk a fürt szintű események figyelésére. Log Analytics munkaterületet a Azure Resource Manager, a PowerShell vagy az Azure Marketplace segítségével állíthat be. Ha a központi telepítés frissített Resource Manager-sablonját tartja jövőbeli használatra, használja ugyanazt a sablont a Azure Monitor naplók környezetének beállításához. A piactéren keresztüli üzembe helyezés egyszerűbb, ha már rendelkezik egy, a diagnosztika engedélyezésével telepített fürttel. Ha nem rendelkezik előfizetési szintű hozzáféréssel abban a fiókban, amelyre a üzembe helyezést végzi, telepítse a PowerShell vagy a Resource Manager-sablon használatával.

> [!NOTE]
> Ha Azure Monitor naplókat szeretne beállítani a fürt figyeléséhez, engedélyezni kell a diagnosztika használatát a fürt szintű vagy a platform szintű események megtekintéséhez. További információt a [diagnosztika beállítása Windows-fürtökben](service-fabric-diagnostics-event-aggregation-wad.md) és a [diagnosztika beállítása Linux-fürtökben](service-fabric-diagnostics-oms-syslog.md) című témakörben talál.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Log Analytics munkaterület üzembe helyezése az Azure Marketplace használatával

Ha egy fürt üzembe helyezése után szeretne hozzáadni egy Log Analytics munkaterületet, lépjen az Azure Marketplace-re a portálon, és keresse meg a **Service Fabric Analytics**. Ez egy egyéni megoldás az olyan Service Fabric központi telepítések esetében, amelyek Service Fabricra vonatkozó adattal rendelkeznek. Ebben a folyamatban létrehozza a megoldást (az irányítópultot az elemzések megjelenítéséhez) és a munkaterületet (a mögöttes fürtkonfiguráció összesítését).

1. A bal oldali navigációs menüben válassza az **új** lehetőséget. 

2. **Service Fabric Analytics** keresése. Válassza ki a megjelenő erőforrást.

3. Válassza a **Létrehozás** lehetőséget.

    ![Service Fabric Analytics a piactéren](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. A Service Fabric Analytics létrehozási ablakban válassza ki a **munkaterület kiválasztása** lehetőséget a **OMS munkaterület** mezőben, majd **hozzon létre egy új munkaterületet**. Töltse ki a szükséges bejegyzéseket. Az egyetlen követelmény, hogy az Service Fabric-fürt és a munkaterület előfizetése azonos. A bejegyzések ellenőrzése után a munkaterület üzembe helyezése megkezdődik. Az üzembe helyezés mindössze néhány percet vesz igénybe.

5. Ha elkészült, válassza a **Létrehozás** újra elemet a Service Fabric Analytics létrehozási ablakának alján. Győződjön meg arról, hogy az új munkaterület megjelenik a **OMS munkaterületen**. Ez a művelet hozzáadja a megoldást a létrehozott munkaterülethez.

Ha Windows rendszert használ, folytassa a következő lépésekkel, hogy összekösse Azure Monitor naplókat ahhoz a Storage-fiókhoz, ahol a fürt eseményei vannak tárolva. 

>[!NOTE]
>A Service Fabric Analytics megoldás csak Windows-fürtök esetén támogatott. Linux-fürtök esetében tekintse meg a [Linux-fürtök Azure monitor naplófájljainak beállítását ismertető](service-fabric-diagnostics-oms-syslog.md)cikket.  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>A Log Analytics munkaterület összekötése a fürttel 

1. A munkaterületet a fürtről érkező diagnosztikai adatokhoz kell csatlakoztatni. Keresse meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics megoldást. Válassza **az \<nameOfWorkspace\> ServiceFabric** lehetőséget, és lépjen az Áttekintés lapra. Itt módosíthatja a megoldás beállításait, a munkaterület beállításait, és elérheti a Log Analytics munkaterületet.

2. A bal oldali navigációs menü munkaterület- **adatforrások** területén válassza ki a **Storage-fiókok naplóit**.

3. A **Storage-fiók naplói** lapon a felül található **Hozzáadás** gombra kattintva adja hozzá a fürt naplóit a munkaterülethez.

4. Válassza ki a **Storage-fiók** lehetőséget a fürtben létrehozott megfelelő fiók hozzáadásához. Ha az alapértelmezett nevet használta, a **sfdg \<resourceGroupName\>** a Storage-fiók. Ezt a **applicationDiagnosticsStorageAccountName** használt érték ellenőrzésével is ellenőrizheti a fürt üzembe helyezéséhez használt Azure Resource Manager sablonnal. Ha a név nem jelenik meg, görgessen lefelé, és válassza a **továbbiak betöltés** lehetőséget. Válassza ki a Storage-fiók nevét.

5. Adja meg az adattípust. Állítsa be **Service Fabric eseményekre**.

6. Győződjön meg arról, hogy a forrás automatikusan **WADServiceFabric \* EventTable** van beállítva.

7. A munkaterület naplóihoz való kapcsolódáshoz kattintson **az OK gombra** .

    ![Storage-fiókok naplóinak hozzáadása Azure Monitor naplókhoz](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

A fiók most már megjelenik a Storage-fiók naplóinak részeként a munkaterület adatforrásaiban.

Hozzáadta a Service Fabric Analytics megoldást egy olyan Log Analytics munkaterületen, amely mostantól helyesen csatlakozik a fürt platform-és alkalmazás-naplózási táblájához. A munkaterülethez hasonló módon adhat hozzá további forrásokat.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Azure Monitor naplók üzembe helyezése Azure Resource Manager

Amikor Resource Manager-sablonnal telepít egy fürtöt, a sablon új Log Analytics munkaterületet hoz létre, hozzáadja a Service Fabric megoldást a munkaterülethez, és úgy konfigurálja, hogy a megfelelő tárolási táblákból olvassa be az adatok olvasását.

[Ezt a sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) használhatja és módosíthatja a követelmények teljesítéséhez. Ez a sablon a következő műveleteket végzi el

* Létrehoz egy 5 csomópontos Service Fabric fürtöt
* Létrehoz egy Log Analytics munkaterületet és Service Fabric megoldást
* Úgy konfigurálja a Log Analytics ügynököt, hogy 2 minta teljesítményszámlálókat gyűjtsön és küldjön a munkaterületre
* A WAD beállítása a Service Fabric gyűjtésére és az Azure Storage-táblákba való küldésére (WADServiceFabric * EventTable)
* A Log Analytics munkaterület konfigurálása a táblák eseményeinek olvasásához


A sablont üzembe helyezheti erőforrás-kezelőként a fürtön a `New-AzResourceGroupDeployment` Azure PowerShell modul API használatával. Példa a következő parancsra:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager észleli, hogy ez a parancs egy meglévő erőforrás frissítése. Csak a meglévő telepítést és a megadott új sablont a sablon közötti változásokat dolgozza fel.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Azure Monitor naplók üzembe helyezése Azure PowerShell

A log Analytics-erőforrást a PowerShellen keresztül is üzembe helyezheti a `New-AzOperationalInsightsWorkspace` parancs használatával. Ha ezt a módszert szeretné használni, ellenőrizze, hogy telepítve van-e a [Azure PowerShell](/powershell/azure/install-az-ps). Ezzel a parancsfájllal új Log Analytics munkaterületet hozhat létre, és hozzáadhatja a Service Fabric megoldást: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Ha elkészült, kövesse az előző szakasz lépéseit Azure Monitor naplók a megfelelő Storage-fiókhoz való összekapcsolásához.

Emellett további megoldásokat is hozzáadhat, vagy egyéb módosításokat végezhet a Log Analytics munkaterületen a PowerShell használatával. További információ: [Azure monitor naplók kezelése a PowerShell használatával](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Következő lépések
* [A log Analytics-ügynök üzembe helyezése](service-fabric-diagnostics-oms-agent.md) a csomópontokon a teljesítményszámlálók összegyűjtéséhez, valamint a tárolók Docker-statisztikáinak és naplóinak összegyűjtéséhez
* Ismerkedjen meg az Azure Monitor naplók részeként kínált [naplóbeli keresési és lekérdezési](../azure-monitor/logs/log-query-overview.md) funkciókkal
* [Egyéni nézetek létrehozása Azure Monitor naplókban a Tervező nézet használatával](../azure-monitor/visualize/view-designer.md)
