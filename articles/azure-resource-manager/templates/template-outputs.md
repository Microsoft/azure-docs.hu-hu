---
title: Kimenetek a sablonokban
description: Ismerteti, hogyan lehet kimeneti értékeket definiálni egy Azure Resource Manager sablonban (ARM-sablon) és a bicep-fájlban.
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 2b6a6afa127bf43102103baadae576233843f00d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123411"
---
# <a name="outputs-in-arm-templates"></a>Kimenetek ARM-sablonokban

Ez a cikk bemutatja, hogyan határozhatja meg a kimeneti értékeket a Azure Resource Manager-sablonban (ARM-sablon) és a bicep-fájlban. A kimenetek akkor használhatók, ha értékeket kell visszaadnia az üzembe helyezett erőforrásokból.

Az egyes kimeneti értékek formátumának meg kell oldania az egyik [adattípust](data-types.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-output-values"></a>Kimeneti értékek definiálása

Az alábbi példa bemutatja, hogyan adhat vissza egy tulajdonságot egy üzembe helyezett erőforrásból.

# <a name="json"></a>[JSON](#tab/json)

A JSON esetében adja hozzá a `outputs` szakaszt a sablonhoz. A kimeneti érték egy nyilvános IP-cím teljes tartománynevét kapja meg.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

A bicep, használja a `output` kulcsszót.

A következő példában a a `publicIP` bicep fájlban üzembe helyezett nyilvános IP-cím azonosítója. A kimeneti érték a nyilvános IP-cím teljes tartománynevét kapja meg.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

---

Ha olyan tulajdonságot kell kiadnia, amely kötőjelet tartalmaz a névben, a név helyett szögletes zárójeleket használjon. A helyett használja például a következőt:  `['property-name']` `.property-name` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "user": {
            "user-name": "Test Person"
        }
    },
    "resources": [
    ],
    "outputs": {
        "nameResult": {
            "type": "string",
            "value": "[variables('user')['user-name']]"
        }
    }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var user = {
  'user-name': 'Test Person'
}

output stringOutput string = user['user-name']
```

---

## <a name="conditional-output"></a>Feltételes kimenet

Feltételesen visszaadhat egy értéket. Általában feltételes kimenetet használ, amikor [feltételesen telepített](conditional-resource-deployment.md) egy erőforrást. Az alábbi példa azt mutatja be, hogyan lehet feltételesen visszaadni egy nyilvános IP-cím erőforrás-AZONOSÍTÓját attól függően, hogy egy újat telepített-e:

# <a name="json"></a>[JSON](#tab/json)

A JSON-ban adja hozzá az `condition` elemet annak meghatározásához, hogy a rendszer visszaadja-e a kimenetet.

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Ha feltételes kimenetet szeretne megadni a bicep, használja a `?` kezelőt. A következő példa egy végpont URL-címét vagy egy feltételtől függően üres karakterláncot ad vissza.

```bicep
param deployStorage bool = true
param storageName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (deployStorage) {
  name: storageName
  location: location
  kind: 'StorageV2'
  sku:{
    name:'Standard_LRS'
    tier: 'Standard'
  }
  properties: {
    accessTier: 'Hot'
  }
}

output endpoint string = deployStorage ? sa.properties.primaryEndpoints.blob : ''
```

---

A feltételes kimenet egyszerű példáját lásd: [feltételes kimeneti sablon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Kimenetek dinamikus száma

Bizonyos helyzetekben nem tudja, hogy hány példányban kell visszaadni a sablon létrehozásakor. Egy változó számú értéket adhat vissza iterációs kimenet használatával.

# <a name="json"></a>[JSON](#tab/json)

A JSON-ban adja hozzá az `copy` elemet, és ismételje meg a kimenetet.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

Az iterációs kimenet jelenleg nem érhető el a bicep számára.

---

További információ: [a kimenet iterációja az ARM-sablonokban](copy-outputs.md).

## <a name="linked-templates"></a>Hivatkozott sablonok

A JSON-sablonokban a kapcsolódó sablonokat [csatolt sablonok](linked-templates.md)használatával is telepítheti. Egy csatolt sablon kimeneti értékének lekéréséhez használja a fölérendelt sablon [hivatkozási](template-functions-resource.md#reference) függvényét. A fölérendelt sablon szintaxisa a következőket eredményezi:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Az alábbi példa bemutatja, hogyan állíthatja be az IP-címet egy terheléselosztó számára egy érték egy csatolt sablonból való beolvasásával.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Ha a tulajdonság neve kötőjelet tartalmaz, a név helyett szögletes zárójeleket használjon.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs['resource-ID'].value]"
}
```

`reference` [Beágyazott sablon](linked-templates.md#nested-template)kimenetek szakaszában nem használhatja a függvényt. Egy beágyazott sablonban lévő üzembe helyezett erőforrás értékeinek visszaküldéséhez alakítsa át a beágyazott sablont egy csatolt sablonba.

A [nyilvános IP-cím sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) egy nyilvános IP-címet hoz létre, és kiírja az erőforrás-azonosítót. A [terheléselosztó sablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) az előző sablonra hivatkozik. A terheléselosztó létrehozásakor a kimenetben lévő erőforrás-azonosítót használja.

## <a name="modules"></a>Modulok

A bicep-fájlokban a kapcsolódó sablonokat modulok használatával is telepítheti. Egy modul kimeneti értékének lekéréséhez használja a következő szintaxist:

```bicep
<module-name>.outputs.<property-name>
```

Az alábbi példa bemutatja, hogyan állíthatja be egy terheléselosztó IP-címét egy modul értékének beolvasásával. A modul neve: `publicIP` .

```bicep
publicIPAddress: {
  id: publicIP.outputs.resourceID
}
```

## <a name="example-template"></a>Példa sablonra

A következő sablon egyetlen erőforrást sem helyez üzembe. A különböző típusú kimenetek visszaküldésének bizonyos módjait mutatja be.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/outputs.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

A bicep jelenleg nem támogatja a hurkokat.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/outputs.bicep":::

---

## <a name="get-output-values"></a>Kimeneti értékek beolvasása

Az üzembe helyezés sikeressége után a rendszer automatikusan visszaadja a kimeneti értékeket a központi telepítés eredményeiben.

A telepítési előzményekből származó kimeneti értékek lekéréséhez használhatja a parancsfájlt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="next-steps"></a>Következő lépések

* A kimenetek elérhető tulajdonságainak megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerése](template-syntax.md)című témakört.
