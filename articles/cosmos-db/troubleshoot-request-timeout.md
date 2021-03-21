---
title: Azure Cosmos DB szolgáltatási kérelem időtúllépési kivételeinek megoldása
description: Ismerje meg, hogyan diagnosztizálhatja és javíthatja Azure Cosmos DB szolgáltatási kérelmek időtúllépési kivételeit.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b188021de30561222f098e2b5782bada25d7ce0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411252"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Azure Cosmos DB kérelmek időtúllépési kivételeinek diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB a HTTP 408-kérelem időtúllépését adta vissza.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Az alábbi lista a kérelmek időtúllépési kivételeinek ismert okait és megoldásait tartalmazza.

### <a name="check-the-sla"></a>Az SLA ellenõrzése
Ellenőrizze [Azure Cosmos db a figyelést](monitor-cosmos-db.md) , hogy az 408-es kivételek száma megsértse-e a Azure Cosmos db SLA-t.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>1. megoldás: nem sérti a Azure Cosmos DB SLA-t
Az alkalmazásnak kezelnie kell ezt a forgatókönyvet, és újra kell próbálkoznia ezekkel az átmeneti hibákkal.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>2. megoldás: megszegte a Azure Cosmos DB SLA-t
Forduljon az [Azure ügyfélszolgálatához](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Gyors partíciós kulcs
A Azure Cosmos DB a teljes kiosztott átviteli sebességet egyenletesen osztja el a fizikai partíciók között. Gyakori partíció esetén a fizikai partíciók egy vagy több logikai partíciója a fizikai partíciók másodpercenkénti kérelmi egységét (RU/s) is felhasználja. Ugyanakkor a többi fizikai partíción lévő RU/s nem lesz használatban. Ennek tünete, hogy a felhasznált RU/s-k száma kevesebb lesz, mint az adatbázis vagy a tároló teljes kiépített RU/s-je. Továbbra is megtekintheti a szabályozást (429s) a gyors logikai partíciós kulcsra vonatkozó kérelmeknél. A [normalizált ru](monitor-normalized-request-units.md) -használati metrika használatával ellenőrizze, hogy a számítási feladatok egy gyors partícióval találkoznak-e. 

#### <a name="solution"></a>Megoldás:
Válasszon egy jó partíciós kulcsot, amely egyenletesen osztja el a kérelmek mennyiségét és tárolását. Megtudhatja, hogyan [módosíthatja a partíciós kulcsot](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.
* A Azure Cosmos DB Java v4 SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-java-sdk-v4-sql.md) .
* A [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)teljesítményére vonatkozó irányelvek ismertetése.