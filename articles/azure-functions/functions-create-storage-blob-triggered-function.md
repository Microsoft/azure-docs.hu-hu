---
title: BLOB Storage által aktivált függvény létrehozása az Azure-ban
description: A Azure Functions használatával hozzon létre egy kiszolgáló nélküli függvényt, amelyet a blob Storage-tárolóhoz hozzáadott elemek hív meg.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "83123123"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>BLOB Storage által aktivált függvény létrehozása az Azure-ban

Megtudhatja, hogyan hozhat létre egy olyan függvényt, amely akkor aktiválódik, ha a fájlokat blob Storage-tárolóba töltötték vagy frissítik.

## <a name="prerequisites"></a>Előfeltételek

+ Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-an-azure-function-app"></a>Azure-függvényalkalmazás létrehozása

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Sikeresen létrehozta az új Function alkalmazást.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="A függvényalkalmazás elkészült." border="true":::

Ezután létrehozhat egy függvényt az új függvényalkalmazásban.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Azure Blob Storage által aktivált függvény létrehozása

1. Válassza a **függvények** lehetőséget, majd válassza a **+ Hozzáadás** lehetőséget egy új függvény hozzáadásához.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Válasszon egy függvény sablont a Azure Portal." border="true":::

1. Válassza ki az **Azure Blob Storage trigger** sablonját.

1. Használja az ábra alatti táblázatban megadott beállításokat.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="A blob Storage által aktivált függvény neve és konfigurálása." border="true":::

    | Beállítás | Ajánlott érték | Leírás |
    |---|---|---|
    | **Új függvény** | Egyedi a függvényalkalmazásban | A blob által aktivált függvény neve. |
    | **Elérési út**   | samples-workitems/{name}    | A figyelt blobtárolóban található hely. A blob fájlneve a kötésben adható át a _name_ paraméterrel.  |
    | **Tárfiók kapcsolata** | AzureWebJobsStorage | Választhatja a függvényalkalmazás által már használt tárfiókkapcsolatot, vagy létrehozhat egy újat.  |

1. A függvény létrehozásához válassza a **create Function** elemet.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Hozza létre a blobtároló által aktivált függvényt." border="true":::

Ezután hozza létre a **Samples-workitems** tárolót.

## <a name="create-the-container"></a>A tároló létrehozása

1. A függvényben az **Áttekintés** lapon válassza ki az erőforráscsoportot.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Válassza ki az Azure Portal erőforráscsoportot." border="true":::

1. Keresse meg és válassza ki az erőforráscsoport Storage-fiókját.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Hozzáférés a Storage-fiókhoz." border="true":::

1. Válassza a **tárolók**, majd a **+ tároló** elemet. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Vegyen fel tárolót a Storage-fiókjába a Azure Portal." border="true":::

1. A **név** mezőbe írja be a következőt: `samples-workitems` , majd válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Nevezze el a Storage-tárolót." border="true":::

A blobtároló létrehozása után tesztelheti a függvényt úgy, hogy feltölt egy fájlt a tárolóba.

## <a name="test-the-function"></a>A függvény tesztelése

1. Lépjen vissza a Azure Portalba, és keresse meg a függvényt a lap alján található **naplók** kibontásával, és győződjön meg arról, hogy a naplózási adatfolyam nincs szüneteltetve.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Bontsa ki a Azure Portal a naplót." border="true":::

1. Egy különálló böngészőablakban nyissa meg az erőforráscsoportot a Azure Portalban, és válassza ki a Storage-fiókot.

1. Válassza a **tárolók** lehetőséget, majd válassza ki a **Samples-workitems** tárolót.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Nyissa meg a Samples-workitems tárolót a Azure Portal." border="true":::

1. Válassza a **feltöltés** lehetőséget, majd válassza ki a mappa ikont a feltölteni kívánt fájl kiválasztásához.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Töltsön fel egy fájlt a blobtárolóba." border="true":::

1. Tallózással keresse meg a fájlt a helyi számítógépen, például egy képfájlt, és válassza ki a fájlt. Válassza a **Megnyitás** , majd a **feltöltés** lehetőséget.

1. Lépjen vissza a függvény naplóihoz, és győződjön meg arról, hogy megtörtént a blob olvasása.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Tekintse meg a naplókban található üzeneteket." border="true":::

    >[!NOTE]
    > Amikor a függvényalkalmazás az alapértelmezett használatalapú csomagban fut, előfordulhat, hogy akár több is perc is eltelik a blob hozzáadása vagy frissítése és a függvény aktiválása között. Ha kis késleltetésre van szüksége a blob által aktivált függvényekhez, célszerű App Service-csomagban futtatnia a függvényalkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Létrehozott egy függvényt, amely akkor fut, amikor blob felvétele vagy frissítése történik a blobtárolóban. További információ a blobtároló eseményindítóiról: [Azure Functions – a blobtároló kötései](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
