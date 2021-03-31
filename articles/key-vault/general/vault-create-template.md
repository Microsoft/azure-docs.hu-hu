---
title: Azure Key Vault és egy tároló hozzáférési szabályzatának létrehozása ARM-sablonnal
description: Ez a cikk bemutatja, hogyan hozhat létre Azure Key Vault-és tároló-hozzáférési házirendeket egy Azure Resource Manager sablon használatával.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e489efd335d2a46a0e55d4ceb9bdee809ae6c39d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595243"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Azure Key Vault és a tároló hozzáférési házirendjének létrehozása Resource Manager-sablonnal

A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkokhoz, például kulcsokhoz, jelszavakhoz és tanúsítványokhoz. Ez a cikk egy Azure Resource Manager-sablon (ARM-sablon) üzembe helyezésének folyamatát ismerteti a kulcstartó létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához:

* Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .


## <a name="create-a-key-vault-resource-manager-template"></a>Key Vault Resource Manager-sablon létrehozása

A következő sablon egy alapszintű módszert mutat be kulcstartó létrehozásához. Néhány érték van megadva a sablonban.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
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
        "description": "Specifies whether the key vault is a standard vault or a premium vault."
      }
    }
   },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('keyVaultName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "false",
        "enabledForDiskEncryption": "false",
        "enabledForTemplateDeployment": "false",
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
    }
  ]
}

```

További információ az Key Vault sablon beállításairól: [Key Vault ARM-sablon referenciája](/azure/templates/microsoft.keyvault/vaults).

> [!IMPORTANT]
> A sablon újbóli üzembe helyezése esetén a Key vaultban lévő összes meglévő hozzáférési szabályzat felülbírálva lesz. Azt javasoljuk, hogy a `accessPolicies` tulajdonságot meglévő hozzáférési házirendekkel töltse fel, hogy elkerülje a kulcstartóhoz való hozzáférés elvesztését. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Hozzáférési szabályzat hozzáadása Key Vault Resource Manager-sablonhoz

A teljes Key Vault-sablon újbóli üzembe helyezése nélkül is üzembe helyezhet hozzáférési házirendeket egy meglévő kulcstartón. A következő sablon a hozzáférési szabályzatok létrehozásának alapvető módját mutatja be:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the key vault."
      }
    },
    "objectId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the object ID of a user, service principal or security group in the Azure Active Directory tenant for the vault. The object ID must be unique for the list of access policies. Get it by using Get-AzADUser or Get-AzADServicePrincipal cmdlets."
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to keys in the vault. Valid values are: all, encrypt, decrypt, wrapKey, unwrapKey, sign, verify, get, list, create, update, import, delete, backup, restore, recover, and purge."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to secrets in the vault. Valid values are: all, get, list, set, delete, backup, restore, recover, and purge."
      }
    },
    "certificatePermissions": {
      "type": "array",
      "defaultValue": [
        "list"
      ],
      "metadata": {
        "description": "Specifies the permissions to certificates in the vault. Valid values are: all,  create, delete, update, deleteissuers, get, getissuers, import, list, listissuers, managecontacts, manageissuers,  recover, backup, restore, setissuers, and purge."
      }
    },
  "resources": [
     {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "2019-09-01",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]",
              "certificates": "[parameters('certificatePermissions')]"
            }
          }
        ]
      }
    }
  ]
}

```

További információ az Key Vault sablon beállításairól: [Key Vault ARM-sablon referenciája](/azure/templates/microsoft.keyvault/vaults/accesspolicies).

## <a name="more-key-vault-resource-manager-templates"></a>További Key Vault Resource Manager-sablonok

Más Resource Manager-sablonok is elérhetők Key Vault objektumokhoz:

| Titkos kulcsok | Kulcsok | Tanúsítványok |
|--|--|--|
|<ul><li>[Gyors útmutató](../secrets/quick-create-template.md)<li>[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)|N.A.|N.A.|

További Key Vault sablonokat itt talál: [Key Vault Resource Manager-referenciát](/azure/templates/microsoft.keyvault/allversions).

## <a name="deploy-the-templates"></a>A sablonok üzembe helyezése

A Azure Portal az előző sablonok üzembe helyezéséhez használja a **saját sablon létrehozása a szerkesztőben** című témakört az itt leírtak szerint: [erőforrások üzembe helyezése egyéni sablonból](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

Az előző sablonokat fájlokat is mentheti, és a következő parancsokat használhatja:  [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja az ezt követő rövid útmutatók és oktatóanyagok meglétét, ezeket az erőforrásokat helyben hagyhatja. Ha már nincs szüksége az erőforrásokra, törölje az erőforráscsoportot. Ha törli a csoportot, a Key Vault és a kapcsolódó erőforrások is törlődnek. Az erőforráscsoport Azure CLI vagy Azure PowerShell használatával történő törléséhez hajtsa végre a következő lépéseket:

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

## <a name="resources"></a>Források

- Olvassa el [a Azure Key Vault áttekintése című témakört](../general/overview.md).
- További információ a [Azure Resource Managerról](../../azure-resource-manager/management/overview.md).
- Tekintse át a [Azure Key Vault biztonsági áttekintést](security-overview.md)

## <a name="next-steps"></a>Következő lépések

- [Biztonságos hozzáférés egy kulcstartóhoz](secure-your-key-vault.md)
- [Hitelesítés a Key vaultban](authentication.md)
- [Az Azure Key Vault fejlesztői útmutatója](developers-guide.md)
