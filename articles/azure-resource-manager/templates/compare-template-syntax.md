---
title: A JSON-és a bicep Azure Resource Manager-sablonok szintaxisának összehasonlítása
description: Összehasonlítja a JSON-val és a bicep-vel fejlesztett Azure Resource Manager sablonokat, és bemutatja, hogyan lehet váltani a nyelvek között.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461794"
---
# <a name="comparing-json-and-bicep-for-templates"></a>A JSON és a bicep összehasonlítása a sablonokkal

Ez a cikk a bicep szintaxist hasonlítja össze a Azure Resource Manager-sablonok JSON-szintaxisával (ARM-sablonok). A legtöbb esetben a bicep olyan szintaxist biztosít, amely kevésbé részletes, mint a JSON-ban.

Ha ismeri a JSON-t az ARM-sablonok fejlesztéséhez, az alábbi példákat követve megismerheti a bicep-hoz egyenértékű szintaxist.

## <a name="expressions"></a>Kifejezések

Kifejezés készítése:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Paraméterek

Paraméter deklarálása alapértelmezett értékkel:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Paraméter értékének beolvasása:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Változók

Változó deklarálása:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Változó értékének beolvasása:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Sztringek

Karakterláncok összefűzése:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Logikai operátorok

A logikai **és** a következő érték visszaadása:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Egy érték feltételes beállítása:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Központi telepítés hatóköre

A központi telepítés cél hatókörének beállítása:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>További források

Erőforrás deklarálása:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Erőforrás feltételes üzembe helyezése:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Az erőforrás tulajdonság beállítása:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Az erőforrás erőforrás-AZONOSÍTÓjának lekérése a sablonban:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Hurkok

Tömb vagy darabszám elemeinek megismétlése:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Erőforrás-függőségek

Függőség beállítása az erőforrások között:

A bicep esetében vagy a függőségek automatikus észlelésére vagy a függőség manuális beállítására támaszkodik.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Segédanyagok

Tulajdonság beolvasása a sablonban lévő erőforrásból:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Egy olyan meglévő erőforrásból származó tulajdonság beszerzése, amely nincs telepítve a sablonban:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Kimenetek

Tulajdonság kimenetének létrehozása a sablonban lévő erőforrásból:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Kód újrafelhasználása

Megoldás elkülönítése több fájlba:

* A bicep esetében használjon [modulokat](bicep-tutorial-add-modules.md).
* JSON esetén használjon [csatolt sablonokat](linked-templates.md).

## <a name="recommendations"></a>Javaslatok

* Ha lehetséges, kerülje a [hivatkozási](template-functions-resource.md#reference) és a [resourceId](template-functions-resource.md#resourceid) függvények használatát a bicep fájlban. Ha ugyanarra a bicep-telepítésre hivatkozik egy erőforrásra, használja helyette az erőforrás-azonosítót. Ha például telepített egy erőforrást a bicep-fájlban `stg` erőforrás-azonosítóként, használja a (z) szintaxist, `stg.id` vagy adja `stg.properties.primaryEndpoints.blob` meg a tulajdonság értékeit. Az erőforrás-azonosító használatával implicit függőséget hoz létre az erőforrások között. Nem kell explicit módon beállítania a függőséget a dependsOn tulajdonsággal.
* Ha az erőforrás nincs telepítve a bicep fájlban, akkor továbbra is szimbolikus hivatkozást kaphat az erőforráshoz a **meglévő** kulcsszó használatával.
* Használjon konzisztens burkolatot az azonosítók számára. Ha nem tudja biztosan, hogy milyen típusú burkolatot szeretne használni, próbálja ki a Camel burkolatot. Például: `param myCamelCasedParameter string`.
* Csak akkor adjon meg egy leírást a paraméterhez, ha a Leírás alapvető információkat biztosít a felhasználóknak. `//`Néhány információhoz megjegyzéseket is használhat.

## <a name="next-steps"></a>Következő lépések

* További információ a bicep: [bicep oktatóanyag](./bicep-tutorial-create-first-bicep.md).
* A sablonok nyelvek közötti átalakításával kapcsolatos további információkért lásd: [ARM-sablonok konvertálása JSON és bicep között](bicep-decompile.md).
