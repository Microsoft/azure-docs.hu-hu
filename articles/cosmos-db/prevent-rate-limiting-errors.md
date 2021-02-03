---
title: A MongoDB-műveletekhez tartozó Azure Cosmos DB API-k arányának korlátozására vonatkozó hibák megakadályozása.
description: Megtudhatja, hogyan akadályozhatja meg Azure Cosmos DB API-ját a MongoDB-műveletekhez, hogy a rendszer az SSR (kiszolgálóoldali újrapróbálkozás) szolgáltatással kapcsolatos hibákat korlátozza.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507930"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>A MongoDB-műveletekre vonatkozó Azure Cosmos DB API-k arányának korlátozására vonatkozó hibák megelőzése
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A MongoDB-műveletek Azure Cosmos DB API-ját a ráta korlátozásával (16500/429) kapcsolatos hibák léphetnek fel, ha meghaladják a gyűjtemény átviteli korlátját (RUs). 

Engedélyezheti a kiszolgálóoldali újrapróbálkozás (SSR) szolgáltatást, és lehetővé teheti a kiszolgáló számára a műveletek automatikus megismétlését. A kéréseket a rendszer a fiókban lévő összes gyűjtemény rövid késleltetése után újrapróbálkozik. Ez a funkció kényelmes alternatíva a ráta-korlátozó hibák kezelésére az ügyfélalkalmazás számára.

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Navigáljon a MongoDB-fiókhoz tartozó Azure Cosmos DB API-hoz.

1. Nyissa meg a **szolgáltatások** ablaktáblát a **Beállítások** szakasz alatt.

1. Válassza a **kiszolgálóoldali újrapróbálkozás** lehetőséget.

1. Az **Engedélyezés** gombra kattintva engedélyezheti ezt a funkciót a fiókban lévő összes gyűjteményhez.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="A MongoDB Azure Cosmos DB API-hoz készült kiszolgálóoldali újrapróbálkozás funkciójának képernyőképe":::

## <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata

1. Ellenőrizze, hogy a SSR szolgáltatás már engedélyezve van-e a fiókjában:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Engedélyezés** Az adatbázis-fiókban található összes gyűjteményhez tartozó SSR. A módosítás érvénybe léptetése akár 15min is eltarthat.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
A következő parancs **letiltja** az adatbázis-fiókban lévő összes gyűjteményhez tartozó SSR-t, ha eltávolítja a "DisableRateLimitingResponses" elemet a képességek listából. A módosítás érvénybe léptetése akár 15min is eltarthat.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Gyakori kérdések
* Hogyan történik a kérelmek újrapróbálása?
    * A kéréseket a rendszer folyamatosan újrapróbálkozik (újra és újra), amíg a 60 másodperces időkorlát el nem éri. Ha elérte az időtúllépést, az ügyfél [ExceededTimeLimit-kivételt fog kapni (50)](mongodb-troubleshoot.md).
*  Hogyan figyelhető meg a SSR hatása?
    *  Megtekintheti a Cosmos DB metrikák ablaktáblán a kiszolgálóoldali újrapróbált hibák (429s) korlátozásának mértékét. Ne feledje, hogy ezek a hibák nem az ügyfélre mutatnak, amikor a SSR engedélyezve van, mivel azokat a rendszer kezeli és újrapróbálkozza. 
    *  A "estimatedDelayFromRateLimitingInMilliseconds" bejegyzést tartalmazó naplóbejegyzések a [Cosmos db erőforrás-naplókban](cosmosdb-monitor-resource-logs.md)kereshetők.
*  Befolyásolja a rendszer a konzisztencia szintjét?
    *  A SSR nem befolyásolja a kérelmek konzisztenciáját. A rendszer újrapróbálkozik a kérelmekkel, ha korlátozott a díjszabás (429-es hiba miatt). 
*  Befolyásolja-e az SSR bármilyen típusú hibát, amelyet az ügyfelem fogadni fog?
    *  Nem, a SSR csak a kiszolgáló-oldal újbóli kipróbálásával befolyásolja a sebesség-korlátozó hibákat (429s). Ezzel a szolgáltatással megakadályozható, hogy az ügyfélalkalmazás a ráta korlátozásával kapcsolatos hibákat kezeljen. Az összes [többi hiba](mongodb-troubleshoot.md) a-ügyfélre lép. 

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a gyakori hibák elhárításáról, tekintse meg ezt a cikket:

* [Az Azure Cosmos DB API-MongoDB kapcsolatos gyakori problémák elhárítása](mongodb-troubleshoot.md)
