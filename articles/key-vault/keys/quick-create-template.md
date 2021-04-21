---
title: Azure rövid útmutató – Azure Key Vault és kulcs létrehozása Azure Resource Manager sablon | Microsoft Docs
description: Rövid útmutató, amely bemutatja, hogyan hozhat létre Azure-kulcstartókat, és hogyan adhat hozzá kulcsokat a Azure Resource Manager (ARM-sablon) használatával.
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 10/14/2020
ms.author: sebansal
ms.openlocfilehash: 048482c6b52d3fd9225224bd3b4ff3ee66bf24fd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815359"
---
# <a name="quickstart-create-an-azure-key-vault-and-a-key-by-using-arm-template"></a>Rövid útmutató: Azure Key Vault és kulcs létrehozása ARM-sablonnal 

[Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkos kulcsok, például kulcsok, jelszavak, tanúsítványok és egyéb titkos kulcsok számára. Ez a rövid útmutató a kulcstartó és kulcs Azure Resource Manager sablon (ARM-sablon) üzembe helyezésének folyamatát tartalmazza.

> [!NOTE]
> Ez a funkció nem érhető el a Azure Government.

## <a name="prerequisites"></a>Előfeltételek

A cikk befejezéséhez:

- Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- A felhasználónak egy beépített Azure-szerepkört kell hozzárendelni, például: Közreműködő. [További információ itt](../../role-based-access-control/role-assignments-portal.md)
- A sablonnak szüksége van az ÖN Azure AD-felhasználói objektumazonosítójára az engedélyek konfigurálásához. Az alábbi eljárás az objektumazonosítót (GUID) kapja meg.

    1. Futtassa a Azure PowerShell vagy Azure CLI-parancsot a Kipróbálom gombra **kattintva,** majd illessze be a szkriptet a felület paneljére. A szkript beillesztéshez kattintson a jobb gombbal a rendszerhéjra, majd válassza a Beillesztés **lehetőséget.**

        # <a name="cli"></a>[Parancssori felület](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    1. Írja le az objektumazonosítót. Erre a rövid útmutató következő szakaszában lesz szüksége.

## <a name="review-the-template"></a>A sablon áttekintése

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key vault to be created."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "The SKU of the vault to be created."
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "The name of the key to be created."
      }
    },
    "keyType": {
      "type": "string",
      "metadata": {
        "description": "The JsonWebKeyType of the key to be created."
      }
    },
    "keyOps": {
      "type": "array",
      "defaultValue": [],
      "metadata": {
        "description": "The permitted JSON web key operations of the key to be created."
      }
    },
    "keySize": {
      "type": "int",
      "defaultValue": 2048,
      "metadata": {
        "description": "The size in bits of the key to be created."
      }
    },
    "curveName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
        "description": "The JsonWebKeyCurveName of the key to be created."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enableRbacAuthorization": false,
        "enableSoftDelete": false,
        "enabledForDeployment": false,
        "enabledForDiskEncryption": false,
        "enabledForTemplateDeployment": false,
        "tenantId": "[subscription().tenantId]",
        "accessPolicies": [],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        },
        "networkAcls": {
          "defaultAction": "Allow",
          "bypass": "AzureServices"
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/keys",
      "apiVersion": "2019-09-01",
      "name": "[concat(parameters('vaultName'), '/', parameters('keyName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('vaultName'))]"
      ],
      "properties": {
        "kty": "[parameters('keyType')]",
        "keyOps": "[parameters('keyOps')]",
        "keySize": "[parameters('keySize')]",
        "curveName": "[parameters('curveName')]"
      }
    }
  ],
  "outputs": {
    "proxyKey": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.KeyVault/vaults/keys', parameters('vaultName'), parameters('keyName')))]"
    }
  }
}
```

A sablonban két erőforrás van meghatározva:

- [Microsoft.KeyVault/vaults](/azure/templates/microsoft.keyvault/vaults)
- Microsoft.KeyVault/vaults/keys

További Azure Key Vault mintákat az Azure gyorsindítási [sablonok között talál.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése
Használhatja a [Azure Portal,](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-portal)Azure PowerShell, az Azure CLI-t vagy a REST API. Az üzembe helyezési módszerekkel kapcsolatos további információkért lásd: [Sablonok üzembe helyezése.](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell)

## <a name="review-deployed-resources"></a>Az üzembe helyezett erőforrások áttekintése

A kulcstartót Azure Portal kulcs ellenőrzéshez használhatja, vagy használhatja a következő Azure CLI-t vagy Azure PowerShell szkriptet a létrehozott kulcs listához.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault key list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultKey -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Erre a rövid útmutatóra egyéb Key Vault-útmutatók és oktatóanyagok is épülnek. Ha azt tervezi, hogy az ezt követő rövid útmutatókkal és oktatóanyagokkal dolgozik tovább, ne törölje ezeket az erőforrásokat.
Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat is. Az erőforráscsoport törlése az Azure CLI vagy a Azure PowerShell:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót és egy kulcsot egy ARM-sablonnal, és érvényesítette az üzembe helyezést. Ha többet szeretne megtudni a Key Vault és Azure Resource Manager, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](../general/overview.md)
- További információ az [Azure Resource Managerről](../../azure-resource-manager/management/overview.md)
- Tekintse át [a Key Vault biztonsági áttekintését](../general/security-features.md)
