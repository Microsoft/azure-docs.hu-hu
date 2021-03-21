---
title: C#-függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre C#-függvényeket, és hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetésére a Visual Studio Code Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2848ce6214d59ba2732dcfc148ccaf9936497f17
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121031"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Rövid útmutató: C#-függvény létrehozása az Azure-ban a Visual Studio Code használatával

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Egyéb (Go/Rust)](create-first-function-vs-code-other.md)

Ebben a cikkben a Visual Studio Code használatával hozzon létre egy C# szintű függvénytár-alapú függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően telepítse azt a <abbr title="Egy futásidejű számítástechnikai környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazás-fejlesztők számára, leegyszerűsítve a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezet <abbr title="Az Azure szolgáltatása, amely alacsony költséghatékonyságú, kiszolgáló nélküli számítási környezetet biztosít az alkalmazásokhoz.">Azure Functions</abbr>.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

Ennek a cikknek a [CLI-alapú verziója](create-first-function-cli-csharp.md) is létezik.
    
## <a name="1-configure-your-environment"></a>1. a környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Egy Azure <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">account</abbr> aktív <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 3. x verzió.

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A Visual Studio Code [C#-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

## <a name="2-create-your-local-project"></a><a name="create-an-azure-functions-project"></a>2. a helyi projekt létrehozása

Ebben a szakaszban a Visual Studio Code használatával hozhat létre helyi <abbr title="Egy vagy több, együtt telepíthető és felügyelhető különálló függvény logikai tárolója.">Azure Functions projekt</abbr> a C# nyelvben. A cikk későbbi részében közzéteszi a függvény kódját az Azure-ban.

1. Válassza ki az Azure ikont a <abbr title="A Visual Studio Code ablak bal oldalán lévő ikonok függőleges csoportja.">Tevékenység sáv</abbr>, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `C#` elemet.

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Adjon meg egy névteret**: Gépelje be: `My.Functions`.

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szintekről az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakörben olvashat bővebben.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-vel <abbr title="A függvény kódját meghívó esemény típusa, például egy HTTP-kérelem, egy üzenetsor-üzenet vagy egy adott időpont.">eseményindító</abbr>. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="5-publish-the-project-to-azure"></a>5. a projekt közzététele az Azure-ban

Ebben a szakaszban egy Function-alkalmazást és egy kapcsolódó erőforrást hoz létre az Azure-előfizetésében, majd üzembe helyezi a kódot. 

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban. 

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Mappa kiválasztása**: válasszon egy mappát a munkaterületről, vagy tallózással keresse meg a Function alkalmazást tartalmazó mappát. Ez nem jelenik meg, ha már nyitva van egy érvényes Function alkalmazás.

    + **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `+ Create new Function App` elemet. (Ne válassza a `Advanced` lehetőséget, amely nem szerepel ebben a cikkben.)

    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy megbizonyosodjon róla, hogy <abbr title="A névnek egyedinek kell lennie az összes Azure-ügyfél által a globálisan használt összes functions projektben. Általában a saját vagy a vállalat neve, az alkalmazás neve és a numerikus azonosító kombinációját használja, mint a contoso-bizapp-függvény-20">egyedi a Azure Functionsban</abbr>. 

    + **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) .

    A bővítmény megjeleníti az egyes erőforrások állapotát az értesítési terület Azure-ban való létrehozásakor.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Értesítés az Azure-erőforrások létrehozásáról":::

1. Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, és neveket kell használni a Function app neve alapján:

    + Egy **erőforráscsoport**, amely a kapcsolódó erőforrások logikai tárolója.
    + Szabványos **Azure Storage-fiók**, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
    + Egy használati **terv**, amely a kiszolgáló nélküli Function alkalmazás mögöttes gazdagépét határozza meg. 
    + Egy **Function alkalmazás**, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function app lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az azonos üzemeltetési csomagban található erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.
    + A Function alkalmazáshoz csatlakoztatott **Application Insights-példány** , amely a kiszolgáló nélküli függvény használatát követi nyomon.

    A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. 

    > [!TIP]
    > Alapértelmezés szerint a Function alkalmazás által igényelt Azure-erőforrások létrehozása a megadott Function alkalmazás neve alapján történik. Alapértelmezés szerint a függvény alkalmazással azonos új erőforráscsoport is létrejön. Ha testre szeretné szabni ezeknek az erőforrásoknak a nevét, vagy meglévő erőforrásokat szeretne használni, akkor [a projektet a speciális létrehozási beállításokkal kell közzétennie](functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).


1. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is. Ha kihagyja az értesítést, válassza a jobb alsó sarokban található harang ikont az újbóli megjelenítéshez.

    ![Teljes értesítés létrehozása](./media/functions-create-first-function-vs-code/function-create-notifications.png)

## <a name="6-run-the-function-in-azure"></a>6. Futtassa a függvényt az Azure-ban

1. Vissza az **Azure: functions** területen az oldalsó sávban, bontsa ki az előfizetést, az új Function **alkalmazást és a függvényeket**. Kattintson a jobb gombbal a (Windows) vagy a <kbd>CTRL-</kbd> click (MacOS) elemre `HttpExample` , és válassza a **függvény végrehajtása most..**. lehetőséget.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Az Azure Visual Studio Code-ból való végrehajtása most":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` .

    Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  

1. Amikor a függvény végrehajtja az Azure-ban, és választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés.

## <a name="5-clean-up-resources"></a>5. erőforrások tisztítása

Ha folytatja a [következő lépéssel](#next-steps) , és hozzáad egy <abbr title="Azt jelenti, hogy a függvényt egy tárolási várólistához kell rendelni, hogy üzeneteket lehessen létrehozni a várólistán.">Azure Storage-üzenetsor kimeneti kötése</abbr> a függvénynek az összes erőforrását meg kell őriznie, hogy a már elvégzett funkciókra építsen.

Ellenkező esetben a következő lépésekkel törölheti a Function alkalmazást és annak kapcsolódó erőforrásait, hogy elkerülje a további költségek felmerülését.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

A functions-költségekkel kapcsolatos további tudnivalókért lásd: a [felhasználási terv költségeinek becslése](functions-consumption-costs.md).

## <a name="next-steps"></a>Következő lépések

A Visual Studio Code segítéségével létrehozott egy egyszerű, HTTP-eseményindítóval aktivált függvényt tartalmazó függvényalkalmazást. A következő cikkben kiterjesztheti a függvényt egy kimenet hozzáadásával <abbr title="Egy függvény és más erőforrások deklaratív kapcsolata. A bemeneti kötés adatokat biztosít a függvénynek; a kimeneti kötések adatokat biztosítanak a függvénytől más erőforrásoknak.">kötés</abbr>. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
