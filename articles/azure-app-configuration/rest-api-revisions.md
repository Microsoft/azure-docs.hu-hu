---
title: Azure app Configuration REST API – kulcs-érték változatok
description: A kulcs-érték változatokkal való munkavégzésre szolgáló lapok az Azure-alkalmazás konfigurációjának használatával REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cfa117d1ed017170c279b7c4e0a146ae4edac108
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932472"
---
# <a name="key-value-revisions"></a>Kulcs-érték változatok

A *kulcs-érték változat* a kulcs-érték erőforrás korábbi megjelenítését határozza meg. A változatok érvényessége 7 nap után lejár az ingyenes, a standard szintű áruházak esetében pedig 30 nap. A változatok támogatják a `List` műveletet.

Az összes művelet esetében ``key`` egy opcionális paraméter. Ha nincs megadva, az azt jelenti, hogy az egyik kulcs.

Az összes művelet esetében ``label`` egy opcionális paraméter. Ha nincs megadva, az azt jelenti, hogy bármilyen címkét tartalmaz.

Ez a cikk a 1,0-es API-verzióra vonatkozik.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-revisions"></a>Változatok listázása

```http
GET /revisions?label=*&api-version={api-version} HTTP/1.1
```

**Válaszok**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8"
Accept-Ranges: items
```

```json
{
    "items": [
        {
          "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
          "key": "{key}",
          "label": "{label}",
          "content_type": null,
          "value": "example value",
          "last_modified": "2017-12-05T02:41:26.4874615+00:00",
          "tags": []
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>Oldalra tördelés

Ha a visszaadott elemek száma meghaladja a válasz korlátot, a rendszer oldalszámozást jelenít meg. Kövesse a választható ``Link`` Válasz fejlécét, és használja ``rel="next"`` a navigáláshoz. Azt is megteheti, hogy a tartalom egy következő hivatkozást biztosít a ``@nextLink`` tulajdonság formájában.

```http
GET /revisions?api-version={api-version} HTTP/1.1
```

**Válasz**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
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

## <a name="list-subset-of-revisions"></a>Változatok részhalmaza

Használja a `Range` kérelem fejlécét. A válasz tartalmaz egy `Content-Range` fejlécet. Ha a kiszolgáló nem tudja kielégíteni a kért tartományt, a a HTTP `416` () protokollal válaszol `RangeNotSatisfiable` .

```http
GET /revisions?api-version={api-version} HTTP/1.1
Range: items=0-2
```

**Válasz**

```http
HTTP/1.1 206 Partial Content
Content-Type: application/vnd.microsoft.appconfig.revs+json; charset=utf-8
Content-Range: items 0-2/80
```

## <a name="filtering"></a>Szűrés

A és a `key` szűrés kombinációja `label` támogatott.
Használja az opcionális `key` és a `label` lekérdezési karakterlánc paramétereit.

```http
GET /revisions?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>Támogatott szűrők

|Kulcs szűrője|Hatás|
|--|--|
|`key` nincs megadva vagy `key=*`|Megfelel **bármely** kulcsnak|
|`key=abc`|Az **ABC** nevű kulcsra illeszkedik.|
|`key=abc*`|Az **ABC** -vel kezdődő kulcsok neveinek felel meg|
|`key=*abc`|Az **ABC** -végződésű kulcsok neveinek felel meg|
|`key=*abc*`|Az **ABC** -t tartalmazó kulcsok neveinek felel meg|
|`key=abc,xyz`|Az **ABC** vagy az **XYZ** kulcsok neveinek felel meg (legfeljebb 5 CSV)|

|Címke szűrője|Hatás|
|--|--|
|`label` nincs megadva vagy `label=`|A bejegyzéshez címke nélkül illeszkedik|
|`label=*`|**Bármely** címkére illeszkedik|
|`label=prod`|Megfelel a címkének ( **Prod** )|
|`label=prod*`|A **Prod** kifejezéssel kezdődő címkékre illeszkedik|
|`label=*prod`|A **Prod** kifejezéssel záró címkékre illeszkedik|
|`label=*prod*`|A **gyártó** címkét tartalmazó címkékre illeszkedik|
|`label=prod,test`|A következő **címkékkel** egyezik **meg (legfeljebb** 5 CSV)|

### <a name="reserved-characters"></a>Fenntartott karakterek

A fenntartott karakterek a következők:

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
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

### <a name="examples"></a>Példák

- Összes

    ```http
    GET /revisions
    ```

- Azok az elemek, amelyekben a kulcs neve **ABC**-val kezdődik:

    ```http
    GET /revisions?key=abc*&api-version={api-version}
    ```

- **Olyan elemek**, amelyekben a kulcsnév **ABC** vagy **XYZ**, a címkék pedig a következők:

    ```http
    GET /revisions?key=abc,xyz&label=*prod*&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>Kérelmek adott mezői

Használja az opcionális `$select` lekérdezési karakterlánc paramétert, és adja meg a kért mezők vesszővel tagolt listáját. Ha a `$select` paraméter nincs megadva, a válasz tartalmazza az alapértelmezett készletet.

```http
GET /revisions?$select=value,label,last_modified&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>Időalapú hozzáférés

Az eredmény megjelenítésének beszerzése az elmúlt időpontban. További információkért lásd: [http-keretrendszer Time-Based erőforrás-állapotokhoz való hozzáféréshez – emlékeztető](https://tools.ietf.org/html/rfc7089#section-2.1), 2.1.1. szakasz.

```http
GET /revisions?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**Válasz**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.revs+json"
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
