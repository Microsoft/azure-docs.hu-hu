---
title: Az első kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban
description: Kiszolgáló nélküli alkalmazások létrehozása, üzembe helyezése és kezelése a Visual Studióban Azure Logic Apps és Azure Functions használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 1758cca902eb77ffc66824cb56b8add9446fabf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96749147"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Az első kiszolgáló nélküli alkalmazás létrehozása a Visual Studióban Azure Logic Apps és Azure Functions használatával

A Felhőbeli alkalmazások gyors fejlesztése és üzembe helyezése az Azure kiszolgáló nélküli eszközeivel és képességeivel, például a [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a [Azure functions](../azure-functions/functions-overview.md)használatával végezhető el. Ez a cikk bemutatja, hogyan hozhat létre egy kiszolgáló nélküli alkalmazást, amely egy Azure-függvényt meghívó logikai alkalmazást használ a Visual Studióban. Ha többet szeretne megtudni az Azure-beli kiszolgáló nélküli megoldásokról, tekintse meg [a functions és a Logic apps](../logic-apps/logic-apps-serverless-overview.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Ha kiszolgáló nélküli alkalmazást szeretne létrehozni a Visual Studióban, a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A következő eszközök. Ha még nem rendelkezik velük, töltse le és telepítse őket.

  * [Visual Studio 2019, 2017 vagy 2015 (közösségi vagy egyéb kiadás)](https://aka.ms/download-visual-studio). 
  Ez a rövid útmutató a Visual Studio Community 2017 kiadást használja, amely ingyenes.

    > [!IMPORTANT]
    > A Visual Studio 2019-es vagy 2017-es vagy-es telepítésekor ügyeljen arra, hogy kiválassza az **Azure-fejlesztési** számítási feladatot.

  * [Microsoft Azure SDK for .net (2.9.1 vagy újabb verzió)](https://azure.microsoft.com/downloads/). 
  További információ az [Azure SDK for .NET](/dotnet/azure/intro) készlettel kapcsolatban.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Azure Logic Apps a Visual Studio-verzióhoz használni kívánt eszközöket:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    Letöltheti és telepítheti az Azure Logic Apps alkalmazást közvetlenül a Visual Studio piacteréről, vagy nézzen utána, [hogyan telepítheti a bővítményt a Visual Studio rendszerén belül](/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Ne feledje, hogy a telepítés után újra kell indítani a Visual Studiót.

  * [Azure functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) a helyi hibakeresési funkciókhoz.

* Hozzáférés a web-hez a beágyazott Logic app Designer használatával.

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. 
  Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

## <a name="create-a-resource-group-project"></a>Erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projektet](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) a kiszolgáló nélküli alkalmazáshoz. Az Azure-ban erőforrás- *csoporton* belül hozhat létre erőforrásokat, amely egy olyan logikai gyűjtemény, amelyet egy teljes alkalmazás erőforrásainak rendszerezéséhez, kezeléséhez és üzembe helyezéséhez használhat egyetlen eszközként. Az Azure-beli kiszolgáló nélküli alkalmazások esetében az erőforráscsoport Azure Logic Apps és Azure Functions egyaránt tartalmaz erőforrásokat. További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/management/overview.md).

1. Indítsa el a Visual studiót, és jelentkezzen be az Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet.

   ![Új projekt létrehozása a Visual Studióban](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Ezután válassza a **Cloud**  >  **Azure-erőforráscsoport** lehetőséget.

   > [!NOTE]
   > Ha a **felhő** vagy az **Azure erőforráscsoport** -projekt nem létezik, győződjön meg arról, hogy telepítette a Visual STUDIÓhoz készült Azure SDK-t.

   Ha a Visual Studio 2019-et használja, kövesse az alábbi lépéseket:

   1. Az **új projekt létrehozása** mezőben válassza ki a Visual C# vagy a Visual Basic **Azure erőforráscsoport** -sablonját, majd válassza a **tovább** lehetőséget.

   1. Adja meg az Azure-erőforráscsoport számára használni kívánt nevet és egyéb Project-információkat. Ha elkészült, válassza a **Létrehozás** lehetőséget.

1. Adja meg a projekt nevét és helyét, majd kattintson **az OK gombra**.

   A Visual Studio egy sablon kiválasztását kéri a sablonok listából. 
   Ez a példa egy Azure gyors üzembe helyezési sablont használ, amellyel olyan kiszolgáló nélküli alkalmazások hozhatók létre, amelyek egy logikai alkalmazást és egy Azure-függvény hívását is tartalmaznak.

   > [!TIP]
   > Olyan esetekben, amikor nem szeretné előtelepíteni a megoldását egy Azure-erőforráscsoporthoz, használhatja az üres **logikai alkalmazás** sablont, amely csak egy üres logikai alkalmazást hoz létre.

1. A **sablonok megjelenítése ezen a helyen** listából válassza az Azure rövid útmutató **(GitHub.com/Azure/Azure-Quickstart-Templates)** lehetőséget.

1. A keresőmezőbe írja be szűrőként a "Logic-app" kifejezést. Az eredmények közül válassza ki a **101-Logic-app-and-Function-app** sablont.

   ![Azure Gyorsindítás sablon kiválasztása](./media/logic-apps-serverless-get-started-vs/select-template.png)

   A Visual Studio létrehoz és megnyit egy megoldást az erőforráscsoport-projekthez. 
   A kiválasztott Azure Gyorsindítás sablon létrehoz egy azuredeploy.jsnevű központi telepítési sablont az erőforráscsoport-projekten belül. Ez a központi telepítési sablon tartalmazza a HTTP-kérelem által aktivált egyszerű logikai alkalmazás definícióját, meghívja az Azure-függvényt, és az eredményt HTTP-válaszként adja vissza.

   ![Új kiszolgáló nélküli megoldás](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Ezután telepítse üzembe a megoldását az Azure-ban. Ezt csak akkor kell megtennie, ha megnyitja a központi telepítési sablont, és áttekinti a kiszolgáló nélküli alkalmazás erőforrásait.

## <a name="deploy-your-solution"></a>A megoldás üzembe helyezése

Mielőtt megnyithatja a logikai alkalmazást a Logic app Designerben a Visual Studióban, rendelkeznie kell egy, az Azure-ban már üzembe helyezett Azure-erőforráscsoport használatával. A tervező ezután létrehozhat kapcsolatokat a logikai alkalmazás erőforrásaihoz és szolgáltatásaihoz. Ehhez a feladathoz kövesse az alábbi lépéseket a megoldás Visual studióból a Azure Portalba való üzembe helyezéséhez:

1. Megoldáskezelő az erőforrás-projekt helyi menüjéből válassza az   >  **új** telepítése lehetőséget.

   ![Új központi telepítés létrehozása erőforráscsoport számára](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Ha még nincsenek kijelölve, válassza ki az Azure-előfizetését és azt az erőforráscsoportot, amelyre telepíteni kívánja. Ezután válassza a **telepítés** lehetőséget.

   ![Központi telepítési beállítások](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adja meg a logikai alkalmazáshoz és az Azure Function alkalmazáshoz a telepítéskor használni kívánt erőforrás-neveket, majd mentse a beállításokat. Győződjön meg arról, hogy globálisan egyedi nevet használ a Function alkalmazáshoz.

   ![Adja meg a logikai alkalmazás és a Function alkalmazás nevét](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Ha a Visual Studio elindítja az üzembe helyezést a megadott erőforráscsoporthoz, a megoldás telepítési állapota a Visual Studio **kimeneti** ablakában jelenik meg. 
   Az üzembe helyezés befejeződése után a logikai alkalmazás a Azure Portalban él.

## <a name="edit-your-logic-app-in-visual-studio"></a>Logikai alkalmazás szerkesztése a Visual Studióban

Ha az üzembe helyezés után szeretné szerkeszteni a logikai alkalmazást, nyissa meg a logikai alkalmazást a Visual Studióban a Logic app Designer használatával.

1. Megoldáskezelő a fájl azuredeploy.jshelyi menüjében válassza a **Megnyitás a Logic app Designerben** lehetőséget.

   ![azuredeploy.jsmegnyitása a Logic app Designerben](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

1. Miután a **Logic app tulajdonságok** mező megjelenik, az **előfizetés** területen válassza ki az Azure-előfizetését, ha még nincs kiválasztva. Az **erőforráscsoport** területen válassza ki azt az erőforráscsoportot és helyet, ahová a megoldást telepítette, majd kattintson **az OK gombra**.

   ![Logikai alkalmazás tulajdonságai](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   A Logic app Designer megnyitása után folytathatja a lépések hozzáadását, illetve módosíthatja a munkafolyamatot, és mentheti a frissítéseket.

   ![Logikai alkalmazás megnyitva a Logic app Designerben](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Azure Functions projekt létrehozása

A functions-projekt és-függvény JavaScript, Python, F #, PowerShell, batch vagy bash használatával történő létrehozásához kövesse a következő témakörben ismertetett lépéseket: [Azure functions Core Tools](../azure-functions/functions-run-local.md). Ha az Azure-függvényt a C#on belül a megoldásban kívánja fejleszteni, egy C# osztály-függvénytárat kell használnia a [.net-osztály könyvtára függvényalkalmazásként való közzétételének](https://azure.microsoft.com/blog/)lépésein követve.

## <a name="deploy-functions-from-visual-studio"></a>Függvények üzembe helyezése a Visual studióból

Az üzembe helyezési sablon minden olyan Azure-függvényt üzembe helyez, amelyet a megoldásában a fájl azuredeploy.jsváltozói által megadott git-tárházban kell megadni. Ha a functions-projektet a megoldásában hozza létre és készíti el, a projektet a git-verziókövetés (például a GitHub vagy az Azure DevOps) segítségével ellenőrizheti, majd frissítheti a `repo` változót úgy, hogy a sablon üzembe helyezi az Azure-függvényt.

## <a name="manage-logic-apps-and-view-run-history"></a>Logikai alkalmazások kezelése és a futtatási előzmények megtekintése

Az Azure-ban már üzembe helyezett Logic apps esetében továbbra is szerkesztheti, kezelheti, megtekintheti a futtatási előzményeit, és letilthatja ezeket az alkalmazásokat a Visual studióból.

1. A Visual Studio **nézet** menüjében nyissa meg a **Cloud Explorert**.

1. A **minden előfizetés** területen válassza ki a kezelni kívánt logikai alkalmazásokhoz társított Azure-előfizetést, majd kattintson az **alkalmaz** gombra.

1. A **Logic apps** alatt válassza ki a logikai alkalmazást. Az alkalmazás helyi menüjében válassza **a Megnyitás a Logic app Editor** használatával lehetőséget.

   > [!TIP]
   > Ha nem rendelkezik ezzel a paranccsal a Visual Studio 2019-ben, ellenőrizze, hogy rendelkezik-e a legújabb Visual Studio-frissítésekkel.

Ezután letöltheti a már közzétett logikai alkalmazást az erőforráscsoport-projektbe. Így bár lehet, hogy elindított egy logikai alkalmazást a Azure Portalban, továbbra is importálhatja és kezelheti az alkalmazást a Visual Studióban. További információ: [logikai alkalmazások kezelése a Visual Studióval](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Következő lépések

* [Logikai alkalmazások kezelése a Visual Studióval](manage-logic-apps-with-visual-studio.md)
