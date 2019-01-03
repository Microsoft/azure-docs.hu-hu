---
title: Készítsen kiszolgáló nélküli alkalmazásokat a Visual Studióval |} A Microsoft Docs
description: Elkészítheti, telepítheti és kezelheti az első kiszolgáló nélküli alkalmazás az Azure Logic Apps és az Azure Functions Visual studióban
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d565873c-6b1b-4057-9250-cf81a96180ae
ms.custom: vs-azure
ms.topic: article
ms.date: 08/01/2018
ms.openlocfilehash: c172519984cce765217a713b276db5ccc8f67183
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53558600"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Az Azure Logic Apps és az Azure Functions – a Visual Studio az első kiszolgáló nélküli alkalmazás létrehozása

Gyorsan fejleszthet és helyezhet üzembe felhőalkalmazásokat használatával kiszolgáló nélküli eszközöket és funkciókat az Azure-ban például [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [Azure Functions](../azure-functions/functions-overview.md). Ez a cikk bemutatja, hogyan kezdjen el létrehozni egy kiszolgáló nélküli alkalmazás, amely egy logikai alkalmazást, amely meghívja ezt a Visual Studióban egy Azure-függvényt használja. Az Azure-beli kiszolgáló nélküli megoldásokkal kapcsolatos további információkért lásd: [Logic Apps és Functions kiszolgáló nélküli Azure](../logic-apps/logic-apps-serverless-overview.md).

## <a name="prerequisites"></a>Előfeltételek

A Visual Studióban egy kiszolgáló nélküli alkalmazás készítése, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [A Visual Studio 2017](https://www.visualstudio.com/vs/) vagy a Visual Studio 2015 – Community, Professional vagy Enterprise

* [A Microsoft Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 vagy újabb)

* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

* [Azure Logic Apps Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio-18551) vagy a [Visual Studio 2015-ös verziója](https://marketplace.visualstudio.com/items?itemName=VinaySinghMSFT.AzureLogicAppsToolsforVisualStudio)

  Letöltheti és telepítheti az Azure Logic Apps Tools a Visual Studio Marketplace-ről vagy [megtudhatja, hogyan telepítheti a bővítményt a Visual Studión belül](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). Ellenőrizze, hogy telepítésének befejezése után indítsa újra a Visual Studio.

* [Az Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) helyi hibakeresési funkciók

* A Visual Studióban beágyazott hozzáférést a Logikaialkalmazás-Tervező használatakor.

  A tervezőnek internetkapcsolatra van szüksége, hogy létre tudja hozni az erőforrásokat az Azure-ban, és be tudja olvasni a tulajdonságokat és adatokat a logikai alkalmazásban lévő összekötőkből. Ha például a Dynamics CRM Online-összekötőt használja, a tervező elérhető alapértelmezett és egyéni tulajdonságokat keres a CRM-példányon.

## <a name="create-resource-group-project"></a>Erőforráscsoport-projekt létrehozása

Első lépésként hozzon létre egy [Azure erőforráscsoport-projekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) a kiszolgáló nélküli alkalmazáshoz. Azure-hoz létre egy erőforráscsoportot, amely olyan logikai gyűjtemény, rendszerezése és kezelése használhatja erőforrásának, és a egy egész alkalmazás az erőforrások telepítését egyetlen eszközként. Az Azure-beli kiszolgáló nélküli alkalmazások esetén az erőforráscsoport az Azure Logic Apps és az Azure Functions forrásokat tartalmazza. További információk az [Azure-erőforráscsoportokról és -erőforrásokról](../azure-resource-manager/resource-group-overview.md).

1. Indítsa el a Visual Studiót, és jelentkezzen be az Azure-fiókjával.

1. A **Fájl** menüben válassza az **Új** > **Projekt** elemet.

   ![Új projekt létrehozása a Visual Studióban](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. A **Telepítve** területen válassza a **Visual C#** vagy a **Visual Basic** elemet. Válassza a **Felhő** > **Azure-erőforráscsoport** elemet.

   Ha a **felhőalapú** kategória vagy **Azure-erőforráscsoport** projekt nem létezik, győződjön meg arról, hogy telepítette az Azure SDK for Visual Studio.

1. A projekthez adjon egy nevet és a egy olyan helyre, és válassza a **OK**.

   A Visual Studio megkéri, hogy válasszon ki egy sablont. Kezdhet egy üres, a logikai alkalmazás vagy a többi sablont, de ez a példa egy Azure gyorsindítási sablon használ, amely tartalmazza a Logic Apps-alkalmazás és a egy Azure-függvényt egy hívás kiszolgáló nélküli alkalmazás létrehozásához.

   Csak a logikai alkalmazás létrehozása a Visual Studióban, válassza ki a **logikai alkalmazás** sablont. Ez a sablon létrehoz egy üres logikai alkalmazást, megnyílik a Logic App Designerben előtelepítését megoldását az Azure-erőforráscsoport nélkül.

1. A **erről a helyről sablon megjelenítése**válassza **Azure gyors üzembe helyezés (GitHub/Azure/azure-gyorsindítási-sablonok)**.

1. A Keresés mezőbe írja be az "logikai alkalmazás" szűrőként, és válassza a kiszolgáló nélküli gyorsindítási sablon, majd **OK**: **101-Logic-App-and-Function-App**

   ![Válassza ki az Azure gyorsindítási sablon](./media/logic-apps-serverless-get-started-vs/select-template.png)

   A Visual Studio létrehozza, és megnyitja az erőforráscsoport-projekt megoldást. A kiválasztott gyorsindítási sablon létrehozza a központi telepítési sablont `azuredeploy.json` belül az erőforráscsoport-projekt. A központi telepítési sablont a HTTP-kérés aktiválja, meghív egy Azure-függvényt, és visszaadja az eredményt egy HTTP-választ, egyszerű logikai alkalmazás definícióját tartalmazza.
   
   ![Új kiszolgáló nélküli megoldás](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Ezután telepítenie kell a megoldás az Azure-bA ahhoz, hogy nyissa meg a központi telepítési sablont, és tekintse át az erőforrásokat a kiszolgáló nélküli alkalmazáshoz.

## <a name="deploy-your-solution"></a>A megoldás üzembe helyezése

A logikai alkalmazás megnyitása a Visual studióban a Logikaialkalmazás-tervezővel, előtt rendelkeznie kell egy Azure-erőforráscsoportot, amely már üzembe helyezte az Azure-ban. A Tervező majd hozhatnak létre kapcsolatokat erőforrásokat és szolgáltatásokat a logikai alkalmazásban. Ebben a feladatban üzembe helyezése a megoldást a Visual Studióból az Azure Portalon.

1. A Megoldáskezelőben nyissa meg az erőforráscsoport-projekt helyi menüjéből, és válassza ki **telepítés** > **új**.

   ![Erőforráscsoporthoz. új üzemelő példány létrehozása](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Ha még nem lenne kiválasztva, válassza ki az Azure-előfizetés és az erőforráscsoport, ahol telepíteni szeretné. Válasszon **üzembe helyezése**.

   ![Központi telepítési beállítások](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Ha a **paraméterek szerkesztése** mezőben jelenik meg, adja meg a logikai alkalmazás és az Azure-függvényalkalmazás üzembe helyezéskor használandó erőforrás nevét, majd mentse a beállításokat. Ellenőrizze, hogy az a függvényalkalmazás globálisan egyedi név.

   ![Adja meg a nevet a logikai alkalmazás és a függvényalkalmazás](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   A megadott erőforráscsoportba irányuló üzembe helyezés a Visual Studio indulásakor a megoldás üzembe helyezés állapota látható-e a Visual Studio **kimeneti** ablak. Üzembe helyezés befejezése után a logikai alkalmazás aktiválódik az Azure Portalon.

## <a name="edit-logic-app-in-visual-studio"></a>A Visual Studióban a logikai alkalmazás szerkesztése

Most, hogy a megoldás az erőforráscsoportban üzembe, nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, szerkesztheti, és módosítsa a logikai alkalmazást.

1. A Megoldáskezelőben nyissa meg a `azuredeploy.json` fájlt a helyi menü, és válassza ki **nyissa meg a Logic App Designerben**.

   ![Nyissa meg a "azuredeploy.json" Logic App Designerben](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Miután a **logikai alkalmazás tulajdonságai** mező jelenik meg, és ha még nem lenne kiválasztva, a **előfizetés**, válassza ki az Azure-előfizetés. A **erőforráscsoport**, válassza ki az erőforráscsoportot és a megoldás üzembe helyezésének helye, és válassza **OK**.

   ![Logikai alkalmazás tulajdonságai](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Miután megnyílik a Logikaialkalmazás-Tervező, hozzáadni a lépéseket vagy módosítsa a munkafolyamatot, és mentse a módosításokat.

   ![Logic App Designerben megnyitott logikai alkalmazás](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Az Azure Functions-projekt létrehozása

A JavaScript, Python, a Functions-projekt és a függvény létrehozásához F#, PowerShell, a Batch vagy a Bash, kövesse a cikk a [együttműködik az Azure Functions Core Tools](../azure-functions/functions-run-local.md). Az Azure-függvényt a C# belül a megoldás fejlesztése, használhatja a C# osztálytár a cikk lépéseit követve [közzététele a .NET osztálytár, a Függvényalkalmazás](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>A Visual Studio functions üzembe helyezése

A központi telepítési sablont bármely az Azure functions, amely rendelkezik a Git-adattár, amely a változó által meghatározott, a megoldás üzembe helyezi a `azuredeploy.json` fájlt. Hoz létre, és a Functions-projekt létrehozásához a megoldásában, ha a Git verziókövetési, például a GitHub vagy az Azure DevOps, tekintsen meg erre a projektre, és frissítse a `repo` változó, úgy, hogy a sablon üzembe helyez az Azure-függvény.

## <a name="manage-logic-apps-and-view-run-history"></a>Kezelheti a logic apps és a futtatási előzmények megtekintése

Már telepítve van az Azure logic Apps Ön is továbbra is szerkesztése, kezelése, futtatási előzmények megtekintése és tiltsa le ezeket az alkalmazásokat a Visual Studióból.

1. Az a **nézet** elemét a Visual Studióban, a nyílt **Cloud Explorer**.

1. A **minden előfizetés**, válassza ki az Azure-előfizetése társítva a logikai alkalmazásokat szeretne kezelni, és válassza a **alkalmaz**.

1. A **Logic Apps**, válassza ki a logikai alkalmazást. Az alkalmazás helyi menüjén válassza **nyissa meg a logikai alkalmazás szerkesztő**.

Letöltheti a már közzétett logikai alkalmazás most már be az erőforráscsoport-projekt. Tehát bár előfordulhat, hogy megkezdte egy logikai alkalmazást az Azure Portalon, továbbra is importálni és kezelése a Visual Studio alkalmazást. További információkért lásd: [logikai alkalmazások Visual studióval kezelése](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>További lépések

* [Egy kiszolgáló nélküli közösségi irányítópult létrehozása](logic-apps-scenario-social-serverless.md)
* [Logikai alkalmazások kezelése a Visual Studióval](manage-logic-apps-with-visual-studio.md)
* [Logikai alkalmazás munkafolyamat-definíciós nyelv](logic-apps-workflow-definition-language.md)
