---
title: Az első függvény létrehozása az Azure portálon
description: Ismerje meg, hogyan hozhatja létre az első Azure-függvényét kiszolgáló nélküli végrehajtáshoz az Azure Portalon.
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 336e531f4ec64141770fc26d7e6eea9ebfedf922
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517138"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Az első függvény létrehozása az Azure portálon

Azure Functions lehetővé teszi a kód kiszolgáló nélküli környezetben való futtatását anélkül, hogy először létre kell hoznia egy virtuális gépet (VM), vagy közzé kell tennie egy webalkalmazást. Ebből a cikkből megtudhatja, hogyan hozhat létre egy "hello world" HTTP Azure Functions függvényt a Azure Portal.

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

Javasoljuk, hogy helyileg fejlessze a [függvényeket,](functions-develop-local.md) és tegye közzé őket egy Azure-beli függvényalkalmazásban.  
A választott helyi fejlesztési környezet és nyelv használatának elkezdéséhez használja az alábbi hivatkozások egyikét:

| Visual Studio Code | Terminál/parancssor | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[Első lépések a C-ben #](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[A Java első lépések](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[A JavaScript első lépések](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[A PowerShell első lépések](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[A Python első lépések](./create-first-function-vs-code-python.md) |&bull;&nbsp;[Első lépések a C-ben #](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[A Java első lépések](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[A JavaScript első lépések](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[A PowerShell első lépések](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[A Python első lépések](./create-first-function-cli-python.md) | [Első lépések a C-ben #](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókjával.

## <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy függvényalkalmazással a függvények végrehajtásának biztosításához. A függvényalkalmazásokkal logikai egységként csoportosíthatja a függvényeket az erőforrások egyszerűbb kezelése, üzembe helyezése, skálázása és megosztása érdekében.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ezután hozzon létre egy függvényt az új függvényalkalmazásban.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP-eseményindító függvény létrehozása

1. A Függvények ablak bal oldali menüjében **válassza** a **Függvények** lehetőséget, majd a felső menü **hozzáadás** parancsát. 
 
1. A Függvény **hozzáadása ablakban** válassza a **HTTP-eseményindító sablont.**

    ![A HTTP-eseményindító függvény kiválasztása](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. A **Sablon részletei alatt** használja az Új függvény lehetőséget, válassza a Névtelen lehetőséget az Engedélyezési szint legördülő `HttpExample` listából, majd válassza a Hozzáadás **lehetőséget.**   **[](functions-bindings-http-webhook-trigger.md#authorization-keys)**

    Az Azure létrehozza a HTTP-eseményindító függvényt. Mostantól egy HTTP-kérelem küldésével futtathatja az új függvényt.

## <a name="test-the-function"></a>A függvény tesztelése

1. Az új HTTP-eseményindító függvényben válassza a bal oldali **menü Code + Test** (Kód + tesztelés) parancsát, majd a függvény **URL-címének** be szereznie lehetőséget a felső menüben.

    ![Válassza a Függvény URL-címének lekérte lehetőséget](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. A **Függvény URL-címének**  beszúrása párbeszédpanelen válassza az alapértelmezett értéket a legördülő listából, majd válassza a Másolás **a vágólapra** ikont. 

    ![A függvény URL-címének másolása az Azure portálról](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. Illessze be a függvény URL-címét a böngésző címsorába. Adja hozzá a lekérdezési sztring értékét az URL-cím végéhez, majd nyomja le az Enter billentyűt `?name=<your_name>` a kérés futtatásához. A böngészőnek egy válaszüzenetet kell megjelenítenie, amely megismétli a lekérdezési sztring értékét. 

    Ha a kérés URL-címe tartalmaz egy [hozzáférési kulcsot](functions-bindings-http-webhook-trigger.md#authorization-keys) ( ), akkor a függvény létrehozásakor a Névtelen hozzáférési szint helyett a Függvényt `?code=...` kell választania.   Ebben az esetben ehelyett a következőt kell hozzáfűzni: `&name=<your_name>` .

1. A függvény futásakor a rendszer nyomkövetési adatok ír a naplókba. A nyomkövetési kimenet megtekintéséhez térjen vissza a Portál Kód  **+ teszt** oldalára, és bontsa ki a lap alján található Naplók nyilat. Hívja meg újra a függvényt a naplókba írt nyomkövetési kimenet megtekintéséhez. 

    :::image type="content" source="media/functions-create-first-azure-function/function-view-logs.png" alt-text="Függvénynapló-megjelenítő a Azure Portal":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
