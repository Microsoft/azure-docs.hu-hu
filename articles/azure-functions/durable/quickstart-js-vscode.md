---
title: Az első tartós funkció létrehozása az Azure-ban JavaScript használatával
description: Azure tartós függvény létrehozása és közzététele a JavaScriptben a Visual Studio Code használatával.
author: anthonychu
ms.topic: quickstart
ms.date: 05/07/2020
ms.reviewer: azfuncdf, antchu
ms.custom: devx-track-js
ms.openlocfilehash: f8ffa90ba0f1ac32d4691165fabf3d8eb9fb7605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91335449"
---
# <a name="create-your-first-durable-function-in-javascript"></a>Az első tartós függvény létrehozása a JavaScriptben

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írására kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code Azure Functions bővítményt a "Hello World" tartós funkciójának helyi létrehozására és tesztelésére.  Ez a függvény összehangolja és összekapcsolja a hívásokat más funkciókkal. Ezután közzéteheti a függvénykódot az Azure-ban.

![Tartós funkció futtatása az Azure-ban](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.

* A [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code bővítmény telepítése

* Győződjön meg arról, hogy rendelkezik a [Azure functions Core Tools](../functions-run-local.md)legújabb verziójával.

* Durable Functions Azure Storage-fiókra van szükség. Szüksége van egy Azure-előfizetésre.

* Győződjön meg arról, hogy a telepített [Node.js](https://nodejs.org/) 10. x vagy 12. x verziója van telepítve.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet. 

1. A Visual Studio Code-ban nyomja le az F1 billentyűt (vagy a CTRL/cmd + SHIFT + P billentyűkombinációt) a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create New Project...` .

    ![Create függvény](media/quickstart-js-vscode/functions-create-project.png)

1. Válasszon egy üres mappát a projekthez, és válassza a **kiválasztás** lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válasszon nyelvet a Function app-projekthez | JavaScript | Hozzon létre egy helyi Node.js functions projektet. |
    | Válasszon verziót | Azure Functions v3 | Ez a beállítás csak akkor jelenik meg, ha az alapvető eszközök még nincsenek telepítve. Ebben az esetben a rendszer az alkalmazás első futtatásakor telepíti az alapvető eszközöket. |
    | Válasszon sablont a projekt első függvényéhez | Kihagyás | |
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Megnyitás az aktuális ablakban | Nyissa meg újra a VS Code-t a kiválasztott mappában. |

A Visual Studio Code szükség esetén telepíti a Azure Functions Core Tools. Emellett egy Function app-projektet is létrehoz egy mappában. Ez a projekt tartalmazza a konfigurációs fájlok [host.js](../functions-host-json.md) és [local.settings.js](../functions-run-local.md#local-settings-file) .

A rendszer a gyökérkönyvtárban is létrehoz egy package.jsa fájlban.

## <a name="install-the-durable-functions-npm-package"></a>A Durable Functions NPM-csomag telepítése

Ha Durable Functionst szeretne használni egy Node.js Function-alkalmazásban, használja a nevű tárat `durable-functions` .

1. A VS Code-ban a *View (nézet* ) vagy a CTRL + SHIFT + billentyűkombináció használatával nyisson meg egy új terminált.

1. Telepítse a `durable-functions` NPM csomagot a `npm install durable-functions` Function alkalmazás gyökérkönyvtárában.

## <a name="creating-your-functions"></a>A függvények létrehozása

A legalapvetőbb Durable Functions alkalmazás három funkciót tartalmaz:

* *Orchestrator függvény* – olyan munkafolyamatot ír le, amely más funkciókat hangol össze.
* *Activity függvény* – a Orchestrator függvény hívja meg, végrehajtja a munkát, és opcionálisan egy értéket ad vissza.
* *Client Function* – egy normál Azure-függvény, amely egy Orchestrator függvényt indít el. Ez a példa egy HTTP által aktivált függvényt használ.

### <a name="orchestrator-function"></a>Orchestrator függvény

A sablon segítségével létrehozhatja a tartós függvény kódját a projektben.

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions Orchestrator | Durable Functions-előkészítés létrehozása |
    | Adja meg a függvény nevét | HelloOrchestrator | Tartós függvény neve |

Hozzáadta a Orchestrator a tevékenységi funkciók koordinálásához. A Orchestrator függvény megjelenítéséhez nyissa meg a *HelloOrchestrator/index.js* . Minden hívás meghívja a `context.df.callActivity` nevű tevékenységi függvényt `Hello` .

Ezután adja hozzá a hivatkozott `Hello` tevékenység függvényt.

### <a name="activity-function"></a>Activity függvény

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions tevékenység | Tevékenységi függvény létrehozása |
    | Adja meg a függvény nevét | Üdvözöljük | A tevékenység függvényének neve |

Hozzáadta a `Hello` Orchestrator által meghívott tevékenység-függvényt. Nyissa meg a *Hello/index.jst* , és tekintse meg, hogy bemenetként és Üdvözlésként adja meg a nevet. A Activity függvény olyan műveleteket hajt végre, mint például egy adatbázis hívása vagy egy számítási művelet végrehajtása.

Végül egy HTTP által aktivált függvényt fog hozzáadni, amely elindítja a koordinálást.

### <a name="client-function-http-starter"></a>Client Function (HTTP Starter)

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions HTTP Starter | HTTP Starter-függvény létrehozása |
    | Adja meg a függvény nevét | DurableFunctionsHttpStart | A tevékenység függvényének neve |
    | Engedélyszint | Névtelen | Bemutató céljából engedélyezze, hogy a függvény hitelesítés nélkül legyen meghívva |

Olyan HTTP által aktivált függvényt adott hozzá, amely egy előkészítést indít el. Nyissa meg a *DurableFunctionsHttpStart/index.jst* , és tekintse meg, hogy az `client.startNew` új előkészítés elindításához használja-e. Ezután egy olyan `client.createCheckStatusResponse` http-választ ad vissza, amely URL-címeket tartalmaz, amelyek az új előkészítés figyelésére és kezelésére használhatók.

Most már rendelkezik egy Durable Functions alkalmazással, amely helyileg futtatható és üzembe helyezhető az Azure-ban.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez állítson be egy töréspontot a `Hello` tevékenység funkció kódjában (*Hello/index.js*). Nyomja le az F5 billentyűt, vagy válassza ki `Debug: Start Debugging` a parancssorból a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

    > [!NOTE]
    > A hibakereséssel kapcsolatos további információkért tekintse meg a [Durable functions diagnosztikát](durable-functions-diagnostics.md#debugging) .

1. Durable Functions futtatásához Azure Storage-fiókra van szükség. Ha a VS Code megkéri a Storage-fiók kiválasztására, válassza a **Storage-fiók kiválasztása** lehetőséget.

    ![Storage-fiók létrehozása](media/quickstart-js-vscode/functions-select-storage.png)

1. Az utasításokat követve adja meg a következő információkat egy új Storage-fiók létrehozásához az Azure-ban.

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Előfizetés kiválasztása | *az előfizetés neve* | Válassza ki az Azure-előfizetését |
    | Válasszon Storage-fiókot | Új tárfiók létrehozása |  |
    | Adja meg az új Storage-fiók nevét | *egyedi név* | A létrehozandó Storage-fiók neve |
    | Erőforráscsoport kiválasztása | *egyedi név* | A létrehozandó erőforráscsoport neve |
    | Hely kiválasztása | *régió* | Válasszon ki egy régiót a közelben |

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](media/quickstart-js-vscode/functions-f5.png)

1. A böngésző vagy egy hasonló eszköz, például a [Poster](https://www.getpostman.com/) vagy a [curl](https://curl.haxx.se/)használatával HTTP POST-kérést küldhet az URL-végpontnak. Cserélje le az utolsó szegmenst a Orchestrator függvény () nevére `HelloOrchestrator` . Az URL-címnek hasonlónak kell lennie `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   A válasz a HTTP-függvény kezdeti eredménye, amely arról tájékoztatja, hogy a tartós előkészítés sikeresen elindult. Még nem az előkészítés végeredménye. A válasz több hasznos URL-címet is tartalmaz. Most pedig lekérdezjük a folyamat állapotát.

1. Másolja ki az URL-címet, `statusQueryGetUri` és illessze be a böngésző címsorába, majd hajtsa végre a kérelmet. Azt is megteheti, hogy továbbra is a Poster használatával adja ki a GET kérelmet.

   A kérelem lekérdezi az állapotot az előkészítési példányon. Egy végleges választ kap, amely megmutatja, hogy a példány befejeződik, és tartalmazza a tartós funkció kimeneteit vagy eredményét. A következőképpen néz ki: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. A hibakeresés leállításához nyomja le a **SHIFT + F5** billentyűkombinációt a vs Code-ban.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-triggert használó függvényt meghívó URL-címnek a következő formátumúnak kell lennie: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>Következő lépések

A Visual Studio Code használatával JavaScript tartós Function-alkalmazást hozhat létre és tehet közzé.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)
