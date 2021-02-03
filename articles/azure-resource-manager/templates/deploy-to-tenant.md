---
title: Erőforrások üzembe helyezése a bérlőn
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a bérlői hatókörben egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: fd5a9ae60c578a3be7f70d82baae0a15e406b9db
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491486"
---
# <a name="tenant-deployments-with-arm-templates"></a>Bérlői üzemelő példányok ARM-sablonokkal

A szervezete leállása esetén előfordulhat, hogy az Azure AD-bérlőn belül meg kell adnia és hozzá kell rendelnie a [szabályzatokat](../../governance/policy/overview.md) vagy az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) . A bérlői szintű sablonok használatával a szabályzatok deklaratív alkalmazása és a szerepkörök globális szinten való hozzárendelését végezheti el.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípust lehet központilag telepíteni a bérlői szintre. Ez a szakasz felsorolja, hogy milyen típusú erőforrástípusok támogatottak.

Azure-szabályzatok esetén használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata esetén használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

A felügyeleti csoportokra, előfizetésekre vagy erőforráscsoportokre telepítendő beágyazott sablonok esetében használja a következőt:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)

Felügyeleti csoportok létrehozásához használja a következőt:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Az előfizetések létrehozásához használja a következőt:

* [aliasok](/azure/templates/microsoft.subscription/aliases)

A költségek kezeléséhez használja a következőt:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [utasításokat](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

A portál konfigurálásához használja a következőt:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

## <a name="schema"></a>Séma

A bérlői központi telepítésekhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Szükséges hozzáférés

A sablon központi telepítésének jogosultsággal kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre a bérlői hatókörben. A rendszerbiztonsági tag számára engedéllyel kell rendelkeznie a telepítési műveletek végrehajtásához `Microsoft.Resources/deployments/*` , valamint a sablonban definiált erőforrások létrehozásához. Egy felügyeleti csoport létrehozásához például a résztvevőnek közreműködői engedéllyel kell rendelkeznie a bérlői hatókörben. A szerepkör-hozzárendelések létrehozásához a rendszerbiztonsági tag tulajdonosi engedéllyel kell rendelkeznie.

A Azure Active Directory globális rendszergazdája nem rendelkezik automatikusan jogosultsággal a szerepkörök hozzárendeléséhez. Ha engedélyezni szeretné a sablonok központi telepítését a bérlői hatókörben, a globális rendszergazdának a következő lépéseket kell elvégeznie:

1. Fiók-hozzáférés emelése, hogy a globális rendszergazda szerepköröket rendeljen hozzá. További információ: jogosultságszint- [emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md).

1. Rendeljen tulajdonost vagy közreműködőt a sablonok üzembe helyezéséhez szükséges rendszerbiztonsági tag számára.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

A rendszerbiztonsági tag most már rendelkezik a sablon üzembe helyezéséhez szükséges engedélyekkel.

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

A bérlők központi telepítésére vonatkozó parancsok eltérnek az erőforráscsoport-telepítések parancsaitól.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetén használja az [az Deployment bérlő Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell esetén használja a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

További információt az üzembe helyezési parancsokról és az ARM-sablonok üzembe helyezési lehetőségeiről a következő témakörben talál:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Sablonok üzembe helyezése a GitHub-tárházból a központi telepítés gomb használatával](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Bérlői szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat. Az [előfizetések](deploy-to-subscription.md) és a [felügyeleti csoportok](deploy-to-management-group.md) központi telepítései helyhez is szükségesek. Az [erőforráscsoport](deploy-to-resource-group.md) -telepítések esetében az erőforráscsoport helye a központi telepítési adattárolási szolgáltatás tárolására szolgál.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy _azuredeploy.js_ nevű sablon üzembe helyezése például a **azuredeploy** alapértelmezett központi telepítési nevét hozza létre.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha például létrehoz egy **deployment1** nevű bérlői telepítést a **CentralUS**-ben, akkor a későbbiekben nem hozhat létre újabb telepítést a **deployment1** névvel, de a **westus** helyét. Ha a hibakódot kapja `InvalidDeploymentLocation` , használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Bérlőre való központi telepítés esetén a következő erőforrások helyezhetők üzembe:

* a bérlő
* a bérlőn belüli felügyeleti csoportok
* előfizetések
* erőforráscsoportok

A [kiterjesztési erőforrás](scope-extension-resources.md) hatóköre olyan cél lehet, amely eltér a telepítési céltól.

A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Ez a szakasz bemutatja, hogyan határozhat meg különböző hatóköröket. Ezeket a különböző hatóköröket egyetlen sablonban kombinálhatja.

### <a name="scope-to-tenant"></a>Hatókör a bérlőre

A sablon erőforrások szakaszában meghatározott erőforrások a bérlőre lesznek alkalmazva.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Hatókör a felügyeleti csoportnak

Egy felügyeleti csoportnak a bérlőn belüli célzásához adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,18,22":::

### <a name="scope-to-subscription"></a>Hatókör az előfizetéshez

Az előfizetéseket a bérlőn belül is megcélozhatja. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

A bérlőn belüli előfizetés célzásához használjon egy beágyazott telepítést és a `subscriptionId` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="9,10,18":::

### <a name="scope-to-resource-group"></a>Hatókör az erőforráscsoporthoz

Az erőforráscsoportok a bérlőn belül is megadhatók. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Egy erőforráscsoport a bérlőn belüli megcélzásához használjon egy beágyazott telepítést. Adja meg a `subscriptionId` és a `resourceGroup` tulajdonságokat. Ne állítson be helyet a beágyazott központi telepítéshez, mert az az erőforráscsoport helyén van üzembe helyezve.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-rg.json" highlight="9,10,18":::

## <a name="create-management-group"></a>Felügyeleti csoport létrehozása

A következő sablon létrehoz egy felügyeleti csoportot.

:::code language="json" source="~/quickstart-templates/tenant-deployments/new-mg/azuredeploy.json":::

Ha a fiókja nem rendelkezik engedéllyel a bérlőn való üzembe helyezéshez, a felügyeleti csoportokat továbbra is létrehozhatja egy másik hatókörbe való telepítéssel. További információ: [felügyeleti csoport](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Szerepkör hozzárendelése

A következő sablon egy szerepkört rendel a bérlői hatókörhöz.

:::code language="json" source="~/quickstart-templates/tenant-deployments/tenant-role-assignment/azuredeploy.json":::

## <a name="next-steps"></a>Következő lépések

* A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) vagy [felügyeleti csoport szintjén](deploy-to-management-group.md)is üzembe helyezheti.
