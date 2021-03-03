---
title: 'Rövid útmutató: az első függvény létrehozása az Azure-ban a Visual Studióval'
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és tehet közzé egy HTTP-trigger Azure-függvényt a Visual Studio használatával.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: d691ee60f624f75c89e44e905e5343bf9c71fd4d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701415"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Rövid útmutató: az első függvény létrehozása az Azure-ban a Visual Studióval

Ebben a cikkben a Visual Studióval hozzon létre egy C# szintű függvénytár-alapú függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.  

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez először telepítse a [Visual Studio 2019](https://azure.microsoft.com/downloads/)-es frissítést. Győződjön meg arról, hogy a telepítés során kijelöli az **Azure-fejlesztési** számítási feladatot. Ha a Visual Studio 2017 használatával szeretne létrehozni egy Azure Functions projektet, először telepítenie kell a [legújabb Azure functions eszközöket](functions-develop-vs.md#check-your-tools-version).

![A Visual Studio telepítése az Azure-fejlesztési számítási feladattal](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

Ha nem rendelkezik Azure- [előfizetéssel](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/dotnet/) .

## <a name="create-a-function-app-project"></a>Függvényalkalmazás-projekt létrehozása

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

A Visual Studio létrehoz egy projektet és egy osztályt, amely tartalmazza a HTTP trigger függvény típusának szabványos kódját. A sablonkód elküld egy HTTP-választ, amely tartalmaz egy értéket a kérelem szövegtörzséből vagy a lekérdezési sztringből. Az `HttpTrigger` attribútum azt adja meg, hogy a függvényt egy HTTP-kérelem indítja el. 

## <a name="rename-the-function"></a>A függvény átnevezése

A `FunctionName` Method attribútum a függvény nevét állítja be, amely alapértelmezés szerint a következőképpen jön létre: `Function1` . Mivel az eszközök nem teszik lehetővé, hogy a projekt létrehozásakor felülbírálja az alapértelmezett függvény nevét, szánjon egy percet a Function osztály, a fájl és a metaadatok jobb nevének létrehozásához.

1. A **fájlkezelőben** kattintson a jobb gombbal a Function1.cs fájlra, és nevezze át a következőre: `HttpExample.cs` .

1. A kódban nevezze át a Function1 osztályt a következőre: "HttpExample".

1. A `HttpTrigger` nevű metódusban `Run` nevezze át a `FunctionName` Method attribútumot a következőre: `HttpExample` .

Most, hogy átnevezte a függvényt, tesztelheti a helyi számítógépen.

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A Visual Studio integrálva van Azure Functions Core Tools, így helyileg tesztelheti a függvényeket a teljes Azure Functions Runtime használatával.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Miután ellenőrizte, hogy a függvény megfelelően fut a helyi számítógépen, tegye közzé a projektet az Azure-ban.

## <a name="publish-the-project-to-azure"></a>A projekt közzététele az Azure-ban

A projekt közzététele előtt rendelkeznie kell egy Function alkalmazással az Azure-előfizetésében. A Visual Studio Publishing létrehoz egy Function alkalmazást a projekt első közzétételekor.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>A függvény tesztelése az Azure-ban

1. A Cloud Explorerben ki kell választania az új Function alkalmazást. Ha nem, bontsa ki az előfizetését > **app Services**, és válassza ki az új Function alkalmazást.

1. Kattintson a jobb gombbal a Function alkalmazásra, és válassza a **Megnyitás böngészőben** lehetőséget. Ekkor megnyílik a Function alkalmazás gyökere az alapértelmezett böngészőben, és megjeleníti azt a lapot, amely azt jelzi, hogy a Function alkalmazás fut. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="Futó Function alkalmazás":::

1. A böngésző címsorában fűzze hozzá a karakterláncot `/api/HttpExample?name=Functions` az alap URL-címhez, és futtassa a kérelmet.

    A HTTP-trigger függvényt meghívó URL-cím formátuma a következő:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Nyissa meg ezt az URL-címet, és megjelenik a függvény által visszaadott távoli GET kérelemre adott válasz, amely a következő példához hasonlóan néz ki:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="A függvény által visszaadott válasz a böngészőben":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a gyűjteményben lévő többi rövid útmutató erre a rövid útmutatóra épül. Ha azt tervezi, hogy az ezt követő rövid útmutatók, oktatóanyagok vagy az ebben a rövid útmutatóban létrehozott bármelyik szolgáltatással dolgozik, ne törölje az erőforrásokat.

Az *erőforrások* kifejezés az Azure-ban például függvényalkalmazásokra, függvényekre vagy tárfiókokra utal. *Erőforráscsoportokba* vannak csoportosítva, és a csoport törlésével törölheti a csoport összes elemét. 

E rövid útmutatók elvégzéséhez erőforrásokat hozott létre. [Fiókjának állapotától](https://azure.microsoft.com/account/) és a [szolgáltatási díjszabástól](https://azure.microsoft.com/pricing/) függően lehetséges, hogy az erőforrások használata díjköteles. Ha már nincs szüksége ezekre az erőforrásokra, a következőképpen törölheti őket:

1. A Cloud Explorerben bontsa ki az előfizetését > **app Services**, kattintson a jobb gombbal a Function alkalmazásra, majd válassza a **Megnyitás a portálon** lehetőséget. 

1. A Function alkalmazás lapon válassza az **Áttekintés** lapot, majd válassza ki az **erőforráscsoport** területen található hivatkozást.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Válassza ki a függvény alkalmazás oldaláról törölni kívánt erőforráscsoportot.":::

2. Az **erőforráscsoport** lapon tekintse át a befoglalt erőforrások listáját, és ellenőrizze, hogy a törölni kívántak-e.
 
3. Válassza az **Erőforráscsoport törlése** elemet, majd kövesse az utasításokat.

   A törlés eltarthat néhány percig. Amint a művelet befejeződött, néhány másodpercre egy értesítés jelenik meg. Az értesítést úgy is megtekintheti, ha kiválasztja a harang ikont az oldal tetején.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a Visual studiót használta egy C#-függvény alkalmazás létrehozásához és közzétételéhez az Azure-ban egy egyszerű HTTP trigger függvénnyel. 

A következő cikkből megtudhatja, hogyan adhat hozzá Azure Storage-üzenetsor-kötést a függvényhez:
> [!div class="nextstepaction"]
> [Azure Storage-várólista kötésének hozzáadása a függvényhez](functions-add-output-binding-storage-queue-vs.md)

