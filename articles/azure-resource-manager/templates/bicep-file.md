---
title: A bicep fájl szerkezete és szintaxisa
description: Leírja egy bicep-fájl felépítését és tulajdonságait a deklaratív szintaxis használatával.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168688"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>A bicep-fájlok szerkezetének és szintaxisának megismerése

Ez a cikk a bicep-fájlok szerkezetét ismerteti. Bemutatja a fájl különböző részeit és az ezekben a részekben elérhető tulajdonságokat.

Ez a cikk olyan felhasználók számára készült, akik jól ismerik a bicep-fájlokat. Részletes információkat tartalmaz a sablon struktúrájáról. A bicep-fájlok létrehozásának folyamatán alapuló lépésenkénti oktatóanyagért lásd [: oktatóanyag: első Azure Resource Manager bicep-fájl létrehozása és központi telepítése](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Sablon formátuma

A bicep-fájlok a következő elemeket tartalmazza. Az elemek tetszőleges sorrendben megjelenhetnek.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

Az alábbi példa ezen elemek megvalósítását mutatja be.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Cél hatóköre

Alapértelmezés szerint a célként megadott hatókör a következő: `resourceGroup` . Ha az erőforráscsoport szintjén végzi az üzembe helyezést, nem kell beállítania a cél hatókört a bicep fájlban.

Az engedélyezett értékek a következők:

* **resourceGroup** – az [erőforráscsoport-telepítésekhez](deploy-to-resource-group.md)használt alapértelmezett érték.
* **előfizetés** – előfizetés- [telepítésekhez](deploy-to-subscription.md)használatos.
* **managementGroup** – [felügyeleti csoportok központi telepítéséhez](deploy-to-management-group.md)használatos.
* **bérlő** – [bérlői telepítésekhez](deploy-to-tenant.md)használatos.

## <a name="parameters"></a>Paraméterek

Paraméterek használata a különböző üzemelő példányokon eltérő értékekhez. Megadhat egy alapértelmezett értéket a paraméterhez, amelyet a rendszer akkor használ, ha nem ad meg értéket az üzembe helyezés során.

Előfordulhat például, hogy hozzáad egy SKU paramétert egy erőforrás különböző méretének megadásához. Az alapértelmezett érték létrehozásához használhatja a Template functions funkciót, például az erőforráscsoport helyének beolvasását.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Az elérhető adattípusok esetében lásd: [adattípusok a sablonokban](data-types.md).

További információ: [paraméterek a sablonokban](template-parameters.md).

## <a name="parameter-decorators"></a>Paraméter-dekorációk

Az egyes paraméterekhez hozzá lehet adni egy vagy több dekoratőr-t. Ezek a dekorációk határozzák meg a paraméter számára engedélyezett értékeket. A következő példa a bicep-fájllal üzembe helyezhető SKU-ket határozza meg.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

A következő táblázat ismerteti a rendelkezésre álló dekoratőr-ket és azok használatát.

| Decorator | Alkalmazás | Argumentum | Leírás |
| --------- | ---- | ----------- | ------- |
| futhat a háttérben | összes | array | A paraméter megengedett értékei. Ezzel a dekoratőr használatával gondoskodhat arról, hogy a felhasználó helyes értékeket biztosítson. |
| leírás | összes | sztring | A paraméter használatát elmagyarázó szöveg A leírás a portálon keresztül jelenik meg a felhasználók számára. |
| maxLength | tömb, karakterlánc | int | A karakterlánc és a tömb paramétereinek maximális hossza Az érték inkluzív. |
| maxValue | int | int | Az egész szám paraméter maximális értéke. Ez az érték inkluzív. |
| metaadatok | összes | object | A paraméterre alkalmazandó egyéni tulajdonságok. Tartalmazhat egy Description (Leírás) tulajdonságot, amely egyenértékű a leírási dekoratőr szolgáltatással. |
| minLength | tömb, karakterlánc | int | A karakterlánc és a tömb paramétereinek minimális hossza. Az érték inkluzív. |
| minValue | int | int | Az egész szám paraméter minimális értéke. Ez az érték inkluzív. |
| biztonságos | karakterlánc, objektum | Nincs | A paramétert biztonságosként jelöli meg. A biztonságos paraméterek értékét a rendszer nem menti a telepítési előzményekbe, és nincs naplózva. További információ: [biztonságos karakterláncok és objektumok](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Változók

Használjon változókat a bicep fájlban ismétlődő összetett kifejezésekhez. Például hozzáadhat egy változót egy olyan erőforrás neveként, amely több érték összefűzésével jön létre.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Nem adhat meg [adattípust](data-types.md) egy változóhoz. Ehelyett az adattípus az értékből van kikövetkeztetve.

További információ: [változók a sablonokban](template-variables.md).

## <a name="resource"></a>Erőforrás

A `resource` kulcsszó használatával határozza meg az üzembe helyezni kívánt erőforrást. Az erőforrás-deklaráció tartalmazza az erőforrás szimbolikus nevét. Ezt a szimbolikus nevet fogja használni a bicep-fájl más részeiben, ha értéket kell kapnia az erőforrásból.

Az erőforrás-deklaráció az erőforrás típusát és az API verzióját is tartalmazza.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

Az erőforrás-deklarációban az erőforrástípus tulajdonságait kell megadnia. Ezek a tulajdonságok minden erőforrástípus esetében egyediek.

További információ: [erőforrás-deklaráció a sablonokban](resource-declaration.md).

[Egy erőforrás feltételes üzembe helyezéséhez](conditional-resource-deployment.md)adjon hozzá egy `if` kifejezést.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

Egy erőforrástípus egynél [több példányának üzembe helyezéséhez](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) adjon hozzá egy `for` kifejezést. A kifejezés egy tömb tagjain keresztül is megismételhető.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Modulok

Modulok használata a használni kívánt kódokat tartalmazó más bicep-fájlokhoz való hivatkozáshoz. A modul egy vagy több telepítendő erőforrást tartalmaz. Ezek az erőforrások a bicep-fájlban található egyéb erőforrásokkal együtt települnek.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

A szimbolikus név lehetővé teszi a modul hivatkozását a fájlban lévő valahol máshol. Egy modulból például lekérhet egy kimeneti értéket a szimbolikus név és a kimeneti érték nevének használatával.

Az erőforrásokhoz hasonlóan feltételesen vagy iteratív üzembe helyezhet egy modult is. A szintaxis ugyanaz, mint a modulok erőforrásai.

További információ: a [bicep modulok használata](bicep-modules.md).

## <a name="resource-and-module-decorators"></a>Erőforrás-és modul-dekorációk

Felvehet egy dekoratőr-t egy erőforrás-vagy modul-definícióba. Az egyetlen támogatott dekoratőr `batchSize(int)` . Csak olyan erőforrás-vagy modul-definícióra alkalmazhat, amely kifejezést használ `for` .

Alapértelmezés szerint a rendszer párhuzamosan telepíti az erőforrásokat. Nem ismeri a befejezésük sorrendjét. A dekoratőr hozzáadásakor a `batchSize` példányok üzembe helyezése Az egész szám argumentum használatával adhatja meg a párhuzamosan telepítendő példányok számát.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

További információ: [soros vagy párhuzamos](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Kimenetek

A kimenetek használatával adja vissza az értéket az üzemelő példányból. Általában egy központilag telepített erőforrás értékét kell visszaadnia, ha egy másik művelethez újra kell használnia ezt az értéket.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Adja meg a kimeneti érték [adattípusát](data-types.md) .

További információ: [kimenetek a sablonokban](template-outputs.md).

## <a name="comments"></a>Megjegyzések

`//`Egysoros megjegyzésekhez vagy `/* ... */` Többsoros megjegyzésekhez használható

Az alábbi példa egy egysoros megjegyzést mutat be.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

A következő példában egy többsoros megjegyzés látható.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Többsoros karakterláncok

A sztringeket több sorba is lehet bontani. `'''`A többsoros karakterlánc elindításához és befejezéséhez használjon három szimpla idézőjelet. 

A többsoros karakterláncban szereplő karakterek a-as értékkel vannak kezelve. Az Escape-karakterek nem szükségesek. `'''`A többsoros karakterlánc nem vehető fel. A karakterlánc-interpoláció jelenleg nem támogatott.

A sztringet közvetlenül a megnyitást követően vagy új sorral is elindíthatja `'''` . Mindkét esetben az eredményül kapott karakterlánc nem tartalmaz új sort. A bicep-fájlban végződő soroktól függően az új vonalakat a vagy a rendszer értelmezi `\r\n` `\n` .

Az alábbi példa egy többsoros karakterláncot mutat be.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Az előző példa a következő JSON-val egyenértékű.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Következő lépések

A bicep a [Mi az a bicep?](bicep-overview.md)című témakörben talál bevezetést.
