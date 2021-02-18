---
title: Az Azure Red Hat OpenShift v3-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthatja le a Azure Monitor for containers szolgáltatással az Azure Red Hat OpenShift-fürt figyelését.
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7e6ab46940ed29a98b3988c00c92d6c691d6e0f0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621208"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-v3-cluster"></a>Az Azure Red Hat OpenShift v3-fürt figyelésének leállítása

>[!IMPORTANT]
> Az Azure Red Hat OpenShift 3,11 június 2022-én megszűnik.
>
> Október 2020-én már nem fog tudni új 3,11-fürtöket létrehozni.
> A meglévő 3,11-es fürtök továbbra is a 2022-ig fognak működni, de ezt követően már nem lesznek támogatottak.
>
> Kövesse ezt az útmutatót [egy Azure Red Hat OpenShift 4-fürt létrehozásához](../../openshift/tutorial-create-cluster.md).
> Ha konkrét kérdései vannak, vegye [fel velünk a kapcsolatot](mailto:aro-feedback@microsoft.com).

Miután engedélyezte az Azure Red Hat OpenShift 3. x verziójú fürt figyelését, leállíthatja a fürt figyelését Azure Monitor for containers használatával, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan hajthatja végre ezt a Azure Resource Manager sablonnal.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A megadott két Azure Resource Manager sablon, amely támogatja a megoldás erőforrásainak következetes és ismétlődő eltávolítását az erőforráscsoporthoz. Az egyik egy JSON-sablon, amely meghatározza, hogy a rendszer hogyan állítsa le a figyelést, a másik pedig olyan paramétereket tartalmaz, amelyeket úgy konfigurál, hogy megadja a fürt üzembe helyezett OpenShift-erőforrás-AZONOSÍTÓját és az Azure-régiót.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

### <a name="create-template"></a>Sablon létrehozása

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Mentse ezt a fájlt **OptOutTemplate.jsként** egy helyi mappába.

3. Illessze be a következő JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Szerkessze a **aroResourceId** és a **aroResourceLocation** értékeit a OpenShift-fürt értékeivel, amelyet a kiválasztott fürt **Tulajdonságok** lapján talál.

    ![Tároló tulajdonságai lap](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Mentse ezt a fájlt **OptOutParam.jsként** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

### <a name="remove-the-solution-using-azure-cli"></a>A megoldás eltávolítása az Azure CLI használatával

Futtassa a következő parancsot az Azure CLI-vel Linuxon a megoldás eltávolításához és a fürt konfigurációjának tisztításához.

```azurecli
az login   
az account set --subscription "Subscription Name"
az deployment group create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza a kapott eredményt:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>A megoldás eltávolítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Hajtsa végre a következő PowerShell-parancsokat a sablont tartalmazó mappában, hogy eltávolítsa a megoldást, és törölje a konfigurációt a fürtből.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A konfiguráció módosítása több percet is igénybe vehet. Ha elkészült, az alábbihoz hasonló üzenet jelenik meg, amely tartalmazza a kapott eredményt:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Következő lépések

Ha a munkaterület csak a fürt figyelésének támogatásához lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, tekintse meg [Az Azure log Analytics munkaterület törlése](../platform/delete-workspace.md)című témakört.
