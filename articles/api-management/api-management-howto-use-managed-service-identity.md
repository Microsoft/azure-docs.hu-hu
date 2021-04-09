---
title: Felügyelt identitások használata az Azure API Managementban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre rendszerszintű és felhasználó által hozzárendelt identitásokat a API Managementban a Azure Portal, a PowerShell és egy Resource Manager-sablon használatával.
services: api-management
documentationcenter: ''
author: miaojiang
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 03/09/2021
ms.author: apimpm
ms.openlocfilehash: 98237efae89e7d88dd23cb7e8fc9f7e9f05bca70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521543"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Felügyelt identitások használata az Azure-ban API Management

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy Azure API Management-példányhoz, és hogyan férhet hozzá más erőforrásokhoz. Azure Active Directory (Azure AD) által generált felügyelt identitás lehetővé teszi, hogy a API Management-példány könnyen és biztonságosan hozzáférhessen más Azure AD-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Az Azure kezeli ezt az identitást, így nem kell semmilyen titkot kiépíteni vagy elforgatnia. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md).

API Management-példányhoz két típusú identitást adhat meg:

- A *rendszer által hozzárendelt identitás* a szolgáltatáshoz van kötve, és törlődik, ha a szolgáltatás törölve van. A szolgáltatásnak csak egy rendszerhez rendelt identitása lehet.
- A *felhasználó által hozzárendelt identitás* egy önálló Azure-erőforrás, amelyet hozzá lehet rendelni a szolgáltatáshoz. A szolgáltatás több felhasználó által hozzárendelt identitással rendelkezhet.

## <a name="create-a-system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás létrehozása

### <a name="azure-portal"></a>Azure Portal

A Azure Portal felügyelt identitásának beállításához először létre kell hoznia egy API Management példányt, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy API Management példányt a portálon. Tallózással keresse meg a portálon.
2. Válassza a **felügyelt identitások** lehetőséget.
3. A **rendszerhez rendelt** lapon váltson az **állapot** bekapcsolva **értékre**. Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="A rendszer által hozzárendelt felügyelt identitás engedélyezésének kiválasztása" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigvezetik egy API Management példány létrehozásán és az identitás Azure PowerShell használatával történő hozzárendelésének lépésein. 

1. Ha szükséges, telepítse Azure PowerShell a [Azure PowerShell útmutató](/powershell/azure/install-az-ps)utasításait követve. Ezután futtassa a parancsot `Connect-AzAccount` Az Azure-beli kapcsolatok létrehozásához.

2. A példány létrehozásához használja a következő kódot. További példák a Azure PowerShell API Management-példánnyal való használatáról: [API Management PowerShell-minták](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Meglévő példány frissítése az identitás létrehozásához:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A következő tulajdonságot az erőforrás-definícióban is létrehozhatja az identitással rendelkező API Management-példányt:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez a tulajdonság azt jelzi, hogy az Azure létrehozza és kezeli az API Management példány identitását.

A teljes Azure Resource Manager sablon például a következőhöz hasonló lehet:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "systemAssigned"
        }
    }]
}
```

A példány létrehozásakor a következő tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A `tenantId` tulajdonság azonosítja, hogy az identitás melyik Azure ad-bérlőhöz tartozik. A `principalId` tulajdonság a példány új identitásának egyedi azonosítója. Az Azure AD-n belül az egyszerű szolgáltatásnév neve megegyezik a API Management példányával.

> [!NOTE]
> Egy API Management-példányhoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság a következő `type` lesz: `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>A rendszerhez rendelt identitást használó támogatott forgatókönyvek

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Egyéni TLS/SSL-tanúsítvány beszerzése a API Management-példányhoz Azure Key Vault
API Management példány rendszerhez rendelt identitásával lekérheti a Azure Key Vault tárolt egyéni TLS/SSL-tanúsítványokat. Ezeket a tanúsítványokat az API Management-példányban lévő egyéni tartományokhoz rendelheti hozzá. A következő szempontokat vegye figyelembe:

- A titkos kód tartalomtípusának az *Application/x-PKCS12/pfx-profil* kell lennie.
- Használja az Key Vault-tanúsítvány titkos végpontját, amely tartalmazza a titkos kulcsot.

> [!Important]
> Ha nem adja meg a tanúsítvány objektumának verzióját, akkor a API Management a tanúsítvány újabb verzióját a Key Vault frissítése után négy órán belül automatikusan beolvassa.

Az alábbi példa egy Azure Resource Manager sablont mutat be, amely a következő lépéseket tartalmazza:

1. Hozzon létre egy API Management példányt felügyelt identitással.
2. Frissítse egy Azure Key Vault példány hozzáférési szabályzatait, és engedélyezze, hogy a API Management-példány megszerezze a titkos kulcsokat.
3. Frissítse a API Management példányt úgy, hogy egyéni tartománynevet állít be a Key Vault-példány tanúsítványán keresztül.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publisherEmail": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "The email address of the owner of the service"
            }
        },
        "publisherName": {
            "type": "string",
            "defaultValue": "Contoso",
            "minLength": 1,
            "metadata": {
                "description": "The name of the owner of the service"
            }
        },
        "sku": {
            "type": "string",
            "allowedValues": ["Developer",
            "Standard",
            "Premium"],
            "defaultValue": "Developer",
            "metadata": {
                "description": "The pricing tier of this API Management instance"
            }
        },
        "skuCount": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The instance size of this API Management instance."
            }
        },
        "keyVaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the vault"
            }
        },
        "proxyCustomHostname1": {
            "type": "string",
            "metadata": {
                "description": "Proxy Custom hostname."
            }
        },
        "keyVaultIdToCertificate": {
            "type": "string",
            "metadata": {
                "description": "Reference to the Key Vault certificate. https://contoso.vault.azure.net/secrets/contosogatewaycertificate."
            }
        }
    },
    "variables": {
        "apiManagementServiceName": "[concat('apiservice', uniqueString(resourceGroup().id))]",
        "apimServiceIdentityResourceId": "[concat(resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName')),'/providers/Microsoft.ManagedIdentity/Identities/default')]"
    },
    "resources": [{
        "apiVersion": "2019-01-01",
        "name": "[variables('apiManagementServiceName')]",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {
        },
        "sku": {
            "name": "[parameters('sku')]",
            "capacity": "[parameters('skuCount')]"
        },
        "properties": {
            "publisherEmail": "[parameters('publisherEmail')]",
            "publisherName": "[parameters('publisherName')]"
        },
        "identity": {
            "type": "systemAssigned"
        }
    },
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2015-06-01",
        "dependsOn": [
            "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "accessPolicies": [{
                "tenantId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').tenantId]",
                "objectId": "[reference(variables('apimServiceIdentityResourceId'), '2015-08-31-PREVIEW').principalId]",
                "permissions": {
                    "secrets": ["get"]
                }
            }]
        }
    },
    {
        "apiVersion": "2017-05-10",
        "name": "apimWithKeyVault",
        "type": "Microsoft.Resources/deployments",
        "dependsOn": [
        "[resourceId('Microsoft.ApiManagement/service', variables('apiManagementServiceName'))]"
        ],
        "properties": {
            "mode": "incremental",
            "templateLink": {
                "uri": "https://raw.githubusercontent.com/solankisamir/arm-templates/master/basicapim.keyvault.json",
                "contentVersion": "1.0.0.0"
            },
            "parameters": {
                "publisherEmail": { "value": "[parameters('publisherEmail')]"},
                "publisherName": { "value": "[parameters('publisherName')]"},
                "sku": { "value": "[parameters('sku')]"},
                "skuCount": { "value": "[parameters('skuCount')]"},
                "proxyCustomHostname1": {"value" : "[parameters('proxyCustomHostname1')]"},
                "keyVaultIdToCertificate": {"value" : "[parameters('keyVaultIdToCertificate')]"}
            }
        }
    }]
}
```

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Hitelesítés a háttérben API Management identitás használatával

A rendszer által hozzárendelt identitás használatával a [hitelesítéssel felügyelt identitásra](api-management-authentication-policies.md#ManagedIdentity) vonatkozó házirend segítségével végezheti el a hitelesítést a háttérben.

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Kapcsolódás az IP-tűzfal mögötti Azure-erőforrásokhoz rendszerhez rendelt felügyelt identitás használatával


API Management megbízható Microsoft-szolgáltatás a következő erőforrásokhoz. Ez lehetővé teszi, hogy a szolgáltatás tűzfal mögött a következő erőforrásokhoz kapcsolódjon. Miután explicit módon hozzárendelte a megfelelő Azure-szerepkört az adott erőforrás [-példány rendszerhez rendelt felügyelt identitásához](../active-directory/managed-identities-azure-resources/overview.md) , a példányhoz való hozzáférés hatóköre megfelel a felügyelt identitáshoz rendelt Azure-szerepkörnek.


|Azure-szolgáltatás | Hivatkozás|
|---|---|
|Azure Storage | [Megbízható hozzáférés az Azure-hoz – tárterület](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Megbízható hozzáférés – Azure-Service – busz](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Többplatformos hozzáférés – Azure-Event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

> [!NOTE]
> API Management-példányt társíthat legfeljebb 10 felhasználó által hozzárendelt felügyelt identitással.

### <a name="azure-portal"></a>Azure Portal

Ha felügyelt identitást szeretne beállítani a portálon, először létre kell hoznia egy API Management példányt, majd engedélyeznie kell a szolgáltatást.

1. A szokásos módon hozzon létre egy API Management példányt a portálon. Tallózással keresse meg a portálon.
2. Válassza a **felügyelt identitások** lehetőséget.
3. A **felhasználó által hozzárendelt** lapon válassza a **Hozzáadás** lehetőséget.
4. Keresse meg a korábban létrehozott identitást, és válassza ki. Válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Felhasználó által hozzárendelt felügyelt identitás engedélyezésének kiválasztása" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigvezetik egy API Management példány létrehozásán és az identitás Azure PowerShell használatával történő hozzárendelésének lépésein. 

1. Ha szükséges, telepítse a Azure PowerShellt a [Azure PowerShell útmutató](/powershell/azure/install-az-ps)utasításai alapján. Ezután futtassa a parancsot `Connect-AzAccount` Az Azure-beli kapcsolatok létrehozásához.

2. A példány létrehozásához használja a következő kódot. További példák a Azure PowerShell API Management-példánnyal való használatáról: [API Management PowerShell-minták](powershell-samples.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Meglévő szolgáltatás frissítése identitás hozzárendeléséhez a szolgáltatáshoz:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Update an API Management instance
    $userIdentities = @($userAssignedIdentity.Id)
    Set-AzApiManagement -InputObject $apimService -UserAssignedIdentity $userIdentities
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

A következő tulajdonságot az erőforrás-definícióban is létrehozhatja az identitással rendelkező API Management-példányt:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

A felhasználó által hozzárendelt típus hozzáadásával közli az Azure-t, hogy a példányhoz megadott felhasználó által hozzárendelt identitást használja.

A teljes Azure Resource Manager sablon például a következőhöz hasonló lehet:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "resources": [{
        "apiVersion": "2019-12-01",
        "name": "contoso",
        "type": "Microsoft.ApiManagement/service",
        "location": "[resourceGroup().location]",
        "tags": {},
        "sku": {
            "name": "Developer",
            "capacity": "1"
        },
        "properties": {
            "publisherEmail": "admin@contoso.com",
            "publisherName": "Contoso"
        },
        "identity": {
            "type": "UserAssigned",
             "userAssignedIdentities": {
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
             }
        },
         "dependsOn": [       
          "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
        ]
    }]
}
```

A szolgáltatás létrehozása után a következő tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

A `principalId` tulajdonság az Azure ad-felügyelethez használt identitás egyedi azonosítója. A `clientId` tulajdonság az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívások során használandó identitás megadására szolgál.

> [!NOTE]
> Egy API Management-példányhoz a rendszerhez hozzárendelt és felhasználó által hozzárendelt identitások is tartozhatnak egyszerre. Ebben az esetben a tulajdonság a következő `type` lesz: `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>A felhasználóhoz rendelt felügyelt identitást használó támogatott forgatókönyvek

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Egyéni TLS/SSL-tanúsítvány beszerzése a API Management-példányhoz Azure Key Vault
Bármely felhasználó által hozzárendelt identitást használhat a API Management-példány és a kulcstartó közötti megbízhatósági kapcsolat létrehozásához. Ezt a megbízhatóságot ezután a Azure Key Vaultban tárolt egyéni TLS/SSL-tanúsítványok beolvasására használhatja. Ezeket a tanúsítványokat az API Management-példányban lévő egyéni tartományokhoz rendelheti hozzá. 

A következő szempontokat vegye figyelembe:

- A titkos kód tartalomtípusának az *Application/x-PKCS12/pfx-profil* kell lennie.
- Használja az Key Vault-tanúsítvány titkos végpontját, amely tartalmazza a titkos kulcsot.

> [!Important]
> Ha nem adja meg a tanúsítvány objektumának verzióját, akkor a API Management a tanúsítvány újabb verzióját a Key Vault frissítése után négy órán belül automatikusan beolvassa.

A teljes sablonhoz lásd: [API Management a kulcstároló-alapú SSL használatával a felhasználó által hozzárendelt identitással](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json).

Ebben a sablonban a következőket fogja telepíteni:

* Azure API Management
* Azure által felügyelt felhasználóhoz rendelt identitás
* Azure-kulcstartó az SSL/TLS-tanúsítvány tárolásához

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Hitelesítés a háttérben a felhasználó által hozzárendelt identitás használatával

A felhasználó által hozzárendelt identitás használatával a [hitelesítéssel felügyelt identitás](api-management-authentication-policies.md#ManagedIdentity) -házirenddel végezheti el a hitelesítést a háttérben.

## <a name="remove-an-identity"></a><a name="remove"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitást úgy távolíthatja el, ha letiltja a szolgáltatást a portálon vagy a Azure Resource Manager sablonon, ahogyan azt létrehozták. A felhasználó által hozzárendelt identitások egyenként eltávolíthatók. Az összes identitás eltávolításához állítsa az identitás típusát a következőre: `"None"` .

A rendszer által hozzárendelt identitások eltávolítása az Azure AD-ből is törölve lesz. A rendszer által hozzárendelt identitások automatikusan törlődnek az Azure AD-ből, ha a API Management példányt törlik.

Ha a Azure Resource Manager sablonnal szeretné eltávolítani az összes identitást, frissítse ezt a szakaszt:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Ha egy API Management-példány egyéni SSL-tanúsítvánnyal van konfigurálva Key Vault és megpróbál letiltani egy felügyelt identitást, a kérelem sikertelen lesz.
>
> A blokkolás feloldásához váltson át egy Azure Key Vault-tanúsítványról egy beágyazott kódolt tanúsítványra, majd tiltsa le a felügyelt identitást. További információ: [Egyéni tartománynév konfigurálása](configure-custom-domain.md).

## <a name="next-steps"></a>Következő lépések

További információ az Azure-erőforrások felügyelt identitásáról:

* [Mik azok az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)
* [Hitelesítés felügyelt identitással egy házirendben](./api-management-authentication-policies.md#ManagedIdentity)
