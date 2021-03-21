---
title: Azure Cosmos DB kérelmek arányának hibája túl nagy kivételek
description: Megtudhatja, hogyan diagnosztizálhatja és elháríthatja a kérelmek arányának túl nagy kivételeit.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: c3a692a8df93931bd8fd7982e69fe2f9118444c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411337"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Azure Cosmos DB kérelmek arányának diagnosztizálása és megoldása túl nagy kivételek
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A "kérési arány túl nagy" üzenet vagy hibakód 429 jelzi, hogy a kérések szabályozása folyamatban van.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A következő szakasz a túl sok kérelem ismert okait és megoldásait tartalmazza.

### <a name="check-the-metrics"></a>A metrikák keresése
Az 429-es kivételek számának megtekintéséhez tekintse meg az [Azure Cosmos db figyelését](monitor-cosmos-db.md) .

#### <a name="cause"></a>Ok:
A felhasznált átviteli sebesség (kérési egység/másodperc) túllépte a [kiosztott átviteli sebességet](set-throughput.md). Az SDK automatikusan újrapróbálkozik a kérelmekkel a megadott újrapróbálkozási házirend alapján. Ha ez a hiba gyakran előfordul, érdemes lehet növelni az átviteli sebességet a gyűjteményen. Tekintse meg a portál metrikáit, és ellenőrizze, hogy van-e 429-es hiba. Tekintse át a partíciós kulcsot, és győződjön meg arról, hogy a [tárolók és a kérelmek mennyiségének egyenletes eloszlását](partitioning-overview.md)eredményezi.

#### <a name="solution"></a>Megoldás:
1. A kiépített átviteli sebesség növeléséhez használja a [portált vagy az SDK](set-throughput.md) -t.
1. Az adatbázis vagy a tároló automatikus [skálázásra](provision-throughput-autoscale.md)váltása.

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.