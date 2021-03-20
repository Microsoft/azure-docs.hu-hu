---
title: Üzenetsor-üzenetek által aktivált függvény létrehozása az Azure-ban
description: A Azure Functions használatával hozzon létre egy kiszolgáló nélküli függvényt, amelyet az Azure-ban egy üzenetsor számára elküldött üzenet hív meg.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90981581"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Azure Storage-üzenetsor által aktivált függvény létrehozása

Megtudhatja, hogyan hozhat létre olyan függvényt, amely akkor aktiválódik, amikor üzeneteket küldenek egy Azure Storage-üzenetsorba.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="A Function alkalmazás sikeresen létrejött.." border="true":::

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Üzenetsor által aktivált függvény létrehozása

1. Válassza a **függvények** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget egy új függvény hozzáadásához.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Válasszon egy függvény sablont a Azure Portal." border="true":::

1. Válassza ki az **Azure Queue Storage trigger** sablonját.

1. Használja az ábra alatti táblázatban megadott beállításokat.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="A várólista-tároló által aktivált függvény neve és konfigurálása." border="true":::


    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Név** | Egyedi a függvényalkalmazásban | Az üzenetsor által aktivált függvény neve. |
    | **Üzenetsor neve**   | myqueue-items    | A tárfiókhoz csatlakoztatni kívánt üzenetsor neve. |
    | **Tárfiók kapcsolata** | AzureWebJobsStorage | Választhatja a függvényalkalmazás által már használt tárfiókkapcsolatot, vagy létrehozhat egy újat.  |    

1. A függvény létrehozásához válassza a **create Function** elemet.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Hozza létre az üzenetsor-tároló által aktivált függvényt." border="true":::

Ezután kapcsolódjon az Azure Storage-fiókjához, és hozza létre a **myqueue-elemek** tárolási várólistáját.

## <a name="create-the-queue"></a>Az üzenetsor létrehozása

1. A függvényben az **Áttekintés** lapon válassza ki az erőforráscsoportot.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Válassza ki az Azure Portal erőforráscsoportot." border="true":::

1. Keresse meg és válassza ki az erőforráscsoport Storage-fiókját.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Hozzáférés a Storage-fiókhoz." border="true":::

1. Válassza a **várólisták** lehetőséget, majd válassza a **+ üzenetsor** lehetőséget. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Vegyen fel egy várólistát a Storage-fiókjába a Azure Portal." border="true":::

1. A **név** mezőbe írja be a következőt: `myqueue-items` , majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Nevezze el a várólista Storage-tárolóját." border="true":::

Az üzenetsor létrehozása után tesztelheti a függvényt úgy, hogy felvesz egy üzenetet az üzenetsorba.

## <a name="test-the-function"></a>A függvény tesztelése

1. Lépjen vissza a Azure Portalba, és keresse meg a függvényt a lap alján található **naplók** kibontásával, és győződjön meg arról, hogy a naplózási adatfolyam nincs szüneteltetve.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Bontsa ki a Azure Portal a naplót." border="true":::

1. Egy különálló böngészőablakban nyissa meg az erőforráscsoportot a Azure Portalban, és válassza ki a Storage-fiókot.

1. Válassza a **várólisták** lehetőséget, majd válassza ki a **myqueue** tárolót.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Nyissa meg a myqueue-elemek várólistáját a Azure Portal." border="true":::

1. Válassza az **üzenet hozzáadása** lehetőséget, és írja be a "Hello World!" kifejezést. az **üzenet szövege**. Válassza az **OK** lehetőséget.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Képernyőfelvétel: az üzenet hozzáadása gomb és az üzenet szövege mező kiemelve." border="true":::

1. Várjon néhány másodpercet, majd térjen vissza a függvény naplóihoz, és győződjön meg arról, hogy megtörtént az új üzenet olvasása az üzenetsorból.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Tekintse meg a naplókban található üzeneteket." border="true":::

1. A Storage-várólistában kattintson a **frissítés** elemre, és ellenőrizze, hogy az üzenet feldolgozása megtörtént-e, és már nem szerepel-e a várólistán.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor üzenet felvétele történik a tárolási üzenetsorba. További információ a tárolási üzenetsor eseményindítóiról: [Azure Functions – a tárolási üzenetsor kötései](functions-bindings-storage-queue.md).

Most, hogy létrehozta az első függvényt, hozzon létre egy kimeneti kötést a függvényhez, amely visszaírja az üzenetet egy másik várólistába.

> [!div class="nextstepaction"]
> [Üzenetek hozzáadása az Azure Storage üzenetsorába függvények használatával](functions-integrate-storage-queue-output-binding.md)
