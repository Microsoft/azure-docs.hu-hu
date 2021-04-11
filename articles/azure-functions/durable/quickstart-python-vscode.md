---
title: Az első tartós funkció létrehozása az Azure-ban a Python használatával
description: Azure tartós funkciót hozhat létre és tehet közzé a Pythonban a Visual Studio Code használatával.
author: anthonychu
ms.topic: quickstart
ms.date: 12/23/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: cccb42041ece65ad4f245c28dd7c5cf356d5e63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775153"
---
# <a name="create-your-first-durable-function-in-python"></a>Az első tartós függvény létrehozása a Pythonban

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írására kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code Azure Functions bővítményt a "Hello World" tartós funkciójának helyi létrehozására és tesztelésére.  Ez a függvény összehangolja és összekapcsolja a hívásokat más funkciókkal. Ezután közzéteheti a függvénykódot az Azure-ban.

![Tartós funkció futtatása az Azure-ban](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.

* Telepítse a [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code bővítményt.

* Győződjön meg arról, hogy rendelkezik a [Azure functions Core Tools](../functions-run-local.md)legújabb verziójával.

* Durable Functions Azure Storage-fiókra van szükség. Szüksége van egy Azure-előfizetésre.

* Győződjön meg arról, hogy a telepített [Python](https://www.python.org/) 3,6, 3,7 vagy 3,8 verziója van telepítve.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet. 

1. A Visual Studio Code-ban nyomja le az F1 billentyűt (vagy a <kbd>CTRL/cmd + SHIFT + P</kbd>billentyűkombinációt) a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create New Project...` .

    ![Create függvény](media/quickstart-python-vscode/functions-create-project.png)

1. Válasszon egy üres mappát a projekthez, és válassza a **kiválasztás** lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válasszon nyelvet a Function app-projekthez | Python | Hozzon létre egy helyi Python functions-projektet. |
    | Válasszon verziót | Azure Functions v3 | Ez a beállítás csak akkor jelenik meg, ha az alapvető eszközök még nincsenek telepítve. Ebben az esetben a rendszer az alkalmazás első futtatásakor telepíti az alapvető eszközöket. |
    | Python-verzió | Python 3,6, 3,7 vagy 3,8 | A VS Code egy virtuális környezetet hoz létre a kiválasztott verzióval. |
    | Válasszon sablont a projekt első függvényéhez | Kihagyás | |
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Megnyitás az aktuális ablakban | Nyissa meg újra a VS Code-t a kiválasztott mappában. |

A Visual Studio Code szükség esetén telepíti a Azure Functions Core Tools. Emellett egy Function app-projektet is létrehoz egy mappában. Ez a projekt tartalmazza a konfigurációs fájlok [host.js](../functions-host-json.md) és [local.settings.js](../functions-run-local.md#local-settings-file) .

A gyökérmappa egy *requirements.txt* fájlt is létrehoz. Meghatározza a Function alkalmazás futtatásához szükséges Python-csomagokat.

## <a name="install-azure-functions-durable-from-pypi"></a>Az Azure-functions-instrapabíró telepítése a PyPI-ből

A projekt létrehozásakor a Azure Functions VS Code bővítmény automatikusan létrehozott egy virtuális környezetet a kiválasztott Python-verzióval. Egy terminálon aktiválnia kell a virtuális környezetet, és telepítenie kell a Azure Functions és Durable Functions által igényelt függőségeket.

1. Nyissa meg *requirements.txt* a szerkesztőben, és módosítsa a tartalmát a következőre:

    ```
    azure-functions
    azure-functions-durable
    ```

1. Nyissa meg a szerkesztő integrált terminálját az aktuális mappában (<kbd>CTRL + SHIFT +</kbd>).

1. Az integrált terminálon aktiválja a virtuális környezetet az aktuális mappában:

    **Linux vagy macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![Virtuális környezet aktiválása](media/quickstart-python-vscode/activate-venv.png)

1. A virtuális környezetet aktiváló integrált terminálon a pip használatával telepítse az imént definiált csomagokat:

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>Függvények létrehozása

Az alapszintű Durable Functions alkalmazás három funkciót tartalmaz:

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

Hozzáadta a Orchestrator a tevékenységi funkciók koordinálásához. A Orchestrator függvény megjelenítéséhez nyissa meg a *HelloOrchestrator/ \_ \_ init__. a.* Minden hívás meghívja a `context.call_activity` nevű tevékenységi függvényt `Hello` .

Ezután adja hozzá a hivatkozott `Hello` tevékenység függvényt.

### <a name="activity-function"></a>Activity függvény

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions tevékenység | Tevékenységi függvény létrehozása |
    | Adja meg a függvény nevét | Üdvözöljük | A tevékenység függvényének neve |

Hozzáadta a `Hello` Orchestrator által meghívott tevékenység-függvényt. Nyissa meg a *Hello/ \_ \_ init__. a.* a (z). A Activity függvény olyan műveleteket hajt végre, mint például egy adatbázis hívása vagy egy számítási művelet végrehajtása.

Végül egy HTTP által aktivált függvényt fog hozzáadni, amely elindítja a koordinálást.

### <a name="client-function-http-starter"></a>Client Function (HTTP Starter)

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions HTTP Starter | HTTP Starter-függvény létrehozása |
    | Adja meg a függvény nevét | DurableFunctionsHttpStart | A tevékenység függvényének neve |
    | Engedélyszint | Névtelen | Bemutató céljából engedélyezze, hogy a függvény hitelesítés nélkül legyen meghívva |

Olyan HTTP által aktivált függvényt adott hozzá, amely egy előkészítést indít el. Nyissa meg a *DurableFunctionsHttpStart/ \_ \_ init__. a.* , és ellenőrizze, hogy az új előkészítési folyamat indításához használja-e `client.start_new` . Ezután egy olyan `client.create_check_status_response` http-választ ad vissza, amely URL-címeket tartalmaz, amelyek az új előkészítés figyelésére és kezelésére használhatók.

Most már rendelkezik egy Durable Functions alkalmazással, amely helyileg futtatható és üzembe helyezhető az Azure-ban.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Ha még nincs telepítve, akkor a rendszer a Visual Studio Code-ból való első elindításakor megkéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez állítson be egy töréspontot a `Hello` tevékenység funkció kódjában (*Hello/ \_ \_ init__.* másol). Nyomja le az F5 billentyűt, vagy válassza ki `Debug: Start Debugging` a parancssorból a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

    > [!NOTE]
    > A hibakereséssel kapcsolatos további információkért tekintse meg a [Durable functions diagnosztikát](durable-functions-diagnostics.md#debugging) .

1. Durable Functions futtatásához Azure Storage-fiókra van szükség. Ha a VS Code megkéri a Storage-fiók kiválasztására, válassza a **Storage-fiók kiválasztása** lehetőséget.

    ![Storage-fiók létrehozása](media/quickstart-python-vscode/functions-select-storage.png)

1. Az utasításokat követve adja meg a következő információkat egy új Storage-fiók létrehozásához az Azure-ban.

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Előfizetés kiválasztása | *az előfizetés neve* | Válassza ki az Azure-előfizetését |
    | Válasszon Storage-fiókot | Új tárfiók létrehozása |  |
    | Adja meg az új Storage-fiók nevét | *egyedi név* | A létrehozandó Storage-fiók neve |
    | Erőforráscsoport kiválasztása | *egyedi név* | A létrehozandó erőforráscsoport neve |
    | Hely kiválasztása | *régió* | Válasszon ki egy régiót a közelben |

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](media/quickstart-python-vscode/functions-f5.png)

1. A böngésző vagy egy hasonló eszköz, például a [Poster](https://www.getpostman.com/) vagy a [curl](https://curl.haxx.se/)használatával HTTP-kérést küldhet az URL-végpontnak. Cserélje le az utolsó szegmenst a Orchestrator függvény () nevére `HelloOrchestrator` . Az URL-címnek hasonlónak kell lennie `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   A válasz a HTTP-függvény kezdeti eredménye, amely arról tájékoztatja, hogy a tartós előkészítés sikeresen elindult. Még nem az előkészítés végeredménye. A válasz több hasznos URL-címet is tartalmaz. Most pedig lekérdezjük a folyamat állapotát.

1. Másolja ki az URL-címet, `statusQueryGetUri` és illessze be a böngésző címsorába, majd hajtsa végre a kérelmet. Azt is megteheti, hogy továbbra is a Poster használatával adja ki a GET kérelmet.

   A kérelem lekérdezi az állapotot az előkészítési példányon. Meg kell kapnia egy végleges választ, amely azt mutatja, hogy a példány befejeződött, és tartalmazza a tartós függvény kimeneteit vagy eredményét. A következőképpen néz ki: 

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

1. A hibakeresés leállításához nyomja le a <kbd>SHIFT + F5</kbd> billentyűkombinációt a vs Code-ban.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-triggert használó függvényt meghívó URL-címnek a következő formátumúnak kell lennie: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>Következő lépések

A Visual Studio Code használatával létrehozhatja és közzéteheti a Python tartós Function alkalmazást.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)
