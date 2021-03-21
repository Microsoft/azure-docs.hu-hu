---
title: Python-függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre Python-függvényeket, és hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetésére a Visual Studio Code Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 42f07b76cefed38aad53caba9ba35c74238540fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031735"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Gyors útmutató: függvény létrehozása az Azure-ban a Python használatával a Visual Studio Code-ban

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Egyéb (Go/Rust)](create-first-function-vs-code-other.md)

Ebben a cikkben a Visual Studio Code használatával hozzon létre egy Python-függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően telepítse azt a <abbr title="Egy futásidejű számítástechnikai környezet, amelyben a kiszolgáló összes adata átlátható az alkalmazások fejlesztői számára, ami leegyszerűsíti a kód üzembe helyezésének és kezelésének folyamatát.">kiszolgáló nélküli</abbr> környezet <abbr title="Egy Azure-szolgáltatás, amely alacsony költséghatékonyságú, kiszolgáló nélküli számítási környezetet biztosít az alkalmazásokhoz.">Azure Functions</abbr>.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

Ennek a cikknek a [CLI-alapú verziója](create-first-function-cli-python.md) is létezik.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Egy Azure <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">account</abbr> aktív <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 3. x verzió.

+ [Python 3,8](https://www.python.org/downloads/release/python-381/), [Python 3,7](https://www.python.org/downloads/release/python-375/), [Python 3,6](https://www.python.org/downloads/release/python-368/) Azure functions (x64) támogatja.

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz.  

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

<hr/>
<br/>

## <a name="2-create-your-local-project"></a>2. <a name="create-an-azure-functions-project"></a> a helyi projekt létrehozása

1. Válassza ki az Azure ikont a <abbr title="A Visual Studio Code ablak bal oldalán lévő ikonok függőleges csoportja.">Tevékenység sáv</abbr>, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `Python` elemet.

    + **Válasszon ki egy Python-aliast a virtuális környezet létrehozásához**: válassza ki a Python-tolmács helyét. Ha a hely nem jelenik meg, írja be a Python bináris fájl teljes elérési útját.  

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szintekről az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakörben olvashat bővebben.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

<br/>
<details>
<summary><strong>Nem hozható létre Function projekt?</strong></summary>

A helyi functions-projekt létrehozásakor feloldható leggyakoribb problémák a következők:
* Nincs telepítve a Azure Functions bővítmény. 
</details>

<hr/>
<br/>

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. Nyomja le az <kbd>F5</kbd> billentyűt a Function app projekt elindításához.

1. A **terminál** panelen tekintse meg a helyileg futó függvény URL-végpontját.

    ![Helyi függvény VS Code kimenet](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)


1. Az alapszintű eszközök futtatásával nyissa meg az **Azure: functions** területen. A **függvények** területen bontsa ki a **helyi Project**  >  **functions** elemet. Kattintson a jobb gombbal a (Windows) vagy a <kbd>CTRL-</kbd> click (MacOS) elemre `HttpExample` , és válassza a **függvény végrehajtása most..**. lehetőséget.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="A függvény végrehajtása most a Visual Studio Code-ból":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` . Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  

1. Ha a függvény helyileg fut, és egy választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés. A függvény végrehajtásával kapcsolatos információk a **Terminal** panelen jelennek meg.

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.

<br/>
<details>
<summary><strong>Nem lehet helyileg futtatni a függvényt?</strong></summary>

A helyi functions-projektek futtatásakor feloldható leggyakoribb problémák a következők:
* Nincsenek telepítve a központi eszközök. 
*  Ha problémája van a Windows rendszeren, győződjön meg arról, hogy a Visual Studio Code-hoz készült alapértelmezett WSL nem a **bash** értékre van állítva. 
</details>

<hr/>
<br/>

## <a name="4-sign-in-to-azure"></a>4. Jelentkezzen be az Azure-ba

Az alkalmazás közzétételéhez jelentkezzen be az Azure-ba. Ha már bejelentkezett, ugorjon a következő szakaszra.

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza a **Bejelentkezés az Azure**-ba... lehetőséget.

    ![Bejelentkezés az Azure-ba a VS code-on belül](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Ha a rendszer kéri a böngészőt, **válassza ki az Azure-fiókját** , és **Jelentkezzen** be az Azure-fiókja hitelesítő adataival.

1. Miután sikeresen bejelentkezett, zárjuk be az új böngészőablakot, és térjen vissza a Visual Studio Code-hoz. 

<hr/>
<br/>

## <a name="5-publish-the-project-to-azure"></a>5. a projekt közzététele az Azure-ban

A kód első üzembe helyezése magában foglalja az Azure-előfizetésben található Function-erőforrás létrehozását.

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Mappa kiválasztása**: válassza ki azt a mappát, amely a Function alkalmazást tartalmazza.

    + **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `+ Create new Function App` elemet.

    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy URL-útvonalon érvényes nevet. A rendszer érvényesíti a beírt nevet, hogy megbizonyosodjon róla, hogy <abbr title="A névnek globálisan egyedinek kell lennie az összes Azure-ügyfélen. Használhatja például a személyes vagy szervezet neve, az alkalmazás neve és a numerikus azonosító kombinációját, mint a contoso-bizapp-függvény-20.">egyedi az Azure-ban</abbr>. 

    + **Válasszon egy futtatókörnyezetet**: válassza ki, hogy a Python melyik verzióját helyileg futtatja. A `python --version` parancs segítségével megtekintheti a verziót.

    + **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) .

    A bővítmény megjeleníti az egyes erőforrások állapotát az értesítési terület Azure-ban való létrehozásakor.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Értesítés az Azure-erőforrások létrehozásáról":::

1. A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. A létrehozási és a telepítési eredmények megtekintéséhez válassza a **kimenet megtekintése** lehetőséget. 

    ![Teljes értesítés létrehozása](./media/functions-create-first-function-vs-code/function-create-notifications.png)

<br/>
<details>
<summary><strong>Nem tudja közzétenni a függvényt?</strong></summary>

Ez a szakasz létrehozta az Azure-erőforrásokat, és üzembe helyezte a helyi kódot a Function alkalmazásban. Ha ez nem sikerült:

* Tekintse át a hibaüzenetek kimenetét. A jobb alsó sarokban található harang ikon egy másik lehetőség a kimenet megtekintéséhez. 
* Közzétett egy meglévő Function alkalmazást? Ez a művelet felülírja az alkalmazás tartalmát az Azure-ban.
</details>


<br/>
<details>
<summary><strong>Milyen erőforrásokat hozott létre?</strong></summary>

Ha elkészült, a következő Azure-erőforrások jönnek létre az előfizetésben, és neveket kell használni a Function app neve alapján: 
* **Erőforráscsoport**: az erőforráscsoport logikai tároló az azonos régióban található kapcsolódó erőforrásokhoz.
* **Azure Storage-fiók**: a tárolási erőforrások megőrzik a projekt állapotát és egyéb információit.
* Használati **terv**: egy használati terv határozza meg a kiszolgáló nélküli Function alkalmazás mögöttes gazdagépét.
* **Function alkalmazás**: a Function alkalmazás biztosítja a környezetet a függvény kódjának és csoportosítási funkcióinak logikai egységként való végrehajtásához.
* **Application Insights**: a Application Insights nyomon követi a kiszolgáló nélküli függvény használatát.

</details>

<hr/>
<br/>

## <a name="6-run-the-function-in-azure"></a>6. Futtassa a függvényt az Azure-ban

1. Vissza az **Azure: functions** oldali sávban, és bontsa ki az új Function alkalmazást.
1. Bontsa ki a **függvények** csomópontot, majd kattintson a jobb gombbal (Windows) vagy a <kbd>CTRL +</kbd> kattintás (MacOS) elemre, `HttpExample` és válassza a **függvény végrehajtása most...** lehetőséget.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Az Azure Visual Studio Code-ból való végrehajtása most":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` .

    Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  

1. Amikor a függvény végrehajtja az Azure-ban, és választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés.

## <a name="7-clean-up-resources"></a>7. erőforrások tisztítása

Ha folytatja a [következő lépéssel](#next-steps) , és hozzáad egy <abbr title="Azt jelenti, hogy a függvényt egy tárolási várólistához kell rendelni, hogy üzeneteket lehessen létrehozni a várólistán.">Azure Storage-üzenetsor kimeneti kötése</abbr> a függvénynek az összes erőforrását meg kell őriznie, hogy a már elvégzett funkciókra építsen.

Ellenkező esetben a következő lépésekkel törölheti a Function alkalmazást és annak kapcsolódó erőforrásait, hogy elkerülje a további költségek felmerülését.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

A functions-költségekkel kapcsolatos további tudnivalókért lásd: a [felhasználási terv költségeinek becslése](functions-consumption-costs.md).

## <a name="next-steps"></a>Következő lépések

A függvény kibontása kimenet hozzáadásával <abbr title="Egy függvény és más erőforrások deklaratív kapcsolata. A bemeneti kötés adatokat biztosít a függvénynek; a kimeneti kötések adatokat biztosítanak a függvénytől más erőforrásoknak.">kötés</abbr>. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Problémák léptek fel? Tudassa velünk.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
