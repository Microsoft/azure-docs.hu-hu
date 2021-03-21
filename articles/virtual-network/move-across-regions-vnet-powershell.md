---
title: Azure-beli virtuális hálózat áthelyezése egy másik Azure-régióba Azure PowerShell használatával
description: Azure-beli virtuális hálózat áthelyezése egyik Azure-régióból a másikba egy Resource Manager-sablon és Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fa89a458b23d18bc6fddfca9cf6d9f2a16f91669
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934926"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Azure-beli virtuális hálózat áthelyezése egy másik régióba Azure PowerShell használatával

A meglévő Azure-beli virtuális hálózatok egyik régióból a másikba való áthelyezésének különböző forgatókönyvei vannak. Előfordulhat például, hogy egy virtuális hálózatot szeretne létrehozni ugyanazzal a konfigurációval a teszteléshez és a rendelkezésre álláshoz, mint a meglévő virtuális hálózatot. Vagy előfordulhat, hogy a vész-helyreállítási terv részeként egy éles virtuális hálózatot szeretne áthelyezni egy másik régióba.

A virtuális hálózat áthelyezését egy másik régióba Azure Resource Manager sablon használatával végezheti el. Ezt úgy teheti meg, hogy a virtuális hálózatot sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban. A Resource Manager-sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy a virtuális hálózat abban az Azure-régióban található, amelyet át szeretne helyezni.

- Virtuális hálózat exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő virtuális hálózat létrehozásához szükség van a hálózati közreműködő szerepkörre vagy magasabbra.

- A virtuális hálózati partnerek nem jönnek létre újra, és sikertelenek lesznek, ha még jelen vannak a sablonban. A sablon exportálása előtt el kell távolítania minden virtuális hálózati társat. Ezután újra létrehozhatók a virtuális hálózat áthelyezése után.
    
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a nyilvános IP-címek számára.

- Ellenőrizze, hogy az Azure-előfizetése lehetővé teszi-e a virtuális hálózatok létrehozását a célcsoportban. A szükséges kvóta engedélyezéséhez forduljon az ügyfélszolgálathoz.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a virtuális hálózatok ezen folyamathoz való hozzáadásának támogatásához. További információk: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-for-the-move"></a>Felkészülés az áthelyezésre
Ebben a szakaszban egy Resource Manager-sablon használatával készíti elő a virtuális hálózatot az áthelyezéshez. Ezután Azure PowerShell parancsok használatával helyezheti át a virtuális hálózatot a célként megadott régióba.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A virtuális hálózat exportálásához és a cél virtuális hálózat PowerShell használatával történő üzembe helyezéséhez tegye a következőket:

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, majd kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. Szerezze be annak a virtuális hálózatnak az erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, majd helyezze egy változóba a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)használatával:

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. Exportálja a forrásként szolgáló virtuális hálózatot egy. JSON-fájlba abban a könyvtárban, ahol az [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. A letöltött fájl neve megegyezik azzal az erőforrás-csoporttal, amelyben az erőforrást exportálták. Keresse meg a paranccsal exportált *\<resource-group-name> . JSON* fájlt, majd nyissa meg a szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. A virtuális hálózat nevének paraméterének szerkesztéséhez módosítsa a forrás virtuális hálózat nevének **defaultValue** tulajdonságát a célként megadott virtuális hálózat nevére. Ügyeljen arra, hogy idézőjelek közé foglalja a nevet.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. A virtuális hálózatot áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot az erőforrások területen.

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
1. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. Választható A további paramétereket a *\<resource-group-name> . JSON* fájlban is módosíthatja, a követelményektől függően:

    * **Címterület**: a fájl mentése előtt megváltoztathatja a virtuális hálózat addressSpace, ha módosítja az **erőforrások**  >   szakaszát, és megváltoztatja a **addressPrefixes** tulajdonságot:

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },
        ```

    * **Alhálózat**: a fájl **alhálózatai** szakaszának módosításával módosíthatja vagy hozzáadhatja az alhálózat nevét és az alhálózati címtartomány méretét. A **Name (név** ) tulajdonság módosításával módosíthatja az alhálózat nevét. Az alhálózati címterület a **addressPrefix** tulajdonság módosításával is módosítható:

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

        A címzési előtag módosításához két helyen szerkessze a fájlt: az előző szakasz kódjában és a következő kód Type ( **típus** ) szakaszában. Módosítsa a **addressPrefix** tulajdonságot a következő kódban, hogy az megfeleljen az előző szakaszban található kódban szereplő **addressPrefix** tulajdonságnak.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

1. Mentse a *\<resource-group-name> . JSON* fájlt.

1. Hozzon létre egy erőforráscsoportot a cél régióban a cél virtuális hálózat [új-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával történő telepítéséhez:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. Telepítse a szerkesztett *\<resource-group-name> . JSON* fájlt az előző lépésben létrehozott erőforráscsoporthoz a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. A [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) és a [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)használatával ellenőrizheti, hogy létrejöttek-e az erőforrások a célként megadott régióban:
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>A virtuális hálózat vagy az erőforráscsoport törlése 

A virtuális hálózat üzembe helyezését követően a virtuális hálózat megkezdéséhez vagy elvetéséhez törölje a megcélzott régióban létrehozott erőforráscsoportot, és a rendszer törli az áthelyezett virtuális hálózatot. 

Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások véglegesítéséhez és a virtuális hálózat áthelyezésének befejezéséhez tegye a következők egyikét:

* Törölje az erőforráscsoportot a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)használatával:

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* Törölje a forrás virtuális hálózatot a [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)használatával:  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban áthelyezett egy virtuális hálózatot az egyik régióból a másikba a PowerShell használatával, majd megtisztította a szükségtelen források forrásait. Ha többet szeretne megtudni a régiók és a vész-helyreállítási erőforrások közötti áthelyezésről az Azure-ban, tekintse meg a következőket:

- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)
