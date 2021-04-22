---
title: Az Azure Functions fejlesztése a Visual Studio Code használatával
description: Megtudhatja, hogyan fejleszthet és tesztelhet Azure Functions a Azure Functions Code-hoz Visual Studio bővítmény használatával.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: c2869b2b30722495523a9f0dfb2d70a17a205854
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871272"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Az Azure Functions fejlesztése a Visual Studio Code használatával

A [Azure Functions Code Visual Studio bővítménye] lehetővé teszi a függvények helyi fejlesztését és az Azure-ban való üzembe helyezését. Ha ez az első alkalom a Azure Functions, a Bevezetés a [Azure Functions.](functions-overview.md)

A Azure Functions bővítmény a következő előnyöket biztosítja:

* Függvények szerkesztése, összeállítása és futtatása a helyi fejlesztői számítógépen.
* Tegye közzé Azure Functions projektjét közvetlenül az Azure-ban.
* Függvényeket írhat különböző nyelveken, miközben kihasználja a kód Visual Studio előnyeit.

A bővítmény a következő nyelvekkel használható, amelyeket a Azure Functions 2.x verziótól kezdődően támogat:

* [C# lefordítva](functions-dotnet-class-library.md)
* [C#-szkript](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>Ehhez be kell [állítania a C#-szkriptet alapértelmezett projektnyelvként.](#c-script-projects)

Ebben a cikkben a példák jelenleg csak a JavaScript (Node.js) és a C# osztálytári függvények esetén érhetők el.  

Ez a cikk részletesen bemutatja, hogyan használhatja a Azure Functions bővítményt függvények fejlesztéshez és az Azure-ban való közzétételéhez. Mielőtt elolvassa ezt a cikket, létre kell hoznia az első függvényét a [Visual Studio használatával.](./create-first-function-vs-code-csharp.md)

> [!IMPORTANT]
> Ne keverje a helyi fejlesztést és a portálfejlesztést egyetlen függvényalkalmazáshoz. Amikor helyi projektből tesz közzé közzétételt egy függvényalkalmazásba, az üzembe helyezési folyamat felülírja a portálon fejlesztett összes függvényt.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt telepíti és futtatja a [Azure Functions-bővítményt][Azure Functions]code-Visual Studio bővítményéhez, meg kell felelnie a következő követelményeknek:

* [Visual Studio támogatott](https://code.visualstudio.com/) platformok egyikére telepített [kód.](https://code.visualstudio.com/docs/supporting/requirements#_platforms)

* Aktív Azure-előfizetés.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A további szükséges erőforrások, például egy Azure Storage-fiók, a kód használatával való közzétételkor, az [előfizetésben Visual Studio létrejönnek.](#publish-to-azure) 

### <a name="run-local-requirements"></a>Helyi követelmények futtatása

Ezek az előfeltételek csak a függvények helyi futtatásához és [hibakereséséhez szükségesek.](#run-functions-locally) Nem szükséges projekteket létrehozni vagy közzétenni a Azure Functions.

# <a name="c"></a>[C\#](#tab/csharp)

+ A [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x vagy újabb verzió. A Core Tools-csomag letöltése és telepítése automatikusan megtörténik a projekt helyi indítsa el. A Core Tools a teljes Azure Functions tartalmazza, ezért a letöltés és a telepítés némi időt is el fog venni.

+ A Visual Studio Code [C#-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp). 

+ [a .NET Core parancssori felülete eszközök:](/dotnet/core/tools/?tabs=netcore2x).  

# <a name="java"></a>[Java](#tab/java)

+ A [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x vagy újabb verzió. A Core Tools-csomag letöltése és telepítése automatikusan megtörténik a projekt helyi indítsa el. A Core Tools a teljes Azure Functions tartalmazza, ezért a letöltés és a telepítés némi időt is el fog venni.

+ [Hibakereső Java-bővítményhez.](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)

+ [Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) ajánlott. További támogatott verziókért lásd: [Java-verziók.](functions-reference-java.md#java-versions)

+ [Maven 3 vagy újabb](https://maven.apache.org/)

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

+ A [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x vagy újabb verzió. A Core Tools-csomag letöltése és telepítése automatikusan megtörténik a projekt helyi indítsa el. A Core Tools a teljes Azure Functions tartalmazza, ezért a letöltés és a telepítés némi időt is el fog venni.

+ [Node.js](https://nodejs.org/), Active LTS és Maintenance LTS verziók (10.14.1 ajánlott). A verzió `node --version` ellenőrzéshez használja a parancsot. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

+ A [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x vagy újabb verzió. A Core Tools-csomag letöltése és telepítése automatikusan megtörténik a projekt helyi indítsa el. A Core Tools a teljes Azure Functions tartalmazza, így a letöltés és a telepítés némi időt is bele fog tetsszen.

+ [PowerShell 7 ajánlott.](/powershell/scripting/install/installing-powershell-core-on-windows) A verzióra vonatkozó információkért lásd: [PowerShell-verziók.](functions-reference-powershell.md#powershell-versions)

+ A [.NET Core 3.1-es és](https://dotnet.microsoft.com/download) a [.NET Core 2.1-es futtatás is](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ A [kódhoz Visual Studio PowerShell-bővítmény.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)  

# <a name="python"></a>[Python](#tab/python)

+ A [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 2.x vagy újabb verzió. A Core Tools-csomag letöltése és telepítése automatikusan megtörténik a projekt helyi indítsa el. A Core Tools a teljes Azure Functions tartalmazza, így a letöltés és a telepítés némi időt is bele fog tetsszen.

+ [Python 3.x](https://www.python.org/downloads/). A verzióinformációkért lásd: [Python-verziók](functions-reference-python.md#python-version) a Azure Functions szerint.

+ [Python-bővítmény](https://marketplace.visualstudio.com/items?itemName=ms-python.python) a Visual Studio Code-hoz.

---

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Azure Functions-projekt létrehozása

A Függvénybővítmény lehetővé teszi egy függvényalkalmazás-projekt és az első függvény létrehozását. A következő lépések azt mutatják be, hogyan hozhat létre HTTP által aktivált függvényt egy új Functions-projektben. [A HTTP-eseményindító](functions-bindings-http-webhook.md) a legegyszerűbben bemutatható függvény eseményindító-sablonja.

1. Az **Azure: Functionsben válassza** a **Függvény létrehozása ikont:**

    ![Függvény létrehozása](./media/functions-develop-vs-code/create-function.png)

1. Válassza ki a függvényalkalmazás-projekt mappáját, majd válassza ki a függvényprojekt **nyelvét.**

1. Válassza ki a **HTTP-eseményindító** függvénysablont, vagy válassza a **Kihagyás** most lehetőséget egy függvény nélküli projekt létrehozásához. Később mindig [hozzáadhat függvényt a projekthez.](#add-a-function-to-your-project)

    ![A HTTP-eseményindító sablon kiválasztása](./media/functions-develop-vs-code/create-function-choose-template.png)

1. A függvény neveként írja be a **HttpExample** nevet, válassza az Enter, majd a Függvény **engedélyezése** lehetőséget. Ehhez az engedélyezési szinthez meg kell adnia egy [függvénykulcsot,](functions-bindings-http-webhook-trigger.md#authorization-keys) amikor a függvényvégpontot hívja meg.

    ![Függvényengedélyezési funkció kiválasztása](./media/functions-develop-vs-code/create-function-auth.png)

    A rendszer létrehoz egy függvényt a választott nyelven és egy HTTP-eseményindítóval aktivált függvény sablonjának használatával.

    ![HTTP által aktivált függvénysablon a Visual Studio Code-ban](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>Létrehozott projektfájlok

A projektsablon létrehoz egy projektet a választott nyelven, és telepíti a szükséges függőségeket. Az új projekt minden nyelvhez a következő fájlokat tartalmazza:

* **host.jsbe:** A Függvénygazda konfigurálható. Ezek a beállítások a függvények helyi futtatásakor és az Azure-ban való futtatásakor érvényesek. További információt a referencia [host.jstartalmaz.](functions-host-json.md)

* **local.settings.js:** A függvények helyi futtatásakor használt beállításokat tartja fenn. Ezeket a beállításokat csak akkor használja a rendszer, ha helyileg futtat függvényeket. További információ: Helyi [beállításfájl.](#local-settings-file)

    >[!IMPORTANT]
    >Mivel a local.settings.jsfájlban található adatok titkos adatokat is tartalmazhatnak, ki kell zárnia azt a projekt forráskezelőjéhez.

A nyelvtől függően a következő fájlok is létrejönnek:

# <a name="c"></a>[C\#](#tab/csharp)

* [A függvényt megvalósító HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) osztálytárfájl.

# <a name="java"></a>[Java](#tab/java)

+ Egy pom.xml a gyökérmappában, amely meghatározza a projekt és az üzembe helyezés paramétereit, beleértve a projektfüggőségeket és a [Java-verziót.](functions-reference-java.md#java-versions) A pom.xml az üzembe helyezés során létrehozott Azure-erőforrásokkal kapcsolatos információkat is tartalmaz.   

+ Az src elérési útján található [Functions.java](functions-reference-java.md#triggers-and-annotations) fájl, amely megvalósítja a függvényt.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* Egy package.jsa gyökérmappában található fájlban.

* Egy HttpExample mappa, amelyfunction.js[ definíciós](functions-reference-node.md#folder-structure) fájlt és a [index.js](functions-reference-node.md#exporting-a-function)fájlt ( ) tartalmazza, amely Node.js függvénykódot tartalmazza.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

* Egy HttpExample-mappa, amelyfunction.js[ definíciós](functions-reference-powershell.md#folder-structure) fájlon található és run.ps1 fájlt, amely a függvénykódot tartalmazza.
 
# <a name="python"></a>[Python](#tab/python)
    
* A Functions által requirements.txt csomagokat felsoroló projektszintű fájl.
    
* Egy HttpExample mappa, amely a [definíciósfunction.jsés](functions-reference-python.md#folder-structure) a függvénykódot tartalmazó \_ \_ init \_ \_ .py fájlt tartalmazza.

---

Ezen a ponton bemeneti és kimeneti kötéseket [adhat hozzá a](#add-input-and-output-bindings) függvényhez. A [projekthez új függvényt is hozzáadhat.](#add-a-function-to-your-project)

## <a name="install-binding-extensions"></a>Kötési bővítmények telepítése

A HTTP- és időzítő eseményindítók kivételével a kötések bővítménycsomagban vannak megvalósítva. Telepítenie kell a bővítménycsomagokat az azokat szükséges eseményindítókhoz és kötésekhez. A kötésbővítmények telepítésének folyamata a projekt nyelvétől függ.

# <a name="c"></a>[C\#](#tab/csharp)

Futtassa a [dotnet add package](/dotnet/core/tools/dotnet-add-package) parancsot a Terminal (Terminál) ablakban a projektben szükséges bővítménycsomagok telepítéséhez. A következő parancs telepíti az Azure Storage-bővítményt, amely a Blob, Queue és Table Storage kötéseket implementálja.

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

Új függvényt az egyik előre definiált Functions-eseményindító sablon használatával adhat hozzá egy meglévő projekthez. Új függvény-eseményindító hozzáadásához kattintson az F1 gombra a parancskatapaletta megnyitásához, majd keresse meg és futtassa a következő **parancsot: Azure Functions Függvény létrehozása.** Kövesse az utasításokat az eseményindító típusának meghatározásához és az eseményindító szükséges attribútumainak meghatározásához. Ha az eseményindítónak hozzáférési kulcsra vagy kapcsolati sztringre van szüksége a szolgáltatáshoz való csatlakozáshoz, készítse elő, mielőtt létrehoz egy függvény-eseményindítót.

A művelet eredménye a projekt nyelvtől függ:

# <a name="c"></a>[C\#](#tab/csharp)

A rendszer hozzáad egy új C#-osztálytárfájlt (.cs) a projekthez.

# <a name="java"></a>[Java](#tab/java)

A rendszer hozzáad egy új Java- (.java) fájlt a projekthez.

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

A projektben létrejön egy új mappa. A mappa egy új fájlt function.jsa fájlban és az új JavaScript-kódfájlt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

A projektben létrejön egy új mappa. A mappa egy új fájlt function.jsa fájlban és az új PowerShell-kódfájlt.

# <a name="python"></a>[Python](#tab/python)

A projektben létrejön egy új mappa. A mappa egy új fájlt function.jsa fájlban és az új Python-kódfájlt.

---

## <a name="connect-to-services"></a><a name="add-input-and-output-bindings"></a>Kapcsolódás szolgáltatásokhoz

A függvényt csatlakoztathatja más Azure-szolgáltatásokhoz bemeneti és kimeneti kötések hozzáadásával. A kötések anélkül csatlakoztatják a függvényt más szolgáltatásokhoz, hogy meg kellene írnia a kapcsolati kódot. A kötések hozzáadásának folyamata a projekt nyelvétől függ. A kötésekkel kapcsolatos további információkért lásd: Azure Functions eseményindítók és kötések [alapfogalmai.](functions-triggers-bindings.md)

Az alábbi példák egy nevű tárolási üzenetsorhoz csatlakoznak, ahol a tárfiók kapcsolati sztringjének beállítása az `outqueue` `MyStorageConnection` alkalmazásbeállításban local.settings.jsbe.

# <a name="c"></a>[C\#](#tab/csharp)

Frissítse a függvény metódusát úgy, hogy a következő paramétert adja hozzá `Run` a metódus definícióhoz:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="17":::

A paraméter egy típus, amely a kimeneti kötésbe írt üzenetek gyűjteményét jelöli, amikor `msg` `ICollector<T>` a függvény befejeződik. A következő kód egy üzenetet ad hozzá a gyűjteményhez:

:::code language="csharp" source="~/functions-docs-csharp/functions-add-output-binding-storage-queue-cli/HttpExample.cs" range="30-31":::

 A függvény befejeződése után a rendszer üzeneteket küld az üzenetsorba.

További tudnivalókért tekintse meg a Queue Storage kimeneti [kötésének referenciadokumentációját.](functions-bindings-storage-queue-output.md?tabs=csharp) A függvényhez hozzáadható kötésekkel kapcsolatos általános információkért lásd: [Kötések](add-bindings-existing-function.md?tabs=csharp)hozzáadása meglévő függvényhez a Azure Functions. 

# <a name="java"></a>[Java](#tab/java)

Frissítse a függvény metódusát úgy, hogy a következő paramétert adja hozzá `Run` a metódus definícióhoz:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="20-21":::

A paraméter egy típus, ahol a egy kimeneti kötésbe írt sztring, amikor `msg` `OutputBinding<T>` a függvény `T` befejeződik. A következő kód állítja be az üzenetet a kimeneti kötésben:

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/main/java/com/function/Function.java" range="33-34":::

Ez az üzenet a függvény befejeződése után lesz elküldve az üzenetsorba.

További tudnivalókért tekintse meg a Queue Storage kimeneti [kötésének referenciadokumentációját.](functions-bindings-storage-queue-output.md?tabs=java) A függvényhez hozzáadható kötésekkel kapcsolatos általános információkért lásd: [Kötések](add-bindings-existing-function.md?tabs=java)hozzáadása meglévő függvényhez a Azure Functions. 

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

A függvénykódban a kötés az -ból érhető `msg` `context` el, ahogy az alábbi példában is:

:::code language="javascript" range="5-7" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/index.js":::

Ez az üzenet a függvény befejeződése után lesz elküldve az üzenetsorba.

További tudnivalókért tekintse meg a Queue Storage kimeneti [kötésének referenciadokumentációját.](functions-bindings-storage-queue-output.md?tabs=javascript) A függvényhez hozzáadható kötésekkel kapcsolatos általános információkért lásd: [Kötések](add-bindings-existing-function.md?tabs=javascript)hozzáadása meglévő függvényhez a Azure Functions. 

# <a name="powershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

:::code language="powershell" range="18-19" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/run.ps1":::

Ez az üzenet a függvény befejeződése után lesz elküldve az üzenetsorba.

További tudnivalókért tekintse meg a Queue Storage kimeneti [kötésének referenciadokumentációját.](functions-bindings-storage-queue-output.md?tabs=powershell) A függvényhez hozzáadható kötésekkel kapcsolatos általános információkért lásd: [Kötések](add-bindings-existing-function.md?tabs=powershell)hozzáadása meglévő függvényhez a Azure Functions. 

# <a name="python"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-vs-code](../../includes/functions-add-output-binding-vs-code.md)]

Frissítse a `Main` definíciót egy kimeneti paraméter hozzáadásához, hogy `msg: func.Out[func.QueueMessage]` a definíció az alábbi példához hasonlítsa:

:::code language="python" range="6" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

A következő kód sztringadatokat ad hozzá a kérelemből a kimeneti üzenetsorhoz:

:::code language="python" range="18" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/__init__.py":::

Ez az üzenet a függvény befejeződése után lesz elküldve az üzenetsorba.

További tudnivalókért tekintse meg a Queue Storage kimeneti [kötésének referenciadokumentációját.](functions-bindings-storage-queue-output.md?tabs=python) A függvényhez hozzáadható kötésekkel kapcsolatos általános információkért lásd: [Kötések](add-bindings-existing-function.md?tabs=python)hozzáadása meglévő függvényhez a Azure Functions. 

---

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Közzététel az Azure-ban

Visual Studio Code lehetővé teszi a Functions-projekt közvetlen közzétételét az Azure-ban. A folyamat során létrehoz egy függvényalkalmazást és az azokhoz tartozó erőforrásokat az Azure-előfizetésében. A függvényalkalmazás végrehajtási környezetet biztosít a függvények számára. A projektet a rendszer becsomagolja, és az Azure-előfizetésben lévő új függvényalkalmazásban helyezi üzembe.

Amikor az Visual Studio Code-ból egy új függvényalkalmazásba tesz közzé közzétételt az Azure-ban, választhat egy gyors függvényalkalmazás létrehozási útvonalát az alapértelmezett értékekkel vagy egy speciális elérési út használatával, ahol jobban szabályozhatja a létrehozott távoli erőforrásokat. 

Amikor egy kódból Visual Studio közzé, kihasználhatja a Zip üzembe [helyezési technológiáját.](functions-deployment-technologies.md#zip-deploy) 

### <a name="quick-function-app-create"></a>Gyors függvényalkalmazás létrehozása

Ha az + Új függvényalkalmazás létrehozása az **Azure-ban...** lehetőséget választja, a bővítmény automatikusan létrehozza a függvényalkalmazáshoz szükséges Azure-erőforrások értékeit. Ezek az értékek a választott függvényalkalmazás-névn alapulnak. A projekt azure-beli új függvényalkalmazásban való alapértelmezett közzétételre való használatának példájért tekintse meg a [Visual Studio Code rövid útmutatóját.](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure)

Ha explicit neveket szeretne adni a létrehozott erőforrásoknak, a speciális létrehozási útvonalat kell választania.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Projekt közzététele egy új függvényalkalmazásban az Azure-ban speciális beállításokkal

A következő lépésekkel közzéteheti a projektet egy speciális létrehozási beállításokkal létrehozott új függvényalkalmazásban:

1. A parancs raklapon írja be a **következőt: Azure Functions: Üzembe helyezés a függvényalkalmazásban.**

1. Ha nincs bejelentkezve, a rendszer felszólítja, hogy jelentkezzen be **az Azure-ba.** Létrehozhat egy **ingyenes Azure-fiókot is.** Miután bejelentkezik a böngészőből, vissza kell Visual Studio Code-hoz.

1. Ha több előfizetéssel rendelkezik, válassza **ki a** függvényalkalmazás előfizetését, majd válassza a + Új függvényalkalmazás létrehozása **az Azure-ban... lehetőséget. _Speciális_**. Ezzel _a Speciális_ beállítással jobban szabályozhatja az Azure-ban létrehozott erőforrásokat. 

1. Az utasításokat követően adja meg az alábbi adatokat:

    | Adatkérés | Érték | Leírás |
    | ------ | ----- | ----------- |
    | Függvényalkalmazás kiválasztása az Azure-ban | Új függvényalkalmazás létrehozása az Azure-ban | A következő parancssorba írjon be egy globálisan egyedi nevet, amely azonosítja az új függvényalkalmazást, majd válassza az Enter billentyűt. A függvényalkalmazás nevéhez használható érvényes karakterek a következők: `a-z`, `0-9` és `-`. |
    | Operációs rendszer kiválasztása | Windows | A függvényalkalmazás Windows rendszeren fut. |
    | Üzemeltetési csomag kiválasztása | Használatalapú csomag | Kiszolgáló nélküli [használatcsomag-üzemeltetést](consumption-plan.md) használ. |
    | Futásidő kiválasztása az új alkalmazáshoz | A projekt nyelve | A runtime-nak egyeznie kell a közzétevő projekttel. |
    | Erőforráscsoport kiválasztása az új erőforrásokhoz | Új erőforráscsoport létrehozása | A következő parancssorba írjon be egy erőforráscsoport-nevet,például `myResourceGroup` , majd válassza az Enter billentyűt. Kiválaszthat egy meglévő erőforráscsoportot is. |
    | Tárfiók kiválasztása | Új tárfiók létrehozása | A következő parancssorba írjon be egy globálisan egyedi nevet a függvényalkalmazás által használt új tárfiókhoz, majd válassza az Enter billentyűt. A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokat és kisbetűket tartalmazhat. Kiválaszthat egy meglévő fiókot is. |
    | Új erőforrások helyének kiválasztása | régió | Válasszon ki egy helyet egy olyan [régióban,](https://azure.microsoft.com/regions/) amely a függvények által elérhető, az Ön közelében vagy más szolgáltatások közelében található. |

    A függvényalkalmazás létrehozása és az üzembe helyezési csomag alkalmazása után megjelenik egy értesítés. Az **értesítés kimenetének** megtekintése lehetőséget választva megtekintheti a létrehozási és üzembe helyezési eredményeket, beleértve a létrehozott Azure-erőforrásokat is.

### <a name="get-the-url-of-an-http-triggered-function-in-azure"></a><a name="get-the-url-of-the-deployed-function"></a>HTTP-eseményindítóval aktivált függvény URL-címének le kérése az Azure-ban

Ha egy ügyfélről http által aktivált függvényt kell hívnia, szüksége lesz a függvény URL-címére, amikor üzembe lesz telepítve a függvényalkalmazásban. Ez az URL-cím tartalmazza a szükséges függvénykulcsokat. A bővítmény használatával lekérte ezeket az URL-címeket az üzembe helyezett függvények számára. Ha csak a távoli függvényt szeretné futtatni az Azure-ban, használja a bővítmény [Execute függvény most](#run-functions-in-azure) funkcióját.

1. Válassza az F1 lehetőséget a parancskatapaletta megnyitásához, majd keresse meg és futtassa az **Azure Functions: Függvény URL-címének másolása** lehetőséget.

1. Kövesse az utasításokat, és válassza ki a függvényalkalmazást az Azure-ban, majd válassza ki a meghívni kívánt HTTP-eseményindítót.

A függvény URL-címét a rendszer a vágólapra másolja a lekérdezési paraméter által átadott szükséges `code` kulcsokkal együtt. EGY HTTP-eszközzel POST-kéréseket küldhet, vagy egy böngészőt, hogy GET-kéréseket küldjön a távoli függvénynek.  

Az Azure-beli függvények URL-címének lekérésekor a bővítmény az Ön Azure-fiókjával automatikusan lekéri a függvény elindítani kívánt kulcsokat. [További információ a függvényelérési kulcsokról.](security-concepts.md#function-access-keys) A nem HTTP által aktivált függvények indításához a rendszergazdai kulcsot kell használni.

## <a name="republish-project-files"></a>Projektfájlok ismételt közzététele

A folyamatos üzembe helyezés [beállításakor](functions-continuous-deployment.md)a függvényalkalmazás frissül az Azure-ban, amikor frissíti a forrásfájlokat a csatlakoztatott forráshelyen. Javasoljuk a folyamatos üzembe helyezést, de a projektfájl frissítéseit újra közzé is tehet a Visual Studio Code-ból.

> [!IMPORTANT]
> Meglévő függvényalkalmazásba való közzététel felülírja az adott alkalmazás tartalmát az Azure-ban.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="run-functions"></a>Függvények futtatása

A Azure Functions bővítmény lehetővé teszi egyéni függvények futtatását a projektben a helyi fejlesztői számítógépen vagy az Azure-előfizetésében. 

HTTP-eseményindító függvények esetén a bővítmény a HTTP-végpontot hívja meg. Más típusú eseményindítókhoz rendszergazdai API-kat hív meg a függvény elindítani. A függvénynek küldött kérés üzenet törzse az eseményindító típusától függ. Ha egy eseményindító tesztadatokat igényel, a rendszer arra kéri, hogy adjon meg adatokat egy adott JSON-formátumban.

### <a name="run-functions-in-azure"></a>Függvények futtatása az Azure-ban

Függvény végrehajtása az Azure-ban a Visual Studio kódból. 

1. A parancs raklapon írja be a **következőt: Azure Functions: Függvény** végrehajtása most, és válassza ki az Azure-előfizetését. 

1. Válassza ki a függvényalkalmazást az Azure-ban a listából. Ha nem látja a függvényalkalmazást, ellenőrizze, hogy a megfelelő előfizetésbe jelentkezett-e be. 

1. Válassza ki a listából a futtatni kívánt függvényt, és írja be a kérés üzenet törzsét az **Enter request body (Kérés törzsének begépelését) mezőbe.** Nyomja le az Enter billentyűt a kérésüzenet függvénynek való elküldését. Az Enter kérés törzsének **alapértelmezett szövegében** meg kell adnia a törzs formátumát. Ha a függvényalkalmazás nem rendelkezik függvényekkel, egy értesítési hiba jelenik meg ezzel a hibával. 

1. Amikor a függvény fut az Azure-ban, és választ ad vissza, egy értesítés jön Visual Studio Kódban.
 
A függvényt az **Azure: Functions** területről is futtathatja, ha a jobb gombbal kattint (a Ctrl billentyűt lenyomva tartva a Mac gépen) a függvényalkalmazásból futtatni kívánt függvényre az Azure-előfizetésben, és válassza a Függvény végrehajtása **most...** lehetőséget.

Amikor függvényeket futtat az Azure-ban, a bővítmény az Azure-fiókjával automatikusan lekéri a függvény futtatásához szükséges kulcsokat. [További információ a függvényelérési kulcsokról.](security-concepts.md#function-access-keys) A nem HTTP által aktivált függvények indításához a rendszergazdai kulcsot kell használni.

### <a name="run-functions-locally"></a>Függvények helyi futtatása

A helyi futtatás ugyanaz a futtatás, amely a függvényalkalmazást az Azure-ban futtatja. A helyi beállításokat a fájl [local.settings.jsolvassa be.](#local-settings-file) A Functions-projekt helyi futtatásához további követelményeknek [kell megfelelnie.](#run-local-requirements)

#### <a name="configure-the-project-to-run-locally"></a>A projekt konfigurálása helyi futtatáshoz

A Functions-futtatás belsőleg egy Azure Storage-fiókot használ a HTTP- és webhookokon kívül minden eseményindító-típushoz. Ezért a **Values.AzureWebJobsStorage** kulcsot egy érvényes Azure Storage-fiók kapcsolati sztringre kell beállítania.

Ez a szakasz az Azure Storage-bővítményt [](https://storageexplorer.com/) használja a [Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) Azure Storage Explorer a storage kapcsolati sztringhez való csatlakozáshoz és a sztring lekéréshez.

A tárfiók kapcsolati sztringének beállítása:

1. A Visual Studio nyissa meg a **Cloud Explorert,** bontsa ki a **Tárfiók** Saját tárfiók területet, majd válassza a Tulajdonságok lehetőséget, és másolja ki az Elsődleges kapcsolati  >   **sztring értékét.** 

2. A projektben nyissa meg a local.settings.jsfájlban, és állítsa az **AzureWebJobsStorage** kulcs értékét a kimásott kapcsolati sztringre.

3. Ismételje meg az előző lépést, ha egyedi kulcsokat szeretne hozzáadni az **Értékek** tömbhöz a függvények által megkövetelt egyéb kapcsolatokhoz.

További információ: Helyi [beállításfájl.](#local-settings-file)

#### <a name="debug-functions-locally"></a><a name="debugging-functions-locally"></a>Függvények helyi hibakeresése  

A függvények hibakeresését az F5 billentyűvel lehet végezni. Ha még nem letöltötte a [Core Tools][Azure Functions Core Tools,]a rendszer erre kéri. A Core Tools telepítése és futtatása után a kimenet megjelenik a terminálon. Ez ugyanaz, mint a Core Tools parancs futtatása a terminálról, de további buildfeladatokkal és egy csatolt `func host start` hibakeresővel.  

Ha a projekt fut, a bővítmény **Execute Function Now...** funkcióját használva úgy aktiválhatja a függvényeket, mintha a projektet az Azure-ban helyezné üzembe. Ha a projekt hibakeresési módban fut, a töréspontok a várt Visual Studio a kódban. 

1. A parancssorba írja be a **következőt: Azure Functions: Execute function now (Függvény** végrehajtása most) és válassza **a Local project (Helyi projekt) lehetőséget.** 

1. Válassza ki a projektben futtatni kívánt függvényt, és írja be a kérés üzenet törzsét az **Enter request body (Kérés törzsének begépelését) mezőbe.** Nyomja le az Enter billentyűt a kérésüzenet függvénynek való elküldését. Az Enter kérés törzsében az alapértelmezett **szövegnek** a törzs formátumát kell jeleznie. Ha a függvényalkalmazás nem rendelkezik függvényekkel, egy értesítési hiba jelenik meg ezzel a hibával. 

1. Amikor a függvény helyileg fut, és a válasz érkezik, egy értesítés jelenik meg a Visual Studio Kódban. A függvény végrehajtásával kapcsolatos információk a Terminál **panelen jelennek** meg.

A függvények helyi futtatásához nincs szükség kulcsokra. 

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Alapértelmezés szerint ezek a beállítások nem migrálódnak automatikusan, amikor a projektet közzéteszik az Azure-ban. A közzététel befejezése után lehetősége van a beállítások közzétételére a local.settings.jsa függvényalkalmazásban az Azure-ban. További információ: [Alkalmazásbeállítások közzététele.](#publish-application-settings)

A **ConnectionStrings értékei soha** nem vannak közzétéve.

A függvényalkalmazás beállításainak értékei környezeti változókként is olvashatók a kódban. További információért tekintse meg az alábbi nyelvspecifikus referenciacikkek Környezeti változók szakaszát:

* [C# előre le van kompatiálva](functions-dotnet-class-library.md#environment-variables)
* [C#-szkript (.csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)
* [PowerShell](functions-reference-powershell.md#environment-variables)
* [Python](functions-reference-python.md#environment-variables)

## <a name="application-settings-in-azure"></a>Alkalmazásbeállítások az Azure-ban

A projekt fájljában local.settings.jsfájl beállításainak meg kell egyednek lennie az Azure-beli függvényalkalmazás alkalmazásbeállításával. A függvényalkalmazáshoz local.settings.jsbeállításokat az Azure-beli függvényalkalmazáshoz is hozzá kell adni. Ezeket a beállításokat a rendszer nem tölti fel automatikusan a projekt közzétételekor. Hasonlóképpen, a függvényalkalmazásban a [](functions-how-to-use-azure-function-app-settings.md#settings) portálon létrehozott beállításokat is le kell tölteni a helyi projektbe.

### <a name="publish-application-settings"></a>Alkalmazásbeállítások közzététele

A szükséges beállítások azure-beli függvényalkalmazásban való közzétételének legegyszerűbb módja a projekt közzététele után megjelenő Beállítások feltöltése hivatkozás használata: 

![Alkalmazásbeállítások feltöltése](./media/functions-develop-vs-code/upload-app-settings.png)

A beállításokat a parancskatapaletta **helyi Azure Functions helyi** beállítás feltöltése parancsával is közzéteheti. Az Azure-beli alkalmazásbeállításokhoz egyéni beállításokat adhat hozzá a **Azure Functions: Új beállítás hozzáadása paranccsal.**

> [!TIP]
> A közzététel előtt local.settings.jsmenteni a fájlt.

Ha a helyi fájl titkosítva van, a rendszer visszafejti, közzéteszi és újra titkosítja. Ha a két helyen ütköző értékek találhatók, a rendszer felkéri, hogy válassza ki a folytatási lehetőséget.

Tekintse meg a meglévő alkalmazásbeállításokat az **Azure: Functions** területen az előfizetés, a függvényalkalmazás és az **alkalmazásbeállítások kibontásához.**

![Függvényalkalmazás beállításainak megtekintése a Visual Studio Code-ban](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Beállítások letöltése az Azure-ból

Ha már létrehozott alkalmazásbeállításokat az Azure-ban, letöltheti őket a local.settings.jsfájlba a **következő Azure Functions: Távoli** beállítások letöltése paranccsal.

A feltöltéshez mint a feltöltéshez, ha a helyi fájl titkosítva van, a rendszer visszafejti, frissíti és újra titkosítja. Ha a két helyen ütköző értékek találhatók, a rendszer felkéri, hogy válassza ki a folytatási lehetőséget.

## <a name="monitoring-functions"></a>Monitorozási függvények

Amikor [helyileg futtat függvényeket,](#run-functions-locally)a rendszer a naplóadatokat a terminálkonzolra streameli. Naplóadatokat akkor is lekért, ha a Functions-projekt egy Függvényalkalmazásban fut az Azure-ban. Csatlakozhat az Azure-beli streamnaplókhoz a közel valós idejű naplóadatok megtekintéséhez, vagy engedélyezheti a Application Insights-t a függvényalkalmazás működésének teljesebb megértéséhez.

### <a name="streaming-logs"></a>Streamelési naplók

Alkalmazás fejlesztésekor gyakran hasznos közel valós időben látni a naplózási adatokat. Megtekintheti a függvények által létrehozott naplófájlok streamét. Ez a kimenet egy példa egy HTTP-eseményindítóval aktivált függvényre vonatkozó kérés streamnaplóira:

![Streamnaplók kimenete HTTP-eseményindítóhoz](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

További információ: [Streamnaplók.](functions-monitoring.md#streaming-logs)

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> A streamelési naplók csak a Functions-gazdagép egyetlen példányát támogatják. Ha a függvény több példányra van skálázva, más példányok adatai nem jelennek meg a naplóstreamben. [Élő metrikastream](../azure-monitor/app/live-stream.md) a Application Insights több példányt is támogat. Bár közel valós időben is, a streamelési elemzés a [mintavételből vett adatokon alapul.](configure-monitoring.md#configure-sampling)

### <a name="application-insights"></a>Application Insights

Javasoljuk, hogy monitorja a függvények végrehajtását a függvényalkalmazás és a Application Insights. Amikor függvényalkalmazást hoz létre a Azure Portal, ez az integráció alapértelmezés szerint megtörténik. Amikor a közzététel során létrehozza a függvényalkalmazást Visual Studio, saját magának kell integrálni Application Insights alkalmazásokat. További információ: [Enable Application Insights integration](configure-monitoring.md#enable-application-insights-integration)( Integráció engedélyezése).

További információ a monitorozásról a Application Insights [monitorozását Azure Functions.](functions-monitoring.md)

## <a name="c-script-projects"></a>\#C-szkriptprojektek

Alapértelmezés szerint minden C#-projekt [C#-fordítású osztálytárprojektként jön létre.](functions-dotnet-class-library.md) Ha inkább C#-szkriptprojektekkel szeretne dolgozni, a C#-szkriptet kell kiválasztania alapértelmezett nyelvként a Azure Functions beállításaiban:

1. Válassza **a**  >  **Fájlbeállítások**  >  **lehetőséget.**

1. A Felhasználói **beállítások**  >  **bővítményei lapra Azure Functions.**  >  

1. Válassza a C#Script from Azure Function: Project Language **(C#-szkript** az **Azure-függvényből: Projekt nyelve) lehetőséget.**

A lépések befejezése után a mögöttes Core Tools-hívások között szerepel a beállítás, amely `--csx` C#-szkriptfájlokat (.csx) hoz létre és tesz közzé. Ha meg van adva ez az alapértelmezett nyelv, az összes létrehozott projekt alapértelmezetten C#-szkriptprojekt lesz. Az alapértelmezett beállítás esetén a rendszer nem kéri a projekt nyelvének választását. Ha más nyelvű projekteket szeretne létrehozni, módosítania kell ezt a beállítást, vagy el kell távolítania azt a settings.jsfájlban található felhasználói fiókból. A beállítás eltávolítása után a rendszer a projekt létrehozásakor ismét kérni fogja a nyelv választását.

## <a name="command-palette-reference"></a>Parancskatapaletta referenciája

A Azure Functions bővítmény hasznos grafikus felületet biztosít az Azure-beli függvényalkalmazásokkal való interakcióhoz. Ugyanez a funkció parancsként is elérhető a parancskatajánlában (F1). A Azure Functions alábbi parancsokat érhetők el:

|Azure Functions parancs  | Description  |
|---------|---------|
|**Új beállítások hozzáadása**  |  Új alkalmazásbeállítást hoz létre az Azure-ban. További információ: Alkalmazásbeállítások [közzététele.](#publish-application-settings) Előfordulhat, hogy le kell [töltenie ezt a beállítást a helyi beállításokra.](#download-settings-from-azure) |
| **Központi telepítési forrás konfigurálása** | Csatlakoztatja az Azure-beli függvényalkalmazást egy helyi Git-adattárhoz. További információ: Folyamatos üzembe [helyezés a Azure Functions.](functions-continuous-deployment.md) |
| **Csatlakozás GitHub-adattárhoz** | Csatlakoztatja a függvényalkalmazást egy GitHub-adattárhoz. |
| **Függvény URL-címének másolása** | Lekérte az Azure-ban futó HTTP-eseményindítóval aktivált függvény távoli URL-címét. További tudnivalókért lásd: Az üzembe helyezett függvény [URL-címének lekért címe.](#get-the-url-of-the-deployed-function) |
| **Függvényalkalmazás létrehozása az Azure-ban** | Létrehoz egy új függvényalkalmazást az Azure-előfizetésében. További tudnivalókért tekintse meg az új függvényalkalmazásban való közzétételről [az Azure-ban című szakaszt.](#publish-to-azure)        |
| **Beállítások visszafejtése** | Visszafejti [a következő](#local-settings-file) által titkosított helyi **beállításokat: Azure Functions beállítások titkosítása.**  |
| **Függvényalkalmazás törlése** | Eltávolít egy függvényalkalmazást az Azure-előfizetésből. Ha a csomag nem App Service más alkalmazást, akkor azt is törölheti. Más erőforrások, például a tárfiókok és az erőforráscsoportok nem törlődnek. Az összes erőforrás eltávolításához törölje az [erőforráscsoportot.](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources) A helyi projektet ez nem érinti. |
|**Függvény törlése**  | Eltávolít egy meglévő függvényt egy Azure-beli függvényalkalmazásból. Mivel ez a törlés nincs hatással a helyi projektre, fontolja meg a függvény helyi eltávolítását, majd a [projekt újra közzétételét.](#republish-project-files) |
| **Proxy törlése** | Eltávolít egy Azure Functions proxyt az Azure-beli függvényalkalmazásból. A proxykról a Work with Azure Functions-proxyk (A proxyk [Azure Functions-proxyk) Azure Functions-proxyk.](functions-proxies.md) |
| **Beállítás törlése** | Töröl egy függvényalkalmazás-beállítást az Azure-ban. Ez a törlés nincs hatással a fájl local.settings.jsbeállításaira. |
| **Leválasztás az újratétről**  | Eltávolítja [a folyamatos üzembe helyezési](functions-continuous-deployment.md) kapcsolatot egy Azure-beli függvényalkalmazás és egy forrásvezérlő adattár között. |
| **Távoli beállítások letöltése** | Letölti a beállításokat a kiválasztott azure-beli függvényalkalmazásból local.settings.jsfájlba. Ha a helyi fájl titkosítva van, a rendszer visszafejti, frissíti és újra titkosítja. Ha a két helyen ütköző értékek találhatók, a rendszer felkéri, hogy válassza ki a folytatást. A parancs futtatása előtt ne local.settings.jsa fájlba a módosításokat. |
| **Beállítások szerkesztése** | Módosítja egy meglévő függvényalkalmazás beállításának értékét az Azure-ban. Ez a parancs nincs hatással a fájl local.settings.jsbeállításaira.  |
| **Beállítások titkosítása** | A tömb egyes elemeit `Values` titkosítja a [helyi beállításokban.](#local-settings-file) Ebben a fájlban szintén a van beállítva, ami azt határozza meg, hogy a helyi futásidő a használat előtt visszafejti `IsEncrypted` `true` a beállításokat. A helyi beállítások titkosítása az értékes információk kiszivárgásának kockázatának csökkentése érdekében. Az Azure-ban az alkalmazásbeállítások mindig titkosítva vannak tárolva. |
| **Függvény végrehajtása** | Manuálisan elindít egy függvényt rendszergazdai API-k használatával. Ez a parancs helyi teszteléshez használható a hibakeresés során és az Azure-ban futó függvények esetén is. Amikor elindít egy függvényt az Azure-ban, a bővítmény először automatikusan beszerez egy rendszergazdai kulcsot, amellyel az Azure-ban függvényeket elindító távoli rendszergazdai API-kat hív meg. Az API-nak küldött üzenet törzse az eseményindító típusától függ. Az időzítő eseményindítókhoz nem szükséges adatokat átadni. |
| **Projekt inicializálása a VS Code-kóddal való használatra** | Hozzáadja a Visual Studio Code-projektfájlokat egy meglévő Functions-projekthez. Ezzel a paranccsal dolgozhat egy, a Core Tools használatával létrehozott projekttel. |
| **Telepítési vagy frissítési Azure Functions Core Tools** | Telepíti vagy frissíti [a Azure Functions Core Tools,]amely a függvények helyi futtatására használható. |
| **Ismételt üzembe helyezés**  | Lehetővé teszi projektfájlok ismételt üzembe helyezését egy csatlakoztatott Git-adattárból egy adott Üzemelő példányra az Azure-ban. A helyi frissítéseknek az Visual Studio Code-ból való újbóli közzétételéhez tegye közzé újra a [projektet.](#republish-project-files) |
| **Beállítások átnevezése** | Módosítja egy meglévő függvényalkalmazás-beállítás kulcsnevét az Azure-ban. Ez a parancs nincs hatással a fájl local.settings.jsbeállításaira. Miután átnevezte a beállításokat az Azure-ban, töltse le a módosításokat a [helyi projektbe.](#download-settings-from-azure) |
| **Újraindítás** | Újraindítja a függvényalkalmazást az Azure-ban. A frissítések üzembe helyezése szintén újraindítja a függvényalkalmazást. |
| **Az AzureWebJobsStorage beállítása**| Beállítja az `AzureWebJobsStorage` alkalmazásbeállítás értékét. Erre a beállításra a Azure Functions. Ez akkor van beállítva, amikor függvényalkalmazás jön létre az Azure-ban. |
| **Kezdés** | Elindít egy leállított függvényalkalmazást az Azure-ban. |
| **Streamelési naplók kezdete** | Elindítja a függvényalkalmazás streamnaplóit az Azure-ban. Streamnaplók használata távoli hibaelhárítás során az Azure-ban, ha közel valós időben kell látnia a naplózási információkat. További információ: [Streamnaplók.](#streaming-logs) |
| **Leállítás** | Leállít egy Azure-ban futó függvényalkalmazást. |
| **Streamelési naplók leállítása** | Leállítja a függvényalkalmazás streamnaplóit az Azure-ban. |
| **Váltás tárolóhely-beállításként** | Ha engedélyezve van, biztosíthatja, hogy az alkalmazásbeállítás megmarad egy adott üzembe helyezési tárolóhelyen. |
| **A Azure Functions Core Tools** | Eltávolítja Azure Functions Core Tools, amelyre a bővítménynek szüksége van. |
| **Helyi beállítások feltöltése** | Feltölti a beállításokat a local.settings.jsfájlból a kiválasztott függvényalkalmazásba az Azure-ban. Ha a helyi fájl titkosítva van, a rendszer visszafejti, feltölti és újra titkosítja. Ha a két helyen ütköző értékek találhatók, a rendszer felkéri, hogy válassza ki a folytatási lehetőséget. A parancs futtatása előtt ne local.settings.jsa fájlba a módosításokat. |
| **Véglegesítés megtekintése a GitHubon** | Megjeleníti a legutóbbi véglegesítést egy adott üzemelő példányban, amikor a függvényalkalmazás csatlakozik egy adattárhoz. |
| **Telepítési naplók megtekintése** | Megjeleníti az Azure-beli függvényalkalmazásban való adott üzembe helyezés naplóit. |

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions Core Tools: [Work with Azure Functions Core Tools](functions-run-local.md).

További információ a függvények .NET-osztálytárakként való fejlesztésről: Azure Functions [C# fejlesztői referencia.](functions-dotnet-class-library.md) Ez a cikk olyan példákra mutató hivatkozásokat is tartalmaz, amelyek bemutatják, hogyan deklaráljuk az attribútumok használatával a Azure Functions.

[Azure Functions-bővítmény a Visual Studio Code-hoz]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md