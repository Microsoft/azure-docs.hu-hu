---
title: Konfigurációs hibakódok – Azure Stream Analytics
description: A konfigurációs hibakódokkal Azure Stream Analytics hibák elhárítása.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 6137ceeb0d86b1531adab910175ddbc4722ef858
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019516"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Azure Stream Analytics konfigurációs hibakódok

A tevékenységek naplóit és erőforrás-naplóit használhatja a nem várt viselkedések hibakereséséhez a Azure Stream Analytics feladatból. Ez a cikk felsorolja az összes konfigurációs hibakód leírását. A konfigurációs hibák a feladatok konfigurációjával, illetve a bemeneti és kimeneti konfigurációkhoz kapcsolódnak.

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **OK**: az Event hub *jogosulatlan hozzáférési* hibát okozott.

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **OK**: egynél több Event hub-fogadó rendelkezik különböző időpontok értékkel.
* **Javaslat**: Győződjön meg arról, *Service Bus Explorer* vagy egy *EventProcessorHost* -alkalmazás nincs csatlakoztatva, amíg a stream Analytics-feladatot futtatja.

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **OK**: a stream Analytics nem tud kapcsolódni egy partícióhoz, mert elérte a felhasználói csoportokban lévő engedélyezett fogadók maximális számát.
* **Javaslat**: gondoskodjon arról, hogy a többi stream Analytics feladat vagy a Service Bus Explorer ne használja ugyanazt a fogyasztói csoportot.

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **OK**: hiba történt az események az Event hubhoz való írásakor a szabályozás miatt.
* **Javaslat**: Ha ez következetesen történik, frissítse az átviteli sebességet.

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **OK**: a megadott hálózati konfiguráció helytelen.
* **Javaslat**: javítsa ki a konfigurációt, és indítsa újra a feladatot.

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **OK**: az Event hub-gazdagép nem érhető el.
* **Javaslat**: Ellenőrizze, hogy helyes-e a megadott állomásnév.

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **OK**: a EventHub küldője váratlan EventHub-partíciókat észlelt.
* **Javaslat**: indítsa újra a stream Analytics feladatot, ha módosult a EventHub partícióinak száma.

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **OK**: a stream Analytics nem találta meg egy adott Cosmos db gyűjtemény partíciós kulcsát az adatbázisban.
* **Javaslat**: Ellenőrizze, hogy van-e érvényes partíciós kulcs a gyűjteményhez Cosmos DBban.

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **OK**: akkor kerül kidobásra, ha a partíciós kulcs nem a levél csomópontja, sem a legfelső szinten.

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **OK**: a lekérdezés kimenete nem tartalmazhatja az oszlop \[ azonosítóját], ha egy másik oszlop van kiválasztva, mint az elsődleges kulcs tulajdonsága.

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **OK**: a stream Analytics nem talál CosmosDB-adatbázist.

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **OK**: a stream Analytics nem talál egy adott Cosmos db gyűjteményt egy adatbázisban.

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **OK**: hiba történt az adatírás során Cosmos db által történő szabályozás miatt.
* **Javaslat**: a gyűjtemény teljesítményi szintjeinek frissítése és az adatbázis teljesítményének finomhangolása.

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **OK**: a stream Analytics-feladattípus hitelesítési hibát észlelt.
* **Javaslat**: Ellenőrizze, hogy helyes-e a SQL Database-kapcsolatok karakterlánca.

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **OK**: a stream Analytics-feladattípus hitelesítési hibát észlelt. 
* **Javaslat**: Ellenőrizze, hogy a fiók neve megfelelően van-e konfigurálva, és hogy a feladatokhoz tartozó felügyelt identitás hozzáfér-e a SQL Databasehoz.

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **OK**: a stream Analytics nem találja az adott tábla sémájának adatait.

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **OK**: a SQL Database nem támogatott.
* **Javaslat**: dedikált SQL-készlet használata.

## <a name="next-steps"></a>Következő lépések

* [Bemeneti kapcsolatok hibaelhárítása](stream-analytics-troubleshoot-input.md)
* [Azure Stream Analytics kimenetek hibáinak megoldása](stream-analytics-troubleshoot-output.md)
* [Azure Stream Analytics lekérdezések hibáinak megoldása](stream-analytics-troubleshoot-query.md)
* [Azure Stream Analyticsek hibakeresése erőforrás-naplók használatával](stream-analytics-job-diagnostic-logs.md)
* [AdatAzure Stream Analyticsi hibák](data-errors.md)
