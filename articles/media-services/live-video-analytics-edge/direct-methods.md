---
title: Közvetlen módszerek használata az élő videó-elemzésekben IoT Edge – Azure
description: A IoT Edge Live Video Analytics számos közvetlen módszert tesz elérhetővé. A közvetlen módszerek a jelen témakörben ismertetett konvenciók alapján működnek.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97656240"
---
# <a name="direct-methods"></a>Közvetlen metódusok

A IoT Edge Live Video Analytics számos közvetlen metódust tesz elérhetővé, amelyek IoT Hubból is meghívhatók. A közvetlen metódusok a kérés-válasz interakciót egy olyan eszközhöz hasonlítják, amely a HTTP-híváshoz hasonlóan sikeres vagy sikertelen (a felhasználó által megadott időtúllépés után). Ez a módszer olyan helyzetekben hasznos, amikor az azonnali művelet végrehajtása eltérő attól függően, hogy az eszköz válaszképes volt-e. További információ: [közvetlen metódusok megismerése és meghívása IoT hubból](../../iot-hub/iot-hub-devguide-direct-methods.md).

Ez a témakör ezeket a metódusokat és konvenciókat ismerteti.

## <a name="conventions"></a>Konvenciók

A közvetlen módszerek a következő egyezményeken alapulnak:

1. A közvetlen metódusok verziója a főverzióban van megadva. MÁSODLAGOS formátum (az alábbi példában látható módon):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. Az élő videó Analytics adott verziója IoT Edge modulban az aktuális verzióra vonatkozó összes közvetlen módszert támogatja. A modul 1,3-es verziója például a 1,3, 1,2, 1,1 és 1,0 verziókkal rendelkező közvetlen metódusokat fogja támogatni.
3. Minden közvetlen metódus szinkronban van.
4. Hiba eredménye a OData-hiba sémájának követése.
5. A névnek a következő korlátozásokat kell figyelembe vennie:
    
    * Csak alfanumerikus karakterek és kötőjelek, ha nem indul és végződik kötőjel
    * Nincsenek szóközök
    * Legfeljebb 32 karakter

### <a name="example"></a>Példa

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Legfelső szintű hibakódok     

|Állapot |Code   |Üzenet|
|---|---|---|
|400|   BadRequest| A kérelem érvénytelen.|
|400|   InvalidResource|    Az erőforrás érvénytelen|
|400|   InvalidVersion| Az API-verzió érvénytelen|
|402|   ConnectivityRequired    |Az Edge-modulhoz a Felhőbeli kapcsolat szükséges a megfelelő működéshez.|
|404|   NotFound    |Az erőforrás nem található|
|409|   Ütközés|   Művelet ütközése|
|500|   InternalServerError|    Belső kiszolgálóhiba|
|503|   ServerBusy| Kiszolgáló foglalt|

### <a name="detailed-error-codes"></a>Részletes hibakódok

Részletes érvényesítési hiba, például a Graph-modul érvényességi adatai a hiba részleteiként lesznek hozzáadva:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Állapot|    Részletes kód   |Leírás|
|---|---|---|
|400|   GraphValidationError|   Általános gráf-hibák, például ciklusok vagy particionálások stb.|
|400|   ModuleValidationError|  Modul-specifikus érvényesítési hibák.|
|409|   GraphTopologyInUse| A Graph-topológiát továbbra is egy vagy több Graph-példány hivatkozik.|
|409|   OperationNotAllowedInState| A kért művelet nem hajtható végre a jelenlegi állapotban.|
|409|   ResourceValidationError|    A hivatkozott erőforrás (például: eszköz) nem érvényes állapotban van.|

## <a name="details"></a>Részletek  

### <a name="graphtopologyget"></a>GraphTopologyGet

Ez a közvetlen metódus egyetlen gráf topológiát kérdez le.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| Entitás található | 200 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| Az entitás nem található | 404 |  |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Egyetlen topológiát hoz létre, ha a megadott névvel vagy frissítésekkel nem rendelkezik meglévővel, és már létezik ilyen nevű fájl.

Legfontosabb szempontok:

* A topológia szabadon frissíthető, mert nincs hivatkozva gráf.
* A topológia szabadon frissíthető, ha az összes hivatkozó gráf inaktiválva van, amíg:

    * Az újonnan hozzáadott paraméterek alapértelmezett értékei
    * Az eltávolított paramétereket egyetlen gráf sem hivatkozik
* A topológia frissítései nem engedélyezettek, ha vannak aktív diagramok

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Állapotkódok

|Feltétel  |Állapotkód    |Részletes hibakód|
|---|---|---|
Meglévő entitás frissítve |200|   N/A|
Új entitás létrehozva  |201|   N/A|
Általános felhasználói hibák |400 tartomány  ||
Gráf-érvényesítési hibák |400    |GraphValidationError|
Modul-ellenőrzési hibák|   400 |ModuleValidationError|
Általános kiszolgálói hibák   |500 tartomány  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Egyetlen gráf topológiát töröl.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| Entitás törölve | 200 | N/A |
| Az entitás nem található | 204 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| A Graph-topológiát egy vagy több gráf-példány hivatkozik | 409 | GraphTopologyInUse |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphtopologylist"></a>GraphTopologyList

A szűrési feltételeknek megfelelő gráf-topológiák listájának beolvasása.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Szűrő támogatása

|Művelet |Mező (k)    |Operátorok|
|---|---|---|
|$orderby|name  |ASC|


#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| Siker | 200 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Egyetlen gráf-példányt kér le:

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| Entitás található | 200 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| Az entitás nem található | 404 |  |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Egy gráf-példányt hoz létre, ha a megadott névvel vagy frissítésekkel nem rendelkezik meglévővel, és már létezik ilyen nevű.

Legfontosabb szempontok:

* A Graph-példány szabadon frissíthető a "deaktivált" állapotban.

    * A gráf minden frissítéskor újra érvényesítve van.
* A Graph-példány frissítései részben vannak korlátozva, amíg a gráf nem "inaktív" állapotban van.
* A Graph-példány frissítései nem engedélyezettek az aktív gráfokon.

#### <a name="request"></a>Kérés

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Reagálás

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| Meglévő entitás frissítve | 200 | N/A |
| Új entitás létrehozva | 201 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| Gráf-érvényesítési hibák | 400 | GraphValidationError |
| Modul-ellenőrzési hibák | 400 | ModuleValidationError |
| Erőforrás-ellenőrzési hibák | 409 | ResourceValidationError |
| Általános kiszolgálói hibák | 500 tartomány |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Egyetlen gráf-példányt töröl.

Legfontosabb szempontok:

* Csak inaktivált gráfokat lehet törölni.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| A gráf törlése sikerült | 200 | N/A |
| A gráf nem található | 204 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| A gráf nem a "leállítva" állapotban van | 409 | OperationNotAllowedInState |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphinstancelist"></a>GraphInstanceList

Ez hasonló a GraphTopologyList-hez. Lehetővé teszi a használatot a Graph-példányok enumerálásához.
Lekéri az összes olyan gráf-példány listáját, amely megfelel a szűrési feltételeknek.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Szűrő támogatása

|Művelet  |   Mező (k)|   Operátorok|
|---|---|---|
|$orderby|  name|   ASC|

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| Siker | 200 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Egyetlen gráf-példány aktiválása. 

Kulcsfontosságú szempontok

* A metódus csak akkor tér vissza, ha a gráf aktiválva van 
* A Graph az aktiválás során feltételezi az aktiválási állapotot.

    * A diagramon egy lista-vagy lekérési művelet a megfelelő állapotú gráfot adja vissza.
* Idempotencia

    * Az "aktiválás" állapotú gráf megkezdése ugyanúgy viselkedik, mintha a gráf inaktiválása megtörtént (azaz a hívás blokkolja a gráf aktiválása előtt).
    * Az "aktív" állapotú gráfok aktiválása azonnal sikeres.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Állapotkódok

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| A gráf sikeresen aktiválva | 200 | N/A |
| Új entitás létrehozva | 201 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| Modul-ellenőrzési hibák | 400 | ModuleValidationError |
| Erőforrás-ellenőrzési hibák | 409 | ResourceValidationError |
| A gráf inaktivált állapotban van | 409 | OperationNotAllowedInState |
| Általános kiszolgálói hibák | 500 tartomány |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Egy gráf-példány inaktiválása. Egy gráf inaktiválása szabályosan inaktiválja a média feldolgozását, és gondoskodik arról, hogy az Edge-ben tárolt összes esemény és média elkötelezett legyen a felhőben, ha van ilyen.

Legfontosabb szempontok:

* A metódus csak akkor tér vissza, ha a gráf inaktiválva van
* A Graph azt feltételezi, hogy inaktivált állapotban van az "inaktiválás" állapot.

    * A diagramon egy lista-vagy lekérési művelet a megfelelő állapotú gráfot adja vissza.
    * A Leállítás csak akkor fejeződik be, ha az összes adathordozó fel lett töltve a felhőbe.
* Idempotencia

    * Egy gráf inaktiválási állapotba való inaktiválása ugyanúgy viselkedik, mintha a gráf inaktiválása megtörtént (azaz a rendszer blokkolja a blokkokat a gráf inaktiválása előtt).
    * Az "inaktív" állapotú gráfok inaktiválása azonnal sikeres.

#### <a name="request"></a>Kérés

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Reagálás

```
{
    "status": 200,
    "payload": null
}
```

| Feltétel | Állapotkód | Részletes hibakód |
|--|--|--|
| A gráf sikeresen aktiválva | 200 | N/A |
| Új entitás létrehozva | 201 | N/A |
| Általános felhasználói hibák | 400 tartomány |  |
| A gráf aktiváló állapotban van | 409 | OperationNotAllowedInState |
| Általános kiszolgálói hibák | 500 tartomány |  |

## <a name="next-steps"></a>Következő lépések

[Ikermodul konfigurációs sémája](module-twin-configuration-schema.md)
