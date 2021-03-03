---
title: Java-függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre Java-függvényeket, és hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetésére a Visual Studio Code Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/03/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 38ed05d8bf9f7714b9cd58449f565cf94cb481e5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748567"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Rövid útmutató: Java-függvény létrehozása az Azure-ban a Visual Studio Code használatával

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

A Visual Studio Code használatával létrehozhat egy olyan Java-függvényt, amely válaszol a HTTP-kérelmekre. Tesztelje a kódot helyileg, majd telepítse a Azure Functions kiszolgáló nélküli környezetébe.

A rövid útmutató elvégzésével néhány USD értékű vagy annál kisebb költséggel jár a <abbr title="Az Azure-használati számlázási adatokat fenntartó profil.">Azure-fiók</abbr>.

Ha a Visual Studio Code nem az Ön előnyben részesített fejlesztői eszköze, tekintse meg a Java-fejlesztőknek készült hasonló oktatóanyagokat a [Maven](create-first-function-cli-java.md), a [Gradle](./functions-create-first-java-gradle.md) és a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)használatával.

## <a name="1-prepare-your-environment"></a>1. a környezet előkészítése

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Aktív Azure-fiók <abbr title="Az alapszintű szervezeti struktúra, amelyben az Azure-ban kezelheti az erőforrásokat, jellemzően egy adott szervezeten belüli személyhez vagy részleghez társítva.">előfizetést</abbr>. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Java fejlesztői csomag](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója.

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [Java-bővítmény csomagja](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

<br/>
<hr/>

## <a name="2-create-your-local-functions-project"></a>2. <a name="create-an-azure-functions-project"></a> a helyi függvények projekt létrehozása

1. Válassza ki az Azure ikont a **tevékenység sávjában**, majd az **Azure: functions** területen válassza az **új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. **Válasszon egy címtárbeli helyet** a projekt munkaterülethez, majd válassza a **kiválasztás** lehetőséget.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `Java` elemet.

    + **Válassza ki a Java-verziót**: válassza ki `Java 8` vagy `Java 11` a Java-verziót, amelyen a függvények futnak az Azure-ban. Válassza ki a helyileg ellenőrzött Java-verziót.

    + **Adja meg a csoport azonosítóját**: válassza a lehetőséget `com.function` .

    + **Adja meg az összetevő azonosítóját**: válassza a lehetőséget `myFunction` .

    + **Adja meg a verziószámot**: válassza a lehetőséget `1.0-SNAPSHOT` .

    + **Adja meg a csomag nevét**: válassza a lehetőséget `com.function` .

    + **Adja meg az alkalmazás nevét**: válassza a lehetőséget `myFunction-12345` .

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

<br/>

<details>
<summary><strong>Nem hozható létre Function projekt?</strong></summary>

A helyi functions-projekt létrehozásakor feloldható leggyakoribb problémák a következők:
* Nincs telepítve a Azure Functions bővítmény. 
</details>

<br/>
<hr/>

## <a name="3-run-the-function-locally"></a>3. a függvény helyi futtatása

1. Nyomja le az <kbd>F5</kbd> billentyűt a Function app projekt elindításához.

1. A **terminálban** tekintse meg a helyileg futó függvény URL-végpontját.

    ![Helyi függvény VS Code kimenet](media/functions-create-first-function-vs-code/functions-vscode-f5.png)

1. Az alapszintű eszközök futtatásával nyissa meg az **Azure: functions** területen. A **függvények** területen bontsa ki a **helyi Project**  >  **functions** elemet. Kattintson a jobb gombbal a (Windows) vagy a <kbd>CTRL-</kbd> click (MacOS) elemre `HttpExample` , és válassza a **függvény végrehajtása most..**. lehetőséget.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="A függvény végrehajtása most a Visual Studio Code-ból":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` . Nyomja le az <kbd>ENTER</kbd> billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  

1. Ha a függvény helyileg fut, és egy választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés. A függvény végrehajtásával kapcsolatos információk a **Terminal** panelen jelennek meg.

1. Nyomja le a <kbd>CTRL + C</kbd> billentyűkombinációt az alapvető eszközök leállításához és a hibakereső leválasztásához.

<br/>

<details>
<summary><strong>Nem lehet helyileg futtatni a függvényt?</strong></summary>

A helyi functions-projektek futtatásakor feloldható leggyakoribb problémák a következők:
* Nincsenek telepítve a központi eszközök. 
*  Ha problémája van a Windows rendszeren, győződjön meg arról, hogy a Visual Studio Code-hoz készült alapértelmezett WSL nem a bash értékre van állítva. 
</details>

<br/>
<hr/>

## <a name="4-sign-in-to-azure"></a>4. Jelentkezzen be az Azure-ba

Az alkalmazás közzétételéhez jelentkezzen be az Azure-ba. Ha már bejelentkezett, ugorjon a következő szakaszra.

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza a **Bejelentkezés az Azure**-ba... lehetőséget.

    ![Bejelentkezés az Azure-ba a VS code-on belül](../../includes/media/functions-sign-in-vs-code/functions-sign-into-azure.png)

1. Ha a rendszer kéri a böngészőt, **válassza ki az Azure-fiókját** , és **Jelentkezzen** be az Azure-fiókja hitelesítő adataival.

1. Miután sikeresen bejelentkezett, zárjuk be az új böngészőablakot, és térjen vissza a Visual Studio Code-hoz.

<br/>
<hr/>

## <a name="5-publish-the-project-to-azure"></a>5. a projekt közzététele az Azure-ban

A kód első üzembe helyezése magában foglalja az Azure-előfizetésben található Function-erőforrás létrehozását.

1. Válassza ki az Azure ikont a tevékenység sávjában, majd az **Azure: functions** területen válassza az **üzembe helyezés az alkalmazásban** ... gombot.

    ![A projekt közzététele az Azure-ban](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Mappa kiválasztása**: válassza ki azt a mappát, amely a Function alkalmazást tartalmazza. 

    + **Előfizetés kiválasztása**: válassza ki a használni kívánt előfizetést. Ez nem jelenik meg, ha csak egy előfizetéssel rendelkezik.

    + **Azure-beli Függvényalkalmazás kiválasztása**: Válassza a `- Create new Function App` elemet.

    + **Adja meg a Function alkalmazás globálisan egyedi nevét**: írjon be egy, az Azure-ban egyedi nevet egy URL-útvonalon. A rendszer érvényesíti a beírt nevet a globális egyediség biztosítása érdekében.

    - **Válasszon egy helyet az új erőforrásokhoz**: a jobb teljesítmény érdekében válasszon egy Önhöz közeli [régiót](https://azure.microsoft.com/regions/) .

1. A függvényalkalmazás létrehozása és a telepítőcsomag alkalmazása után megjelenik egy értesítés. A létrehozási és a telepítési eredmények megtekintéséhez válassza a **kimenet megtekintése** lehetőséget.

    ![Teljes értesítés létrehozása](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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

<br/>
<hr/>

## <a name="6-run-the-function-in-azure"></a>6. Futtassa a függvényt az Azure-ban

1. Vissza az **Azure: functions** területen az oldalsó sávban, bontsa ki az előfizetést, az új Function **alkalmazást és a függvényeket**. Kattintson a jobb gombbal a (Windows) vagy a <kbd>CTRL-</kbd> click (MacOS) elemre `HttpExample` , és válassza a **függvény végrehajtása most..**. lehetőséget.

    :::image type="content" source="../../includes/media/functions-vs-code-run-remote/execute-function-now.png" alt-text="Az Azure Visual Studio Code-ból való végrehajtása most":::

1. Az **írja be a kérelem törzsében** megjelenik a kérelem üzenet törzsének értéke `{ "name": "Azure" }` . Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez.  

1. Amikor a függvény végrehajtja az Azure-ban, és választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés.

<br/>
<hr/>

## <a name="7-clean-up-resources"></a>7. erőforrások tisztítása

Ha nem tervezi a [következő lépés](#next-steps)megadását, törölje a Function alkalmazást és annak erőforrásait, hogy elkerülje a további költségek felmerülését.

1. A Visual Studio Code-ban válassza ki az Azure ikont a tevékenység sávján, majd válassza ki a functions (funkciók) elemet az oldalsó sávban.
1. Válassza ki a Function alkalmazást, majd kattintson a jobb gombbal, és válassza a **függvény alkalmazás törlése..**. lehetőséget.

<br/>
<hr/>

## <a name="next-steps"></a>Következő lépések

A függvény kibontásához adjon hozzá egy <abbr title="Az Azure Storage-ban egy függvényt társíthat egy tárolási sorhoz, hogy üzeneteket lehessen létrehozni a várólistán.">kimeneti kötés</abbr>. Ez a kötés a HTTP-kérelemből írja be a karakterláncot egy Azure Queue Storage-várólistában lévő üzenetbe.

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)
