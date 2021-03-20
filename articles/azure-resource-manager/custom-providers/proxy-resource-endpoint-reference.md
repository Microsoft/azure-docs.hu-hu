---
title: Egyéni erőforrásproxy – referencia
description: Egyéni erőforrás-proxy referenciája az Azure egyéni erőforrás-szolgáltatóinak. Ez a cikk végigvezeti a proxy egyéni erőforrásainak megvalósításához szükséges végpontok követelményein.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650460"
---
# <a name="custom-resource-proxy-reference"></a>Egyéni erőforrás-proxy referenciája

Ez a cikk végigvezeti a proxy egyéni erőforrásainak megvalósításához szükséges végpontok követelményein. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatóit, tekintse [meg az egyéni erőforrás-szolgáltatók áttekintése](overview.md)című témakört.

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Proxy erőforrás-végpont meghatározása

A proxy-erőforrások létrehozásához meg kell adni a **routingType** a "proxy" értékre.

Példa egyéni erőforrás-szolgáltatóra:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Proxy erőforrás-végpont kiépítése

Egy "proxy" erőforrás- **végpontot** implementáló **végpontnak** az új API-ra vonatkozó kérést és választ kell kezelnie az Azure-ban. Ebben az esetben a **resourceType** új Azure Resource API-t fog kiszolgálni a `PUT` , a és a `GET` `DELETE` szifilisz egyetlen erőforráson való végrehajtásához, valamint az `GET` összes meglévő erőforrás lekéréséhez.

> [!NOTE]
> A `id` , a `name` és a `type` mezők nem szükségesek, de az egyéni erőforrásnak a meglévő Azure-ökoszisztémával való integrálásához szükséges.

Minta erőforrás:

``` JSON
{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Paraméter-hivatkozás:

Tulajdonság | Sample | Leírás
---|---|---
name | '{myCustomResourceName}' | Az egyéni erőforrás neve.
típus | "Microsoft. CustomProviders/resourceProviders/{resourceTypeName}" | Az erőforrástípus névtere.
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>szolgáltatók/Microsoft. CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | Az erőforrás-azonosító.

### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

Azure API bejövő kérelem:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Ezt a kérést a rendszer a következő formában továbbítja a **végpontnak** :

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Hasonlóképpen a **végpont** válasza is vissza lesz továbbítva az ügyfélnek. A végpont válaszának vissza kell térnie:

- Egy érvényes JSON-objektum dokumentuma. Az összes tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet az "Application/JSON;" értékre kell beállítani. charset = UTF-8 ".

**Végpont** Válasz

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Egyéni erőforrás eltávolítása

Azure API bejövő kérelem:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérést a rendszer a következő formában továbbítja a **végpontnak** :

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Hasonlóképpen a **végpont** válasza is vissza lesz továbbítva az ügyfélnek. A végpont válaszának vissza kell térnie:

- Érvényes JSON-objektum dokumentuma. Az összes tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet az "Application/JSON;" értékre kell beállítani. charset = UTF-8 ".

**Végpont** Válasz

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Egyéni erőforrás lekérése

Azure API bejövő kérelem:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérést a rendszer a következő formában továbbítja a **végpontnak** :

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Hasonlóképpen a **végpont** válasza is vissza lesz továbbítva az ügyfélnek. A végpont válaszának vissza kell térnie:

- Egy érvényes JSON-objektum dokumentuma. Az összes tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet az "Application/JSON;" értékre kell beállítani. charset = UTF-8 ".

**Végpont** Válasz

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Az összes egyéni erőforrás enumerálása

Azure API bejövő kérelem:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Ezt a kérést a rendszer a következő formában továbbítja a **végpontnak** :

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Hasonlóképpen a **végpont** válasza is vissza lesz továbbítva az ügyfélnek. A végpont válaszának vissza kell térnie:

- Egy érvényes JSON-objektum dokumentuma. Az összes tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet az "Application/JSON;" értékre kell beállítani. charset = UTF-8 ".
- Az erőforrások listáját a legfelső szintű tulajdonság alá kell helyezni `value` .

**Végpont** Válasz

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

Azure egyéni erőforrás-szolgáltató válasza:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure egyéni erőforrás-szolgáltatóinak áttekintése](overview.md)
- [Rövid útmutató: Azure egyéni erőforrás-szolgáltató létrehozása és egyéni erőforrások üzembe helyezése](./create-custom-provider.md)
- [Oktatóanyag: egyéni műveletek és erőforrások létrehozása az Azure-ban](./tutorial-get-started-with-custom-providers.md)
- [Útmutató: egyéni műveletek hozzáadása az Azure REST APIhoz](./custom-providers-action-endpoint-how-to.md)
- [Hivatkozás: az erőforrás-gyorsítótár egyéni leírása](proxy-cache-resource-endpoint-reference.md)
