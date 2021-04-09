---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 448c445f49fb4baa300ce6636288a39080da3baf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026345"
---
Az Adatkezelő lévő lekérdezések segítségével lekérheti és szűrheti az adatait.

1. Az Adatkezelő **elemek** lapjának tetején tekintse át az alapértelmezett lekérdezést `SELECT * FROM c` . Ez a lekérdezés lekérdezi és megjeleníti a tárolóban az azonosító alapján rendezett összes dokumentumot. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="A Adatkezelőban az alapértelmezett lekérdezés a * c közül":::
   
1. A lekérdezés módosításához válassza a **Szűrő szerkesztése** lehetőséget, cserélje le az alapértelmezett lekérdezést a értékre, `ORDER BY c._ts DESC` majd válassza a **szűrő alkalmazása** lehetőséget.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Az alapértelmezett lekérdezés módosítása az ORDER BY c._ts DESC hozzáadásával és a Szűrő alkalmazása lehetőségre való kattintással":::

   A módosított lekérdezés a dokumentumokat csökkenő sorrendben jeleníti meg az időbélyegző alapján, így most már a második dokumentum jelenik meg. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="A lekérdezés módosult c._ts DESC sorrendbe rendezéséhez, majd a szűrő alkalmazása elemre.":::

Ha már ismeri az SQL-szintaxist, megadhat bármilyen támogatott [SQL-lekérdezést](../articles/cosmos-db/sql-query-getting-started.md) a lekérdezési predikátum mezőben. A Adatkezelő használatával tárolt eljárásokat, UDF és a kiszolgálóoldali üzleti logikát kiváltó eseményindítókat is létrehozhat. 

A Adatkezelő egyszerű Azure Portal hozzáférést biztosít az API-kkal elérhető beépített programozott adathozzáférési funkciókhoz. A portálon az átviteli sebességet, a kulcsok és a kapcsolódási karakterláncok beszerzését, valamint a Azure Cosmos DB-fiók metrikáinak és SLA-ának áttekintését is használhatja.