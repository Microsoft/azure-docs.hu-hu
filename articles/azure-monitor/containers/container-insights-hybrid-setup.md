---
title: Hibrid Kubernetes-fürtök konfigurálása a Azure Monitor for containers szolgáltatásban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható Azure Monitor a tárolók számára Azure Stack vagy más környezetben üzemeltetett Kubernetes-fürtök figyelésére.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 12901b1d2d7edd85fbe1650600856d09105c15b2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616278"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Hibrid Kubernetes-fürtök konfigurálása Azure Monitor tárolók számára

A tárolók Azure Monitor széles körű monitorozási élményt biztosítanak az Azure-ban üzemeltetett Azure Kubernetes szolgáltatás (ak) és az AK-os [motor](https://github.com/Azure/aks-engine)számára, amely az Azure-ban üzemeltetett, önállóan felügyelt Kubernetes-fürt. Ez a cikk bemutatja, hogyan engedélyezheti az Azure-on kívül üzemeltetett Kubernetes-fürtök figyelését, és így hasonló figyelési élményt érhet el.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A következő konfigurációk hivatalosan támogatottak a tárolók Azure Monitor. Ha a Kubernetes és az operációsrendszer-verziók eltérő verzióját használja, küldjön egy e-mailt a következőnek: askcoin@microsoft.com .

- Környezetben

    - Helyszíni Kubernetes
    - AK-motor az Azure-ban és a Azure Stack. További információ: AK-beli [motor on Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - A [OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4-es és újabb verziója, helyszíni vagy más felhőalapú környezetek.

- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint a [támogatott AK](../../aks/supported-kubernetes-versions.md)-verziók.

- A következő tároló-futtatókörnyezetek támogatottak: Docker, Moby és ICC-kompatibilis futtatókörnyezetek, mint például az ICC-O és a tároló.

- A támogatott fő-és munkavégző csomópontok linuxos operációsrendszer-kiadása a következő: Ubuntu (18,04 LTS és 16,04 LTS) és Red Hat Enterprise Linux CoreOS 43,81.

- Támogatott hozzáférés-vezérlés: Kubernetes RBAC és nem RBAC

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy [Log Analytics-munkaterület](../platform/design-logs-deployment.md).

    A tárolók Azure Monitor Log Analytics munkaterületet támogatnak az Azure [Products By Region régióban](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)felsorolt régiókban. Saját munkaterület létrehozásához [Azure Resource Manager](../samples/resource-manager-workspace.md), a [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../learn/quick-create-workspace.md)használatával hozható létre.

    >[!NOTE]
    >Az azonos nevű fürttel rendelkező több fürt figyelésének engedélyezése ugyanahhoz a Log Analytics-munkaterülethez nem támogatott. A fürt nevének egyedinek kell lennie.
    >

- Tagja a **log Analytics közreműködő szerepkörnek** a tárolók figyelésének engedélyezéséhez. További információ a Log Analytics munkaterület hozzáférésének szabályozásáról: a [munkaterület hozzáférésének kezelése és az adatok naplózása](../platform/manage-access.md).

- A figyelési adat megtekintéséhez [*log Analytics olvasó*](../platform/manage-access.md#manage-access-using-azure-permissions) szerepkörrel kell rendelkeznie a log Analytics munkaterületen, amely a tárolók Azure monitorhoz van konfigurálva.

- A [Helm-ügyfél](https://helm.sh/docs/using_helm/) a megadott Kubernetes-fürthöz tartozó tárolók diagramjának Azure monitor bevezetéséhez.

- A következő proxy-és tűzfal-konfigurációs információk szükségesek a Linux rendszerhez készült Log Analytics-ügynöknek a Azure Monitorval való kommunikációhoz:

    |Ügynök erőforrása|Portok |
    |------|---------|
    |*.ods.opinsights.azure.com |443-es port |
    |*.oms.opinsights.azure.com |443-es port |
    |*. dc.services.visualstudio.com |443-es port |

- A tároló ügynöknek a Kubelet kell `cAdvisor secure port: 10250` `unsecure port :10255` megnyitnia, vagy a fürt összes csomópontján meg kell nyitni a teljesítmény-metrikák gyűjtéséhez. Javasoljuk, hogy konfigurálja a `secure port: 10250` Kubelet cAdvisor, ha még nincs konfigurálva.

- A tároló ügynöknek a következő környezeti változókat kell megadnia a tárolón ahhoz, hogy a Kubernetes API szolgáltatással kommunikáljon a fürtön belül a leltári adatok összegyűjtéséhez – `KUBERNETES_SERVICE_HOST` és `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>A hibrid Kubernetes-fürtök figyeléséhez támogatott minimális ügynök verziója ciprod10182019 vagy újabb.

## <a name="enable-monitoring"></a>Monitorozás engedélyezése

A hibrid Kubernetes-fürthöz tartozó tárolók Azure Monitorának engedélyezése a következő lépések végrehajtásával történik.

1. Konfigurálja a Log Analytics munkaterületet a Container bepillantást biztosító megoldással.   

2. Engedélyezze a Azure Monitor for containers HELM diagramot Log Analytics munkaterülettel.

A Azure Monitor figyelésével kapcsolatos további [információkért tekintse](../../azure-monitor/insights/solutions.md)meg a következőt:.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>A Azure Monitor containers megoldás hozzáadása

A megoldás a megadott Azure Resource Manager sablonnal is üzembe helyezhető a Azure PowerShell parancsmag `New-AzResourceGroupDeployment` vagy az Azure CLI használatával.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza a figyelés engedélyezésének konfigurációját, a másik pedig a konfigurált paramétereket az alábbiak megadásához:

- **workspaceResourceId** – a log Analytics munkaterület teljes erőforrás-azonosítója.
- **workspaceRegion** – az a régió, amely a munkaterületet hozza létre, és a munkaterület **tulajdonságai között a** Azure Portal.

A következő lépésekkel azonosíthatja a Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját `workspaceResourceId` , amely a paraméter értékéhez szükséges a fájl **containerSolutionParams.jsjában** . Ehhez hajtsa végre az alábbi lépéseket, majd futtassa a PowerShell-parancsmagot vagy az Azure CLI-parancsot a megoldás hozzáadásához.

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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓját a mező **azonosítója** alá.

4. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Mentse ezt a fájlt containerSolution.jsként egy helyi mappába.

6. Illessze be a következő JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. Szerkessze a **workspaceResourceId** értékeit a 3. lépésben átmásolt értékkel, és a **WorkspaceRegion** másolja a **régió** értékét az Azure CLI parancs futtatása után az [monitor log-Analytics Workspace show](/cli/azure/monitor/log-analytics/workspace#az-monitor-log-analytics-workspace-list&preserve-view=true)paranccsal.

8. Mentse ezt a fájlt containerSolutionParams.jsként egy helyi mappába.

9. Készen áll a sablon üzembe helyezésére.

   - A Azure PowerShell használatával történő üzembe helyezéshez használja az alábbi parancsokat a sablont tartalmazó mappában:

       ```powershell
       # configure and login to the cloud of Log Analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of Log Analytics workspace>
       ```

       ```powershell
       # execute deployment command to add Container Insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of Log Analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

       ```azurecli
       provisioningState       : Succeeded
       ```

       A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

## <a name="install-the-helm-chart"></a>A HELM diagram telepítése

Ebben a szakaszban az Azure Monitor tárolók számára telepíti a tároló ügynököt. A továbblépés előtt meg kell határoznia a `omsagent.secret.wsid` paraméterhez szükséges munkaterület-azonosítót és a paraméterhez szükséges elsődleges kulcsot `omsagent.secret.key` . Ezeket az információkat a következő lépések végrehajtásával azonosíthatja, majd a parancs futtatásával telepítheti az ügynököt a HELM diagram használatával.

1. Futtassa a következő parancsot a munkaterület AZONOSÍTÓjának azonosításához:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    A kimenetben keresse meg a munkaterület nevét **a mezőnév alatt, majd** másolja a log Analytics munkaterület munkaterület-azonosítóját a **Vevőkód** mező alá.

2. Futtassa a következő parancsot a munkaterület elsődleges kulcsának azonosításához:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    A kimenetben keresse meg az elsődleges kulcsot a **primarySharedKey** mezőben, majd másolja az értéket.

>[!NOTE]
>A következő parancsok csak a Helm 2-es verziójára érvényesek. A paraméter használata `--name` nem alkalmazható a Helm 3-as verziójára. 

>[!NOTE]
>Ha a Kubernetes-fürt proxykiszolgálón keresztül kommunikál, konfigurálja a paramétert a `omsagent.proxy` proxykiszolgáló URL-címével. Ha a fürt nem a proxykiszolgálón keresztül kommunikál, akkor nem kell megadnia ezt a paramétert. További információ: proxy- [végpont konfigurálása](#configure-proxy-endpoint) a cikk későbbi részében.

3. Adja hozzá az Azure-diagramok tárházát a helyi listához a következő parancs futtatásával:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. A következő parancs futtatásával telepítse a diagramot:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Ha a Log Analytics munkaterület az Azure China 21Vianet található, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Ha az Log Analytics munkaterület az Azure US Governmentben van, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>A Helm diagram engedélyezése az API-modell használatával

Megadhat egy addont az AK-motor fürt specifikációjának JSON-fájljában (más néven API-modellként). Ebben az addon-ben adja meg az Log Analytics-munkaterület Base64 kódolású verzióját `WorkspaceGUID` és a `WorkspaceKey` begyűjtött megfigyelési adatokat tároló munkaterületet. Az `WorkspaceGUID` előző szakaszban található és az `WorkspaceKey` 1. és a 2. lépést is megtalálhatja.

Az Azure Stack hub-fürt támogatott API-definíciói a következő példában találhatók: [kubernetes-container-monitoring_existing_workspace_id_and_key.js](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Pontosabban keresse meg az **addons** tulajdonságot a **kubernetesConfig**-ben:

```json
"orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Id in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="configure-agent-data-collection"></a>Az ügynök adatgyűjtésének konfigurálása

Az ügynök adatgyűjtési beállításai a ConfigMap-ből vezérelhetők, és a diagramon a 1.0.0. Tekintse meg az ügynök adatgyűjtési beállításaival kapcsolatos dokumentációt [itt](container-insights-agent-config.md).

A diagram sikeres üzembe helyezését követően áttekintheti a hibrid Kubernetes-fürt adatait a Azure Portal tárolók Azure Monitorban.  

>[!NOTE]
>A betöltési késés körülbelül öt – tíz perc az ügynöktől az Azure Log Analytics-munkaterületen véglegesítve. A fürt állapota megjelenítheti a **nem adat** vagy **ismeretlen** értéket, amíg a Azure monitor összes szükséges figyelési adata nem érhető el.

## <a name="configure-proxy-endpoint"></a>Proxy végpontjának konfigurálása

A 2.7.1-es diagrammal kezdődően a diagram támogatja a proxy végpont megadását a `omsagent.proxy` diagram paraméterrel. Ez lehetővé teszi, hogy az informatikai kiszolgáló a proxykiszolgálón keresztül kommunikáljon. A Azure Monitor for containers Agent és a Azure Monitor közötti kommunikáció HTTP-vagy HTTPS-proxykiszolgáló lehet, és a névtelen és az alapszintű hitelesítés (username/Password) is támogatott.

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

Például: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Ha a protokollt **http**-ként adta meg, a HTTP-kérelmek SSL/TLS biztonságos kapcsolat használatával jönnek létre. A proxykiszolgáló támogatja az SSL/TLS protokollokat.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha hibát tapasztal a hibrid Kubernetes-fürt figyelésének engedélyezése során, másolja a PowerShell-parancsfájlt [TroubleshootError_nonAzureK8s.ps1](https://aka.ms/troubleshoot-non-azure-k8s) , és mentse a számítógép egyik mappájába. Ez a parancsfájl az észlelt problémák észleléséhez és megoldásához nyújt segítséget. A probléma megoldásához a következő problémák észlelhetők:

- A megadott Log Analytics munkaterület érvényes
- A Log Analytics munkaterület a Azure Monitor for containers megoldáshoz van konfigurálva. Ha nem, konfigurálja a munkaterületet.
- OmsAgent REPLICASET hüvely fut
- OmsAgent daemonset elemet hüvely fut
- A OmsAgent állapotfigyelő szolgáltatás fut
- A tároló ügynökön konfigurált Log Analytics munkaterület azonosítója és kulcsa megegyezik azzal a munkaterülettel, amelyhez az Insight konfigurálva van.
- Ellenőrizze, hogy az összes linuxos feldolgozó csomópont rendelkezik `kubernetes.io/role=agent` -e címkével az RS Pod-hoz. Ha nem létezik, adja hozzá.
- A (z) érvényesítése `cAdvisor secure port:10250` vagy `unsecure port: 10255` megnyitása a fürt összes csomópontján megtörtént.

Azure PowerShell végrehajtásához használja a következő parancsokat a parancsfájlt tartalmazó mappában:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Következő lépések

Ha a figyelés engedélyezve van a hibrid Kubernetes-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-felhasználásának gyűjtéséhez, Ismerje meg, [hogyan használhatja](container-insights-analyze.md) a Azure monitor for containers szolgáltatást.
