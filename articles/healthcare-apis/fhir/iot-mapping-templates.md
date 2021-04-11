---
title: 'Fogalmak: sablonok leképezése az Azure IoT Connectorban a FHIR (előzetes verzió) szolgáltatáshoz az Azure API for FHIR'
description: Ismerje meg, hogyan hozhat létre két típusú leképezési sablont az Azure IoT Connectorban a FHIR (előzetes verzió) szolgáltatásban. Az eszköz-hozzárendelési sablon egy normalizált sémába alakítja át az eszközöket. A FHIR-leképezési sablon normalizált üzenetet alakít át egy FHIR-alapú megfigyelési erőforrásra.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: punagpal
ms.openlocfilehash: e43b5068544927ba4d04c86b16d3a15510c32bed
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448472"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>Az FHIR-hez készült Azure IoT-összekötő (előzetes verzió) leképezési sablonjai
Ez a cikk részletesen ismerteti, hogyan konfigurálhatja az Azure IoT Connectort a gyors egészségügyi együttműködési erőforrásokhoz (FHIR&#174;) * a leképezési sablonok használatával.

A FHIR készült Azure IoT-összekötőhöz két típusú JSON-alapú leképezési sablon szükséges. Az első típus, az **eszköz-hozzárendelés** feladata az `devicedata` Azure Event hub-végpontnak eljuttatott eszköz-adattartalom megfeleltetése. Kibontja a típusokat, az eszközök azonosítóit, a mérési dátum időpontját és a mérési értéket. A második típus, a **FHIR leképezése** SZABÁLYOZZA a FHIR-erőforrás leképezését. Lehetővé teszi a megfigyelési időszak hosszának, az értékek tárolására szolgáló FHIR és a terminológiai kódoknak a konfigurálását. 

A leképezési sablonok a típusuk alapján JSON-dokumentumba állnak. Ezek a JSON-dokumentumok ezután a Azure Portalon keresztül hozzáadódnak az Azure IoT-összekötőhöz a FHIR. Az eszköz-hozzárendelési dokumentum az **eszköz-hozzárendelés konfigurálása** oldalon és a FHIR-leképezési dokumentumon keresztül vehető fel a **FHIR-leképezés konfigurálása** lapon.

> [!NOTE]
> A leképezési sablonok egy mögöttes blob Storage-tárolóban tárolódnak, és a Blobok alapján töltődnek be a számítási végrehajtásból. A frissítés után azonnal érvénybe lépnek. 

## <a name="device-mapping"></a>Eszköz-hozzárendelés
Az eszközök leképezése lehetővé teszi a leképezési funkciókat, hogy az eszköz tartalmát közös formátumban kinyerje a további kiértékeléshez. Minden fogadott üzenet kiértékelése az összes sablon alapján történik. Ez a megközelítés lehetővé teszi, hogy egyetlen bejövő üzenetet több kimenő üzenetre lehessen kiszolgálni, amelyek később a FHIR különböző észrevételeire vannak leképezve. Az eredmény egy normalizált adatobjektum, amely a sablonok által elemzett értéket vagy értékeket jelöli. A normalizált adatmodellnek néhány szükséges tulajdonsága van, amelyeket meg kell találni és ki kell olvasni:

| Tulajdonság | Leírás |
| - | - |
|**Típus**|A mérték besorolásának neve/típusa. Ez az érték a szükséges FHIR-leképezési sablonhoz való kötésre szolgál.  Több sablon kimenete is lehet ugyanabba a típusba, amely lehetővé teszi, hogy a különböző adatábrázolásokat több eszközről egyetlen közös kimenetre képezze.|
|**OccurenceTimeUtc**|A mérés időpontja.|
|**DeviceId**|Az eszköz azonosítója. Ennek az értéknek meg kell egyeznie a cél FHIR-kiszolgálón található eszköz erőforrás-azonosítóval.|
 |**Tulajdonságok**|Bontson ki legalább egy tulajdonságot, hogy az érték menthető a megfigyelt erőforrásban.  A tulajdonságok a normalizálás során kinyert kulcs érték párok gyűjteményei.|

Alább látható egy példa arra, hogy mi történik a normalizálás során.

![Normalizálás – példa](media/concepts-iot-mapping-templates/normalization-example.png)

A tartalom hasznos tartalma egy Azure Event hub-üzenet, amely három részből áll: törzs, tulajdonságok és SystemProperties. Az egy `Body` bájtos tömb, amely egy UTF-8 kódolású karakterláncot jelképez. A sablon kiértékelése során a rendszer automatikusan átalakítja a bájt tömböt a karakterlánc értékre. `Properties` a egy Key Value gyűjtemény, amelyet az üzenet létrehozója használhat. `SystemProperties` Az Azure Event hub-keretrendszer által fenntartott Key Value-gyűjtemény is, amely automatikusan feltölti azokat a bejegyzéseket.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>Leképezés JSON elérési úttal
A jelenleg támogatott három eszköz tartalmának sablonja JSON-útvonalon alapul, és mindkettőnek meg kell egyeznie a szükséges sablonnal és a kinyert értékekkel. A JSON-útvonalról további információt [itt](https://goessner.net/articles/JsonPath/)találhat. Mindhárom sablon típusa a [JSON .net-implementációt](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) használja a JSON-elérésiút-kifejezések feloldásához.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
A JsonPathContentTemplate lehetővé teszi, hogy az Event hub-üzenetből a JSON elérési úttal való egyezést és kinyerje az értékeket.

| Tulajdonság | Leírás |<div style="width:150px">Példa</div>
| --- | --- | --- 
|**TypeName**|A sablonnak megfelelő mértékekkel társítandó típus.|`heartrate`
|**TypeMatchExpression**|A JSON-elérésiút-kifejezés, amelyet a rendszer kiértékel az Event hub adattartalommal. Ha a rendszer megfelelő JToken talál, a sablon egyezésnek minősül. Az összes további kifejezés kiértékelése a kibontott JToken történik.|`$..[?(@heartRate)]`
|**TimestampExpression**|A JSON-útvonal kifejezése a mérték OccurenceTimeUtc tartozó időbélyeg értékének kinyeréséhez.|`$.endDate`
|**DeviceIdExpression**|A JSON-útvonal kifejezése az eszköz azonosítójának kinyeréséhez.|`$.deviceId`
|**PatientIdExpression**|Nem *kötelező*: a JSON-elérési út kifejezése a beteg azonosítójának kinyeréséhez.|`$.patientId`
|**EncounterIdExpression**|Nem *kötelező*: a JSON-elérési út kifejezése a találkozási azonosító kinyeréséhez.|`$.encounterId`
|**Értékek []. ValueName**|A következő kifejezés által kinyert értékkel társítandó név. A szükséges érték/összetevő kötésére szolgál a FHIR-leképezési sablonban. |`hr`
|**Értékek []. ValueExpression**|A JSON-útvonal kifejezése a szükséges érték kinyeréséhez.|`$.heartRate`
|**Értékek []. Szükséges**|Megköveteli, hogy az érték a hasznos adatokban legyen jelen.  Ha nem található, a rendszer nem hoz létre mérést, és egy InvalidOperationException fog dobni.|`true`

##### <a name="examples"></a>Példák
---
**Pulzusszám**

*Üzenetet*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Sablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Vérnyomás**

*Üzenetet*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Sablon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Több mérés kivetítése egyetlen üzenetből**

*Üzenetet*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*1. sablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*2. sablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**Több mérés kivetítése a tömbből az üzenetben**

*Üzenetet*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Sablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

A IotJsonPathContentTemplate hasonló a JsonPathContentTemplate, kivéve a DeviceIdExpression és a TimestampExpression-t.

A sablon használatakor a rendszer a kiértékelt üzeneteket az Azure [IoT hub Device SDK](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) -k vagy az [Azure IoT Central](../../iot-central/core/overview-iot-central.md) [exportálási (örökölt)](../../iot-central/core/howto-export-data-legacy.md) funkciójának használatával küldi el. Ezen SDK-k használatakor az eszköz identitása (feltéve, hogy az Azure IOT hub/Central eszközének azonosítóját a rendszer regisztrálja az termékazonosító a cél FHIR-kiszolgálón), és az üzenet időbélyege ismert. Ha az Azure IoT Hub eszköz SDK-kat használja, de egyéni tulajdonságokat használ az üzenettörzs vagy a mérési időbélyeg számára, akkor továbbra is használhatja a JsonPathContentTemplate.

*Megjegyzés: a IotJsonPathContentTemplate használatakor a TypeMatchExpression a teljes üzenetre JToken kell oldania. Tekintse meg az alábbi példákat.* 
##### <a name="examples"></a>Példák
---
**Pulzusszám**

*Üzenetet*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Sablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Vérnyomás**

*Üzenetet*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Sablon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

A IotCentralJsonPathContentTemplate emellett nem igényli a DeviceIdExpression és a TimestampExpression használatát, és a kiértékeléskor használt üzeneteket az [Azure IoT Central](../../iot-central/core/overview-iot-central.md) [adatexportálási](../../iot-central/core/howto-export-data.md) funkciójával küldi el. Ha ezt a funkciót használja, az eszköz identitása (feltéve, hogy az Azure IOT Central eszközének azonosítója regisztrálva van egy termékazonosító a cél FHIR-kiszolgálón), és az üzenet időbélyege ismert. Ha az Azure IoT Central adatexportálási funkcióját használja, de egyéni tulajdonságokat használ az üzenettörzs vagy a mérési időbélyeg számára, akkor továbbra is használhatja a JsonPathContentTemplate.

*Megjegyzés: a IotCentralJsonPathContentTemplate használatakor a TypeMatchExpression a teljes üzenetre JToken kell oldania. Tekintse meg az alábbi példákat.* 
##### <a name="examples"></a>Példák
---
**Pulzusszám**

*Üzenetet*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Sablon*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Vérnyomás**

*Üzenetet*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Sablon*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>FHIR leképezése
Az eszköz tartalmának normalizált modellbe való kinyerése után az adatok gyűjtése és csoportosítása az eszköz azonosítója, a mérték típusa és az időszak szerint történik. Ennek a csoportnak a kimenetét a rendszer a FHIR-erőforrásba való átalakítás céljából küldi el (jelenleg[megfigyelés](https://www.hl7.org/fhir/observation.html) ). Itt a FHIR-leképezési sablon vezérli, hogy az egyes FHIR-megfigyelések hogyan legyenek leképezve. Létre kell hozni egy megfigyelést egy adott időpontra vagy egy órán belül? Milyen kódokat kell hozzáadni a megfigyeléshez? Az értéknek [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) vagy [mennyiséget](https://www.hl7.org/fhir/datatypes.html#Quantity)kell képviselnie? Ezek az adattípusok mind a FHIR-leképezési konfigurációt szabályozó beállítások.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
A CodeValueFhirTemplate jelenleg csak az FHIR-leképezésben támogatott sablon.  Lehetővé teszi a kódok, a hatályos időtartam és a megfigyelés értékének meghatározását. Több értéktípus is támogatott: [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData), [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)és [mennyiség](https://www.hl7.org/fhir/datatypes.html#Quantity). Ezekkel a konfigurálható értékekkel együtt automatikusan kezeli a megfigyelési erőforrás azonosítóját, valamint a megfelelő eszköz és a beteg erőforrásaihoz való csatolást.

| Tulajdonság | Leírás 
| --- | ---
|**TypeName**| A sablonhoz kötni kívánt mérték típusa. Legalább egy olyan eszköz-hozzárendelési sablonnak kell lennie, amelyik ezt a típust kiírja.
|**PeriodInterval**|A kimutatott megfigyelés időtartama. A támogatott értékek: 0 (egy példány), 60 (egy óra), 1440 (egy nap).
|**Kategória**|Tetszőleges számú [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) a létrehozott megfigyelési típus besorolásához.
|**Kódok**|Egy vagy több olyan [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding) , amelyet a létrehozott megfigyelésre alkalmazni kell.
|**Kódok []. Kód**|A [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding)kódja.
|**Kódok []. Rendszer**|A [kódolási](http://hl7.org/fhir/datatypes-definitions.html#coding)rendszer.
|**Kódok []. Kijelző**|A [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding)megjelenítése.
|**Érték**|A megfigyelés során kinyerni és ábrázolni kívánt érték. További információ: [Value type templates](#valuetypes).
|**Összetevők**|Nem *kötelező:* Egy vagy több olyan összetevő, amelyet a megfigyeléshez kell létrehozni.
|**Összetevők []. Kódok**|Az összetevőre alkalmazandó egy vagy több [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding) .
|**Összetevők []. Érték**|Az összetevőben kinyerni és ábrázolni kívánt érték. További információ: [Value type templates](#valuetypes).

### <a name="value-type-templates"></a>Érték típusú sablonok <a name="valuetypes"></a>
Alább láthatók a jelenleg támogatott értékmodell-sablonok. A későbbiekben további sablonokat is hozzáadhat.
#### <a name="sampleddata"></a>SampledData
A [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR adattípust jelöli. A megfigyelési mérések egy adott időpontnál kezdődő adatfolyamként íródnak, és a megadott időszakon belül megnövekszik. Ha nincs megadva érték, `E` a rendszer beírja az adatfolyamba. Ha az időszak úgy van, hogy két további érték ugyanazt a pozíciót foglalja el az adatfolyamban, a rendszer a legújabb értéket fogja használni. Ugyanezt a logikát alkalmazza a rendszer, amikor az SampledData-t használó megfigyelés frissül.

| Tulajdonság | Leírás 
| --- | ---
|**DefaultPeriod**|A használandó alapértelmezett időtartam ezredmásodpercben. 
|**Unit (Egység)**|A SampledData forrására beállított egység 

#### <a name="quantity"></a>Mennyiség
A [mennyiség](http://hl7.org/fhir/datatypes.html#Quantity) FHIR adattípust jelöli. Ha a csoportosításban egynél több érték szerepel, akkor csak az első értéket használja a rendszer. Ha az új érték ugyanarra a megfigyelésre érkezik, akkor a régi értéket írja felül.

| Tulajdonság | Leírás 
| --- | --- 
|**Unit (Egység)**| Egység ábrázolása.
|**Kód**| Az egység kódolt formája.
|**Rendszer**| A kódolt egység űrlapot meghatározó rendszer.

### <a name="codeableconcept"></a>CodeableConcept
A [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR adattípust jelöli. A tényleges érték nincs használatban.

| Tulajdonság | Leírás 
| --- | --- 
|**Szöveg**|Egyszerű szöveges ábrázolás. 
|**Kódok**|Egy vagy több olyan [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding) , amelyet a létrehozott megfigyelésre alkalmazni kell.
|**Kódok []. Kód**|A [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding)kódja.
|**Kódok []. Rendszer**|A [kódolási](http://hl7.org/fhir/datatypes-definitions.html#coding)rendszer.
|**Kódok []. Kijelző**|A [kódolás](http://hl7.org/fhir/datatypes-definitions.html#coding)megjelenítése.

### <a name="examples"></a>Példák
**Heart rate – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Lépések – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Vérnyomás – SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Vérnyomás – mennyiség**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Eszköz eltávolítva – CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Következő lépések

Tekintse meg a FHIR (előzetes verzió) Azure IoT-összekötővel kapcsolatos gyakori kérdéseket.

>[!div class="nextstepaction"]
>[Azure IoT-összekötő FHIR – gyakori kérdések](fhir-faq.md)

* A Azure Portal a FHIR készült Azure IoT Connector a IoT Connector (előzetes verzió) néven ismert. A FHIR a HL7 bejegyzett védjegye, és a HL7 engedélyével van használatban.