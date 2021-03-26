---
title: Sablon functions – dátum
description: A Azure Resource Manager-sablonban (ARM-sablonban) használandó függvényeket ismerteti a dátumokkal való munkavégzéshez.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: abff5b86ad1e10042596b11f613cdb594e307209
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889926"
---
# <a name="date-functions-for-arm-templates"></a>Az ARM-sablonokhoz tartozó Date functions

A Resource Manager a következő függvényeket biztosítja a dátumoknak a Azure Resource Manager-sablonban való használatához (ARM-sablon):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Az időtartamot adja hozzá egy alapértékhez. Az ISO 8601 formátuma várható.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| base | Igen | sztring | A Hozzáadás kezdő datetime értéke. Az [ISO 8601 timestamp formátumot](https://en.wikipedia.org/wiki/ISO_8601)használja. |
| duration | Igen | sztring | Az alaphoz hozzáadandó idő érték. Ez lehet negatív érték. Az [ISO 8601 időtartam formátuma](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| formátumban | Nem | sztring | A dátum és idő eredményének kimeneti formátuma Ha nincs megadva, a rendszer az alapérték formátumát használja. Használjon [szabványos formázó karakterláncokat](/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [Egyéni formázó karakterláncokat](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Visszatérési érték

A DateTime érték, amely az időtartam értékének az alapértékhez való hozzáadását eredményezi.

### <a name="examples"></a>Példák

Az alábbi példában az időértékek hozzáadásának különböző módjai láthatók.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]"
    }
  },
  "variables": {
    "add3Years": "[dateTimeAdd(parameters('baseTime'), 'P3Y')]",
    "subtract9Days": "[dateTimeAdd(parameters('baseTime'), '-P9D')]",
    "add1Hour": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [],
  "outputs": {
    "add3YearsOutput": {
      "value": "[variables('add3Years')]",
      "type": "string"
    },
    "subtract9DaysOutput": {
      "value": "[variables('subtract9Days')]",
      "type": "string"
    },
    "add1HourOutput": {
      "value": "[variables('add1Hour')]",
      "type": "string"
    },
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

---

Ha az előző sablon üzembe helyezése alapidővel történik `2020-04-07 14:53:14Z` , a kimenet a következőket eredményezi:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| add3YearsOutput | Sztring | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | Sztring | 3/29/2020 2:53:14 PM |
| add1HourOutput | Sztring | 4/7/2020 3:53:14 PM |

A következő példa azt mutatja be, hogyan állíthatja be az automatizálási ütemterv kezdési idejét.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "omsAutomationAccountName": {
      "type": "string",
      "defaultValue": "demoAutomation",
      "metadata": {
        "description": "Use an existing Automation account."
      }
    },
    "scheduleName": {
      "type": "string",
      "defaultValue": "demoSchedule1",
      "metadata": {
        "description": "Name of the new schedule."
      }
    },
    "baseTime": {
      "type": "string",
      "defaultValue": "[utcNow('u')]",
      "metadata": {
        "description": "Schedule will start one hour from this time."
      }
    }
  },
  "variables": {
    "startTime": "[dateTimeAdd(parameters('baseTime'), 'PT1H')]"
  },
  "resources": [
    ...
    {
      "type": "Microsoft.Automation/automationAccounts/schedules",
      "apiVersion": "2015-10-31",
      "name": "[concat(parameters('omsAutomationAccountName'), '/', parameters('scheduleName'))]",

      "properties": {
        "description": "Demo Scheduler",
        "startTime": "[variables('startTime')]",
        "interval": 1,
        "frequency": "Hour"
      }
    }
  ],
  "outputs": {
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

---

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Az aktuális (UTC) dátum és idő értéket adja vissza a megadott formátumban. Ha nincs megadva formátum, az ISO 8601 ( `yyyyMMddTHHmmssZ` ) formátumot használja a rendszer. **Ez a függvény csak a paraméter alapértelmezett értékében használható.**

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| formátumban |Nem |sztring |A karakterláncra konvertálandó URI-kódolású érték. Használjon [szabványos formázó karakterláncokat](/dotnet/standard/base-types/standard-date-and-time-format-strings) vagy [Egyéni formázó karakterláncokat](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Megjegyzések

Ezt a függvényt csak egy paraméter alapértelmezett értékére használhatja egy kifejezésen belül. Ha ezt a funkciót a sablonban bárhol máshol használja, hibaüzenetet ad vissza. A függvény nem engedélyezett a sablon más részeiben, mert minden egyes híváskor más értéket ad vissza. Ugyanazon sablon ugyanazon paraméterekkel való üzembe helyezése nem eredményezi megbízhatóan ugyanazt az eredményt.

Ha a hibát egy korábbi sikeres központi telepítésre történő [visszaállításra](rollback-on-error.md) használja, és a korábbi központi telepítés tartalmaz egy utcNow-t használó paramétert, a paraméter nem lett újraértékelve. Ehelyett a korábbi központi telepítés paraméterének értékét a rendszer automatikusan újra felhasználja a visszaállítási telepítésben.

Ügyeljen arra, hogy egy alapértelmezett érték esetén a utcNow függvényre támaszkodó sablont telepítse újra. Ha újratelepíti, és nem ad meg értéket a paraméterhez, a függvény újraértékelése megtörténik. Ha egy meglévő erőforrást nem új létrehozása helyett szeretne frissíteni, adja át a paraméter értékét a korábbi telepítésből.

### <a name="return-value"></a>Visszatérési érték

Az aktuális UTC datetime érték.

### <a name="examples"></a>Példák

A következő példa sablon a DateTime érték különböző formátumait jeleníti meg.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    },
    "utcShortValue": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "utcCustomValue": {
      "type": "string",
      "defaultValue": "[utcNow('M d')]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "utcOutput": {
      "type": "string",
      "value": "[parameters('utcValue')]"
    },
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShortValue')]"
    },
    "utcCustomOutput": {
      "type": "string",
      "value": "[parameters('utcCustomValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

---

Az előző példa kimenete az egyes központi telepítések esetében változik, de a következőhöz hasonló lesz:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| utcOutput | sztring | 20190305T175318Z |
| utcShortOutput | sztring | 03/05/2019 |
| utcCustomOutput | sztring | 3 5 |

A következő példa azt szemlélteti, hogyan használható a függvény értéke a címke értékének beállításakor.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcShort": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "rgName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2020-10-01",
      "name": "[parameters('rgName')]",
      "location": "westeurope",
      "tags": {
        "createdDate": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ],
  "outputs": {
    "utcShortOutput": {
      "type": "string",
      "value": "[parameters('utcShort')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

---

## <a name="next-steps"></a>Következő lépések

* Az ARM-sablon fejezeteinek leírását az [ARM-sablonok szerkezetének és szintaxisának megismerését](template-syntax.md)ismertető cikk tartalmazza.
