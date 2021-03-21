---
title: Egyéni erőforrás-gyorsítótár – referencia
description: Egyéni erőforrás-gyorsítótár-referenciák az Azure egyéni erőforrás-szolgáltatói számára. Ez a cikk végigvezeti azon végpontok követelményein, amelyek a gyorsítótár egyéni erőforrásait implementálják.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "75650382"
---
# <a name="custom-resource-cache-reference"></a>Egyéni erőforrás-gyorsítótár referenciája

Ez a cikk végigvezeti azon végpontok követelményein, amelyek a gyorsítótár egyéni erőforrásait implementálják. Ha nem ismeri az Azure egyéni erőforrás-szolgáltatóit, tekintse [meg az egyéni erőforrás-szolgáltatók áttekintése](overview.md)című témakört.

## <a name="how-to-define-a-cache-resource-endpoint"></a>Gyorsítótár-erőforrás végpontjának definiálása

A proxy-erőforrások létrehozásához meg kell adni a **routingType** a "proxy, cache" értékre.

Példa egyéni erőforrás-szolgáltatóra:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy, Cache",
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

A "proxy, gyorsítótár" erőforrás- **végpontot** implementáló **végpontnak** az új API-ra vonatkozó kérést és választ kell kezelnie az Azure-ban. Ebben az esetben a **resourceType** új Azure Resource API-t fog kiszolgálni a `PUT` , a és a `GET` `DELETE` szifilisz egyetlen erőforráson való végrehajtásához, valamint az `GET` összes meglévő erőforrás lekéréséhez:

> [!NOTE]
> Az Azure API létrehozza a kérések metódusait, a `PUT` `GET` és `DELETE` a-t, de a gyorsítótár- **végpontnak** csak a és a kell kezelnie `PUT` `DELETE` .
> Azt javasoljuk, hogy a **végpont** is implementálja `GET` .

### <a name="create-a-custom-resource"></a>Egyéni erőforrás létrehozása

Azure API bejövő kérelem:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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
- Az egyéni erőforrás-szolgáltató felülírja `name` a `type` kéréshez tartozó, és `id` mezőket.
- Az egyéni erőforrás-szolgáltató csak a `properties` gyorsítótár-végpont objektumában lévő mezőket fogja visszaadni.

**Végpont** Válasz

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

A `name` , a `id` és a `type` mezők automatikusan létrejönnek az egyéni erőforráshoz az egyéni erőforrás-szolgáltató alapján.

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

- Egy érvényes JSON-objektum dokumentuma. Az összes tömböt és karakterláncot egy felső objektum alá kell ágyazni.
- A `Content-Type` fejlécet az "Application/JSON;" értékre kell beállítani. charset = UTF-8 ".
- Az Azure egyéni erőforrás-szolgáltató csak akkor távolítja el az elemet a gyorsítótárból, ha 200 szintű választ ad vissza. Akkor is, ha az erőforrás nem létezik, a **végpontnak** 204 értéket kell visszaadnia.

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

A kérés **nem** lesz továbbítva a **végpontnak**.

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

A rendszer **nem** továbbítja a kérést a **végpontnak**.

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
- [Hivatkozás: egyéni erőforrás-proxy referenciája](proxy-resource-endpoint-reference.md)
