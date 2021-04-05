---
title: Az erőforrás-naplózási hibák Azure Stream Analytics
description: Ez a cikk ismerteti azokat a különböző bemeneti és kimeneti adathibákat, amelyek a Azure Stream Analytics használatakor előfordulhatnak.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.openlocfilehash: 2ca289cd52b9a406e486ee9c186be683e71f02d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020111"
---
# <a name="azure-stream-analytics-data-errors"></a>AdatAzure Stream Analyticsi hibák

Az adathibák olyan hibák, amelyek az adatfeldolgozás során jelentkeznek.  Ezek a hibák leggyakrabban az adatok deszerializálása, a szerializálás és az írási műveletek során fordulnak elő.  Az adathibák előfordulása esetén Stream Analytics részletes információkat és példákat ír az erőforrás-naplókba. A további részletek eléréséhez engedélyezze a feladatban található diagnosztikai naplókat. Bizonyos esetekben ezen információk összegzése is elérhető a portál értesítésein keresztül.

Ez a cikk a bemeneti és kimeneti adatokhoz tartozó hibák különböző típusait, okát és erőforrás-naplóját ismerteti.

## <a name="resource-logs-schema"></a>Erőforrás-naplók sémája

Az erőforrás-naplók sémájának megtekintéséhez tekintse meg az [Azure stream Analytics hibakeresése diagnosztikai naplók használatával](stream-analytics-job-diagnostic-logs.md#resource-logs-schema) című témakört. A következő JSON egy példa az adathibaok erőforrás-naplójának **Tulajdonságok** mezőjére.

```json
{
    "Source": "InputTelemetryData",
    "Type": "DataError",
    "DataErrorType": "InputDeserializerError.InvalidData",
    "BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "Message": "Input Message Id: https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt Error: Json input stream should either be an array of objects or line separated objects. Found token type: Integer",
    "ExampleEvents": "[\"1,2\\\\u000d\\\\u000a3,4\\\\u000d\\\\u000a5,6\"]",
    "FromTimestamp": "2019-03-22T22:34:18.5664937Z",
    "ToTimestamp": "2019-03-22T22:34:18.5965248Z",
    "EventCount": 1
}
```

## <a name="input-data-errors"></a>Bemeneti adatok hibái

### <a name="inputdeserializererrorinvalidcompressiontype"></a>InputDeserializerError.InvalidCompressionType

* Ok: a kiválasztott bemeneti tömörítési típus nem felel meg az adatoknak.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: bármilyen deszerializáló hibával rendelkező üzenet el lett dobva a bemenetből, beleértve az érvénytelen tömörítési típust.
* Napló részletei
   * Bemeneti üzenet azonosítója. Az Event hub esetében az azonosító a PartitionId, az eltolás és a sorszám.

**Hibaüzenet**

```json
"BriefMessage": "Unable to decompress events from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please ensure compression setting fits the data being processed."
```

### <a name="inputdeserializererrorinvalidheader"></a>InputDeserializerError.InvalidHeader

* Ok: a bemeneti adatok fejléce érvénytelen. Egy CSV-fájl például ismétlődő névvel rendelkező oszlopokat tartalmaz.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: bármilyen deszerializáló hibával rendelkező üzenet el lett dobva, beleértve az érvénytelen fejlécet is.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * A tényleges adattartalom legfeljebb pár kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Invalid CSV Header for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt'. Please make sure there are no duplicate field names."
```

### <a name="inputdeserializererrormissingcolumns"></a>InputDeserializerError.MissingColumns

* Ok: a CREATE TABLE vagy az IDŐBÉLYEG által megadott bemeneti oszlopok nem léteznek.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: a hiányzó oszlopokkal rendelkező események el lesznek dobva a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * A hiányzó oszlopok neve. 
   * A tényleges adattartalom akár néhány kilobájtos.

**Hibaüzenetek**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Missing fields specified in query or in create table. Fields expected:ColumnA Fields found:ColumnB"
```

### <a name="inputdeserializererrortypeconversionerror"></a>InputDeserializerError.TypeConversionError

* Ok: a bemenet nem alakítható át a CREATE TABLE utasításban megadott típusra.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: a rendszer eldobta a beírási hiba típusú eseményeket a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * Az oszlop és a várt típus neve.

**Hibaüzenetek**

```json
"BriefMessage": "Could not deserialize the input event(s) from resource '''https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' as Csv. Some possible reasons: 1) Malformed events 2) Input source configured with incorrect serialization format" 
```

```json
"Message": "Unable to convert column: dateColumn to expected type."
```

### <a name="inputdeserializererrorinvaliddata"></a>InputDeserializerError.InvalidData

* Ok: a bemeneti adatok formátuma nem megfelelő. Például a bemenet nem érvényes JSON.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az üzenetben szereplő összes esemény érvénytelen Adathiba miatt el lett dobva a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * A tényleges adattartalom legfeljebb pár kilobájt.

**Hibaüzenetek**

```json
"BriefMessage": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

```json
"Message": "Json input stream should either be an array of objects or line separated objects. Found token type: String"
```

### <a name="invalidinputtimestamp"></a>InvalidInputTimeStamp

* Ok: a TIMESTAMP BY kifejezés értéke nem konvertálható datetime értékre.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az érvénytelen bemeneti időbélyegzővel rendelkező események el lesznek dobva a bemenetből.
* Napló részletei
   * Bemeneti üzenet azonosítója. 
   * Hibaüzenet. 
   * A tényleges adattartalom legfeljebb pár kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Unable to get timestamp for resource 'https:\\/\\/exampleBlob.blob.core.windows.net\\/inputfolder\\/csv.txt ' due to error 'Cannot convert string to datetime'"
```

### <a name="invalidinputtimestampkey"></a>InvalidInputTimeStampKey

* Ok: az IDŐBÉLYEG értéke több mint timestampColumn NULL értékű.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az érvénytelen bemeneti időbélyeg-kulccsal rendelkező események el lesznek dobva a bemenetből.
* Napló részletei
   * A tényleges hasznos adatok mérete akár néhány kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Unable to get value of TIMESTAMP BY OVER COLUMN"
```

### <a name="lateinputevent"></a>LateInputEvent

* Ok: az alkalmazás ideje és az érkezési idő közötti különbség nagyobb, mint a késői beérkezés tolerancia ablaka.
* A portálon megadott értesítés: nem
* Erőforrás-naplózási szint: információ
* Hatás: a késői bemeneti események kezelése a feladatok konfigurációjának események rendezése szakaszának "más események kezelése" beállításának megfelelően történik. További információ: [Időkezelési szabályzatok](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Napló részletei
   * Az alkalmazás ideje és az érkezés időpontja. 
   * A tényleges adattartalom legfeljebb pár kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Input event with application timestamp '2019-01-01' and arrival time '2019-01-02' was sent later than configured tolerance."
```

### <a name="earlyinputevent"></a>EarlyInputEvent

* Ok: az alkalmazás ideje és az érkezési idő közötti különbség 5 percnél nagyobb.
* A portálon megadott értesítés: nem
* Erőforrás-naplózási szint: információ
* Hatás: a korai bemeneti események kezelése a feladatok konfigurációjának események rendezése szakaszának "más események kezelése" beállításának megfelelően történik. További információ: [Időkezelési szabályzatok](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Napló részletei
   * Az alkalmazás ideje és az érkezés időpontja. 
   * A tényleges adattartalom legfeljebb pár kilobájt.

**Hibaüzenet**

```json
"BriefMessage": "Input event arrival time '2019-01-01' is earlier than input event application timestamp '2019-01-02' by more than 5 minutes."
```

### <a name="outoforderevent"></a>OutOfOrderEvent

* Ok: az eseményt a rendszer a megadott sorrendben beállított tolerancia ablak szerint tekinti sorrendbe.
* A portálon megadott értesítés: nem
* Erőforrás-naplózási szint: információ
* Hatás: a megrendelési események kezelése a feladatok konfigurációjának események rendezése szakaszának "egyéb események kezelése" beállításának megfelelően történik. További információ: [Időkezelési szabályzatok](/stream-analytics-query/time-skew-policies-azure-stream-analytics).
* Napló részletei
   * A tényleges adattartalom legfeljebb pár kilobájt.

**Hibaüzenet**

```json
"Message": "Out of order event(s) received."
```

## <a name="output-data-errors"></a>Kimeneti adathibák

A Azure Stream Analytics a konfigurációtól függően a kimeneti adathibákat a kimeneti fogadó I/O-kérelmével vagy anélkül tudja azonosítani. Például hiányzik egy szükséges oszlop, például:  `PartitionKey` , ha az Azure Table output használata I/O-kérelem nélkül azonosítható. Az SQL-kimenetben azonban a korlátozás megsértése I/O-kérést igényel.

Több olyan Adathiba is észlelhető, amelyet a rendszer csak a kimeneti fogadó hívása után észlel, ami lelassíthatja a feldolgozást. Ennek megoldásához módosítsa a feladatok konfigurációját vagy az adathibát okozó lekérdezést.

### <a name="outputdataconversionerrorrequiredcolumnmissing"></a>OutputDataConversionError.RequiredColumnMissing

* Ok: a kimenethez szükséges oszlop nem létezik. Például létezik egy, az Azure Table PartitionKey csinál ' definiált oszlop.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az összes kimeneti Adatátalakítási hiba, beleértve a hiányzó kötelező oszlopot, a [kimeneti adatházirend](./stream-analytics-output-error-policy.md) -beállításnak megfelelően kezeli.
* Napló részletei
   * Az oszlop neve és a rekord azonosítója vagy a rekord egy része.

**Hibaüzenet**

```json
"Message": "The output record does not contain primary key property: [deviceId] Ensure the query output contains the column [deviceId] with a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorcolumnnameinvalid"></a>OutputDataConversionError.ColumnNameInvalid

* Ok: az oszlop értéke nem felel meg a kimenetnek. Az oszlop neve például nem érvényes Azure Table oszlop.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az összes kimeneti Adatátalakítási hiba, beleértve az érvénytelen oszlopnevet is, a [kimeneti adatházirend](./stream-analytics-output-error-policy.md) -beállításnak megfelelően kezeli.
* Napló részletei
   * Az oszlop neve és a rekord azonosítója vagy a rekord egy része.

**Hibaüzenet**

```json
"Message": "Invalid property name #deviceIdValue. Please refer MSDN for Azure table property naming convention."
```

### <a name="outputdataconversionerrortypeconversionerror"></a>OutputDataConversionError.TypeConversionError

* Ok: egy oszlop nem alakítható át érvényes típusra a kimenetben. Az oszlop értéke például nem kompatibilis az SQL-táblában definiált korlátozásokkal vagy típusokkal.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az összes kimeneti Adatátalakítási hiba, beleértve a típus-átalakítási hibát, a [kimeneti adatházirend](./stream-analytics-output-error-policy.md) -beállításnak megfelelően van kezelve.
* Napló részletei
   * Az oszlop neve.
   * Vagy a rekord azonosítóját vagy egy részét.

**Hibaüzenet**

```json
"Message": "The column [id] value null or its type is invalid. Ensure to provide a unique non-empty string less than '255' characters."
```

### <a name="outputdataconversionerrorrecordexceededsizelimit"></a>OutputDataConversionError.RecordExceededSizeLimit

* Ok: az üzenet értéke nagyobb, mint a támogatott kimeneti méret. Egy rekord például 1 MB-nál nagyobb az Event hub-kimenetnél.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az összes kimeneti Adatátalakítási hiba, beleértve a rekord meghaladta a méretkorlátot, a [kimeneti adatházirend](./stream-analytics-output-error-policy.md) -beállításnak megfelelően kezeli a rendszer.
* Napló részletei
   * Vagy a rekord azonosítóját vagy egy részét.

**Hibaüzenet**

```json
"BriefMessage": "Single output event exceeds the maximum message size limit allowed (262144 bytes) by Event Hub."
```

### <a name="outputdataconversionerrorduplicatekey"></a>OutputDataConversionError.DuplicateKey

* Ok: egy rekord már tartalmaz egy olyan oszlopot, amelynek a neve megegyezik a System oszlop nevével. Például a kimenet CosmosDB egy azonosító nevű oszloppal, ha az azonosító oszlop egy másik oszlop.
* Portálon megadott értesítés: igen
* Erőforrás-naplózási szint: figyelmeztetés
* Hatás: az összes kimeneti Adatátalakítási hiba, beleértve az ismétlődő kulcsot, a [kimeneti adatházirend](./stream-analytics-output-error-policy.md) -beállításnak megfelelően van kezelve.
* Napló részletei
   * Az oszlop neve.
   * Vagy a rekord azonosítóját vagy egy részét.

```json
"BriefMessage": "Column 'devicePartitionKey' is being mapped to multiple columns."
```

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics hibakeresése diagnosztikai naplók használatával](stream-analytics-job-diagnostic-logs.md)

* [A Stream Analytics feladatok figyelésének és a lekérdezések figyelésének ismertetése](stream-analytics-monitoring.md)