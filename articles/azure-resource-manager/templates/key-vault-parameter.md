---
title: Titok Key Vault sablonnal
description: Bemutatja, hogyan lehet átadni egy titkos kulcsot a Key vaultból paraméterként az üzembe helyezés során.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 8983d51d90e07e21ac4c9489c7ed553530278d3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97672705"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Az Azure Key Vault használata biztonságos paraméterértékek üzembe helyezéskori átadására

Ahelyett, hogy egy biztonságos értéket (például a jelszót) közvetlenül a sablonban vagy a paraméterben található fájlba helyezze, lekérheti az értéket egy [Azure Key Vault](../../key-vault/general/overview.md) egy központi telepítés során. Az értéket úgy kell lekérnie, hogy a kulcstárolóra hivatkozik, és a titkos kulcsot a paraméter fájljában. Az érték soha nem lesz kitéve, mert csak a Key Vault-AZONOSÍTÓra hivatkozik. A Key Vault egy másik előfizetésben is létezhet, mint az üzembe helyezett erőforráscsoport.

Ez a cikk a bizalmas érték sablon-paraméterként való átadásának forgatókönyvét tárgyalja. Nem vonatkozik arra a forgatókönyvre, amely a virtuális gép tulajdonságát egy Key Vault lévő tanúsítvány URL-címére állítja be. Az adott forgatókönyvhöz tartozó rövid útmutató sablonját a [tanúsítvány telepítése Azure Key Vault virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)című témakörben tekintheti meg.

## <a name="deploy-key-vaults-and-secrets"></a>Kulcstartók és titkos kulcsok üzembe helyezése

Egy kulcstartó eléréséhez a sablon üzembe helyezése során állítsa be a Key vaultot a következőre: `enabledForTemplateDeployment` `true` .

Ha már rendelkezik Key Vault, győződjön meg róla, hogy lehetővé teszi a sablonok központi telepítését.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Új Key Vault létrehozásához és a titkos kód hozzáadásához használja a következőt:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

A Key Vault tulajdonosaként automatikusan hozzáférhet a titkok létrehozásához. Ha a titkos kulcsokkal dolgozó felhasználó nem tulajdonosa a kulcstartónak, a következőkhöz biztosít hozzáférést:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

A kulcstartók létrehozásával és a titkok hozzáadásával kapcsolatos további információkért lásd:

- [Titkos kód beállítása és lekérése a parancssori felület használatával](../../key-vault/secrets/quick-create-cli.md)
- [Titkos kód beállítása és beolvasása a PowerShell használatával](../../key-vault/secrets/quick-create-powershell.md)
- [Titkos kód beállítása és lekérése a portál használatával](../../key-vault/secrets/quick-create-portal.md)
- [Titkos kód beállítása és lekérése a .NET használatával](../../key-vault/secrets/quick-create-net.md)
- [Titkos kód beállítása és lekérése Node.jshasználatával ](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Hozzáférés biztosítása a titkokhoz

A sablont telepítő felhasználónak engedéllyel kell rendelkeznie az `Microsoft.KeyVault/vaults/deploy/action` erőforráscsoport és a kulcstartó hatóköréhez. A [tulajdonos](../../role-based-access-control/built-in-roles.md#owner) és a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkör egyaránt megadja ezt a hozzáférést. Ha létrehozta a kulcstartót, akkor Ön a tulajdonosa, hogy Ön rendelkezik az engedélyekkel.

A következő eljárás azt mutatja be, hogyan hozható létre egy szerepkör a minimális engedélyekkel, és hogyan rendelhető hozzá a felhasználó

1. Hozzon létre egy egyéni szerepkör-definíciós JSON-fájlt:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Cserélje le az "00000000-0000-0000-0000-000000000000" kifejezést az előfizetés-AZONOSÍTÓra.

2. Hozza létre az új szerepkört a JSON-fájl használatával:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    A minták hozzárendelik az egyéni szerepkört a felhasználóhoz az erőforráscsoport szintjén.

Ha egy [felügyelt alkalmazáshoz](../managed-applications/overview.md)tartozó sablonnal Key Vault használ, hozzáférést kell biztosítania a **készülék erőforrás-szolgáltatói** egyszerű szolgáltatásához. További információ: [hozzáférés Key Vault secret Azure Managed Applications telepítésekor](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Hivatkozási titkok statikus AZONOSÍTÓval

Ezzel a módszerrel a Key vaultra hivatkozik a paraméter fájljában, nem pedig a sablonban. Az alábbi képen látható, hogy a paraméter fájlja hogyan hivatkozik a titkos kulcsra, és átadja ezt az értéket a sablonnak.

![Resource Manager Key Vault-integráció – statikus azonosító diagramja](./media/key-vault-parameter/statickeyvault.png)

[Oktatóanyag: a Azure Key Vault integrálása a Resource managerben template Deployment](./template-tutorial-use-key-vault.md) ezt a módszert használja.

A következő sablon olyan SQL Servert telepít, amely rendszergazdai jelszót tartalmaz. A Password paraméter egy biztonságos karakterláncra van beállítva. A sablon azonban nem határozza meg, hogy az adott érték honnan származik.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Most hozzon létre egy paramétert az előző sablonhoz. A paraméter fájljában adjon meg egy paramétert, amely megegyezik a sablonban található paraméter nevével. A paraméter értékeként hivatkozzon a Key Vault titkos kódjára. A titkos kulcsot a Key Vault erőforrás-azonosítójának és a titkos kulcs nevének a megadásával hivatkozhat:

A következő paraméter fájljában a Key Vault-titoknak már léteznie kell, és statikus értéket kell megadnia az erőforrás-AZONOSÍTÓhoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

Ha az aktuális verziótól eltérő titkos verziót kell használnia, használja a `secretVersion` tulajdonságot.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Telepítse a sablont, és adja meg a paramétert a fájlban:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Hivatkozási titkok dinamikus AZONOSÍTÓval

Az előző szakasz azt mutatta be, hogyan lehet átadni a Key Vault titkos kulcsának statikus erőforrás-AZONOSÍTÓját a paraméterből. Bizonyos helyzetekben azonban egy Key Vault-titokra kell hivatkoznia, amely az aktuális telepítéstől függően változik. Vagy előfordulhat, hogy paramétereket kell átadnia a sablonnak, és nem kell hivatkozási paramétert létrehoznia a paraméter fájljában. Mindkét esetben a Key Vault-titok erőforrás-AZONOSÍTÓját egy csatolt sablon használatával lehet dinamikusan előállítani.

A Parameters fájlban nem lehet dinamikusan előállítani az erőforrás-azonosítót, mert a Parameters fájlban nem engedélyezettek a sablon kifejezései.

A fölérendelt sablonban adja hozzá a beágyazott sablont, és adjon meg egy olyan paramétert, amely tartalmazza a dinamikusan generált erőforrás-azonosítót. Az alábbi képen látható, hogy a csatolt sablon egyik paramétere hogyan hivatkozik a titkos kulcsra.

![Dinamikus azonosító](./media/key-vault-parameter/dynamickeyvault.png)

A következő sablon dinamikusan létrehozza a Key Vault-azonosítót, és paraméterként továbbítja azt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Következő lépések

- A Key vaultokkal kapcsolatos általános információkért lásd: [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
- A legfontosabb titkokra hivatkozó példákat itt talál: [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- Egy olyan Microsoft Learn modulhoz, amely a kulcstartó biztonságos értékét fedi le, lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablonok használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).
