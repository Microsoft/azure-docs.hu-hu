---
title: Az Azure Kubernetes Service-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan szüntetheti meg az Azure AK-fürt figyelését a tárolók Azure Monitorával.
ms.topic: conceptual
ms.date: 08/19/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 476934d84d8ffc63336ec620432db3507b2cbb34
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100616188"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Az Azure Kubernetes Service (AKS) tárolókhoz készült Azure Monitorral való monitorozásának leállítása

Miután engedélyezte az AK-fürt figyelését, leállíthatja a fürt figyelését, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan hajthatja végre ezt az Azure CLI vagy a megadott Azure Resource Manager sablonok használatával.  


## <a name="azure-cli"></a>Azure CLI

A tárolók Azure Monitor letiltásához használja az az [AK disable-addons](/cli/azure/aks#az-aks-disable-addons) parancsot. A parancs eltávolítja az ügynököt a fürtcsomópontokből, nem távolítja el a megoldást, vagy a már összegyűjtött és a Azure Monitor erőforrásban tárolt adatokat.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

A fürt figyelésének újbóli engedélyezéséhez tekintse meg a [figyelés engedélyezése az Azure CLI használatával](container-insights-enable-new-cluster.md#enable-using-azure-cli)című témakört.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A megadott két Azure Resource Manager sablon, amely támogatja a megoldás erőforrásainak következetes és ismétlődő eltávolítását az erőforráscsoporthoz. Az egyik egy JSON-sablon, amely meghatározza, hogy a rendszer hogyan állítsa le a figyelést, a másik pedig a konfigurált paraméterek értékeit, amelyekkel megadhatja azt az AK-fürterőforrás-azonosítót és erőforráscsoportot, amelyben a fürt telepítve van.

Ha nem ismeri az erőforrások sablon használatával történő központi telepítésének fogalmát, tekintse meg a következőt:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>A sablont a fürt azonos erőforráscsoporthoz kell telepíteni. Ha a sablon használatakor kihagyja a többi tulajdonságot vagy bővítményt, akkor azt eredményezheti a fürtből való eltávolításuk. Például *enableRBAC* a fürtben megvalósított Kubernetes RBAC-házirendekhez, vagy *aksResourceTagValues* , ha CÍMKÉKET ad meg az AK-fürthöz.  
>

Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.27 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

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

2. Mentse ezt a fájlt **OptOutTemplate.jsként** egy helyi mappába.

3. Illessze be a következő JSON-szintaxist a fájlba:

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

4. Szerkessze a **aksResourceId** és a **aksResourceLocation** értékeit az AK-fürt értékeivel, amelyet a kiválasztott fürt **Tulajdonságok** lapján talál.

    ![Tároló tulajdonságai lap](media/container-insights-optout/container-properties-page.png)

    A **Tulajdonságok** lapon a **munkaterület erőforrás-azonosítóját** is másolja. Erre az értékre akkor van szükség, ha később szeretné törölni az Log Analytics munkaterületet. A Log Analytics munkaterület törlése nem a folyamat részeként történik.

    Szerkessze a **aksResourceTagValues** értékeit, hogy azok megfeleljenek az AK-fürthöz megadott meglévő címkék értékének.

5. Mentse ezt a fájlt **OptOutParam.jsként** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

### <a name="remove-the-solution-using-azure-cli"></a>A megoldás eltávolítása az Azure CLI használatával

Futtassa a következő parancsot az Azure CLI-vel Linuxon a megoldás eltávolításához, és törölje a konfigurációt az AK-fürtön.

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

Hajtsa végre a következő PowerShell-parancsokat a sablont tartalmazó mappában, és távolítsa el a megoldást, és törölje a konfigurációt az AK-fürtből.    

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

Ha a munkaterület csak a fürt figyelésének támogatásához lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterületek törlésének módját, tekintse meg az [Azure log Analytics munkaterület törlése a Azure Portal](../platform/delete-workspace.md)használatával című témakört. Ne felejtse el a 4. lépésben korábban átmásolt **munkaterület-erőforrás azonosítóját** , amelyre szüksége lesz.
