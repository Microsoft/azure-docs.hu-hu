---
title: Felügyelt identitások használata az Azure API Management | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre rendszer által hozzárendelt és felhasználó által hozzárendelt identitásokat a API Management a Azure Portal, a PowerShell és egy Resource Manager használatával.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 40ee196f53af040e4099fb344de5488109ce001b
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812245"
---
# <a name="use-managed-identities-in-azure-api-management"></a>Felügyelt identitások használata az Azure API Management

Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást egy Azure API Management-példányhoz, és hogyan férhet hozzá más erőforrásokhoz. A Azure Active Directory (Azure AD) által létrehozott felügyelt identitás lehetővé teszi, hogy API Management API Management-példány könnyedén és biztonságosan hozzáférjen más Azure AD által védett erőforrásokhoz, például Azure Key Vault. Az Azure kezeli ezt az identitást, így Önnek nem kell titkos okat kiépítve vagy elforgatni. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md).

Egy példánynak kétféle identitástípust adhat API Management számára:

- A *rendszer által hozzárendelt identitás* a szolgáltatáshoz van kötve, és a szolgáltatás törlésekor törlődik. A szolgáltatás csak egy rendszer által hozzárendelt identitással lehet.
- A *felhasználó által hozzárendelt identitás* egy önálló Azure-erőforrás, amely hozzárendelhető a szolgáltatáshoz. A szolgáltatás több felhasználó által hozzárendelt identitással is lehet.

## <a name="create-a-system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás létrehozása

### <a name="azure-portal"></a>Azure Portal

Ha felügyelt identitást kell beállítania a Azure Portal, először létre kell hoznia egy API Management-példányt, majd engedélyeznie kell a funkciót.

1. Hozzon létre API Management-példányt a portálon, ahogyan általában tenné. Keresse meg a portálon.
2. Válassza **a Felügyelt identitások lehetőséget.**
3. A Rendszer **által hozzárendelt lapon** kapcsolja be az **Állapot** **kapcsolót.** Kattintson a **Mentés** gombra.

    :::image type="content" source="./media/api-management-msi/enable-system-msi.png" alt-text="A rendszer által hozzárendelt felügyelt identitás engedélyezésének kiválasztásai" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigkik egy API Management és identitás hozzárendelését a Azure PowerShell. 

1. Szükség esetén telepítse a Azure PowerShell az útmutatóban található [Azure PowerShell használatával.](/powershell/azure/install-az-ps) Ezután futtassa `Connect-AzAccount` az gombra az Azure-ral való kapcsolat létrehozásához.

2. A példány létrehozásához használja az alábbi kódot. A powershell-példányokkal való Azure PowerShell további API Management a [PowerShell-minták API Management talál.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an API Management Consumption Sku service.
    New-AzApiManagement -ResourceGroupName $resourceGroupName -Name consumptionskuservice -Location $location -Sku Consumption -Organization contoso -AdminEmail contoso@contoso.com -SystemAssignedIdentity
    ```

3. Frissítsen egy meglévő példányt az identitás létrehozásához:

    ```azurepowershell-interactive
    # Get an API Management instance
    $apimService = Get-AzApiManagement -ResourceGroupName $resourceGroupName -Name $apiManagementName

    # Update an API Management instance
    Set-AzApiManagement -InputObject $apimService -SystemAssignedIdentity
    ```

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Létrehozhat egy API Management-példányt egy identitással, ha a következő tulajdonságot tartalmazza az erőforrás-definícióban:

```json
"identity" : {
    "type" : "SystemAssigned"
}
```

Ez a tulajdonság arra utasítja az Azure-t, hogy hozza létre és kezelje a API Management identitását.

Egy teljes sablonsablon például Azure Resource Manager alábbihoz hasonló lehet:

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

A példány a létrehozása után a következő további tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

A `tenantId` tulajdonság azonosítja azt az Azure AD-bérlőt, amelyhez az identitás tartozik. A `principalId` tulajdonság a példány új identitásának egyedi azonosítója. Az Azure AD-ban a szolgáltatásnévnek ugyanaz a neve, mint amit a API Management adott.

> [!NOTE]
> A API Management példányok egyszerre kaphatnak rendszer- és felhasználó által hozzárendelt identitásokat. Ebben az esetben a `type` tulajdonság a következő lesz: `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-system-assigned-identity"></a>Rendszer által hozzárendelt identitást használó támogatott forgatókönyvek

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault"></a>Szerezzen be egyéni TLS-/SSL-tanúsítványt a API Management példányhoz a Azure Key Vault
Egy példány rendszer által hozzárendelt identitásával lekérheti a API Management-ban tárolt egyéni TLS-/SSL-Azure Key Vault. Ezeket a tanúsítványokat ezután egyéni tartományokhoz rendelheti a API Management példányban. A következő szempontokat vegye figyelembe:

- A titkos fájl tartalomtípusának *application/x-pkcs12* kell lennie.
- Használja a Key Vault titkos tanúsítványvégpontot, amely tartalmazza a titkos adatokat.

> [!Important]
> Ha nem adja meg a tanúsítvány objektumverzióját, a API Management automatikusan lekéri a tanúsítvány újabb verzióját a Key Vault.

Az alábbi példa egy Azure Resource Manager, amely a következő lépéseket tartalmazza:

1. Hozzon létre API Management-példányt egy felügyelt identitással.
2. Frissítse egy új Azure Key Vault hozzáférési szabályzatát, és engedélyezze a API Management számára, hogy titkos adatokat szerezzen be róla.
3. Frissítse a API Management-példányt úgy, hogy egyéni tartománynevet ad meg a Key Vault tanúsítványán keresztül.

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

### <a name="authenticate-to-the-back-end-by-using-an-api-management-identity"></a>Hitelesítés a háttér felé egy API Management identitással

A rendszer által hozzárendelt identitással hitelesíthet a háttérrendszerben a [authentication-managed-identity szabályzaton](api-management-authentication-policies.md#ManagedIdentity) keresztül.

### <a name="connect-to-azure-resources-behind-ip-firewall-using-system-assigned-managed-identity"></a><a name="apim-as-trusted-service"></a>Csatlakozás IP-tűzfal mögötti Azure-erőforrásokhoz a rendszer által hozzárendelt felügyelt identitással


API Management a Microsoft megbízható szolgáltatása a következő erőforrásokhoz. Ez lehetővé teszi, hogy a szolgáltatás a következő erőforrásokhoz csatlakozzon egy tűzfal mögött. Miután explicit módon hozzárendeli a megfelelő [Azure-szerepkört](../active-directory/managed-identities-azure-resources/overview.md) az adott erőforráspéldány rendszer által hozzárendelt felügyelt identitásához, a példány hozzáférési hatóköre megfelel a felügyelt identitáshoz rendelt Azure-szerepkörnek.


|Azure-szolgáltatás | Hivatkozás|
|---|---|
|Azure Storage | [Megbízható hozzáférés az azure-storage-hoz](../storage/common/storage-network-security.md?tabs=azure-portal#trusted-access-based-on-system-assigned-managed-identity)|
|Azure Service Bus | [Megbízható hozzáférés az azure-service-bushoz](../service-bus-messaging/service-bus-ip-filtering.md#trusted-microsoft-services)|
|Azure Event Hub | [Trused-access-to-azure-event-hub](../event-hubs/event-hubs-ip-filtering.md#trusted-microsoft-services)|


## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

> [!NOTE]
> Egy felügyelt API Management legfeljebb 10 felhasználó által hozzárendelt felügyelt identitáshoz társíthat.

### <a name="azure-portal"></a>Azure Portal

A felügyelt identitás portálon való beállításához először létre kell hoznia egy API Management-példányt, majd engedélyeznie kell a funkciót.

1. Hozzon létre API Management-példányt a portálon, ahogyan általában tenné. Keresse meg a portálon.
2. Válassza **a Felügyelt identitások lehetőséget.**
3. A **Felhasználóhoz rendelt lapon** válassza a Hozzáadás **lehetőséget.**
4. Keresse meg és válassza ki a korábban létrehozott identitást. Válassza a **Hozzáadás** lehetőséget.

   :::image type="content" source="./media/api-management-msi/enable-user-assigned-msi.png" alt-text="Felhasználó által hozzárendelt felügyelt identitás engedélyezésének kiválasztásai" border="true":::

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő lépések végigmennek egy API Management példány létrehozásán és identitás hozzárendelésének folyamatán Azure PowerShell. 

1. Ha szükséges, telepítse a Azure PowerShell az útmutatóban található [Azure PowerShell használatával.](/powershell/azure/install-az-ps) Ezután futtassa `Connect-AzAccount` az gombra az Azure-ral való kapcsolat létrehozásához.

2. A példány létrehozásához használja az alábbi kódot. A powershell-példányokkal való Azure PowerShell további API Management a [PowerShell-minták API Management talál.](powershell-samples.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create an API Management Consumption Sku service.
    $userIdentities = @($userAssignedIdentity.Id)

    New-AzApiManagement -ResourceGroupName $resourceGroupName -Location $location -Name $apiManagementName -Organization contoso -AdminEmail admin@contoso.com -Sku Consumption -UserAssignedIdentity $userIdentities
    ```

3. Meglévő szolgáltatás frissítése egy identitás szolgáltatáshoz való hozzárendeléséhez:

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

Létrehozhat egy API Management-példányt egy identitással, ha a következő tulajdonságot tartalmazza az erőforrás-definícióban:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

A felhasználó által hozzárendelt típus hozzáadása arra utasítja az Azure-t, hogy a példányhoz megadott, felhasználó által hozzárendelt identitást használja.

Egy teljes sablonsablon például Azure Resource Manager alábbihoz hasonló lehet:

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

A szolgáltatás a létrehozásakor a következő további tulajdonságokkal rendelkezik:

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

A `principalId` tulajdonság az Azure AD-felügyelethez használt identitás egyedi azonosítója. A tulajdonság az alkalmazás új identitásának egyedi azonosítója, amely a futásidejű hívások során használni kívánt `clientId` identitás megadására használatos.

> [!NOTE]
> A API Management példányok egyszerre kaphatnak rendszer- és felhasználó által hozzárendelt identitásokat. Ebben az esetben a `type` tulajdonság a következő lesz: `SystemAssigned,UserAssigned` .

## <a name="supported-scenarios-using-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitást használó támogatott forgatókönyvek

### <a name="obtain-a-custom-tlsssl-certificate-for-the-api-management-instance-from-azure-key-vault"></a><a name="use-ssl-tls-certificate-from-azure-key-vault-ua"></a>Szerezzen be egyéni TLS-/SSL-tanúsítványt a API Management példányhoz a Azure Key Vault
Bármely felhasználó által hozzárendelt identitással megbízhatóságot létesíthet egy API Management KeyVault és között. Ezzel a megbízhatósági kapcsolattal lekérheti a tárolóban tárolt egyéni TLS-/SSL-Azure Key Vault. Ezeket a tanúsítványokat ezután egyéni tartományokhoz rendelheti a API Management példányban. 

A következő szempontokat vegye figyelembe:

- A titkos fájl tartalomtípusának *application/x-pkcs12 típusúnak kell lennie.*
- Használja a Key Vault titkos tanúsítványvégpontját, amely tartalmazza a titkos adatokat.

> [!Important]
> Ha nem adja meg a tanúsítvány objektumverzióját, a API Management automatikusan lekéri a tanúsítvány újabb verzióját a Key Vault.

A teljes sablont lásd: [KeyVault-API Management SSL használata felhasználó által hozzárendelt identitással.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-api-management-key-vault-create/azuredeploy.json)

Ebben a sablonban a következőt fogja üzembe helyezni:

* Azure API Management
* Azure-beli felügyelt felhasználóhoz rendelt identitás
* Azure KeyVault az SSL-/TLS-tanúsítvány tárolásához

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-api-management-key-vault-create%2Fazuredeploy.json)

### <a name="authenticate-to-the-back-end-by-using-a-user-assigned-identity"></a>Hitelesítés a háttérben felhasználó által hozzárendelt identitással

A felhasználó által hozzárendelt identitással hitelesíthet a háttér felé a [authentication-managed-identity szabályzaton](api-management-authentication-policies.md#ManagedIdentity) keresztül.

## <a name="remove-an-identity"></a><a name="remove"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitás eltávolításához tiltsa le a funkciót a portálon vagy a Azure Resource Manager sablonon keresztül, ugyanúgy, ahogyan azt létrehozták. A felhasználó által hozzárendelt identitások egyenként távolíthatók el. Az összes identitás eltávolításához állítsa az identitástípust a következőre: `"None"` .

A rendszer által hozzárendelt identitás ily módon való eltávolítása az Azure AD-ból is törli azt. A rendszer a rendszer által hozzárendelt identitásokat is automatikusan eltávolítja az Azure AD-ból a API Management példány törlésekor.

Ha az összes identitást el szeretné távolítani a Azure Resource Manager sablonnal, frissítse ezt a szakaszt:

```json
"identity": {
    "type": "None"
}
```

> [!Important]
> Ha egy API Management-példány egyéni SSL-tanúsítvánnyal van konfigurálva a Key Vault és megpróbál letiltani egy felügyelt identitást, a kérés sikertelen lesz.
>
> A zárolás feloldásához váltson egy Azure Key Vault tanúsítványról egy beágyazott kódolású tanúsítványra, majd tiltsa le a felügyelt identitást. További információ: [Egyéni tartománynév konfigurálása.](configure-custom-domain.md)

## <a name="next-steps"></a>Következő lépések

További információ az Azure-erőforrások felügyelt identitási szolgáltatásról:

* [Mik az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)
* [Azure Resource Manager-sablonok](https://github.com/Azure/azure-quickstart-templates)
* [Hitelesítés felügyelt identitással egy szabályzatban](./api-management-authentication-policies.md#ManagedIdentity)
