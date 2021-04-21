---
title: Erőforrások üzembe helyezése a bérlőben
description: Ismerteti, hogyan helyezhet üzembe erőforrásokat a bérlői hatókörben egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 0b17b8741d1701720de86d8039be3b6cd28ace5c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781900"
---
# <a name="tenant-deployments-with-arm-templates"></a>Bérlői üzembe helyezések ARM-sablonokkal

Ahogy a szervezet kiforrott, előfordulhat, hogy szabályzatokat vagy [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) kell meghatároznia és hozzárendelni az Azure AD-bérlőben. [](../../governance/policy/overview.md) A bérlői szintű sablonokkal deklaratív módon alkalmazhat szabályzatokat, és globális szinten rendelhet szerepköröket.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípus helyezhető üzembe a bérlői szinten. Ez a szakasz a támogatott erőforrástípusokat sorolja fel.

Azure-szabályzatok:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions (szabályzatdefiníciók)](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Az Azure szerepköralapú hozzáférés-vezérléshez (Azure RBAC) használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

A felügyeleti csoportokba, előfizetésekbe vagy erőforráscsoportokba üzembe helyezett beágyazott sablonokhoz használja a következőt:

* [Telepítések](/azure/templates/microsoft.resources/deployments)

Felügyeleti csoportok létrehozásához használja a következőt:

* [managementGroups (felügyeleti csoportok)](/azure/templates/microsoft.management/managementgroups)

Előfizetések létrehozásához használja a következőt:

* [Aliasok](/azure/templates/microsoft.subscription/aliases)

A költségek kezeléséhez használja a következőt:

* [billingProfiles (számlázási profilok)](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [Utasításokat](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections (számlaszakaszok)](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

A portál konfigurálásához használja a következőt:

* [tenantConfigurations (bérlő konfigurációi)](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Séma

A bérlők üzembe helyezéséhez használt séma eltér az erőforráscsoportok üzembe helyezéséhez használt sémától.

Sablonokhoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

A paraméterfájl sémája minden üzembe helyezési hatókörben ugyanaz. Paraméterfájlokhoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Szükséges hozzáférés

A sablont üzembe helyező rendszerbiztonsági tagnak engedélyekkel kell rendelkeznie az erőforrások bérlői hatókörben való létrehozásához. A rendszerbiztonsági tagnak engedéllyel kell rendelkeznie a telepítési műveletek () végrehajtásához és a sablonban meghatározott erőforrások `Microsoft.Resources/deployments/*` létrehozásához. Felügyeleti csoport létrehozásához például a rendszerbiztonsági tagnak Közreműködő engedéllyel kell rendelkeznie a bérlői hatókörben. Szerepkör-hozzárendelések létrehozásához a rendszerbiztonsági tagnak Tulajdonos engedéllyel kell rendelkeznie.

A globális rendszergazda nem Azure Active Directory automatikusan jogosultságot a szerepkörök hozzárendelésére. A sablontelepítések bérlői hatókörben való engedélyezéséhez a globális rendszergazdának a következő lépéseket kell tennie:

1. Jogosultságszint-kiosztás a fiókhoz, hogy a globális rendszergazda szerepköröket rendelhet hozzá. További információkért lásd: Az összes Azure-előfizetés és felügyeleti csoport felügyeletéhez szükséges hozzáférési [jogosultságszint megemelve.](../../role-based-access-control/elevate-access-global-admin.md)

1. Rendeljen tulajdonost vagy közreműködőt a sablonokat üzembe helyező rendszerbiztonsági taghoz.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

A rendszerbiztonsági tag most már rendelkezik a sablon üzembe helyezéséhez szükséges engedélyekkel.

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

A bérlői üzemelő példányok parancsai eltérnek az erőforráscsoportok üzembe helyezésének parancsaitól.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI-hez használja [az az deployment tenant create szolgáltatásokat:](/cli/azure/deployment/tenant#az_deployment_tenant_create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Azure PowerShell használja a [New-AzTenantDeployment et.](/powershell/module/az.resources/new-aztenantdeployment)

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

Az üzembe helyezési parancsokkal és az ARM-sablonok üzembe helyezésének beállításaival kapcsolatos további információkért lásd:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és az Azure CLI-val](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Üzembe helyezési gomb használata sablonok GitHub-adattárból való üzembe helyezéséhez](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Üzembe helyezés helye és neve

Bérlői szintű üzemelő példányok esetén meg kell adnia az üzemelő példány helyét. Az üzembe helyezés helye elkülönül az üzembe helyezett erőforrások helyétől. Az üzembe helyezési hely határozza meg, hogy hol tárolja az üzembe helyezési adatokat. [Az](deploy-to-subscription.md) [előfizetések és felügyeleti csoportok üzembe](deploy-to-management-group.md) helyezéséhez hely is szükséges. Az [erőforráscsoportok üzemelő](deploy-to-resource-group.md) példányai esetén az erőforráscsoport helye az üzembe helyezési adatok tárolására használható.

Meg kell adnia az üzemelő példány nevét, vagy használhatja az alapértelmezett üzembe helyezési nevet. Az alapértelmezett név a sablonfájl neve. Ha például üzembe helyez egy _azuredeploy.jsnevű sablont,_ létrejön egy **alapértelmezett azuredeploy üzembe helyezési név.**

A hely minden üzembe helyezési név esetén nem módosítható. Nem hozhat létre üzemelő példányokat egy helyen, ha már létezik azonos nevű üzemelő példány egy másik helyen. Ha például a **centralusban** létrehoz egy **deployment1** nevű bérlőtelepítést, később nem hozhat létre másik üzemelő példányot **deployment1** néven, de a westus nevű **helyet.** Ha a hibakód jelenik meg, vagy használjon egy másik nevet, vagy ugyanazt a helyet, mint az előző üzemelő `InvalidDeploymentLocation` példány a névhez.

## <a name="deployment-scopes"></a>Üzembe helyezési hatókörök

Bérlőben való üzembe helyezéskor a következő erőforrásokat helyezheti üzembe:

* a bérlő
* felügyeleti csoportok a bérlőn belül
* Előfizetések
* erőforráscsoportok

A [bővítményerőforrás](scope-extension-resources.md) hatóköre az üzembe helyezési céltól eltérő célra is kiterjedhet.

A sablont üzembe helyező felhasználónak hozzáféréssel kell lennie a megadott hatókörben.

Ez a szakasz a különböző hatókörök megadását mutatja be. Ezeket a különböző hatókörök egyetlen sablonban kombinálhatóak.

### <a name="scope-to-tenant"></a>Hatókör bérlőre

A sablon resources szakaszában meghatározott erőforrások a bérlőre vannak alkalmazva.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Hatókör felügyeleti csoportra

Ha egy felügyeleti csoportot szeretne megcélni a bérlőn belül, adjon hozzá egy beágyazott üzemelő példányt, és adja meg a `scope` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Előfizetés hatóköre

A bérlőn belül előfizetéseket is megcélhat. A sablont üzembe helyező felhasználónak hozzáféréssel kell lennie a megadott hatókörben.

Ha előfizetést céloz meg a bérlőn belül, használjon beágyazott üzemelő példányokat és a `subscriptionId` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Hatókör erőforráscsoporthoz

A bérlőn belül erőforráscsoportokat is megcélhat. A sablont üzembe helyező felhasználónak hozzáféréssel kell lennie a megadott hatókörben.

Ha egy erőforráscsoportot a bérlőn belül céloz meg, használjon beágyazott üzemelő példányokat. Állítsa be a `subscriptionId` és a `resourceGroup` tulajdonságot. Ne állítsa be a beágyazott üzemelő példány helyét, mert az az erőforráscsoport helyén van üzembe állítva.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Felügyeleti csoport létrehozása

Az alábbi sablon egy felügyeleti csoportot hoz létre.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Ha a fiókja nem rendelkezik a bérlőn való üzembe helyezéshez szükséges engedéllyel, akkor is létrehozhat felügyeleti csoportokat egy másik hatókörben való üzembe helyezésével. További információ: [Felügyeleti csoport.](deploy-to-management-group.md#management-group)

## <a name="assign-role"></a>Szerepkör hozzárendelése

Az alábbi sablon egy szerepkört rendel hozzá a bérlői hatókörben.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

* További információ a szerepkörök hozzárendelésről: [Azure-beli szerepkör-hozzárendelések hozzáadása Azure Resource Manager sablonokkal.](../../role-based-access-control/role-assignments-template.md)
* A sablonokat az előfizetés [vagy](deploy-to-subscription.md) a felügyeleti csoport szintjén is [üzembe helyezheti.](deploy-to-management-group.md)
