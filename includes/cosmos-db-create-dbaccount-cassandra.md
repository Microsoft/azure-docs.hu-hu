---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 10/01/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 30a7f3ae878cebcd1e58287fc59241651dac2bfd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94503895"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza az **erőforrás létrehozása** lehetőséget.
   
   ![Erőforrás létrehozása az Azure Portalon](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **új** lapon válassza az **adatbázisok**  >  **Azure Cosmos db** elemet.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-cassandra/create-nosql-db-databases-json-tutorial-1.png)
   
3. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos db-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg ugyanazt a nevet, mint a fiók neve|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport-nevet a fiókhoz. Az egyszerűség kedvéért használja az Azure Cosmos-fiók nevével megegyező nevet. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A fiók URI-ja az egyedi *Cassandra.Cosmos.Azure.com* lesz hozzáfűzve.<br><br>A fiók neve csak kisbetűket, számokat és kötőjeleket (-) tartalmazhat, és 3 – 31 karakter hosszúságú lehet.
    API|Cassandra|A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: Core (SQL) dokumentum-adatbázisokhoz, Gremlin, MongoDB, Azure Table és Cassandra-hoz. Minden API-hoz létre kell hoznia egy külön fiókot. <br><br>Válassza a **Cassandra** lehetőséget, mert ebben a rövid útmutatóban olyan táblát hoz létre, amely együttműködik a Cassandra API. <br><br>[További információ a Cassandra API-ról](../articles/cosmos-db/cassandra-introduction.md).|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.
    Kapacitásmód|Kiosztott átviteli sebesség vagy kiszolgáló nélküli|Válassza a **kiépített átviteli sebesség** lehetőséget egy fiók [kiépített átviteli](../articles/cosmos-db/set-throughput.md) módban való létrehozásához. [Kiszolgáló nélküli módban lévő](../articles/cosmos-db/serverless.md) fiók létrehozásához válassza a **kiszolgáló** nélküli lehetőséget.

    Válassza a **felülvizsgálat + létrehozás** lehetőséget. Kihagyhatja a **hálózatkezelés**, a **biztonsági mentés**, a **titkosítás** és a **címkék** szakaszt. 

    ![Az Azure Cosmos DB új fiók lapja](./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portálon megjelenik a **gratuláló oldal. A Azure Cosmos DB-fiókja létrejött**.

