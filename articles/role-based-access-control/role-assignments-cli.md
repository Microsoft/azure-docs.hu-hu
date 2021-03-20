---
title: Azure-szerepkörök kiosztása az Azure CLI-vel – Azure RBAC
description: Ismerje meg, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások számára az Azure CLI és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: ee356f32b6799c6182ec1c9e061a35271a4bbc23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100556981"
---
# <a name="assign-azure-roles-using-azure-cli"></a>Azure-szerepkörök kiosztása az Azure CLI-vel

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Ez a cikk azt ismerteti, hogyan rendelhet hozzá szerepköröket az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A szerepkörök hozzárendeléséhez a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)
- [Bash Azure Cloud Shell](../cloud-shell/overview.md) vagy [Azure CLI](/cli/azure) -ben

## <a name="steps-to-assign-an-azure-role"></a>Azure-szerepkörök hozzárendelésének lépései

A szerepkörök hozzárendeléséhez a következő három elemből áll: rendszerbiztonsági tag, szerepkör-definíció és hatókör.

### <a name="step-1-determine-who-needs-access"></a>1. lépés: annak meghatározása, hogy kinek van hozzáférése

Szerepkört hozzárendelhet egy felhasználóhoz, csoporthoz, egyszerű szolgáltatásnévhez vagy felügyelt identitáshoz. A szerepkörök hozzárendeléséhez szükség lehet az objektum egyedi AZONOSÍTÓjának megadására. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111` . Az azonosítót a Azure Portal vagy az Azure CLI használatával szerezheti be.

**Felhasználó**

Azure AD-felhasználó esetén szerezze be az egyszerű felhasználónevet, például a *patlong \@ contoso.com* vagy a felhasználói objektum azonosítóját. Az objektumazonosító beszerzéséhez az [az ad User show](/cli/azure/ad/user#az_ad_user_show)lehetőséget használhatja.

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Csoport**

Azure AD-csoport esetén a csoport objektum-AZONOSÍTÓra van szükség. Az objektumazonosító beszerzéséhez használhatja [az az ad Group show](/cli/azure/ad/group#az_ad_group_show) vagy [az ad Group List](/cli/azure/ad/group#az_ad_group_list)lehetőséget.

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Szolgáltatásnév**

Az Azure AD egyszerű szolgáltatásnév (az alkalmazás által használt identitás) esetében szüksége lesz a szolgáltatásnév objektum AZONOSÍTÓra. Az objektumazonosító beszerzéséhez használja az [az ad SP listát](/cli/azure/ad/sp#az_ad_sp_list). Egyszerű szolgáltatásnév esetén használja az objektumazonosító azonosítót, **ne** pedig az alkalmazás azonosítóját.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Kezelt identitás**

A rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitáshoz szüksége lesz az objektum AZONOSÍTÓJÁRA. Az objektumazonosító beszerzéséhez használja az [az ad SP listát](/cli/azure/ad/sp#az_ad_sp_list).

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Ha csak a felhasználó által hozzárendelt felügyelt identitásokat szeretné listázni, használhatja [az az Identity List](/cli/azure/identity#az_identity_list)lehetőséget.

```azurecli
az identity list
```
    
### <a name="step-2-select-the-appropriate-role"></a>2. lépés: válassza ki a megfelelő szerepkört

Az engedélyek szerepkörökbe vannak csoportosítva. Több [Azure beépített szerepkörből](built-in-roles.md) is választhat, vagy használhatja saját egyéni szerepköreit is. Az ajánlott eljárás az, hogy hozzáférést biztosítson a legkevésbé szükséges jogosultsággal, ezért ne rendeljen hozzá szélesebb körű szerepkört.

A szerepkörök listázásához és az egyedi szerepkör-azonosító lekéréséhez használja az [az role definition List](/cli/azure/role/definition#az_role_definition_list)lehetőséget.

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

A következőképpen listázhatja egy adott szerepkör részleteit.

```azurecli
az role definition list --name "{roleName}"
```

További információ: az [Azure szerepkör-definíciók listázása](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>3. lépés: a szükséges hatókör azonosítása

Az Azure a hatókör négy szintjét biztosítja: erőforrás, [erőforráscsoport](../azure-resource-manager/management/overview.md#resource-groups), előfizetés és [felügyeleti csoport](../governance/management-groups/overview.md). Az ajánlott eljárás az, hogy hozzáférést biztosítson a legkevésbé szükséges jogosultsággal, ezért ne rendeljen hozzá egy szerepkört szélesebb körben. A hatókörrel kapcsolatos további információkért lásd a [hatókör ismertetése](scope-overview.md)című témakört.

**Erőforrás hatóköre**

Erőforrás-hatókör esetén szüksége lesz az erőforrás erőforrás-AZONOSÍTÓJÁRA. Az erőforrás-azonosítót a Azure Portalban található erőforrás tulajdonságainak megtekintésével tekintheti meg. Az erőforrás-azonosító formátuma a következő:

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Erőforráscsoport hatóköre**

Az erőforráscsoport hatóköréhez szüksége lesz az erőforráscsoport nevére. A nevet a Azure Portal **erőforráscsoportok** lapján találja, vagy használhatja az [az Group List](/cli/azure/group#az_group_list)paranccsal.

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Előfizetés hatóköre** 

Az előfizetés hatóköréhez szüksége lesz az előfizetés-AZONOSÍTÓra. Az azonosítót a Azure Portal **előfizetések** lapján találja, vagy használhatja az [az Account List](/cli/azure/account#az_account_list)paranccsal.

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Felügyeleti csoport hatóköre** 

A felügyeleti csoport hatóköréhez szükség van a felügyeleti csoport nevére. A nevet a **felügyeleti csoportok** lapon találja a Azure Portal, vagy használhatja az [az Account Management-Group listát](/cli/azure/account/management-group#az_account_management_group_list).

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-assign-role"></a>4. lépés: szerepkör kiosztása

Szerepkör hozzárendeléséhez használja az az [szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create) parancsot. A hatókörtől függően a parancs általában a következő formátumok valamelyikével rendelkezik.

**Erőforrás hatóköre**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Erőforráscsoport hatóköre**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Előfizetés hatóköre** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Felügyeleti csoport hatóköre** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Az alábbi példa bemutatja a kimenetet, amikor hozzárendeli a [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört egy erőforráscsoport-hatókörben lévő felhasználóhoz.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="assign-role-examples"></a>Szerepkör-hozzárendelési példák

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Szerepkör kiosztása a Storage-fiók erőforrás-hatókörében lévő összes blob-tárolóhoz

Hozzárendeli a [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy, a *storage12345* nevű Storage-fiók erőforrás-HATÓKÖRében a *55555555-5555-5555-5555-555555555555* azonosítójú azonosítójú szolgáltatáshoz.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Szerepkör társítása egy adott blob-tároló erőforrás-hatóköréhez

Hozzárendeli a [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy olyan szolgáltatáshoz, amelynek a *55555555-5555-5555-5555-555555555555* -es azonosítójú objektuma egy *blob-Container-01* nevű blob-tároló erőforrás-hatókörében van.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Szerepkör társítása egy adott virtuális hálózati erőforrás-hatókörben lévő csoporthoz

Hozzárendeli a [virtuálisgép-közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört az *Ann Mack Team* csoporthoz a 22222222-2222-2222-2222-222222222222 azonosítóval, amely egy *Pharma-Sales-Project-Network* nevű virtuális hálózat erőforrás-hatóköre.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Szerepkör társítása egy erőforráscsoport-hatókörben lévő felhasználóhoz

Hozzárendeli a [virtuális gépi közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört a *patlong \@ contoso.com* -felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Szerepkör társítása egy adott felhasználóhoz egy erőforráscsoport-hatókörben lévő egyedi szerepkör-azonosító használatával

Néhány alkalommal, amikor a szerepkör neve változhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy megváltoztatja a nevet.
- Olyan előzetes verziójú szerepkört használ, amely **(előzetes verzió)** szerepel a névben. A szerepkör megjelenése után a rendszer átnevezi a szerepkört.

A szerepkör-azonosító nem változik, még akkor is, ha a szerepkör át lett nevezve. Ha parancsfájlokat vagy automationt használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok nagyobb valószínűséggel fognak működni.

A következő példa a [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört rendeli hozzá a *patlong \@ contoso.com* felhasználóhoz a *Pharma-Sales erőforráscsoport-* hatókörben.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-resource-group-scope"></a>Szerepkör társítása az összes blob-tárolóhoz egy erőforráscsoport-hatókörben

Hozzárendeli a [Storage blob-adatközreműködői](built-in-roles.md#storage-blob-data-contributor) szerepkört egy, a *55555555-5555-5555-5555-555555555555* azonosítójú azonosítójú szolgáltatáshoz a következő *példában: Storage-RG* erőforráscsoport-hatókör.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Másik lehetőségként megadhatja a teljesen minősített erőforráscsoportot a (z) `--scope` paraméterrel:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Szerepkör társítása egy adott alkalmazáshoz egy erőforráscsoport-hatókörben

Hozzárendeli a [virtuálisgép-közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört egy, az 44444444-4444-4444-4444-444444444444-es azonosítójú alkalmazáshoz a *Pharma-Sales* erőforráscsoport hatókörében.

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-new-service-principal-at-a-resource-group-scope"></a>Szerepkör társítása egy új egyszerű szolgáltatásnév számára egy erőforráscsoport-hatókörben

Ha létrehoz egy új szolgáltatásnevet, és azonnal megpróbál hozzárendelni egy szerepkört az egyszerű szolgáltatáshoz, a szerepkör-hozzárendelés bizonyos esetekben sikertelen lehet. Ha például egy parancsfájl használatával új felügyelt identitást hoz létre, majd megpróbál hozzárendelni egy szerepkört az adott egyszerű szolgáltatáshoz, akkor a szerepkör-hozzárendelés sikertelen lehet. A hiba oka valószínűleg a replikálás késése. Az egyszerű szolgáltatás egy régióban jön létre; a szerepkör-hozzárendelés azonban egy másik régióban is előfordulhat, amely még nem replikálta a szolgáltatásnevet. Ennek a forgatókönyvnek a megoldásához a szerepkör-hozzárendelés létrehozásakor meg kell adnia a rendszerbiztonsági tag típusát.

Szerepkör hozzárendeléséhez használja az [az szerepkör-hozzárendelési létrehozás](/cli/azure/role/assignment#az_role_assignment_create)lehetőséget, adja meg a értékét, majd állítsa a következőre: `--assignee-object-id` `--assignee-principal-type` `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

A következő példa a [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört rendeli hozzá a *Pharma-Sales* erőforráscsoport hatókörének *MSI-test* felügyelt identitásához:

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Szerepkör kiosztása egy felhasználóhoz előfizetési hatókörben

Hozzárendeli az [olvasó](built-in-roles.md#reader) szerepkört a *annm \@ example.com* -felhasználóhoz egy előfizetési hatókörben.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-group-at-a-subscription-scope"></a>Szerepkör társítása egy csoporthoz előfizetési hatókörben

Hozzárendeli az [olvasó](built-in-roles.md#reader) szerepkört az *Ann Mack Team* csoporthoz, amelynek azonosítója 22222222-2222-2222-2222-222222222222, előfizetési hatókörben.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-subscription-scope"></a>Szerepkör társítása az összes blob-tárolóhoz az előfizetés hatókörében

Hozzárendeli a [Storage blob Adatolvasói](built-in-roles.md#storage-blob-data-reader) szerepkört az *Alain \@ example.com* -felhasználóhoz egy előfizetési hatókörben.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Szerepkör társítása egy felügyeleti csoport hatókörében lévő felhasználóhoz

A [Számlázási olvasó](built-in-roles.md#billing-reader) szerepkört a felügyeleti csoport hatókörében lévő *Alain \@ example.com* -felhasználóhoz rendeli.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések listázása az Azure CLI használatával](role-assignments-list-cli.md)
- [Azure-erőforrások és-erőforráscsoportok kezelése az Azure CLI használatával](../azure-resource-manager/management/manage-resources-cli.md)
