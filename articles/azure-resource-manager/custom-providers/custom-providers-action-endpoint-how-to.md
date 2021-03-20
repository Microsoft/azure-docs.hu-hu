---
title: Egyéni műveletek hozzáadása az Azure REST APIhoz
description: Ismerje meg, hogyan adhat hozzá egyéni műveleteket az Azure REST APIhoz. Ez a cikk végigvezeti azon végpontok követelményein és ajánlott eljárásain, amelyek egyéni műveleteket kívánnak megvalósítani.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650395"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Egyéni műveletek hozzáadása az Azure REST APIhoz

Ez a cikk az egyéni műveleteket megvalósító Azure-beli egyéni erőforrás-szolgáltatói végpontok létrehozásának követelményeit és ajánlott eljárásait ismerteti. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatóit, tekintse [meg az egyéni erőforrás-szolgáltatók áttekintése](overview.md)című témakört.

## <a name="how-to-define-an-action-endpoint"></a>Műveleti végpont definiálása

A **végpont** olyan URL-cím, amely egy szolgáltatásra mutat, amely megvalósítja az alapul szolgáló szerződést az IT és az Azure között. A végpont az egyéni erőforrás-szolgáltatóban van definiálva, és bármely nyilvánosan elérhető URL-cím lehet. Az alábbi minta egy implementált nevű **műveletet** tartalmaz `myCustomAction` `endpointURL` .

Minta **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Műveleti végpont létrehozása

Egy **műveletet** végrehajtó **végpontnak** az új API-ra vonatkozó kérést és választ kell kezelnie az Azure-ban. Ha egy **művelettel** rendelkező egyéni erőforrás-szolgáltatót hoz létre, új API-készletet fog létrehozni az Azure-ban. Ebben az esetben a művelet egy új Azure Action API-t hoz majd a `POST` hívásokhoz:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API bejövő kérelem:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Ezt a kérést a rendszer a következő formában továbbítja a **végpontnak** :

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Hasonlóképpen a **végpont** válasza is vissza lesz továbbítva az ügyfélnek. A végpont válaszának vissza kell térnie:

- Egy érvényes JSON-objektum dokumentuma. Az összes tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet az "Application/JSON;" értékre kell beállítani. charset = UTF-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Egyéni művelet meghívása

Az egyéni műveletek egy egyéni erőforrás-szolgáltatón való meghívásának két fő módja van:

- Azure CLI
- Azure Resource Manager-sablonok

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Paraméter | Kötelező | Leírás
---|---|---
művelet | *igen* | A **ResourceProvider** definiált művelet neve.
azonosítók | *igen* | A **ResourceProvider** erőforrás-azonosítója.
kérelem – törzs | *nem* | A **végpontnak** küldendő kérelem törzse.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

> [!NOTE]
> A műveletek korlátozott támogatást Azure Resource Manager sablonokban. Ahhoz, hogy a művelet meghívható legyen egy sablonon belül, tartalmaznia kell az [`list`](../templates/template-functions-resource.md#list) előtagot a nevében.

Példa **ResourceProvider** a List művelettel:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Minta Azure Resource Manager sablon:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Paraméter | Kötelező | Leírás
---|---|---
resourceIdentifier | *igen* | A **ResourceProvider** erőforrás-azonosítója.
apiVersion | *igen* | Az erőforrás-futtatókörnyezet API-verziója. Ennek mindig "2018-09-01-Preview" értékűnek kell lennie.
functionValues | *nem* | A **végpontnak** küldendő kérelem törzse.

## <a name="next-steps"></a>Következő lépések

- [Az Azure egyéni erőforrás-szolgáltatóinak áttekintése](overview.md)
- [Rövid útmutató: Azure egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: egyéni erőforrások hozzáadása az Azure REST APIhoz](./custom-providers-resources-endpoint-how-to.md)
