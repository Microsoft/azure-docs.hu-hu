---
title: Az Azure Kubernetes Service (ak)-fürt üzembe helyezésének figyelése | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti az előfizetésében már üzembe helyezett Azure Kubernetes-szolgáltatási (ak-) fürtök figyelését.
ms.topic: conceptual
ms.date: 09/12/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: fba12fb8dd5c6c91883a88d4acfdca6c6d332c24
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105109624"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>A már üzembe helyezett Azure Kubernetes Service-(ak-) fürt figyelésének engedélyezése

Ez a cikk azt ismerteti, hogyan állítható be a tároló-bepillantást az előfizetésében már üzembe helyezett, az [Azure Kubernetes szolgáltatásban](../../aks/index.yml) üzemeltetett felügyelt Kubernetes-fürtök figyelésére.

Engedélyezheti a már üzembe helyezett AK-fürtök figyelését a támogatott módszerek egyikével:

* Azure CLI
* Terraform
* [Azure monitor](#enable-from-azure-monitor-in-the-portal) vagy közvetlenül a Azure Portal [AK-fürtjéből](#enable-directly-from-aks-cluster-in-the-portal)
* A [megadott Azure Resource Manager sablonnal](#enable-using-an-azure-resource-manager-template) a Azure PowerShell parancsmaggal vagy az `New-AzResourceGroupDeployment` Azure CLI használatával.

Ha egy meglévő AK-fürtöt egy másik előfizetésben lévő Azure Log Analytics-munkaterülethez csatlakoztat, a Microsoft. Tárolószolgáltatás erőforrás-szolgáltatót regisztrálni kell ahhoz az előfizetéshez, amelyben a Log Analytics munkaterület létrejött. További információ: az [erőforrás-szolgáltató regisztrálása](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-using-azure-cli"></a>Engedélyezés az Azure CLI használatával

A következő lépés lehetővé teszi az AK-fürt figyelését az Azure CLI használatával. Ebben a példában nem kell előzetesen létrehoznia vagy megadnia egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot azáltal, hogy létrehoz egy alapértelmezett munkaterületet az AK-fürt előfizetés alapértelmezett erőforráscsoporthoz, ha az egyik még nem létezik a régióban.  A létrehozott alapértelmezett munkaterület a *alapértelmezettmunkaterület \<GUID> - \<Region>* formátumához hasonlít.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

A kimenet a következőhöz hasonló lesz:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integrálás meglévő munkaterülettel

Ha inkább egy meglévő munkaterületet szeretne integrálni, hajtsa végre a következő lépéseket a paraméterhez szükséges Log Analytics munkaterület teljes erőforrás-AZONOSÍTÓjának megadásához `--workspace-resource-id` , majd futtassa a parancsot, hogy engedélyezze a figyelési bővítményt a megadott munkaterületen.

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáféréssel rendelkezik a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőhöz hasonló lesz:

    ```output
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
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

4. Futtassa a következő parancsot a figyelési bővítmény engedélyezéséhez, és cserélje le a paraméter értékét `--workspace-resource-id` . A karakterlánc értékének idézőjelek közé kell esnie:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    A kimenet a következőhöz hasonló lesz:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Engedélyezés a Terraform használatával

1. Adja hozzá a **oms_agent** kiegészítő profilt a meglévő [azurerm_kubernetes_cluster erőforráshoz](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adja hozzá a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a Terraform dokumentációjának lépéseit követve.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Engedélyezés Azure Monitor a portálon

Ha az AK-fürt figyelését szeretné engedélyezni a Azure Monitor Azure Portalban, tegye a következőket:

1. A Azure Portal válassza a **figyelő** elemet.

2. Válassza ki a **tárolókat** a listából.

3. A **figyelő-tárolók** lapon válassza a nem **figyelt fürtök** lehetőséget.

4. A nem figyelt fürtök listájában keresse meg a tárolót a listában, és kattintson az **Engedélyezés** gombra.

5. Ha már rendelkezik egy meglévő Log Analytics munkaterülettel ugyanabban az előfizetésben, mint a fürt, a bevezetéskor a **Container** bevezetése lapon válassza ki azt a legördülő listából.
    A lista előadja az alapértelmezett munkaterületet és helyet, amelyet az AK-tároló üzembe helyez az előfizetésben.

    ![AK-tárolók adatellenőrzésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse az [log Analytics munkaterület létrehozása](../logs/quick-create-workspace.md)című témakör utasításait. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyben az AK-tároló telepítve van.

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Közvetlenül az AK-fürtből történő engedélyezés a portálon

Ha közvetlenül szeretné engedélyezni a figyelést a Azure Portal egyik AK-fürtjéből, tegye a következőket:

1. A Azure Portal válassza a **minden szolgáltatás** lehetőséget.

2. Az erőforrások listájában kezdje el begépelni a **tárolókat**.  A lista a bemenet alapján szűri a szűrőket.

3. Válassza a **Kubernetes Services** elemet.
    
4. A Kubernetes szolgáltatások listájában válasszon ki egy szolgáltatást.

5. A Kubernetes szolgáltatás áttekintése lapon válassza a **figyelés-** információk elemet.

6. Ha egy meglévő Log Analytics munkaterülettel rendelkezik a fürttel azonos előfizetésben, válassza ki **azt a** legördülő listából.
    A lista előadja az alapértelmezett munkaterületet és helyet, amelyet az AK-tároló üzembe helyez az előfizetésben.

    ![AK-tároló állapot-figyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse az [log Analytics munkaterület létrehozása](../logs/quick-create-workspace.md)című témakör utasításait. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyben az AK-tároló telepítve van.

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt működési adatait.

## <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager sablon használatával

Ez a metódus két JSON-sablont tartalmaz. Az egyik sablon meghatározza a figyelés engedélyezésének konfigurációját, a másik pedig a konfigurált paramétereket az alábbiak megadásához:

* Az AK-tároló erőforrás-azonosítója.
* Az az erőforráscsoport, amelyben a fürt telepítve van.

>[!NOTE]
>A sablont a fürttel azonos erőforráscsoporthoz kell telepíteni.
>

A Log Analytics munkaterületet létre kell hozni, mielőtt engedélyezi a figyelést a Azure PowerShell vagy a parancssori felület használatával. A munkaterület létrehozásához a [Azure Resource Manager](../logs/resource-manager-workspace.md), a [PowerShell](../logs/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy a [Azure Portal](../logs/quick-create-workspace.md)használatával állíthatja be.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.59 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

### <a name="create-and-execute-a-template"></a>Sablon létrehozása és végrehajtása

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "type": "string",
          "metadata": {
            "description": "AKS Cluster Resource ID"
          }
        },
        "aksResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the AKS resource e.g. \"East US\""
          }
        },
        "aksResourceTagValues": {
          "type": "object",
          "metadata": {
            "description": "Existing all tags on AKS Cluster Resource"
          }
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
          }
        }
      },
      "resources": [
        {
          "name": "[split(parameters('aksResourceId'),'/')[8]]",
          "type": "Microsoft.ContainerService/managedClusters",
          "location": "[parameters('aksResourceLocation')]",
          "tags": "[parameters('aksResourceTagValues')]",
          "apiVersion": "2018-03-31",
          "properties": {
            "mode": "Incremental",
            "id": "[parameters('aksResourceId')]",
            "addonProfiles": {
              "omsagent": {
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Mentse ezt a fájlt **existingClusterOnboarding.jsként** egy helyi mappába.

3. Illessze be a következő JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Szerkessze a **aksResourceId** és a **aksResourceLocation** értékeit az AK-fürt **AK-áttekintés** lapján található értékek használatával. A **workspaceResourceId** értéke a log Analytics munkaterület teljes erőforrás-azonosítója, amely tartalmazza a munkaterület nevét.

    Szerkessze a **aksResourceTagValues** értékeit, hogy azok megfeleljenek az AK-fürthöz megadott meglévő címkék értékének.

5. Mentse ezt a fájlt **existingClusterParam.jsként** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

   * A Azure PowerShell használatával történő üzembe helyezéshez használja az alábbi parancsokat a sablont tartalmazó mappában:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

       ```output
       provisioningState       : Succeeded
       ```

   * Az Azure CLI-vel való üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az deployment group create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, egy üzenet jelenik meg, amely a következőhöz hasonló, és az eredményt tartalmazza:

       ```output
       provisioningState       : Succeeded
       ```

       A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait.

## <a name="verify-agent-and-solution-deployment"></a>Ügynök és megoldás központi telepítésének ellenőrzése

Az ügynök *06072018* -es vagy újabb verziójával ellenőrizheti, hogy az ügynök és a megoldás telepítése is sikeres volt-e. Az ügynök korábbi verzióiban csak az ügynök üzembe helyezését ellenőrizheti.

### <a name="agent-version-06072018-or-later"></a>Ügynök 06072018-es vagy újabb verziója

A következő parancs futtatásával ellenőrizheti, hogy az ügynök telepítése sikeresen megtörtént-e.

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

Ha a fürtben Windows Server-csomópontok vannak, akkor a következő parancs futtatásával ellenőrizheti, hogy az ügynök telepítése sikeresen megtörtént-e.

```
kubectl get ds omsagent-win --namespace=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

```output
User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
```

A megoldás üzembe helyezésének ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Ügynök verziója 06072018-nál korábbi

A következő parancs futtatásával ellenőrizheti, hogy a Log Analytics ügynöknek a *06072018* -es verziójának megfelelő telepítését adta-e meg:

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése a parancssori felülettel

A `aks show` paranccsal olyan részleteket kaphat, mint például a megoldás, amely a log Analytics munkaterület resourceID, valamint a fürt összegző részleteit is lehetővé teszi.

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc elteltével a parancs befejeződik, és a megoldáshoz tartozó JSON-formátumú adatokat adja vissza.  A parancs eredményeinek meg kell jeleníteniük a figyelési bővítmény profilját, és a következő példában szereplő kimenetre hasonlítanak:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Következő lépések

* Ha problémákat tapasztal a megoldás bevezetésére tett kísérlet során, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .

* A figyelés lehetővé teszi, hogy az AK-fürt és a rajtuk futó munkaterhelések állapota és Erőforrás-kihasználtsága összegyűjtse a tároló-információk [használatát](container-insights-analyze.md) .
