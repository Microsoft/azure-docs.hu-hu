---
title: Egy tulajdonság több példányának meghatározása
description: A másolási művelettel egy Azure Resource Manager sablonban (ARM-sablon) többször is iterálhat, amikor tulajdonságot hoz létre egy erőforráson.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480001"
---
# <a name="property-iteration-in-arm-templates"></a>Tulajdonság iterációja ARM-sablonokban

Ez a cikk bemutatja, hogyan hozhatja létre egy tulajdonság egynél több példányát a Azure Resource Manager sablonban (ARM-sablon). Ha másolási hurkot ad hozzá a sablonban lévő erőforrás tulajdonságok szakaszhoz, dinamikusan beállíthatja egy tulajdonság elemeinek számát az üzembe helyezés során. A sablonszintaxis megismétlését is elkerülheti.

A másolási hurok csak legfelső szintű erőforrásokkal használható, még akkor is, ha másolási ciklust alkalmaz egy tulajdonságra. További információ a gyermekerőforrás legfelső szintű erőforrásra való módosításáról: [Iteráció gyermekerőforráshoz.](copy-resources.md#iteration-for-a-child-resource)

A másolási ciklust [](copy-resources.md)erőforrásokkal, [változókkal](copy-variables.md)és [kimenetekkel is használhatja.](copy-outputs.md)

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Adja hozzá a elemet a sablon resources (erőforrások) szakaszhoz egy tulajdonság `copy` elemeinek számának beállítására. A másolási elem általános formátuma a következő:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

A fájlhoz adja meg a létrehozni kívánt `name` erőforrás-tulajdonság nevét.

A tulajdonság határozza meg a tulajdonsághoz `count` kívánt iterációk számát.

A `input` tulajdonság határozza meg az ismételni kívánt tulajdonságokat. A tulajdonságban található értékből létrehozott elemek tömbje lesz `input` létrehozva.

# <a name="bicep"></a>[Bicep](#tab/bicep)

A hurkok több tulajdonság deklarálhatóak a következővel:

- Iterating over an array (Iterating egy tömbben):

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Iterating over the elements of an array

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Ciklusindex használata

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Másolási korlátok

A szám nem haladhatja meg a 800-at.

A szám nem lehet negatív szám. Nulla is lehet, ha a sablont az Azure CLI, a PowerShell vagy a REST API. Konkrétan a következőt kell használnia:

- Azure PowerShell **2.6-os vagy** újabb
- Azure CLI **2.0.74 vagy** újabb
- REST API **2019. 05. 10.** vagy újabb verzió
- [A csatolt üzemelő példányok](linked-templates.md) az API **2019-05-10-es** vagy újabb verzióját kell használniuk az üzembe helyezési erőforrástípushoz

A PowerShell, a parancssori felület és a REST API nem támogatják a nullát a darabszámhoz.

## <a name="property-iteration"></a>Tulajdonság iterációja

Az alábbi példa bemutatja, hogyan alkalmazhat másolási hurkot a tulajdonságra `dataDisks` egy virtuális gépen:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

Figyelje meg, hogy a tulajdonság-iteráción belüli használata esetén meg kell adnia `copyIndex` az iteráció nevét. A tulajdonság-iteráció az eltolási argumentumot is támogatja. Az eltolásnak az iteráció neve után kell lennie, például `copyIndex('dataDisks', 1)` : .

Az üzembe helyezett sablon a következő lesz:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

A másolási művelet tömbökhöz való munka során hasznos, mert a tömb minden elemét iterálhatja. A tömb függvényének használatával megadhatja az iterációk számát, és lekéri a tömb `length` `copyIndex` aktuális indexét.

Az alábbi példasablon létrehoz egy feladatátvételi csoportot a tömbként átadott adatbázisokhoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

Az elem egy tömb, így több tulajdonságot `copy` is megadhat az erőforráshoz.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

Az üzembe helyezett sablon a következő lesz:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

---

Az erőforrás- és tulajdonság-iteráció együtt is használható. Hivatkozhat a tulajdonság-iterációra név alapján.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Példasablonok

Az alábbi példa egy olyan gyakori forgatókönyvet mutat be, amikor egy tulajdonsághoz egynél több értéket kell létrehozni.

|Sablon  |Leírás  |
|---------|---------|
|[Virtuális gép üzembe helyezése változó számú adatlemezzel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Több adatlemezt helyez üzembe egy virtuális géppel. |

## <a name="next-steps"></a>Következő lépések

- Az oktatóanyagot lásd: [Oktatóanyag: Több erőforráspéldány létrehozása ARM-sablonokkal.](template-tutorial-create-multiple-instances.md)
- A másolási ciklus egyéb felhasználási módokhoz lásd:
  - [Erőforrás-iteráció ARM-sablonokban](copy-resources.md)
  - [Változó iteráció az ARM-sablonokban](copy-variables.md)
  - [Kimeneti iteráció ARM-sablonokban](copy-outputs.md)
- Ha többet szeretne megtudni a sablonok szakaszairól, tekintse meg az ARM-sablonok szerkezetének és [szintaxisának megismerése szakaszt.](template-syntax.md)
- A sablon üzembe helyezésének elsajátítása: Erőforrások üzembe helyezése [ARM-sablonokkal](deploy-powershell.md)és Azure PowerShell.
