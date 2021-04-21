---
title: A Azure Red Hat OpenShift v3.x konfigurálása a Container Insights | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja egy Kubernetes-fürt monitorozását a Azure Monitor 3-as vagy újabb Azure Red Hat OpenShift üzemeltetett fürttel.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a2910655601548f39983547e12460d949901954d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784676"
---
# <a name="configure-azure-red-hat-openshift-v3-with-container-insights"></a>A Azure Red Hat OpenShift v3 konfigurálása a Container Insights segítségével

>[!IMPORTANT]
> Azure Red Hat OpenShift 3.11-es vagy 2022. júniusi kivezetve lesz.
>
> 2020 októberéig nem fog tudni új 3.11-es fürtöt létrehozni.
> A meglévő 3.11-es fürtök 2022 júniusáig továbbra is működni fognak, de a továbbiakban nem lesznek támogatva.
>
> Kövesse ezt az útmutatót [egy 4-es Azure Red Hat OpenShift létrehozásához.](../../openshift/tutorial-create-cluster.md)
> Ha konkrét kérdései vannak, lépjen [kapcsolatba velünk.](mailto:aro-feedback@microsoft.com)

A Container Insights gazdag monitorozási élményt biztosít a Azure Kubernetes Service (AKS) és az AKS Engine-fürtökhöz. Ez a cikk azt ismerteti, hogyan engedélyezheti a [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) 3-as és a 3-as verzió legújabb támogatott verzióján üzemeltetett Kubernetes-fürtök monitorozását hasonló monitorozási élmény elérése érdekében.

>[!NOTE]
>A Azure Red Hat OpenShift jelenleg a nyilvános előzetes verzió egyik funkciója.
>

A tárolóelemzések engedélyezhetők új vagy egy vagy több meglévő üzemelő példányhoz Azure Red Hat OpenShift következő támogatott módszerekkel:

- Meglévő fürt esetén a Azure Portal vagy Azure Resource Manager sablon használatával.
- Új, sablont használó Azure Resource Manager, vagy új fürt létrehozása során az [Azure CLI használatával.](/cli/azure/openshift#az_openshift_create)

## <a name="supported-and-unsupported-features"></a>Támogatott és nem támogatott funkciók

A Container Insights az Azure Red Hat OpenShift cikk [](container-insights-overview.md) alapján támogatja a monitorozást, kivéve a következő funkciókat:

- Élő adatok (előzetes verzió)
- [Metrikák gyűjtése](container-insights-update-metrics.md) fürtcsomópontokból és podokból, és tárolásuk a Azure Monitor metrikák adatbázisában

## <a name="prerequisites"></a>Előfeltételek

- Egy [Log Analytics-munkaterület](../logs/design-logs-deployment.md).

    A tárolóelemzések támogatják a Log Analytics-munkaterületeket az Azure-termékek régiók szerint [listában felsorolt régiókban.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Saját munkaterület létrehozásához létrehozható a [](../logs/resource-manager-workspace.md)következő Azure Resource Manager, a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)használatával, vagy a [Azure Portal.](../logs/quick-create-workspace.md)

- A Container Insights funkcióinak engedélyezéséhez és eléréséhez legalább Azure-közreműködői  szerepkörnek kell lennie az Azure-előfizetésben, valamint a Container Insights szolgáltatással konfigurált [*Log Analytics-munkaterület Log Analytics-közreműködői*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörének a tagja.

- A monitorozási adatok megtekintéséhez Ön a [*Log Analytics*](../logs/manage-access.md#manage-access-using-azure-permissions) olvasói szerepkör engedélyének tagja a Container Insights szolgáltatással konfigurált Log Analytics-munkaterületen.

## <a name="identify-your-log-analytics-workspace-id"></a>A Log Analytics-munkaterület azonosítójának azonosítása

 Egy meglévő Log Analytics-munkaterülettel való integrációhoz először azonosítsa a Log Analytics-munkaterület teljes erőforrás-azonosítóját. A munkaterület erőforrás-azonosítója akkor szükséges a paraméterhez, ha engedélyezi a `workspaceResourceId` monitorozást Azure Resource Manager sablon metódusával.

1. Az alábbi parancs futtatásával sorolja fel az összes előfizetést, amelyekhez hozzáféréssel rendelkezik:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet az alábbihoz hasonló lesz:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

1. Másolja ki a **SubscriptionId értékét.**

1. Váltson arra az előfizetésre, amely a Log Analytics-munkaterületet futtatja a következő parancs futtatásával:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

1. Jelenítse meg az előfizetésében található munkaterületek listáját az alapértelmezett JSON formátumban a következő parancs futtatásával:

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

1. A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics-munkaterület teljes erőforrás-azonosítóját a **mezőazonosító alá.**

## <a name="enable-for-a-new-cluster-using-an-azure-resource-manager-template"></a>Engedélyezés új fürtön Azure Resource Manager sablonnal

A következő lépésekkel üzembe helyezhet egy olyan Azure Red Hat OpenShift fürtöt, amelynél engedélyezve van a figyelés. A folytatás előtt tekintse [át](../../openshift/tutorial-create-cluster.md) az Azure Red Hat OpenShift fürt létrehozása oktatóanyagot, amelyből megtudhatja, milyen függőségeket kell konfigurálnia ahhoz, hogy a környezet megfelelően legyen beállítva.

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza a fürt üzembe helyezésének konfigurációját engedélyezett figyelés mellett, a másik pedig a következő paraméterértékek megadására konfigurált paraméterértékeket tartalmazza:

- Az Azure Red Hat OpenShift fürt erőforrás-azonosítója.

- Az erőforráscsoport, amelybe a fürt telepítve van.

- [Azure Active Directory a bérlőazonosítót,](../../openshift/howto-create-tenant.md#create-a-new-azure-ad-tenant) amely egy vagy egy már létrehozott bérlő létrehozásához szükséges lépések végrehajtása után van megjelölve.

- [Azure Active Directory az ügyfélalkalmazás-azonosítót,](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-app-registration) amely egy vagy egy már létrehozott ügyfélalkalmazás létrehozásához szükséges lépések végrehajtása után van feljegyve.

- [Azure Active Directory client secret (Titkos](../../openshift/howto-aad-app-configuration.md#create-a-client-secret) ügyfél titkos gombra) a már létrehozott ügyfél titkos létrehozására tett lépések végrehajtása után kell feljegyezésre.

- [Az Azure AD biztonsági csoport](../../openshift/howto-aad-app-configuration.md#create-an-azure-ad-security-group) a lépések végrehajtása után feljegyzett egy vagy egy már létrehozott biztonsági csoport létrehozása után.

- Egy meglévő Log Analytics-munkaterület erőforrás-azonosítója. Az információk lekért információkért [lásd: A Log Analytics-munkaterület](#identify-your-log-analytics-workspace-id) azonosítójának azonosítása.

- A fürtben létrehozatni szükséges főcsomópontok száma.

- Az ügynökkészlet-profilban lévő számítási csomópontok száma.

- Az ügynökkészlet-profilban lévő infrastruktúra-csomópontok száma.

Ha nem ismeri az erőforrások sablonnal való üzembe helyezésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager sablonokkal és az Azure CLI-val](../../azure-resource-manager/templates/deploy-cli.md)

Ha az Azure CLI használatát választja, először helyileg kell telepítenie és használnia a CLI-t. Az Azure CLI 2.0.65-ös vagy újabb verziójával kell futnia. A verzió azonosításához futtassa az `az --version` -t. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

1. Töltse le és mentse a fájlt egy helyi mappába, a Azure Resource Manager sablont és paraméterfájlt, és hozzon létre egy fürtöt a figyelési bővítményrel az alábbi parancsokkal:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoring.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_new_cluster/newClusterWithMonitoringParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login
    ```

    Ha több előfizetéshez is hozzáféréssel rendelkezik, futtassa a következőt: . Cserélje le a et `az account set -s {subscription ID}` `{subscription ID}` a használni kívánt előfizetésre.

3. Ha még nem rendelkezik erőforráscsoporttal, hozzon létre egy erőforráscsoportot a fürthöz. Az Azure-on openShiftet támogató Azure-régiók listáját lásd: [Támogatott régiók.](../../openshift/supported-resources.md#azure-regions)

    ```azurecli
    az group create -g <clusterResourceGroup> -l <location>
    ```

4. Szerkessze a **JSON-paraméterfájlt,newClusterWithMonitoringParam.jsés** frissítse a következő értékeket:

    - *Helyen*
    - *clusterName (fürt neve)*
    - *aadTenantId (aadTenantId)*
    - *aadClientId*
    - *aadClientSecret*
    - *aadCustomerAdminGroupId*
    - *workspaceResourceId*
    - *masterNodeCount*
    - *computeNodeCount*
    - *infraNodeCount*

5. A következő lépés az Azure CLI használatával engedélyezett monitorozással telepíti a fürtöt.

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./newClusterWithMonitoring.json --parameters @./newClusterWithMonitoringParam.json
    ```

    A kimenet az alábbihoz hasonló:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-for-an-existing-cluster"></a>Engedélyezés meglévő fürtön

A következő lépésekkel engedélyezheti az Azure-ban Azure Red Hat OpenShift fürt monitorozását. Ezt elvégezheti a Azure Portal a megadott sablonokkal.

### <a name="from-the-azure-portal"></a>Az Azure Portalról

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A Azure Portal vagy a Kezdőlapon válassza a Azure Monitor **lehetőséget.** Az Elemzések **szakaszban** válassza a **Tárolók lehetőséget.**

3. A Monitor **– tárolók lapon** válassza a **Nem figyelt fürtök lehetőséget.**

4. A nem figyelt fürtök listájában keresse meg a fürtöt a listában, és kattintson az **Enable (Engedélyezés) gombra.** Az eredmények a listában való azonosításához az **ARO** értéket kell keresnie a CLUSTER TYPE (FÜRT **TÍPUSA) oszlopban.**

5. Ha a **Container Insightsba** való beléptetés oldalon a fürttel azonos előfizetésben van egy meglévő Log Analytics-munkaterület, válassza ki azt a legördülő listából.  
    A lista előre bejelölve tartalmazza azt az alapértelmezett munkaterületet és helyet, ahol a fürt üzembe lesz állítva az előfizetésben.

    ![Nem figyelt fürtök monitorozásának engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics-munkaterületet szeretne létrehozni a fürtből származó monitorozási adatok tárolásához, kövesse [a Log Analytics-munkaterület létrehozása útmutatót.](../logs/quick-create-workspace.md) A munkaterületet ugyanabban az előfizetésben hozza létre, mint a RedHat OpenShift-fürtöt.

A figyelés engedélyezése után a fürt állapotmetrikák megtekintése körülbelül 15 percet is igénybe vehet.

### <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés sablon Azure Resource Manager használatával

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon megadja a figyelés engedélyezéséhez szükséges konfigurációt, a másik pedig olyan paraméterértékeket tartalmaz, amelyek konfigurálva vannak a következők megadásához:

- Az Azure RedHat OpenShift-fürt erőforrás-azonosítója.

- Az erőforráscsoport, amelybe a fürt telepítve van.

- Egy Log Analytics-munkaterület. Az információk lekért információkért [lásd: A Log Analytics-munkaterület](#identify-your-log-analytics-workspace-id) azonosítójának azonosítása.

Ha nem ismeri az erőforrások sablonnal való üzembe helyezésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager sablonokkal és az Azure CLI-val](../../azure-resource-manager/templates/deploy-cli.md)

Ha az Azure CLI használatát választja, először helyileg kell telepítenie és használnia a CLI-t. Az Azure CLI 2.0.65-ös vagy újabb verziójával kell futnia. A verzió azonosításához futtassa az `az --version` -t. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

1. Töltse le a sablont és a paraméterfájlt, hogy frissítse a fürtöt a monitorozási bővítményrel a következő parancsokkal:

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterOnboarding.json`

    `curl -LO https://raw.githubusercontent.com/microsoft/Docker-Provider/ci_dev/scripts/onboarding/aro/enable_monitoring_to_existing_cluster/existingClusterParam.json`

2. Bejelentkezés az Azure-ba

    ```azurecli
    az login
    ```

    Ha több előfizetéshez is hozzáféréssel rendelkezik, futtassa a következőt: . Cserélje le a et `az account set -s {subscription ID}` `{subscription ID}` a használni kívánt előfizetésre.

3. Adja meg az Azure RedHat OpenShift-fürt előfizetését.

    ```azurecli
    az account set --subscription "Subscription Name"  
    ```

4. Futtassa a következő parancsot a fürt helyének és erőforrás-azonosítójának azonosításához:

    ```azurecli
    az openshift show -g <clusterResourceGroup> -n <clusterName>
    ```

5. Szerkessze a **JSON-paraméterfájlt aexistingClusterParam.jsés** frissítse az *aroResourceId* és *az aroResourceLocation értékeket.* A **workspaceResourceId** értéke a Log Analytics-munkaterület teljes erőforrás-azonosítója, amely tartalmazza a munkaterület nevét.

6. Az Azure CLI-val való üzembe helyezéshez futtassa a következő parancsokat:

    ```azurecli
    az deployment group create --resource-group <ClusterResourceGroupName> --template-file ./ExistingClusterOnboarding.json --parameters @./existingClusterParam.json
    ```

    A kimenet az alábbihoz hasonló:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="next-steps"></a>Következő lépések

- Ha a monitorozás engedélyezve van a RedHat OpenShift-fürt és a rajta futó számítási feladatok állapotának és erőforrás-felhasználásának gyűjtéséhez, ismerje meg a [Container](container-insights-analyze.md) Insights használatát.

- Alapértelmezés szerint a tárolóba gyűjtött ügynök a kube-system kivételével az összes névtérben futó összes tároló stdout/stderr tárolónaplóit gyűjti. Ha adott névtérre vagy névterre vonatkozó tárolónapló-gyűjtési beállításokat kíván konfigurálni, tekintse át a [Container Insights-ügynök](container-insights-agent-config.md) konfigurációját, és konfigurálja a kívánt adatgyűjtési beállításokat a ConfigMap konfigurációs fájlhoz.

- A Prometheus-metrikák fürtből való leselkedéséhez és elemzéséhez tekintse át a [Prometheus-metrikák](container-insights-prometheus-integration.md) leselkedésének konfigurálásával kapcsolatos ismertetőt

- A fürt Container Insights segítségével való monitorozásának leállításával kapcsolatos információkért lásd: How to Stop Monitoring Your Azure Red Hat OpenShift cluster (A fürt [monitorozásának Azure Red Hat OpenShift le).](./container-insights-optout-openshift-v3.md)