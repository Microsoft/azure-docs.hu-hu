---
title: Több erőforráspéldány üzembe helyezése
description: Másolási művelet és tömbök használata Azure Resource Manager sablonban (ARM-sablon) az erőforrástípus többszöri üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479967"
---
# <a name="resource-iteration-in-arm-templates"></a>Erőforrás-iteráció ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egy erőforrás egynél több példányát a Azure Resource Manager sablonban (ARM-sablon). Ha másolási hurkot ad hozzá a sablon resources szakaszhoz, dinamikusan beállíthatja az üzembe helyezni kívánt erőforrások számát. A sablonszintaxis megismétlését sem kell megismételnie.

A másolási ciklust olyan tulajdonságokkal is [használhatja,](copy-properties.md)mint a , [a változók](copy-variables.md)és [a kimenetek.](copy-outputs.md)

Ha meg kell adnia, hogy egy erőforrás egyáltalán üzembe legyen-e helyezni, tekintse meg a [feltételelemet.](conditional-resource-deployment.md)

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Adja hozzá a elemet a sablon resources szakaszhoz az erőforrás `copy` több példányának üzembe helyezéséhez. A `copy` elem általános formátuma a következő:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A `name` tulajdonság bármely érték, amely azonosítja a ciklust. A tulajdonság adja meg az erőforrástípushoz kívánt iterációk `count` számát.

A és a tulajdonsággal adhatja meg, hogy az erőforrások párhuzamosan vagy egymás után `mode` `batchSize` vannak-e telepítve. Ezeket a tulajdonságokat a Serial (Soros) vagy [a Parallel (Párhuzamos) leírás ismerteti.](#serial-or-parallel)

# <a name="bicep"></a>[Bicep](#tab/bicep)

A hurkok több erőforrás deklarálhatóak a következővel:

- Iterating over an array (Iterating egy tömbben):

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterating over the elements of an array

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Ciklusindex használata

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Másolási korlátok

A szám nem haladhatja meg a 800-at.

A szám nem lehet negatív szám. Nulla lehet, ha a sablont az Azure CLI, a PowerShell vagy a REST API. Pontosabban a következőt kell használnia:

- Azure PowerShell **2.6-os vagy** újabb
- Azure CLI **2.0.74 vagy** újabb
- REST API **2019. 05. 10.** vagy újabb verziója
- [A csatolt üzemelő példányok](linked-templates.md) az api **2019-05-10-es** vagy újabb verzióját kell használniuk az üzembe helyezési erőforrástípushoz

A PowerShell, a parancssori felület és a REST API korábbi verziói nem támogatják a nullát a darabszámhoz.

Körültekintően használja a [teljes módú üzembe helyezést](deployment-modes.md) a másolási hurokkal. Ha teljes módban újra üzembe lép egy erőforráscsoporton, a rendszer törli azokat az erőforrásokat, amelyek a másolási ciklus feloldása után nem vannak megadva a sablonban.

## <a name="resource-iteration"></a>Erőforrás-iteráció

Az alábbi példa a paraméterben megadott tárfiókok számát `storageCount` hozza létre.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

Figyelje meg, hogy az egyes erőforrások neve tartalmazza a függvényt, amely az aktuális `copyIndex()` iterációt adja vissza a hurokban. A `copyIndex()` nulla alapú. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex())]",
```

A következő neveket hozza létre:

- storage0
- storage1
- storage2.

Az indexérték eltolására értéket is átadhat a `copyIndex()` függvényben. Az iterációk száma továbbra is meg van adva a másolási elemben, de a értékét eltoltuk `copyIndex` a megadott értékkel. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex(1))]",
```

A következő neveket hozza létre:

- storage1
- storage2
- storage3

A másolási művelet tömbökhöz való munka során hasznos, mert a tömb minden elemét iterálhatja. A tömb függvényének használatával megadhatja az iterációk számát, és lekéri a tömb `length` `copyIndex` aktuális indexét.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Figyelje meg, hogy a rendszer `i` az indexet használja a tárfiók erőforrásnevének létrehozásakor.

---

Az alábbi példa egy tárfiókot hoz létre a paraméterben megadott összes névhez.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Ha értékeket szeretne visszaadni az üzembe helyezett erőforrásokból, használhatja a copy (másolás) részt a [outputs (kimenetek) szakaszban.](copy-outputs.md)

## <a name="serial-or-parallel"></a>Soros vagy párhuzamos

Alapértelmezés szerint a Resource Manager párhuzamosan hozza létre az erőforrásokat. A sablonban található 800 erőforrás teljes korlátja kívül nincs korlátozva a párhuzamosan üzembe helyezett erőforrások számára. A létrehozás sorrendje nem garantált.

Előfordulhat azonban, hogy az erőforrások egymás után vannak telepítve. Éles környezet frissítésekor például szükség lehet a frissítések eltömödésére, hogy egyszerre csak egy bizonyos szám legyen frissítve.

Ha például egyszerre kettővel több tárfiókot is üzembe helyez, használja a következőt:

# <a name="json"></a>[JSON](#tab/json)

Ha egy erőforrás egynél több példányát kell sorosan üzembe helyeznie, állítsa sorosra, illetve az egyszerre üzembe helyezendő `mode`  `batchSize` példányok számára. Soros módban a Resource Manager a ciklus korábbi példányaitól hoz létre függőséget, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

A értéke nem haladhatja meg a értékét `batchSize` a `count` másolási elemben.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

A `mode` tulajdonság a párhuzamos értéket is **elfogadja,** amely az alapértelmezett érték.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Egy erőforrás több példányának soros üzembe helyezéséhez állítsa a dekorátort az egyszerre üzembe helyezni szükséges `batchSize` [](./bicep-file.md#resource-and-module-decorators) példányok számára. Soros módban a Resource Manager a ciklus korábbi példányaitól hoz létre függőséget, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iteráció gyermekerőforráshoz

Gyermekerőforráshoz nem használhat másolási hurkot. Ha egy erőforrás egynél több olyan példányát is létre kell hoznia, amely általában egy másik erőforráson belül beágyazottként van definiálva, akkor azt az erőforrást felső szintű erőforrásként kell létrehoznia. A szülőerőforrással való kapcsolatot a típus és a név tulajdonságaival határozhatja meg.

Tegyük fel például, hogy egy adatkészletet általában gyermekerőforrásként definiál egy adat-előállítón belül.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Egynél több adatkészlet létrehozásához helyezze át azt az adat-előállítón kívülre. Az adatkészletnek az adat-előállítóval azonos szinten kell lennie, de továbbra is az adat-előállító gyermekerőforrása. Az adatkészlet és az adat-előállító közötti kapcsolatot a típus és a név tulajdonságaival őrizze meg. Mivel a típust már nem lehet kikövetkeztetni a sablonban való pozíciójából, a teljes típust a következő formátumban kell meg adnia: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Ha szülő/gyermek kapcsolatot létesít az adat-előállító egy példányával, adjon meg egy nevet az adatkészletnek, amely tartalmazza a szülőerőforrás nevét. Használja a következő formátumot: `{parent-resource-name}/{child-resource-name}`.

Az alábbi példa az implementációt mutatja be:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Példasablonok

Az alábbi példák gyakori forgatókönyveket mutatnak be egy erőforrás vagy tulajdonság több példányának létrehozásához.

|Sablon  |Leírás  |
|---------|---------|
|[Tároló másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Több tárfiókot helyez üzembe, amelyek nevében egy indexszám van megszabadelve. |
|[Soros másolási tároló](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Egyszerre több tárfiókot is üzembe helyez. A név tartalmazza az indexszámot. |
|[Tároló másolása tömböt tartalmazó hellyel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Több tárfiókot helyez üzembe. A név egy tömbből származó értéket tartalmaz. |

## <a name="next-steps"></a>Következő lépések

- A másolási ciklusban létrehozott erőforrások függőségeinek beállítását lásd: Az erőforrások ARM-sablonokban való üzembe helyezésének sorrendjének [definiálása.](define-resource-dependency.md)
- Az oktatóanyagot lásd: [Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal.](template-tutorial-create-multiple-instances.md)
- Az erőforrás-Microsoft Learn egy új modult az Összetett felhőbeli üzemelő példányok kezelése [speciális ARM-sablonszolgáltatásokkal.](/learn/modules/manage-deployments-advanced-arm-template-features/)
- A másolási ciklus egyéb felhasználási módokhoz lásd:
  - [Tulajdonság iterációja ARM-sablonokban](copy-properties.md)
  - [Változó iteráció az ARM-sablonokban](copy-variables.md)
  - [Kimeneti iteráció ARM-sablonokban](copy-outputs.md)
- A másolás beágyazott sablonokkal való használatával kapcsolatos információkért lásd: [Másolás használata.](linked-templates.md#using-copy)
