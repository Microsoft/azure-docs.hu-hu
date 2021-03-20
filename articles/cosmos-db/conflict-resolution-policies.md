---
title: Ütközés-feloldási típusok és megoldási szabályzatok a Azure Cosmos DB
description: Ez a cikk a Azure Cosmos DB ütközési kategóriáit és ütközés-feloldási házirendjeit ismerteti.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ba55d88de3a5a4087db30613b22a7d2441de9be1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334378"
---
# <a name="conflict-types-and-resolution-policies-when-using-multiple-write-regions"></a>Ütközési típusok és feloldási házirendek több írási régió használata esetén
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az ütközések és az ütközések feloldására szolgáló házirendek akkor alkalmazhatók, ha a Azure Cosmos DB-fiók több írási régióval van konfigurálva.

Több írási régióval konfigurált Azure Cosmos-fiókok esetén a frissítési ütközések akkor fordulhatnak elő, ha az írók egyidejűleg frissítik ugyanazt az elemeket több régióban. A frissítési ütközések a következő három típusból lehetnek:

* **Ütközések beszúrása**: ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás egyidejűleg két vagy több régióban ugyanazt az egyedi indexszel rendelkező elemet szúr be. Előfordulhat például, hogy ez az ütközés egy ID tulajdonsággal együtt fordul elő.

* **Ütközések cseréje**: ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás két vagy több régióban egyszerre frissíti ugyanazt az elemeket.

* **Ütközések törlése**: ezek az ütközések akkor fordulhatnak elő, ha egy alkalmazás egyidejűleg törli az egyik régióban lévő elemeket, és egy másik régióban frissíti azt.

## <a name="conflict-resolution-policies"></a>Ütközésfeloldási szabályzatok

Azure Cosmos DB rugalmas, házirend által vezérelt mechanizmust kínál az írási ütközések feloldásához. Az Azure Cosmos-tárolóban két ütközés-feloldási szabályzat közül választhat:

* **Utolsó írási WINS (LWW)**: Ez a megoldási házirend alapértelmezés szerint rendszer által definiált időbélyeg-tulajdonságot használ. Ez a Time-Sync Clock protokollon alapul. Ha az SQL API-t használja, megadhat bármilyen más egyéni numerikus tulajdonságot (például egy időbélyeg saját fogalmát) az ütközés feloldásához. Az egyéni numerikus tulajdonságokat az *ütközés feloldási útvonalnak* is nevezzük. 

  Ha két vagy több elem ütközik az INSERT vagy a Replace művelettel, az ütközés feloldási útvonalának legmagasabb értékét tartalmazó elem lesz a nyertes. A rendszer meghatározza a nyertest, ha több elemnek ugyanaz a numerikus értéke az ütközési feloldási útvonalhoz. Minden régió egyetlen nyertesnek fog megbirkózni, és a véglegesített elem azonos verziójával végződik. Ha törlési ütközések merülnek fel, a törölt verzió mindig az INSERT vagy a Replace ütközések között nyer. Ennek az eredménynek az az oka, hogy az ütközés feloldási elérési útjának értéke nem számít.

  > [!NOTE]
  > Az utolsó írási WINS az alapértelmezett ütközés-feloldási házirend, és a következő API-k időbélyegét használja `_ts` : SQL, MongoDB, Cassandra, Gremlin és Table. Az egyéni numerikus tulajdonság csak az SQL API esetében érhető el.

  További információért lásd: [LWW ütközési megoldási szabályzatokat használó példák](how-to-manage-conflicts.md).

* **Egyéni**: Ez a megoldási házirend az alkalmazás által definiált szemantikai szabályokhoz készült az ütközések egyeztetéséhez. Amikor beállítja ezt a házirendet az Azure Cosmos-tárolón, regisztrálnia kell egy *egyesítéssel tárolt eljárást* is. Ezt az eljárást automatikusan meghívja a rendszer, ha ütközések észlelhetők a kiszolgálón található adatbázis-tranzakció alatt. A rendszer pontosan egyszer garantálja az egyesítési eljárás végrehajtását a kötelezettségvállalási protokoll részeként.  

  Ha a tárolót az egyéni feloldási lehetőséggel konfigurálja, és nem tud regisztrálni egy egyesítési eljárást a tárolón, vagy az egyesítési eljárás futásidejű kivételt jelez, az ütközések az *ütközések hírcsatornába* íródnak. Az alkalmazásnak ezután manuálisan kell feloldania az ütközéseket a hírcsatornában. További információ: példák az [Egyéni megoldási szabályzat használatára és az ütközések hírcsatornájának használatára](how-to-manage-conflicts.md).

  > [!NOTE]
  > Az egyéni ütközés-feloldási szabályzat csak az SQL API-fiókok esetében érhető el.

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan konfigurálhat ütközés-feloldási házirendeket:

* [Több írási régió konfigurálása az alkalmazásokhoz](how-to-multi-master.md)
* [Az ütközés-feloldási házirendek kezelése](how-to-manage-conflicts.md)
* [Az ütközések hírcsatornájának beolvasása](how-to-manage-conflicts.md#read-from-conflict-feed)
