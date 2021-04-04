---
title: Adathibák kódjai – Azure Stream Analytics
description: Az adathibák kódjaival Azure Stream Analytics hibák elhárítása.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 33e617c8c3589d76d649dd2ea2236a4247b12500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016490"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Adathiba-kódok Azure Stream Analytics

A tevékenységek naplóit és erőforrás-naplóit használhatja a nem várt viselkedések hibakereséséhez a Azure Stream Analytics feladatból. Ez a cikk felsorolja az összes Adathiba hibakódjának leírását. Adathibák fordulnak elő, ha az adatfolyamban rossz adatok vannak, például egy váratlan rekord sémája.

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **OK**: hiba történt a bemeneti adatok deszerializálása közben.

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **OK**: a stream Analytics nem tudja lekérni az erőforrás időbélyegét. 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **OK**: a stream Analytics nem tudja beolvasni a értékét `TIMESTAMP BY OVER COLUMN` .

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **OK**: egy bemeneti esemény lett elküldve a beállított tűréshatárnál később.

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **OK**: a bemeneti esemény megérkezésének időpontja korábbi, mint a bemeneti esemény alkalmazásának időbélyeg-küszöbértéke.

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **OK**: az Azure functions üzenet kimenete meghaladja a méretkorlátot.

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **OK**: a kimeneti rekord meghaladja a maximális méretkorlátot az Event hub-ba való írás során.

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **OK**: egy adott oszlop értéke vagy típusa érvénytelen.
* **Javaslat**: adjon meg olyan egyedi, nem üres karakterláncot, amely nem hosszabb 255 karakternél.

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **OK**: a kimeneti rekord dokumentum-azonosítója érvénytelen karaktert tartalmaz.

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **OK**: a kimeneti rekord nem tartalmazza az oszlop \[ azonosítóját], amelyet elsődleges kulcs tulajdonságként kíván használni.

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **OK**: a kimeneti rekord nem tartalmazza a dokumentum-azonosító tulajdonságot. 
* **Javaslat**: Ellenőrizze, hogy a lekérdezés kimenete tartalmazza-e a "255" karakternél kisebb, egyedi, nem üres sztringet tartalmazó oszlopot.

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **OK**: a kimeneti rekordból hiányzik a Partition Key tulajdonságként használandó oszlop.

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **OK**: egy rekord Cosmos DBba írása túl nagy.

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **OK**: a stream Analytics nem tud olyan esemény (eke) t írni, amely az adatproblémák miatt SQL Database.

## <a name="next-steps"></a>Következő lépések

* [Bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics kimenetek hibáinak megoldása](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics lekérdezések hibáinak megoldása](stream-analytics-troubleshoot-query.md)
* [Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával](stream-analytics-job-diagnostic-logs.md)
* [AdatAzure Stream Analyticsi hibák](data-errors.md)
