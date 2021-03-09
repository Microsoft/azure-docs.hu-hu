---
title: Az Azure Red Hat OpenShift v4. x konfigurálása a Container bepillantást tartalmazó szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürtök figyelését az Azure Red Hat OpenShift 4-es vagy újabb verziójában üzemeltetett Azure Monitor.
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 02cb794463b965ebafef0b6861477dbf69227511
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506412"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-container-insights"></a>Az Azure Red Hat OpenShift v4. x konfigurálása a Container bepillantást tartalmazó szolgáltatással

A Container bepillantást nyerhet az Azure Kubernetes Service (ak) és az KABAi motor fürtök széles körű figyelési felületét. Ez a cikk azt ismerteti, hogyan lehet hasonló figyelési élményt elérni az [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 4. x verziójában üzemeltetett Kubernetes-fürtök figyelésének engedélyezésével.

>[!NOTE]
>Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
>

A jelen cikkben ismertetett támogatott módszerek használatával engedélyezheti a tárolók bepillantást az Azure Red Hat OpenShift v4. x-es verziójának egy vagy több példányára.

Meglévő fürt esetén futtassa ezt a [bash-szkriptet az Azure CLI-ben](/cli/azure/openshift#az-openshift-create&preserve-view=true).

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A Container-megállapítások az Azure Red Hat OpenShift v4. x figyelését támogatják a [Container-információk áttekintése](container-insights-overview.md)című témakörben leírtak szerint, a következő funkciók kivételével:

- Élő adatértékek (előzetes verzió)
- A fürtcsomópontok és a hüvelyek [metrikáinak összegyűjtése](container-insights-update-metrics.md) és tárolása a Azure monitor metrikai adatbázisban

## <a name="prerequisites"></a>Előfeltételek

- Az Azure CLI verziója 2.0.72 vagy újabb verzió  

- A [Helm 3](https://helm.sh/docs/intro/install/) CLI-eszköz

- [Bash 4-es verziója](https://www.gnu.org/software/bash/)

- A [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) parancssori eszköz

- Egy [Log Analytics-munkaterület](../logs/design-logs-deployment.md).

    A Container-bepillantást az Azure [Products By Region régiójában](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiók log Analytics munkaterületét támogatják. Saját munkaterület létrehozásához [Azure Resource Manager](../logs/resource-manager-workspace.md), a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../logs/quick-create-workspace.md)használatával hozható létre.

- A tároló-betekintő funkcióinak engedélyezéséhez és eléréséhez legalább egy Azure- *közreműködő* szerepkörrel kell rendelkeznie az Azure-előfizetésben, valamint egy [*log Analytics közreműködő*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkört a log Analytics munkaterületen, amely a tároló-felismerésekkel van konfigurálva.

- A figyelési adat megtekintéséhez [*log Analytics olvasó*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörrel kell rendelkeznie a log Analytics munkaterületen, amely a Container-elemzésekkel van konfigurálva.

## <a name="enable-monitoring-for-an-existing-cluster"></a>Meglévő fürt figyelésének engedélyezése

Ha engedélyezni szeretné az Azure-ban a megadott bash-parancsfájllal üzembe helyezett Azure Red Hat OpenShift 4-es vagy újabb verziójú fürt figyelését, tegye a következőket:

1. Jelentkezzen be az Azure-ba az alábbi parancs futtatásával:

    ```azurecli
    az login
    ```

1. Töltse le és mentse a helyi mappába a parancsfájlt, amely a következő parancs futtatásával konfigurálja a fürtöt a figyelési bővítménnyel:

    `curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script`

1. Kapcsolódjon az ARO v4-fürthöz az [oktatóanyag: Kapcsolódás Azure Red Hat OpenShift 4-fürthöz](../../openshift/tutorial-connect-cluster.md)című témakör útmutatásai alapján.


### <a name="integrate-with-an-existing-workspace"></a>Integrálás meglévő munkaterülettel

Ebben a szakaszban engedélyezheti a fürt figyelését a korábban letöltött bash-szkript használatával. Egy meglévő Log Analytics munkaterülettel való integrációhoz először adja meg a paraméterhez szükséges Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját `logAnalyticsWorkspaceResourceId` , majd futtassa a parancsot, hogy engedélyezze a figyelési bővítményt a megadott munkaterületen.

Ha nem rendelkezik a megadható munkaterülettel, ugorjon az [integrálás az alapértelmezett munkaterülettel](#integrate-with-the-default-workspace) szakaszra, és hagyja, hogy a szkript létrehoz egy új munkaterületet.

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs futtatásával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőhöz hasonlóan fog kinézni:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Másolja a **SubscriptionId** értékét.

1. Váltson arra az előfizetésre, amely a Log Analytics munkaterületet üzemelteti, a következő parancs futtatásával:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. A következő parancs futtatásával jelenítse meg az előfizetésekben lévő munkaterületek listáját az alapértelmezett JSON-formátumban:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját a mező **azonosítója** alá.

1. A figyelés engedélyezéséhez futtassa a következő parancsot. Cserélje le a és a `azureAroV4ClusterResourceId` paraméterek értékeit `logAnalyticsWorkspaceResourceId` .

    ```bash
    export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>" 
    ```

    Az alábbi parancsot kell futtatnia, ha a 3 változót az exportálási parancsokkal tölti fel:

    `bash enable-monitoring.sh --resource-id $azureAroV4ClusterResourceId --workspace-id $logAnalyticsWorkspaceResourceId`

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot-mérőszámokat.

### <a name="integrate-with-the-default-workspace"></a>Integrálás az alapértelmezett munkaterülettel

Ebben a szakaszban a letöltött bash-szkript használatával engedélyezheti az Azure Red Hat OpenShift v4. x fürt figyelését.

Ebben a példában nem kell előzetesen létrehoznia vagy megadnia egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot azáltal, hogy létrehoz egy alapértelmezett munkaterületet a fürt előfizetésének alapértelmezett erőforrás-csoportjában, ha még nem létezik a régióban.

A létrehozott alapértelmezett munkaterület a *alapértelmezettmunkaterület \<GUID> - \<Region>* formátuma.  

Cserélje le a paraméter értékét `azureAroV4ClusterResourceId` .

```bash
export azureAroV4ClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/<clusterName>"
```

Például:

' bash enable-monitoring.sh--Resource-id $azureAroV 4ClusterResourceId 

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="enable-monitoring-from-the-azure-portal"></a>A Azure Portal figyelésének engedélyezése

A tároló-felismerések többfürtes nézete kiemeli az Azure Red Hat OpenShift-fürtöket, amelyek nem rendelkeznek figyeléssel a nem **figyelt fürtök** lapon. A fürt melletti **Engedélyezés** lehetőség nem kezdeményezi a figyelés bevezetését a portálról. A rendszer átirányítja erre a cikkre, hogy manuálisan engedélyezze a figyelést a jelen cikk korábbi részében ismertetett lépések követésével.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali ablaktáblán vagy a kezdőlapon válassza a **Azure monitor** lehetőséget.

1. Az **áttekintések** szakaszban válassza a **tárolók** lehetőséget.

1. A **figyelő-tárolók** lapon válassza a nem **figyelt fürtök** lehetőséget.

1. A nem figyelt fürtök listájában jelölje ki a fürtöt, majd válassza az **Engedélyezés** lehetőséget.

    A lista eredményeit a **fürt típusa** oszlopban található **ARO** érték megkeresésével azonosíthatja. Miután kiválasztotta az **Engedélyezés** lehetőséget, a rendszer átirányítja erre a cikkre.

## <a name="next-steps"></a>Következő lépések

- Most, hogy engedélyezte a figyelést, hogy összegyűjtse a RedHat OpenShift 4. x-es verziójának és a rajtuk futó munkaterheléseknek az állapotát és erőforrás-kihasználtságát, Ismerje meg, [hogyan használhatja](container-insights-analyze.md) a Container-információkat.

- Alapértelmezés szerint a tároló ügynök összegyűjti az *StdOut* és a *stderr* tároló naplóit az összes olyan tárolóból, amely az összes névtérben fut, kivéve a Kube rendszert. Egy adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjtemény konfigurálásához tekintse át a tároló-elemzési [ügynök konfigurációját](container-insights-agent-config.md) a *ConfigMap* -konfigurációs fájlhoz használni kívánt adatgyűjtési beállítások konfigurálásához.

- A Prometheus-metrikák a fürtből való leselejtezéséhez és elemzéséhez tekintse át a [Prometheus-metrikák leselejtezésének konfigurálása](container-insights-prometheus-integration.md)című ismertetőt.

- Ha szeretné megtudni, hogyan állíthatja le a fürt figyelését a tároló-információk használatával, tekintse meg az [Azure Red Hat OpenShift-fürt figyelésének leállítása](./container-insights-optout-openshift-v3.md)című témakört.
