---
title: Sablon konfigurálása felügyelt identitások használatára virtuálisgép-méretezési készletekben – Azure AD
description: Részletes útmutató az Azure-erőforrások felügyelt identitásának konfigurálásához egy virtuálisgép-méretezési Azure Resource Manager használatával.
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
ms.date: 04/12/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e948b96022972dcf702ac5a4d8be85c9afe16e7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365977"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Azure-beli virtuálisgép-méretezésű Azure-erőforrások felügyelt identitásának konfigurálása sablon használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitása automatikusan felügyelt identitást biztosít az Azure-szolgáltatásoknak a Azure Active Directory. Ezzel az identitással bármely Azure AD-hitelesítést támogató szolgáltatásban hitelesíthet anélkül, hogy a hitelesítő adatok a kódban lenne.

Ebből a cikkből megtudhatja, hogyan végezheti el a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy Azure-beli virtuálisgép-méretezési Azure Resource Manager használatával:

- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása azure-beli virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása azure-beli virtuálisgép-méretezési csoporton

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintés szakaszt.](overview.md) Mindenképpen tekintse át a rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt **[identitások közötti különbséget.](overview.md#managed-identity-types)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A cikkben említett felügyeleti műveletek végrehajtásához a fiókjának a következő Azure-beli szerepköralapú hozzáférés-vezérlési hozzárendelésre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

    - [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) virtuálisgép-méretezési csoport létrehozásához, valamint a rendszer és/vagy felhasználó által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához egy virtuálisgép-méretezési csoportból.
    - [Felügyelt identitás közreműködője](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitáskezelői](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitások virtuálisgép-méretezési csoportokhoz való hozzárendelése és eltávolítása érdekében.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A Azure Portal és a szkriptek futtatásához Azure Resource Manager [sablonok](../../azure-resource-manager/management/overview.md) lehetővé teszi az Azure-erőforráscsoport által definiált új vagy módosított erőforrások üzembe helyezését. A sablonok szerkesztéséhez és üzembe helyezéséhez számos lehetőség áll rendelkezésre, helyi és portálalapúak is, például:

   - Egyéni sablon [használata a](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)Azure Marketplace, amellyel sablont hozhat létre az elejétől kezdve, vagy egy meglévő közös vagy gyorsindítási [sablonra alapozhatja azt.](https://azure.microsoft.com/documentation/templates/)
   - Származtatás meglévő erőforráscsoportból, sablon exportálása [](../../azure-resource-manager/templates/export-template-portal.md)az eredeti üzemelő példányból vagy az üzemelő példány aktuális [állapotából.](../../azure-resource-manager/templates/export-template-portal.md)
   - Helyi [JSON-szerkesztő (például VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)használatával, majd a PowerShell vagy a parancssori felület használatával való feltöltés és üzembe helyezés.
   - A Visual Studio [Azure-erőforráscsoport](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) projekt használatával sablont hozhat létre és helyezhet üzembe.  

A választott beállítástól függetlenül a sablon szintaxisa ugyanaz a kezdeti üzembe helyezés és ismételt üzembe helyezés során. Az Azure-erőforrások felügyelt identitásának engedélyezése egy új vagy meglévő virtuális gépen ugyanúgy történik. Emellett alapértelmezés szerint a Azure Resource Manager növekményes [frissítést is tesz az](../../azure-resource-manager/templates/deployment-modes.md) üzemelő példányok számára.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban engedélyezheti és letilthatja a rendszer által hozzárendelt felügyelt identitást egy Azure Resource Manager használatával.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport vagy meglévő virtuálisgép-méretezési csoport létrehozása során

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuálisgép-méretezési készletet tartalmazó Azure-előfizetéshez van társítva.
2. A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez töltse be a sablont egy szerkesztőbe, keresse meg a kívánt erőforrást az erőforrások szakaszban, és adja hozzá a tulajdonságot a tulajdonsággal azonos `Microsoft.Compute/virtualMachinesScaleSets` `identity` `"type": "Microsoft.Compute/virtualMachinesScaleSets"` szinten. Használja a következő szintaxist:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

4. Ha végzett, a következő szakaszokat kell hozzáadnia a sablon erőforrás szakaszhoz, és az alábbihoz hasonlónak kell lennie:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
        
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rendszer által hozzárendelt felügyelt identitás letiltása Azure-beli virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoportja van, amelyhez már nincs szükség rendszer által hozzárendelt felügyelt identitásra:

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuálisgép-méretezési készletet tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe,](#azure-resource-manager-templates) és keresse meg `Microsoft.Compute/virtualMachineScaleSets` a kívánt erőforrást a `resources` szakaszban. Ha olyan virtuális gépe van, amely csak rendszer által hozzárendelt felügyelt identitással rendelkezik, letilthatja, ha az identitás típusát a következőre módosítja: `None` .

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01-es verzió**

   Ha az apiVersion és a virtuális gép rendszer- és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, távolítsa el az identitástípust, és tartsa meg a `2018-06-01` `SystemAssigned` `UserAssigned` userAssignedIdentities szótár értékeit.

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01-es verzió**

   Ha az apiVersion az, és a virtuálisgép-méretezési csoport rendszer- és felhasználó által hozzárendelt felügyelt identitásokkal is rendelkezik, távolítsa el az identitástípust, és tartsa meg a felhasználó által hozzárendelt felügyelt identitások `2017-12-01` `SystemAssigned` `UserAssigned` `identityIds` tömböt.



   Az alábbi példa bemutatja, hogyan távolíthat el egy rendszer által hozzárendelt felügyelt identitást a felhasználó által hozzárendelt felügyelt identitások nélkül található virtuálisgép-méretezési csoportból:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban felhasználó által hozzárendelt felügyelt identitást rendel hozzá egy virtuálisgép-méretezési csoporthoz az Azure Resource Manager használatával.

> [!Note]
> Ha felhasználó által hozzárendelt felügyelt identitást Azure Resource Manager sablonnal, lásd: Felhasználó által [hozzárendelt felügyelt identitás létrehozása.](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoporthoz

1. A elem alatt adja hozzá a következő bejegyzést egy felhasználó által hozzárendelt felügyelt identitás a `resources` virtuálisgép-méretezési csoporthoz való hozzárendeléshez.  Ne feledjük a helyére a létrehozott, felhasználó által hozzárendelt `<USERASSIGNEDIDENTITY>` felügyelt identitás nevét.

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01-es verzió**

   Ha az apiVersion , a felhasználó által hozzárendelt felügyelt identitások szótár formátumban vannak tárolva, az értéket pedig a sablon szakaszában meghatározott változóban `2018-06-01` `userAssignedIdentities` kell `<USERASSIGNEDIDENTITYNAME>` `variables` tárolni.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01-es verzió**

   Ha a felhasználó által hozzárendelt felügyelt identitások a tömbben vannak tárolva, az értéket pedig a sablon változók szakaszában meghatározott változóban `apiVersion` `2017-12-01` kell `identityIds` `<USERASSIGNEDIDENTITYNAME>` tárolni.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }

3. When you are done, your template should look similar to the following:

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API 2017-12-01-es verzió**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                }
            }
        }
    ]
   ```
### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoportja van, amelyhez már nincs szükség felhasználó által hozzárendelt felügyelt identitásra:

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuálisgép-méretezési készletet tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe,](#azure-resource-manager-templates) és keresse meg a fontos `Microsoft.Compute/virtualMachineScaleSets` erőforrást a `resources` szakaszban. Ha olyan virtuálisgép-méretezési csoportja van, amely csak felhasználó által hozzárendelt felügyelt identitással rendelkezik, letilthatja azt, ha az identitástípust a következőre módosítja: `None` .

   Az alábbi példa bemutatja, hogyan távolíthat el minden felhasználó által hozzárendelt felügyelt identitást a rendszer által hozzárendelt felügyelt identitások nélküli virtuális gépről:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01-es verzió**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuálisgép-méretezési csoportból, távolítsa el a `userAssignedIdentities` szótárból.

   Ha rendszer által hozzárendelt identitással bír, tartsa meg az érték `type` `identity` alatt.

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01-es verzió**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuálisgép-méretezési csoportból, távolítsa el a `identityIds` tömbből.

   Ha rendszer által hozzárendelt felügyelt identitással bír, tartsa meg az érték `type` `identity` alatt.

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése.](overview.md)