---
title: Hatókör a bővítmény erőforrástípusok esetében
description: Ismerteti, hogyan használható a hatókör tulajdonság a bővítmény típusú erőforrástípusok telepítésekor.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: ce08ca951e24c1c0a5450052cf814a68888837c2
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492161"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Hatókör beállítása a bővítmény erőforrásaihoz az ARM-sablonokban

A kiterjesztési erőforrások olyan erőforrások, amelyek egy másik erőforrást módosítanak. Hozzárendelhet például egy szerepkört egy erőforráshoz. A szerepkör-hozzárendelés egy bővítmény típusú erőforrástípus.

A bővítmények erőforrásainak teljes listáját lásd: [más erőforrások képességeit kiterjesztő erőforrástípusok](../management/extension-resource-types.md).

Ez a cikk bemutatja, hogyan állíthatja be egy bővítmény erőforrástípus hatókörét Azure Resource Manager sablon (ARM-sablon) telepítésekor. Ismerteti a hatókör tulajdonságot, amely a bővítmény erőforrásaihoz érhető el az erőforrásokra való alkalmazáskor.

> [!NOTE]
> A hatókör tulajdonság csak a kiterjesztési erőforrástípusok esetében érhető el. Ha eltérő hatókört szeretne megadni egy olyan erőforrástípus számára, amely nem a bővítmény típusa, használjon beágyazott vagy csatolt központi telepítést. További információ: [erőforráscsoportok központi telepítése](deploy-to-resource-group.md), [előfizetés telepítése](deploy-to-subscription.md), [felügyeleti csoport telepítése](deploy-to-management-group.md)és [bérlői telepítések](deploy-to-tenant.md).

## <a name="apply-at-deployment-scope"></a>Alkalmazás a központi telepítés hatókörében

A bővítmény erőforrástípus a cél központi telepítési hatókörön való alkalmazásához adja hozzá az erőforrást a sablonhoz, ahogy az erőforrástípus lenne. Az elérhető hatókörök az [erőforráscsoport](deploy-to-resource-group.md), az [előfizetés](deploy-to-subscription.md), a [felügyeleti csoport](deploy-to-management-group.md)és a [bérlő](deploy-to-tenant.md). A központi telepítési hatókörnek támogatnia kell az erőforrás típusát.

A következő sablon egy zárolást helyez üzembe.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

Egy erőforráscsoport telepítésekor a rendszer zárolja az erőforráscsoportot.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

A következő példa egy szerepkört rendel hozzá.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

Egy előfizetéshez való üzembe helyezéskor a szerepkört hozzárendeli az előfizetéshez.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Alkalmazás erőforrásra

Ha erőforrásra szeretné alkalmazni a bővítmény erőforrását, használja a (z `scope` ) tulajdonságot. Állítsa a hatókör tulajdonságot annak az erőforrásnak a nevére, amelyhez a bővítményt hozzáadja. A hatókör tulajdonság a bővítmény erőforrástípus legfelső szintű tulajdonsága.

A következő példa létrehoz egy Storage-fiókot, és egy szerepkört alkalmaz rá.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
* A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [Private ARM-sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
