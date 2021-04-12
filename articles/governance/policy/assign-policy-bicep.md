---
title: 'Gyors útmutató: új szabályzat-hozzárendelés a bicep (előzetes verzió) fájlból'
description: Ebben a rövid útmutatóban egy bicep (előzetes verzió) fájlt használ a szabályzat-hozzárendelés létrehozásához a nem megfelelő erőforrások azonosításához.
ms.date: 04/01/2021
ms.topic: quickstart
ms.custom: subject-bicepqs
ms.openlocfilehash: 17460f9a06d7225d50933608645ea8aea2f5e8f6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223982"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-bicep-file"></a>Gyors útmutató: szabályzat-hozzárendelés létrehozása a nem megfelelő erőforrások azonosításához a bicep-fájl használatával

Az Azure-ral való megfelelőség megértéséhez szükséges első lépés a saját erőforrásai állapotának megállapítása.
Ez a rövid útmutató végigvezeti egy Azure Resource Manager sablonhoz (ARM-sablonhoz) fordított [bicep (előzetes verziójú)](https://github.com/Azure/bicep) fájl használatának folyamatán, amely egy szabályzat-hozzárendelést hoz létre a felügyelt lemezeket nem használó virtuális gépek azonosításához. A folyamat végén sikeresen fogja azonosítani a felügyelt lemezeket nem használó virtuális gépeket. Ezek a szabályzat-hozzárendelés szempontjából _nem megfelelőnek_ minősülnek.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon megnyílik a Azure Portalban.

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Gomb az ARM-sablon üzembe helyezéséhez Azure Policy az Azure-hoz való hozzárendeléséhez." border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>Előfeltételek

- Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.
- A bicep `0.3` vagy újabb verzió van telepítve. Ha még nem rendelkezik a bicep CLI-vel, vagy frissítenie kell, tekintse meg a következőt: [install bicep (előzetes verzió)](../../azure-resource-manager/templates/bicep-install.md).

## <a name="review-the-bicep-file"></a>A bicep-fájl áttekintése

Ebben a rövid útmutatóban egy szabályzat-hozzárendelést hoz létre, és a _felügyelt lemezeket nem használó, naplózási virtuális gépek_ () nevű beépített szabályzat-definíciót rendel hozzá `06a78e20-9358-41c9-923c-fb736d382a4d` . Az elérhető beépített szabályzatok részleges listáját lásd: [Azure Policy minták](./samples/index.md).

Hozza létre a következő bicep-fájlt `assignment.bicep` :

```bicep
param policyAssignmentName string = 'audit-vm-manageddisks'
param policyDefinitionID string = '/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d'

resource assignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
    name: policyAssignmentName
    properties: {
        scope: subscriptionResourceId('Microsoft.Resources/resourceGroups', resourceGroup().name)
        policyDefinitionId: policyDefinitionID
    }
}

output assignmentId string = assignment.id
```

A fájlban megadott erőforrás:

- [Microsoft. Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

> [!NOTE]
> Azure Policy szolgáltatás ingyenes. További információ: [Azure Policy áttekintése](./overview.md).

Miután telepítette a bicep CLI-t, és létrehozta a fájlt, a következővel telepítheti a bicep fájlt:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name PolicyDeployment `
  -ResourceGroupName PolicyGroup `
  -TemplateFile assignment.bicep
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name PolicyDeployment \
  --resource-group PolicyGroup \
  --template-file assignment.bicep
```

---

Néhány további erőforrás:

- További minták sablonjait az Azure rövid útmutató [sablonjában](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)talál.
- A sablon hivatkozásának megtekintéséhez nyissa meg az [Azure-sablonok referenciáját](/azure/templates/microsoft.authorization/allversions).
- Az ARM-sablonok fejlesztéséről a [Azure Resource Manager dokumentációjában](../../azure-resource-manager/management/overview.md)talál további információt.
- Az előfizetési szintű központi telepítés megismeréséhez tekintse meg [Az erőforráscsoportok és erőforrások létrehozása az előfizetési szinten](../../azure-resource-manager/templates/deploy-to-subscription.md)című témakört.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A lap bal oldalán kattintson a **megfelelőség** elemre. Ezután keresse meg _azokat a naplózási virtuális gépeket, amelyek nem használják a felügyelt lemezeken_ létrehozott házirend-hozzárendelést.

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="Képernyőkép a megfelelőségi részletekről a szabályzat megfelelősége lapon." border="false":::

Ha vannak olyan meglévő erőforrások, amelyek nem felelnek meg az új hozzárendelésnek, akkor a **nem megfelelő erőforrások** alatt jelennek meg.

További információt a [megfelelőség működéséről](./how-to/get-compliance-data.md#how-compliance-works)szóló témakörben talál.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A létrehozott hozzárendelés eltávolításához kövesse az alábbi lépéseket:

1. Válassza a **Megfelelőség** (vagy **Hozzárendelések**) elemet az Azure Policy oldal bal oldalán, és keresse meg a létrehozott _Felügyelt lemezeket nem használó virtuális gépek naplózása_ szabályzat-hozzárendelést.

1. Kattintson a jobb gombbal a felügyelt lemezek házirend-hozzárendelését _nem használó naplózási virtuális gépekre_ , és válassza a **hozzárendelés törlése** lehetőséget.

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="Képernyőfelvétel: a helyi menü használatával törölhet egy hozzárendelést a megfelelőség lapról." border="false":::

1. Törölje a `assignment.bicep` fájlt.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy beépített szabályzat-definíciót rendelt hozzá egy hatókörhöz, és kiértékelte a megfelelőségi jelentést. A házirend-definíció ellenőrzi, hogy a hatókör összes erőforrása megfelelő-e, és azonosítja, hogy melyek nem.

Ha többet szeretne megtudni a szabályzatok hozzárendeléséről az új erőforrások megfelelőségének ellenőrzéséhez, folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
> [Szabályzatok létrehozása és kezelése](./tutorials/create-and-manage.md)