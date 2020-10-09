---
title: Sablon szintaxisa és kifejezések
description: A Azure Resource Manager-sablonok deklaratív JSON-szintaxisát ismerteti.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: baddedae1b918502e579d2ed230e0779960f45e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "82203828"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Az Azure Resource Manager-sablonok szintaxisa és kifejezései

A sablon alapszintű szintaxisa a JSON. A kifejezésekkel azonban a sablonban elérhető JSON-értékeket is kiterjesztheti.  A kifejezések szögletes zárójellel kezdődnek és végződnek: `[` és `]`. A kifejezés értékét a rendszer a sablon üzembe helyezésekor értékeli ki. A kifejezés lehet sztring, egész szám, logikai érték, tömb vagy objektum.

A sablon kifejezése nem lehet hosszabb 24 576 karakternél.

## <a name="use-functions"></a>Függvények használata

A Azure Resource Manager a sablonban használható [függvényeket](template-functions.md) biztosít. Az alábbi példa egy olyan kifejezést mutat be, amely egy függvényt használ egy paraméter alapértelmezett értékében:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

A kifejezésen belül a szintaxis `resourceGroup()` meghívja az egyik olyan függvényt, amelyet a Resource Manager biztosít a sablonon belüli használatra. Ebben az esetben ez a [resourceGroup](template-functions-resource.md#resourcegroup) függvény. A JavaScripthez hasonlóan a függvények hívásai is a következőképpen vannak formázva: `functionName(arg1,arg2,arg3)` . A szintaxis `.location` egy tulajdonságot kérdez le a függvény által visszaadott objektumból.

A sablon függvények és azok paramétereinek kis-és nagybetűk megkülönböztetése. A Resource Manager például feloldja a **változókat ("var1")** és a **változókat ("var1")** . A kiértékeléskor, ha a függvény kifejezetten nem módosítja a kis-és nagybetűket (például toUpper vagy toLower), a függvény megőrzi a kis-és nagybetűket. Bizonyos erőforrástípusok rendelkezhetnek a függvények kiértékelésének módjától eltérő eseti követelményekkel.

Ha karakterlánc-értéket szeretne átadni paraméterként egy függvénynek, használjon aposztrófot.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

A legtöbb függvény ugyanúgy működik, hogy az erőforráscsoport, az előfizetés, a felügyeleti csoport vagy a bérlő számára van-e telepítve. A következő függvények korlátozásai a hatókörön alapulnak:

* [resourceGroup](template-functions-resource.md#resourcegroup) – csak az erőforráscsoporthoz történő központi telepítések esetén használható.
* [resourceId](template-functions-resource.md#resourceid) – bármely hatókörben használható, de az érvényes paraméterek a hatókörtől függően változnak.
* [előfizetés](template-functions-resource.md#subscription) – csak erőforráscsoporthoz vagy előfizetéshez használható központi telepítések esetén.

## <a name="escape-characters"></a>Escape-karakterek

Ahhoz, hogy egy literális sztring bal oldali zárójelmel kezdődjön `[` , és jobb oldali szögletes zárójelet `]` adjon meg, de nem értelmezhető kifejezésként, vegyen fel egy extra zárójelet a sztring elindításához `[[` . Például a következő változó:

```json
"demoVar1": "[[test value]"
```

Feloldás a következőhöz: `[test value]` .

Ha azonban a literál sztring nem egy szögletes zárójelre végződik, ne hagyja ki az első zárójelet. Például a következő változó:

```json
"demoVar2": "[test] value"
```

Feloldás a következőhöz: `[test] value` .

Ha az idézőjeleket egy kifejezésben szeretné kipróbálni, például egy JSON-objektum hozzáadását a sablonban, használja a fordított perjelet.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

A paraméterek értékeinek átadásakor az Escape-karakterek használata attól függ, hogy hol van megadva a paraméter értéke. Ha a sablonban alapértelmezett értéket állít be, akkor az extra bal oldali szögletes zárójelre van szükség.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Ha az alapértelmezett értéket használja, a sablon visszaadja a értéket `[test value]` .

Ha azonban a parancssorban adja meg a paraméter értékét, a karakterek értelmezése szó szerint történik. Az előző sablon üzembe helyezése az alábbiakkal:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Visszatérési érték `[[test value]` . Ehelyett használja a következőket:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Ugyanez a formázás vonatkozik az értékek egy paraméter fájlból való átadásakor. A karakterek értelmezése szó szerint történik. Az előző sablonnal való használat esetén a következő paramétert adja vissza `[test value]` :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null-értékek

Ha a tulajdonságot NULL értékre kívánja beállítani, használhatja a null vagy a **[JSON ("NULL")]** **értéket** . A [JSON-függvény](template-functions-object.md#json) üres objektumot ad vissza, ha `null` paraméterként adja meg. A Resource Manager-sablonok mindkét esetben úgy bánnak, mintha a tulajdonság nincs jelen.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>További lépések

* A sablon függvények teljes listájáért lásd: [Azure Resource Manager template functions](template-functions.md).
* További információ a sablonfájlokről: [Azure Resource Manager sablonok struktúrájának és szintaxisának megismerése](template-syntax.md).
