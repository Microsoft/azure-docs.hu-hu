---
title: Szabályzatok létrehozása programozott módon
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet szabályzatokat az Azure CLI-vel, a Azure PowerShelltal és a REST APIokkal Azure Policy.
ms.date: 03/16/2021
ms.topic: how-to
ms.openlocfilehash: b4f15241799469da2e479cb758f648ec3131a149
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598490"
---
# <a name="programmatically-create-policies"></a>Szabályzatok létrehozása programozott módon

Ez a cikk végigvezeti a szabályzatok programozott létrehozásán és kezelésén. Azure Policy definíciók különböző szabályokat és hatásokat alkalmaznak az erőforrásokra. A kényszerítés biztosítja, hogy az erőforrások megfeleljenek a vállalati szabványoknak és a szolgáltatói szerződéseknek.

A megfelelőséggel kapcsolatos további információkért lásd a [megfelelőségi adatok beszerzése](get-compliance-data.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:

1. Ha még nem tette meg, telepítse az [ARMClient](https://github.com/projectkudu/ARMClient) eszközt. Ez egy olyan segédprogram, amely HTTP-kéréseket küld az Azure Resource Manager-alapú API-khoz.

1. Frissítse Azure PowerShell modulját a legújabb verzióra. Részletes információkért lásd: [Azure PowerShell modul telepítése](/powershell/azure/install-az-ps) . További információ a legújabb verzióról: [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).

1. Regisztrálja a Azure Policy bepillantást erőforrás-szolgáltatót a Azure PowerShell használatával annak ellenőrzéséhez, hogy az előfizetés működik-e az erőforrás-szolgáltatóval. Erőforrás-szolgáltató regisztrálásához engedéllyel kell rendelkeznie az erőforrás-szolgáltató regisztrálása műveletének futtatásához. Ezt a műveletet a Közreműködői és Tulajdonosi szerepkörök magukba foglalják. Az erőforrás-szolgáltató regisztrálásához futtassa az alábbi parancsot:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Az erőforrás-szolgáltatók regisztrálásával és megtekintésével kapcsolatos további információért tekintse meg az [erőforrás-szolgáltatókat és típusaikat](../../../azure-resource-manager/management/resource-providers-and-types.md) ismertető cikket.

1. Ha még nem tette meg, telepítse az Azure CLI-t. A legújabb verziót az [Azure CLI telepítése Windows](/cli/azure/install-azure-cli-windows)rendszeren végezheti el.

## <a name="create-and-assign-a-policy-definition"></a>Szabályzat-definíció létrehozása és társítása

Az erőforrások jobb láthatóságának első lépése, hogy szabályzatokat hozzon létre és rendeljen hozzá az erőforrásokhoz. A következő lépés a szabályzatok programozott létrehozására és hozzárendelésére vonatkozó útmutató. A példa házirend a PowerShell, az Azure CLI és a HTTP-kérések használatával naplózza az összes nyilvános hálózat számára megnyitott tárolási fiókokat.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Házirend-definíció létrehozása és társítása a PowerShell-lel

1. A következő JSON-kódrészlet használatával hozzon létre egy JSON-fájlt, amelynek a neve AuditStorageAccounts.js.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   A szabályzat-definíciók létrehozásával kapcsolatos további információkért lásd: [Azure Policy definíciós struktúra](../concepts/definition-structure.md).

1. A következő parancs futtatásával hozzon létre egy házirend-definíciót a fájl AuditStorageAccounts.jshasználatával.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   A parancs létrehoz egy naplózási fiók nevű házirend-definíciót a _nyilvános hálózatokhoz_.
   További információ a használható egyéb paraméterekről: [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

   Ha a hely paramétereinek megadása nélkül hívja meg őket, az `New-AzPolicyDefinition` alapértelmezett érték a házirend-definíció mentése a munkamenetek környezetének kiválasztott előfizetésében. Ha a definíciót másik helyre szeretné menteni, használja a következő paramétereket:

   - **SubscriptionId** – mentés másik előfizetésbe. _GUID_ -értéket igényel.
   - **ManagementGroupName** – mentés egy felügyeleti csoportba. _Karakterlánc_ -értéket igényel.

1. A házirend-definíció létrehozása után létrehozhat egy szabályzat-hozzárendelést a következő parancsok futtatásával:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Cserélje le a _ContosoRG_ nevet a kívánt erőforráscsoport nevére.

   A **hatókör** -paraméter a `New-AzPolicyAssignment` felügyeleti csoporttal, előfizetéssel, erőforráscsoporthoz vagy egyetlen erőforrással működik. A paraméter teljes erőforrás-elérési utat használ, amely a **ResourceId** tulajdonságot `Get-AzResourceGroup` adja vissza. Az egyes tárolók **hatókörének** mintája a következő. Cserélje le a,, `{rName}` `{rgName}` `{subId}` , és `{mgName}` az erőforrás nevét, az erőforráscsoport nevét, az előfizetés azonosítóját és a felügyeleti csoport nevét.
   `{rType}` lecserélve az erőforrás **erőforrástípus,** például `Microsoft.Compute/virtualMachines` egy virtuális gép esetében.

   - Erőforrás `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Erőforráscsoport – `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Előfizetés `/subscriptions/{subId}`
   - Felügyeleti csoport – `/providers/Microsoft.Management/managementGroups/{mgName}`

Az erőforrás-házirendek Resource Manager PowerShell-modul használatával történő kezelésével kapcsolatos további információkért tekintse meg az [az. Resources](/powershell/module/az.resources/#policy)című témakört.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Szabályzat-definíció létrehozása és társítása a ARMClient használatával

A házirend-definíció létrehozásához kövesse az alábbi eljárást.

1. A következő JSON-kódrészlet másolásával hozzon létre egy JSON-fájlt. A következő lépésben hívnia kell a fájlt.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Hozza létre a házirend-definíciót az alábbi hívások egyikének használatával:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Cserélje le az előző {subscriptionId} azonosítót az előfizetés azonosítójával vagy {managementGroupId} a [felügyeleti csoportjának](../../management-groups/overview.md)azonosítójával.

   További információ a lekérdezés struktúrájáról: [Azure Policy definíciók – létrehozás vagy frissítés](/rest/api/resources/policydefinitions/createorupdate) és [szabályzat-definíciók – létrehozás vagy frissítés a felügyeleti csoportban](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

A következő eljárással hozhat létre szabályzat-hozzárendelést, és hozzárendelheti a házirend-definíciót az erőforráscsoport szintjén.

1. Másolja a következő JSON-kódrészletet egy JSON-szabályzat-hozzárendelési fájl létrehozásához. Cserélje le a példában szereplő információkat a &lt; &gt; saját értékeire.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Hozza létre a szabályzat-hozzárendelést a következő hívás használatával:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Cserélje le a példában szereplő információkat a &lt; &gt; saját értékeire.

   További információ a REST API HTTP-hívásáról: [Azure REST API-erőforrások](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Szabályzat-definíció létrehozása és társítása az Azure CLI-vel

Házirend-definíció létrehozásához kövesse az alábbi eljárást:

1. Másolja a következő JSON-kódrészletet egy JSON-szabályzat-hozzárendelési fájl létrehozásához.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   A szabályzat-definíciók létrehozásával kapcsolatos további információkért lásd: [Azure Policy definíciós struktúra](../concepts/definition-structure.md).

1. A következő parancs futtatásával hozzon létre egy szabályzat-definíciót:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   A parancs létrehoz egy naplózási fiók nevű házirend-definíciót a _nyilvános hálózatokhoz_.
   További információ a használható egyéb paraméterekről: [az Policy definition Create](/cli/azure/policy/definition#az_policy_definition_create).

   Ha a hely paramétereinek megadása nélkül hívja meg őket, az `az policy definition creation` alapértelmezett érték a házirend-definíció mentése a munkamenetek környezetének kiválasztott előfizetésében. Ha a definíciót másik helyre szeretné menteni, használja a következő paramétereket:

   - **előfizetés** – mentse egy másik előfizetésbe. Az előfizetés-AZONOSÍTÓhoz vagy egy _karakterlánc_ -értékhez szükséges _GUID_ -értéket igényel az előfizetés neveként.
   - **felügyelet – csoport** – mentés felügyeleti csoportba. _Karakterlánc_ -értéket igényel.

1. A következő parancs használatával hozzon létre egy szabályzat-hozzárendelést. Cserélje le a példában szereplő információkat a &lt; &gt; saját értékeire.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   A **hatókör** -paraméter a `az policy assignment create` felügyeleti csoporttal, előfizetéssel, erőforráscsoporthoz vagy egyetlen erőforrással működik. A paraméter teljes erőforrás-elérési utat használ. Az egyes tárolók **hatókörének** mintája a következő. Cserélje le a,, `{rName}` `{rgName}` `{subId}` , és `{mgName}` az erőforrás nevét, az erőforráscsoport nevét, az előfizetés azonosítóját és a felügyeleti csoport nevét. `{rType}` lecserélve az erőforrás **erőforrástípus,** például `Microsoft.Compute/virtualMachines` egy virtuális gép esetében.

   - Erőforrás `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Erőforráscsoport – `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Előfizetés `/subscriptions/{subID}`
   - Felügyeleti csoport – `/providers/Microsoft.Management/managementGroups/{mgName}`

A Azure Policy definíciós AZONOSÍTÓját a PowerShell használatával szerezheti be a következő paranccsal:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Az Ön által létrehozott szabályzat-definíció AZONOSÍTÓjának a következő példához hasonlónak kell lennie:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Az erőforrás-házirendek Azure CLI-vel való kezelésével kapcsolatos további információkért lásd: [Azure CLI erőforrás-házirendek](/cli/azure/policy).

## <a name="next-steps"></a>Következő lépések

A cikkben szereplő parancsokkal és lekérdezésekkel kapcsolatos további információkért tekintse át a következő cikkeket.

- [Azure REST API-erőforrások](/rest/api/resources/)
- [Azure PowerShell modulok](/powershell/module/az.resources/#policy)
- [Azure CLI-házirend parancsai](/cli/azure/policy)
- [Azure Policy erőforrás-szolgáltató REST API-referenciája](/rest/api/policy)
- [Erőforrások rendszerezése az Azure-beli felügyeleti csoportokkal](../../management-groups/overview.md).
