---
title: Azure-alkalmazás konfigurációs REST API – Címkék
description: A címkék Azure-alkalmazás-konfigurációval való használatának REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932506"
---
# <a name="labels"></a>Címkék

API-Version: 1,0

A **label** erőforrás a következőképpen van definiálva:

```json
{
      "name": [string]             // Name of the label
}
```

A a következő műveleteket támogatja:

- Lista

Az összes művelethez ``name`` egy opcionális szűrő paraméter. Ha nincs megadva, az azt jelenti, hogy bármilyen címkét **tartalmaz** .

## <a name="prerequisites"></a>Előfeltételek

- Az összes HTTP-kérelmet hitelesíteni kell. Lásd: [hitelesítés](./rest-api-authentication-index.md) szakasz.
- Az összes HTTP-kérelemnek explicit módon kell megadnia `api-version` . Lásd: [verziószámozási](./rest-api-versioning.md) szakasz.

## <a name="list-labels"></a>Címkék listázása

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Oldalra tördelés

Ha a visszaadott elemek száma meghaladja a válasz korlátot, a rendszer oldalszámozást jelenít meg. Kövesse a választható `Link` válaszok fejléceit, és használja `rel="next"` a navigáláshoz. Azt is megteheti, hogy a tartalom egy következő hivatkozást tartalmaz a `@nextLink` tulajdonság formájában. A következő hivatkozás `api-version` paramétert tartalmaz.

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**Válasz**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
Accept-Ranges: items
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

A szűrés a használatával `name` támogatott.

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>Támogatott szűrők

|Kulcs szűrője|Hatás|
|--|--|
|`name` nincs megadva vagy `name=*`|**Bármely** címkére illeszkedik|
|`name=abc`|Megfelel egy **ABC** nevű címkének|
|`name=abc*`|Az **ABC** -vel kezdődő feliratok neveinek egyezése|
|`name=abc,xyz`|Megfelel az **ABC** vagy az **XYZ** címke nevének (legfeljebb 5 CSV)|

### <a name="reserved-characters"></a>Fenntartott karakterek

`*`, `\`, `,`

Ha egy foglalt karakter az érték része, akkor azt a használatával kell megmenekülnie `\{Reserved Character}` . A nem fenntartott karaktereket is el lehet menekülni.

### <a name="filter-validation"></a>Szűrés ellenőrzése

Ha szűrő-érvényesítési hiba történik, a válasz HTTP- `400` hiba részletei:

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

### <a name="examples"></a>Példák

- Mind

    ```http
    GET /labels?api-version={api-version}
    ```

- A címke neve **ABC** -vel kezdődik

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- A címke neve **ABC** vagy **XYZ**

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Kérelmek adott mezői

Használja az opcionális `$select` lekérdezési karakterlánc paramétert, és adja meg a kért mezők vesszővel tagolt listáját. Ha a `$select` paraméter nincs megadva, a válasz tartalmazza az alapértelmezett készletet.

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Time-Based hozzáférés

Az eredmény megjelenítésének beszerzése az elmúlt időpontban. Lásd: [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) . szakasz

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Válasz**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
