---
title: Az első tartós függvény létrehozása Azure Functions a PowerShell használatával
description: Azure tartós függvény létrehozása és közzététele a PowerShellben a Visual Studio Code használatával.
author: anthonychu
ms.topic: quickstart
ms.date: 08/10/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: edd02085abe63b124082255247362f096248ba82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317259"
---
# <a name="create-your-first-durable-function-in-powershell"></a>Az első tartós függvény létrehozása a PowerShellben

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írására kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code Azure Functions bővítményt a "Hello World" tartós funkciójának helyi létrehozására és tesztelésére.  Ez a függvény összehangolja és összekapcsolja a hívásokat más funkciókkal. Ezután közzéteheti a függvénykódot az Azure-ban.

![Tartós funkció futtatása az Azure-ban](./media/quickstart-js-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.

* A [Azure functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code bővítmény telepítése

* Győződjön meg arról, hogy rendelkezik a [Azure functions Core Tools](../functions-run-local.md)legújabb verziójával.

* Durable Functions Azure Storage-fiókra van szükség. Szüksége van egy Azure-előfizetésre.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet. 

1. A Visual Studio Code-ban nyomja le az F1 billentyűt (vagy a CTRL/cmd + SHIFT + P billentyűkombinációt) a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create New Project...` .

    ![Create függvény](media/quickstart-js-vscode/functions-create-project.png)

1. Válasszon egy üres mappát a projekthez, és válassza a **kiválasztás** lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válasszon nyelvet a Function app-projekthez | PowerShell | Hozzon létre egy helyi PowerShell functions-projektet. |
    | Válasszon verziót | Azure Functions v3 | Ez a beállítás csak akkor jelenik meg, ha az alapvető eszközök még nincsenek telepítve. Ebben az esetben a rendszer az alkalmazás első futtatásakor telepíti az alapvető eszközöket. |
    | Válasszon sablont a projekt első függvényéhez | Kihagyás | |
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Megnyitás az aktuális ablakban | Nyissa meg újra a VS Code-t a kiválasztott mappában. |

A Visual Studio Code szükség esetén telepíti a Azure Functions Core Tools. Emellett egy Function app-projektet is létrehoz egy mappában. Ez a projekt tartalmazza a konfigurációs fájlok [host.js](../functions-host-json.md) és [local.settings.js](../functions-run-local.md#local-settings-file) .

A rendszer a gyökérkönyvtárban is létrehoz egy package.jsa fájlban.

### <a name="configure-function-app-to-use-powershell-7"></a>A Function app konfigurálása a PowerShell 7 használatára

Nyissa meg a *local.settings.js* fájlt, és ellenőrizze, hogy a nevű beállítás a következőre van-e `FUNCTIONS_WORKER_RUNTIME_VERSION` beállítva: `~7` . Ha hiányzik vagy egy másik értékre van állítva, frissítse a fájl tartalmát.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "powershell",
    "FUNCTIONS_WORKER_RUNTIME_VERSION" : "~7"
  }
}
```

## <a name="create-your-functions"></a>Függvények létrehozása

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
    | Sablon kiválasztása a függvényhez | Durable Functions Orchestrator (előzetes verzió) | Durable Functions-előkészítés létrehozása |
    | Adja meg a függvény nevét | HelloOrchestrator | Tartós függvény neve |

Hozzáadta a Orchestrator a tevékenységi funkciók koordinálásához. A Orchestrator függvény megjelenítéséhez nyissa meg a *HelloOrchestrator/run.ps1* . A parancsmag minden hívása `Invoke-ActivityFunction` egy nevű tevékenység-függvényt hív meg `Hello` .

Ezután adja hozzá a hivatkozott `Hello` tevékenység függvényt.

### <a name="activity-function"></a>Activity függvény

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions tevékenység (előzetes verzió) | Tevékenységi függvény létrehozása |
    | Adja meg a függvény nevét | Üdvözöljük | A tevékenység függvényének neve |

Hozzáadta a `Hello` Orchestrator által meghívott tevékenység-függvényt. Nyissa meg a *Hello/run.ps1t* , és tekintse meg, hogy bemenetként és Üdvözlésként adja meg a nevet. A Activity függvény olyan műveleteket hajt végre, mint például egy adatbázis hívása vagy egy számítási művelet végrehajtása.

Végül egy HTTP által aktivált függvényt fog hozzáadni, amely elindítja a koordinálást.

### <a name="client-function-http-starter"></a>Client Function (HTTP Starter)

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | Durable Functions HTTP Starter (előzetes verzió) | HTTP Starter-függvény létrehozása |
    | Adja meg a függvény nevét | HttpStart | A tevékenység függvényének neve |
    | Engedélyszint | Névtelen | Bemutató céljából engedélyezze, hogy a függvény hitelesítés nélkül legyen meghívva |

Olyan HTTP által aktivált függvényt adott hozzá, amely egy előkészítést indít el. Nyissa meg a *HttpStart/run.ps1* , és ellenőrizze, hogy a `Start-NewOrchestration` parancsmagot használja-e egy új előkészítés elindításához. Ezután a parancsmag használatával ad `New-OrchestrationCheckStatusResponse` vissza egy olyan URL-címet tartalmazó http-választ, amely az új előkészítés figyelésére és kezelésére használható.

Most már rendelkezik egy Durable Functions alkalmazással, amely helyileg futtatható és üzembe helyezhető az Azure-ban.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Ezeket az eszközöket akkor kell telepíteni, amikor először indítja el a Function alkalmazást a Visual Studio Code-ból.

1. A függvény teszteléséhez állítson be egy töréspontot a `Hello` tevékenység funkció kódjában (*Hello/run.ps1*). Nyomja le az F5 billentyűt, vagy válassza ki `Debug: Start Debugging` a parancssorból a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

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

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot. 

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 


1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](../../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Mappa kiválasztása**: válasszon egy mappát a munkaterületről, vagy tallózással keresse meg a Function alkalmazást tartalmazó mappát. Ez nem jelenik meg, ha már nyitva van egy érvényes Function alkalmazás.

    + **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `+ Create new Function App` elemet. (Ne válassza a `Advanced` lehetőséget, amely nem szerepel ebben a cikkben.)
      
    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy a Azure Functions egyedi legyen. 

    + **Válasszon egy futtatókörnyezetet**: válassza ki, hogy a PowerShell melyik verzióját helyileg futtatja. A `pwsh -version` parancs segítségével megtekintheti a verziót.

        > [!NOTE]
        > Előfordulhat, hogy a Azure Functions VS Code bővítmény nem támogatja a PowerShell 7 használatát. Ha a PowerShell 7 nem érhető el lehetőségként, válassza a PowerShell 6. x lehetőséget, majd [frissítse a verziót manuálisan](#update-function-app-ps7) a Function alkalmazás létrehozása után.

    + **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) . 
    
1.  Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, és neveket kell használni a Function app neve alapján:
    
    + Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
    + Szabványos Azure Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
    + Egy használati terv, amely a kiszolgáló nélküli Function alkalmazás mögöttes gazdagépét határozza meg. 
    + Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az azonos üzemeltetési csomagban található erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.
    + A Function alkalmazáshoz csatlakoztatott Application Insights-példány, amely a kiszolgáló nélküli függvény használatát követi nyomon.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés.

1. <a name="update-function-app-ps7"></a>Ha korábban nem tudta kijelölni a *PowerShell 7* alkalmazást a Function alkalmazás létrehozásakor, nyomja le az F1 billentyűt (vagy a CTRL/cmd + SHIFT + P billentyűkombinációt) a parancs palettájának megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Upload Local Settings...` . A megjelenő utasításokat követve válassza ki a létrehozott Function alkalmazást. Ha a rendszer felszólítja a meglévő beállítások felülírására, válassza a *nem az összes* lehetőséget.
    
1. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](../../../includes/media/functions-publish-project-vscode/function-create-notifications.png)


## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-triggert használó függvényt meghívó URL-címnek a következő formátumúnak kell lennie: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>Következő lépések

A Visual Studio Code használatával egy PowerShell tartós Function-alkalmazást hozhat létre és tehet közzé.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)
