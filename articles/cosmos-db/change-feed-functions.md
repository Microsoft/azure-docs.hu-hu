---
title: Azure Cosmos DB változási hírcsatorna használata a Azure Functions
description: Azure Functions használata a Azure Cosmos DB változási csatornához való kapcsolódáshoz. Később létrehozhat olyan reaktív Azure-függvényeket, amelyek minden új eseménynél aktiválva vannak.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7f4903cf29f15132db91e47d78efe5a556efd937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340243"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Kiszolgáló nélküli eseményvezérelt architektúrák Azure Cosmos DB és Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Functions biztosítja a legegyszerűbb módot a [változási csatornához](change-feed.md)való kapcsolódásra. Létrehozhat olyan kis reaktív Azure Functions, amelyek automatikusan aktiválódik az Azure Cosmos-tároló változási csatornájának minden új eseményén.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="A kiszolgáló nélküli eseményvezérelt függvények a Azure Functions eseményindítóval működnek Cosmos DB" border="false":::

A [Cosmos DB Azure functions eseményindítójának](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)használatával a feldolgozó [infrastruktúra](./change-feed-processor.md)karbantartásának szükségessége nélkül kihasználhatja a [változási csatorna](./change-feed-processor.md)méretezését és a megbízható események észlelési funkcióját. Csak az Azure-függvény logikáját kell összpontosítania anélkül, hogy aggódnia kellene az Event-beszerzés folyamat többi részével. Az triggert más [Azure functions kötésekkel](../azure-functions/functions-triggers-bindings.md#supported-bindings)is összekeverheti.

> [!NOTE]
> A Cosmos DB Azure Functions triggere jelenleg csak a Core (SQL) API-val használható.

## <a name="requirements"></a>Követelmények

Kiszolgáló nélküli eseményvezérelt folyamat megvalósításához a következők szükségesek:

* **A figyelt tároló**: a figyelt tároló a figyelt Azure Cosmos-tároló, amely a változási csatornát generáló adatok tárolására szolgál. A figyelt tárolóban lévő összes Beszúrás a tároló változási csatornáján jelenik meg.
* **A bérlet** tárolója: a címbérleti tároló több és dinamikus kiszolgáló nélküli Azure functions-példányon tárolja az állapotot, és lehetővé teszi a dinamikus skálázást. Ez a címbérleti tároló manuálisan vagy automatikusan hozható létre a Cosmos DB Azure Functions triggerrel. A bérlet tárolójának automatikus létrehozásához állítsa be a *CreateLeaseCollectionIfNotExists* jelzőt a [konfigurációban](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Particionált bérletű tárolók szükségesek a `/id` partíciós kulcs definíciójának létrehozásához.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Hozza létre a Azure Functions triggert a Cosmos DB

Az Azure-függvény létrehozása egy Azure Functions triggerrel Cosmos DB mostantól támogatott az összes Azure Functions IDE és CLI-integráción keresztül:

* Visual Studio- [bővítmény](../azure-functions/functions-develop-vs.md) a Visual Studio-felhasználók számára.
* [Visual Studio Code-bővítmény](/azure/developer/javascript/tutorial-vscode-serverless-node-01) a Visual Studio Code-felhasználók számára.
* És végül a [CLI-eszközök alapszintű](../azure-functions/functions-run-local.md#create-func) , többplatformos ide-alapú felhasználói élményhez.

## <a name="run-your-trigger-locally"></a>Trigger helyi futtatása

Az [Azure-függvényt helyileg](../azure-functions/functions-develop-local.md) is futtathatja a [Azure Cosmos db emulátor](./local-emulator.md) használatával, amely Azure-előfizetés nélkül hozhat létre és fejleszthet kiszolgáló nélküli eseményvezérelt folyamatokat, vagy költségekkel jár.

Ha élő forgatókönyveket szeretne tesztelni a felhőben, akkor [ingyenesen kipróbálhatja a Cosmos DBT](https://azure.microsoft.com/try/cosmosdb/) bankkártyás vagy Azure-előfizetés nélkül.

## <a name="next-steps"></a>Következő lépések

A következő cikkekben továbbra is megismerheti a hírcsatornák változását:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A módosítási csatorna olvasási módjai](read-change-feed.md)
* [A hírcsatorna-feldolgozó függvénytárának módosítása](change-feed-processor.md)
* [A Change feed Processor Library használata](change-feed-processor.md)
* [Kiszolgáló nélküli adatbázis-számítástechnika Azure Cosmos DB és Azure Functions használatával](serverless-computing-database.md)
