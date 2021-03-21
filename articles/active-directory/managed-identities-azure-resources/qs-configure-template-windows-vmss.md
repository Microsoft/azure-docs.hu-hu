---
title: Sablon konfigurálása felügyelt identitások használatára a virtuálisgép-méretezési csoportokban – Azure AD
description: Részletes útmutató az Azure-erőforrások felügyelt identitások konfigurálásához egy virtuálisgép-méretezési csoporton Azure Resource Manager sablon használatával.
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
ms.openlocfilehash: 377bbb9ce111f3cf2daf8426e128186711c30e5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587451"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Felügyelt identitások konfigurálása Azure-beli virtuális gépek méretezéséhez sablon használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Ezt az identitást használhatja bármely olyan szolgáltatás hitelesítéséhez, amely támogatja az Azure AD-hitelesítést, és nem rendelkezik hitelesítő adatokkal a kódban.

Ebből a cikkből megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-beli virtuálisgép-méretezési csoportokon a Azure Resource Manager telepítési sablon használatával:
- A rendszer által hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure virtuálisgép-méretezési csoporton
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása egy Azure-beli virtuálisgép-méretezési csoporton

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárbeli szerepkör-hozzárendelésre.

    - Virtuálisgép-méretezési csoport létrehozásához, illetve a virtuálisgép-méretezési csoportból származó rendszer-és/vagy felhasználó által hozzárendelt felügyelt identitás engedélyezéséhez és eltávolításához a [virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .
    - [Felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitás-kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoportba.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

A Azure Portal és a parancsfájlok futtatásához hasonlóan [Azure Resource Manager](../../azure-resource-manager/management/overview.md) -sablonok lehetővé teszik az Azure-erőforráscsoport által definiált új vagy módosított erőforrások telepítését. A sablonok szerkesztéséhez és üzembe helyezéséhez több lehetőség is rendelkezésre áll, a helyi és a portálon is, beleértve a következőket:

   - [Egyéni sablon használata az Azure piactéren](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy teljesen új sablont hozzon létre, vagy egy meglévő gyakori vagy gyors üzembe helyezési [sablonon](https://azure.microsoft.com/documentation/templates/)alapuljon.
   - Egy meglévő erőforráscsoporthoz származtatva, az [eredeti telepítésből](../../azure-resource-manager/templates/export-template-portal.md)vagy az üzemelő példány [aktuális állapotától](../../azure-resource-manager/templates/export-template-portal.md)származó sablon exportálásával.
   - Helyi JSON- [szerkesztő (például vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)használata, majd a PowerShell vagy a parancssori felület használatával történő feltöltés és üzembe helyezés.
   - A Visual Studio [Azure erőforráscsoport-projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használatával hozzon létre és helyezzen üzembe egy sablont.  

A választott lehetőségtől függetlenül a sablon szintaxisa megegyezik a kezdeti üzembe helyezés és az újratelepítés során. Egy új vagy meglévő virtuális gépen a felügyelt identitások engedélyezése az Azure-erőforrásokhoz hasonló módon történik. Alapértelmezés szerint a Azure Resource Manager a központi telepítések [növekményes frissítését](../../azure-resource-manager/templates/deployment-modes.md) is.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban egy Azure Resource Manager sablon használatával engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitást.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése a virtuális gépek méretezési csoportjának vagy egy meglévő virtuálisgép-méretezési csoport létrehozása során

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez van társítva.
2. A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez töltse be a sablont egy Szerkesztőbe, keresse meg az `Microsoft.Compute/virtualMachinesScaleSets` erőforrások szakaszban található érdeklődési szintet, és adja hozzá a `identity` tulajdonságot a `"type": "Microsoft.Compute/virtualMachinesScaleSets"` tulajdonsággal megegyező szinten. Használja a következő szintaxist:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Igény szerint kiépítheti az Azure-erőforrások virtuálisgép-méretezési csoport bővítményének felügyelt identitásait úgy, hogy megadja azt a `extensionProfile` sablon elemében. Ez a lépés nem kötelező, mivel az Azure Instance Metadata Service (IMDS) identitás-végpontját is használhatja a tokenek lekéréséhez.  További információ: [Migrálás virtuálisgép-bővítményből az Azure IMDS a hitelesítéshez](howto-migrate-vm-extension.md).


4. Ha elkészült, a következő szakaszokat kell hozzáadnia a sablon erőforrás szakaszához, és a következőhöz hasonlónak kell lennie:

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rendszerhez rendelt felügyelt identitás letiltása egy Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amelynek már nincs szüksége a rendszerhez rendelt felügyelt identitásra:

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe](#azure-resource-manager-templates) , és keresse meg a keresett `Microsoft.Compute/virtualMachineScaleSets` erőforrást a `resources` szakaszon belül. Ha olyan virtuális géppel rendelkezik, amely csak rendszerhez rendelt felügyelt identitással rendelkezik, letilthatja az identitás típusának módosításával `None` .

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2018-06-01**

   Ha a apiVersion van, `2018-06-01` és a virtuális gépen a rendszer és a felhasználó által hozzárendelt felügyelt identitás is található, távolítsa el `SystemAssigned` az identitás típusát, és tartsa meg `UserAssigned` a userAssignedIdentities-szótár értékeit.

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2018-06-01**

   Ha a apiVersion, `2017-12-01` és a virtuálisgép-méretezési csoport mind a rendszer, mind a felhasználó által hozzárendelt felügyelt identitással rendelkezik, távolítsa el `SystemAssigned` az identitás típusát, és tartsa meg a `UserAssigned` `identityIds` felhasználó által hozzárendelt felügyelt identitások tömbjét.



   Az alábbi példa bemutatja, hogyan távolíthat el egy rendszerhez rendelt felügyelt identitást egy olyan virtuálisgép-méretezési csoportból, amely nem rendelkezik felhasználó által hozzárendelt felügyelt identitásokkal:

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

Ebben a szakaszban egy felhasználó által hozzárendelt felügyelt identitást rendel hozzá egy virtuálisgép-méretezési csoporthoz Azure Resource Manager sablon használatával.

> [!Note]
> Felhasználó által hozzárendelt, Azure Resource Manager sablon használatával létrehozott felügyelt identitás létrehozásával kapcsolatban tekintse meg [a felhasználó által hozzárendelt felügyelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)című témakört.

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Felhasználóhoz rendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoportokhoz

1. A `resources` elem alatt adja hozzá a következő bejegyzést egy felhasználóhoz rendelt felügyelt identitás hozzárendeléséhez a virtuálisgép-méretezési csoporthoz.  Ügyeljen arra, hogy a helyére a `<USERASSIGNEDIDENTITY>` létrehozott, felhasználó által hozzárendelt felügyelt identitás nevét adja meg.

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2018-06-01**

   Ha a apiVersion `2018-06-01` , a felhasználó által hozzárendelt felügyelt identitások a `userAssignedIdentities` szótár formátumában tárolódnak, és az `<USERASSIGNEDIDENTITYNAME>` értéket a sablon szakaszában meghatározott változóban kell tárolni `variables` .

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

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2017-12-01**

   Ha az `apiVersion` `2017-12-01` vagy a korábbi, a felhasználó által hozzárendelt felügyelt identitások a `identityIds` tömbben tárolódnak, és az `<USERASSIGNEDIDENTITYNAME>` értéket a sablon változók szakaszában meghatározott változóban kell tárolni.

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
   ```
> [!NOTE]
> Igény szerint kiépítheti az Azure-erőforrások virtuálisgép-méretezési csoport bővítményének felügyelt identitásait úgy, hogy megadja azt a `extensionProfile` sablon elemében. Ez a lépés nem kötelező, mivel az Azure Instance Metadata Service (IMDS) identitás-végpontját is használhatja a tokenek lekéréséhez.  További információ: [Migrálás virtuálisgép-bővítményből az Azure IMDS a hitelesítéshez](howto-migrate-vm-extension.md).

3. Ha elkészült, a sablonnak a következőhöz hasonlóan kell kinéznie:

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2018-06-01**   

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```

   **Microsoft. számítási/virtualMachines API-verzió 2017-12-01**

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
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
                                "properties": {
                                  "publisher": "Microsoft.ManagedIdentity",
                                  "type": "ManagedIdentityExtensionForWindows",
                                  "typeHandlerVersion": "1.0",
                                  "autoUpgradeMinorVersion": true,
                                  "settings": {
                                      "port": 50342
                                  }
                                }
                            }
                        ]
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amelynek már nincs szüksége felhasználóhoz rendelt felügyelt identitásra:

1. Akár helyileg, akár a Azure Portal keresztül jelentkezik be az Azure-ba, használjon egy olyan fiókot, amely a virtuálisgép-méretezési csoportját tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe](#azure-resource-manager-templates) , és keresse meg a keresett `Microsoft.Compute/virtualMachineScaleSets` erőforrást a `resources` szakaszon belül. Ha olyan virtuálisgép-méretezési csoporttal rendelkezik, amely csak a felhasználó által hozzárendelt felügyelt identitással rendelkezik, letilthatja az identitás típusának módosításával `None` .

   Az alábbi példa bemutatja, hogyan távolíthat el minden felhasználó által hozzárendelt felügyelt identitást egy olyan virtuális gépről, amely nem rendelkezik rendszerhez rendelt felügyelt identitásokkal:

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

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2018-06-01**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuálisgép-méretezési csoportból, távolítsa el azt a `userAssignedIdentities` szótárból.

   Ha rendszerhez rendelt identitással rendelkezik, tartsa azt az `type` érték alatt lévő értékben `identity` .

   **Microsoft. számítási/virtualMachineScaleSets API-verzió 2017-12-01**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy virtuálisgép-méretezési csoportból, távolítsa el azt a `identityIds` tömbből.

   Ha rendszerhez rendelt felügyelt identitással rendelkezik, tartsa meg az `type` érték alatti értékben `identity` .

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitások az Azure-erőforrások áttekintéséhez](overview.md).