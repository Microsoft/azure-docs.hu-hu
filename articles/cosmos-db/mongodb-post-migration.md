---
title: Áttelepítés utáni optimalizálás lépései Azure Cosmos DB API-MongoDB
description: Ez a dokumentum áttelepítés utáni optimalizálási technikákat biztosít a MongoDB Azure Cosmos DB APi-hoz a Mongo DB-hez.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: 055604c399aa1641e823f24af499102bfff3ed0e
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85263092"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>Áttelepítés utáni optimalizálási lépések Azure Cosmos DB API-MongoDB való használatakor

Miután áttelepítette a MongoDB-adatbázisban tárolt adatAzure Cosmos DB API-ját a MongoDB-hez, csatlakozhat Azure Cosmos DBhoz, és kezelheti az adatkezelési lehetőséget. Ez az útmutató az áttelepítés után megfontolandó lépéseket ismerteti. Az áttelepítési lépésekhez tekintse [meg a MongoDB áttelepítése a Azure Cosmos db API-ját a MongoDB oktatóanyagban](../dms/tutorial-mongodb-cosmos-db.md) .

Ebből az útmutatóból a következőket tudhatja meg:

- [Az alkalmazás összekötése](#connect-your-application)
- [Az indexelési házirend optimalizálása](#optimize-the-indexing-policy)
- [Globális terjesztés konfigurálása a MongoDB-hez Azure Cosmos DB API-hoz](#globally-distribute-your-data)
- [Konzisztencia szintjének beállítása](#set-consistency-level)

> [!NOTE]
> Az alkalmazás szintjén az egyetlen kötelező áttelepítés utáni lépés módosítja az alkalmazásban lévő kapcsolódási karakterláncot, hogy az új Azure Cosmos DB fiókra mutasson. Az összes többi áttelepítési lépés javasolt optimalizálás.
>

## <a name="connect-your-application"></a>Az alkalmazás összekötése

1. Új ablakban jelentkezzen be a [Azure Portal](https://www.portal.azure.com/)
2. A [Azure Portal](https://www.portal.azure.com/)a bal oldali ablaktáblán nyissa meg a **minden erőforrás** menüt, és keresse meg azt a Azure Cosmos db fiókot, amelyre áttelepítette az adatait.
3. Nyissa meg a **kapcsolatok karakterláncának** paneljét. A jobb oldali panel tartalmazza a fiókhoz való kapcsolódáshoz szükséges összes információt.
4. Használja a kapcsolódási adatokat az alkalmazás konfigurációjában (vagy más kapcsolódó helyeken), hogy tükrözze a Azure Cosmos DB API-ját az MongoDB való kapcsolódáshoz.
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="Kapcsolatok – karakterlánc":::

További részletekért tekintse meg a [MongoDB-alkalmazás Összekötése Azure Cosmos db](connect-mongodb-account.md) lapra.

## <a name="optimize-the-indexing-policy"></a>Az indexelési házirend optimalizálása

Alapértelmezés szerint a rendszer az összes adatmezőt automatikusan indexeli a Azure Cosmos DBba történő adatáttelepítés során. Sok esetben ez az alapértelmezett indexelési házirend elfogadható. Általánosságban elmondható, hogy az indexek eltávolítása optimalizálja az írási kérelmeket, és az alapértelmezett indexelési házirend (azaz automatikus indexelés) optimalizálja az olvasási kérelmeket.

Az indexeléssel kapcsolatos további információkért lásd: [adatindexelés Azure Cosmos db API-MongoDB](mongodb-indexing.md) , valamint a Azure Cosmos db cikkek [indexelése](index-overview.md) .

## <a name="globally-distribute-your-data"></a>Az adatai globális terjesztése

A Azure Cosmos DB világszerte minden [Azure-régióban](https://azure.microsoft.com/regions/#services) elérhető. Miután kiválasztotta az alapértelmezett konzisztencia-szintet a Azure Cosmos DB-fiókhoz, hozzárendelhet egy vagy több Azure-régiót (a globális terjesztési igényektől függően). A magas rendelkezésre állás és az üzletmenet folytonossága érdekében mindig javasoljuk, hogy legalább két régiót futtasson. Áttekintheti a [többrégiós központi telepítések költségeit Azure Cosmos DBban](optimize-cost-regions.md).

Az adattovábbítás globális elosztása érdekében tekintse [meg az Adatterjesztés globálisan Azure Cosmos db API-MongoDB](tutorial-global-distribution-mongodb.md)című témakört.

## <a name="set-consistency-level"></a>Konzisztencia szintjének beállítása

Azure Cosmos DB 5 jól meghatározott konzisztencia- [szintet](consistency-levels.md)kínál. A MongoDB és a Azure Cosmos DB konzisztencia-szintek közötti leképezésről a [konzisztencia-szintek és a Azure Cosmos db API](consistency-levels-across-apis.md)-k című cikkből tájékozódhat. Az alapértelmezett konzisztencia-szint a munkamenet konzisztenciáji szintje. A konzisztencia szintjének módosítása nem kötelező, és optimalizálható az alkalmazáshoz. A konzisztencia szintjének módosítása Azure Portal használatával:

1. Lépjen az **alapértelmezett konzisztencia** panelre a beállítások területen.
2. A [konzisztencia szintjének](consistency-levels.md) kiválasztása

A legtöbb felhasználó a konzisztencia szintjét az alapértelmezett munkamenet-konzisztencia beállításnál hagyja. Vannak azonban [rendelkezésre állási és teljesítménybeli kompromisszumok a különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md).

## <a name="next-steps"></a>További lépések

* [MongoDB-alkalmazás csatlakoztatása az Azure Cosmos DB-hez](connect-mongodb-account.md)
* [Kapcsolódás Azure Cosmos DB fiókhoz a Studio 3T használatával](mongodb-mongochef.md)
* [Olvasási műveletek globális elosztása a Azure Cosmos DB API-MongoDB](mongodb-readpreference.md)
* [Adatok lejárttá tétele a MongoDB-hez készült Azure Cosmos DB API-val](mongodb-time-to-live.md)
* [Azure Cosmos DB konzisztenciáji szintjei](consistency-levels.md)
* [Indexelés az Azure Cosmos DB-ben](index-overview.md)
* [Az Azure Cosmos DB kérelemegységei](request-units.md)
