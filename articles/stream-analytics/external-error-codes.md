---
title: Külső hibakódok – Azure Stream Analytics
description: A külső hibakódokkal Azure Stream Analytics hibák elhárítása.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: f963f4c3538146b7f9929960092a18294659be5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045246"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Külső hibakódok Azure Stream Analytics

A tevékenységek naplóit és erőforrás-naplóit használhatja a nem várt viselkedések hibakereséséhez a Azure Stream Analytics feladatból. Ez a cikk felsorolja az összes külső hibakód leírását. A külső hibák olyan általános hibák, amelyek egy felsőbb rétegbeli vagy alsóbb rétegbeli szolgáltatás, amely Stream Analytics nem tudja megkülönböztetni Adathiba, konfigurációs hiba vagy külső rendelkezésre állási hiba esetén.

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **OK**: hiba történt az adapter inicializálásakor.

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **OK**: hiba történt az adatadapterbe való írás során.

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **OK**: a rendszer http-hibát adott vissza az Azure functions szolgáltatásban.

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **OK**: stream Analytics nem tudott eseményeket írni az Azure-függvénybe.

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **OK**: átirányítási hiba történt a Azure functions való üzembe helyezéskor.

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **OK**: hiba történt az ügyfél Azure functions való kihelyezése során.

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **OK**: hiba történt a Azure functions való üzembe helyezéskor.

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **OK**: az Azure functions szolgáltatásba való írás sikertelen volt, mert a HTTP-kérelem túllépte az időkorlátot. 
* **Javaslat**: a lehetséges késések olvassa el a Azure functions naplókat.

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **OK**: a bemeneti eltolások érvénytelenek. Ennek oka lehet feladatátvétel.
* **Javaslat**: indítsa újra a stream Analytics feladatot a legutóbbi kimeneti időpontból.

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **OK**: hiba történt az Event hub-ba való adatküldés során.

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **OK**: az újrapróbálkozások maximális száma után stream Analytics sikertelen volt a Cosmos db-fiókhoz való kapcsolódás.

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **OK**: a stream Analytics nem tudta lekérdezni a Cosmos db adatbázist és a gyűjteményt az újrapróbálkozások maximális száma után.

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **OK**: a CosmosDB nem tud tárolt eljárást létrehozni több újrapróbálkozás után.

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **OK**: a upsert tárolt eljárás hibát adott vissza. 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **OK**: a stream Analytics nem tudja inicializálni a SQL Database kimenetét.

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **OK**: a stream Analytics nem tud eseményeket írni a SQL Database kimenetbe.

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **OK**: hiba történt a szinapszis SQL-készlet kimenetének inicializálásakor.

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **OK**: hiba történt a szinapszis SQL-készletbe való kimenet írásakor.

## <a name="next-steps"></a>További lépések

* [Bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics kimenetek hibáinak megoldása](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics lekérdezések hibáinak megoldása](stream-analytics-troubleshoot-query.md)
* [Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával](stream-analytics-job-diagnostic-logs.md)
* [AdatAzure Stream Analyticsi hibák](data-errors.md)
