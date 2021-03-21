---
title: Lemez titkosításának engedélyezése Service Fabric felügyelt fürt (előzetes verzió) csomópontjaihoz
description: Megtudhatja, hogyan engedélyezheti a lemezek titkosítását az Azure Service Fabric felügyelt fürtcsomópontok esetében a Windowsban ARM-sablon használatával.
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100642509"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Lemez titkosításának engedélyezése Service Fabric felügyelt fürt (előzetes verzió) csomópontjaihoz

Ebből az útmutatóból megtudhatja, hogyan engedélyezheti a lemezek titkosítását a Windows Service Fabric felügyelt csomópontjain a [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) képességgel a [virtuálisgép-méretezési csoportokhoz](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md) Azure Resource Manager (ARM) sablonok használatával.

> [!IMPORTANT]
> A virtuálisgép-méretezési csoport lemezének titkosítási előnézete még nem támogatja a lemezképek frissítését vagy a lemezkép-átállítást. Ne használja, ha frissítenie kell az operációs rendszer rendszerképét.

## <a name="register-for-azure-disk-encryption"></a>Azure Disk Encryption regisztrálása

A virtuálisgép-méretezési csoport lemez-titkosítási előnézete önregisztrációt igényel. Futtassa az alábbi parancsot:

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

A regisztráció állapotának ellenőrzéséhez futtassa a következőket:

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

Ha az állapot *regisztráltra* vált, készen áll a folytatásra.

## <a name="provision-a-key-vault-for-disk-encryption"></a>Key Vault kiépítése a lemezes titkosításhoz

Azure Disk Encryption a lemez titkosítási kulcsainak és titkainak szabályozásához és kezeléséhez Azure Key Vault szükséges. A Key Vault és Service Fabric felügyelt fürtnek ugyanabban az Azure-régióban és-előfizetésben kell lennie. Ha ezek a követelmények teljesülnek, a lemez titkosításának engedélyezésével új vagy meglévő Key Vault is használhat.

### <a name="create-key-vault-with-disk-encryption-enabled"></a>Key Vault létrehozása, ha engedélyezve van a lemez titkosítása

Futtassa a következő parancsokat egy új Key Vault lemezes titkosításhoz való létrehozásához. Győződjön meg arról, hogy a Key Vault régiója [Service Fabric felügyelt fürtök esetében támogatott](faq-managed-cluster.md#what-regions-are-supported-in-the-preview) , és a fürt azonos régiójában található.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>Meglévő Key Vault frissítése a lemez titkosításának engedélyezéséhez

Futtassa a következő parancsokat a lemez titkosításának engedélyezéséhez egy meglévő Key Vault.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>A sablon és a paraméterek fájljainak frissítése a lemezes titkosításhoz

A következő lépés végigvezeti a szükséges sablon változásain a lemez titkosításának engedélyezéséhez egy [meglévő felügyelt fürtön](tutorial-managed-cluster-deploy.md). Másik lehetőségként üzembe helyezhet egy új Service Fabric felügyelt fürtöt, amelyen engedélyezve van a lemez titkosítása a következő sablonnal: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption

1. Adja hozzá a következő paramétereket a sablonhoz, a saját előfizetését, az erőforráscsoport nevét és a tár nevét `keyVaultResourceId` :

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. Ezután adja hozzá a virtuálisgép `AzureDiskEncryption` -bővítményt a felügyelt fürt csomópont-típusaihoz a sablonban:

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. Végül frissítse a paramétereket tartalmazó fájlt, amely a saját előfizetését, az erőforráscsoportot és a Key Vault nevét helyettesíti a *keyVaultResourceId*-ben:

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>A módosítások központi telepítése és ellenőrzése

Ha elkészült, telepítse a módosításokat, hogy engedélyezze a lemez titkosítását a felügyelt fürtön.

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

A parancs használatával megtekintheti a lemez titkosítási állapotát a csomópont típusa mögöttes méretezési csoporton `Get-AzVmssDiskEncryption` . Először meg kell keresnie a felügyelt fürt támogató erőforráscsoport nevét (amely tartalmazza a mögöttes virtuális hálózatot, a terheléselosztó-t, a nyilvános IP-címet, a NSG, a méretezési csoportot és a Storage-fiókokat). Győződjön meg arról, hogy a fürt minden olyan típusát módosítani kívánja, `VmssName` amelyet ellenőrizni kíván (a telepítési sablonban megadott módon).

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

A kimenetnek a következőhöz hasonlóan kell megjelennie:

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>Következő lépések

[Minta: standard SKU Service Fabric felügyelt fürt, 1 csomópontos típus, amelyen engedélyezve van a lemezes titkosítás](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Azure Disk Encryption Windows rendszerű virtuális gépekhez](../virtual-machines/windows/disk-encryption-overview.md)

[Virtuálisgép-méretezési csoportok titkosítása Azure Resource Manager](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
