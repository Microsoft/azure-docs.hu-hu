---
title: Felügyelt identitások konfigurálása Azure-beli virtuális gépen sablon használatával – Azure AD
description: Részletes útmutató az Azure-beli virtuális gépeken futó Azure-erőforrások felügyelt identitásának konfigurálásához Azure Resource Manager sablon használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4948e17d1e0e782a8fa18c3eb5a2185e816a459a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631404"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Felügyelt identitások konfigurálása Azure-beli virtuális gépen lévő Azure-erőforrásokhoz sablonok használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban.

Ebben a cikkben a Azure Resource Manager telepítési sablonjának használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy Azure-beli virtuális gépen:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a Azure Resource Manager telepítési sablon használatát, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A Azure Portal és a parancsfájlok futtatásához hasonlóan [Azure Resource Manager](../../azure-resource-manager/management/overview.md) -sablonok lehetővé teszik az Azure-erőforráscsoport által definiált új vagy módosított erőforrások telepítését. A sablonok szerkesztéséhez és üzembe helyezéséhez több lehetőség is rendelkezésre áll, a helyi és a portálon is, beleértve a következőket:

   - [Egyéni sablon használata az Azure piactéren](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy teljesen új sablont hozzon létre, vagy egy meglévő gyakori vagy gyors üzembe helyezési [sablonon](https://azure.microsoft.com/documentation/templates/)alapuljon.
   - Egy meglévő erőforráscsoporthoz származtatva, az [eredeti telepítésből](../../azure-resource-manager/templates/export-template-portal.md)vagy az üzemelő példány [aktuális állapotától](../../azure-resource-manager/templates/export-template-portal.md)származó sablon exportálásával.
   - Helyi JSON- [szerkesztő (például vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)használata, majd a PowerShell vagy a parancssori felület használatával történő feltöltés és üzembe helyezés.
   - A Visual Studio [Azure erőforráscsoport-projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használatával hozzon létre és helyezzen üzembe egy sablont.  

A választott lehetőségtől függetlenül a sablon szintaxisa megegyezik a kezdeti üzembe helyezés és az újratelepítés során. A rendszer vagy a felhasználó által hozzárendelt felügyelt identitás engedélyezése egy új vagy meglévő virtuális gépen ugyanúgy történik. Alapértelmezés szerint a Azure Resource Manager a központi telepítések [növekményes frissítését](../../azure-resource-manager/templates/deployment-modes.md) is.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban egy Azure Resource Manager sablonnal engedélyezheti és tilthatja le a rendszerhez rendelt felügyelt identitást.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure-beli virtuális gép vagy egy meglévő virtuális gép létrehozása során

Ha engedélyezni szeretné a rendszer által hozzárendelt felügyelt identitást egy virtuális gépen, a fióknak szüksége van a [virtuálisgép-közreműködő](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

2. A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez töltse be a sablont egy Szerkesztőbe, keresse meg a ( `Microsoft.Compute/virtualMachines` z) szakaszban található kamatot, `resources` és adja hozzá a `"identity"` tulajdonságot a tulajdonsággal megegyező szinten `"type": "Microsoft.Compute/virtualMachines"` . Használja a következő szintaxist:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Ha elkészült, a következő szakaszokat kell hozzáadnia a `resource` sablon szakaszához, és a következőhöz hasonlónak kell lennie:

   ```JSON
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                }                        
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Szerepkör hozzárendelése a virtuális gép rendszer által hozzárendelt felügyelt identitásához

Miután engedélyezte a rendszerhez rendelt felügyelt identitást a virtuális gépen, érdemes megadnia egy olyan szerepkört, mint az **olvasó** hozzáférése ahhoz az erőforráscsoporthoz, amelyben létrehozták.

Ha szerepkört szeretne hozzárendelni a virtuális gép rendszerhez rendelt identitásához, a fióknak szüksége van a [felhasználói hozzáférés rendszergazdai](../../role-based-access-control/built-in-roles.md#user-access-administrator) szerepkör-hozzárendelésére.

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe](#azure-resource-manager-templates) , és adja hozzá a következő információkat, hogy a virtuálisgép- **olvasó** hozzáférjen ahhoz az erőforráscsoporthoz, amelyben létrehozták.  A sablon szerkezete a szerkesztőtől és a választott telepítési modelltől függően változhat.

   A `parameters` következő szakaszban adja hozzá a következőket:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    A `variables` következő szakaszban adja hozzá a következőket:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    A `resources` következő szakaszban adja hozzá a következőket:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszerhez rendelt felügyelt identitás letiltása egy Azure-beli virtuális gépről

Ha a rendszer által hozzárendelt felügyelt identitást el szeretné távolítani egy virtuális gépről, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) szerepkörének hozzárendelésére.  Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe](#azure-resource-manager-templates) , és keresse meg a keresett `Microsoft.Compute/virtualMachines` erőforrást a `resources` szakaszon belül. Ha olyan virtuális géppel rendelkezik, amely csak rendszerhez rendelt felügyelt identitással rendelkezik, letilthatja az identitás típusának módosításával `None` .  

   **Microsoft. számítási/virtualMachines API-verzió 2018-06-01**

   Ha a virtuális gépen a rendszer és a felhasználó által hozzárendelt felügyelt identitás is található, távolítsa el `SystemAssigned` az identitás típusát, és tartsa meg a `UserAssigned` `userAssignedIdentities` szótár értékeit.

   **Microsoft. számítási/virtualMachines API-verzió 2018-06-01**

   Ha az `apiVersion` Ön `2017-12-01` és a virtuális gépe egyaránt rendelkezik rendszer-és felhasználó által hozzárendelt felügyelt identitásokkal, távolítsa el `SystemAssigned` az identitás típusát, és tartsa meg a `UserAssigned` `identityIds` felhasználó által hozzárendelt felügyelt identitások tömbjét.  

Az alábbi példa bemutatja, hogyan távolíthat el egy rendszerhez rendelt felügyelt identitást egy olyan virtuális gépről, amely nem rendelkezik felhasználó által hozzárendelt felügyelt identitásokkal:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban egy felhasználó által hozzárendelt felügyelt identitást rendel hozzá egy Azure-beli virtuális géphez Azure Resource Manager sablon használatával.

> [!Note]
> Felhasználó által hozzárendelt, Azure Resource Manager sablon használatával létrehozott felügyelt identitás létrehozásával kapcsolatban tekintse meg [a felhasználó által hozzárendelt felügyelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)című témakört.

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése Azure-beli virtuális géphez

Ha felhasználó által hozzárendelt identitást szeretne hozzárendelni egy virtuális géphez, a fióknak szüksége van a [virtuális gép közreműködői](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) és [felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendeléseire. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. A `resources` elem alatt adja hozzá a következő bejegyzést egy felhasználóhoz rendelt felügyelt identitás a virtuális géphez való hozzárendeléséhez.  Ügyeljen arra, hogy a helyére a `<USERASSIGNEDIDENTITY>` létrehozott, felhasználó által hozzárendelt felügyelt identitás nevét adja meg.

   **Microsoft. számítási/virtualMachines API-verzió 2018-06-01**

   Ha Ön `apiVersion` az `2018-06-01` , a felhasználó által hozzárendelt felügyelt identitások a `userAssignedIdentities` szótár formátumában tárolódnak, és az `<USERASSIGNEDIDENTITYNAME>` értéket a sablon szakaszában meghatározott változóban kell tárolni `variables` .

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Microsoft. számítási/virtualMachines API-verzió 2017-12-01**

   Ha Ön `apiVersion` az `2017-12-01` , a felhasználó által hozzárendelt felügyelt identitások a `identityIds` tömbben tárolódnak, és az `<USERASSIGNEDIDENTITYNAME>` értéket a sablon szakaszában meghatározott változóban kell tárolni `variables` .

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Ha elkészült, a következő szakaszokat kell hozzáadnia a `resource` sablon szakaszához, és a következőhöz hasonlónak kell lennie:

   **Microsoft. számítási/virtualMachines API-verzió 2018-06-01**    

   ```JSON
     "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        }
    ] 
   ```
   **Microsoft. számítási/virtualMachines API-verzió 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        }
   ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása Azure-beli virtuális gépről

A felhasználó által hozzárendelt identitás egy [virtuális gépről](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) való eltávolításához a fióknak szüksége van a virtuálisgép-közreműködő szerepkör-hozzárendelésre. Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuális gépet tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe](#azure-resource-manager-templates) , és keresse meg a keresett `Microsoft.Compute/virtualMachines` erőforrást a `resources` szakaszon belül. Ha van olyan virtuális gépe, amely csak felhasználó által hozzárendelt felügyelt identitással rendelkezik, letilthatja az identitás típusának módosításával `None` .

   Az alábbi példa bemutatja, hogyan távolíthatja el a felhasználó által hozzárendelt összes felügyelt identitást egy olyan virtuális gépről, amely nem rendelkezik rendszerhez rendelt felügyelt identitásokkal:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft. számítási/virtualMachines API-verzió 2018-06-01**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuális gépről, távolítsa el azt a `useraAssignedIdentities` szótárból.

   Ha rendszerhez rendelt felügyelt identitással rendelkezik, tartsa meg az `type` érték alatti értékben `identity` .

   **Microsoft. számítási/virtualMachines API-verzió 2017-12-01**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuális gépről, távolítsa el azt a `identityIds` tömbből.

   Ha rendszerhez rendelt felügyelt identitással rendelkezik, tartsa meg az `type` érték alatti értékben `identity` .

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitások az Azure-erőforrások áttekintéséhez](overview.md).
