---
title: Azure arc-kompatibilis Kubernetes-fürtök figyelése
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Az Azure arc-kompatibilis Kubernetes-fürtök metrikáinak és naplóinak összegyűjtése a Azure Monitor használatával
ms.openlocfilehash: 0a983f6d7032310d02d35e713482de942bfbfd70
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443850"
---
# <a name="azure-monitor-container-insights-for-azure-arc-enabled-kubernetes-clusters"></a>Az Azure arc-kompatibilis Kubernetes-fürtökre Azure Monitor tároló-felismerések

[Azure monitor a tárolók bepillantást nyerhetnek](container-insights-overview.md) az Azure arc-kompatibilis Kubernetes-fürtök széles körű monitorozási felületével.

[!INCLUDE [preview features note](../../azure-arc/kubernetes/includes/preview/preview-callout.md)]

## <a name="supported-configurations"></a>Támogatott konfigurációk

- Azure Monitor tároló-elemzések támogatják az Azure arc-kompatibilis Kubernetes (előzetes verzió) figyelését az [Áttekintés](container-insights-overview.md) című cikkben leírtak szerint, kivéve az élő adatmegjelenítés funkciót. Emellett a felhasználóknak nem kell [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) engedélyekkel rendelkezniük a [metrikák engedélyezéséhez](container-insights-update-metrics.md)
- `Docker`, és az `Moby` ICC-kompatibilis tároló-futtatókörnyezetek, például a `CRI-O` és a `containerd` .
- A kimenő proxy hitelesítés nélkül és a kimenő proxy egyszerű hitelesítéssel való használata támogatott. A megbízható tanúsítványokat elvárt kimenő proxy jelenleg nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

- Ön teljesítette az [általános fürtszolgáltatások dokumentációjában](../../azure-arc/kubernetes/extensions.md#prerequisites)felsorolt előfeltételeket.
- Egy Log Analytics munkaterület: Azure Monitor a tárolók Log Analytics munkaterületet támogatnak az Azure-termékek régiók szerint [lapján](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiókban. A saját munkaterületét [Azure Resource Manager](../logs/resource-manager-workspace.md), [PowerShell](../logs/powershell-sample-create-workspace.md)vagy [Azure Portal](../logs/quick-create-workspace.md)használatával hozhatja létre.
- Az Azure arc-kompatibilis Kubernetes-erőforrást tartalmazó Azure-előfizetéshez [közreműködői](../../role-based-access-control/built-in-roles.md#contributor) szerepkör-hozzárendelés szükséges. Ha a Log Analytics munkaterület egy másik előfizetésben található, akkor a Log Analytics munkaterületen [log Analytics közreműködői](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkör-hozzárendelésre van szükség.
- A figyelési adat megtekintéséhez [log Analytics olvasó](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkör-hozzárendelés szükséges a log Analytics munkaterületen.
- A következő végpontokat engedélyezni kell a kimenő hozzáféréshez azon kívül, hogy a [Kubernetes-fürt csatlakoztatása az Azure arc-hoz](../../azure-arc/kubernetes/quickstart-connect-cluster.md#meet-network-requirements)is megemlíti.

    | Végpont | Port |
    |----------|------|
    | `*.ods.opinsights.azure.com` | 443 |
    | `*.oms.opinsights.azure.com` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    | `*.monitoring.azure.com` | 443 |
    | `login.microsoftonline.com` | 443 |

    Ha az ív használatára képes Kubernetes-erőforrás az Azure USA kormányzati környezetében van, akkor a következő végpontokat engedélyezni kell a kimenő hozzáféréshez:

    | Végpont | Port |
    |----------|------|
    | `*.ods.opinsights.azure.us` | 443 |
    | `*.oms.opinsights.azure.us` | 443 |
    | `dc.services.visualstudio.com` | 443 |
    

- Ha korábban már telepített Azure Monitor tároló-információkat a fürtön a fürt bővítményei nélküli parancsfájl használatával, kövesse az [itt](container-insights-optout-hybrid.md) felsorolt utasításokat a Helm-diagram törléséhez. Ezután továbbra is létrehozhat egy fürt-kiterjesztési példányt Azure Monitor tárolók bepillantást.

    >[!NOTE]
    > A üzembe helyezési Azure Monitor tároló-bevezetések (előzetes verzió) parancsfájl-alapú verzióját a rendszer a [fürt kiterjesztési](../../azure-arc/kubernetes/extensions.md) formáját váltja fel. A korábban a szkripten keresztül üzembe helyezett Azure Monitor csak a 2021-ig támogatott, ezért azt javasoljuk, hogy a lehető leghamarabb telepítse a központi telepítés a fürt kiterjesztésére szolgáló formáját.

### <a name="identify-workspace-resource-id"></a>Munkaterület erőforrás-AZONOSÍTÓjának azonosítása

Futtassa az alábbi parancsokat a Log Analytics munkaterület teljes Azure Resource Managerének megkereséséhez. 

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

2. Váltson a Log Analytics munkaterületet üzemeltető előfizetésre a következő paranccsal:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Az alábbi példa az előfizetésekben lévő munkaterületek listáját jeleníti meg az alapértelmezett JSON-formátumban.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a kívánt munkaterület nevét. A `id` mező, amely a log Analytics munkaterület Azure Resource Manager azonosítóját jelöli.

    >[!TIP]
    > Ez a `id` log Analytics munkaterület *Áttekintés* paneljén is megtalálható a Azure Portal használatával.

## <a name="create-extension-instance-using-azure-cli"></a>Bővítmény-példány létrehozása az Azure CLI-vel

### <a name="option-1---with-default-values"></a>1. lehetőség – alapértelmezett értékekkel

Ez a beállítás a következő alapértékeket használja:

- A fürt régiójának megfelelő, meglévő alapértelmezett log Analytics-munkaterület létrehozása vagy használata
- Az automatikus frissítés engedélyezve van a Azure Monitor-fürt bővítményéhez

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers
```

### <a name="option-2---with-existing-azure-log-analytics-workspace"></a>2. lehetőség – meglévő Azure Log Analytics-munkaterülettel

Használhat egy meglévő Azure Log Analytics-munkaterületet bármely olyan előfizetésben, amelyhez *közreműködő* vagy egy több-egy engedékenyebb szerepkör-hozzárendelés van.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings logAnalyticsWorkspaceResourceID=<armResourceIdOfExistingWorkspace>
```

### <a name="option-3---with-advanced-configuration"></a>3. lehetőség – speciális konfigurációval

Ha az alapértelmezett erőforrás-kérelmeket és-korlátozásokat szeretné felhangolni, használja a speciális konfigurációk beállításait:

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings  omsagent.resources.daemonset.limits.cpu=150m omsagent.resources.daemonset.limits.memory=600Mi omsagent.resources.deployment.limits.cpu=1 omsagent.resources.deployment.limits.memory=750Mi
```

Az elérhető konfigurációs beállításokért vegye [fel a Helm diagram erőforrás-kérelmek és korlátok című szakaszát](https://github.com/helm/charts/blob/master/incubator/azuremonitor-containers/values.yaml) .

### <a name="option-4---on-azure-stack-edge"></a>4. lehetőség – Azure Stack szélén

Ha az Azure arc-kompatibilis Kubernetes-fürt Azure Stack szélén van, akkor az egyéni csatlakoztatási útvonalat `/home/data/docker` kell használni.

```console
az k8s-extension create --name azuremonitor-containers --cluster-name <cluster-name> --resource-group <resource-group> --cluster-type connectedClusters --extension-type Microsoft.AzureMonitor.Containers --configuration-settings omsagent.logsettings.custommountpath=/home/data/docker
```

>[!NOTE]
> Ha explicit módon megadja a bővítménynek a Create parancsban telepítendő verzióját, akkor győződjön meg arról, hogy a megadott verzió >= 2.8.2.

## <a name="create-extension-instance-using-azure-portal"></a>Bővítmény-példány létrehozása Azure Portal használatával

>[!IMPORTANT]
>  Ha Azure Stack Edge felett futó Kubernetes-fürtön helyez üzembe Azure Monitor, akkor az Azure CLI-t az Azure Portal beállítás helyett az egyéni csatlakoztatási útvonal beállításával kell megadnia a fürtökhöz.    

### <a name="onboarding-from-the-azure-arc-enabled-kubernetes-resource-blade"></a>Előkészítés az Azure arc-kompatibilis Kubernetes-erőforrás panelről

1. A Azure Portal válassza ki a figyelni kívánt arc Kubernetes-fürtöt.

2. Az erőforrás panel "Figyelés" szakaszában válassza az "elemzése (előzetes verzió)" elemet.

3. Az előkészítés lapon válassza a "Azure Monitor konfigurálása" gombot.

4. Most kiválaszthatja a [log Analytics munkaterületet](../logs/quick-create-workspace.md) , amellyel elküldheti a metrikákat, és naplózhatja az adatokat.

5. A configure (Konfigurálás) gombra kattintva telepítheti az Azure Monitor Container bepillantást a fürt bővítményét.

### <a name="onboarding-from-azure-monitor-blade"></a>Előkészítés Azure Monitor panelről

1. A Azure Portal navigáljon a "Figyelés" panelre, és válassza a "bepillantások" menü "tárolók" lehetőségét.

2. A "nem figyelt fürtök" lapon megtekintheti az Azure arc-kompatibilis Kubernetes-fürtöket, amelyeken engedélyezheti a figyelést.

3. Kattintson az "engedélyezés" hivatkozásra azon fürt mellett, amelyen engedélyezni kívánja a figyelést.

4. A folytatáshoz válassza a Log Analytics munkaterületet, és kattintson a configure (Konfigurálás) gombra.

## <a name="create-extension-instance-using-azure-resource-manager"></a>Bővítmény-példány létrehozása Azure Resource Manager használatával

1. Azure Resource Manager sablon és paraméter letöltése:

    ```console
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template -o arc-k8s-azmon-extension-arm-template.json
    curl -L https://aka.ms/arc-k8s-azmon-extension-arm-template-params -o  arc-k8s-azmon-extension-arm-template-params.json
    ```

2. A paraméter értékének frissítése a fájlban arc-k8s-azmon-extension-arm-template-params.js. Az Azure nyilvános felhő esetében `opinsights.azure.com` a workspaceDomain értékeként kell használni.

3. A sablon üzembe helyezése Azure Monitor Container-adatáttekintési bővítmény létrehozásához 

    ```console
    az login
    az account set --subscription "Subscription Name"
    az deployment group create --resource-group <resource-group> --template-file ./arc-k8s-azmon-extension-arm-template.json --parameters @./arc-k8s-azmon-extension-arm-template-params.json
    ```

## <a name="delete-extension-instance"></a>Bővítmény példányának törlése

A következő parancs csak a bővítmény példányát törli, de nem törli a Log Analytics munkaterületet. A Log Analytics erőforrásban lévő adatforrások érintetlenek maradnak.

```bash
az k8s-extension delete --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group>
```

## <a name="disconnected-cluster"></a>Leválasztott fürt
Ha a fürt leválasztása az Azure-ból > 48 órán keresztül megszakadt, akkor az Azure Resource Graph nem tartalmaz információt a fürtről. Ennek eredményeképpen előfordulhat, hogy az eredmények panel helytelen adatokat jelenít meg a fürt állapotáról.

## <a name="next-steps"></a>Következő lépések

- Ha a figyelés engedélyezve van az arc-kompatibilis Kubernetes-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-felhasználásának összegyűjtéséhez, Ismerje meg, [hogyan használhatók](container-insights-analyze.md) a Container-információk.

- Alapértelmezés szerint a tároló ügynök gyűjti az összes névtérben futó összes tároló StdOut/stderr-tárolójának naplóit, kivéve a Kube rendszert. Ha az adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjteményt szeretne konfigurálni, tekintse át a Container-elemzések [ügynökének konfigurációját](container-insights-agent-config.md) a kívánt adatgyűjtési beállítások ConfigMap-konfigurációs fájlra való konfigurálásához.

- A Prometheus-metrikák a fürtből való beolvasásához és elemzéséhez tekintse át a [Prometheus-metrikák leselejtezésének konfigurálása](container-insights-prometheus-integration.md)
