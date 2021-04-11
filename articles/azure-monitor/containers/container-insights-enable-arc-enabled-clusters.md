---
title: Azure arc-kompatibilis Kubernetes-fürt konfigurálása a Container bepillantást biztosító szolgáltatással | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a figyelés az Azure arc-kompatibilis Kubernetes-fürtökön található tároló-információkkal.
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: d6a725f6af613a541077cecfed3ba2289909130d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219230"
---
# <a name="enable-monitoring-of-azure-arc-enabled-kubernetes-cluster"></a>Azure Arc-kompatibilis Kubernetes-fürt monitorozásának engedélyezése

A Container-eredmények széles körű monitorozást biztosítanak az Azure Kubernetes szolgáltatás (ak) és az AK-os motor fürtök számára. Ez a cikk azt ismerteti, hogyan engedélyezhető az Azure-on kívül üzemeltetett Kubernetes-fürtök figyelése az Azure arc használatával, hasonló figyelési élmény eléréséhez.

A Kubernetes egy vagy több meglévő üzemelő példánya számára engedélyezhető a PowerShell vagy bash parancsfájl használatával.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A Container-megállapítások támogatják az Azure arc-kompatibilis Kubernetes (előzetes verzió) figyelését az [Áttekintés](container-insights-overview.md) című cikkben leírtak szerint, a következő funkciók kivételével:

- Élő adatértékek (előzetes verzió)

A következő hivatalosan támogatott a Container bepillantást:

- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint a [támogatott AK](../../aks/supported-kubernetes-versions.md)-verziók.

- A következő tároló-futtatókörnyezetek támogatottak: Docker, Moby és ICC-kompatibilis futtatókörnyezetek, mint például az ICC-O és a tároló.

- A támogatott fő-és munkavégző csomópontok linuxos operációsrendszer-kiadása a következő: Ubuntu (18,04 LTS és 16,04 LTS).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy Log Analytics-munkaterület.

    A Container-bepillantást az Azure [Products By Region régiójában](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiók log Analytics munkaterületét támogatják. Saját munkaterület létrehozásához [Azure Resource Manager](../logs/resource-manager-workspace.md), a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../logs/quick-create-workspace.md)használatával hozható létre.

- Az Azure-előfizetésben szereplő funkciók engedélyezéséhez és eléréséhez legalább az Azure *közreműködő* szerepkör tagjának kell lennie az Azure-előfizetésben, valamint a Log Analytics munkaterület [*log Analytics közreműködő*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörének tagja.

- Ön tagja a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkörnek az Azure arc fürterőforrás-erőforráson.

- A figyelési adat megtekintéséhez Ön a [*log Analytics olvasói*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkör tagja, és a log Analytics munkaterületet tároló-elemzésekkel konfigurálta.

- [Helm-ügyfél](https://helm.sh/docs/using_helm/) a megadott Kubernetes-fürthöz tartozó Container bepillantást tartalmazó diagram előkészítéséhez.

- A következő proxy-és tűzfal-konfigurációs információk szükségesek a Linux rendszerhez készült Log Analytics-ügynöknek a Azure Monitorval való kommunikációhoz:

    |Ügynök erőforrása|Portok |
    |------|---------|
    |`*.ods.opinsights.azure.com` |443-es port |
    |`*.oms.opinsights.azure.com` |443-es port |
    |`*.dc.services.visualstudio.com` |443-es port |

- A tároló ügynöknek a Kubelet kell `cAdvisor secure port: 10250` `unsecure port :10255` megnyitnia, vagy a fürt összes csomópontján meg kell nyitni a teljesítmény-metrikák gyűjtéséhez. Javasoljuk, hogy konfigurálja a `secure port: 10250` Kubelet cAdvisor, ha még nincs konfigurálva.

- A tároló ügynöknek a következő környezeti változókat kell megadnia a tárolón ahhoz, hogy a Kubernetes API szolgáltatással kommunikáljon a fürtön belül a leltári adatok összegyűjtéséhez – `KUBERNETES_SERVICE_HOST` és `KUBERNETES_PORT_443_TCP_PORT` .

    >[!IMPORTANT]
    >Az arc-kompatibilis Kubernetes-fürtök figyelésének minimális ügynök-verziója ciprod04162020 vagy újabb.

- Ha a PowerShell-parancsfájl használatával engedélyezi a figyelést, a [PowerShell Core](/powershell/scripting/install/installing-powershell?view=powershell-6&preserve-view=true) megadása szükséges.

- Ha a bash parancsfájl használatával engedélyezi a figyelést, a [bash 4-es verziója](https://www.gnu.org/software/bash/) szükséges.

## <a name="identify-workspace-resource-id"></a>Munkaterület erőforrás-AZONOSÍTÓjának azonosítása

Ha engedélyezni szeretné a fürt figyelését a korábban letöltött PowerShell vagy bash parancsfájl használatával, és integrálva van egy meglévő Log Analytics munkaterülettel, hajtsa végre a következő lépéseket az Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓjának azonosításához. Erre a paraméterre akkor van szükség, `workspaceResourceId` Amikor a parancsot futtatja, hogy engedélyezze a figyelési bővítményt a megadott munkaterületen. Ha nem rendelkezik a megadható munkaterülettel, kihagyhatja a `workspaceResourceId` paramétert, és lehetővé teheti, hogy a szkript létrehozzon egy új munkaterületet.

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőhöz hasonló lesz:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Másolja a **SubscriptionId** értékét.

2. Váltson a Log Analytics munkaterületet üzemeltető előfizetésre a következő paranccsal:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Az alábbi példa az előfizetésekben lévő munkaterületek listáját jeleníti meg az alapértelmezett JSON-formátumban.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját a mező **azonosítója** alá.

## <a name="enable-monitoring-using-powershell"></a>Figyelés engedélyezése a PowerShell használatával

1. Töltse le és mentse a parancsfájlt egy helyi mappába, amely a következő parancsokkal konfigurálja a fürtöt a figyelési bővítmény használatával:

    ```powershell
    Invoke-WebRequest https://aka.ms/enable-monitoring-powershell-script -OutFile enable-monitoring.ps1
    ```

2. Konfigurálja a `$azureArcClusterResourceId` változót úgy, hogy beállítja a megfelelő értékeit `subscriptionId` , `resourceGroupName` és `clusterName` Az Azure arc-kompatibilis Kubernetes-fürterőforrás erőforrás-azonosítóját jelképezi.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurálja a `$kubeContext` változót a fürt **Kube-környezetével** a parancs futtatásával `kubectl config get-contexts` . 

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Ha meglévő Azure Monitor Log Analytics munkaterületet szeretne használni, konfigurálja a változót `$logAnalyticsWorkspaceResourceId` a munkaterület erőforrás-azonosítóját jelképező megfelelő értékkel. Ellenkező esetben állítsa a (z) változót a (z) értékre, `""` és a parancsfájl létrehoz egy alapértelmezett munkaterületet a fürt-előfizetés alapértelmezett erőforrás-csoportjában, ha még nem létezik a régióban. A létrehozott alapértelmezett munkaterület a *alapértelmezettmunkaterület \<SubscriptionID> - \<Region>* formátumához hasonlít.

    ```powershell
    $logAnalyticsWorkspaceResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Ha az arc-kompatibilis Kubernetes-fürt proxykiszolgálón keresztül kommunikál, konfigurálja a változót a `$proxyEndpoint` proxykiszolgáló URL-címével. Ha a fürt nem proxykiszolgálón keresztül kommunikál, akkor beállíthatja a értéket a következőre: `""` .  További információ: proxy- [végpont konfigurálása](#configure-proxy-endpoint) a cikk későbbi részében.

6. A figyelés engedélyezéséhez futtassa a következő parancsot.

    ```powershell
    .\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
    ```

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="using-service-principal"></a>Egyszerű szolgáltatásnév használata
A szkript *enable-monitoring.ps1* az interaktív eszköz bejelentkezését használja. Ha inkább a nem interaktív bejelentkezést részesíti előnyben, használhat egy meglévő szolgáltatásnevet, vagy létrehozhat egy újat, amely rendelkezik a szükséges engedélyekkel az [Előfeltételek](#prerequisites)szakaszban leírtak szerint. Az egyszerű szolgáltatásnév használatához át kell adnia $servicePrincipalClientId, $servicePrincipalClientSecret és $tenantId paramétereket a szolgáltatás *enable-monitoring.ps1* parancsfájlhoz használni kívánt egyszerű szolgáltatásnév értékeivel.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"
```

Az alábbi szerepkör-hozzárendelés csak akkor érvényes, ha a meglévő Log Analytics munkaterületet egy másik Azure-előfizetésben használja, mint az arc K8s kapcsolódó fürterőforrás.

```powershell
$logAnalyticsWorkspaceResourceId = "<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
New-AzRoleAssignment -RoleDefinitionName 'Log Analytics Contributor'  -ObjectId $servicePrincipal.Id -Scope  $logAnalyticsWorkspaceResourceId

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Például:

```powershell
.\enable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId -kubeContext $kubeContext -workspaceResourceId $logAnalyticsWorkspaceResourceId -proxyEndpoint $proxyEndpoint
```



## <a name="enable-using-bash-script"></a>Engedélyezés bash-parancsfájl használatával

Az alábbi lépések végrehajtásával engedélyezheti a figyelést a megadott bash-parancsfájl használatával.

1. Töltse le és mentse a parancsfájlt egy helyi mappába, amely a következő parancsokkal konfigurálja a fürtöt a figyelési bővítmény használatával:

    ```bash
    curl -o enable-monitoring.sh -L https://aka.ms/enable-monitoring-bash-script
    ```

2. Konfigurálja a `azureArcClusterResourceId` változót úgy, hogy beállítja a megfelelő értékeit `subscriptionId` , `resourceGroupName` és `clusterName` Az Azure arc-kompatibilis Kubernetes-fürterőforrás erőforrás-azonosítóját jelképezi.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurálja a `kubeContext` változót a fürt **Kube-környezetével** a parancs futtatásával `kubectl config get-contexts` . 

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. Ha meglévő Azure Monitor Log Analytics munkaterületet szeretne használni, konfigurálja a változót `logAnalyticsWorkspaceResourceId` a munkaterület erőforrás-azonosítóját jelképező megfelelő értékkel. Ellenkező esetben állítsa a (z) változót a (z) értékre, `""` és a parancsfájl létrehoz egy alapértelmezett munkaterületet a fürt-előfizetés alapértelmezett erőforrás-csoportjában, ha még nem létezik a régióban. A létrehozott alapértelmezett munkaterület a *alapértelmezettmunkaterület \<SubscriptionID> - \<Region>* formátumához hasonlít.

    ```bash
    export logAnalyticsWorkspaceResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/microsoft.operationalinsights/workspaces/<workspaceName>"
    ```

5. Ha az arc-kompatibilis Kubernetes-fürt proxykiszolgálón keresztül kommunikál, konfigurálja a változót a `proxyEndpoint` proxykiszolgáló URL-címével. Ha a fürt nem proxykiszolgálón keresztül kommunikál, akkor beállíthatja a értéket a következőre: `""` . További információ: proxy- [végpont konfigurálása](#configure-proxy-endpoint) a cikk későbbi részében.

6. Ha engedélyezni szeretné a figyelést a fürtön, különböző parancsok érhetők el az üzembe helyezési forgatókönyv alapján.

    A következő parancs futtatásával engedélyezheti a figyelést az alapértelmezett beállításokkal, például az aktuális Kube használatával, alapértelmezett Log Analytics munkaterületet hozhat létre, és nem adhat meg proxykiszolgálót:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    A következő parancs futtatásával hozzon létre egy alapértelmezett Log Analytics munkaterületet, és ne adja meg a proxykiszolgálót:

    ```bash
   bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

    Futtassa a következő parancsot egy meglévő Log Analytics munkaterület használatához, és ne adja meg a proxykiszolgálót:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId
    ```

    Futtassa a következő parancsot egy meglévő Log Analytics munkaterület használatához, és adja meg a proxykiszolgálót:

    ```bash
    bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
    ```

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

### <a name="using-service-principal"></a>Egyszerű szolgáltatásnév használata
A bash parancsfájl *enable-monitoring.sh* az interaktív eszköz bejelentkezését használja. Ha inkább a nem interaktív bejelentkezést részesíti előnyben, használhat egy meglévő szolgáltatásnevet, vagy létrehozhat egy újat, amely rendelkezik a szükséges engedélyekkel az [Előfeltételek](#prerequisites)szakaszban leírtak szerint. Az egyszerű szolgáltatás használatához át kell adnia az ügyfél-azonosító,--Client-Secret és a---bérlő-ID értékeket, amelyeket a bash-szkript *enable-monitoring.sh* kíván használni.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')
```

Az alábbi szerepkör-hozzárendelés csak akkor érvényes, ha a meglévő Log Analytics munkaterületet egy másik Azure-előfizetésben használja, mint az arc K8s kapcsolódó fürterőforrás.

```bash
logAnalyticsWorkspaceResourceId="<Azure Resource Id of the Log Analytics Workspace>" # format of the Azure Log Analytics workspace should be /subscriptions/<subId>/resourcegroups/<rgName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>
az role assignment create --role 'Log Analytics Contributor' --assignee $servicePrincipalClientId --scope $logAnalyticsWorkspaceResourceId

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Például:

```bash
bash enable-monitoring.sh --resource-id $azureArcClusterResourceId --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId --kube-context $kubeContext  --workspace-id $logAnalyticsWorkspaceResourceId --proxy $proxyEndpoint
```

## <a name="configure-proxy-endpoint"></a>Proxy végpontjának konfigurálása

A tároló-megállapítások tároló ügynökével konfigurálhat egy proxy végpontot, hogy az a proxykiszolgáló használatával kommunikáljon. A tároló ügynök és a Azure Monitor közötti kommunikáció HTTP-vagy HTTPS-proxykiszolgáló lehet, és a névtelen és az alapszintű hitelesítés (username/Password) is támogatott.

A proxy konfigurációs értékének szintaxisa a következő: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Ha a proxykiszolgáló nem igényel hitelesítést, akkor továbbra is meg kell adnia egy psuedo felhasználónevet vagy jelszót. Ez lehet bármilyen Felhasználónév vagy jelszó.

|Tulajdonság| Leírás |
|--------|-------------|
|Protokoll | http vagy https |
|felhasználó! | Opcionális Felhasználónév a proxy hitelesítéséhez |
|jelszó | Opcionális jelszó a proxy hitelesítéséhez |
|proxyhost | A proxykiszolgáló címe vagy teljes tartományneve |
|port | A proxykiszolgáló nem kötelező portszáma |

Például: `http://user01:password@proxy01.contoso.com:3128`

Ha a protokollt **http**-ként adta meg, a HTTP-kérelmek SSL/TLS biztonságos kapcsolat használatával jönnek létre. A proxykiszolgáló támogatja az SSL/TLS protokollokat.

### <a name="configure-using-powershell"></a>Konfigurálás a PowerShell használatával

Itt adhatja meg a proxykiszolgáló felhasználónevét és jelszavát, IP-címét vagy teljes tartománynevét és portszámát. Például:

```powershell
$proxyEndpoint = https://<user>:<password>@<proxyhost>:<port>
```

### <a name="configure-using-bash"></a>Konfigurálás a bash használatával

Itt adhatja meg a proxykiszolgáló felhasználónevét és jelszavát, IP-címét vagy teljes tartománynevét és portszámát. Például:

```bash
export proxyEndpoint=https://<user>:<password>@<proxyhost>:<port>
```

## <a name="next-steps"></a>Következő lépések

- Ha a figyelés engedélyezve van az arc-kompatibilis Kubernetes-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-felhasználásának összegyűjtéséhez, Ismerje meg, [hogyan használhatók](container-insights-analyze.md) a Container-információk.

- Alapértelmezés szerint a tároló ügynök gyűjti az összes névtérben futó összes tároló StdOut/stderr-tárolójának naplóit, kivéve a Kube rendszert. Ha az adott névtérhez vagy névterekhez tartozó tároló-naplózási gyűjteményt szeretne konfigurálni, tekintse át a Container-elemzések [ügynökének konfigurációját](container-insights-agent-config.md) a kívánt adatgyűjtési beállítások ConfigMap-konfigurációs fájlra való konfigurálásához.

- A Prometheus-metrikák a fürtből való beolvasásához és elemzéséhez tekintse át a [Prometheus-metrikák leselejtezésének konfigurálása](container-insights-prometheus-integration.md)

- Ha meg szeretné tudni, hogyan állíthatja le az arc-kompatibilis Kubernetes-fürtöt a tároló-információkkal, tekintse meg [a hibrid fürt figyelésének leállítása](container-insights-optout-hybrid.md#how-to-stop-monitoring-on-arc-enabled-kubernetes)című témakört.
