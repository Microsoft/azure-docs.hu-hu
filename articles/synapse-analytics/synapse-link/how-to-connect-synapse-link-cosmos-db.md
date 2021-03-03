---
title: Csatlakozás az Azure Cosmos DB-hez készült Azure Synapse Linkhez
description: Megtudhatja, hogyan csatlakoztathat egy Azure Cosmos DB adatbázist egy Azure szinapszis-munkaterülethez az Azure szinapszis hivatkozásával.
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 03/02/2021
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: a8626174e157e79d0250824e0a3cfa97141d50dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695039"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>Csatlakozás az Azure Cosmos DB-hez készült Azure Synapse Linkhez

Ez a cikk azt ismerteti, hogyan férhet hozzá egy Azure Cosmos DB-adatbázishoz az Azure szinapszis Analytics studióból az Azure szinapszis hivatkozásával.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt a munkaterülethez csatlakoztatná egy Azure Cosmos DB adatbázist, a következőkre lesz szüksége:

* Meglévő Azure Cosmos DB adatbázis, vagy hozzon létre egy új fiókot a gyors útmutató [: Azure Cosmos db-fiók kezelése](../../cosmos-db/how-to-manage-database-account.md)című témakörben leírtak szerint.
* Meglévő Azure szinapszis-munkaterület, vagy hozzon létre egy új munkaterületet a gyors útmutató [: szinapszis-munkaterület létrehozása](../quickstart-create-workspace.md)című témakör lépéseit követve.

## <a name="enable-synapse-link-on-an-azure-cosmos-db-database-account"></a>Szinapszis-hivatkozás engedélyezése Azure Cosmos DB adatbázis-fiókon

Ha nagy léptékű elemzést szeretne Azure Cosmos DB a működési teljesítményének befolyásolása nélkül, javasoljuk, hogy a Azure Cosmos DB számára engedélyezze a szinapszis-hivatkozást. A szinapszis hivatkozása HTAP képességet biztosít egy tárolóhoz, és beépített támogatást nyújt az Azure Szinapszisban.

## <a name="go-to-synapse-studio"></a>Ugrás a szinapszis Studióra

Az Azure szinapszis munkaterületen válassza a **szinapszis Studio elindítása** lehetőséget. A szinapszis Studio kezdőlapján **válassza az** adatObject Explorer.

## <a name="connect-an-azure-cosmos-db-database-to-an-azure-synapse-workspace"></a>Azure Cosmos DB-adatbázis összekötése egy Azure-beli szinapszis-munkaterülettel

Azure Cosmos DB adatbázis csatlakoztatása társított szolgáltatásként történik. Egy Azure Cosmos DB társított szolgáltatással böngészhet és megtekintheti az adatApache Spark az Azure szinapszis Analytics vagy az SQL Azure Cosmos DBba való beolvasását és írását.

Az adatok Object Explorer az alábbi lépéseket követve közvetlenül csatlakozhat egy Azure Cosmos DB-adatbázishoz:

1. Válassza ki az **+** ikont a közelében. 
1. Válassza **a Kapcsolódás külső adatforráshoz** lehetőséget.
1. Válassza ki azt az API-t, amelyhez csatlakozni szeretne, például az **SQL API** -t vagy **a MongoDB API**-ját.
1. Válassza a **Folytatás** lehetőséget.
1. A társított szolgáltatás neveként adjon meg egy rövid nevet. A név megjelenik az adatObject Explorerban, és az Azure szinapszis-futtatókörnyezetek használják az adatbázishoz és a tárolóhoz való kapcsolódáshoz.
1. Válassza ki a **Azure Cosmos db fiók nevét** és az **adatbázis nevét**.
1. Választható Ha nincs megadva régió, a rendszer az Azure szinapszis futásidejű műveleteit arra a legközelebbi régió felé irányítja, ahol az analitikai tár engedélyezve van. Manuálisan is beállíthatja azt a régiót, amelyet a felhasználók használni tudnak a Azure Cosmos DB analitikus tároló eléréséhez. Válassza a **további kapcsolatok tulajdonságai** lehetőséget, majd válassza az **új** lehetőséget. A **tulajdonságnév** területen adja meg a **PreferredRegions** nevet. Állítsa be az **értéket** a kívánt régióra, például **WestUS2**. (A szavak és a szám között nincsenek szóközök.)
1. Válassza a **Létrehozás** lehetőséget.

Azure Cosmos DB adatbázisok a **Azure Cosmos db** szakasz **csatolt** lapján jelennek meg. A Azure Cosmos DB lehetővé teszi, hogy a HTAP-kompatibilis tárolót egy csak OLTP tárolóból megkülönböztesse a következő ikonok használatával:

**OLTP-tároló**:

![A OLTP tároló ikonját megjelenítő vizualizáció.](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**HTAP-kompatibilis tároló**:

![A HTAP tároló ikonját megjelenítő vizualizáció.](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>Gyors interakció a kód által generált műveletekkel

Ha a jobb gombbal rákattint egy tárolóra, megtekintheti a Spark-vagy SQL-futtatókörnyezetet kiváltó kézmozdulatok listáját. A tárolóba való írás a Azure Cosmos DB tranzakciós tárolójával történik, és a rendszer a kérelmek egységeit fogja felhasználni.  

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, mi támogatott az Azure szinapszis és a Azure Cosmos DB között?](./concept-synapse-link-cosmos-db-support.md)
* [Privát végpontok konfigurálása Azure Cosmos DB analitikus tárolóhoz](../../cosmos-db/analytical-store-private-endpoints.md)
* [Ismerje meg, hogyan kérdezheti le az analitikai áruházat a Spark használatával](./how-to-query-analytical-store-spark.md)