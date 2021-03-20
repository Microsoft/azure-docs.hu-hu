---
title: JSON-és AVRO elemzése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan működik az összetett adattípusok, például a tömbök, a JSON és a CSV formátumú adat.
ms.service: stream-analytics
author: sidramadoss
ms.author: sidram
ms.topic: conceptual
ms.date: 01/29/2020
ms.custom: devx-track-js
ms.openlocfilehash: f6cb131fb3ff3cab4122aac5e1c6960dee4f8421
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012240"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>JSON-és Avro-adatelemzés Azure Stream Analytics

Azure Stream Analytics támogatja a CSV-, JSON-és Avro-adatformátumok feldolgozási eseményeit. A JSON-és a Avro-adatok is strukturálva lehetnek, és tartalmazhatnak olyan összetett típusokat, mint például a beágyazott objektumok (rekordok) és a tömbök. 

>[!NOTE]
>Az Event hub Capture által létrehozott AVRO-fájlok egy adott formátumot használnak, amelyhez az *Egyéni deszerializáló* funkció használata szükséges. További információ: a [bevitel bármilyen formátumban való olvasása a .NET-alapú egyéni deszerializálók használatával](./custom-deserializer-examples.md).
>
>Stream Analytics AVRO deszerializálása nem támogatja a leképezés típusát. Stream Analytics nem tudja beolvasni a EventHub-rögzítési blobokat, mert a EventHub rögzítés leképezést használ.


## <a name="record-data-types"></a>Adattípusok rögzítése
Az adattípusok a JSON-és Avro-tömböket jelölik, ha a bemeneti adatfolyamokban a megfelelő formátumok használatosak. Ezek a példák egy minta-érzékelőt mutatnak be, amely JSON formátumban olvassa be a bemeneti eseményeket. Íme egy példa egyetlen eseményre:

```json
{
    "DeviceId" : "12345",
    "Location" :
    {
        "Lat": 47,
        "Long": 122
    },
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```

### <a name="access-nested-fields-in-known-schema"></a>Beágyazott mezők elérése az ismert sémában
A beágyazott mezők közvetlenül a lekérdezésből való egyszerű eléréséhez használja a pont jelölését (.). Ez a lekérdezés például kiválasztja a szélességi és hosszúsági koordinátákat az előző JSON-adat Location tulajdonsága alatt. A dot jelöléssel több szint is navigálható az alább látható módon.

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

Az eredmény a következőket eredményezi:

|DeviceID|Lat|Hosszú|Hőmérséklet|Verzió|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>Az összes tulajdonság kijelölése
A beágyazott rekordok összes tulajdonságát "*" helyettesítő karakterrel is kiválaszthatja. Tekintse meg a következő példát:

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

Az eredmény a következőket eredményezi:

|DeviceID|Lat|Hosszú|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>Beágyazott mezők elérése, ha a tulajdonság neve változó

Használja a [GetRecordPropertyValue](/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics) függvényt, ha a tulajdonság neve változó. Ez lehetővé teszi a dinamikus lekérdezések létrehozását rögzítjük nélkül.

Tegyük fel például, hogy a minta adatfolyamot össze kell kapcsolni az egyes eszköz-érzékelők küszöbértékeit tartalmazó **hivatkozási adattal** . Alább láthatók a hivatkozási adatrészletek.

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

A cél az, hogy csatlakozzon a cikk elejéről származó minta-adatkészlethez, és az egyes érzékelők mértékéhez a küszöbérték felett adja ki az egyik eseményt. Ez azt jelenti, hogy a fenti egyetlen esemény több kimeneti eseményt is létrehozhat, ha több érzékelő is meghaladja a vonatkozó küszöbértékeket, a csatlakozásnak köszönhetően. Ha hasonló eredményeket szeretne elérni a csatlakozás nélkül, tekintse meg az alábbi szakaszt.

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

A **GetRecordPropertyValue** kiválasztja a tulajdonságot a *SensorReadings*-ben, amelynek a neve megegyezik a hivatkozási adatokból származó tulajdonság nevével. Ezután a rendszer kinyeri a *SensorReadings* társított értéket.

Az eredmény a következőket eredményezi:

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|Páratartalom|Riasztás: a küszöbérték feletti érzékelő|

### <a name="convert-record-fields-into-separate-events"></a>Rekord mezők konvertálása különálló eseményekre

A rekordok mezőinek különálló eseményekre való átalakításához használja az [Apply](/stream-analytics-query/apply-azure-stream-analytics) operátort a [GetRecordProperties](/stream-analytics-query/getrecordproperties-azure-stream-analytics) függvénnyel együtt.

Az eredeti mintaadatok alapján a következő lekérdezés használható a tulajdonságok különböző eseményekre való kinyeréséhez.

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

Az eredmény a következőket eredményezi:

|DeviceID|SensorName|AlertMessage|
|-|-|-|
|12345|Hőmérséklet|80|
|12345|Páratartalom|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[Object objektum]|

[A és a](/stream-analytics-query/with-azure-stream-analytics)használatával ezeket az eseményeket különböző célhelyekre lehet irányítani:

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>JSON-rekord elemzése az SQL Reference-adatokban
Ha a feladataiban a Azure SQL Database hivatkozási adatként használják, lehetséges, hogy van egy olyan oszlopa, amely JSON formátumú adatokkal rendelkezik. Erre mutat példát az alábbi ábra.

|DeviceID|Adatok|
|-|-|
|12345|{"Key": "érték1"}|
|54321|{"Key": "érték2"}|

*Az adatoszlop JSON* -rekordját egy egyszerű JavaScript felhasználó által definiált függvény írásával elemezheti.

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

Ezután létrehozhat egy lépést a Stream Analytics lekérdezésben az alább látható módon a JSON-rekordok mezőinek eléréséhez.

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>Tömb adattípusai

A tömb típusú adattípusok értékek rendezett gyűjteményei. A tömb értékeivel kapcsolatos néhány jellemző művelet alább látható. Ezek a példák a functions [GetArrayElement](/stream-analytics-query/getarrayelement-azure-stream-analytics), a [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics), a [GetArrayLength](/stream-analytics-query/getarraylength-azure-stream-analytics)és az [Apply](/stream-analytics-query/apply-azure-stream-analytics) operátort használják.

Íme egy példa egyetlen eseményre. Mindkettő `CustomSensor03` `SensorMetadata` **tömb** típusú:

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>Egy adott tömb elem használata

Válassza ki a tömb elemet a megadott indexben (az első tömb elem kiválasztásával):

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

Az eredmény a következőket eredményezi:

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>Tömb hosszának kiválasztása

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

Az eredmény a következőket eredményezi:

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>Tömb elemek konvertálása különálló eseményekre

Válassza az összes tömb elemet egyedi eseményként. Az operátor [alkalmazása](/stream-analytics-query/apply-azure-stream-analytics) a [GetArrayElements](/stream-analytics-query/getarrayelements-azure-stream-analytics) beépített függvénnyel együtt kinyeri az összes tömb elemet az egyes eseményekként:

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

Az eredmény a következőket eredményezi:

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
Az eredmény a következőket eredményezi:

|DeviceId|smKey|smValue|
|-|-|-|
|12345|Gyártó|ABC|
|12345|Verzió|1.2.45|

Ha a kibontott mezőket oszlopokban kell megjeleníteni, az [illesztési](/stream-analytics-query/join-azure-stream-analytics) művelet mellett a [with](/stream-analytics-query/with-azure-stream-analytics) szintaxissal is eldöntheti az adatkészletet. Ehhez az illesztéshez meg kell követelni egy [idő határt](/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff) , amely megakadályozza a duplikált állapotot:

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

Az eredmény a következőket eredményezi:

|DeviceId|Lat|Hosszú|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>Lásd még:
[Adattípusok a Azure Stream Analyticsban](/stream-analytics-query/data-types-azure-stream-analytics)