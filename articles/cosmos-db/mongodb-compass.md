---
title: Kapcsolódás Azure Cosmos DB az iránytű használatával
description: Megtudhatja, hogyan tárolhatja és kezelheti Azure Cosmos DBban az MongoDB Compass használatával.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 06/05/2020
author: jasonwhowell
ms.author: jasonh
ms.openlocfilehash: c005c4fc15f7c7955a7254f386597eee01800da5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126600"
---
# <a name="use-mongodb-compass-to-connect-to-azure-cosmos-dbs-api-for-mongodb"></a>A MongoDB Compass használata a Azure Cosmos DB API-MongoDB való kapcsolódáshoz
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ez az oktatóanyag azt mutatja be, hogyan használható a [MongoDB Compass](https://www.mongodb.com/products/compass) a Cosmos DBban tárolt adattároláshoz és/vagy azok kezeléséhez. Ehhez a MongoDB a Azure Cosmos DB API-ját használjuk. Az Ön számára ismeretlen, az iránytű a MongoDB grafikus felhasználói felülete. Általában az adatai megjelenítéséhez, alkalmi lekérdezések futtatásához, valamint az adatkezeléshez is használható.

Cosmos DB a Microsoft globálisan elosztott, többmodelles adatbázis-szolgáltatása. Gyorsan létrehozhat és lekérdezheti a dokumentum-, kulcs/érték és gráf típusú adatbázisokat, amelyek mindegyike kihasználja a globális elosztási és horizontális méretezési képességeket Cosmos DB középpontjában.

## <a name="pre-requisites"></a>Előfeltételek

Ha a MongoDB Compass használatával szeretne csatlakozni a Cosmos DB-fiókjához, a következőket kell tennie:

* Az [iránytű](https://www.mongodb.com/download-center/compass?jmp=hero) letöltése és telepítése
* A Cosmos DB a [kapcsolatok karakterláncával](connect-mongodb-account.md) kapcsolatos információk

> [!NOTE]
> Jelenleg a MongoDB Compass legújabb verziója támogatja az 3,2-es verziójú MongoDB-kiszolgáló API Azure Cosmos DB-ját. A MongoDB Compass még nem támogatott a 3,6-es kiszolgáló verziójával. 

## <a name="connect-to-cosmos-dbs-api-for-mongodb"></a>Kapcsolódás Cosmos DB MongoDB API-hoz

Ha Cosmos DB-fiókját az iránytűhöz szeretné kapcsolni, kövesse az alábbi lépéseket:

1. A Azure Cosmos DB API-MongoDB konfigurált Cosmos-fiókhoz tartozó kapcsolatok adatainak beolvasása az [itt](connect-mongodb-account.md)leírt utasítások alapján.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-connection.png" alt-text="Képernyőfelvétel a kapcsolatok sztring panelről":::

2. Kattintson a gombra, amely a **vágólapra másolja a vágólapra** az **elsődleges/másodlagos kapcsolódási karakterlánc** mellett Cosmos db. A gombra kattintva a rendszer a teljes kapcsolódási karakterláncot a vágólapra másolja.

    :::image type="content" source="./media/mongodb-compass/mongodb-connection-copy.png" alt-text="Képernyőfelvétel a kapcsolatok sztring panelről":::

3. Nyissa meg az iránytűt az asztalon/gépen, és kattintson a **Kapcsolódás** , majd a **Kapcsolódás..** . elemre.

4. Az iránytű automatikusan felderíti a kapcsolati karakterláncot a vágólapon, és rákérdez, hogy szeretné-e használni a kapcsolódást. Kattintson az **Igen** gombra, ahogy az alábbi képernyőképen is látható.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-detect.png" alt-text="Képernyőfelvétel a kapcsolatok sztring panelről":::

5. Ha a fenti lépésben az **Igen** gombra kattint, a kapcsolati karakterlánc adatai automatikusan fel lesznek töltve. Távolítsa el az automatikusan kitöltött értéket a **replikakészlet neve** mezőben, így biztosítva, hogy a mező üres maradjon.

    :::image type="content" source="./media/mongodb-compass/mongodb-compass-replica.png" alt-text="Képernyőfelvétel a kapcsolatok sztring panelről":::

6. Kattintson a lap alján található **kapcsolat** elemre. A Cosmos DB fiókjának és adatbázisainak most már láthatónak kell lenniük a MongoDB-iránytűn belül.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.
