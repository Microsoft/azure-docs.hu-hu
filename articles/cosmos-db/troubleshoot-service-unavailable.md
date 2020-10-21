---
title: Azure Cosmos DB szolgáltatás nem elérhető kivételeinek hibáinak megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja Azure Cosmos DB szolgáltatás nem elérhető kivételeit.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b40787e1b7c40e5c238b2e400f6b449ad8963dd1
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92277132"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Azure Cosmos DB szolgáltatás nem elérhető kivételeinek diagnosztizálása és megoldása
Az SDK nem tudott csatlakozni a Azure Cosmos DBhoz.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő lista a szolgáltatás nem elérhető kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="the-required-ports-are-being-blocked"></a>A szükséges portok blokkolása folyamatban van
Ellenőrizze, hogy az összes [szükséges port](sql-sdk-connection-modes.md#service-port-ranges) engedélyezve van-e.

### <a name="client-side-transient-connectivity-issues"></a>Ügyféloldali átmeneti kapcsolódási problémák
A szolgáltatás nem érhető el kivételek lehetnek olyan átmeneti kapcsolódási problémák, amelyek időtúllépést okoznak. A forgatókönyvhöz kapcsolódó verem-nyomkövetés általában `TransportException` hibát tartalmaz. Például:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

A megoldásához kövesse az [időtúllépési kérelem hibaelhárításának lépéseit](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) .

### <a name="service-outage"></a>Szolgáltatáskimaradás
Tekintse meg az [Azure állapotát](https://status.azure.com/status) , és ellenőrizze, hogy van-e folyamatban probléma.


## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.