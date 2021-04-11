---
title: JavaScript-függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre JavaScript-függvényt, majd hogyan teheti közzé a helyi Node.js projektet a Azure Functions kiszolgáló nélküli üzemeltetésében a Visual Studio Code Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom:
- devx-track-js
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cf47d04da51db898e667ef8b31d42d79c9f354e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101748591"
---
# <a name="quickstart-create-a-javascript-function-in-azure-using-visual-studio-code"></a>Gyors útmutató: JavaScript-függvény létrehozása az Azure-ban a Visual Studio Code használatával

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [JavaScript](create-first-function-vs-code-node.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [Java](create-first-function-vs-code-java.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [Python](create-first-function-vs-code-python.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Egyéb (Go/Rust)](create-first-function-vs-code-other.md)

A Visual Studio Code használatával létrehozhat egy JavaScript-függvényt, amely válaszol a HTTP-kérelmekre. Tesztelje a kódot helyileg, majd telepítse a Azure Functions kiszolgáló nélküli környezetébe.

A rövid útmutató elvégzésével néhány USD értékű vagy annál kisebb költséggel jár a <abbr title="Az Azure-fiók egy globálisan egyedi entitás, amely hozzáfér az Azure-szolgáltatásokhoz és az Azure-előfizetésekhez.">Azure-fiók</abbr>.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Azure-fiók <abbr title="Az Azure-előfizetés az erőforrások kiosztására használt logikai tároló a Azure-ban. Az összes erőforrás (például virtuális gépek, adatbázisok stb.) adatait tárolja.">aktív előfizetés</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Node.js 10.14.1 +](https://nodejs.org/)

+ [Visual Studio Code](https://code.visualstudio.com/)

+ [Azure functions bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) a Visual Studio Code-hoz.

+ [Azure Functions alapvető eszközök](functions-run-local.md?tabs=linux%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)

<hr/>
<br/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> a helyi függvények projekt létrehozása

1. Válassza ki az Azure ikont a <abbr title="">Tevékenység sáv</abbr>, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Válasszon egy címtárbeli helyet** a projekt munkaterülethez, majd válassza a **kiválasztás** lehetőséget. 

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `JavaScript` elemet.

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.




<br/>
<details>
<summary><strong>Nem hozható létre Function projekt?</strong></summary>

A helyi functions-projekt létrehozásakor feloldható leggyakoribb problémák a következők:
* Nincs telepítve a Azure Functions bővítmény. 
</details>

<hr/>
<br/>

## <a name="3-run-the-function-locally"></a>3. a függvény helyi futtatása


1. Nyomja le az <kbd>F5</kbd> billentyűt a Function app projekt elindításához. 

1. A **terminálban** tekintse meg a helyileg futó függvény URL-végpontját.

    ![Helyi függvény VS Code kimenet](../../includes/media/functions-run-function-test-local-vs-code/functions-vscode-f5.png)

1. Másolja a következő URL-címet, és illessze be a böngészőben, majd nyomja le az ENTER billentyűt.

    `http://localhost:7071/api/HttpExample?name=Functions`

1. A visszaadott válasz megtekintése.


    ![Böngésző – a localhost példa kimenete](./media/create-first-function-vs-code-other/functions-test-local-browser.png)

1. A kérelemmel kapcsolatos információk megtekintése a **terminál** paneljén.

    ![A feladat gazdagépének indítása – VS Code-terminál kimenete](../../includes/media/functions-run-function-test-local-vs-code/function-execution-terminal.png)

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.

<br/>
<details>
<summary><strong>Nem lehet helyileg futtatni a függvényt?</strong></summary>

A helyi functions-projektek futtatásakor feloldható leggyakoribb problémák a következők:
* Nincsenek telepítve a központi eszközök. 
*  Ha problémája van a Windows rendszeren, győződjön meg arról, hogy a Visual Studio Code-hoz készült alapértelmezett WSL nem a bash értékre van állítva. 
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

    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy, az Azure-ban egyedi nevet egy URL-útvonalon. A rendszer érvényesíti a beírt nevet a globális egyediség biztosítása érdekében.

    + **Válasszon egy futtatókörnyezetet**: válassza ki a helyileg futtatott Node.js verzióját. A `node --version` parancs segítségével megtekintheti a verziót.

    + **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) . 

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
1. Az **Azure: functions** oldali sávban bontsa ki az új Function alkalmazást. 
1. Bontsa ki a **függvények** csomópontot, majd kattintson a jobb gombbal a **HttpExample** elemre, majd válassza a **függvény végrehajtása most...** lehetőséget.

    ![A függvény URL-címének másolása az új HTTP-triggerhez](../../includes/media/functions-vs-code-run-remote/execute-function-now.png)

1. Az **ENTER billentyű lenyomásával** elküldheti az alapértelmezett kérési üzenetet a függvénynek. 

1. A művelet végrehajtásának befejeződése után a Visual Studio Code-ban értesítést kap.

<br/>
<details>
<summary><strong>Nem sikerült futtatni a felhőalapú Function alkalmazást?</strong></summary>

* Megjegyezte, hogy hozzáadja a querystring az URL-cím végéhez?

</details>

<hr/>
<br/>

## <a name="7-clean-up-resources"></a>7. erőforrások tisztítása

Törölje a Function alkalmazást és annak erőforrásait, hogy elkerülje a további költségek felmerülését.

1. A Visual Studio Code-ban válassza ki az Azure ikont a tevékenység sávján, majd válassza ki a functions (funkciók) elemet az oldalsó sávban. 
1. Válassza ki a Function alkalmazást, majd kattintson a jobb gombbal, és válassza a **függvény alkalmazás törlése..**. lehetőséget.

<hr/>
<br/>

## <a name="next-steps"></a>Következő lépések

A függvény kibontásához adjon hozzá egy <abbr title="A függvények kötése egy másik erőforrás a függvényhez való deklaratív csatlakoztatásának módja.">kimeneti kötés</abbr>. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe. 

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-javascript)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
