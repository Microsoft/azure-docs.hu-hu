---
title: Sérült Azure-beli automanage-fiók javítása
description: Ha nemrég olyan előfizetést helyezett át, amely egy automatikusan felügyelt fiókot tartalmaz egy új bérlőhöz, újra kell konfigurálnia. Ebből a cikkből megtudhatja, hogyan.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.openlocfilehash: 4694fa679c7bbff309a0452219ff39bacf2488c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96183702"
---
# <a name="repair-an-automanage-account"></a>Automanage-fiók javítása
Az [Azure automatikus felügyelet fiókja](./automanage-virtual-machines.md#automanage-account) az a biztonsági környezet vagy identitás, amely alatt az automatizált műveletek történnek. Ha nemrég olyan előfizetést helyezett át, amely egy automatikusan felügyelt fiókot tartalmaz egy új bérlőhöz, újra kell konfigurálnia a fiókot. Az újrakonfiguráláshoz alaphelyzetbe kell állítania az identitás típusát, és hozzá kell rendelnie a fiókhoz a megfelelő szerepköröket.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>1. lépés: az automanage Account Identity típusának alaphelyzetbe állítása
Az alábbi Azure Resource Manager (ARM) sablon használatával állítsa alaphelyzetbe az automanage Account Identity típust. Mentse a fájlt helyileg armdeploy.jsvagy hasonló néven. Figyelje meg, hogy a fiók neve és helye nem az ARM-sablonban szükséges paraméterek.

1. Hozzon létre egy Resource Manager-telepítést a következő sablonnal. Használja az `identityType = None` parancsot.
    * A telepítés az Azure CLI-ben a használatával hozható létre `az deployment sub create` . További információ: [az Deployment sub](/cli/azure/deployment/sub).
    * A telepítést a PowerShellben hozhatja létre a `New-AzDeployment` modul használatával. További információ: [New-AzDeployment](/powershell/module/az.resources/new-azdeployment).

1. Futtassa újra ugyanezt az ARM-sablont a rel `identityType = SystemAssigned` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>2. lépés: a megfelelő szerepkörök kiosztása az automanage-fiókhoz
Az automanage fiókhoz a közreműködő és az erőforrás-házirend közreműködő szerepköre szükséges azon virtuális gépeket tartalmazó előfizetésben, amelyek a felügyelet alatt állnak. Ezeket a szerepköröket a Azure Portal, ARM-sablonok vagy az Azure CLI használatával rendelheti hozzá.

Ha ARM-sablont vagy az Azure CLI-t használja, szüksége lesz az automanage-fiókjának résztvevő-AZONOSÍTÓra (más néven objektumazonosító). (Ha a Azure Portal használja, nincs szükség az AZONOSÍTÓra.) Ezt az azonosítót a következő módszerekkel érheti el:

- [Azure CLI](/cli/azure/ad/sp): használja az parancsot `az ad sp list --display-name <name of your Automanage Account>` .

- Azure Portal: Nyissa meg a **Azure Active Directory** , és keresse meg a fiókját név szerint. A **vállalati alkalmazások** területen válassza ki a fiók autokezelése nevet, ha megjelenik.

### <a name="azure-portal"></a>Azure Portal
1. Az **előfizetések** alatt keresse meg az előfizetést, amely az Ön által felügyelt virtuális gépeket tartalmazza.
1. Nyissa meg a **hozzáférés-vezérlés (iam)** lehetőséget.
1. Válassza a **szerepkör-hozzárendelések hozzáadása** lehetőséget.
1. Válassza ki a **közreműködő** szerepkört, és adja meg az automanage-fiók nevét.
1. Kattintson a **Mentés** gombra.
1. Ismételje meg a 3 – 5. lépést, ezúttal az **erőforrás-házirend közreműködői** szerepkörével.

### <a name="arm-template"></a>ARM-sablon
Futtassa a következő ARM-sablont. Szüksége lesz az automanage-fiókjának elsődleges AZONOSÍTÓJÁRA. A beszerzésének lépései a szakasz elején találhatók. Ha a rendszer kéri, adja meg az azonosítót.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
Futtassa a következő parancsokat:

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>Következő lépések
[További információ az Azure automanage szolgáltatásról](./automanage-virtual-machines.md)