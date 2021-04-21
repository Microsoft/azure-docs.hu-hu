---
title: Hibrid Kubernetes-fürtök konfigurálása a Container Insights | Microsoft Docs
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Container Insightsot a környezetben vagy más környezetben üzemeltetett Kubernetes-Azure Stack monitorozni.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 90a4c14397df8e70fc8f3d88bc339f826bb1ccc9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767022"
---
# <a name="configure-hybrid-kubernetes-clusters-with-container-insights"></a>Hibrid Kubernetes-fürtök konfigurálása a Container Insights segítségével

A Container Insights gazdag monitorozási élményt biztosít az Azure Azure Kubernetes Service [(AKS) és az AKS Engine](https://github.com/Azure/aks-engine)szolgáltatáshoz, amely egy, az Azure-ban üzemeltetett, ön által felügyelt Kubernetes-fürt. Ez a cikk azt ismerteti, hogyan engedélyezhető az Azure-ban üzemeltetett Kubernetes-fürtök monitorozása, és hogyan lehet hasonló monitorozási élményt elérni.

## <a name="supported-configurations"></a>Támogatott konfigurációk

A Container Insights hivatalosan az alábbi konfigurációkat támogatja. Ha a Kubernetes és az operációs rendszer eltérő verziójával van, küldjön egy e-mailt a askcoin@microsoft.com számára.

- Környezetben:

    - Helyszíni Kubernetes
    - AKS Engine az Azure-ban és Azure Stack. További információ: [AKS Engine on Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview)
    - [Az OpenShift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) 4-es vagy újabb verziója, helyszíni vagy egyéb felhőalapú környezetek.

- A Kubernetes és a támogatási szabályzat verziói ugyanazok, mint a támogatott [AKS-verziók.](../../aks/supported-kubernetes-versions.md)

- A következő tároló-futtatók támogatottak: Docker-, Moby- és CRI-kompatibilis futásidejűk, például CRI-O és ContainerD.

- A támogatott fő és munkavégző csomópontok Linux operációsrendszer-kiadása a következő: Ubuntu (18.04 LTS és 16.04 LTS) és Red Hat Enterprise Linux CoreOS 43.81.

- A hozzáférés-vezérlés támogatott: Kubernetes RBAC és nem RBAC

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy az alábbiakkal is:

- Egy [Log Analytics-munkaterület](../logs/design-logs-deployment.md).

    A tárolóelemzések támogatják a Log Analytics-munkaterületeket az Azure-termékek régiók szerint [listában felsorolt régiókban.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Saját munkaterület létrehozásához létrehozható a [](../logs/resource-manager-workspace.md)következő Azure Resource Manager, a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)használatával, vagy a [Azure Portal.](../logs/quick-create-workspace.md)

    >[!NOTE]
    >Az azonos fürtnévvel rendelkező fürtök ugyanazon Log Analytics-munkaterületre való figyelése nem támogatott. A fürtneveknek egyedinek kell lennie.
    >

- Ön a Log **Analytics közreműködői szerepkörének tagja** a tároló monitorozásának engedélyezéséhez. A Log Analytics-munkaterülethez való hozzáférés szabályozásával kapcsolatos további információkért lásd: Munkaterülethez és naplóadatokhoz való [hozzáférés kezelése.](../logs/manage-access.md)

- A monitorozási adatok megtekintéséhez [*Log Analytics-olvasó*](../logs/manage-access.md#manage-access-using-azure-permissions) szerepkörre van szükség a Log Analytics-munkaterületen, a Container Insights használatával konfigurálva.

- [HELM-ügyfél](https://helm.sh/docs/using_helm/) a megadott Kubernetes-fürt tárolóelemzési diagramának a beszúrása érdekében.

- Az alábbi proxy- és tűzfal-konfigurációs információk szükségesek ahhoz, hogy a Linuxhoz való Log Analytics-ügynök tárolóba tároló verziója kommunikáljon a Azure Monitor:

    |Ügynök erőforrása|Portok |
    |------|---------|
    |*.ods.opinsights.azure.com |443-as port |
    |*.oms.opinsights.azure.com |443-as port |
    |*.dc.services.visualstudio.com |443-as port |

- A tárolóba tároló ügynöknek a Kubelet vagy a fürt összes csomópontján meg kell nyitnia a `cAdvisor secure port: 10250` `unsecure port :10255` teljesítménymetrikák gyűjtéséhez. Ha még nincs konfigurálva, javasoljuk, hogy konfigurálja a konfigurációt a `secure port: 10250` Kubelet cAkb-ában.

- A tárolóba tároló ügynöknek a következő környezeti változókat kell megadnia a tárolón ahhoz, hogy kommunikáljon a fürtön belüli Kubernetes API szolgáltatással a leltáradatok gyűjtéséhez – `KUBERNETES_SERVICE_HOST` és `KUBERNETES_PORT_443_TCP_PORT` .

>[!IMPORTANT]
>A hibrid Kubernetes-fürtök monitorozása esetén támogatott minimális ügynökverzió a ciprod10182019 vagy újabb.

## <a name="enable-monitoring"></a>Monitorozás engedélyezése

A hibrid Kubernetes-fürtön a Container Insights engedélyezése a következő lépések sorrendben való elvégzéséből áll.

1. Log Analytics-munkaterület konfigurálása Container Insights-megoldással.   

2. Engedélyezze a Container Insights HELM-diagramot a Log Analytics-munkaterületen.

A monitorozási megoldásokkal kapcsolatos további Azure Monitor itt [talál.](../../azure-monitor/insights/solutions.md)

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Az Azure Monitor containers megoldás hozzáadása

A megoldást a megadott sablonnal helyezheti Azure Resource Manager a Azure PowerShell parancsmaggal vagy az `New-AzResourceGroupDeployment` Azure CLI-val.

Ha nem ismeri az erőforrások sablonnal való üzembe helyezésének fogalmát, tekintse meg a következőt:

- [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

- [Erőforrások üzembe helyezése Resource Manager sablonokkal és az Azure CLI-val](../../azure-resource-manager/templates/deploy-cli.md)

Ha az Azure CLI használatát választja, először helyileg kell telepítenie és használnia a CLI-t. Az Azure CLI 2.0.59-es vagy újabb verziójának kell futnia. A verzió azonosításához futtassa az `az --version` -t. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon megadja a figyelés engedélyezéséhez szükséges konfigurációt, a másik pedig olyan paraméterértékeket tartalmaz, amelyek konfigurálva vannak a következők megadásához:

- **workspaceResourceId** – a Log Analytics-munkaterület teljes erőforrás-azonosítója.
- **workspaceRegion** – az a régió, amelyben a munkaterület  létrejön, amelyet a munkaterület tulajdonságai között Hely néven is Azure Portal.

AcontainerSolutionParams.json file paraméterértékéhez szükséges Log Analytics-munkaterület teljes erőforrás-azonosítójának azonosításához hajtsa végre az alábbi lépéseket, majd futtassa a PowerShell-parancsmagot vagy az Azure CLI-parancsot a `workspaceResourceId` megoldás hozzáadásához. 

1. Sorolja fel az összes olyan előfizetést, amelyekhez hozzáféréssel rendelkezik az alábbi paranccsal:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőre fog hasonlítani:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   0fb60ef2-03cc-4290-b595-e71108e8f4ce  Enabled  True
    ```

    Másolja ki a **SubscriptionId értékét.**

2. Váltson a Log Analytics-munkaterületet üzemeltető előfizetésre a következő paranccsal:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. Az alábbi példa az előfizetésében található munkaterületek listáját jeleníti meg az alapértelmezett JSON formátumban.

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics-munkaterület teljes erőforrás-azonosítóját a **mezőazonosító alá.**

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

5. Mentse ezt a fájlt containerSolution.jshelyi mappába.

6. Illessze be az alábbi JSON-szintaxist a fájlba:

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

7. Szerkessze a **workspaceResourceId** értékét a 3. lépésben másolt értékkel, a **workspaceRegion** esetében pedig másolja a **Region** értéket az [az monitor log-analytics workspace show Azure CLI-parancs](/cli/azure/monitor/log-analytics/workspace#az_monitor-log-analytics-workspace-list&preserve-view=true)futtatása után.

8. Mentse ezt a fájlt containerSolutionParams.jsegy helyi mappába.

9. Készen áll a sablon üzembe helyezésére.

   - A sablont Azure PowerShell a következő parancsokkal helyezheti üzembe a sablont tartalmazó mappában:

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

       A konfiguráció módosítása eltarthat néhány percig. Ha elkészült, megjelenik egy üzenet, amely az alábbihoz hasonló, és tartalmazza az eredményt:

       ```powershell
       provisioningState       : Succeeded
       ```

   - Az Azure CLI-val való üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A konfiguráció módosítása eltarthat néhány percig. Ha elkészült, megjelenik egy üzenet, amely az alábbihoz hasonló, és tartalmazza az eredményt:

       ```azurecli
       provisioningState       : Succeeded
       ```

       A monitorozás engedélyezése után a fürt állapotmetrikák megtekintése körülbelül 15 percet is igénybe vehet.

## <a name="install-the-helm-chart"></a>A HELM-diagram telepítése

Ebben a szakaszban a Container Insights tárolóba helyeznie kell az ügynököt. A folytatás előtt meg kell határoznia a paraméterhez szükséges munkaterület-azonosítót és a paraméterhez szükséges `omsagent.secret.wsid` elsődleges `omsagent.secret.key` kulcsot. Ezeket az információkat a következő lépések elvégzésével azonosíthatja, majd a parancsok futtatásával telepítheti az ügynököt a HELM-diagram használatával.

1. Futtassa a következő parancsot a munkaterület azonosítójának azonosításához:

    `az monitor log-analytics workspace list --resource-group <resourceGroupName>`

    A kimenetben keresse meg a munkaterület nevét a mezőnév **alatt,** majd másolja a log Analytics-munkaterület munkaterület-azonosítóját a **customerID mezőbe.**

2. Futtassa a következő parancsot a munkaterület elsődleges kulcsának azonosításához:

    `az monitor log-analytics workspace get-shared-keys --resource-group <resourceGroupName> --workspace-name <logAnalyticsWorkspaceName>`

    A kimenetben keresse meg az elsődleges kulcsot a **primarySharedKey** mező alatt, majd másolja ki az értéket.

>[!NOTE]
>Az alábbi parancsok csak a Helm 2-es verziójára vonatkoznak. A paraméter `--name` használata nem alkalmazható a Helm 3-as verziójára. 

>[!NOTE]
>Ha a Kubernetes-fürt proxykiszolgálón keresztül kommunikál, konfigurálja a paramétert a `omsagent.proxy` proxykiszolgáló URL-címével. Ha a fürt nem proxykiszolgálón keresztül kommunikál, akkor ezt a paramétert nem kell megadnia. További információt a cikk [későbbi, Proxyvégpont konfigurálásacíme](#configure-proxy-endpoint) tartalmaz.

3. Adja hozzá az Azure-diagramok adattárát a helyi listához a következő parancs futtatásával:

    ```
    helm repo add microsoft https://microsoft.github.io/charts/repo
    ````

4. Telepítse a diagramot a következő parancs futtatásával:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<my_prod_cluster> microsoft/azuremonitor-containers
    ```

    Ha a Log Analytics-munkaterület a Azure China 21Vianet, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Ha a Log Analytics-munkaterület az Azure US Governmentben található, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<logAnalyticsWorkspaceId>,omsagent.secret.key=<logAnalyticsWorkspaceKey>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

### <a name="enable-the-helm-chart-using-the-api-model"></a>Helm-diagram engedélyezése az API-modell használatával

Megadhat egy bővítményt az AKS Engine fürtspecifikációs JSON-fájljában, más néven AZ API-modellben. Ebben a bővítményben adja meg annak a Log Analytics-munkaterületnek a base64 kódolású verzióját és verzióját, ahol az összegyűjtött monitorozási adatok `WorkspaceGUID` `WorkspaceKey` tárolva vannak. A és a et az előző `WorkspaceGUID` `WorkspaceKey` szakaszban található 1. és 2. lépéssel találhatja meg.

A fürthöz támogatott API Azure Stack Hub definíciókat ebben a példában, a következő [kubernetes-container-monitoring_existing_workspace_id_and_key.jstalál:](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Pontosabban keresse meg **az addons tulajdonságot** a **kubernetesConfig fájlban:**

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

## <a name="configure-agent-data-collection"></a>Ügynökadatok gyűjtésének konfigurálása

Az 1.0.0-s diagramverzióra nézve az ügynök adatgyűjtési beállításait a ConfigMap parancs vezérli. Az ügynök adatgyűjtési beállításaival kapcsolatos dokumentációt itt [találhatja.](container-insights-agent-config.md)

A diagram sikeres üzembe helyezése után áttekintheti a hibrid Kubernetes-fürt adatait a tárolóelemzési Azure Portal.  

>[!NOTE]
>Az ügynöktől az Azure Log Analytics-munkaterületen való véglegesítésig körülbelül 5–10 perc késéssel kell eltelni. A fürt állapota a **Nincs**  adat vagy ismeretlen értéket mutatja, amíg az összes szükséges monitorozási adat elérhetővé nem válik a Azure Monitor.

## <a name="configure-proxy-endpoint"></a>Proxyvégpont konfigurálása

A 2.7.1-es verziótól kezdődően a diagram támogatja a proxyvégpont megadását a `omsagent.proxy` diagramparaméterrel. Ez lehetővé teszi, hogy a proxykiszolgálón keresztül kommunikáljon. A Container Insights-ügynök és a Azure Monitor közötti kommunikáció LEHET HTTP- vagy HTTPS-proxykiszolgáló, és a névtelen és az alapszintű hitelesítés (felhasználónév/jelszó) is támogatott.

A proxykonfiguráció értéke a következő szintaxissal rendelkezik: `[protocol://][user:password@]proxyhost[:port]`

> [!NOTE]
>Ha a proxykiszolgáló nem igényel hitelesítést, akkor is meg kell adnia egy psuedo felhasználónevet/jelszót. Ez bármilyen felhasználónév vagy jelszó lehet.

|Tulajdonság| Leírás |
|--------|-------------|
|Protokoll | http vagy https |
|felhasználó! | Nem kötelező felhasználónév a proxyhitelesítéshez |
|jelszó | Nem kötelező jelszó a proxyhitelesítéshez |
|proxyhost | A proxykiszolgáló címe vagy teljes tartománya |
|port | Nem kötelező portszám a proxykiszolgálóhoz |

Például: `omsagent.proxy=http://user01:password@proxy01.contoso.com:8080`

Ha a protokollt HTTP-ként adja meg, a HTTP-kérések SSL/TLS biztonságos kapcsolat használatával jön létre. A proxykiszolgálónak támogatnia kell az SSL/TLS protokollokat.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha hibába ütközik, amikor megpróbálja engedélyezni a hibrid Kubernetes-fürt [](https://aka.ms/troubleshoot-non-azure-k8s) monitorozását, másolja a PowerShell-TroubleshootError_nonAzureK8s.ps1, és mentse a számítógép egyik mappájába. Ez a szkript segít a tapasztalt problémák észlelésében és kijavításában. A következő problémák észlelésére és javítására tervezték őket:

- A megadott Log Analytics-munkaterület érvényes
- A Log Analytics-munkaterület a Container Insights megoldással van konfigurálva. Ha nem, konfigurálja a munkaterületet.
- Az OmsAgent replikakészlet podja fut
- Az OmsAgent démonkészlet podok futnak
- Az OmsAgent Health szolgáltatás fut
- A tárolóba került ügynökön konfigurált Log Analytics-munkaterület azonosítója és kulcsa megegyezik azzal a munkaterülettel, amelyhez az Insight konfigurálva van.
- Ellenőrizze, hogy az összes Linux feldolgozó csomópontnak van-e `kubernetes.io/role=agent` címkéje az rs pod ütemezése során. Ha még nem létezik, adja hozzá.
- Ellenőrizze, `cAdvisor secure port:10250` hogy a fürt összes `unsecure port: 10255` csomópontján meg van-e nyitva, vagy meg van nyitva.

A Azure PowerShell szkriptet tartalmazó mappában használja a következő parancsokat:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>Következő lépések

Ha engedélyezve van a monitorozás a hibrid Kubernetes-fürt és a rajta futó számítási feladatok állapotának és erőforrás-felhasználásának gyűjtéséhez, ismerje meg a [Container](container-insights-analyze.md) Insights használatát.