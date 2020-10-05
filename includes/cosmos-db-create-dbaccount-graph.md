---
title: fájlbefoglalás
description: fájlbefoglalás
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/27/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 1f36f2f26b65fc6078b99a43b6f0bae9a8a8e9f5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91578383"
---
1. Egy új böngészőablakban jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A bal oldali menüben válassza az **erőforrás létrehozása**lehetőséget.
   
   ![Erőforrás létrehozása az Azure Portalon](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-0.png)
   
3. Az **új** lapon válassza az **adatbázisok**  >  **Azure Cosmos db**elemet.
   
   ![Az Azure Portal Adatbázisok panelje](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)
   
   > [!TIP]
   > Ha nem látja Azure Cosmos DB a listában, csak írja be a lap tetején található keresőmezőbe, majd nyomja le az _ENTER_ billentyűt. 

3. A **Azure Cosmos db fiók létrehozása** lapon adja meg az új Azure Cosmos db-fiók beállításait. 
 
    Beállítás|Érték|Leírás
    ---|---|---
    Előfizetés|Az Ön előfizetése|Válassza ki az Azure Cosmos DB-fiókhoz használni kívánt Azure-előfizetést. 
    Erőforráscsoport|Új létrehozása<br><br>Ezután adja meg ugyanazt a nevet, mint a fiók neve|Válassza az **Új létrehozása** lehetőséget. Ezután adjon meg egy új erőforráscsoport-nevet a fiókhoz. Az egyszerűség kedvéért használja ugyanazt a nevet, mint a Azure Cosmos DB fiók nevét. 
    Fiók neve|Adjon meg egy egyedi nevet|Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához. A fiók URI-ja az egyedi *Gremlin.Azure.com* lesz hozzáfűzve.<br><br>A fiók neve csak kisbetűket, számokat és kötőjeleket (-) tartalmazhat, és 3 – 31 karakter hosszúságú lehet.
    API|Gremlin (gráf)|A létrehozni kívánt fiók típusát az API határozza meg. A Azure Cosmos DB öt API-t biztosít: Core (SQL) dokumentum-adatbázisokhoz, Gremlin, MongoDB, Azure Table és Cassandra-hoz. Minden API-hoz létre kell hoznia egy külön fiókot. <br><br>Válassza a **Gremlin (gráf)** lehetőséget, mivel ebben a rövid útmutatóban olyan táblát hoz létre, amely együttműködik a Gremlin API-val. <br><br>[További információ a GREMLIN API-ról](../articles/cosmos-db/graph-introduction.md).|
    Hely|Válassza ki a felhasználóihoz legközelebb eső régiót|Válassza ki az Azure Cosmos DB-fiókot üzemeltetéséhez használni kívánt földrajzi helyet. Használja a felhasználókhoz legközelebb lévő helyet, hogy a lehető leggyorsabb hozzáférést biztosítsa az adatokhoz.

    Válassza a **felülvizsgálat + létrehozás**lehetőséget. Kihagyhatja a **hálózat** és **címkék** szakaszt. 

    ![Az Azure Cosmos DB Új fiók lapja](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. A fiók létrehozása eltarthat néhány percig. Várja meg, amíg a portálon megjelenik a **Gratulálunk! A Azure Cosmos DB fiók létrehozása** lap.
   
   ![Azure Cosmos DB fiók létrehozva lap](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)

