---
title: Azure hálózati biztonsági csoport (NSG) áthelyezése egy másik Azure-régióba Azure PowerShell használatával
description: Azure Resource Manager sablonnal áthelyezheti az Azure hálózati biztonsági csoportot az egyik Azure-régióból a másikba Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: ad73ef03aa9623fb724f1397697fac18f659a90c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934999"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Azure hálózati biztonsági csoport (NSG) áthelyezése egy másik régióba Azure PowerShell használatával

Különböző helyzetekben érdemes áthelyezni a meglévő NSG egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy NSG ugyanazzal a konfigurációs és biztonsági szabályokkal a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként egy NSG is át szeretne helyezni egy másik régióba.

Az Azure biztonsági csoportok nem helyezhetők át az egyik régióból egy másikba. A NSG meglévő konfigurációs és biztonsági szabályainak exportálásához azonban Azure Resource Manager sablont is használhat.  Ezután egy másik régióban is elvégezheti az erőforrást, ha a NSG sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célhelynek, majd üzembe helyezi a sablont az új régióban.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure-beli hálózati biztonsági csoport abban az Azure-régióban található, amelyről át kívánja helyezni.

- Az Azure-beli hálózati biztonsági csoportok nem helyezhetők át régiók között.  Az új NSG hozzá kell rendelnie a cél régió erőforrásaihoz.

- NSG-konfiguráció exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő NSG létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.
   
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a nyilvános IP-címek és a virtuális hálózatok számára.

- Győződjön meg arról, hogy az Azure-előfizetése lehetővé teszi, hogy NSG hozzon létre a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a folyamat NSG hozzáadásának támogatásához.  Tekintse meg a következőt: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a hálózati biztonsági csoportot a konfigurációs és biztonsági szabály számára egy Resource Manager-sablon használatával, és helyezze át a NSG konfigurációs és biztonsági szabályait a célként megadott régióra Azure PowerShell használatával.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Sablon exportálása és üzembe helyezése parancsfájlból

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be annak a NSG az erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)használatával:

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrás NSG egy. JSON-fájlba abba a könyvtárba, amelybe az [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg a **\<resource-group-name> . JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. A NSG nevének a paraméterének szerkesztéséhez módosítsa a forrás NSG **neve tulajdonságát** a cél NSG nevére, győződjön meg arról, hogy a név idézőjelek közé esik:
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. A NSG-konfigurációt és a biztonsági szabályokat áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot az **erőforrások** területen.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. A **\<resource-group-name> . JSON** fájl egyéb paramétereit is módosíthatja, ha az Ön által választott, és a követelményektől függően választható:

    * **Biztonsági szabályok** – a **\<resource-group-name> . JSON** fájl **securityRules** szakaszának szabályainak hozzáadásával vagy eltávolításával szerkesztheti, hogy mely szabályok legyenek telepítve a cél NSG:

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        A cél NSG lévő szabályok hozzáadásának vagy eltávolításának befejezéséhez a **\<resource-group-name> . JSON** fájl végén található egyéni szabályok típusait is szerkesztenie kell az alábbi példa formátumában:

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. Mentse a **\<resource-group-name> . JSON** fájlt.

10. Hozzon létre egy erőforráscsoportot a cél régióban a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával telepítendő NSG:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. Telepítse a szerkesztett **\<resource-group-name> . JSON** fájlt az előző lépésben létrehozott erőforráscsoporthoz a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. A cél régióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) és a [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>Elvetés 

Ha az üzembe helyezés után szeretné elindítani vagy elvetni a NSG a célhelyen, törölje a célhelyen létrehozott erőforráscsoportot, és az áthelyezett NSG is törlődni fog.  Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások végrehajtásához és a NSG áthelyezésének befejezéséhez törölje a forrás NSG vagy az erőforráscsoportot, majd használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) vagy a [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure-beli hálózati biztonsági csoportot helyezett át az egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)
