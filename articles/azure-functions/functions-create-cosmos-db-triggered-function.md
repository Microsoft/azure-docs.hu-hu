---
title: Azure Cosmos DB által aktivált függvény létrehozása
description: Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvények létrehozására, amelyek akkor aktiválódnak, ha adatokat ad hozzá az Azure Cosmos DB egyik adatbázisához.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 621773a84db99dbacfaa163f77189974ba102163
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98034815"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Azure Cosmos DB által aktivált függvény létrehozása

Megismerheti, hogyan hozhat létre egy olyan függvényt, amelyet az aktivál, ha adatokat ad hozzá az Azure Cosmos DB-hez, illetve abban található adatokat módosít. További információ az Azure Cosmos DB-ről: [Azure Cosmos DB: Kiszolgáló nélküli adatbázis-használat az Azure Functions szolgáltatással](../cosmos-db/serverless-computing-database.md).

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Azure Cosmos DB kód":::

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

+ Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure-fiókjával.

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

A trigger létrehozásához rendelkeznie kell egy SQL API-t használó Azure Cosmos DB-fiókkal.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-a-function-app-in-azure"></a>Függvényalkalmazás létrehozása az Azure-ban

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Azure Cosmos DB-eseményindító létrehozása

1. A Function alkalmazásban válassza a bal oldali menü **függvények** elemét, majd a felső menüben válassza a **Hozzáadás** lehetőséget. 

1. Az **új függvény** lapon írja be `cosmos` a kifejezést a Keresés mezőbe, majd válassza ki a **Azure Cosmos db trigger** sablont.

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Függvények oldal a Azure Portal":::


1. Konfigurálja az új triggert a következő táblázatban megadott beállításokkal:

    | Beállítás      | Ajánlott érték  | Leírás                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Új függvény** | Fogadja el az alapértelmezett nevet | A függvény neve. |
    | **Cosmos DB-fiókkapcsolat** | Az alapértelmezett új név elfogadása | Válassza az **új**, a korábban létrehozott **adatbázis-fiók** elemet, majd **az OK gombot**. Ez a művelet létrehoz egy Alkalmazásbeállítás a fiók kapcsolatához. Ezt a beállítást használja a kötés, hogy kapcsolódjon az adatbázishoz. |
    | **Adatbázis neve** | Feladatok | A figyelni kívánt gyűjteményt tartalmazó adatbázis neve. |
    | **Gyűjtemény neve** | Elemek | A figyelni kívánt gyűjtemény neve. |
    | **Gyűjtemény neve bérletekhez** | leases | A bérletek tárolására szolgáló gyűjtemény neve. |
    | **Címbérlet-gyűjtemény létrehozása, ha nem létezik** | Yes | Ellenőrzi a címbérleti gyűjtemény létezését, és automatikusan létrehozza azt. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Az Azure Cosmos DB-eseményindító létrehozása":::

1. Válassza a **Függvény létrehozása** lehetőséget. 

    Az Azure létrehozza a Cosmos DB trigger függvényt.

1. A sablon alapú függvény kódjának megjelenítéséhez válassza a **Code + test (kód + tesztelés**) lehetőséget.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="Cosmos DB-függvénysablon C# környezetben":::

    Ez a függvénysablon a naplókba írja a dokumentumok számát és az első dokumentumazonosítót.

Ezután kapcsolódjon a Azure Cosmos DB-fiókjához, és hozza létre a `Items` tárolót az `Tasks` adatbázisban.

## <a name="create-the-items-container"></a>Az Items tároló létrehozása

1. Nyissa meg az [Azure Portal](https://portal.azure.com) egy újabb példányát egy új böngészőlapon.

1. A portál bal oldali menüjében bontsa ki az ikonsort, gépelje be a `cosmos` szöveget a keresőmezőbe, és válassza az **Azure Cosmos DB** lehetőséget.

    ![Az Azure Cosmos DB szolgáltatás megkeresése](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Válassza ki Azure Cosmos DB-fiókját, majd válassza az **Adatkezelő** lehetőséget. 

1. Az **SQL API** alatt válassza a **feladatok** adatbázis lehetőséget, és válassza az **új tároló** elemet.

    ![Tároló létrehozása](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. A **tároló hozzáadása** területen használja a rendszerkép alatti táblázatban látható beállításokat. 

    ![A feladatok tárolójának megadása](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Beállítás|Ajánlott érték|Leírás |
    | ---|---|--- |
    | **Adatbázis-azonosító** | Feladatok |Az új adatbázis neve. A névnek meg kell egyeznie a függvénykötésben meghatározott névvel. |
    | **Tároló azonosítója** | Elemek | Az új tároló neve. A névnek meg kell egyeznie a függvénykötésben meghatározott névvel.  |
    | **[Partíciókulcs](../cosmos-db/partitioning-overview.md)** | /kategória|Az egyes partíciók között az adatokat egyenletesen elosztó partíciókulcs. A megfelelő partíciós kulcs kiválasztásával fontos szerepet játszik a teljesítményű tárolók létrehozásában. | 
    | **Átviteli sebesség** |400 kérelemegység| Használja az alapértelmezett értéket. Később lehetősége lesz növelni az átviteli sebességet a késés csökkentése érdekében. |    

1. Az elemek tároló létrehozásához kattintson **az OK** gombra. Eltarthat egy rövid ideig, amíg a tároló létre nem kerül.

Ha a függvény kötésében megadott tároló létezik, akkor tesztelheti a függvényt, ha elemeket ad hozzá az új tárolóhoz.

## <a name="test-the-function"></a>A függvény tesztelése

1. Bontsa ki az új **elemek** tárolót Adatkezelőban, majd válassza az **elemek**, majd az **új elem** lehetőséget.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Elem létrehozása az elemek tárolóban":::

1. Cserélje le az új elem tartalmát a következő tartalomra, majd válassza a **Mentés** lehetőséget.

    ```yaml
    {
        "id": "task1",
        "category": "general",
        "description": "some task"
    }
    ```

1. Váltson a böngészőben az első lapra, amely tartalmazza a függvényt a portálon. Bontsa ki a függvénynaplókat, és ellenőrizze, hogy az új dokumentum aktiválta-e a függvényt. Meggyőződhet arról, hogy a `task1` dokumentumazonosító értéke a naplókba lett írva. 

    ![Tekintse meg a naplókban található üzeneteket.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Nem kötelező) Térjen vissza a dokumentumhoz, hajtson végre egy módosítást, majd kattintson a **Frissítés** elemre. Térjen vissza a függvénynaplókhoz és ellenőrizze, hogy a frissítés is aktiválta-e a függvényt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor dokumentum hozzáadása vagy módosítása történik az Azure Cosmos DB-ben. További információ az Azure Cosmos DB-eseményindítókról: [Azure Cosmos DB – Azure Functions kötések](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
