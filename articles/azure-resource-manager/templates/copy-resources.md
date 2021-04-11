---
title: Több erőforrás-példány üzembe helyezése
description: A másolási művelet és tömbök használata Azure Resource Manager sablonban (ARM-sablon) az erőforrástípus többszöri üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 3af676cce544c125e441857f06556b9ff7eee697
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385708"
---
# <a name="resource-iteration-in-arm-templates"></a>Erőforrás-iteráció az ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhat létre egy erőforrás több példányát a Azure Resource Manager-sablonban (ARM-sablon). A másolási hurok a sablon erőforrások szakaszába való hozzáadásával dinamikusan állíthatja be a telepítendő erőforrások számát. Emellett ne kelljen megismételni a sablon szintaxisát.

A másolási hurkot használhatja a [Tulajdonságok](copy-properties.md), [változók](copy-variables.md)és [kimenetek](copy-outputs.md)használatával is.

Ha meg kell adnia, hogy az erőforrás telepítve van-e, tekintse meg a [feltétel elemet](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Adja hozzá az `copy` elemet a sablon erőforrások szakaszához az erőforrás több példányának telepítéséhez. Az `copy` elem formátuma a következő:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

A `name` tulajdonság bármely olyan érték, amely a hurok azonosítására szolgál. A `count` tulajdonság határozza meg az erőforrástípus kívánt ismétlések számát.

A `mode` és a `batchSize` Tulajdonságok használatával adhatja meg, hogy az erőforrások párhuzamosan vagy sorba vannak-e telepítve. Ezeket a tulajdonságokat a [soros vagy párhuzamosan](#serial-or-parallel)kell ismertetni.

# <a name="bicep"></a>[Bicep](#tab/bicep)

A hurkok több erőforrást is használhatnak:

- Iteráció egy tömbben:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }
  ```

- Egy tömb elemeinek megismétlése

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }
  ```

- Hurok-index használata

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }
  ```

---

## <a name="copy-limits"></a>Másolási korlátok

A szám nem lehet nagyobb, mint 800.

A darabszám nem lehet negatív szám. Ha az Azure CLI, a PowerShell vagy a REST API legújabb verziójával telepíti a sablont, akkor nulla lehet. Pontosabban a következőket kell használnia:

- Azure PowerShell **2,6** vagy újabb
- Azure CLI- **2.0.74** vagy újabb
- REST API **2019-05-10** -es vagy újabb verzió
- A [csatolt központi telepítéseknek](linked-templates.md) a telepítési erőforrástípus **2019-05-10** -es vagy újabb API-verzióját kell használniuk

A PowerShell, a CLI és a REST API korábbi verziói nem támogatják a nulla értéket a darabszámhoz.

A másolási hurok használatával ügyeljen a [teljes módú üzembe helyezésre](deployment-modes.md) . Ha egy erőforráscsoport esetében újratelepíti a teljes módot, a másolási hurok feloldása után a sablonban nem megadott erőforrások törlődnek.

## <a name="resource-iteration"></a>Erőforrás-iteráció

A következő példa a paraméterben megadott Storage-fiókok számát hozza létre `storageCount` .

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

Figyelje meg, hogy az egyes erőforrások neve tartalmazza a `copyIndex()` függvényt, amely az aktuális iterációt adja vissza a hurokban. A `copyIndex()` nulla alapú. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex())]",
```

A következő neveket hozza létre:

- storage0
- storage1
- storage2.

Az index értékének eltolásához átadhat egy értéket a `copyIndex()` függvényben. Az ismétlések száma továbbra is meg van adva a másolási elemben, de a értéke a `copyIndex` megadott értékkel van kiegyenlítve. Tehát a következő példa:

```json
"name": "[concat('storage', copyIndex(1))]",
```

A következő neveket hozza létre:

- storage1
- storage2
- storage3

A másolási művelet hasznos lehet a tömbök használatakor, mert a tömb minden elemén megismételhető. Használja a `length` tömb függvényét az iterációk számának megadásához, valamint a `copyIndex` tömb aktuális indexének lekéréséhez.

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

Figyelje meg, hogy az index a `i` Storage-fiók erőforrás-nevének létrehozásakor használatos.

---

A következő példa egy Storage-fiókot hoz létre a paraméterben megadott minden névhez.

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

Ha a központilag telepített erőforrások értékeit szeretné visszaadni, a [kimenetek szakaszban a másolás](copy-outputs.md)lehetőséget használhatja.

## <a name="serial-or-parallel"></a>Soros vagy párhuzamos

Alapértelmezés szerint a Resource Manager párhuzamosan hozza létre az erőforrásokat. Nem korlátozza a párhuzamosan üzembe helyezett erőforrások számát, kivéve a sablonban lévő 800-erőforrások teljes korlátját. A létrehozásuk sorrendje nem garantált.

Azonban érdemes megadnia, hogy az erőforrások sorba legyenek telepítve. Ha például éles környezetet frissít, érdemes lehet megosztani a frissítéseket, hogy csak egy adott szám legyen frissítve egyszerre.

Ha például a Storage-fiókokat egyszerre két sorba szeretné telepíteni, használja a következőt:

# <a name="json"></a>[JSON](#tab/json)

Egy adott erőforrás egynél több példányának soros üzembe helyezéséhez állítsa a `mode` **soros** értéket, és adja meg `batchSize` az egyszerre telepítendő példányok számát. A soros módban a Resource Manager egy függőséget hoz létre a hurok korábbi példányain, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

A nem lehet nagyobb az értéknél a `batchSize` `count` másolási elemben.

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

A `mode` tulajdonság szintén **párhuzamosan** fogadja el az alapértelmezett értéket.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Egy erőforrás egynél több példányának soros üzembe helyezéséhez állítsa a `batchSize` [dekoratőr](./bicep-file.md#resource-and-module-decorators) -t az egyszerre telepítendő példányok számára. A soros módban a Resource Manager egy függőséget hoz létre a hurok korábbi példányain, így nem indít el egy köteget, amíg az előző köteg be nem fejeződik.

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

## <a name="iteration-for-a-child-resource"></a>Gyermek erőforrás iterációja

Alárendelt erőforráshoz nem használhat másolási hurkot. Ha egy erőforrás több példányát szeretné létrehozni, amelyet általában egy másik erőforrásban ágyaznak be, ehelyett legfelső szintű erőforrásként kell létrehoznia az erőforrást. A szülő erőforrással létesített kapcsolatot a típus és a név tulajdonsággal határozhatja meg.

Tegyük fel például, hogy az adatkészletet általában alárendelt erőforrásként definiálja egy adat-előállítón belül.

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

Egynél több adathalmaz létrehozásához helyezze át azt az adatelőállítón kívülre. Az adatkészletnek a adat-előállítóval megegyező szinten kell lennie, de még mindig az adat-előállító alárendelt erőforrása. Az adatkészletek és a adatfeldolgozók közötti kapcsolatot a típus és a név tulajdonságon keresztül megőrizheti. Mivel a típus már nem következtethető ki a sablonban lévő pozícióból, a teljes típust a következő formátumban kell megadnia: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Ha szülő/gyermek kapcsolatot szeretne létesíteni az adatelőállító egy példányával, adja meg a szülő erőforrás nevét tartalmazó adatkészlet nevét. Használja a következő formátumot: `{parent-resource-name}/{child-resource-name}`.

A következő példa a megvalósítást mutatja be:

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

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példák egy erőforrás vagy tulajdonság egynél több példányának létrehozására vonatkozó gyakori forgatókönyveket mutatnak be.

|Sablon  |Leírás  |
|---------|---------|
|[Tárterület másolása](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Egynél több Storage-fiókot telepít a névben. |
|[Soros másolási tár](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Egyszerre több Storage-fiókot helyez üzembe. A név tartalmazza az index számát. |
|[Tároló másolása tömbvel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Több Storage-fiók üzembe helyezése. A név egy tömbből származó értéket tartalmaz. |

## <a name="next-steps"></a>Következő lépések

- A másolási hurokban létrehozott erőforrásokra vonatkozó függőségek beállításához tekintse meg [az erőforrások ARM-sablonokban való üzembe helyezési sorrendjének meghatározása](define-resource-dependency.md)című témakört.
- Az oktatóanyag lépéseinek megismeréséhez tekintse meg [az oktatóanyag: több erőforrás-példány létrehozása ARM-sablonokkal](template-tutorial-create-multiple-instances.md)című témakört.
- Az erőforrás-másolást lefedi Microsoft Learn modul esetében lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablonok használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).
- A másolási hurok egyéb felhasználási módjaiért lásd:
  - [Tulajdonság-iteráció az ARM-sablonokban](copy-properties.md)
  - [Változó iteráció az ARM-sablonokban](copy-variables.md)
  - [Kimeneti iteráció az ARM-sablonokban](copy-outputs.md)
- További információ a másolás beágyazott sablonokkal történő használatáról: [a másolás használata](linked-templates.md#using-copy).
