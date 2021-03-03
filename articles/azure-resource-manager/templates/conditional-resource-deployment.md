---
title: Feltételes üzembe helyezés sablonokkal
description: Útmutató az erőforrásoknak egy Azure Resource Manager sablonban (ARM-sablon) való feltételes üzembe helyezéséhez.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 409d258d7dfe3ed186e5cf97cc0dbe6dc149b849
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741174"
---
# <a name="conditional-deployment-in-arm-templates"></a>Feltételes üzembe helyezés ARM-sablonokban

Néha szükség van egy erőforrás üzembe helyezésére egy Azure Resource Manager sablonban (ARM-sablon) vagy a bicep-fájlban. JSON-sablonok esetén használja az `condition` elemet annak megadásához, hogy az erőforrás telepítve van-e. A bicep `if` értéknél használja a kulcsszót annak megadásához, hogy az erőforrás telepítve van-e. A feltétel értéke TRUE (igaz) vagy FALSE (hamis) értékre lesz feloldva. Ha az érték TRUE (igaz), a rendszer létrehozza az erőforrást. Ha az érték false (hamis), az erőforrás nincs létrehozva. Az értéket csak a teljes erőforrásra lehet alkalmazni.

> [!NOTE]
> A feltételes üzembe helyezés nem a [gyermek erőforrásaira](child-resource-name-type.md)van kaszkád. Ha az erőforrást és annak alárendelt erőforrásait feltételesen szeretné üzembe helyezni, akkor minden egyes erőforrástípus esetében ugyanazt a feltételt kell alkalmaznia.

## <a name="deploy-condition"></a>Üzembe helyezési feltétel

Egy paraméter értékét átadhatja, amely jelzi, hogy az adott erőforrás telepítve van-e. A következő példa feltételesen helyez üzembe egy DNS-zónát.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployZone": {
      "type": "bool"
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[parameters('deployZone')]",
      "type": "Microsoft.Network/dnsZones",
      "apiVersion": "2018-05-01",
      "name": "myZone",
      "location": "global"
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

---

Összetettebb példákat az [Azure SQL logikai kiszolgáló](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)című témakörben talál.

## <a name="new-or-existing-resource"></a>Új vagy meglévő erőforrás

A feltételes telepítés használatával létrehozhat egy új erőforrást, vagy használhat egy meglévőt is. Az alábbi példa bemutatja, hogyan helyezhet üzembe egy új Storage-fiókot, vagy hogyan használhat meglévő Storage-fiókot.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "newOrExisting": {
      "type": "string",
      "defaultValue": "new",
      "allowedValues": [
        "new",
        "existing"
      ]
    }
  },
  "functions": [],
  "resources": [
    {
      "condition": "[equals(parameters('newOrExisting'), 'new')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageAccountName string
param location string = resourceGroup().location

@allowed([
  'new'
  'existing'
])
param newOrExisting string = 'new'

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

Ha a paraméter `newOrExisting` **új** értékre van állítva, a feltétel igaz értéket ad vissza. A Storage-fiók telepítve van. Ha azonban a értéke `newOrExisting` **meglévő**, akkor a feltétel hamis értékre van állítva, és a Storage-fiók nincs telepítve.

A elemet használó teljes példaként `condition` tekintse meg a [virtuális gép új vagy meglévő Virtual Network, tárterületet és nyilvános IP-címet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="runtime-functions"></a>Futásidejű függvények

Ha a [hivatkozás](template-functions-resource.md#reference) vagy a [lista](template-functions-resource.md#list) függvényt egy feltételesen telepített erőforrással használja, akkor a függvény akkor is ki lesz értékelve, ha az erőforrás nincs telepítve. Hibaüzenet jelenik meg, ha a függvény olyan erőforrásra hivatkozik, amely nem létezik.

Az [IF](template-functions-logical.md#if) függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor feltételként van kiértékelve. Tekintse meg az [IF függvényt](template-functions-logical.md#if) egy olyan sablon esetében, amely a `if` és `reference` egy feltételesen telepített erőforrást használ.

Egy erőforrást egy feltételes erőforrástól [függőként](define-resource-dependency.md) kell beállítania, pontosan úgy, ahogy bármely más erőforrás. Ha egy feltételes erőforrás nincs telepítve, Azure Resource Manager automatikusan eltávolítja a szükséges függőségekről.

## <a name="complete-mode"></a>Teljes mód

Ha [teljes móddal](deployment-modes.md) rendelkező sablont telepít, és az erőforrás nincs telepítve, mert a `condition` kiértékelése hamis értéket ad vissza, akkor az eredmény attól függ, hogy REST API milyen verziót használ a sablon üzembe helyezéséhez. Ha 2019-05-10-nál korábbi verziót használ, az erőforrás **nem törlődik**. A 2019-05-10-es vagy újabb verziókban az erőforrás **törölve lesz**. A Azure PowerShell és az Azure CLI legújabb verziói törlik az erőforrást, ha a feltétel hamis.

## <a name="next-steps"></a>Következő lépések

* A feltételes üzembe helyezést lefedi Microsoft Learn modul esetében lásd: [összetett Felhőbeli központi telepítések kezelése speciális ARM-sablonok használatával](/learn/modules/manage-deployments-advanced-arm-template-features/).
* A sablonok létrehozásával kapcsolatos javaslatokért lásd: [ARM-sablon – ajánlott eljárások](template-best-practices.md).
* Egy erőforrás több példányának létrehozásához tekintse [meg az erőforrás-iteráció az ARM-sablonokban](copy-resources.md)című témakört.
