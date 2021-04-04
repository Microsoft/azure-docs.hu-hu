---
title: A MongoDB Azure Cosmos DB API konzisztenciáji szintjeinek megfeleltetése
description: A MongoDB Azure Cosmos DB API konzisztenciáji szintjeinek leképezése.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: e8ac3e376c8d67e82def3a57910707c6b1433912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93333154"
---
# <a name="consistency-levels-for-azure-cosmos-db-and-the-api-for-mongodb"></a>A Azure Cosmos DB és a MongoDB API-k konzisztenciáji szintjei
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

A Azure Cosmos DBtól eltérően a natív MongoDB nem biztosít pontosan meghatározott konzisztencia-garanciát. Ehelyett a natív MongoDB lehetővé teszi a felhasználók számára a következő konzisztencia-garanciák konfigurálását: egy írási aggály, egy olvasási probléma és a isMaster direktíva, hogy az olvasási műveleteket elsődleges vagy másodlagos replikára irányítsa a kívánt konzisztencia-szint elérése érdekében.

Ha Azure Cosmos DB API-ját használja a MongoDB, a MongoDB-illesztőprogram az írási régiót elsődleges replikaként kezeli, és minden más régió olvasási replika. Kiválaszthatja, hogy melyik régiót rendeli hozzá az Azure Cosmos-fiókjához elsődleges replikaként.

> [!NOTE]
> Az Azure Cosmos DB alapértelmezett konzisztencia-modellje a munkamenet. A munkamenet egy ügyfél-központú konzisztencia-modell, amelyet Cassandra vagy MongoDB sem natív módon támogat. További információ arról, hogy melyik konzisztencia-modell válassza a következőt: [Azure Cosmos db a konzisztencia szintjei](consistency-levels.md)

A MongoDB Azure Cosmos DB API-ját használja:

* Az írási aggály az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintre van leképezve.

* Azure Cosmos DB dinamikusan leképezi a MongoDB-ügyfél illesztőprogramja által megadott olvasási aggályt az olvasási kérelemben dinamikusan konfigurált Azure Cosmos DB konzisztencia-szintek egyikére.  

* Az Azure Cosmos-fiókjához tartozó adott régiót "elsődlegesként" is láthatja, ha az első írható régióként teszi a régiót. 

## <a name="mapping-consistency-levels"></a>Konzisztenciaszintek leképezése

Az alábbi táblázat bemutatja, hogyan vannak leképezve a natív MongoDB írási/olvasási problémái az Azure Cosmos konzisztencia-szintjére, ha Azure Cosmos DB API-ját használja a MongoDB számára:

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB konzisztencia-modell leképezése" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

Ha az Azure Cosmos-fiókja nem az erős konzisztencia mellett van konfigurálva, akkor megtudhatja, hogy az ügyfelek erős és konzisztens olvasási feladatokat szereznek a számítási feladatokhoz a *Probabilistically határos* elavulás (PBS) metrikájának megtekintésével. Ez a mérőszám a Azure Portalban érhető el. További információért lásd: a [Probabilistically kötött elévülés (PBS) mérőszámának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A valószínűséggel határos elavulás azt mutatja, hogy a végleges konzisztencia milyen módon lehetséges. Ez a mérőszám betekintést nyújt abba, hogy milyen gyakran lehet erősebb konzisztencia, mint az Azure Cosmos-fiókjában jelenleg konfigurált konzisztencia-szint. Más szóval az írási és olvasási régiók kombinációjának nagy mértékben konzisztens olvasásának valószínűsége (ezredmásodpercben mérve) látható.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Cosmos DB globális eloszlásáról és konzisztenciáji szintjeiről:

* [A globális terjesztés áttekintése](distribute-data-globally.md)
* [Konzisztencia-szint áttekintése](consistency-levels.md)
* [Magas rendelkezésre állás](high-availability.md)
