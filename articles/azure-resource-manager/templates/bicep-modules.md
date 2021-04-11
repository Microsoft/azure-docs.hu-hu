---
title: Bicep modulok
description: Leírja, hogyan lehet definiálni és használni egy modult, és hogyan használhatók a modul-hatókörök.
ms.topic: conceptual
ms.date: 03/30/2021
ms.openlocfilehash: 6c325bbbe265e13241119761373985ca4552b158
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967880"
---
# <a name="use-bicep-modules-preview"></a>Bicep modulok használata (előzetes verzió)

A bicep lehetővé teszi egy összetett megoldás modulokra bontását. A bicep modul egy vagy több, együtt üzembe helyezhető erőforrás készlete. A modulok elvonták a nyers erőforrás deklarációjának összetett részleteit, ami növelheti az olvashatóságot. Újra felhasználhatja ezeket a modulokat, és megoszthatja azokat más személyekkel. A [sablon specifikációinak](./template-specs.md)együttes használata lehetővé teszi a modularitás és a kód újbóli használatának módját. A bicep modulok egyetlen ARM-sablonba vannak transpiled, [beágyazott sablonokkal](./linked-templates.md#nested-template) a telepítéshez. A bicep-ben a [_dependsOn_](./template-syntax.md#resources) automatikusan lesz kezelve.

Oktatóanyagért lásd [: oktatóanyag: a bicep modulok hozzáadása](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Modulok definiálása

Minden bicep-fájl felhasználható modulként. A modul csak paramétereket és kimeneteket tesz elérhetővé más bicep-fájlokként. Mindkét paraméter és kimenet nem kötelező.

A következő bicep-fájl közvetlenül üzembe helyezhető egy Storage-fiók létrehozásához, vagy modulként használható.  A következő szakasz bemutatja, hogyan használhatja a modulokat:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

A kimenet használatával értékeket adhat át a szülő bicep-fájloknak.

## <a name="consume-modules"></a>Modulok felhasználása

A modul _kulcsszó használatával használhat modulokat_ . A következő bicep-fájl telepíti a hivatkozott modul fájljában definiált erőforrást:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **modul**: kulcsszó.
- **szimbolikus név** (stgModule): a modul azonosítója.
- **modul-fájl**: ebben a példában a modul elérési útja relatív elérési úttal (./storageAccount.bicep) van megadva. A teljes munkafolyamaton belüli összes elérési utat meg kell adni a továbbítási perjel (/) címtár-elválasztó használatával, hogy biztosítsa a konzisztens fordítást a platformon. A Windows fordított perjel ( \\ ) karakter nem támogatott.
- Modul használatakor a **_Name (név_** ) tulajdonságot (storageDeploy) kell megadni. Amikor a bicep létrehozza az IL sablont, a program ezt a mezőt használja a beágyazott üzembe helyezési erőforrás neveként, amely a modulhoz jön létre:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Egy modul kimeneti értékének lekéréséhez a tulajdonság értékét a következő szintaxissal kell lekérni, például: `stgModule.outputs.storageEndpoint` Where a `stgModule` modul azonosítója.

## <a name="configure-module-scopes"></a>Modulok hatókörének konfigurálása

Amikor deklarál egy modult, megadhat egy _hatókör_ -tulajdonságot a modul üzembe helyezési hatókörének beállításához:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

A _hatókör_ tulajdonság kihagyható, ha a modul célként megadott hatóköre és a szülő célként megadott hatóköre azonos. Ha nincs megadva a hatókör tulajdonság, a rendszer a szülő célként megadott hatókörében telepíti a modult.

A következő bicep-fájl bemutatja, hogyan hozhat létre egy erőforráscsoportot, és hogyan helyezhet üzembe egy modult az erőforráscsoporthoz:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Következő lépések

- A következő témakörben talál útmutatást [: oktatóanyag: a bicep modulok hozzáadása](./bicep-tutorial-add-modules.md).
