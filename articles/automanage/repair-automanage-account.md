---
title: Hibás fiók Azure Automanage javítása
description: Ha a közelmúltban áthelyezett egy automatikus rendszergazdai fiókot tartalmazó előfizetést egy új bérlőbe, újra kell konfigurálnia azt. Ebből a cikkből megtudhatja, hogyan.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: alsin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e6bf5404a33e0b4e57c2ff8d82d8791eda3d0f06
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834191"
---
# <a name="repair-an-automanage-account"></a>Automatikus fiókművelet javítása
Az [Azure Automanage-fiók](./automanage-virtual-machines.md#automanage-account) az a biztonsági környezet vagy identitás, amelyben az automatizált műveletek le vannak végezve. Ha a közelmúltban áthelyezett egy automatikus rendszergazdai fiókot tartalmazó előfizetést egy új bérlőbe, újra kell konfigurálnia a fiókot. Az újrakonfiguráláshoz alaphelyzetbe kell állítania az identitás típusát, és hozzá kell rendelnie a megfelelő szerepköröket a fiókhoz.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>1. lépés: A fiókidentitás típusának alaphelyzetbe állítása
Állítsa alaphelyzetbe a fiók identitástípusát az alábbi Azure Resource Manager (ARM) sablon használatával. Mentse helyileg a fájlt armdeploy.jsnéven vagy hasonló néven. Jegyezze fel a fiók nevét és helyét, mert ezek kötelező paraméterek az ARM-sablonban.

1. Hozzon létre Resource Manager üzembe helyezést az alábbi sablonnal. Használja az `identityType = None` parancsot.
    * Az üzemelő példány az Azure CLI-ban a használatával hozható `az deployment sub create` létre. További információ: [az deployment sub.](/cli/azure/deployment/sub)
    * Az üzembe helyezést a PowerShellben a modul használatával `New-AzDeployment` hozhatja létre. További információ: [New-AzDeployment.](/powershell/module/az.resources/new-azdeployment)

1. Futtassa újra ugyanazt az ARM-sablont a `identityType = SystemAssigned` következővel: .

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

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>2. lépés: A megfelelő szerepkörök hozzárendelése az automatikus fiókhoz
Az automatikus felügyelethez a Közreműködő és az Erőforrás-szabályzat közreműködője szerepkör szükséges az automatikus kezelés alatt áll virtuális gépeket tartalmazó előfizetésben. Ezeket a szerepköröket hozzárendelheti a Azure Portal, ARM-sablonok vagy az Azure CLI használatával.

Ha ARM-sablont vagy az Azure CLI-t használja, szüksége lesz az Automanage-fiók egyszerű azonosítójára (más néven objektumazonosítójára). (Az azonosítóra nincs szükség, ha a Azure Portal.) Ezt az azonosítót az alábbi módszerekkel találhatja meg:

- [Azure CLI:](/cli/azure/ad/sp)Használja a `az ad sp list --display-name <name of your Automanage Account>` parancsot.

- Azure Portal: Keresse **Azure Active Directory,** és keresse meg az automatikus fióknevet. A **Vállalati alkalmazások alatt** válassza ki a fiók nevének automatikus felügyeletét, amikor megjelenik.

### <a name="azure-portal"></a>Azure Portal
1. Az **Előfizetések alatt** menjen az automatikusan felügyelet alatt található virtuális gépeket tartalmazó előfizetéshez.
1. Ugrás a **Hozzáférés-vezérlés (IAM) pontra.**
1. Válassza **a Szerepkör-hozzárendelések hozzáadása lehetőséget.**
1. Válassza ki **a Közreműködő szerepkört,** és adja meg az automatikus fiók nevét.
1. Kattintson a **Mentés** gombra.
1. Ismételje meg a 3–5. lépést, ezúttal az Erőforrás-szabályzat **közreműködője szerepkört.**

### <a name="arm-template"></a>ARM-sablon
Futtassa az alábbi ARM-sablont. Szüksége lesz az automanage-fiók egyszerű azonosítójára. A lekért lépések ennek a szakasznak a elején vannak. Amikor a rendszer kéri, adja meg az azonosítót.

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
[További információ a Azure Automanage](./automanage-virtual-machines.md)
