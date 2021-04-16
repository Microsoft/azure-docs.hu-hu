---
title: Bicep-fájlstruktúra és szintaxis
description: Deklaratív szintaxissal ismerteti a Bicep-fájlok szerkezetét és tulajdonságait.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 1b8eddd388878be8f653f963ef967cf2c0af685f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537854"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>A Bicep-fájlok struktúrája és szintaxisa

Ez a cikk a Bicep-fájlok struktúráját ismerteti. Bemutatja a fájl különböző szakaszait és a szakaszokban elérhető tulajdonságokat.

Ez a cikk olyan felhasználóknak van szánva, akik ismerik a Bicep-fájlokat. Részletes információkat nyújt a sablon struktúrájáról. A Bicep-fájlok létrehozásának folyamatán végigvezető részletes oktatóanyagért lásd: Oktatóanyag: Első [bicep-fájl](bicep-tutorial-create-first-bicep.md)létrehozása Azure Resource Manager telepítése.

## <a name="template-format"></a>Sablon formátuma

A Bicep-fájlok a következő elemekből áll. Az elemek bármilyen sorrendben megjelenhetnek.

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

Az alábbi példa ezeknek az elemeknek az implementációját mutatja be.

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

## <a name="target-scope"></a>Célhatókör

Alapértelmezés szerint a célhatókör a következőre van `resourceGroup` beállítva: . Ha az üzembe helyezés az erőforráscsoport szintjén történik, nem kell beállítania a célhatókört a Bicep-fájlban.

Az engedélyezett értékek a következőek:

* **resourceGroup** – alapértelmezett érték, amely [erőforráscsoportok üzembe helyezéséhez használatos.](deploy-to-resource-group.md)
* **subscription** – előfizetések [üzembe helyezéséhez használatos.](deploy-to-subscription.md)
* **managementGroup** – felügyeleti [csoportok központi telepítéséhez használatos.](deploy-to-management-group.md)
* **tenant** – bérlői [üzemelő példányok esetén használatos.](deploy-to-tenant.md)

## <a name="parameters"></a>Paraméterek

Olyan értékekhez használjon paramétereket, amelyek eltérőek lehetnek a különböző üzemelő példányok esetén. Megadhatja az alapértelmezett értéket a paraméterhez, ha az üzembe helyezés során nem ad meg értéket.

Hozzáadhat például egy termékváltozat-paramétert, amely különböző méreteket ad meg egy erőforráshoz. Az alapértelmezett érték létrehozásához sablonfunkciókat használhat, például lekértheti az erőforráscsoport helyét.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Az elérhető adattípusokkal kapcsolatos információkért lásd: [Adattípusok sablonokban.](data-types.md)

További információ: [Paraméterek a sablonokban.](template-parameters.md)

## <a name="parameter-decorators"></a>Paraméterkorrekátorok

Minden paraméterhez hozzáadhat egy vagy több dekorátort. Ezek a dekorátorok határozzák meg a paraméter számára engedélyezett értékeket. Az alábbi példa a Bicep-fájlon keresztül üzembe helyezhető SKUS-okat adja meg.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Az alábbi táblázat ismerteti az elérhető dekorátorokat és azok használatát.

| Dekoratőr | Alkalmazás | Argumentum | Leírás |
| --------- | ---- | ----------- | ------- |
| futhat a háttérben | összes | array | A paraméter megengedett értékei. Ezzel a dekorátorral biztosíthatja, hogy a felhasználó a megfelelő értékeket ad-e meg. |
| leírás | összes | sztring | A paraméter használatát magyarázó szöveg. A leírás a portálon keresztül jelenik meg a felhasználók számára. |
| Maxlength | tömb, sztring | int | A sztring- és tömbparaméterek maximális hossza. Az érték befogadó. |
| maxValue (maximális érték) | int | int | Az egész szám paraméter maximális értéke. Ez az érték befogadó. |
| metaadatok | összes | object | A paraméterre alkalmazandó egyéni tulajdonságok. A leírás-dekorátornak megfelelő description tulajdonságot is tartalmazhat. |
| Minlength | tömb, sztring | int | A sztring- és tömbparaméterek minimális hossza. Az érték befogadó. |
| minValue (minimális érték) | int | int | Az egész szám paraméter minimális értéke. Ez az érték befogadó. |
| Biztonságos | sztring, objektum | Nincs | Biztonságosként jelöli meg a paramétert. A biztonságos paraméterek értékét a rendszer nem menti az üzembehelyhely helyezési előzményekbe, és nem naplózza. További információ: [Secure strings and objects (Biztonságos sztringek és objektumok).](data-types.md#secure-strings-and-objects) |

## <a name="variables"></a>Változók

Használjon változókat a Bicep-fájlokban ismétlődő összetett kifejezésekhez. Hozzáadhat például egy változót egy erőforrásnévhez, amely több érték összeadása alapján áll össze.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

A változókhoz [nem kell](data-types.md) adattípust megadni. Ehelyett az adattípus az értékből van kikövetkeztetve.

További információ: [Változók a sablonokban.](template-variables.md)

## <a name="resource"></a>Erőforrás

Az `resource` kulcsszóval határozzon meg egy üzembe helyezni kívánt erőforrást. Az erőforrás-deklaráció tartalmazza az erőforrás szimbolikus nevét. Ezt a szimbolikus nevet fogja használni a Bicep-fájl más részeiben, ha értéket kell kapnia az erőforrásból.

Az erőforrás-deklaráció az erőforrástípust és az API-verziót is tartalmazza.

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

Az erőforrás-deklarációban meg kell adnia az erőforrástípus tulajdonságait. Ezek a tulajdonságok minden erőforrástípus egyediek.

További információ: [Erőforrás-deklaráció a sablonokban.](resource-declaration.md)

Egy [erőforrás feltételes üzembe helyezéséhez adjon](conditional-resource-deployment.md)hozzá egy `if` kifejezést.

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

Egy [erőforrástípus több példányának](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) üzembe helyezéséhez adjon hozzá egy `for` kifejezést. A kifejezés iterálhat egy tömb tagjain.

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

A modulok használatával hivatkozhatja az újból fel használni kívánt kódot tartalmazó más Bicep-fájlokra. A modul egy vagy több üzembe helyezni kívánt erőforrást tartalmaz. Ezek az erőforrások a Bicep-fájlban található egyéb erőforrásokkal együtt vannak telepítve.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

A szimbolikus név lehetővé teszi, hogy a modulra a fájl egy másik elemében hivatkozhat. Például lekért egy kimeneti értéket egy modulból a szimbolikus név és a kimeneti érték nevének használatával.

Az erőforrásokhoz hasonló módon feltételesen vagy iteratív módon is üzembe helyezhet egy modult. A szintaxis megegyezik a modulok és az erőforrások szintaxisával.

További információ: [Bicep-modulok használata.](bicep-modules.md)

## <a name="resource-and-module-decorators"></a>Erőforrás- és modulkorrekulátorok

Decoratort hozzáadhat egy erőforrás- vagy moduldefinícióhoz. Az egyetlen támogatott dekorátor a `batchSize(int)` . Csak kifejezéseket használó erőforrásra vagy moduldefinícióra `for` alkalmazhatja.

Alapértelmezés szerint az erőforrások párhuzamosan vannak üzembe állítva. Nem tudja, hogy milyen sorrendben érik el a befejezést. A dekorátor `batchSize` hozzáadásakor a példányokat sorosan kell üzembe helyezni. Az egész szám argumentummal adhatja meg a párhuzamosan üzembe helyező példányok számát.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

További információ: [Serial (Soros) vagy Parallel (Párhuzamos).](copy-resources.md#serial-or-parallel)

## <a name="outputs"></a>Kimenetek

A kimenetek használatával értéket ad vissza az üzemelő példányból. Általában akkor ad vissza értéket egy üzembe helyezett erőforrásból, ha azt egy másik művelethez újra fel kell használnia.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Adjon meg [egy adattípust](data-types.md) a kimeneti értékhez.

További információ: [Kimenetek sablonokban.](template-outputs.md)

## <a name="comments"></a>Megjegyzések

`//`Egysoros megjegyzésekhez vagy `/* ... */` többsoros megjegyzésekhez használható

Az alábbi példa egy egysoros megjegyzést mutat be.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

Az alábbi példa egy többsoros megjegyzést mutat be.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Többsoros sztringek

Egy sztringet több sorra is fel lehet tördörni. Használjon három egyszeres idézőjelet a többsoros sztring kezdő és záró `'''` karaktereként.

A többsoros sztringen belüli karakterek kezelése a ként van kezelve. A escape-karakterek szükségtelenek. A többsoros sztringbe `'''` nem foglalhatja bele a karakterláncot. A sztring-interpoláció jelenleg nem támogatott.

Elindíthatja a sztringet közvetlenül a megnyitás `'''` után, vagy be is foglalhat egy új sort. Az eredményül kapott sztring mindkét esetben nem tartalmaz új sort. A Bicep-fájl sorvégeitől függően az új sorok a vagy a formátumban vannak `\r\n` `\n` értelmezve.

Az alábbi példa egy többsoros sztringet mutat be.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Az előző példa az alábbi JSON-nak felel meg.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Következő lépések

A Bicep bemutatása: Mi az a [Bicep?](bicep-overview.md).
