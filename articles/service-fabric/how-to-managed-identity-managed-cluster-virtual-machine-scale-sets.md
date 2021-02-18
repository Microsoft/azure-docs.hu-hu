---
title: Felügyelt identitás hozzáadása egy Service Fabric felügyelt fürtcsomópont-típushoz (előzetes verzió)
description: Ez a cikk bemutatja, hogyan adhat hozzá felügyelt identitást egy Service Fabric felügyelt fürtcsomópont-típushoz
ms.topic: how-to
ms.date: 11/24/2020
ms.custom: references_regions
ms.openlocfilehash: 3ff5d66160ddbb037469378634826fd9eeae0c54
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651646"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type-preview"></a>Felügyelt identitás hozzáadása egy Service Fabric felügyelt fürtcsomópont-típushoz (előzetes verzió)

Egy Service Fabric felügyelt fürt mindegyik csomópont-típusát egy virtuálisgép-méretezési csoport támogatja. Ahhoz, hogy a felügyelt identitások felügyelt fürtcsomópont-típussal használhatók legyenek, `vmManagedIdentity` hozzá lett adva egy tulajdonság a használni kívánt identitások listáját tartalmazó csomópont típusú definíciók listájához `userAssignedIdentities` . A funkciók tükrözik, hogy a felügyelt identitások hogyan használhatók nem felügyelt fürtökben, például felügyelt identitás használata a [Azure Key Vault virtuálisgép-méretezési csoport bővítménnyel](../virtual-machines/extensions/key-vault-windows.md).


A felügyelt identitásnak a csomópontok típusán való használatát Service Fabric felügyelt fürt üzembe helyezésére példaként tekintse meg [ezt a sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). A támogatott régiók listáját itt találja: [felügyelt fürt – gyakori kérdések](./faq-managed-cluster.md#what-regions-are-supported-in-the-preview).

> [!NOTE]
> Ehhez a szolgáltatáshoz csak a felhasználó által hozzárendelt identitások támogatottak.

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

* Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
* Ha azt tervezi, hogy a PowerShellt használja, [telepítse](/cli/azure/install-azure-cli) az Azure CLI-t a CLI-hivatkozások parancsainak futtatásához.

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

A felhasználó által hozzárendelt felügyelt identitást egy Azure Resource Manager (ARM) sablon erőforrások szakaszában lehet meghatározni a telepítéskor történő létrehozáshoz:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

vagy a PowerShell használatával létrehozva:

```powershell
az group create --name <resourceGroupName> --location <location>
az identity create --name <userAssignedIdentityName> --resource-group <resourceGroupName>
```

## <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Szerepkör-hozzárendelés hozzáadása Service Fabric erőforrás-szolgáltatóval

Adjon hozzá egy szerepkör-hozzárendelést a felügyelt identitáshoz a Service Fabric erőforrás-szolgáltatói alkalmazással. Ez a hozzárendelés lehetővé teszi, hogy Service Fabric erőforrás-szolgáltató társítsa az identitást a felügyelt fürt virtuálisgép-méretezési csoportjának. 

A következő értékeket kell használni, ahol alkalmazható:

|Name|A megfelelő Service Fabric erőforrás-szolgáltató értéke|
|----|-------------------------------------|
|Alkalmazásazonosító|74cb6831-0dbb-4be1-8206-fd4df301cdc2|
|Objektumazonosító|fbc587f2-66f5-4459-a027-bcd908b9d278|


|Szerepkör-definíció neve|Szerepkör-definíció azonosítója|
|----|-------------------------------------|
|Felügyelt identitás operátora|f1a07417-d97a-45cb-824c-7a7467783830
|



Ez a szerepkör-hozzárendelés az objektum azonosítója és a szerepkör-definíció AZONOSÍTÓjának használatával adható meg az erőforrások szakaszban:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "fbc587f2-66f5-4459-a027-bcd908b9d278" 
    } 
}, 
```

vagy a PowerShell használatával hozható létre az alkalmazás-azonosító és a szerepkör-definíció azonosítója segítségével:

```powershell
New-AzRoleAssignment -ApplicationId 74cb6831-0dbb-4be1-8206-fd4df301cdc2 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

vagy az objektumazonosító és a szerepkör-definíció azonosítója:

```powershell
New-AzRoleAssignment -PrincipalId fbc587f2-66f5-4459-a027-bcd908b9d278 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

## <a name="add-managed-identity-properties-to-node-type-definition"></a>Felügyelt identitás tulajdonságainak hozzáadása a csomópont típusú definícióhoz

Végül adja hozzá a `vmManagedIdentity` és a `userAssignedIdentities` tulajdonságokat a felügyelt fürt csomópont típusú definíciójában. Ügyeljen arra, hogy a esetében a **2021-01-01 – Preview** vagy újabb verziót használja `apiVersion` .

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-01-01-preview",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
            ]
        }
    }
}
```

Az üzembe helyezést követően a létrehozott felügyelt identitás hozzá lett adva a kijelölt csomópont típusú virtuálisgép-méretezési csoporthoz, és az elvárt módon használható, ugyanúgy, mint a nem felügyelt fürtök esetében.

## <a name="troubleshooting"></a>Hibaelhárítás

A szerepkör-hozzárendelés megfelelő hozzáadása nem sikerült, mert a telepítés során a következő hiba történik:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Azure Portal telepítési hiba, amely azt jelzi, hogy a SFRP objektuma/alkalmazás-azonosítója nem rendelkezik jogosultsággal az Identitáskezelés tevékenység végrehajtásához.":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazás üzembe helyezése Service Fabric felügyelt fürtön](./tutorial-managed-cluster-deploy-app.md)