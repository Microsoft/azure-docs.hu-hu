---
title: Rendszerdokumentum-tulajdonságok elérése Azure Cosmos DB gráfon keresztül
description: Útmutató Cosmos DB rendszerdokumentum tulajdonságainak olvasásához és írásához a Gremlin API-n keresztül
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: 61814082ebe9828a08da1e8786890b500c239082
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081841"
---
# <a name="system-document-properties"></a>Rendszerdokumentum tulajdonságai
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos db olyan [rendszertulajdonságokkal](/rest/api/cosmos-db/databases) rendelkezik, mint a,,, ```_ts``` ```_self``` ```_attachments``` ```_rid``` és ```_etag``` minden dokumentumon. Ezenkívül a Gremlin-motor az élekhez az ```inVPartition``` és az ```outVPartition``` tulajdonságokat is hozzárendeli. Alapértelmezés szerint ezek a tulajdonságok a bejáráshoz érhetők el. Azonban a Gremlin bejárásakor lehetséges, hogy a megadott tulajdonságokat vagy azok mindegyikét is tartalmazza.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-címke

Ez a tulajdonság az optimista egyidejűség vezérléséhez szükséges. Ha az alkalmazásnak néhány különálló bejárásokat kell megszüntetnie a műveletet, az eTag tulajdonság használatával elkerülhető az adatvesztés az egyidejű írásokban.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

Ha a gyűjteményben engedélyezve van a dokumentum lejárata ```ttl``` , és a dokumentumok tulajdonsága be van állítva, akkor ez a tulajdonság a Gremlin bejárásakor lesz elérhető a normál csúcspont vagy Edge tulajdonságként. ```ProjectionStrategy``` nincs szükség az élettartam-tulajdonság expozíciójának engedélyezésére.

Az alábbi bejárással létrehozott csúcs **123 másodperc** múlva automatikusan törölve lesz.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Következő lépések
* [A Cosmos DB optimista egyidejűség-kezelése](faq.md#how-does-the-sql-api-provide-concurrency)
* Élettartam [(TTL)](time-to-live.md) Azure Cosmos db
