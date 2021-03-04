---
title: Sablonokban található paraméterek
description: Ismerteti, hogyan lehet paramétereket definiálni egy Azure Resource Manager sablonban (ARM-sablon) és a bicep-fájlban.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b23417766524204e490450568bb80b8c49e2d328
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043932"
---
# <a name="parameters-in-arm-templates"></a>Paraméterek az ARM-sablonokban

Ez a cikk azt ismerteti, hogyan lehet paramétereket definiálni és használni a Azure Resource Manager-sablonban (ARM-sablon) és a bicep-fájlban. A paraméterek eltérő értékeinek megadásával más környezetekhez is felhasználhat sablont.

A Resource Manager a telepítési műveletek megkezdése előtt oldja fel a paramétereket. Ha a paramétert használja a sablonban, a Resource Manager lecseréli azt a megoldott értékre.

Minden paramétert az egyik [adattípusra](template-syntax.md#data-types)kell beállítani.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="minimal-declaration"></a>Minimális deklaráció

Legalább minden paraméternek nevet és típust kell tartalmaznia. A bicep-ban egy paraméternek nem lehet ugyanaz a neve, mint változó, erőforrás, kimenet vagy más paraméter ugyanabban a hatókörben.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoString": {
    "type": "string"
  },
  "demoInt": {
    "type": "int"
  },
  "demoBool": {
    "type": "bool"
  },
  "demoObject": {
    "type": "object"
  },
  "demoArray": {
    "type": "array"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoString string
param demoInt int
param demoBool bool
param demoObject object
param demoArray array
```

---

## <a name="secure-parameters"></a>Biztonságos paraméterek

A karakterlánc-vagy objektum-paramétereket biztonságosként jelölheti meg. A biztonságos paraméterek értékét a rendszer nem menti a telepítési előzményekbe, és nincs naplózva.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoPassword": {
    "type": "secureString"
  },
  "demoSecretObject": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param demoPassword string

@secure()
param demoSecretObject object
```

---

## <a name="allowed-values"></a>Megengedett értékek

Megadhatja egy paraméter megengedett értékeit. Megadhatja az engedélyezett értékeket egy tömbben. A telepítés az érvényesítés során meghiúsul, ha a paraméter értéke nem az engedélyezett értékek egyike.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoEnum": {
    "type": "string",
    "allowedValues": [
      "one",
      "two"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'one'
  'two'
])
param demoEnum string
```

---

## <a name="default-value"></a>Alapértelmezett érték

Megadhat egy paraméter alapértelmezett értékét. Az alapértelmezett érték akkor használatos, ha nincs megadva érték az üzembe helyezés során.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param demoParam string = 'Contoso'
```

---

Az alapértelmezett érték megadásához a paraméter egyéb tulajdonságaival együtt használja a következő szintaxist.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso",
    "allowedValues": [
      "Contoso",
      "Fabrikam"
    ]
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@allowed([
  'Contoso'
  'Fabrikam'
])
param demoParam string = 'Contoso'
```

---

A kifejezéseket az alapértelmezett értékkel is használhatja. A paraméterek szakaszban nem használható a [Reference](template-functions-resource.md#reference) függvény vagy a [List](template-functions-resource.md#list) függvény. Ezek a függvények egy erőforrás futásidejű állapotát kapják meg, és az üzembe helyezés előtt nem hajthatók végre a paraméterek feloldásakor.

A kifejezések nem engedélyezettek más paraméterekkel.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param location string = resourceGroup().location
```

---

Az alapértelmezett érték kiépítéséhez használhat egy másik paraméter értékét is. A következő sablon egy állomásnév nevét hozza létre a hely nevéből.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]"
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string = 'site${uniqueString(resourceGroup().id)}'
param hostingPlanName string = '${siteName}-plan'
```

---

## <a name="length-constraints"></a>Megkötések hossza

A sztringek és a tömb paramétereinek minimális és maximális hosszát is megadhatja. Beállíthat egy vagy mindkét korlátozást. A karakterláncok esetében a hossz a karakterek számát jelzi. Tömbök esetén a hossz a tömbben lévő elemek számát jelzi.

Az alábbi példa két paramétert deklarál. Az egyik paraméter olyan nevű Storage-fióknév, amelynek 3-24 karakterből kell állnia. A másik paraméter egy olyan tömb, amelynek 1-5 elemből kell állnia.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "appNames": {
    "type": "array",
    "minLength": 1,
    "maxLength": 5
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

@minLength(1)
@maxLength(5)
param appNames array
```

---

## <a name="integer-constraints"></a>Egész szám megkötése

Megadhatja a minimális és a maximális értéket az egész szám paramétereknél. Beállíthat egy vagy mindkét korlátozást.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "month": {
    "type": "int",
    "minValue": 1,
    "maxValue": 12
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(1)
@maxValue(12)
param month int
```

---

## <a name="description"></a>Leírás

Leírást adhat hozzá egy paraméterhez, amely segítséget nyújt a sablon felhasználói számára, hogy megértse a megadható értéket. A sablonnak a portálon keresztüli üzembe helyezése során a leírásban megadott szöveget a rendszer automatikusan tippként használja a paraméterhez. Csak akkor vegyen fel leírást, ha a szöveg több információt tartalmaz, mint amennyit a paraméter nevéből lehet következtetni.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "virtualMachineSize": {
    "type": "string",
    "metadata": {
      "description": "Must be at least Standard_A3 to support 2 NICs."
    },
    "defaultValue": "Standard_DS1_v2"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@description('Must be at least Standard_A3 to support 2 NICs.')
param virtualMachineSize string = 'Standard_DS1_v2'
```

---

## <a name="use-parameter"></a>Paraméter használata

Egy JSON-sablonban a [Paraméterek](template-functions-deployment.md#parameters) függvény használatával hivatkozhat a paraméter értékére. A Bicepban használja a paraméter nevét. Az alábbi példa egy Key Vault nevű név paraméter értékét használja.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vaultName": {
      "type": "string",
      "defaultValue": "[format('keyVault{0}', uniqueString(resourceGroup().id))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "apiVersion": "2019-09-01",
      "name": "[parameters('vaultName')]",
      ...
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vaultName string = 'keyVault${uniqueString(resourceGroup().id)}'

resource keyvault 'Microsoft.KeyVault/vaults@2019-09-01' = {
  name: vaultName
  ...
}
```

---

## <a name="objects-as-parameters"></a>Objektumok paraméterként

A kapcsolódó értékek könnyebben rendezhetők úgy, hogy egy objektumként adják át őket. Ez a módszer a sablonban található paraméterek számát is csökkenti.

A következő példa egy olyan paramétert mutat be, amely egy objektum. Az alapértelmezett érték az objektum várt tulajdonságait jeleníti meg. Ezek a tulajdonságok a telepítendő erőforrás definiálásakor használatosak.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vNetSettings": {
      "type": "object",
      "defaultValue": {
        "name": "VNet1",
        "location": "eastus",
        "addressPrefixes": [
          {
            "name": "firstPrefix",
            "addressPrefix": "10.0.0.0/22"
          }
        ],
        "subnets": [
          {
            "name": "firstSubnet",
            "addressPrefix": "10.0.0.0/24"
          },
          {
            "name": "secondSubnet",
            "addressPrefix": "10.0.1.0/24"
          }
        ]
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Network/virtualNetworks",
      "apiVersion": "2020-06-01",
      "name": "[parameters('vNetSettings').name]",
      "location": "[parameters('vNetSettings').location]",
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[parameters('vNetSettings').addressPrefixes[0].addressPrefix]"
          ]
        },
        "subnets": [
          {
            "name": "[parameters('vNetSettings').subnets[0].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[0].addressPrefix]"
            }
          },
          {
            "name": "[parameters('vNetSettings').subnets[1].name]",
            "properties": {
              "addressPrefix": "[parameters('vNetSettings').subnets[1].addressPrefix]"
            }
          }
        ]
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param vNetSettings object = {
  name: 'VNet1'
  location: 'eastus'
  addressPrefixes: [
    {
      name: 'firstPrefix'
      addressPrefix: '10.0.0.0/22'
    }
  ]
  subnets: [
    {
      name: 'firstSubnet'
      addressPrefix: '10.0.0.0/24'
    }
    {
      name: 'secondSubnet'
      addressPrefix: '10.0.1.0/24'
    }
  ]
}
resource vnet 'Microsoft.Network/virtualNetworks@2020-06-01' = {
  name: vNetSettings.name
  location: vNetSettings.location
  properties: {
    addressSpace: {
      addressPrefixes: [
        vNetSettings.addressPrefixes[0].addressPrefix
      ]
    }
    subnets: [
      {
        name: vNetSettings.subnets[0].name
        properties: {
          addressPrefix: vNetSettings.subnets[0].addressPrefix
        }
      }
      {
        name: vNetSettings.subnets[1].name
        properties: {
          addressPrefix: vNetSettings.subnets[1].addressPrefix
        }
      }
    ]
  }
}
```

---

## <a name="example-templates"></a>Példák sablonokra

Az alábbi példák a paraméterek használatának forgatókönyveit mutatják be.

|Sablon  |Leírás  |
|---------|---------|
|[az alapértelmezett értékek függvényeit tartalmazó paraméterek](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Bemutatja, hogyan használható a Template functions a paraméterek alapértelmezett értékeinek definiálásához. A sablon nem telepít semmilyen erőforrást. Paraméterek értékeit állítja össze, és visszaadja ezeket az értékeket. |
|[paraméter objektum](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Egy paraméter objektumának használatát mutatja be. A sablon nem telepít semmilyen erőforrást. Paraméterek értékeit állítja össze, és visszaadja ezeket az értékeket. |

## <a name="next-steps"></a>Következő lépések

* A paraméterek elérhető tulajdonságainak megismeréséhez tekintse meg [az ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető témakört.
* A paraméterek értékeinek fájlként való átadásáról a [Resource Manager-paraméterérték létrehozása](parameter-files.md)című témakörben olvashat bővebben.
* A paraméterek létrehozásával kapcsolatos javaslatokért lásd: [ajánlott eljárások – paraméterek](template-best-practices.md#parameters).
