---
title: fájl belefoglalása
description: fájl belefoglalása
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 08/07/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: f80efbac256871af073354f23317c447d6a85f1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "70020174"
---
Az új tárolóba az Adatkezelő használatával adhat hozzá adatait.

1. A **adatkezelő** bontsa ki a **feladatok** adatbázist, és bontsa ki az **elemek** tárolót. Válassza az **elemek** lehetőséget, majd válassza az **új elem** lehetőséget.

   ![Új dokumentumok létrehozása az Azure Portal Adatkezelőjében](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-new-document.png)
  
2. Most adjon hozzá egy dokumentumot a tárolóhoz az alábbi struktúrával.

     ```json
     {
         "id": "1",
         "category": "personal",
         "name": "groceries",
         "description": "Pick up apples and strawberries.",
         "isComplete": false
     }
     ```

3. Miután hozzáadta a JSON-t a **dokumentumok** laphoz, válassza a **Mentés** lehetőséget.

    ![Másolja a JSON-adatfájlt, és válassza a Mentés Adatkezelő a Azure Portal](./media/cosmos-db-create-sql-api-add-sample-data/azure-cosmosdb-data-explorer-save-document.png)

4.  Hozzon létre és mentsen még egy dokumentumot, amelyben egyedi értéket szúr be az `id` tulajdonság számára, és tetszés szerint módosítja a többi tulajdonságot. Mivel az Azure Cosmos DB nem kötelezi egy adott adatséma használatára, új dokumentumaihoz bármilyen struktúrát választhat.