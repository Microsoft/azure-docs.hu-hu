---
title: Key Vault használata felügyelt alkalmazás telepítésekor
description: Bemutatja, hogyan használhatók a hozzáférési Titkok a Azure Key Vault a felügyelt alkalmazások telepítésekor
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "81458283"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Key Vault titok elérése Azure Managed Applications telepítésekor

Ha biztonságos értéket (például jelszót) kell átadnia paraméterként az üzembe helyezés során, lekérheti az értéket egy [Azure Key Vault](../../key-vault/general/overview.md). Ha a felügyelt alkalmazások telepítésekor szeretné elérni a Key Vault, hozzáférést kell biztosítania a **készülék erőforrás-szolgáltatói** egyszerű szolgáltatásához. A felügyelt alkalmazások szolgáltatás ezt az identitást használja a műveletek futtatásához. Ha az üzembe helyezés során sikeresen beolvas egy Key Vault értéket, az egyszerű szolgáltatásnak el kell tudnia érni a Key Vault.

Ez a cikk azt ismerteti, hogyan konfigurálható a Key Vault a felügyelt alkalmazásokkal való együttműködéshez.

## <a name="enable-template-deployment"></a>Sablon központi telepítésének engedélyezése

1. A portálon válassza ki a Key Vault.

1. Válassza a **Hozzáférési szabályzatok** lehetőséget.   

   ![Hozzáférési szabályzatok kiválasztása](./media/key-vault-access/select-access-policies.png)

1. Válassza a **Kattintson a speciális hozzáférési szabályzatok megtekintéséhez** lehetőséget.

   ![Speciális hozzáférési szabályzatok megjelenítése](./media/key-vault-access/advanced.png)

1. Jelölje be **a Azure Resource Manager hozzáférésének engedélyezése a sablonok üzembe helyezéséhez** lehetőséget. Ezután válassza a **Mentés** lehetőséget.

   ![Sablon központi telepítésének engedélyezése](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Szolgáltatás hozzáadása közreműködőként

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.

   ![Hozzáférés-vezérlés kiválasztása](./media/key-vault-access/access-control.png)

1. Válassza a **Szerepkör-hozzárendelés hozzáadása** lehetőséget.

   ![Hozzáadás kiválasztása](./media/key-vault-access/add-access-control.png)

1. Válassza a szerepkör **közreműködői** lehetőséget. Keresse meg a **készülék erőforrás-szolgáltatóját** , és válassza ki az elérhető lehetőségek közül.

   ![Szolgáltató keresése](./media/key-vault-access/search-provider.png)

1. Kattintson a **Mentés** gombra.

## <a name="reference-key-vault-secret"></a>Key Vault titok hivatkozása

Ha át szeretne adni egy titkos kulcsot egy Key Vault a felügyelt alkalmazásban található sablonhoz, egy [csatolt vagy beágyazott sablont](../templates/linked-templates.md) kell használnia, és hivatkoznia kell a Key Vaultra a csatolt vagy beágyazott sablon paraméterei között. Adja meg a Key Vault erőforrás-AZONOSÍTÓját és a titok nevét.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Úgy konfigurálta a Key Vault, hogy elérhető legyen egy felügyelt alkalmazás telepítése során.

* A Key Vault sablon paraméterként való átadásával kapcsolatos információkért lásd: a [Azure Key Vault használata a biztonságos paraméterek értékének](../templates/key-vault-parameter.md)átadására a telepítés során.
* A felügyelt alkalmazások példáit lásd: [Sample projects for Azure Managed Applications](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.
