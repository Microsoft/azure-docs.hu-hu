---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994550"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza az **erőforrás létrehozása** lehetőséget.
   
   ![Erőforrás létrehozása az Azure Portalon](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **új** lapon válassza az **adatbázisok**  >  **Azure Cosmos db** elemet.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos db-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg ugyanazt a nevet, mint a fiók neve|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport-nevet a fiókhoz. Az egyszerűség kedvéért használja ugyanazt a nevet, mint a Azure Cosmos DB fiók nevét. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A fiók URI-ja az egyedi *Mongo.Cosmos.Azure.com* lesz hozzáfűzve.<br><br>A fiók neve csak kisbetűket, számokat és kötőjeleket (-) tartalmazhat, és 3 – 31 karakter hosszúságú lehet.
    API|Azure Cosmos DB a Mongo DB API-hoz|A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít a dokumentumok adatbázisaihoz, a Gremlin Azure Cosmos DB, a Mongo DB API-hoz a dokumentum-adatbázisok, az Azure Table és a Cassandra esetében. Jelenleg külön fiókot kell létrehoznia minden egyes API-hoz. <br><br>Válassza ki **Azure Cosmos db a MONGO db API**  -hoz, mert ebben a rövid útmutatóban olyan gyűjteményt hoz létre, amely együttműködik a MongoDB.<br><br>[További információ a Azure Cosmos db for MONGODB API-ról](../articles/cosmos-db/mongodb-introduction.md).|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.|
    Kapacitásmód|Kiosztott átviteli sebesség vagy kiszolgáló nélküli|Válassza a **kiépített átviteli sebesség** lehetőséget egy fiók [kiépített átviteli](../articles/cosmos-db/set-throughput.md) módban való létrehozásához. [Kiszolgáló nélküli módban lévő](../articles/cosmos-db/serverless.md) fiók létrehozásához válassza a **kiszolgáló** nélküli lehetőséget.<br><br>**Megjegyzés**: a kiszolgáló nélküli fiókok csak a MongoDB API 3,6-es verzióját támogatják. Az 3,2-as verzió választása esetén a rendszer kiépített átviteli üzemmódban kényszeríti a fiókot.

    Válassza a **felülvizsgálat + létrehozás** lehetőséget. Kihagyhatja a **hálózat** és **címkék** szakaszt. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portálon megjelenik a **Gratulálunk! A Mongo DB API-fiókhoz tartozó Azure Cosmos DB kész** oldal.

    ![Az Azure Portal Értesítések panelje](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
