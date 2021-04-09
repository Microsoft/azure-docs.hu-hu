---
title: Azure app Configuration REST API – kulcsok
description: A kulcsok Azure-alkalmazás-konfigurációval való használatának REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932540"
---
# <a name="keys"></a>Kulcsok

API-Version: 1,0

A következő szintaxis a kulcs erőforrását jelöli:

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Üzemeltetés

A legfontosabb erőforrások a következő műveletet támogatják:

- Lista

Az összes művelethez `name` egy opcionális szűrő paraméter. Ha nincs megadva, az azt jelenti, hogy *az egyik* kulcs.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>Kulcsok listázása

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Oldalra tördelés

Ha a visszaadott elemek száma meghaladja a válasz korlátot, a rendszer oldalszámozást jelenít meg. Kövesse a választható `Link` válaszok fejléceit, és használja `rel="next"` a navigáláshoz. Azt is megteheti, hogy a tartalom egy következő hivatkozást biztosít a `@nextLink` tulajdonság formájában. A következő hivatkozás `api-version` paramétert tartalmaz.

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**Válasz**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>Szűrés

A szűrés a használatával ```name``` támogatott.

```http
GET /keys?name={key-name}&api-version={api-version}
```

A következő szűrők támogatottak:

|Kulcs szűrője|Hatás|
|--|--|
|`name` nincs megadva vagy `name=*`|Megfelel **bármely** kulcsnak|
|`name=abc`|Az **ABC** nevű kulcsra illeszkedik.|
|`name=abc*`|Az **ABC** -vel kezdődő kulcsok neveinek felel meg|
|`name=abc,xyz`|Megfelel az **ABC** vagy az **XYZ** kulcsnévnek (legfeljebb 5 CSV)|

A következő karakterek vannak fenntartva: `*` , `\` , `,`

Ha egy foglalt karakter az érték része, akkor azt a használatával kell megmenekülnie `\{Reserved Character}` . A nem fenntartott karaktereket is el lehet menekülni.

## <a name="filter-validation"></a>Szűrés ellenőrzése

Szűrő-ellenőrzési hiba esetén a válasz HTTP- `400` hiba részletei:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>Példák

- Mind

    ```http
    GET /keys?api-version={api-version}
    ```

- A kulcs neve **ABC** -vel kezdődik

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- A kulcs neve **ABC** vagy **XYZ**

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Kérelmek adott mezői

Használja az opcionális `$select` lekérdezési karakterlánc paramétert, és adja meg a kért mezők vesszővel tagolt listáját. Ha a `$select` paraméter nincs megadva, a válasz tartalmazza az alapértelmezett készletet.

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based hozzáférés

Az eredmény megjelenítésének beszerzése az elmúlt időpontban. Lásd: [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) . szakasz

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Válasz**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
