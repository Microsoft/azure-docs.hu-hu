---
title: Azure-kulcstartó és tároló-hozzáférési szabályzat létrehozása ARM-sablon használatával
description: Ez a cikk bemutatja, hogyan hozhat létre Azure-kulcstartókat és tároló-hozzáférési szabályzatokat egy Azure Resource Manager sablonnal.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 3/14/2021
ms.author: mbaldwin
ms.openlocfilehash: e70906cbf26c899744bfbe137da4ce9cfa651b20
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753145"
---
# <a name="how-to-create-an-azure-key-vault-and-vault-access-policy-by-using-a-resource-manager-template"></a>Azure Key Vault- és tároló-hozzáférési szabályzat létrehozása Resource Manager sablonnal

[Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkos kulcsok, például kulcsok, jelszavak és tanúsítványok számára. Ez a cikk azt ismerteti, hogyan lehet üzembe helyezni egy Azure Resource Manager-sablont (ARM-sablont) egy kulcstartó létrehozásához.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Előfeltételek

A cikkben található lépések befejezéséhez:

* Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes fiókot.


## <a name="create-a-key-vault-resource-manager-template"></a>Új Key Vault Resource Manager létrehozása

Az alábbi sablon egy alapvető kulcstartó-létrehozási módját mutatja be. Egyes értékek meg vannak adva a sablonban.

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

További információ a Key Vault beállításairól: [arm Key Vault sablon-referencia.](/azure/templates/microsoft.keyvault/vaults)

> [!IMPORTANT]
> Ha a sablont újra üzembe emik, a rendszer felülírja a kulcstartóban meglévő hozzáférési szabályzatokat. Javasoljuk, hogy a kulcstartóhoz való hozzáférés elvesztése érdekében töltse fel a tulajdonságot a meglévő `accessPolicies` hozzáférési szabályzatokkal. 

## <a name="add-an-access-policy-to-a-key-vault-resource-manager-template"></a>Hozzáférési szabályzat hozzáadása Key Vault Resource Manager sablonhoz

Hozzáférési szabályzatokat a teljes Key Vault-sablon ismételt üzembe helyezése nélkül helyezhet üzembe egy meglévő kulcstartóban. Az alábbi sablon a hozzáférési szabályzatok létrehozásának alapvető módját mutatja be:

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

A sablonbeállításokról az ARM Key Vault referenciában [Key Vault további információt.](/azure/templates/microsoft.keyvault/vaults/accesspolicies)

## <a name="more-key-vault-resource-manager-templates"></a>További Key Vault Resource Manager sablonok

Az objektumokhoz Resource Manager más sablon Key Vault is rendelkezésre áll:

| Titkos kulcsok | Kulcsok | Tanúsítványok |
|--|--|--|
|<ul><li>[Gyors útmutató](../secrets/quick-create-template.md)<li>[Referencia](/azure/templates/microsoft.keyvault/vaults/secrets)|N.A.|N.A.|

További sablonokat itt Key Vault talál: [Key Vault Resource Manager hivatkozás.](/azure/templates/microsoft.keyvault/allversions)

## <a name="deploy-the-templates"></a>A sablonok üzembe helyezése

Az alábbi Azure Portal a fenti sablonokat a **Build your own template in editor** (Saját sablon létrehozása szerkesztőben) lehetőséggel helyezheti üzembe a következő módon: Erőforrások üzembe helyezése egyéni [sablonból.](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)

A fenti sablonokat fájlokba is mentheti, és a következő parancsokat használhatja: [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) és [az az deployment group create:](/cli/azure/deployment/group#az_deployment_group_create)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile key-vault-template.json
```

```azurecli
az deployment group create --resource-group ExampleGroup --template-file key-vault-template.json
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az ezt követő rövid útmutatókat és oktatóanyagokat folytatja, ezeket az erőforrásokat a helyén hagyhatja. Ha már nincs szüksége az erőforrásokra, törölje az erőforráscsoportot. Ha törli a csoportot, a kulcstartó és a kapcsolódó erőforrások is törlődnek. Ha törölni szeretné az erőforráscsoportot az Azure CLI vagy a Azure PowerShell használatával, kövesse az alábbi lépéseket:

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

- Olvassa el az [áttekintést a Azure Key Vault.](../general/overview.md)
- További információ a [Azure Resource Manager.](../../azure-resource-manager/management/overview.md)
- A biztonsági [Azure Key Vault áttekintése](security-overview.md)

## <a name="next-steps"></a>Következő lépések

- [Biztonságos hozzáférés egy kulcstartóhoz](security-overview.md)
- [Hitelesítés kulcstartóban](authentication.md)
- [Az Azure Key Vault fejlesztői útmutatója](developers-guide.md)
