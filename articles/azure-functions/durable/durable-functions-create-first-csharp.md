---
title: 'Az első tartós függvény létrehozása az Azure-ban C használatával #'
description: Azure tartós függvény létrehozása és közzététele a Visual Studio vagy a Visual Studio Code használatával.
author: jeffhollan
ms.topic: quickstart
ms.date: 03/18/2020
ms.author: azfuncdf
zone_pivot_groups: code-editors-set-one
ms.openlocfilehash: d5b6ecc12cee983cee0772da8b6f8f26a3b5a8ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96008188"
---
# <a name="create-your-first-durable-function-in-c"></a>Hozza létre első tartós funkcióját a C-ben\#

A *Durable Functions* [Azure functions](../functions-overview.md) , amely lehetővé teszi állapot-nyilvántartó függvények írására kiszolgáló nélküli környezetben. A bővítmény automatikusan kezeli az állapotokat, az ellenőrzőpontokat és az újraindításokat.

::: zone pivot="code-editor-vscode"

Ebből a cikkből megtudhatja, hogyan használhatja a Visual Studio Code-ot a "Hello World" tartós funkciójának helyi létrehozására és tesztelésére.  Ez a függvény összehangolja és láncokba rendezi a más függvények hívásait. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök a VS Code [Azure functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)részeként érhetők el.

![Képernyőfelvétel: a Visual Studio Code ablak tartós funkcióval rendelkezik.](./media/durable-functions-create-first-csharp/functions-vscode-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.

* Telepítse a következő VS Code-bővítményeket:
    - [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
    - [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)

* Győződjön meg arról, hogy rendelkezik a [Azure functions Core Tools](../functions-run-local.md)legújabb verziójával.

* Durable Functions Azure Storage-fiókra van szükség. Szüksége van egy Azure-előfizetésre.

* Ellenőrizze, hogy telepítve van-e a [.NET Core SDK](https://dotnet.microsoft.com/download) 3,1 verziója vagy újabb verziója.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása 

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions projektet. 

1. A Visual Studio Code-ban nyomja le az F1 billentyűt (vagy a CTRL/cmd + SHIFT + P billentyűkombinációt) a parancs paletta megnyitásához. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create New Project...` .

    ![Függvényprojekt létrehozása](media/durable-functions-create-first-csharp/functions-vscode-create-project.png)

1. Válasszon egy üres mappát a projekthez, és válassza a **kiválasztás** lehetőséget.

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Válasszon nyelvet a Function app-projekthez | C# | Hozzon létre egy helyi C# functions projektet. |
    | Válasszon verziót | Azure Functions v3 | Ez a beállítás csak akkor jelenik meg, ha az alapvető eszközök még nincsenek telepítve. Ebben az esetben a rendszer az alkalmazás első futtatásakor telepíti az alapvető eszközöket. |
    | Válasszon sablont a projekt első függvényéhez | Kihagyás | |
    | Válassza ki, hogyan szeretné megnyitni a projektet? | Megnyitás az aktuális ablakban | Nyissa meg újra a VS Code-t a kiválasztott mappában. |

A Visual Studio Code szükség esetén telepíti a Azure Functions Core Tools. Emellett egy Function app-projektet is létrehoz egy mappában. Ez a projekt tartalmazza a konfigurációs fájlok [host.js](../functions-host-json.md) és [local.settings.js](../functions-run-local.md#local-settings-file) .

## <a name="add-functions-to-the-app"></a>Függvények hozzáadása az alkalmazáshoz

A következő lépésekkel hozhatja létre a projektben a tartós függvény kódját.

1. A parancs palettáján keresse meg és válassza ki a következőt: `Azure Functions: Create Function...` .

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Sablon kiválasztása a függvényhez | DurableFunctionsOrchestration | Durable Functions-előkészítés létrehozása |
    | Adja meg a függvény nevét | HelloOrchestration | Azon osztály neve, amelyben a függvények jönnek létre |
    | Névtér megadása | Vállalati. függvény | A generált osztály névtere |

1. Ha a VS Code megkéri a Storage-fiók kiválasztására, válassza a **Storage-fiók kiválasztása** lehetőséget. Az utasításokat követve adja meg a következő információkat egy új Storage-fiók létrehozásához az Azure-ban.

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Előfizetés kiválasztása | *az előfizetés neve* | Válassza ki az Azure-előfizetését |
    | Válasszon Storage-fiókot | Új tárfiók létrehozása |  |
    | Adja meg az új Storage-fiók nevét | *egyedi név* | A létrehozandó Storage-fiók neve |
    | Erőforráscsoport kiválasztása | *egyedi név* | A létrehozandó erőforráscsoport neve |
    | Hely kiválasztása | *régió* | Válasszon ki egy régiót a közelben |

Az új függvényeket tartalmazó osztályt a projekthez adja hozzá a rendszer. A VS Code emellett hozzáadja a Storage-fiókhoz tartozó kapcsolódási karakterláncot a *local.settings.jshoz* , valamint a [`Microsoft.Azure.WebJobs.Extensions.DurableTask`](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask) NuGet-csomagra mutató hivatkozást a *. csproj* Project fájlra.

A tartalom megtekintéséhez nyissa meg az új *HelloOrchestration. cs* fájlt. Ez a tartós függvény egy egyszerű függvények láncolására szolgáló példa a következő módszerekkel:  

| Metódus | Függvénynév | Description |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `HelloOrchestration` | Kezeli a tartós összeszerelést. Ebben az esetben a folyamat elindul, létrehoz egy listát, és hozzáadja a listához a három függvény hívásának eredményét.  Ha a három függvény meghívása befejeződött, a rendszer visszaadja a listát. |
| **`SayHello`** | `HelloOrchestration_Hello` | A függvény egy Hello értéket ad vissza. Ez az a függvény, amely az összehangolt üzleti logikát tartalmazza. |
| **`HttpStart`** | `HelloOrchestration_HttpStart` | [Http-triggert használó függvény](../functions-bindings-http-webhook.md) , amely elindítja a folyamat egy példányát, és visszaadja az állapot-ellenőrzési választ. |

Most, hogy létrehozta a függvény projektjét és egy tartós függvényt, tesztelheti a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studio Code-ból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez állítson be egy töréspontot a `SayHello` tevékenység funkció kódjában, és nyomja le az F5 billentyűt a Function app projekt elindításához. A Core Tools kimenete a **Terminal** (Terminál) panelen jelenik meg.

    > [!NOTE]
    > A hibakereséssel kapcsolatos további információkért tekintse meg a [Durable functions diagnosztikát](durable-functions-diagnostics.md#debugging) .

1. A **Terminal** (Terminál) panelen másolja a vágólapra a HTTP által indított függvény URL-végpontját.

    ![Az Azure helyi kimenete](media/durable-functions-create-first-csharp/functions-vscode-f5.png)

1. Ha például a [Poster](https://www.getpostman.com/) vagy a [curl](https://curl.haxx.se/)eszközt használja, küldjön egy HTTP POST-kérelmet az URL-végpontnak.

   A válasz a HTTP-függvény kezdeti eredménye, amely közli, hogy a tartós összehangolás sikeresen elindult. Még nem az előkészítés végeredménye. A válasz több hasznos URL-címet is tartalmaz. Most pedig lekérdezjük a folyamat állapotát.

1. Másolja ki az URL-címet, `statusQueryGetUri` és illessze be a böngésző címsorába, majd hajtsa végre a kérelmet. Azt is megteheti, hogy továbbra is a Poster használatával adja ki a GET kérelmet.

   A kérelem lekérdezi az állapotot az előkészítési példányon. Egy végleges választ kap, amely megmutatja, hogy a példány befejeződik, és tartalmazza a tartós funkció kimeneteit vagy eredményét. A következőképpen néz ki: 

    ```json
    {
        "name": "HelloOrchestration",
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

1. Másolja a vágólapra a HTTP-eseményindító URL-címét az **Output** (Kimenet) panelről. A HTTP-eseményindítót használó függvényt meghívó URL-címnek az alábbi formátumban kell lennie:

    `https://<functionappname>.azurewebsites.net/api/HelloOrchestration_HttpStart`

1. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>Következő lépések

A Visual Studio Code használatával C# tartós Function-alkalmazást hozhat létre és tehet közzé.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)

::: zone-end

::: zone pivot="code-editor-visualstudio"

Ebből a cikkből megtudhatja, hogyan hozhat létre és tesztelheti a "Hello World" tartós funkciót a Visual Studio 2019 használatával.  Ez a függvény összehangolja és láncokba rendezi a más függvények hívásait. Ezután közzéteheti a függvénykódot az Azure-ban. Ezek az eszközök az Azure-fejlesztési számítási feladatok részeként érhetők el a Visual Studio 2019-ben.

![A képernyőfelvételen a Visual Studio 2019-ablaka tartós funkcióval jelenik meg.](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* Telepítse a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)alkalmazást. Győződjön meg arról, hogy az **Azure-fejlesztési** munkaterhelés is telepítve van. A Visual Studio 2017 támogatja a Durable Functions fejlesztést is, de a felhasználói felület és a lépések eltérnek egymástól.

* Ellenőrizze, hogy az [Azure Storage-emulátor](../../storage/common/storage-use-emulator.md) telepítve van-e és fut-e.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

A Azure Functions sablon létrehoz egy projektet, amely közzétehető egy Azure-beli Function alkalmazásban. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése, skálázása és megosztása érdekében.

1. A Visual Studio **Fájl** menüjében válassza az **Új** > **Projekt** lehetőséget.

1. Az **új projekt létrehozása** párbeszédpanelen keresse meg a elemet, `functions` válassza ki a **Azure functions** sablont, és kattintson a **tovább** gombra. 

    ![Új projekt párbeszédpanel a Visual Studióban egy függvény létrehozásához](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Írjon be egy **projekt nevét** a projekthez, majd kattintson **az OK gombra**. A projekt nevének érvényesnek kell lennie C# névtérként, ezért ne használjon aláhúzást, kötőjelet vagy más nem alfanumerikus karaktereket.

1. Az **új Azure functions alkalmazás létrehozása** területen használja a képet követő táblázatban megadott beállításokat.

    ![Új Azure Functions alkalmazás párbeszédpanel létrehozása a Visual Studióban](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Beállítás      | Ajánlott érték  | Leírás                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Verzió** | Azure Functions 3,0 <br />(.NET Core) | Egy olyan Function-projektet hoz létre, amely az Azure Functions 3,0-es verzióját használja, amely támogatja a .NET Core 3,1-et. További információ: [Azure Functions futtatókörnyezet-verzió megcélzása](../functions-versions.md).   |
    | **Sablon** | Üres | Létrehoz egy üres Function alkalmazást. |
    | **Storage-fiók**  | Storage Emulator | A tartós működés állapotának kezeléséhez Storage-fiókra van szükség. |

4. Válassza a **Létrehozás** lehetőséget egy üres Function projekt létrehozásához. Ez a projekt a függvények futtatásához szükséges alapszintű konfigurációs fájlokat tartalmaz.

## <a name="add-functions-to-the-app"></a>Függvények hozzáadása az alkalmazáshoz

A következő lépésekkel hozhatja létre a projektben a tartós függvény kódját.

1. Kattintson a jobb gombbal a projektre a Visual Studióban, majd válassza az   >  **új Azure-függvény** hozzáadása lehetőséget.

    ![Új függvény hozzáadása](./media/durable-functions-create-first-csharp/functions-vs-add-function.png)

1. Ellenőrizze, hogy az **Azure Function** be van-e jelölve a Hozzáadás menüben, írja be a C#-fájl nevét, majd válassza a **Hozzáadás** lehetőséget.

1. Válassza ki a **Durable functions a hangszerelési** sablont, majd kattintson **az OK gombra** .

    ![Tartós sablon kiválasztása](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)

A rendszer új tartós függvényt ad hozzá az alkalmazáshoz.  A tartalom megtekintéséhez nyissa meg az új. cs fájlt. Ez a tartós függvény egy egyszerű függvények láncolására szolgáló példa a következő módszerekkel:  

| Metódus | Függvénynév | Description |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Kezeli a tartós összeszerelést. Ebben az esetben a folyamat elindul, létrehoz egy listát, és hozzáadja a listához a három függvény hívásának eredményét.  Ha a három függvény meghívása befejeződött, a rendszer visszaadja a listát. |
| **`SayHello`** | `<file-name>_Hello` | A függvény egy Hello értéket ad vissza. Ez az a függvény, amely az összehangolt üzleti logikát tartalmazza. |
| **`HttpStart`** | `<file-name>_HttpStart` | [Http-triggert használó függvény](../functions-bindings-http-webhook.md) , amely elindítja a folyamat egy példányát, és visszaadja az állapot-ellenőrzési választ. |

Most, hogy létrehozta a függvény projektjét és egy tartós függvényt, tesztelheti a helyi számítógépen.

## <a name="test-the-function-locally"></a>A függvény helyi tesztelése

Az Azure Functions Core Tools lehetővé teszi Azure Functions-projektek helyi fejlesztői számítógépen való futtatását. Amikor a Visual Studióból először indít el egy függvényt, a rendszer arra kéri, hogy telepítse ezeket az eszközöket.

1. A függvény teszteléséhez nyomja le az F5 billentyűt. Ha a rendszer kéri, fogadja el a Visual Studio kérését az Azure Functions Core (CLI) eszközök telepítéséhez. Lehet, hogy egy tűzfalkivételt is engedélyeznie kell, hogy az eszközök kezelhessék a HTTP-kéréseket.

2. Másolja a függvény URL-címét az Azure-függvény futtatókörnyezetéből.

    ![Az Azure helyi futtatókörnyezete](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. Illessze be a HTTP-kérelem URL-címét a böngésző címsorába, és hajtsa végre a kérelmet. Az alábbiakban látható a böngészőben a helyi GET kérelemre a függvény által visszaadott válasz:

    ![A képernyőképen egy böngészőablak jelenik meg, amelyen a statusQueryGetUri meghívták.](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    A válasz a HTTP-függvény kezdeti eredménye, amely közli, hogy a tartós összehangolás sikeresen elindult.  Még nem az előkészítés végeredménye.  A válasz több hasznos URL-címet is tartalmaz.  Most pedig lekérdezjük a folyamat állapotát.

4. Másolja ki és illessze be az URL-címet `statusQueryGetUri` a böngésző címsorába, majd hajtsa végre a kérelmet.

    A kérelem lekérdezi az állapotot az előkészítési példányon. A következőhöz hasonló, végleges választ kaphat.  Ez a kimenet mutatja, hogy a példány befejeződött, és tartalmazza a tartós függvény kimeneteit vagy eredményét.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. A hibakeresés leállításához nyomja le a **Shift + F5** billentyűkombinációt.

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzétételéhez rendelkeznie kell egy függvényalkalmazással.az Azure-előfizetéséhez. Közvetlenül a Visual Studióból is létrehozhat függvényalkalmazást.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. Másolja a függvényalkalmazás alap URL-címét a Publish (Közzététel) profiloldalról. Cserélje ki a függvény helyi tesztelésekor használt `localhost:port` URL-címrészt az új alap URL-címmel.

    A tartós függvény HTTP-triggerét meghívó URL-címnek a következő formátumúnak kell lennie:

    `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart`

2. Illessze be a HTTP-kérelem új URL-címét a böngésző címsorába. Ugyanezt az állapot-választ kell megadnia, mint korábban a közzétett alkalmazás használatakor.

## <a name="next-steps"></a>Következő lépések

A Visual Studio használatával C# tartós Function-alkalmazást hozhat létre és tesz közzé.

> [!div class="nextstepaction"]
> [További tudnivalók a tartós függvények gyakori mintái](durable-functions-overview.md#application-patterns)

::: zone-end
