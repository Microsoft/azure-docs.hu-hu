---
title: A fürtcsomópont Azure Kubernetes Service monitorozásának | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan szüntetheti meg az Azure AKS-fürt monitorozását a Container Insights használatával.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 619b6fc4cce860e5869fd0b31e303b4a474f8428
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774016"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-container-insights"></a>A tárolók monitorozásának Azure Kubernetes Service (AKS) a Container Insights segítségével

Miután engedélyezi az AKS-fürt monitorozását, leállíthatja a fürt monitorozását, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan valósítja meg ezt az Azure CLI vagy a rendelkezésre bocsátott Azure Resource Manager sablonokkal.  


## <a name="azure-cli"></a>Azure CLI

A Container Insights letiltásához használja az [az aks disable-addons](/cli/azure/aks#az_aks_disable_addons) parancsot. A parancs eltávolítja az ügynököt a fürtcsomópontokról, nem távolítja el a megoldást vagy a már összegyűjtött és a fürterőforrásban tárolt Azure Monitor adatokat.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

A fürt monitorozásának engedélyezéséhez lásd: [Monitorozás engedélyezése az Azure CLI használatával.](container-insights-enable-new-cluster.md#enable-using-azure-cli)

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A megadott két Azure Resource Manager támogatja a megoldás-erőforrások következetes és ismétlődő eltávolítását az erőforráscsoportban. Az egyik egy JSON-sablon, amely megadja a figyelés leállításának konfigurációját, a másik pedig olyan paraméterértékeket tartalmaz, amelyek segítségével megadhatja az AKS-fürt azon erőforrás-azonosítóját és erőforráscsoportját, amelybe a fürt telepítve van.

Ha nem ismeri az erőforrások sablonnal való üzembe helyezésének fogalmát, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése Resource Manager sablonokkal és az Azure CLI-val](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>A sablont a fürt ugyanabban az erőforráscsoportban kell üzembe helyezni. Ha a sablon használatakor kihagy bármilyen más tulajdonságot vagy bővítményt, az a fürtről való eltávolításukat eredményezheti. Például engedélyezze az *enableRBAC-t* a fürtben megvalósított Kubernetes RBAC-szabályzatok számára, vagy az *aksResourceTagValues* szabályzatot, ha meg vannak adva címkék az AKS-fürthöz.  
>

Ha az Azure CLI használatát választja, először helyileg kell telepítenie és használnia a CLI-t. Az Azure CLI 2.0.27-es vagy újabb verziójával kell futnia. A verzió azonosításához futtassa az `az --version` -t. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [az Azure CLI telepítését.](/cli/azure/install-azure-cli)

### <a name="create-template"></a>Sablon létrehozása

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
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Mentse ezt a fájlt **OptOutTemplate.jsegy** helyi mappába.

3. Illessze be az alábbi JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
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

4. Szerkessze **az aksResourceId** és **az aksResourceLocation** értékeit az AKS-fürt értékeinek használatával, amelyeket a kiválasztott fürt **Tulajdonságok** lapján talál.

    ![Tárolótulajdonságok lap](media/container-insights-optout/container-properties-page.png)

    Amíg a Tulajdonságok **lapon** van, másolja a munkaterület **erőforrás-azonosítóját is.** Erre az értékre akkor van szükség, ha később törölni szeretné a Log Analytics-munkaterületet. A Log Analytics-munkaterület törlése nem a folyamat részeként történik.

    Szerkessze **az aksResourceTagValues** értékeit úgy, hogy megegyeznek az AKS-fürthöz megadott meglévő címkeértékekkel.

5. Mentse ezt a fájlt **OptOutParam.jsegy** helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

### <a name="remove-the-solution-using-azure-cli"></a>A megoldás eltávolítása az Azure CLI használatával

A megoldás eltávolításához és az AKS-fürtön a konfiguráció eltávolításához hajtsa végre a következő parancsot az Azure CLI-val Linux rendszeren.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A konfiguráció módosítása eltarthat néhány percig. Ha elkészült, a rendszer az alábbihoz hasonló üzenetet ad vissza, amely tartalmazza az eredményt:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>A megoldás eltávolítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Hajtsa végre a következő PowerShell-parancsokat a sablont tartalmazó mappában a megoldás eltávolításához és az AKS-fürt konfigurációjának eltávolításához.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A konfiguráció módosítása eltarthat néhány percig. Ha elkészült, a rendszer az alábbihoz hasonló üzenetet ad vissza, amely tartalmazza az eredményt:

```output
ProvisioningState       : Succeeded
```


## <a name="next-steps"></a>Következő lépések

Ha a munkaterület csak a fürt monitorozásának támogatására lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem jártas a munkaterületek törlésében, tekintse meg Az Azure Log Analytics-munkaterület törlése a [Azure Portal.](../logs/delete-workspace.md) Ne feledkezzünk meg  a 4. lépésben másolt munkaterületi erőforrás-azonosítóról. Erre szüksége lesz.