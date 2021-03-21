---
title: Az Azure Functions fejlesztése a Visual Studio Code használatával
description: Megtudhatja, hogyan fejlesztheti és tesztelheti Azure Functions a Visual Studio Code-hoz készült Azure Functions bővítménnyel.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: d4353e6be313d61716933879efa930e22472781b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493946"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Az Azure Functions fejlesztése a Visual Studio Code használatával

A [Visual Studio Code Azure functions-bővítménye] lehetővé teszi a függvények helyi fejlesztését és üzembe helyezését az Azure-ban. Ha ez a felhasználói élmény Azure Functions, akkor a [Azure functions bevezetésével](functions-overview.md)többet is megtudhat.

A Azure Functions bővítmény a következő előnyöket biztosítja:

* Függvények szerkesztése, létrehozása és futtatása a helyi fejlesztői számítógépen.
* Tegye közzé a Azure Functions projektet közvetlenül az Azure-ban.
* A Visual Studio Code előnyeinek kihasználásával különböző nyelveken írhat függvényeket.

A bővítmény a következő nyelvekkel használható, amelyeket a Azure Functions Runtime a 2. x verziótól kezdődően támogat:

* [C# lefordítva](functions-dotnet-class-library.md)
* [C#-parancsfájl](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Ehhez az szükséges, hogy [a C#-szkriptet a projekt alapértelmezett nyelvének megfelelően állítsa be](#c-script-projects).

Ebben a cikkben a példák jelenleg csak a JavaScript (Node.js) és a C# Class Library függvények esetében érhetők el.  

Ez a cikk részletesen ismerteti, hogyan használhatók a Azure Functions bővítmény a függvények fejlesztéséhez és az Azure-ban való közzétételéhez. A cikk elolvasása előtt létre kell [hoznia az első függvényt a Visual Studio Code használatával](./create-first-function-vs-code-csharp.md).

> [!IMPORTANT]
> Egyetlen Function-alkalmazás esetében ne keverje a helyi fejlesztést és a portál fejlesztését. Amikor egy helyi projektből tesz közzé egy Function alkalmazást, a telepítési folyamat felülírja a portálon kifejlesztett összes funkciót.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt telepítené és futtatja a [Azure functions-Azure functions bővítményt]a[Visual Studio Code]-hoz, meg kell felelnie a következő követelményeknek:

* A [Visual Studio Code](https://code.visualstudio.com/) telepítve van az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Az előfizetésében a [Visual Studio Code használatával történő közzétételkor](#publish-to-azure)a szükséges egyéb erőforrások, például az Azure Storage-fiók létrehozása is létrejön. 

### <a name="run-local-requirements"></a>Helyi követelmények futtatása

Ezek az előfeltételek csak a [függvények helyi futtatásához és hibakereséséhez](#run-functions-locally)szükségesek. Nem szükségesek projektek létrehozásához vagy közzétételéhez Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2. x vagy újabb verzió. A rendszer automatikusan letölti és telepíti a központi eszközkészletet a projekt helyi indításakor. Az alapvető eszközök tartalmazzák a teljes Azure Functions futtatókörnyezetet, így a letöltés és a telepítés is eltarthat egy ideig.

+ A Visual Studio Code [C#-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp). 

+ [A .net Core parancssori felülete eszközök](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2. x vagy újabb verzió. A rendszer automatikusan letölti és telepíti a központi eszközkészletet a projekt helyi indításakor. Az alapvető eszközök tartalmazzák a teljes Azure Functions futtatókörnyezetet, így a letöltés és a telepítés is eltarthat egy ideig.

+ [Hibakereső a Java-bővítményhez](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug).

+ A [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) ajánlott. Más támogatott verziók esetében lásd: [Java-verziók](functions-reference-java.md#java-versions).

+ [Maven 3 vagy újabb](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2. x vagy újabb verzió. A rendszer automatikusan letölti és telepíti a központi eszközkészletet a projekt helyi indításakor. Az alapvető eszközök tartalmazzák a teljes Azure Functions futtatókörnyezetet, így a letöltés és a telepítés is eltarthat egy ideig.

+ [Node.js](https://nodejs.org/), aktív LTS-és karbantartási LTS-verziók (10.14.1 ajánlott). A `node --version` parancs használatával vizsgálja meg a verziót. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2. x vagy újabb verzió. A rendszer automatikusan letölti és telepíti a központi eszközkészletet a projekt helyi indításakor. Az alapvető eszközök tartalmazzák a teljes Azure Functions futtatókörnyezetet, így a letöltés és a telepítés is eltarthat egy ideig.

+ A [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows) használata javasolt. A verzióval kapcsolatos információkért lásd: [PowerShell-verziók](functions-reference-powershell.md#powershell-versions).

+ [.Net core 3,1 Runtime](https://www.microsoft.com/net/download) és [.net Core 2,1 Runtime](https://dotnet.microsoft.com/download/dotnet-core/2.1)  

+ A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)-hoz készült PowerShell-bővítmény.  

# <a name="python"></a>[Python](#tab/python)

+ A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2. x vagy újabb verzió. A rendszer automatikusan letölti és telepíti a központi eszközkészletet a projekt helyi indításakor. Az alapvető eszközök tartalmazzák a teljes Azure Functions futtatókörnyezetet, így a letöltés és a telepítés is eltarthat egy ideig.

+ [Python 3. x](https://www.python.org/downloads/). A verzióval kapcsolatos információkért lásd a Azure Functions Runtime [Python-verzióit](functions-reference-python.md#python-version) .

+ [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A functions bővítmény lehetővé teszi egy Function app-projekt létrehozását az első függvénnyel együtt. A következő lépések bemutatják, hogyan hozhat létre egy HTTP-triggert függvényt egy új functions-projektben. A [http-trigger](functions-bindings-http-webhook.md) a legegyszerűbb függvény trigger-sablonja, amely bemutatja.

1. Az **Azure: functions** lapon válassza a **create Function (függvény létrehozása** ) ikont:

    ![Függvény létrehozása](./media/functions-develop-vs-code/create-function.png)

1. Válassza ki a Function app-projekt mappáját, majd **válasszon egy nyelvet a függvény projekthez**.

1. Válassza ki a **http-trigger** függvény sablonját, vagy a **kihagyás** gombra kattintva hozzon létre egy projektet anélkül, hogy függvényt kellene létrehoznia. Később bármikor [hozzáadhat egy függvényt a projekthez](#add-a-function-to-your-project) .

    ![A HTTP-eseményindító sablon kiválasztása](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Írja be a **HttpExample** nevet a függvény neveként, majd válassza az ENTER, majd a **függvény** engedélyezése lehetőséget. Ehhez az engedélyezési szinthez meg kell adnia egy [funkcióbillentyűk](functions-bindings-http-webhook-trigger.md#authorization-keys) használatát a függvény végpontjának meghívásakor.

    ![Függvény engedélyezésének kiválasztása](./media/functions-develop-vs-code/create-function-auth.png)

    A rendszer egy függvényt hoz létre a választott nyelven, valamint a sablonban egy HTTP által aktivált függvényhez.

    ![HTTP által aktivált függvények sablonja a Visual Studio Code-ban](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Létrehozott projektfájlok

A Project sablon létrehoz egy projektet a választott nyelven, és telepíti a szükséges függőségeket. Az új projekt bármilyen nyelven rendelkezik a következő fájlokkal:

* **host.json**: lehetővé teszi a functions gazdagép konfigurálását. Ezek a beállítások akkor lépnek érvénybe, ha a functions szolgáltatást helyileg futtatja, és az Azure-ban futtatja őket. További információ: [host.json Reference](functions-host-json.md).

* **local.settings.json**: megtartja a függvények helyi futtatásakor használt beállításokat. Ezeket a beállításokat csak akkor használja a rendszer, ha helyileg futtatja a függvényeket. További információ: [Local Settings fájl](#local-settings-file).

    >[!IMPORTANT]
    >Mivel a fájl local.settings.jstartalmazhatja a titkos kulcsokat, ki kell zárnia azt a projekt forrása vezérlőelemből.

A nyelvtől függően ezek a többi fájl is létrejön:

# <a name="c"></a>[C\#](#tab/csharp)

* A függvényt megvalósító [HttpExample. cs osztály függvénytár-fájlja](functions-dotnet-class-library.md#functions-class-library-project) .

# <a name="java"></a>[Java](#tab/java)

+ Egy pom.xml fájl a gyökérkönyvtárban, amely meghatározza a projekt-és telepítési paramétereket, beleértve a projekt függőségeit és a [Java-verziót](functions-reference-java.md#java-versions). A pom.xml az üzembe helyezés során létrehozott Azure-erőforrásokról is tartalmaz információkat.   

+ A függvényt megvalósító src elérési úton található [functions. Java fájl](functions-reference-java.md#triggers-and-annotations) .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Egy package.jsa gyökérmappa fájljában.

* Egy HttpExample mappa, amely tartalmazza a [function.jsa definíciós fájlban](functions-reference-node.md#folder-structure) , valamint a [index.js fájlt](functions-reference-node.md#exporting-a-function), a függvény kódját tartalmazó Node.js fájlt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Egy HttpExample mappa, amely tartalmazza a [function.jsa definíciós fájlban](functions-reference-powershell.md#folder-structure) , valamint a run.ps1 fájlt, amely tartalmazza a függvény kódját.
 
# <a name="python"></a>[Python](#tab/python)
    
* Egy projekt szintű requirements.txt fájl, amely a függvények által igényelt csomagokat sorolja fel.
    
* Egy HttpExample mappa, amely tartalmazza a [function.jst a definíciós fájlon](functions-reference-python.md#folder-structure) , valamint az \_ \_ init \_ \_ . file fájlt, amely tartalmazza a függvény kódját.

---

Ezen a ponton [bemeneti és kimeneti kötéseket adhat hozzá](#add-input-and-output-bindings) a függvényhez. [Új függvényt is hozzáadhat a projekthez](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Kötési bővítmények telepítése

A HTTP-és időzítő-eseményindítók kivételével a kötések a kiterjesztési csomagokban vannak implementálva. Telepítenie kell a kiterjesztési csomagokat a szükséges eseményindítók és kötések számára. A kötési bővítmények telepítésének folyamata a projekt nyelvétől függ.

# <a name="c"></a>[C\#](#tab/csharp)

A terminál ablakban futtassa a [DotNet-csomag hozzáadása](/dotnet/core/tools/dotnet-add-package) parancsot a projektben szükséges kiterjesztési csomagok telepítéséhez. A következő parancs telepíti az Azure Storage bővítményt, amely a blob, a várólista és a Table Storage kötéseit valósítja meg.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="java"></a>[Java](#tab/java)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Függvény hozzáadása a projekthez

Hozzáadhat egy új függvényt egy meglévő projekthez az előre meghatározott függvények trigger-sablonjainak használatával. Új függvény-trigger hozzáadásához válassza az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a következő parancsot **Azure functions: Create Function**. Az utasításokat követve válassza ki az trigger típusát, és határozza meg az trigger szükséges attribútumait. Ha az triggerhez hozzáférési kulcs vagy kapcsolati karakterlánc szükséges a szolgáltatáshoz való kapcsolódáshoz, a függvény trigger létrehozása előtt készüljön fel.

A művelet eredménye a projekt nyelvétől függ:

# <a name="c"></a>[C\#](#tab/csharp)

A rendszer új C#-függvénytárat (. cs) ad hozzá a projekthez.

# <a name="java"></a>[Java](#tab/java)

Új Java-(. Java-) fájl van hozzáadva a projekthez.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Egy új mappa jön létre a projektben. A mappa új function.jst tartalmaz a fájl és az új JavaScript-kód fájljában.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Egy új mappa jön létre a projektben. A mappa új function.jst tartalmaz a fájl és az új PowerShell-kód fájljában.

# <a name="python"></a>[Python](#tab/python)

Egy új mappa jön létre a projektben. A mappa új function.jst tartalmaz a fájl és az új Python-kód fájljában.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Kapcsolódás szolgáltatásokhoz

A függvényt más Azure-szolgáltatásokhoz is csatlakoztathatja bemeneti és kimeneti kötések hozzáadásával. A kötések összekötik a függvényt más szolgáltatásokkal anélkül, hogy írnia kellene a kapcsolat kódját. A kötések hozzáadásának folyamata a projekt nyelvétől függ. További információ a kötésekről: [Azure functions eseményindítók és kötések fogalmai](functions-triggers-bindings.md).

Az alábbi példák egy nevű Storage-várólistához csatlakoznak `outqueue` , ahol a Storage-fiók kapcsolati karakterlánca a `MyStorageConnection` local.settings.jsalkalmazás beállításában van beállítva.

# <a name="c"></a>[C\#](#tab/csharp)

A Function metódus frissítésével adja hozzá a következő paramétert a `Run` metódus-definícióhoz:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

A `msg` paraméter egy `ICollector<T>` típus, amely a függvény befejeződése után kimeneti kötésbe írt üzenetek gyűjteményét jelöli. A következő kód egy üzenetet szúr be a gyűjteménybe:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 A függvény befejezésekor a rendszer elküldi az üzeneteket a várólistára.

További információt a [várólista-tároló kimeneti kötési útmutatója című cikkben](functions-bindings-storage-queue-output.md?tabs=csharp) találhat. Ha többet szeretne megtudni arról, hogy mely kötéseket lehet egy függvényhez adni, tekintse meg a [kötések hozzáadása egy meglévő függvényhez Azure functions](add-bindings-existing-function.md?tabs=csharp). 

# <a name="java"></a>[Java](#tab/java)

A Function metódus frissítésével adja hozzá a következő paramétert a `Run` metódus-definícióhoz:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

A `msg` paraméter egy `OutputBinding<T>` típus, ahol a a `T` függvény befejeződése után kimeneti kötésbe írt karakterlánc. A következő kód a kimeneti kötésben szereplő üzenetet állítja be:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Ezt az üzenetet a rendszer a függvény befejeződése után küldi el a várólistára.

További információt a [várólista-tároló kimeneti kötési útmutatója című cikkben](functions-bindings-storage-queue-output.md?tabs=java) találhat. Ha többet szeretne megtudni arról, hogy mely kötéseket lehet egy függvényhez adni, tekintse meg a [kötések hozzáadása egy meglévő függvényhez Azure functions](add-bindings-existing-function.md?tabs=java). 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

A függvény kódjában a `msg` kötés a `context` következővel érhető el, az alábbi példában látható módon:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Ezt az üzenetet a rendszer a függvény befejeződése után küldi el a várólistára.

További információt a [várólista-tároló kimeneti kötési útmutatója című cikkben](functions-bindings-storage-queue-output.md?tabs=javascript) találhat. Ha többet szeretne megtudni arról, hogy mely kötéseket lehet egy függvényhez adni, tekintse meg a [kötések hozzáadása egy meglévő függvényhez Azure functions](add-bindings-existing-function.md?tabs=javascript). 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Ezt az üzenetet a rendszer a függvény befejeződése után küldi el a várólistára.

További információt a [várólista-tároló kimeneti kötési útmutatója című cikkben](functions-bindings-storage-queue-output.md?tabs=powershell) találhat. Ha többet szeretne megtudni arról, hogy mely kötéseket lehet egy függvényhez adni, tekintse meg a [kötések hozzáadása egy meglévő függvényhez Azure functions](add-bindings-existing-function.md?tabs=powershell). 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

A definíció frissítése a `Main` kimeneti paraméter hozzáadásához, `msg: func.Out[func.QueueMessage]` hogy a definíció a következő példához hasonlítson:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

A következő kód karakterlánc-adatokat ad hozzá a kérésből a kimeneti várólistába:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Ezt az üzenetet a rendszer a függvény befejeződése után küldi el a várólistára.

További információt a [várólista-tároló kimeneti kötési útmutatója című cikkben](functions-bindings-storage-queue-output.md?tabs=python) találhat. Ha többet szeretne megtudni arról, hogy mely kötéseket lehet egy függvényhez adni, tekintse meg a [kötések hozzáadása egy meglévő függvényhez Azure functions](add-bindings-existing-function.md?tabs=python). 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Közzététel az Azure-ban

A Visual Studio Code lehetővé teszi, hogy közvetlenül az Azure-ban tegye közzé a functions-projektet. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Ha Visual Studio Code-ból tesz közzé egy új Function alkalmazást az Azure-ban, kiválaszthatja a gyors függvény alkalmazás létrehozási útvonalát az alapértelmezett beállításokkal vagy egy speciális elérési úttal, ahol a létrehozott távoli erőforrások nagyobb mértékben szabályozható. 

A Visual Studio Code-ból való közzétételkor kihasználhatja a [zip üzembe helyezési](functions-deployment-technologies.md#zip-deploy) technológiát. 

### <a name="quick-function-app-create"></a>Gyors funkció alkalmazás létrehozása

Ha az **+ új Function alkalmazás létrehozása az Azure-ban** lehetőséget választja, a bővítmény automatikusan generál értékeket a Function alkalmazás által igényelt Azure-erőforrásokhoz. Ezek az értékek a kiválasztott Function app-név alapján jelennek meg. A Project új Azure-beli Function alkalmazásban való közzétételére szolgáló alapértelmezett beállításokkal kapcsolatos példát a [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)rövid útmutatójában talál.

Ha explicit neveket kíván megadni a létrehozott erőforrásokhoz, ki kell választania a speciális létrehozási útvonalat.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Projekt közzététele egy új Function alkalmazásban az Azure-ban speciális beállítások használatával

A következő lépésekkel teheti közzé a projektjét a speciális létrehozási beállításokkal létrehozott új Function alkalmazásban:

1. A Command raklapon írja be a következőt **: Azure functions: Deploy to Function app**.

1. Ha nincs bejelentkezve, a rendszer felszólítja, hogy **Jelentkezzen be az Azure-** ba. **Létrehozhat egy ingyenes Azure-fiókot** is. A böngészőből való bejelentkezés után lépjen vissza a Visual Studio Code-ba.

1. Ha több előfizetéssel rendelkezik, **válasszon egy előfizetést** a Function alkalmazáshoz, majd válassza az **+ új Függvényalkalmazás létrehozása az Azure-ban... _Speciális_**. Ez a _speciális_ beállítás nagyobb mértékű irányítást biztosít az Azure-ban létrehozott erőforrások felett. 

1. Az utasításokat követve adja meg a következő információkat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Function app kiválasztása az Azure-ban | Új függvényalkalmazás létrehozása az Azure-ban | A következő üzenetben írjon be egy globálisan egyedi nevet, amely azonosítja az új Function alkalmazást, majd válassza az ENTER billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Operációs rendszer kiválasztása | Windows | A Function alkalmazás Windows rendszeren fut. |
    | Üzemeltetési csomag kiválasztása | Használatalapú csomag | A kiszolgáló nélküli [fogyasztási csomag üzemeltetése](consumption-plan.md) használatos. |
    | Válasszon futtatókörnyezetet az új alkalmazáshoz | A projekt nyelve | A futtatókörnyezetnek meg kell egyeznie a közzétett projekttel. |
    | Válasszon ki egy erőforráscsoportot az új erőforrásokhoz | Új erőforráscsoport létrehozása | A következő parancssorba írja be az erőforráscsoport nevét, például:, majd kattintson az `myResourceGroup` ENTER gombra. Választhat egy meglévő erőforráscsoportot is. |
    | Válasszon Storage-fiókot | Új tárfiók létrehozása | A következő üzenetben írjon be egy globálisan egyedi nevet a Function alkalmazás által használt új Storage-fiókhoz, majd válassza az ENTER billentyűt. A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokból és kisbetűkből állhat. Választhat egy meglévő fiókot is. |
    | Válasszon helyet az új erőforrásokhoz | régió | Válasszon egy helyet a közeli [régióban](https://azure.microsoft.com/regions/) vagy más, a funkciókhoz hozzáférő szolgáltatások közelében. |

    Megjelenik egy értesítés a Function alkalmazás létrehozása és a központi telepítési csomag alkalmazása után. Válassza ki az értesítés **kimenetének megtekintése** lehetőséget a létrehozási és a telepítési eredmények megtekintéséhez, beleértve a létrehozott Azure-erőforrásokat is.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>HTTP által aktivált függvény URL-címének beolvasása az Azure-ban

Egy HTTP által aktivált függvény ügyféltől való meghívásához szüksége lesz a függvény URL-címére, amikor a rendszer üzembe helyezi a Function alkalmazásban. Ez az URL-cím tartalmazza az összes szükséges funkcióbillentyűk. A bővítmény használatával lekérheti a telepített függvények URL-címeit. Ha csak az Azure-ban szeretné futtatni a távoli függvényt, [használja a bővítmény funkcióját](#run-functions-in-azure) .

1. Válassza az F1 billentyűt a parancs paletta megnyitásához, majd keresse meg és futtassa a parancsot **Azure functions: Copy Function URL-cím**.

1. Az utasításokat követve válassza ki a Function alkalmazást az Azure-ban, majd adja meg a meghívni kívánt HTTP-eseményindítót.

A függvény URL-címét a vágólapra másolja a rendszer, valamint a lekérdezési paraméter által átadott szükséges kulcsokat `code` . HTTP-eszköz használatával küldhet POST kéréseket, vagy egy böngészőt a kérések távoli függvénynek való beolvasásához.  

Az Azure-beli függvények URL-címének lekérése során a bővítmény az Azure-fiók használatával automatikusan lekéri a függvény elindításához szükséges kulcsokat. [További információ a függvény-hozzáférési kulcsokról](security-concepts.md#function-access-keys). A nem HTTP által aktivált függvények indításához a rendszergazdai kulcsot kell használnia.

## <a name="republish-project-files"></a>Projektfájlok ismételt közzététele

A [folyamatos üzembe helyezés](functions-continuous-deployment.md)beállításakor az Azure-beli Function alkalmazás frissül, amikor a forrás fájljait frissíti a csatlakoztatott forrás helyén. Javasoljuk a folyamatos üzembe helyezést, de a Project-fájlok frissítéseit is újra közzéteheti a Visual Studio Code-ból.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Függvények futtatása

A Azure Functions bővítmény lehetővé teszi az egyes függvények futtatását a projektben a helyi fejlesztési számítógépen vagy az Azure-előfizetésében. 

HTTP-trigger függvények esetén a bővítmény meghívja a HTTP-végpontot. Más típusú eseményindítók esetén meghívja a rendszergazda API-kat a függvény elindításához. A függvénynek küldött kérelem szövege az trigger típusától függ. Ha egy triggerhez tesztelési célú adatműveletre van szükség, a rendszer kéri, hogy adjon meg egy adott JSON-formátumú adatbevitelt.

### <a name="run-functions-in-azure"></a>Függvények futtatása az Azure-ban

Függvények végrehajtása az Azure-ban a Visual Studio Code-ból. 

1. A Command raklapon írja be **Azure functions: Execute függvényt most** , és válassza ki az Azure-előfizetését. 

1. Válassza ki a Function alkalmazást az Azure-ban a listából. Ha nem látja a Function alkalmazást, ellenőrizze, hogy be van-e jelentkezve a megfelelő előfizetésbe. 

1. Válassza ki a listából a futtatni kívánt függvényt, és írja be a kérelem szövegtörzsét az **ENTER kérelem törzsében**. Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez. Az **ENTER kérelem törzsében** szereplő alapértelmezett szövegnek a törzs formátumát kell megadnia. Ha a Function alkalmazás nem rendelkezik függvényekkel, a hibával kapcsolatos értesítési hiba jelenik meg. 

1. Amikor a függvény végrehajtja az Azure-ban, és választ ad vissza, a Visual Studio Code-ban megjelenik egy értesítés.
 
A függvényt az **Azure: functions** területén is futtathatja. ehhez kattintson a jobb gombbal (CTRL + kattintás Mac gépen) az Azure-előfizetésében futtatni kívánt függvényt, és válassza a **művelet végrehajtása most.**.. lehetőséget.

A függvények Azure-ban való futtatásakor a bővítmény az Azure-fiók használatával automatikusan lekéri a függvény elindításához szükséges kulcsokat. [További információ a függvény-hozzáférési kulcsokról](security-concepts.md#function-access-keys). A nem HTTP által aktivált függvények indításához a rendszergazdai kulcsot kell használnia.

### <a name="run-functions-locally"></a>Függvények helyi futtatása

A helyi futtatókörnyezet megegyezik az Azure-beli Function alkalmazást futtató futtatókörnyezettel. A helyi beállításokat a rendszer beolvassa a [local.settings.jsfájlból](#local-settings-file). A functions projekt helyi futtatásához [további követelményeknek](#run-local-requirements)kell megfelelnie.

#### <a name="configure-the-project-to-run-locally"></a>A projekt konfigurálása helyileg történő futtatásra

A functions Runtime az Azure Storage-fiókot belsőleg használja az összes trigger típushoz, a HTTP és webhookok kivételével. Ezért az **Values. AzureWebJobsStorage** kulcsot egy érvényes Azure Storage-fiók kapcsolódási karakterlánccá kell beállítania.

Ez a szakasz a [Visual Studio Code-hoz készült Azure Storage-bővítményt](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) használja a [Azure Storage Explorer](https://storageexplorer.com/) a kapcsolódáshoz és a tárolási kapcsolati karakterlánc lekéréséhez.

A Storage-fiók kapcsolódási karakterláncának beállítása:

1. A Visual Studióban nyissa meg a **Cloud Explorer alkalmazást** **, bontsa ki a Storage-**  >  **fiók** elemet, majd válassza a **Tulajdonságok** lehetőséget, és másolja ki az **elsődleges kapcsolatok karakterláncának** értékét.

2. A projektben nyissa meg a local.settings.jsfájlt, és állítsa be a **AzureWebJobsStorage** kulcs értékét a másolt kapcsolódási karakterláncra.

3. Az előző lépés megismétlésével egyedi kulcsokat adhat hozzá az **értékek** tömbhöz a függvények által igényelt egyéb kapcsolatokhoz.

További információ: [Local Settings fájl](#local-settings-file).

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Helyi hibakeresési függvények  

A függvények hibakereséséhez válassza az F5 lehetőséget. Ha még nem töltötte le a [Core Tools][Azure functions Core Tools], a rendszer erre kéri. Ha a Core Tools telepítése és futtatása történik, a kimenet megjelenik a terminálban. Ez ugyanaz, mint a `func host start` Core Tools parancs terminálból való futtatása, de további felépítési feladatokkal és egy csatolt hibakeresővel.  

Ha a projekt fut, a bővítmény **végrehajtás funkciójának** használatával aktiválhatja a függvényeket úgy, ahogy a projekt üzembe helyezését végzi az Azure-ban. Ha a projekt hibakeresési módban fut, a töréspontokat a Visual Studio Code-ban találja, ahogy azt várnánk. 

1. A Command raklapon írja be **Azure functions: Execute függvényt** , és válassza a **helyi projekt** lehetőséget. 

1. Válassza ki a projektben futtatni kívánt függvényt, és írja be a kérelem üzenet törzsét az **ENTER kérelem törzsében**. Nyomja le az ENTER billentyűt a kérelem üzenetének a függvénynek való elküldéséhez. Az **ENTER kérelem törzsében** szereplő alapértelmezett szövegnek a törzs formátumát kell megadnia. Ha a Function alkalmazás nem rendelkezik függvényekkel, a hibával kapcsolatos értesítési hiba jelenik meg. 

1. Ha a függvény helyileg fut, és a válasz fogadása után a rendszer értesítést küld a Visual Studio Code-ban. A függvény végrehajtásával kapcsolatos információk a **Terminal** panelen jelennek meg.

A függvények helyi futtatása nem igényli a kulcsok használatát. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem települnek át automatikusan, ha a projekt közzé van téve az Azure-ban. A közzététel befejeződése után lehetősége van a beállítások közzétételére local.settings.jsaz Azure-beli Function alkalmazásban. További információ:  [Alkalmazásbeállítások közzététele](#publish-application-settings).

A **ConnectionStrings** lévő értékek soha nem lesznek közzétéve.

A Function Application Alkalmazásbeállítások értékei környezeti változókként is olvashatók a kódban. További információkért tekintse meg a nyelvspecifikus hivatkozási cikkek környezeti változók című részeit:

* [C# előre lefordított](functions-dotnet-class-library.md#environment-variables)
* [C#-szkript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Alkalmazásbeállítások az Azure-ban

A projektben lévő local.settings.jsban szereplő beállításoknak meg kell egyezniük az Azure-beli Function alkalmazás beállításaival. A local.settings.jshoz hozzáadott beállításokat az Azure-beli Function alkalmazáshoz is hozzá kell adnia. Ezeket a beállításokat a projekt közzétételekor nem kell automatikusan feltölteni. Hasonlóképpen, [a portálon](functions-how-to-use-azure-function-app-settings.md#settings) létrehozott összes beállítást le kell tölteni a helyi projektbe.

### <a name="publish-application-settings"></a>Alkalmazásbeállítások közzététele

Az Azure-beli Function alkalmazásban a szükséges beállítások közzétételének legegyszerűbb módja a projekt közzététele után megjelenő **feltöltési beállítások** hivatkozás használata:

![Alkalmazásbeállítások feltöltése](./media/functions-develop-vs-code/upload-app-settings.png)

A beállításokat a **Azure functions: feltöltés helyi beállítás** parancs használatával is közzéteheti a parancs palettáján. Az Azure-ban a **Azure functions: Add New Setting** parancs használatával egyéni beállításokat adhat hozzá az alkalmazás beállításaihoz.

> [!TIP]
> Ügyeljen arra, hogy a közzététel előtt mentse a local.settings.jsfájlt.

Ha a helyi fájl titkosítva van, a rendszer visszafejti, közzéteszi és titkosítja újra. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját.

Megtekintheti az alkalmazás meglévő beállításait az **Azure: functions** területen az előfizetés, a Function alkalmazás és az **Alkalmazásbeállítások** kibontásával.

![A Function app-beállítások megtekintése a Visual Studio Code-ban](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Beállítások letöltése az Azure-ból

Ha az Azure-ban hozta létre az Alkalmazásbeállítások használatát, letöltheti őket a local.settings.jsfájlba a **Azure functions: távoli beállítások letöltése** paranccsal.

A feltöltéshez hasonlóan, ha a helyi fájl titkosítva van, annak visszafejtése, frissítése és titkosítása újra megtörténik. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját.

## <a name="monitoring-functions"></a>Figyelési függvények

Ha [helyileg futtatja a függvényeket](#run-functions-locally), a rendszer a naplófájlba továbbítja az adatok továbbítását. Ha a functions-projekt egy Azure-beli Function alkalmazásban fut, akkor a naplózási adatok is beszerezhetők. Csatlakozhat az Azure-beli streaming-naplókhoz, és megtekintheti a közel valós idejű naplózási adatait, vagy engedélyezheti Application Insights a Function app működésének részletesebb megismeréséhez.

### <a name="streaming-logs"></a>Streamelési naplók

Az alkalmazások fejlesztésekor gyakran hasznos a naplózási információk megjelenítése a közel valós időben. Megtekintheti a függvények által generált naplófájlok streamjét. Ez a kimenet egy HTTP által aktivált függvényre irányuló kérelem továbbítási naplóinak példája:

![A folyamatos átviteli naplók kimenete HTTP-trigger esetén](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

További információ: [streaming logs](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> A folyamatos átviteli naplók csak a functions gazdagép egyetlen példányát támogatják. Ha a függvény több példányra van méretezve, a más példányokból származó adatok nem jelennek meg a log streamben. A Application Insights [élő metrikastream](../azure-monitor/app/live-stream.md) több példányt támogat. A streaming Analytics közel valós időben is a [mintavételezésen](configure-monitoring.md#configure-sampling)alapul.

### <a name="application-insights"></a>Application Insights

Javasoljuk, hogy figyelje a függvények végrehajtását úgy, hogy integrálja a Function alkalmazást a Application Insightsával. Ha a Azure Portalban hoz létre egy Function alkalmazást, az integráció alapértelmezés szerint bekövetkezik. Ha a Visual Studio Publishing szolgáltatásban hozza létre a Function alkalmazást, integrálnia kell Application Insights magát. További információt a [Application Insights integrációjának engedélyezése](configure-monitoring.md#enable-application-insights-integration)című témakörben talál.

Ha többet szeretne megtudni a Application Insights használatával végzett figyelésről, tekintse meg a [Azure functions figyelése](functions-monitoring.md)című témakört.

## <a name="c-script-projects"></a>C \# parancsfájl-projektek

Alapértelmezés szerint a rendszer minden C#-projektet [c# lefordított függvénytár-projektként](functions-dotnet-class-library.md)hoz létre. Ha inkább C# parancsfájl-projekteket szeretne dolgozni, a C#-parancsfájlt az alapértelmezett nyelvként kell kiválasztania a Azure Functions bővítmény beállításaiban:

1. Válassza a **fájl**  >  **beállításai**  >  **Beállítások** lehetőséget.

1. Lépjen a **felhasználói beállítások**  >  **bővítmények**  >  **Azure functions**.

1. Válassza ki a **C # szkriptet** az **Azure-függvényből: projekt nyelve**.

A fenti lépések elvégzése után a mögöttes alapeszközökre irányuló hívások közé tartozik a `--csx` beállítás, amely C# parancsfájl-(. CSX) projektfájlt hoz létre és tesz közzé. Ha ez az alapértelmezett nyelv van megadva, a rendszer minden olyan projektet, amelyet a C# parancsfájl-projektjeihez alapértelmezettként hoz létre. A rendszer nem kéri a projekt nyelvének kiválasztását az alapértelmezett beállítás megadásakor. Ha más nyelveken szeretne projekteket létrehozni, ezt a beállítást módosítania kell, vagy el kell távolítania a fájl felhasználói settings.js. Ha eltávolítja ezt a beállítást, a rendszer ismét rákérdez a nyelv kiválasztására a projekt létrehozásakor.

## <a name="command-palette-reference"></a>A Command paletta referenciája

A Azure Functions bővítmény egy hasznos grafikus felületet biztosít az Azure-beli Function apps-alkalmazásokkal való interakcióhoz. Ugyanez a funkció a Command paletta (F1) parancsként is elérhető. Ezek a Azure Functions parancsok elérhetők:

|Azure Functions parancs  | Description  |
|---------|---------|
|**Új beállítások hozzáadása**  |  Létrehoz egy új Alkalmazásbeállítás-beállítást az Azure-ban. További információ: [Alkalmazásbeállítások közzététele](#publish-application-settings). Előfordulhat, hogy a [beállítást a helyi beállításokra is le kell töltenie](#download-settings-from-azure). |
| **Központi telepítési forrás konfigurálása** | A Function alkalmazást az Azure-ban csatlakoztatja egy helyi git-tárházhoz. További információ: [Azure functions folyamatos üzembe helyezése](functions-continuous-deployment.md). |
| **Kapcsolódás a GitHub-tárházhoz** | Összekapcsolja a Function alkalmazást egy GitHub-adattárral. |
| **Függvény URL-címének másolása** | Lekérdezi az Azure-ban futó HTTP-triggerű függvények távoli URL-címét. További információ: az [üzembe helyezett függvény URL-címének lekérése](#get-the-url-of-the-deployed-function). |
| **Function-alkalmazás létrehozása az Azure-ban** | Létrehoz egy új Function-alkalmazást az előfizetésben az Azure-ban. További információt az [Azure-beli új Function App-alkalmazásban való közzétételről](#publish-to-azure)szóló szakaszban talál.        |
| **Visszafejtési beállítások** | Visszafejti a Azure Functions által titkosított [helyi beállításokat](#local-settings-file) **: Titkosítsa a beállításokat**.  |
| **függvényalkalmazás törlése** | Eltávolít egy Function alkalmazást az előfizetésből az Azure-ban. Ha a App Service-csomagban nincsenek más alkalmazások, lehetősége van törölni azt is. Más erőforrások, például a Storage-fiókok és-erőforráscsoportok nem törlődnek. Az összes erőforrás eltávolításához Ehelyett [törölje az erőforráscsoportot](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). A helyi projekt nincs hatással. |
|**Függvény törlése**  | Eltávolít egy meglévő függvényt egy Azure-beli Function alkalmazásból. Mivel ez a törlés nem befolyásolja a helyi projektet, érdemes lehet helyileg eltávolítani a függvényt, majd újból [közzétenni a projektet](#republish-project-files). |
| **Proxy törlése** | Eltávolít egy Azure Functions proxyt a Function alkalmazásból az Azure-ban. További információ a proxykkal kapcsolatban: [Azure functions-proxyk használata](functions-proxies.md). |
| **Beállítás törlése** | Egy Function app-beállítás törlése az Azure-ban. Ez a törlés nem érinti a local.settings.jsfájl beállításait. |
| **Leválasztás az adattárból**  | Eltávolítja a [folyamatos üzembe helyezési](functions-continuous-deployment.md) kapcsolatot egy Azure-beli Function alkalmazás és egy verziókövetés tárháza között. |
| **Távoli beállítások letöltése** | Az Azure-ban kiválasztott Function alkalmazásban lévő beállítások letöltése a local.settings.jsfájlba. Ha a helyi fájl titkosítva van, annak visszafejtése, frissítése és titkosítása újra megtörténik. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját. A parancs futtatása előtt ne felejtse el menteni a local.settings.jsfájl módosításait. |
| **Beállítások szerkesztése** | Megváltoztatja egy meglévő Function app-beállítás értékét az Azure-ban. Ez a parancs nem érinti a local.settings.jsfájljának beállításait.  |
| **Beállítások titkosítása** | Titkosítja a tömb egyes elemeit `Values` a [helyi beállításokban](#local-settings-file). Ebben a fájlban a `IsEncrypted` is értékre van állítva `true` , amely megadja, hogy a helyi futtatókörnyezet visszafejtse a beállításokat a használat előtt. A helyi beállítások titkosításával csökkentheti az értékes információk szivárgásának kockázatát. Az Azure-ban az Alkalmazásbeállítások mindig titkosítva tárolódnak. |
| **A művelet végrehajtása most** | A függvény manuális elindítása a felügyeleti API-k használatával. Ezt a parancsot a rendszer a hibakereséshez és az Azure-ban futó függvényeknél helyileg teszteli. Amikor egy függvényt aktivál az Azure-ban, a bővítmény először automatikusan beolvas egy rendszergazdai kulcsot, amelyet a a függvényeket indító távoli felügyeleti API-k meghívására használ az Azure-ban. Az API-nak küldött üzenet törzse az trigger típusától függ. Az időzítő-eseményindítók nem igénylik az adattovábbítást. |
| **Projekt inicializálása a VS Code-ban való használatra** | Hozzáadja a szükséges Visual Studio Code Project-fájlokat egy meglévő functions-projekthez. Ezzel a paranccsal dolgozhat a Core Tools használatával létrehozott projekttel. |
| **Azure Functions Core Tools telepítése vagy frissítése** | A [Azure functions Core Tools]telepítése vagy frissítése, amely a függvények helyi futtatására szolgál. |
| **Ismételt üzembe helyezés**  | Lehetővé teszi, hogy egy csatlakoztatott git-tárházból újratelepítse a projektfájlok egy adott üzembe helyezését az Azure-ban. Ha újra közzé szeretné tenni a helyi frissítéseket a Visual Studio Code-ból, [tegye közzé újra a projektet](#republish-project-files). |
| **Beállítások átnevezése** | Megváltoztatja egy meglévő Function app-beállítás kulcsának nevét az Azure-ban. Ez a parancs nem érinti a local.settings.jsfájljának beállításait. Miután átnevezte az Azure-beli beállításokat, [töltse le ezeket a módosításokat a helyi projektbe](#download-settings-from-azure). |
| **Újraindítás** | Újraindítja a Function alkalmazást az Azure-ban. A frissítések telepítése a Function alkalmazást is újraindítja. |
| **AzureWebJobsStorage beállítása**| Az Alkalmazásbeállítás értékének beállítása `AzureWebJobsStorage` . Ezt a beállítást a Azure Functions kötelező megadni. Akkor van beállítva, amikor egy Function-alkalmazás létrejön az Azure-ban. |
| **Kezdés** | Elindít egy leállított Function alkalmazást az Azure-ban. |
| **Adatfolyam-naplók indítása** | Elindítja az Azure-beli Function alkalmazás adatfolyam-naplóit. Ha a naplózási információkat közel valós időben kell megtekinteni, használja az Azure-ban a távoli hibaelhárítás során használt adatfolyam-naplókat. További információ: [streaming logs](#streaming-logs). |
| **Leállítás** | Leállítja az Azure-ban futó Function alkalmazást. |
| **Folyamatos átviteli naplók leállítása** | Leállítja az Azure-beli Function alkalmazás adatfolyam-naplóit. |
| **Váltás tárolóhely-beállításként** | Ha engedélyezve van, gondoskodik arról, hogy egy alkalmazás beállítása megmaradjon egy adott üzembe helyezési pontnál. |
| **Azure Functions Core Tools eltávolítása** | Eltávolítja a bővítmény által igényelt Azure Functions Core Toolst. |
| **Helyi beállítások feltöltése** | Feltölti a beállításokat a local.settings.jsfájlból a kiválasztott Function alkalmazásba az Azure-ban. Ha a helyi fájl titkosítva van, a rendszer visszafejti, feltölti és titkosítja újra. Ha vannak olyan beállítások, amelyek ütköző értékekkel rendelkeznek a két helyen, a rendszer kéri, hogy válassza ki a folytatás módját. A parancs futtatása előtt ne felejtse el menteni a local.settings.jsfájl módosításait. |
| **Véglegesítés megtekintése a GitHubon** | Megjeleníti a legutóbbi véglegesítés egy adott központi telepítésben, ha a Function alkalmazás egy adattárhoz csatlakozik. |
| **Telepítési naplók megtekintése** | Megjeleníti az Azure-beli Function alkalmazáshoz megadott központi telepítés naplóit. |

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions Core Toolsről: [a Azure functions Core Tools használata](functions-run-local.md).

Ha többet szeretne megtudni a függvények .NET-es kódtáraként való fejlesztéséről, tekintse meg a [Azure functions C# fejlesztői referenciát](functions-dotnet-class-library.md). A cikk hivatkozásokat is tartalmaz arra vonatkozóan, hogyan használhatók az attribútumok a Azure Functions által támogatott különböző típusú kötések deklarálása céljából.

[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md