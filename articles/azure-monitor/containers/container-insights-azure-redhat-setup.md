---
title: Az Azure Red Hat OpenShift v3. x konfigurálása a Container bepillantást tartalmazó szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a Kubernetes-fürtök figyelését az Azure Red Hat OpenShift 3-as vagy újabb verziójában üzemeltetett Azure Monitor használatával.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: b46dfda0bdb0f3b582aa751786187a4d74524f75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708373"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>Az Azure Red Hat OpenShift v3 konfigurálása a Container bepillantást

>[!IMPORTANT]
> Az Azure Red Hat OpenShift 3,11 június 2022-én megszűnik.
>
> Október 2020-én már nem fog tudni új 3,11-fürtöket létrehozni.
> A meglévő 3,11-es fürtök továbbra is a 2022-ig fognak működni, de ezt követően már nem lesznek támogatottak.
>
> Kövesse ezt az útmutatót [egy Azure Red Hat OpenShift 4-fürt létrehozásához](../../openshift/tutorial-create-cluster.md).
> Ha konkrét kérdései vannak, vegye [fel velünk a kapcsolatot](mailto:aro-feedback@microsoft.com).

A Container-eredmények széles körű monitorozást biztosítanak az Azure Kubernetes szolgáltatás (ak) és az AK-os motor fürtök számára. Ez a cikk azt ismerteti, hogyan engedélyezhető a Kubernetes-fürtök figyelése az [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3. verziójában és a 3. verzió legújabb támogatott verzióján, hogy hasonló figyelési élményt lehessen elérni.

>[!NOTE]
>Az Azure Red Hat OpenShift támogatása jelenleg nyilvános előzetes verzióban érhető el.
>

A Container bepillantást a következő támogatott módszerek használatával engedélyezheti az új, vagy egy vagy több Azure Red Hat-OpenShift üzemelő példányhoz:

- Egy meglévő fürt Azure Portal vagy Azure Resource Manager sablon használatával.
- Új fürt Azure Resource Manager sablon használatával vagy új fürt létrehozásakor az [Azure CLI](/cli/azure/openshift#az-openshift-create)használatával.

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A Container-megállapítások támogatják az Azure Red Hat OpenShift figyelését az [Áttekintés](container-insights-overview.md) című cikkben leírtak szerint, a következő funkciók kivételével:

- Élő adatértékek (előzetes verzió)
- A fürtcsomópontok és a hüvelyek [metrikáinak összegyűjtése](container-insights-update-metrics.md) és tárolása a Azure monitor metrikai adatbázisban

## <a name="prerequisites"></a>Előfeltételek

- Egy [Log Analytics-munkaterület](../logs/design-logs-deployment.md).

    A Container-bepillantást az Azure [Products By Region régiójában](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiók log Analytics munkaterületét támogatják. Saját munkaterület létrehozásához [Azure Resource Manager](../logs/resource-manager-workspace.md), a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../logs/quick-create-workspace.md)használatával hozható létre.

- Az Azure-előfizetésben szereplő funkciók engedélyezéséhez és eléréséhez legalább az Azure *közreműködő* szerepkör tagjának kell lennie az Azure-előfizetésben, valamint a Log Analytics munkaterület [*log Analytics közreműködő*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörének tagja.

- A figyelési adat megtekintéséhez Ön a [*log Analytics olvasói*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkör tagja, és a log Analytics munkaterületet tároló-elemzésekkel konfigurálta.

## <a name="identify-your-log-analytics-workspace-id"></a>A Log Analytics munkaterület AZONOSÍTÓjának azonosítása

 Meglévő Log Analytics-munkaterülettel való integrációhoz először azonosítsa a Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját. A (z) paraméterhez szükséges a munkaterület erőforrás-azonosítója, `workspaceResourceId` Ha a Azure Resource Manager sablon módszerének használatával engedélyezi a figyelést.

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

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Új fürt engedélyezése Azure Resource Manager sablon használatával

Az alábbi lépések végrehajtásával telepítsen egy Azure Red Hat OpenShift-fürtöt a figyelés engedélyezve lehetőséggel. A továbblépés előtt tekintse át az oktatóanyag [Azure Red Hat OpenShift-fürt létrehozásával](../../openshift/tutorial-create-cluster.md) foglalkozó témakört, hogy megtudja, milyen függőségek szükségesek a környezet beállításához.

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza azt a konfigurációt, amellyel a fürtöt a figyelés engedélyezve állapotba helyezi, a másik pedig a konfigurált paramétereket adja meg a következők megadásához:

- Az Azure Red Hat OpenShift-fürt erőforrás-azonosítója.

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- [Azure Active Directory a bérlő azonosítóját](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) , miután elvégezte a létrehozásához szükséges lépéseket.

- [Azure Active Directory ÜGYFÉLALKALMAZÁS azonosítóját](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) , miután elvégezte a létrehozásához szükséges lépéseket.

- [Azure Active Directory az ügyfél titkos kulcsát](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) , miután elvégezte a létrehozásához szükséges lépéseket.

- Az [Azure ad biztonsági csoport](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) a létrehozásuk lépéseinek elvégzése után megjegyezte, hogy egy vagy több már létrejött.

- Meglévő Log Analytics munkaterület erőforrás-azonosítója. Az információk beszerzéséhez tekintse meg [az log Analytics munkaterület azonosítójának azonosítását](#identify-your-log-analytics-workspace-id) ismertető témakört.

- A fürtben létrehozandó főcsomópontok száma.

- Az ügynök-készlet profiljában lévő számítási csomópontok száma.

- Az ügynök-készlet profiljában található infrastruktúra-csomópontok száma.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

1. Töltse le és mentse a fájlt egy helyi mappába, a Azure Resource Manager sablonra és a paraméterre, és hozzon létre egy olyan fürtöt a figyelési bővítménnyel, amely a következő parancsokat használja:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login
    ```

    Ha több előfizetéshez is rendelkezik hozzáféréssel, `az account set -s {subscription ID}` futtassa `{subscription ID}` a cserét a használni kívánt előfizetéssel.

3. Hozzon létre egy erőforráscsoportot a fürthöz, ha még nem rendelkezik ilyennel. Az Azure-OpenShift támogató Azure-régiók listáját a [támogatott régiók](../../openshift/supported-resources.md#azure-regions)című részben tekintheti meg.

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Szerkessze **newClusterWithMonitoringParam.jsa** JSON-paramétert, és frissítse a következő értékeket:

    - *helyen*
    - *clusterName*
    - *aadTenantId*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. A következő lépés az Azure CLI használatával helyezi üzembe a fürtöt a figyeléssel.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A kimenet a következőhöz hasonló:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Meglévő fürt engedélyezése

A következő lépések végrehajtásával engedélyezheti az Azure-ban üzembe helyezett Azure Red Hat OpenShift-fürtök figyelését. Ezt a Azure Portal vagy a megadott sablonok használatával végezheti el.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal menüben vagy a Kezdőlap lapon válassza a **Azure monitor** lehetőséget. Az **áttekintések** szakaszban válassza a **tárolók** lehetőséget.

3. A **figyelő-tárolók** lapon válassza a **nem figyelt fürtök** lehetőséget.

4. A nem figyelt fürtök listájából keresse meg a fürtöt a listában, és kattintson az **Engedélyezés** gombra. A listában szereplő eredmények azonosításához keresse meg az **ARO** értéket a **fürt típusa** oszlopban.

5. Ha már rendelkezik egy meglévő Log Analytics munkaterülettel ugyanabban az előfizetésben, mint a fürt, a bevezetéskor a **Container** bevezetése lapon válassza ki azt a legördülő listából.  
    A lista előjelöli az alapértelmezett munkaterületet és helyet, amelyet a fürt az előfizetésben üzembe helyez.

    ![Nem figyelt fürtök figyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse az [log Analytics munkaterület létrehozása](../logs/quick-create-workspace.md)című témakör utasításait. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyben a RedHat OpenShift-fürtöt telepítette.

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager sablon használatával

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza a figyelés engedélyezésének konfigurációját, a másik pedig a konfigurált paramétereket az alábbiak megadásához:

- Az Azure RedHat OpenShift-fürt erőforrás-azonosítója.

- Az az erőforráscsoport, amelyben a fürt telepítve van.

- Egy Log Analytics-munkaterület. Az információk beszerzéséhez tekintse meg [az log Analytics munkaterület azonosítójának azonosítását](#identify-your-log-analytics-workspace-id) ismertető témakört.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

1. Töltse le a sablon és a paraméter fájlját, hogy a következő parancsokkal frissítse a fürtöt a figyelési bővítmény használatával:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login
    ```

    Ha több előfizetéshez is rendelkezik hozzáféréssel, `az account set -s {subscription ID}` futtassa `{subscription ID}` a cserét a használni kívánt előfizetéssel.

3. Az Azure RedHat OpenShift-fürt előfizetésének meghatározása.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Futtassa a következő parancsot a fürt helyének és erőforrás-AZONOSÍTÓjának azonosításához:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Szerkessze **existingClusterParam.jsa** JSON-paramétert, és frissítse a *AroResourceId* és a *aroResourceLocation* értékeket. A **workspaceResourceId** értéke a log Analytics munkaterület teljes erőforrás-azonosítója, amely tartalmazza a munkaterület nevét.

6. Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A kimenet a következőhöz hasonló:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Következő lépések

- Ha a figyelés engedélyezve van a RedHat OpenShift-fürt és a rajtuk futó munkaterhelések állapotának és erőforrás-felhasználásának összegyűjtéséhez, Ismerje meg, [hogyan használhatja](container-insights-analyze.md) a Container-információkat.

- Alapértelmezés szerint a tároló ügynök gyűjti az összes névtérben futó összes tároló StdOut/stderr-tárolójának naplóit, kivéve a Kube rendszert. Ha az adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjteményt szeretne konfigurálni, tekintse át a Container-elemzések [ügynökének konfigurációját](container-insights-agent-config.md) a kívánt adatgyűjtési beállítások ConfigMap-konfigurációs fájlra való konfigurálásához.

- A Prometheus-metrikák a fürtből való beolvasásához és elemzéséhez tekintse át a [Prometheus-metrikák leselejtezésének konfigurálása](container-insights-prometheus-integration.md)

- Ha szeretné megtudni, hogyan állíthatja le a fürt figyelését a tároló-információk alapján, tekintse meg az [Azure Red Hat OpenShift-fürt figyelésének leállítása](./container-insights-optout-openshift-v3.md)című témakört.