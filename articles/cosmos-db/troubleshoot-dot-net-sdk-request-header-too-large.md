---
title: "\"Kérelem fejléce túl nagy\" üzenet vagy 400 hibás kérelem hibáinak megoldása Azure Cosmos DB"
description: Megtudhatja, hogyan diagnosztizálhatja és javíthatja a kérelem fejlécének túl nagy kivételét.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340515"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Azure Cosmos DB "a kérelem fejléce túl nagy" üzenet diagnosztizálása és megoldása
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A "kérelem fejléce túl nagy" üzenetet egy 400-es HTTP-hibakód alapján dobjuk el. Ez a hiba akkor fordul elő, ha a kérelem fejlécének mérete olyan nagy, hogy meghaladja a maximálisan megengedett méretet. Javasoljuk, hogy az SDK legújabb verzióját használja. Legalább a 3. x vagy 2. x verziót használja, mert ezek a verziók a fejléc méretének nyomon követését adja hozzá a kivételt jelző üzenethez.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A "kérelem fejléce túl nagy" üzenet jelenik meg, ha a munkamenet vagy a folytatási jogkivonat túl nagy. A következő szakaszok ismertetik a probléma okát és a megoldását az egyes kategóriákban.

### <a name="session-token-is-too-large"></a>A munkamenet-jogkivonat túl nagy

#### <a name="cause"></a>Ok:
A 400-es hibás kérelem valószínűleg azért fordul elő, mert a munkamenet-jogkivonat túl nagy. Ha a következő utasítások teljesülnek, a munkamenet-jogkivonat túl nagy:

* A hiba olyan pontokon történik, mint például a létrehozás, az olvasás és a frissítés, ahol nincs folytatólagos jogkivonat.
* A kivétel az alkalmazás módosítása nélkül indult el. A munkamenet tokenje nő, ahogy a partíciók száma nő a tárolóban. A partíciók száma nő, mert az adatmennyiség növekszik, vagy ha az átviteli sebesség növekszik.

#### <a name="temporary-mitigation"></a>Ideiglenes csökkentés: 
Indítsa újra az ügyfélalkalmazás alkalmazást az összes munkamenet-token alaphelyzetbe állításához. A munkamenet-token végül a problémát okozó korábbi méretre fog növekedni. A probléma teljes elkerüléséhez használja a következő szakaszban található megoldást.

#### <a name="solution"></a>Megoldás:
1. Kövesse a [.net v3](performance-tips-dotnet-sdk-v3-sql.md) vagy a [.NET v2](performance-tips.md) teljesítménnyel kapcsolatos tippek című cikk útmutatását. Alakítsa át az alkalmazást a közvetlen kapcsolódási mód használatára a Transmission Control Protocol (TCP) használatával. A TCP protokollal létesített közvetlen kapcsolati mód nem rendelkezik a fejléc méretére vonatkozó korlátozással, például a HTTP protokollal, így elkerülhető a probléma. Ügyeljen arra, hogy az SDK legújabb verzióját használja, amely a lekérdezési műveletekhez javításokat tartalmaz, ha a szolgáltatás-együttműködés nem érhető el.
1. Ha a TCP protokollal létesített közvetlen kapcsolati mód nem választható a számítási feladathoz, akkor az [ügyfél konzisztencia-szintjének](how-to-manage-consistency.md)módosításával enyhítheti azt. A munkamenet-jogkivonat csak a munkamenet konzisztenciájához használható, amely a Azure Cosmos DB alapértelmezett konzisztencia-szintje. Más konzisztencia-szintek nem használják a munkamenet tokenjét.

### <a name="continuation-token-is-too-large"></a>A folytatási jogkivonat túl nagy

#### <a name="cause"></a>Ok:
Az 400-es hibás kérelem olyan lekérdezési műveletekben fordul elő, ahol a folytatási tokent akkor használja a rendszer, ha a folytatási token túl nagyra nőtt, vagy ha a különböző lekérdezések eltérőek a folytatólagos token
    
#### <a name="solution"></a>Megoldás:
1. Kövesse a [.net v3](performance-tips-dotnet-sdk-v3-sql.md) vagy a [.NET v2](performance-tips.md) teljesítménnyel kapcsolatos tippek című cikk útmutatását. Alakítsa át az alkalmazást a közvetlen kapcsolati mód használatára a TCP protokollal. A TCP protokollal létesített közvetlen kapcsolati mód nem rendelkezik a fejléc méretére vonatkozó korlátozással, például a HTTP protokollal, így elkerülhető a probléma. 
1. Ha a TCP protokollal létesített közvetlen kapcsolati mód nem választható a munkaterhelés számára, állítsa be a `ResponseContinuationTokenLimitInKb` beállítást. Ezt a lehetőséget a v2-ben `FeedOptions` vagy a v3-ben is megtalálhatja `QueryRequestOptions` .

## <a name="next-steps"></a>Következő lépések
* A Azure Cosmos DB .NET SDK használatakor felmerülő problémák [diagnosztizálása és hibaelhárítása](troubleshoot-dot-net-sdk.md) .
* A [.net v3](performance-tips-dotnet-sdk-v3-sql.md) és a [.NET v2](performance-tips.md)teljesítményére vonatkozó irányelvek ismertetése.
