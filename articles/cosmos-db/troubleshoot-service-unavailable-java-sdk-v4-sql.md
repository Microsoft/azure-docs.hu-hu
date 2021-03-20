---
title: A Azure Cosmos DB szolgáltatás nem érhető el a Java v4 SDK-val való hibáinak megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja Azure Cosmos DB szolgáltatás nem érhető el kivételeket a Java v4 SDK-val.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340071"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Az Azure Cosmos DB Java v4 SDK szolgáltatáshoz nem használható kivételek diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
A Java v4 SDK nem tudott csatlakozni a Azure Cosmos DBhoz.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő lista a szolgáltatás nem elérhető kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="the-required-ports-are-being-blocked"></a>A szükséges portok blokkolása folyamatban van
Ellenőrizze, hogy az összes [szükséges port](sql-sdk-connection-modes.md#service-port-ranges) engedélyezve van-e.

### <a name="client-side-transient-connectivity-issues"></a>Ügyféloldali átmeneti kapcsolódási problémák
A szolgáltatás nem érhető el kivételek lehetnek olyan átmeneti kapcsolódási problémák, amelyek időtúllépést okoznak. A forgatókönyvhöz kapcsolódó verem-nyomkövetés általában a `ServiceUnavailableException` diagnosztikai adatokkal kapcsolatos hibát tartalmaz. Például:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

A megoldásához kövesse az [időtúllépési kérelem hibaelhárításának lépéseit](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) .

### <a name="service-outage"></a>Szolgáltatáskimaradás
Tekintse meg az [Azure állapotát](https://status.azure.com/status) , és ellenőrizze, hogy van-e folyamatban probléma.


## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.