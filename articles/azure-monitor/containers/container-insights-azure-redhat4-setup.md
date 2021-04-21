---
title: A Azure Red Hat OpenShift v4.x konfigurálása a Container Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürt monitorozását olyan Azure Monitor, amely a 4-es vagy újabb Azure Red Hat OpenShift üzemel.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 11c702d1f46725a12e90a01dc1b38467344a1123
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784640"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Az Azure Red Hat OpenShift v4.x konfigurálása a Container Insights segítségével

A Container Insights gazdag monitorozási élményt biztosít a Azure Kubernetes Service (AKS) és az AKS-motorfürtök számára. Ez a cikk azt ismerteti, hogyan lehet hasonló monitorozási élményt elérni [](../../openshift/intro-openshift.md) a 4.x-es Azure Red Hat OpenShift üzemeltetett Kubernetes-fürtök monitorozásának engedélyezésével.

>[!NOTE]
>A Azure Red Hat OpenShift támogatása jelenleg a nyilvános előzetes verzió egyik funkciója.
>

A Container Insights a Azure Red Hat OpenShift v4.x egy vagy több meglévő üzemelő példányán engedélyezhető az ebben a cikkben ismertetett támogatott módszerekkel.

Meglévő fürt esetén futtassa ezt a [Bash-szkriptet az Azure CLI-ban.](/cli/azure/openshift#az_openshift_create&preserve-view=true)

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A Container Insights támogatja Azure Red Hat OpenShift v4.x-es virtuális gépek monitorozását a [Container Insights](container-insights-overview.md)áttekintésében leírtak szerint, a következő funkciók kivételével:

- Élő adatok (előzetes verzió)
- [Metrikák gyűjtése a](container-insights-update-metrics.md) fürtcsomópontokból és podokból, és azok tárolása a Azure Monitor metrikák adatbázisában

## <a name="prerequisites"></a>Előfeltételek

- Az Azure CLI 2.0.72-es vagy újabb verziója  

- A [Helm 3](https://helm.sh/docs/intro/install/) CLI eszköz

- Az [OpenShift CLI legújabb verziója](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)

- [Bash 4-es verzió](https://www.gnu.org/software/bash/)

- A [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) parancssori eszköz

- Egy [Log Analytics-munkaterület](../logs/design-logs-deployment.md).

    A Container Insights támogatja a Log Analytics-munkaterületet az [Azure-termékek](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)régiók szerint területen felsorolt régiókban. Saját munkaterület létrehozásához létrehozható a következő [Azure Resource Manager,](../logs/resource-manager-workspace.md)a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)használatával, vagy a [Azure Portal.](../logs/quick-create-workspace.md)

- A Container Insights funkcióinak engedélyezéséhez és eléréséhez legalább  Azure-közreműködői szerepkörre van szükség az Azure-előfizetésben, és log [*analytics-közreműködői*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörre a Log Analytics-munkaterületen, a Container Insights használatával konfigurálva.

- A monitorozási adatok megtekintéséhez [*Log Analytics-olvasó*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörre van szükség a Container Insights szolgáltatással konfigurált Log Analytics-munkaterületen.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Meglévő fürt monitorozásának engedélyezése

Ha engedélyezni szeretné az Azure Azure Red Hat OpenShift ban a megadott Bash-szkripttel üzembe helyezett 4-es vagy újabb verziójú fürt monitorozását, tegye a következőket:

1. Jelentkezzen be az Azure-ba az alábbi parancs futtatásával:

    ```azurecli
    az login
    ```

1. Töltse le és mentse egy helyi mappába azt a szkriptet, amely a figyelési bővítményt konfigurálja a fürtön a következő parancs futtatásával:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Csatlakozzon az ARO v4-fürthöz az Oktatóanyag: Csatlakozás Azure Red Hat OpenShift [4 fürthöz.](../../openshift/tutorial-connect-cluster.md)


### <a name="integrate-with-an-existing-workspace"></a>Integrálás meglévő munkaterülettel

Ebben a szakaszban engedélyezi a fürt monitorozását a korábban letöltött Bash-szkripttel. Meglévő Log Analytics-munkaterülettel való integrációhoz először azonosítsa a Log Analytics-munkaterület paraméterhez szükséges teljes erőforrás-azonosítóját, majd futtassa a parancsot, hogy engedélyezze a figyelési bővítményt a megadott `logAnalyticsWorkspaceResourceId` munkaterületen.

Ha nem tud megadni munkaterületet, ugorjon az [](#integrate-with-the-default-workspace) Integrálás az alapértelmezett munkaterülettel szakaszra, és hagyja, hogy a szkript létrehoz egy új munkaterületet.

1. A következő parancs futtatásával listába sorolja az összes előfizetést, amelyekhez hozzáféréssel rendelkezik:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet az alábbihoz lesz hasonló:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Másolja ki a **SubscriptionId értékét.**

1. Váltson arra az előfizetésre, amely a Log Analytics-munkaterületet futtatja az alábbi parancs futtatásával:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Jelenítse meg az előfizetésében található munkaterületek listáját az alapértelmezett JSON formátumban a következő parancs futtatásával:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics-munkaterület teljes erőforrás-azonosítóját a **mezőazonosító alá.**

1. A monitorozás engedélyezéséhez futtassa a következő parancsot. Cserélje le a és a paraméter `azureAroV4ClusterResourceId` `logAnalyticsWorkspaceResourceId` értékeit.

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    A 3 változó Exportálás parancsokkal való feltöltése után a következő parancsot kell futtatnia:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

A figyelés engedélyezése után a fürt állapotmetrikák megtekintése körülbelül 15 percet is igénybe vehet.

### <a name="integrate-with-the-default-workspace"></a>Integrálás az alapértelmezett munkaterülettel

Ebben a szakaszban engedélyezi a Azure Red Hat OpenShift v4.x-fürt monitorozását a letöltött Bash-szkript használatával.

Ebben a példában nem kell előre létrehoznia vagy megadnia egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot azáltal, hogy létrehoz egy alapértelmezett munkaterületet a fürt-előfizetés alapértelmezett erőforráscsoportján, ha még nem létezik ilyen a régióban.

A létrehozott alapértelmezett munkaterület formátuma *DefaultWorkspace- \<GUID> - \<Region>*.  

Cserélje le a paraméter `azureAroV4ClusterResourceId` értékét.

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Például:

Bash enable-monitoring.sh --resource-id $azureAroV 4ClusterResourceId 

A monitorozás engedélyezése után a fürt állapotmetrikák megtekintése körülbelül 15 percet is igénybe vehet.

### <a name="enable-monitoring-from-the-azure-portal"></a>Monitorozás engedélyezése a Azure Portal

A Container Insights többfürtű nézete kiemeli azokat Azure Red Hat OpenShift fürtökét, amelyeken nincs engedélyezve a figyelés a Nem **figyelt fürtök lapon.** A **fürt** melletti Engedélyezés lehetőség nem kezdeményezi a monitorozás a portálról való beállítását. A rendszer átirányítja erre a cikkre, hogy engedélyezze a manuális monitorozást a cikkben korábban ismertetett lépések követésével.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali panelen vagy a kezdőlapon válassza **a** Azure Monitor.

1. Az Elemzések **szakaszban** válassza a **Tárolók lehetőséget.**

1. A Monitor **– tárolók lapon** válassza a Nem **figyelt fürtök lehetőséget.**

1. A nem figyelt fürtök listájában válassza ki a fürtöt, majd válassza az Engedélyezés **lehetőséget.**

    Az eredmények a listában való azonosításához az **ARO** értéket kell keresnie a Fürt **típusa oszlopban.** Az Engedélyezés **lehetőség kiválasztása** után a rendszer átirányítja erre a cikkre.

## <a name="next-steps"></a>Következő lépések

- Most, hogy engedélyezte a monitorozást a RedHat OpenShift 4.x verziójú fürt és a rajta futó számítási feladatok állapotának és erőforrás-használatának gyűjtéséhez, ismerje meg a [Container](container-insights-analyze.md) Insights használatát.

- Alapértelmezés szerint a tárolóba került ügynök összegyűjti a kube-system kivételével az összes névtérben futó összes tároló *stdout* és *stderr* tárolónaplóit. Ha egy adott névtérre vagy névtérre vonatkozó tárolónapló-gyűjteményt szeretne konfigurálni, tekintse át a [Container Insights-ügynök](container-insights-agent-config.md) konfigurációját, és konfigurálja a *ConfigMap* konfigurációs fájlhoz kívánt adatgyűjtési beállításokat.

- A Prometheus-metrikák fürtből való leselkedéséhez és elemzéséhez tekintse át a [Prometheus-metrikák leselkedő konfigurálásával kapcsolatos ismertetőt.](container-insights-prometheus-integration.md)

- A fürt Container Insights használatával való monitorozásának leállításával kapcsolatos további információkért lásd: A fürt Azure Red Hat OpenShift [monitorozása.](./container-insights-optout-openshift-v3.md)
