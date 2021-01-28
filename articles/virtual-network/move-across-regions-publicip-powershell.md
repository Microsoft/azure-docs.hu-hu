---
title: Azure nyilvános IP-konfiguráció áthelyezése másik Azure-régióba Azure PowerShell használatával
description: Azure Resource Manager sablon használatával áthelyezheti az Azure nyilvános IP-konfigurációját egyik Azure-régióból a másikba Azure PowerShell használatával.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: a21d088680855b74e7259028ed7ef55165707c56
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938685"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-azure-powershell"></a>Azure nyilvános IP-konfiguráció áthelyezése másik régióba Azure PowerShell használatával

Különböző helyzetekben érdemes áthelyezni a meglévő Azure nyilvános IP-konfigurációkat az egyik régióból a másikba. Előfordulhat például, hogy létre szeretne hozni egy nyilvános IP-címet ugyanazzal a konfigurációval és SKU-val a teszteléshez. Előfordulhat, hogy a vész-helyreállítási tervezés részeként egy nyilvános IP-konfigurációt is át szeretne helyezni egy másik régióba.

**Az Azure nyilvános IP-címei régió-specifikusak, és nem helyezhetők át egyik régióból a másikba.** A nyilvános IP-címek meglévő konfigurációjának exportálásához azonban használhat egy Azure Resource Manager sablont is.  Az erőforrást egy másik régióban is elvégezheti, ha a nyilvános IP-címet egy sablonba exportálja, módosítja a paramétereket, hogy azok megfeleljenek a célként megadott régiónak, majd üzembe helyezi a sablont az új régióban.  A Resource Managerrel és a sablonokkal kapcsolatos további információkért lásd: [erőforráscsoportok exportálása sablonokba](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy az Azure-beli nyilvános IP-cím az áthelyezni kívánt Azure-régióban található.

- Az Azure nyilvános IP-címei nem helyezhetők át a régiók között.  Az új nyilvános IP-címet hozzá kell rendelnie a célként megadott régióban található erőforrásokhoz.

- Nyilvános IP-konfiguráció exportálásához és sablon üzembe helyezéséhez egy másik régióban lévő nyilvános IP-cím létrehozásához szüksége lesz a hálózati közreműködő szerepkörre vagy magasabbra.
   
- Azonosítsa a forrás hálózatkezelési elrendezést és az összes éppen használt erőforrást. Ez az elrendezés tartalmaz, de nem korlátozódik a terheléselosztó, a hálózati biztonsági csoportok (NSG) és a virtuális hálózatok számára.

- Győződjön meg arról, hogy az Azure-előfizetés lehetővé teszi, hogy nyilvános IP-címeket hozzon létre a használt célcsoportban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

- Győződjön meg arról, hogy az előfizetése elegendő erőforrással rendelkezik a nyilvános IP-címek ezen folyamathoz való hozzáadásának támogatásához.  Tekintse meg a következőt: [Az Azure-előfizetések és -szolgáltatások korlátozásai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Előkészítés és áthelyezés
A következő lépések bemutatják, hogyan készítse elő a nyilvános IP-címet a konfiguráció áthelyezéséhez egy Resource Manager-sablon használatával, és a nyilvános IP-konfigurációt a célként megadott régióba helyezheti Azure PowerShell használatával.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>Sablon exportálása és üzembe helyezése parancsfájlból

1. Jelentkezzen be az Azure-előfizetésbe a [AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal, és kövesse a képernyőn megjelenő utasításokat:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Szerezze be annak a nyilvános IP-nek az erőforrás-AZONOSÍTÓját, amelyet át szeretne helyezni a célként megadott régióba, és helyezze egy változóba a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress)használatával:

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. Exportálja a forrásként szolgáló virtuális hálózatot egy. JSON-fájlba abba a könyvtárba, amelyben az [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup)parancsot futtatja:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. A letöltött fájl neve annak az erőforrás-csoportnak az alapján lesz elnevezve, amelyből az erőforrást exportálták.  Keresse meg a **\<resource-group-name> . JSON** nevű parancsból exportált fájlt, és nyissa meg egy tetszőleges szerkesztőben:
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. A nyilvános IP-cím paraméterének szerkesztéséhez módosítsa a forrás nyilvános IP-cím tulajdonságának **alapértelmezett** értékét a célként megadott nyilvános IP-cím nevére, győződjön meg arról, hogy a név idézőjelek közé esik:
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. A nyilvános IP-címet áthelyező cél régió szerkesztéséhez módosítsa a **Location (hely** ) tulajdonságot az erőforrások területen.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. A régióbeli hely kódjának beszerzéséhez használja a [Get-AzLocation](/powershell/module/az.resources/get-azlocation) Azure PowerShell parancsmagot a következő parancs futtatásával:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. A sablon egyéb paramétereit is módosíthatja, és a követelményektől függően választható:

    * **SKU** – a nyilvános IP-cím SKU-jának a standard és az alap közötti értékről a standard típusra módosítható úgy, hogy a  >  **\<resource-group-name> . JSON** fájlban módosítja az SKU **Name** tulajdonságot:

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         Az alapszintű és a standard SKU nyilvános IP-címei közötti különbségekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](./virtual-network-public-ip-address.md).

    * **Nyilvános IP-kiosztási módszer** és **Üresjárati időkorlát** – a sablon mindkét beállítását módosíthatja úgy, hogy a **PublicIPAllocationMethod** tulajdonságot **dinamikusról** **statikusra** vagy statikusra változtatja **a** **dinamikus** értékre. Az Üresjárat időkorlátja módosítható úgy, hogy módosítja a **idleTimeoutInMinutes** tulajdonságot a kívánt értékre.  Az alapértelmezett érték **4**:

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        A kiosztási módszerekkel és az üresjárati időtúllépési értékekkel kapcsolatos további információkért lásd: [nyilvános IP-cím létrehozása, módosítása vagy törlése](./virtual-network-public-ip-address.md).


9. Mentse a **\<resource-group-name> . JSON** fájlt.

10. Hozzon létre egy erőforráscsoportot a céltartományban a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)használatával telepítendő cél nyilvános IP-címhez.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. Telepítse a szerkesztett **\<resource-group-name> . JSON** fájlt az előző lépésben létrehozott erőforráscsoporthoz a [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)használatával:

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. A cél régióban létrehozott erőforrások ellenőrzéséhez használja a [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) és a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress):
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```
## <a name="discard"></a>Elvetés 

Ha a központi telepítés után szeretné elindítani vagy elvetni a nyilvános IP-címet a célhelyen, törölje a célhelyen létrehozott erőforráscsoportot, és az áthelyezett nyilvános IP-címet törli a rendszer.  Az erőforráscsoport eltávolításához használja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A módosítások elvégzéséhez és a virtuális hálózat áthelyezésének befejezéséhez törölje a forrás virtuális hálózatot vagy az erőforráscsoportot a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) vagy a [Remove-AzPublicIPAddress](/powershell/module/az.network/remove-azpublicipaddress):

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy Azure nyilvános IP-címet helyezett át egyik régióból a másikba, és megtisztította a forrás erőforrásait.  Ha többet szeretne megtudni a régiók és a vész-helyreállítás között az Azure-ban, tekintse meg a következőt:


- [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure-beli virtuális gépek áthelyezése egy másik régióba](../site-recovery/azure-to-azure-tutorial-migrate.md)
