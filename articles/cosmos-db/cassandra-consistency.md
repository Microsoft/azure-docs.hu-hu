---
title: Apache Cassandra és Azure Cosmos DB konzisztencia-szintek
description: Apache Cassandra és Azure Cosmos DB konzisztencia-szintek.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 3107610215d5b37c43124ce4129b2eb5437e3b62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516854"
---
# <a name="apache-cassandra-and-azure-cosmos-db-cassandra-api-consistency-levels"></a>Az Apache Cassandra és a Azure Cosmos DB Cassandra API a konzisztencia szintje
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DBtól eltérően az Apache Cassandra nem biztosít natív módon pontosan meghatározott konzisztencia-garanciákat. Az Apache Cassandra Ehelyett írási konzisztencia-szintet és olvasási konzisztencia-szintet biztosít a magas rendelkezésre állás, a konzisztencia és a késési kompromisszumok engedélyezéséhez. Azure Cosmos DB Cassandra API használatakor:

* Az Apache Cassandra írási konzisztencia-szintje az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintre van leképezve. Az írási művelet (CL) konzisztenciája nem módosítható kérelmek alapján.

* Azure Cosmos DB dinamikusan leképezi a Cassandra-ügyfél illesztőprogramjában megadott olvasási konzisztencia-szintet az olvasási kérelemben dinamikusan konfigurált Azure Cosmos DB konzisztencia-szintek egyikére.

## <a name="multi-region-writes-vs-single-region-writes"></a>Többrégiós írások vs egyrégiós írások

Az Apache Cassandra Database alapértelmezés szerint egy több főkiszolgálós rendszer, és nem biztosít beépített lehetőséget az egyrégiós, többrégiós replikációval való olvasáshoz. A Azure Cosmos DB azonban kulcsrakész képességet biztosít egyetlen régióval vagy [többrégiós](how-to-multi-master.md) írási konfigurációval. Az egyik lehetőség, hogy egyetlen régióból álló írási konfigurációt is kiválaszthat több régióban, a régiók közötti ütközések elkerülését, és lehetővé teszi a több régión belüli erős konzisztencia fenntartását. 

Az egyrégiós írásokkal erős konzisztenciát tarthat fenn, miközben az [automatikus feladatátvételsel](high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage)továbbra is magas rendelkezésre állást biztosít a régiók között. Ebben a konfigurációban továbbra is kihasználhatja az adatterületet az olvasási késés csökkentése érdekében azáltal, hogy az adott kérelmek alapján a végleges konzisztencia mértékét visszaminősíti. Ezen képességek mellett a Azure Cosmos DB platform is lehetővé teszi a [zóna-redundancia](high-availability.md#availability-zone-support) engedélyezését a régió kiválasztásakor. Így a natív Apache Cassandra-vel ellentétben a Azure Cosmos DB lehetővé teszi, hogy a CAP [-tétel kereskedelmi spektrumát](consistency-levels.md#rto) nagyobb részletességgel navigálja.

## <a name="mapping-consistency-levels"></a>Konzisztenciaszintek leképezése

A Azure Cosmos DB platform öt jól definiált, üzleti felhasználásra vonatkozó konzisztencia-beállítást biztosít a replikálás és a [Cap-tétel](https://en.wikipedia.org/wiki/CAP_theorem) és a [PACLC-tétel](https://en.wikipedia.org/wiki/PACELC_theorem)által meghatározott kompromisszumok tekintetében. Mivel ez a megközelítés jelentősen eltér az Apache Cassandratől, javasoljuk, hogy tekintse át és Ismerje meg a [dokumentációban](consistency-levels.md)Azure Cosmos db konzisztencia-beállításokat, vagy tekintse meg ezt a rövid [videós](https://www.youtube.com/watch?v=t1--kZjrG-o) útmutatót a Azure Cosmos db platform konzisztencia-beállításainak megismeréséhez.

Az alábbi táblázat az Apache Cassandra és a Azure Cosmos DB konzisztencia-szintek közötti lehetséges leképezéseket szemlélteti Cassandra API használatakor. Ez az egyetlen régió, a többrégiós olvasások és az egyrégiós írások, valamint a többrégiós írások konfigurációit jeleníti meg.

> [!NOTE]
> Ezek nem pontos leképezések. Ehelyett a legközelebb elérhetővé tettük az Apache Cassandra-t, és disambiguated a jobb oldali oszlopban szereplő minőségi különbségeket. A fentiekben említettek szerint a Azure Cosmos DB [konzisztencia-beállításainak](consistency-levels.md)áttekintését javasoljuk. 

:::image type="content" source="./media/cassandra-consistency/account.png" alt-text="Cassandra konzisztencia-fiók szintjének leképezése" lightbox="./media/cassandra-consistency/account.png" :::

:::image type="content" source="./media/cassandra-consistency/dynamic.png" alt-text="Cassandra konzisztencia dinamikus leképezése" lightbox="./media/cassandra-consistency/dynamic.png" :::

Ha az Azure Cosmos-fiókja nem az erős konzisztencia mellett van konfigurálva, akkor megtudhatja, hogy az ügyfelek erős és konzisztens olvasási feladatokat szereznek a számítási feladatokhoz a *Probabilistically határos* elavulás (PBS) metrikájának megtekintésével. Ez a mérőszám a Azure Portalban érhető el. További információért lásd: a [Probabilistically kötött elévülés (PBS) mérőszámának figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A valószínűséggel határos elavulás azt mutatja, hogy a végleges konzisztencia milyen módon lehetséges. Ez a mérőszám betekintést nyújt abba, hogy milyen gyakran lehet erősebb konzisztencia, mint az Azure Cosmos-fiókjában jelenleg konfigurált konzisztencia-szint. Más szóval az írási és olvasási régiók kombinációjának nagy mértékben konzisztens olvasásának valószínűsége (ezredmásodpercben mérve) látható.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Cosmos DB globális eloszlásáról és konzisztenciáji szintjeiről:

* [A globális terjesztés áttekintése](distribute-data-globally.md)
* [Konzisztencia-szint áttekintése](consistency-levels.md)
* [Magas rendelkezésre állás](high-availability.md)
