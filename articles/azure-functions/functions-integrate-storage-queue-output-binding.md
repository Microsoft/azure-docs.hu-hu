---
title: Üzenetek hozzáadása az Azure Storage üzenetsorába a Functions szolgáltatás használatával
description: Használja az Azure Functions szolgáltatást olyan kiszolgáló nélküli függvény létrehozására, amelynek meghívása HTTP-kérelemmel történik, és üzenetet hoz létre egy Azure Storage-üzenetsorban.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: d2821a16e0b72b32cc392b7ae626d782734458a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674201"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Üzenetek hozzáadása az Azure Storage üzenetsorába a Functions szolgáltatás használatával

Az Azure Functions bemeneti és kimeneti kötései deklaratív módszert biztosítanak ahhoz, hogy a külső szolgáltatások adatai hozzáférhetők legyenek a kód számára. Ebben a rövid útmutatóban kimeneti kötés használatával hoz létre üzenetsori üzenetet, ha a függvény meghívása HTTP-kérelemmel történik. Az Azure Storage-tároló használatával megtekintheti a függvény által létrehozott üzenetsor-üzeneteket.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

- Kövesse [Az első függvény Azure Portalon való létrehozását](./functions-get-started.md) ismertető cikk utasításait, és ne végezze el **Az erőforrások eltávolítása** lépést. Ez a rövid útmutató az itt használt függvényalkalmazást és függvényt hozza létre.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Kimeneti kötés hozzáadása

Ebben a szakaszban a portál felhasználói felületén fogja hozzáadni egy üzenetsor-tároló kimeneti kötését a korábban létrehozott függvényhez. Ez a kötés lehetővé teszi, hogy a várólistában lévő üzenet létrehozásához minimális kódot írjon. Nem kell kódot írnia olyan feladatok elvégzéséhez, mint például egy tárolási kapcsolat megnyitása, egy üzenetsor létrehozása vagy egy üzenetsor-hivatkozás beszerzése. Ezeket a feladatokat az Azure Functions futtatókörnyezete és üzenetsorának kimeneti kötése végzi el Ön helyett.

1. Az Azure Portalon nyissa meg [Az első függvény létrehozása az Azure Portalon](./functions-get-started.md) útmutatóban létrehozott függvényalkalmazás oldalát. A lap megnyitásához keresse meg és válassza ki a **függvényalkalmazás**. Ezután válassza ki a Function alkalmazást.

1. Válassza ki a Function alkalmazást, majd válassza ki azt a függvényt, amelyet a korábbi gyors útmutatóban hozott létre.

1. Válassza az **integráció** lehetőséget, majd válassza a **+ kimenet hozzáadása** elemet.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Hozzon létre egy kimeneti kötést a függvényhez." border="true":::

1. Válassza ki az **Azure Queue Storage** kötés típusát, és adja hozzá a következő képernyőképen szereplő táblázatban megadott beállításokat: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Vegye fel egy üzenetsor-tároló kimeneti kötését egy függvénybe az Azure Portalon." border="true":::
    
    | Beállítás      |  Ajánlott érték   | Leírás                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Üzenet-paraméter neve** | outputQueueItem | A kimeneti kötés paraméterének neve. | 
    | **Üzenetsor neve**   | outqueue  | A tárfiókhoz csatlakoztatni kívánt üzenetsor neve. |
    | **Tárfiók kapcsolata** | AzureWebJobsStorage | Választhatja a függvényalkalmazás által már használt tárfiókkapcsolatot, vagy létrehozhat egy újat.  |

1. A kötés hozzáadásához kattintson **az OK gombra** .

Miután meghatározta a kimeneti kötést, módosítania kell a kódot, hogy az a kötés használatával üzeneteket adjon hozzá az üzenetsorhoz.  

## <a name="add-code-that-uses-the-output-binding"></a>Kimeneti kötést használó kód hozzáadása

Ebben a szakaszban egy olyan kódot fog hozzáadni, amely a kimeneti üzenetsorba ír üzeneteket. Ez az üzenet tartalmazza az értéket, amelyet a HTTP-eseményindító a lekérdezési sztringben kap meg. Ha például a lekérdezési sztring a `name=Azure` értéket tartalmazza, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

1. A függvényben kattintson a **Code + test (kód + teszt** ) elemre a függvény kódjának a szerkesztőben való megjelenítéséhez.

1. Frissítse a függvénykódot a függvény nyelvétől függően:

    # <a name="c"></a>[C\#](#tab/csharp)

    Adjon hozzá egy **outputQueueItem** paramétert a metódus aláírásához, ahogy az alábbi példában is látható.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    A függvény törzsében, a `return` utasítás előtt adja hozzá az üzenetsori üzenet létrehozására szolgáló paramétert használó kódot.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Olyan kódot adjon hozzá, amely a `context.bindings` objektumon alkalmazza a kimeneti kötést az üzenetsori üzenetek létrehozásához. Ezt a kódot a `context.done` utasítás elé írja be.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. A módosítások mentéséhez kattintson a **Mentés** elemre.

## <a name="test-the-function"></a>A függvény tesztelése

1. A kód módosításainak mentése után válassza a **teszt** lehetőséget.
1. Győződjön meg arról, hogy a teszt megfelel az alábbi képen, majd válassza a **Futtatás** lehetőséget. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Tesztelje a várólista tárolási kötését a Azure Portalban." border="true":::

    Figyelje meg, hogy a **Kérelem törzse** tartalmazza az *Azure*`name` értéket. Ez az érték jelenik meg a létrehozott üzenetsori üzenetben a függvény meghívásakor.
    
    A **Futtatás** lehetőség kiválasztása helyett egy URL-cím böngészőbe történő beírásával is meghívhatja a függvényt, ahol a lekérdezési sztringben adhatja meg a `name` értékét. A böngésző használatával végrehajtott módszert az [előző rövid útmutatóban](./functions-get-started.md) ismertettük.

1. A naplók ellenőrzésével győződjön meg arról, hogy sikeres volt a függvény futtatása. 

A Functions futtatókörnyezete egy **outqueue** nevű új üzenetsort hoz létre a tárfiókjában a kimeneti kötés első használatakor. A Storage-fiók használatával ellenőrizheti, hogy a várólista és a benne lévő üzenet létrejött-e.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>A AzureWebJobsStorage-hez csatlakoztatott Storage-fiók keresése


1. Nyissa meg a Function alkalmazást, és válassza a **Konfigurálás** lehetőséget.

1. Az **Alkalmazásbeállítások** területen válassza a **AzureWebJobsStorage** lehetőséget.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Képernyőfelvétel: a AzureWebJobsStorage kiválasztott konfiguráció lapja." border="true":::

1. Keresse meg és jegyezze fel a fiók nevét.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Keresse meg a AzureWebJobsStorage csatlakoztatott Storage-fiókot." border="true":::

### <a name="examine-the-output-queue"></a>A kimeneti üzenetsor vizsgálata

1. A Function alkalmazás erőforráscsoporthoz válassza ki azt a Storage-fiókot, amelyet ehhez a rövid útmutatóhoz használ.

1. A **Queue szolgáltatás** alatt válassza ki a **várólisták** elemet, és válassza ki az **üzenetsor nevű** várólistát. 

   Az üzenetsor tartalmazza az üzenetet, amelyet az üzenetsor kimeneti kötése létrehozott a HTTP által aktivált függvény futtatásakor. Ha az alapértelmezett *Azure*`name` értékkel hívta meg a függvényt, az üzenetsorban található üzenet a következő lesz: *A függvénynek átadott név: Azure*.

1. Ha újból futtatja a függvényt, egy új üzenet jelenik meg az üzenetsorban.  

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban hozzáadott egy kimeneti kötést egy meglévő függvényhez. További információ a tárolási üzenetsor kötéséről: [Azure Functions – a tárolási üzenetsor kötései](functions-bindings-storage-queue.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]
