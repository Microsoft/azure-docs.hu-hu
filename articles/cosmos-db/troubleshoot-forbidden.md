---
title: Tiltott kivételek Azure Cosmos DB hibáinak megoldása
description: Megtudhatja, hogyan diagnosztizálhatja és javíthatja a tiltott kivételeket.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99971905"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Tiltott kivételek Azure Cosmos DB diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A 403-es HTTP-állapotkód azt jelenti, hogy a kérés nem hajtható végre.

## <a name="firewall-blocking-requests"></a>Tűzfal-blokkoló kérelmek
Ebben az esetben gyakran előfordul, hogy az alábbi hibákat látja:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Megoldás
Győződjön meg arról, hogy az aktuális [tűzfalbeállítások](how-to-configure-firewall.md) helyesek, és tartalmazza azokat az IP-címeket vagy hálózatokat, amelyekről csatlakozni próbál.
Ha nemrég frissítette őket, ne feledje, hogy a módosítások **alkalmazása akár 15 percet** is igénybe vehet.

## <a name="next-steps"></a>Következő lépések
* Konfigurálja az [IP-tűzfalat](how-to-configure-firewall.md).
* Konfigurálja a [virtuális hálózatok](how-to-configure-vnet-service-endpoint.md)elérését.
* [Privát végpontok](how-to-configure-private-endpoints.md)hozzáférésének konfigurálása.
