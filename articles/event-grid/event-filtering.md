---
title: Azure Event Grid eseményeinek szűrése
description: Ismerteti, hogyan szűrhetők az események Azure Event Grid előfizetés létrehozásakor.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: fa63296f97bfa888cb0f425d0c03a5e4a7e46525
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419847"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid-előfizetések esemény-szűrésének ismertetése

Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel szűrheti, hogy mely eseményeket küldi a rendszer a végpontnak. Egy esemény-előfizetés létrehozásakor három lehetőség van a szűrésre:

* Eseménytípusok
* A tárgy kezdete vagy vége
* Speciális mezők és operátorok

## <a name="event-type-filtering"></a>Eseménytípus szűrése

Alapértelmezés szerint az eseményforrás összes [eseménytípus](event-schema.md) küldése a végpontnak történik. Dönthet úgy is, hogy csak bizonyos eseménytípus küldését küldi el a végpontnak. Például értesítést kaphat az erőforrásairól, de nem kap értesítést más műveletekhez, például törlésekhez. Ebben az esetben az eseménytípus alapján szűrhet `Microsoft.Resources.ResourceWriteSuccess` . Adjon meg egy tömböt az eseménytípus közül, vagy adja meg az eseményforrás `All` összes eseménytípus beolvasását.

Az Eseménytípus szerinti szűrés JSON-szintaxisa a következő:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Tulajdonos szűrése

Egyszerű szűréshez a tárgy mezőben meg kell adni a tulajdonos kezdő vagy záró értékét. Megadhatja például, hogy a tárgy vége legyen, `.txt` hogy csak a szövegfájlok Storage-fiókba való feltöltésével kapcsolatos események legyenek lekérdezve. Másik lehetőségként szűrheti a tulajdonost, `/blobServices/default/containers/testcontainer` hogy lekérje a tároló összes eseményét, de a Storage-fiókban nem található más tároló.

Az események egyéni témakörökbe való közzétételekor olyan témákat hozhat létre az eseményekhez, amelyek megkönnyítik az előfizetők számára, hogy megismerjék, hogy érdeklik-e az esemény. Az előfizetők a tárgy tulajdonságot használják az események szűrésére és irányítására. Vegye fontolóra azt az elérési utat, ahol az esemény megtörtént, így az előfizetők az elérési út szegmensei alapján szűrhetik. Az elérési út lehetővé teszi az előfizetők számára az események szűk vagy széles körű szűrését. Ha három szegmens elérési utat ad meg `/A/B/C` , mint a tárgy, az előfizetők az első szegmens alapján szűrhetik az `/A` események széles körét. Ezek az előfizetők olyan eseményeket kapnak, mint a `/A/B/C` vagy a `/A/D/E` . Más előfizetők is szűrhetik a `/A/B` t, hogy Szűkítse az események szűk körét.

A következő JSON-szintaxis a tárgy szerinti szűréshez:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Speciális szűrés

Az adatmezőkben lévő értékek alapján történő szűréshez és az összehasonlító operátor megadásához használja a speciális szűrési lehetőséget. A speciális szűrés területen a következőket kell megadnia:

* operátor típusa – az összehasonlítás típusa.
* kulcs – a szűréshez használt esemény adatai mezője. Ez lehet egy szám, egy logikai, egy karakterlánc vagy egy tömb.
* értékek – a kulcshoz összehasonlítandó érték vagy értékek.

## <a name="key"></a>Kulcs
A kulcs a szűréshez használt esemény adatai mezője. A következő típusok egyike lehet:

- Szám
- Logikai érték
- Sztring
- Tömb. A `enableAdvancedFilteringOnArrays` funkció használatához a tulajdonságot True értékre kell állítani. A Azure Portal jelenleg nem támogatja a funkció engedélyezését. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

A **Event Grid sémában** lévő eseményekhez használja a következő értékeket a kulcshoz:,,,, `ID` `Topic` `Subject` `EventType` `DataVersion` vagy eseményvezérelt adatok (például `data.key1` ).

A **Felhőbeli események sémájában** lévő eseményekhez használja a következő értékeket a kulcshoz:,,, `eventid` `source` `eventtype` `eventtypeversion` vagy eseményvezérelt adatok (például `data.key1` ).

**Egyéni bemeneti séma** esetén használja az esemény adatmezőit (például `data.key1` ). Az adat szakasz mezőinek eléréséhez használja a `.` (dot) jelölést. Például a `data.sitename` `data.appEventTypeDetail.action` `sitename` `action` következő minta eseményhez való hozzáféréshez vagy az alkalmazáshoz.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Értékek
Az értékek a következőket okozhatják: number, string, Boolean vagy Array.

## <a name="operators"></a>Operátorok

A **számok** számára elérhető operátorok a következők:

## <a name="numberin"></a>NumberIn
A NumberIn operátor igaz értéket ad vissza, ha a **kulcs** értéke a megadott **szűrési** értékek egyike. A következő példában ellenőrzi, hogy a `counter` szakaszban az attribútum értéke `data` 5 vagy 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a, b, c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
A NumberNotIn igaz értéket ad vissza, ha a **kulcs** értéke **nem** a megadott **szűrési** értékek egyike. A következő példában ellenőrzi, hogy a `counter` szakasz attribútumának értéke `data` nem 41 és 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a, b, c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
A NumberLessThan operátor igaz értéket ad vissza, ha a **kulcs** értéke **kisebb** a megadott **szűrő** értéknél. A következő példában ellenőrzi, hogy a `counter` szakasz attribútumának értéke kisebb-e, `data` mint 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrő értékével. Itt látható a következő kulcsot tartalmazó pszeudo-kód: `[v1, v2, v3]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
A NumberGreaterThan operátor igaz értéket ad vissza, ha a **kulcs** értéke **nagyobb** a megadott **szűrő** értéknél. A következő példában ellenőrzi, hogy a szakaszban szereplő attribútum értéke nagyobb-e, `counter` `data` mint 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrő értékével. Itt látható a következő kulcsot tartalmazó pszeudo-kód: `[v1, v2, v3]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
A NumberLessThanOrEquals operátor igaz értéket ad vissza, ha a **kulcs** értéke **kisebb vagy egyenlő, mint** a megadott **szűrő** értéke. A következő példában ellenőrzi, hogy a `counter` szakasz attribútumának értéke `data` 100-nál kisebb vagy egyenlő-e. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrő értékével. Itt látható a következő kulcsot tartalmazó pszeudo-kód: `[v1, v2, v3]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
A NumberGreaterThanOrEquals operátor igaz értéket ad vissza, ha a **kulcs** értéke **nagyobb vagy egyenlő, mint** a megadott **szűrő** értéke. A következő példában ellenőrzi, hogy a `counter` szakasz attribútumának értéke `data` nagyobb vagy egyenlő-e, mint 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrő értékével. Itt látható a következő kulcsot tartalmazó pszeudo-kód: `[v1, v2, v3]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
A NumberInRange operátor igaz értéket ad vissza, ha a **kulcs** értéke a megadott **szűrési tartományok** egyikében van. Az alábbi példában ellenőrzi, hogy a `key1` szakasz attribútumának értéke a következő `data` két tartomány egyikében van-e: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

A `values` tulajdonság a tartományok tömbje. Az előző példában ez két tartomány tömbje. Az alábbi példa egy olyan tömböt mutat be, amelynek egy tartománya van. 

**Tömb egyetlen tartománnyal:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a következő pszeudo-kód: `[v1, v2, v3]` és a szűrő: tartományok tömbje. Ebben az ál-kódban `a` a `b` tömb minden tartományának alsó és felső értéke. A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
A NumberNotInRange operátor igaz értéket ad vissza, ha a **kulcs** értéke **nem** szerepel a megadott **szűrési tartományokban**. Az alábbi példában ellenőrzi, hogy a `key1` szakasz attribútumának értéke a következő `data` két tartomány egyikében van-e: 3,14159-999,95, 3000-4000. Ha igen, az operátor hamis értéket ad vissza. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
A `values` tulajdonság a tartományok tömbje. Az előző példában ez két tartomány tömbje. Az alábbi példa egy olyan tömböt mutat be, amelynek egy tartománya van.

**Tömb egyetlen tartománnyal:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a következő pszeudo-kód: `[v1, v2, v3]` és a szűrő: tartományok tömbje. Ebben az ál-kódban `a` a `b` tömb minden tartományának alsó és felső értéke. A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


A **logikai értékek** számára elérhető operátor a következő: 

## <a name="boolequals"></a>BoolEquals
A BoolEquals operátor igaz értéket ad vissza, ha a **kulcs** értéke a megadott logikai érték **szűrő**. A következő példában ellenőrzi, hogy a `isEnabled` `data` szakaszban található attribútum értéke `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Ha a kulcs egy tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a logikai érték szűrésével. Itt látható a következő kulcsot tartalmazó pszeudo-kód: `[v1, v2, v3]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

A **karakterláncok** elérhető operátorai a következők:

## <a name="stringcontains"></a>StringContains
A **StringContains** igaz értéket ad vissza, ha a **kulcs** értéke a megadott **szűrési** értékek bármelyikét **tartalmazza** (alkarakterláncok). A következő példában ellenőrzi, hogy a `key1` szakasz attribútumának értéke tartalmazza-e a `data` megadott alsztringek valamelyikét: `microsoft` vagy `azure` . Például `azure data factory` `azure` :. 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
A **StringNotContains** operátor igaz értéket ad vissza, ha  a kulcs **nem tartalmazza** a megadott **szűrési** értékeket alkarakterláncként. Ha a kulcs egy megadott értéket tartalmaz egy alsztringként, az operátor hamis értéket ad vissza. A következő példában az operátor csak akkor ad vissza igaz értéket, ha a `key1` szakasz attribútumának értéke `data` nem `contoso` és `fabrikam` alsztring. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
A kezelő aktuális korlátozásával kapcsolatban lásd: [korlátozások](#limitations) szakasz.

## <a name="stringbeginswith"></a>StringBeginsWith
A **StringBeginsWith** operátor igaz értéket ad vissza, ha a **kulcs** értéke a megadott **szűrési** értékek bármelyikével **kezdődik** . A következő példában ellenőrzi, hogy a szakaszban szereplő attribútum értéke a vagy a karakterrel kezdődik-e `key1` `data` `event` `grid` . Például a következővel `event hubs` kezdődik: `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
A **StringNotBeginsWith** operátor igaz értéket ad vissza, ha a **kulcs** értéke **nem** a megadott **szűrési** értékekkel kezdődik. A következő példában ellenőrzi, hogy a szakaszban szereplő attribútum értéke nem a vagy a értékkel kezdődik-e `key1` `data` `event` `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
A **StringEndsWith** operátor igaz értéket ad vissza, ha a **kulcs** értéke a megadott **szűrési** értékek egyikével **végződik** . A következő példában ellenőrzi, hogy a `key1` szakasz attribútumának értéke a vagy a értékre `data` végződik- `jpg` e `jpeg` `png` . Például a következővel `eventgrid.png` végződik: `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
A **StringNotEndsWith** operátor igaz értéket ad vissza, ha a **kulcs** értéke **nem** a megadott **szűrési** értékek bármelyikével végződik. A következő példában ellenőrzi, hogy a `key1` szakasz attribútumának értéke nem a vagy a értékre `data` végződik- `jpg` e `jpeg` `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
A **StringIn** operátor ellenőrzi, hogy a **kulcs** értéke **pontosan egyezik** -e a megadott **szűrési** értékek egyikével. A következő példában ellenőrzi, hogy a `key1` `data` szakaszban található attribútum értéke `exact` vagy `string` vagy `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
A **StringNotIn** operátor ellenőrzi, hogy a **kulcs** értéke **nem egyezik** -e a megadott **szűrési** értékek egyikével sem. A következő példában ellenőrzi, hogy a `key1` szakaszban található attribútum értéke `data` nem `aws` és `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Ha a kulcs tömb, a rendszer a tömbben lévő összes értéket ellenőrzi a szűrési értékek tömbje alapján. Itt látható a (z) kulcsot tartalmazó pszeudo `[v1, v2, v3]` -kód: és a szűrő: `[a,b,c]` . A rendszer figyelmen kívül hagyja a szűrő adattípusának megfelelő adattípusú kulcs-értékeket.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Az összes karakterlánc-összehasonlítás nem különbözteti meg a kis-és nagybetűket.

> [!NOTE]
> Ha a JSON-esemény nem tartalmazza a speciális szűrő kulcsát, a szűrő a következő operátorok esetében **nem felel** meg: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, NumberIn, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, StringIn. evaulated.
> 
>A szűrő a következő operátoroknak **megfelelően evaulated** : NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
A IsNullOrUndefined operátor igaz értéket ad vissza, ha a kulcs értéke NULL vagy nincs meghatározva. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

A következő példában a key1 hiányzik, így az operátor igaz értékre értékeli. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

A következő példában a key1 NULL értékre van állítva, így az operátor igaz értékre lesz kiértékelve.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Ha a key1 bármilyen más érték szerepel ezekben a példákban, az operátor kiértékeli a hamis értéket. 

## <a name="isnotnull"></a>IsNotNull
A IsNotNull operátor igaz értéket ad vissza, ha a kulcs értéke nem NULL vagy nincs meghatározva. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>VAGY és és
Ha több értékkel rendelkező egyetlen szűrőt ad meg, a rendszer egy **vagy** műveletet hajt végre, így a Key mező értékének az alábbi értékek egyikének kell lennie. Alább bemutatunk egy példát:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Ha több különböző szűrőt ad meg, a **és** a művelet befejeződött, így minden szűrési feltételnek teljesülnie kell. Bemutatunk egy példát: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
A **CloudEvents sémában** lévő eseményekhez használja a következő értékeket a kulcshoz: `eventid` ,,, `source` `eventtype` `eventtypeversion` vagy eseményvezérelt adatok (például `data.key1` ). 

[A bővítmény környezeti attribútumait is használhatja a CloudEvents 1,0-ben](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). A következő példában a `comexampleextension1` és a `comexampleothervalue` kiterjesztési környezeti attribútumok. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Íme egy példa egy bővítmény környezeti attribútumának egy szűrőben való használatára.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Korlátozások

A speciális szűrés a következő korlátozásokkal rendelkezik:

* 5 speciális szűrő és 25 szűrő érték az összes szűrő/Event Grid-előfizetés alapján
* 512 karakter/karakterlánc érték
* Öt érték a **-ben** és **nem az** operátorokban
* Az `StringNotContains` operátor jelenleg nem érhető el a portálon.
* Kulcsok **`.` (pont)** karakterrel. Például: `http://schemas.microsoft.com/claims/authnclassreference` vagy `john.doe@contoso.com` . Jelenleg nem támogatottak az Escape-karakterek a kulcsokban. 

Ugyanaz a kulcs több szűrőben is használható.





## <a name="next-steps"></a>Következő lépések

* Az események PowerShell-lel és az Azure CLI-vel való szűrésével kapcsolatos további tudnivalókért lásd: [Események szűrése Event Grid](how-to-filter-events.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
