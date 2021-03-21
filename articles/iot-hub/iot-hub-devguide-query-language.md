---
title: Az Azure IoT Hub lekérdezési nyelv ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az SQL-szerű IoT Hub lekérdezési nyelv leírása, amely az eszköz/modul ikrek és feladatok információinak lekérésére szolgál az IoT hub-ból.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: robinsh
ms.custom: devx-track-csharp
ms.openlocfilehash: cae2bcb1a3302814a426fa0cb2dfb36ba1b013fa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218366"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-lekérdezési nyelv az ikereszközökhöz - és modulokhoz, feladatokhoz, valamint az üzenetirányításhoz

A IoT Hub hatékony, SQL-szerű nyelvet biztosít az [eszközökhöz](iot-hub-devguide-device-twins.md), az [ikrekhez](iot-hub-devguide-module-twins.md), a [feladatokhoz](iot-hub-devguide-jobs.md)és az [üzenet-útválasztáshoz](iot-hub-devguide-messages-d2c.md)kapcsolódó információk lekéréséhez. Ez a cikk a következőket mutatja be:

* A IoT Hub lekérdezési nyelv főbb funkcióinak bemutatása, és
* A nyelv részletes leírása. Az üzenet-útválasztás lekérdezési nyelvével kapcsolatos részletekért lásd: [lekérdezések az üzenet-útválasztásban](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Eszközök és modulok kettős lekérdezései

Az ikrek és a [modulok ikrek](iot-hub-devguide-module-twins.md) tetszőleges [JSON-objektumokat](iot-hub-devguide-device-twins.md) tartalmazhatnak címkék és tulajdonságok használatával. A IoT Hub lehetővé teszi, hogy az ikrek és a modul Twins-t egyetlen JSON-dokumentumként kérdezze le, amely tartalmazza az összes Twin-információt.

Tegyük fel például, hogy az IoT hub-eszköz ikrek a következő struktúrával rendelkeznek (a modul Twin-modulja egy további moduleId hasonlóan fog megjelenni):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Eszközök kettős lekérdezései

A **IoT hub eszközként** szolgáló dokumentum-gyűjteményként teszi elérhetővé az eszközt az ikrek számára. Például a következő lekérdezés a teljes eszköz-készletet kérdezi le:

```sql
SELECT * FROM devices
```

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k támogatják a nagyméretű eredmények lapozását.

IoT Hub lehetővé teszi, hogy tetszőleges feltételekkel lekérje az eszközök ikrek általi szűrését. Például az olyan eszközök fogadására, ahol a **Location. region** **címke a következő lekérdezés használatára van** beállítva:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

A logikai operátorok és a aritmetikai összehasonlítások is támogatottak. Például a következő lekérdezéssel kérheti le az USA-ban található, az telemetria-nál kisebb, a percenkénti küldésre konfigurált eszközök ikreket:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Kényelmi szempontból az is lehetséges, hogy tömbbeli konstansokat is használhat a **in** és a **Nin** (nem a) operátorral. A Wi-Fi vagy a vezetékes kapcsolat jelentésének lekéréséhez például használja a következő lekérdezést:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Gyakran szükséges azonosítani az összes olyan eszközt, amely egy adott tulajdonságot tartalmaz. IoT Hub támogatja a függvényt `is_defined()` erre a célra. Ha például a tulajdonságot meghatározó eszköz-ikreket szeretné lekérni, `connectivity` használja a következő lekérdezést:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

A szűrési képességek teljes hivatkozását a [WHERE záradék](iot-hub-devguide-query-language.md#where-clause) című szakaszban találja.

A Csoportosítás és összesítések is támogatottak. Például az egyes telemetria-konfigurációs állapotú eszközök számának megkereséséhez használja a következő lekérdezést:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Ez a csoportosítási lekérdezés az alábbi példához hasonló eredményt ad vissza:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

Ebben a példában három eszköz sikeresen jelentett konfigurációt, a kettő továbbra is alkalmazza a konfigurációt, és egy hibát jelzett.

A kivetítési lekérdezések lehetővé teszik a fejlesztők számára, hogy csak azokat a tulajdonságokat adják vissza, amelyek érdeklik. Például az összes leválasztott eszköz utolsó tevékenységi idejének lekéréséhez használja a következő lekérdezést:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Modul – két lekérdezés

Az ikrek modul lekérdezése hasonló az eszközön található ikrek lekérdezéséhez, de egy másik gyűjteményt vagy névteret használ. az **eszközök** helyett az eszközökről kérdez le lekérdezést **. modulok**:

```sql
SELECT * FROM devices.modules
```

Nem engedélyezzük az eszközök és az eszközök közötti csatlakozást. modulok gyűjteménye. Ha az összes eszközön le szeretné kérdezni a modult, azt a címkék alapján teheti meg. Ez a lekérdezés az összes olyan modult visszaküldi, amely az összes eszközre kiterjed a vizsgálat állapotával:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Ez a lekérdezés az összes olyan modult visszaadja, amely a vizsgálat állapottal rendelkezik, de csak az eszközök megadott részhalmazán:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C# példa

A lekérdezési funkciót a [C# Service SDK](iot-hub-devguide-sdks.md) teszi elérhetővé a **RegistryManager** osztályban.

Íme egy példa egy egyszerű lekérdezésre:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

A **lekérdezési** objektum egy Oldalméret (legfeljebb 100) létrehozásával lett létrehozva. Ezután a **GetNextAsTwinAsync** metódusok többszöri meghívásával több oldal is beolvasható.

A lekérdezési objektum több **következő** értéket tesz elérhetővé a lekérdezés által igényelt deszerializálási beállítástól függően. Például a Device Twin vagy a Job Objects vagy a Plain JSON a vetítések használatakor.

### <a name="nodejs-example"></a>Node.js példa

A lekérdezési funkciót az [Azure IoT Service SDK](iot-hub-devguide-sdks.md) teszi elérhetővé Node.jsszámára a **beállításjegyzék** -objektumban.

Íme egy példa egy egyszerű lekérdezésre:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

A **lekérdezési** objektum egy Oldalméret (legfeljebb 100) létrehozásával lett létrehozva. Ezután a **nextAsTwin** metódus többszöri meghívásával több oldal is beolvasható.

A lekérdezési objektum több **következő** értéket tesz elérhetővé a lekérdezés által igényelt deszerializálási beállítástól függően. Például a Device Twin vagy a Job Objects vagy a Plain JSON a vetítések használatakor.

### <a name="limitations"></a>Korlátozások

> [!IMPORTANT]
> A lekérdezési eredmények néhány percet is igénybe vehetnek az eszközökre vonatkozó legfrissebb értékekkel kapcsolatban. Ha az egyedi eszközökről származó ikreket az azonosító alapján kérdezi le, használja a [Get twin REST API](/java/api/com.microsoft.azure.sdk.iot.device.devicetwin). Ez az API mindig a legújabb értékeket adja vissza, és magasabb szabályozási korlátokat tartalmaz. A REST API közvetlenül, vagy az egyik [Azure IoT hub Service SDK](iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)-ban is használhatja az egyenértékű funkciókat.

Az összehasonlítások jelenleg csak az egyszerű típusok (nincsenek objektumok) között támogatottak, például `... WHERE properties.desired.config = properties.reported.config` csak akkor támogatott, ha ezek a tulajdonságok primitív értékeket tartalmaznak.

## <a name="get-started-with-jobs-queries"></a>Ismerkedés a feladatok lekérdezésekkel

A [feladatok](iot-hub-devguide-jobs.md) eszközök készletén végeznek műveleteket. Minden egyes eszköz tartalmazza azokat a feladatokat, amelyek a **feladatok** nevű gyűjtemény részét képezik.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Ez a gyűjtemény jelenleg a IoT Hub lekérdezési nyelvben **Devices.Jobs** kérdezhető le.

> [!IMPORTANT]
> Jelenleg a feladatok tulajdonságot a rendszer soha nem adja vissza az eszközökhöz tartozó ikrek lekérdezése során. Ez a "FROM Devices" karaktert tartalmazó lekérdezések. A feladatok tulajdonság csak a használatával érhető el közvetlenül a lekérdezésekkel `FROM devices.jobs` .
>
>

Ha például az összes olyan feladatot le szeretné kérni (múlt és ütemezett), amely egyetlen eszközt érint, a következő lekérdezést használhatja:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Vegye figyelembe, hogy ez a lekérdezés a visszaadott feladatok adott eszközre vonatkozó állapotát (és valószínűleg a közvetlen metódus válaszát) is megadja.

A **Devices.Jobs** -gyűjtemény összes objektumának tulajdonságai esetében is tetszőleges logikai feltételekkel lehet szűrni.

Például a következő lekérdezéssel kérheti le az összes befejezett eszköz két frissítési feladatát, amelyek egy adott eszköz szeptember 2016-án lettek létrehozva:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Egy adott feladatokhoz tartozó eszközönkénti eredményeket is lekérheti.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Korlátozások

A **Devices.Jobs** -lekérdezések jelenleg nem támogatják a következőket:

* A kivetítések ezért csak `SELECT *` lehetségesek.
* Azok a feltételek, amelyek a feladatok tulajdonságai mellett a Twin eszközre hivatkoznak (lásd az előző szakaszt).
* Összesítések végrehajtása, például darabszám, átlag, csoportosítás:.

## <a name="basics-of-an-iot-hub-query"></a>IoT Hub lekérdezés alapjai

Minden IoT Hub lekérdezés SELECT és FROM záradékból áll, opcionális WHERE és GROUP BY záradékkal. Minden lekérdezés JSON-dokumentumok gyűjteményén fut, például az eszközök ikrek. A FROM záradék jelzi a dokumentum azon gyűjteményét, amelyet meg kell ismételni (**eszközök**, **eszközök. modulok** vagy **Devices.Jobs**). Ezután alkalmazza a szűrőt a WHERE záradékban. Az összesítésekkel a lépés eredményei a GROUP BY záradékban megadott módon vannak csoportosítva. Az egyes csoportok esetében a SELECT záradékban megadott sorok jönnek létre.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>FROM záradék

A **FROM <from_specification>** záradék csak három értéket vehet igénybe: **eszközökről** lekérdezni az eszközről az ikreket, **az eszközökről. moduloktól** az ikrek lekérdezéséhez, vagy **a Devices.Jobs** -ből a lekérdezési feladatokhoz.

## <a name="where-clause"></a>WHERE záradék

A **where <filter_condition>** záradék megadása nem kötelező. Egy vagy több olyan feltételt határoz meg, amelyet a FROM gyűjteményben található JSON-dokumentumoknak meg kell felelniük az eredmény részeként. Minden JSON-dokumentumnak a megadott feltételeket "true" értékre kell kiértékelnie az eredménybe való felvételhez.

Az engedélyezett feltételek a [kifejezések és kikötések](iot-hub-devguide-query-language.md#expressions-and-conditions)szakaszban olvashatók.

## <a name="select-clause"></a>SELECT záradék

A **SELECT <select_list>** kötelező megadni, és meghatározza, hogy milyen értékeket kér le a rendszer a lekérdezésből. Meghatározza az új JSON-objektumok létrehozásához használandó JSON-értékeket.
A FROM gyűjtemény szűrt (és opcionálisan csoportosított) részhalmazának minden eleméhez a vetítési fázis új JSON-objektumot hoz létre. Ez az objektum a SELECT záradékban megadott értékekkel lett létrehozva.

A SELECT záradék nyelvtana a következő:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** a from gyűjtemény JSON-dokumentumának bármely tulajdonságára vonatkozik. Néhány példa a SELECT záradékokra az első lépések az eszköz kettős lekérdezésekkel című szakaszban található.

Jelenleg a **Select*** utasítástól eltérő kiválasztási záradékok csak az eszközön található összesített lekérdezésekben támogatottak.

## <a name="group-by-clause"></a>GROUP BY záradék

A **Group BY <group_specification>** záradék egy opcionális lépés, amely a WHERE záradékban megadott szűrő után és a SELECT utasításban megadott leképezés előtt fut. Egy attribútum értéke alapján csoportosítja a dokumentumokat. Ezek a csoportok a SELECT záradékban megadott összesített értékek létrehozására szolgálnak.

Példa a GROUP BY használatával végzett lekérdezésre:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

A GROUP BY kifejezés formális szintaxisa a következő:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** a from gyűjtemény JSON-dokumentumának bármely tulajdonságára vonatkozik.

A GROUP BY záradék jelenleg csak az eszközök ikrek lekérdezése esetén támogatott.

> [!IMPORTANT]
> A kifejezés `group` jelenleg speciális kulcsszóként van kezelve a lekérdezésekben. Abban az esetben, ha `group` a tulajdonság nevét használja, érdemes megfontolnia, hogy dupla zárójelek között legyen szó a hibák elkerüléséről, például: `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'` .
>

## <a name="expressions-and-conditions"></a>Kifejezések és kikötések

Magas szintű *kifejezés*:

* Kiértékeli a JSON-típus egy példányát (például logikai, szám, karakterlánc, tömb vagy objektum).
* Az az eszköz JSON-dokumentumában és a beépített operátorok és függvények használatával történő folyamatos adatkezeléssel van meghatározva.

A *feltételek* olyan kifejezések, amelyek kiértékelik a logikai értéket. **A logikai** értékektől eltérő állandók **hamisnak** minősülnek. Ez a szabály **Null**, nem **definiált**, minden objektum vagy tömb példányát, bármely karakterláncot és a logikai **hamis** értéket tartalmazza.

A kifejezések szintaxisa a következő:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Ha meg szeretné tudni, hogy a kifejezések szintaxisa milyen szimbólumokat képvisel, tekintse meg a következő táblázatot:

| Szimbólum | Definíció |
| --- | --- |
| attribute_name | A **from** gyűjtemény JSON-dokumentumának bármely tulajdonsága. |
| binary_operator | A [operátorok](#operators) szakaszban felsorolt bináris operátorok. |
| function_name| A [függvények](#functions) szakaszban felsorolt függvények. |
| decimal_literal |Decimális jelöléssel kifejezett lebegőpontos. |
| hexadecimal_literal |A (z) "0x" karakterláncban kifejezett szám, amelyet hexadecimális számjegyek karakterlánca követ. |
| string_literal |A karakterlánc-literálok olyan Unicode-karakterláncok, amelyek nulla vagy több Unicode-karakter vagy Escape-kódrészletek sorozatából állnak. A karakterlánc-literálok szimpla idézőjelek vagy idézőjelek közé vannak lefoglalva. Engedélyezett Escapes: `\'` ,,, `\"` `\\` , `\uXXXX` 4 hexadecimális számjegyek által definiált Unicode-karakterek esetén. |

### <a name="operators"></a>Operátorok

A következő operátorok támogatottak:

| Family (Család) | Operátorok |
| --- | --- |
| Aritmetikai |+, -, *, /, % |
| Logikai |ÉS, VAGY NEM |
| Összehasonlítás |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions

Az ikrek és a feladatok lekérdezése az egyetlen támogatott függvény:

| Függvény | Leírás |
| -------- | ----------- |
| IS_DEFINED (tulajdonság) | Egy logikai értéket ad vissza, amely azt jelzi, hogy a tulajdonsághoz hozzá van-e rendelve érték (beleértve a `null` következőt) |

Az útvonalakra vonatkozó feltételekben a következő matematikai függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| ABS (x) | A megadott numerikus kifejezés abszolút (pozitív) értékét adja vissza. |
| EXP (x) | A megadott numerikus kifejezés exponenciális értékét adja vissza (e ^ x). |
| TELJESÍTMÉNY (x, y) | A megadott kifejezés értékét adja vissza a megadott hatványra (x ^ y).|
| NÉGYZET (x)    | A megadott numerikus érték négyzetét adja vissza. |
| FELSŐ határ (x) | A megadott numerikus kifejezésnél nagyobb, vagy azzal egyenlő legkisebb egész értéket adja vissza. |
| EMELET (x) | A legnagyobb olyan egész számot adja vissza, amely kisebb vagy egyenlő, mint a megadott numerikus kifejezés. |
| ALÁÍRÁS (x) | A megadott numerikus kifejezés pozitív (+ 1), nulla (0) vagy negatív (-1) előjelét adja vissza.|
| SQRT (x) | A megadott numerikus érték szögletes gyökerét adja vissza. |

Az útvonalakra vonatkozó feltételekben a következő típusú ellenőrzési és öntési függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| AS_NUMBER | A bemeneti karakterláncot számmá alakítja. `noop` Ha a bemenet egy szám; `Undefined` Ha a sztring nem egy számot jelöl.|
| IS_ARRAY | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa tömb-e. |
| IS_BOOL | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa logikai. |
| IS_DEFINED | Egy logikai értéket ad vissza, amely azt jelzi, hogy a tulajdonsághoz hozzá van-e rendelve érték. Ez csak akkor támogatott, ha az érték egy egyszerű típus. Az egyszerű típusok például a következők: string, Boolean, numerikus vagy `null` . A DateTime, az Objektumtípusok és a tömbök nem támogatottak. |
| IS_NULL | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa NULL-e. |
| IS_NUMBER | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa szám-e. |
| IS_OBJECT | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa JSON-objektum-e. |
| IS_PRIMITIVE | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa primitív (karakterlánc, logikai, numerikus vagy `null` ). |
| IS_STRING | Egy logikai értéket ad vissza, amely azt jelzi, hogy a megadott kifejezés típusa sztring-e. |

Az útvonalakra vonatkozó feltételekben a következő karakterlánc-függvények támogatottak:

| Függvény | Leírás |
| -------- | ----------- |
| CONCAt (x, y,...) | Egy olyan karakterláncot ad vissza, amely két vagy több karakterlánc-érték összefűzését eredményezi. |
| Hossz (x) | A megadott karakterlánc-kifejezés karaktereinek számát adja vissza.|
| ALSÓ (x) | Egy karakterlánc-kifejezést ad vissza, miután a nagybetűs karaktereket a kisbetűs értékre konvertálta. |
| FELSŐ (x) | Egy karakterlánc-kifejezést ad vissza a kisbetűs karakterek nagybetűssé alakítását követően. |
| Alkarakterlánc (karakterlánc, Kezdés [, hossz]) | Egy karakterlánc-kifejezés egy részét adja vissza, amely a megadott karakteres nulla alapú pozíciótól kezdődik, és a megadott hosszra, illetve a karakterlánc végére mutat. |
| INDEX_OF (karakterlánc, töredék) | A második karakterlánc-kifejezés első előfordulásának kezdő pozícióját adja vissza az első megadott karakterlánc-kifejezésen belül, vagy-1 értéket, ha a sztring nem található.|
| STARTS_WITH (x, y) | Olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc-kifejezés a másodikval kezdődik-e. |
| ENDS_WITH (x, y) | Egy olyan logikai érték beolvasása, amely azt jelzi, hogy az első karakterlánc kifejezése a másodperctel végződik-e |
| TARTALMAZZA (x, y) | Egy logikai értéket ad vissza, amely azt jelzi, hogy az első karakterlánc-kifejezés tartalmazza-e a másodpercet. |

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan futtathat lekérdezéseket az alkalmazásokban az [Azure IoT SDK](iot-hub-devguide-sdks.md)-k használatával.