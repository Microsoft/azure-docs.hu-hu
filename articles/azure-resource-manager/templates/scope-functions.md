---
title: Sablon függvények a hatókörön belüli központi telepítésekben
description: Ismerteti, Hogyan oldhatók fel a sablon függvények a hatókörön belüli központi telepítések során. A hatókör lehet bérlő, felügyeleti csoport, előfizetések és erőforráscsoportok.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: f128448380612bc9b8d9114226e8a3036feeead8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492093"
---
# <a name="arm-template-functions-in-deployment-scopes"></a>ARM-sablon függvények a telepítési hatókörökben

Azure Resource Manager-sablonokkal (ARM-sablonok) Az erőforráscsoportok, előfizetések, felügyeleti csoportok vagy bérlők számára is telepíthetők. Általában az [ARM-sablon függvények](template-functions.md) ugyanúgy működnek az összes hatókörnél. Ez a cikk a hatókörtől függően egyes függvényekben fennálló különbségeket ismerteti.

## <a name="supported-functions"></a>Támogatott függvények

A különböző hatókörökre való telepítésekor néhány fontos szempontot figyelembe kell venni:

* Az [resourceGroup ()](template-functions-resource.md#resourcegroup) függvényt az erőforráscsoport-telepítések **támogatják** .
* Az [előfizetés ()](template-functions-resource.md#subscription) függvényt az erőforráscsoport és az előfizetés központi telepítései **támogatják** .
* A [Reference ()](template-functions-resource.md#reference) és a [List ()](template-functions-resource.md#list) függvények minden hatókör esetében **támogatottak** .
* A [resourceId ()](template-functions-resource.md#resourceid) használatával szerezze be az erőforrás-csoportba telepített erőforrások azonosítóját.

  ```json
  "subnet": {
    "id": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
  }
  ```

* Használja a [subscriptionResourceId ()](template-functions-resource.md#subscriptionresourceid) függvényt az előfizetésben üzembe helyezett erőforrás azonosítójának lekéréséhez.

  Ha például egy előfizetéshez telepített házirend-definíció erőforrás-AZONOSÍTÓját szeretné lekérni, használja a következőt:

  ```json
  "roleDefinitionId": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
  ```

* Használja a [extensionResourceId ()](template-functions-resource.md#extensionresourceid) függvényt a felügyeleti csoport bővítményként megvalósított erőforrásaihoz. A felügyeleti csoportba központilag telepített egyéni házirend-definíciók a felügyeleti csoport bővítményei.

  Ha egy egyéni házirend-definíció erőforrás-AZONOSÍTÓját szeretné lekérni a felügyeleti csoport szintjén, használja a következőt:

  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

* Használja a [tenantResourceId ()](template-functions-resource.md#tenantresourceid) függvényt a bérlőn üzembe helyezett erőforrás azonosítójának lekéréséhez. A beépített szabályzat-definíciók a bérlői szintű erőforrások. Ha a felügyeleti csoport szintjén rendel hozzá egy beépített szabályzatot, használja a tenantResourceId függvényt.

  Egy beépített szabályzat-definíció erőforrás-AZONOSÍTÓjának lekéréséhez használja a következőt:

  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="function-resolution-in-scopes"></a>Függvény feloldása hatókörökben

Ha egynél több hatókörre telepít központilag, a [resourceGroup ()](template-functions-resource.md#resourcegroup) és az [előfizetés ()](template-functions-resource.md#subscription) függvények a sablon megadása alapján eltérően oldhatók fel. Ha külső sablonra hivatkozik, a függvények mindig a sablon hatókörére lesznek feloldva. Ha sablonon belül ágyaz be egy sablont, a (z `expressionEvaluationOptions` ) tulajdonság használatával megadhatja, hogy a függvények feloldhatók-e az erőforráscsoport és a fölérendelt sablon vagy a beágyazott sablon előfizetése között. Állítsa a tulajdonságot úgy, `inner` hogy feloldja a beágyazott sablon hatókörét. Állítsa a tulajdonságot úgy, `outer` hogy feloldja a fölérendelt sablon hatókörét.

Az alábbi táblázat azt mutatja, hogy a függvények feloldhatók-e a szülő vagy a beágyazott erőforráscsoport és előfizetés esetében.

| Sablon típusa | Hatókör | Feloldás |
| ------------- | ----- | ---------- |
| beágyazott        | külső (alapértelmezett) | Szülő erőforráscsoport |
| beágyazott        | belső | Alerőforrás-csoport |
| csatolt        | N/A   | Alerőforrás-csoport |

A következő [példában a sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) látható:

* beágyazott sablon alapértelmezett (külső) hatókörrel
* beágyazott sablon belső hatókörrel
* csatolt sablon

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Az előző sablon teszteléséhez és az eredmények megtekintéséhez használja a PowerShell vagy az Azure CLI-t.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Az előző példa kimenete a következő:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Következő lépések

* Ha meg szeretné tudni, hogyan határozhat meg paramétereket a sablonban, tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című részt.
* A gyakori telepítési hibák megoldásával kapcsolatos tippekért lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managerokkal](common-deployment-errors.md).
* A SAS-tokent igénylő sablonok telepítésével kapcsolatos információkért lásd: [Private ARM-sablon üzembe helyezése sas-tokenrel](secure-template-with-sas-token.md).
