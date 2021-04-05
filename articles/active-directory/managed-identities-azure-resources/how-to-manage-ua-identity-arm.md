---
title: Felhasználó által hozzárendelt felügyelt identitás létrehozása & törlése Azure Resource Manager használatával
description: Részletes útmutató a felhasználó által hozzárendelt felügyelt identitások létrehozásához és törléséhez Azure Resource Manager használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: acbeb8e0fc638955995cc5aaf00f58d40adc69a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98184876"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása és törlése Azure Resource Manager használatával


Az Azure-erőforrások felügyelt identitásai biztosítják az Azure-szolgáltatásokat Azure Active Directory felügyelt identitással. Ezt az identitást hitelesítheti az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy hitelesítő adatokat kellene használnia a kódban. 

Ebben a cikkben egy felhasználó által hozzárendelt felügyelt identitást hoz létre egy Azure Resource Manager használatával.

Felhasználó által hozzárendelt felügyelt identitást nem lehet listázni és törölni Azure Resource Manager sablon használatával.  A felhasználó által hozzárendelt felügyelt identitások létrehozásához és listázásához tekintse meg a következő cikkeket:

- [Felhasználó által hozzárendelt felügyelt identitások listázása](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Felhasználó által hozzárendelt felügyelt identitás törlése](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="template-creation-and-editing"></a>Sablonok létrehozása és szerkesztése

A Azure Portal és a parancsfájlok futtatásához hasonlóan Azure Resource Manager-sablonok lehetővé teszik az Azure-erőforráscsoport által definiált új vagy módosított erőforrások telepítését. A sablonok szerkesztéséhez és üzembe helyezéséhez több lehetőség is rendelkezésre áll, a helyi és a portálon is, beleértve a következőket:

- [Egyéni sablon használata az Azure piactéren](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy teljesen új sablont hozzon létre, vagy egy meglévő gyakori vagy gyors üzembe helyezési [sablonon](https://azure.microsoft.com/documentation/templates/)alapuljon.
- Egy meglévő erőforráscsoporthoz származtatva, az [eredeti telepítésből](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)vagy az üzemelő példány [aktuális állapotától](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)származó sablon exportálásával.
- Helyi JSON- [szerkesztő (például vs Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)használata, majd a PowerShell vagy a parancssori felület használatával történő feltöltés és üzembe helyezés.
- A Visual Studio [Azure erőforráscsoport-projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használatával hozzon létre és helyezzen üzembe egy sablont. 

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak rendelkeznie kell a [felügyelt identitás közreműködői](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) szerepkör-hozzárendelésével.

Felhasználó által hozzárendelt felügyelt identitás létrehozásához használja a következő sablont. Cserélje le az `<USER ASSIGNED IDENTITY NAME>` értéket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Következő lépések

A felhasználó által hozzárendelt felügyelt identitások Azure-beli virtuális gépekhez Azure Resource Manager sablon használatával történő hozzárendelésével kapcsolatos információkért lásd: [felügyelt identitások konfigurálása](qs-configure-template-windows-vm.md)Azure-beli virtuális gépeken sablonok használatával.


