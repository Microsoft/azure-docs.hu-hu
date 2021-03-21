---
title: Folyamatok exportálása a Power Gyorsbüféból a Azure Logic Appsba
description: Áttelepítheti a folyamatokat a Power Gyorsbüféból a Azure Logic Apps Azure Resource Manager sablonként való exportálással
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 02/22/2021
ms.openlocfilehash: f2b4e09ec9b50bb6993c89d90b0f33c0c905cbf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699096"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Folyamatok exportálása a Power Automate-ből és üzembe helyezés az Azure Logic Appsben

A flow képességeinek bővítéséhez és kibővítéséhez áttelepítheti a folyamatot a [Power gyorsbüféból](https://flow.microsoft.com) a [Azure Logic Appsba](../logic-apps/logic-apps-overview.md). Exportálhatja a folyamatot Azure Resource Manager sablonként egy logikai alkalmazáshoz, üzembe helyezheti a logikai alkalmazás sablonját egy Azure-erőforráscsoporthoz, majd megnyithatja a logikai alkalmazást a Logic app Designerben.

> [!IMPORTANT]
> A Logic Appsra való exportálás nem érhető el az augusztus 2020. után létrehozott Power automatizáló folyamatok számára. Október 2020-án az [OpenAPI 2,0-es protokollon](https://swagger.io/specification/v2/)a powered új flow-létrehozása automatizálható. A protokollon alapuló új folyamatok nem kompatibilisek Logic Apps munkafolyamatokkal, ezért a rendszer letiltotta ezeket a folyamatokat Logic Apps. Ehelyett manuálisan kell [létrehoznia a logikai alkalmazásokat](quickstart-create-first-logic-app-workflow.md) ezekhez a folyamatokhoz.

Nem minden automatizálási összekötő érhető el Azure Logic Appsban. Csak olyan automatizálási folyamatokat telepíthet át, amelyek Azure Logic Appsban egyenértékű összekötővel rendelkeznek. Például a gomb trigger, a jóváhagyási összekötő és az értesítési összekötő kifejezetten a Power automatizálható. 

* Ha szeretné megkeresni, hogy mely automatizálási összekötők nem rendelkeznek Logic Apps megfelelővel, tekintse meg a [Power automatizáló összekötők](/connectors/connector-reference/connector-reference-powerautomate-connectors)

* Ha szeretné megkeresni, hogy mely Logic Apps összekötők nem rendelkeznek az automatikus automatizálással, tekintse meg az [összekötők Logic apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* A Power automatizálni kívánt folyamat

## <a name="export-your-flow"></a>A folyamat exportálása

1. Jelentkezzen be a [Power gyorsbüféba](https://flow.microsoft.com), és válassza **a saját folyamatok** lehetőséget. Keresse meg és válassza ki a folyamatot. Az eszköztáron válassza a három pont (**...**) gombot > **Exportálás**  >  **Logic apps sablon (. JSON)** elemre.

   ![Folyamat exportálása a Power Gyorsbüféból](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Mentse a sablon. JSON fájlját a kívánt helyre.

További információ: [Grow up to Azure Logic apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Sablon üzembe helyezése a Azure Portal használatával

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) Azure-fiókjával.

1. Az Azure kezdőlapján, a keresőmezőbe írja be a következőt: `custom template` . Az eredmények közül válassza az **egyéni sablon**  >  **létrehozása** lehetőséget.

   ![A "Template deployment" megkeresése és kiválasztása](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. Az **Egyéni telepítés** területen válassza **a saját sablon létrehozása lehetőséget a szerkesztőben**.

   ![Válassza a "saját sablon létrehozása a szerkesztőben" lehetőséget.](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. A **Sablon szerkesztése** eszköztáron válassza a **fájl betöltése** lehetőséget.

   ![Válassza a "fájl betöltése" lehetőséget.](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Tallózással keresse meg azt a helyet, ahová a Power automatizálásból exportált JSON-sablonfájlt mentette. Válassza ki a sablonfájl > **megnyitva**.

1. Miután a szerkesztő megjeleníti a sablonban található JSON-, paramétereket és erőforrásokat, válassza a **Mentés** lehetőséget.

   ![Sablon mentése](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Most adja meg a logikai alkalmazás további információit.

   1. Válassza ki vagy adja meg a sablon bemeneti paraméterének értékeit.

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Előfizetés** | A számlázáshoz használandó Azure-előfizetés |
      | **Erőforráscsoport** | A logikai alkalmazáshoz használandó Azure-erőforráscsoport. Meglévő csoportot is használhat, vagy létrehozhat egy új csoportot. |
      | **Hely** | Új erőforráscsoport létrehozásakor használandó Azure-régió |
      | **Logikai alkalmazás neve** | A logikai alkalmazás erőforrásához használandó név |
      | **Logikai alkalmazás helye** | Az Azure-régió, amelyben létre kívánja hozni a logikai alkalmazás erőforrását, ha az Azure-erőforráscsoport eltér |
      | <*kapcsolattípus*> | A logikai alkalmazás által újra felhasználható korábban létrehozott kapcsolatok egy vagy több neve <p><p>**Megjegyzés**: Ha ez a logikai alkalmazás az első, az összes kapcsolat újként jön létre, így elfogadhatja az alapértelmezett neveket. Ellenkező esetben megadhatja a korábban létrehozott kapcsolatok nevét, amelyeket több logikai alkalmazás között használhat. |
      |||

      Például:

      ![Adja meg a sablon bemeneti paramétereit](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Ha elkészült, tekintse át a szükséges Azure-erőforrások létrehozásával és az Azure-előfizetés megfelelő számlázásával kapcsolatos **feltételeket és kikötéseket** .

   1. Ha elkészült, válassza az Elfogadom a megvásárolt **feltételek és kikötések** lehetőséget  >  .

      Az Azure a sablont logikai alkalmazásként telepíti a megadott erőforráscsoporthoz.

1. Az energiagazdálkodási automatizálásból áttelepített összes logikai alkalmazást letiltott állapotban telepíti a rendszer. A logikai alkalmazás engedélyezése előtt engedélyezze a következő lépéseket az új kapcsolatok engedélyezéséhez:

   1. A Azure Portal nyissa meg a létrehozott logikai alkalmazást. A logikai alkalmazás menüjében válassza a **Logic app Designer** elemet.

      Az engedélyezést igénylő minden egyes kapcsolaton figyelmeztető ikon jelenik meg:

      ![Figyelmeztetés ikon](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Minden olyan lépéshez, amelyhez engedélyezve van a kapcsolat, bontsa ki ezt a lépést, és válassza az **új hozzáadása** lehetőséget.

      ![Képernyőkép, amely az Outlook "kapcsolatok" ablakában kiválasztott "új" gombot jeleníti meg.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Jelentkezzen be az egyes szolgáltatásokhoz, vagy adja meg a szükséges hitelesítő adatokat a kapcsolódás engedélyezéséhez.

   1. A kapcsolatok frissítése után a tervező eszköztárán válassza a **Mentés** lehetőséget.

1. Ha készen áll a logikai alkalmazás aktiválására, a logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget, majd válassza az **Engedélyezés** lehetőséget.

   ![Logikai alkalmazás engedélyezése](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Az ismétlődő munkafolyamatok futtatásának elkerüléséhez győződjön meg arról, hogy az eredeti folyamatot inaktiválja vagy törli.

## <a name="deploy-template-by-using-visual-studio"></a>Sablon üzembe helyezése a Visual Studióval

Ha a Visual studiót a Logic apps létrehozásához szükséges [előfeltételekkel](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) állította be, a Visual studióval Azure Logic apps üzembe helyezheti az exportált sablont.

1. A Visual Studióban keresse meg és nyissa meg a Power automatizálásból exportált Logic app-sablonhoz tartozó. JSON fájlt.

1. A Visual Studióban hozzon létre egy **Azure erőforráscsoport** -projektet, amely a **Logic app** -sablont használja a következő témakörben ismertetett lépéseket követve [: automatikus feladatok, folyamatok és munkafolyamatok létrehozása Azure Logic apps-Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   Ez a példa egy "ImportedLogicApp" nevű Visual Studio-megoldást hoz létre.

   ![Azure erőforráscsoport-projekt létrehozása](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. A megoldás létrehozása után Megoldáskezelőban nyissa meg a **LogicApp.js** fájlt, ha a fájl még nincs megnyitva.

1. Másolja a tartalmat az exportált sablonból, és írja felül a **LogicApp.js** fájl tartalmát.

1. A logikai alkalmazás üzembe helyezése előtt engedélyezze a következő lépéseket az új kapcsolatok engedélyezéséhez:

   1. Nyissa meg a **LogicApp.jsa** helyi menüben, majd válassza a **Megnyitás a Logic app Designerben** lehetőséget.

      ![Sablon megnyitása a Logic app Designerrel](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Ha a rendszer kéri, válassza ki azt az Azure-előfizetést és erőforráscsoportot, amelyet a logikai alkalmazás üzembe helyezéséhez használni kíván.

      ![Válassza ki az Azure-előfizetést és az erőforráscsoportot](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Miután a logikai alkalmazás megjelenik a tervezőben, az engedélyezést igénylő összes kapcsolat figyelmeztető ikonokat jelenít meg:

      ![Figyelmeztető ikonnal rendelkező kapcsolatok](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Minden olyan lépéshez, amelyhez engedélyezve van a kapcsolat, bontsa ki ezt a lépést, és válassza az **új hozzáadása** lehetőséget.

      ![Új kapcsolatok hozzáadása](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Jelentkezzen be az egyes szolgáltatásokhoz, vagy adja meg a szükséges hitelesítő adatokat a kapcsolódás engedélyezéséhez.

   1. Mentse a megoldást a logikai alkalmazás üzembe helyezése előtt.

1. A Megoldáskezelőban nyissa meg a projekt helyi menüt, és válassza az  >  **új** telepítése lehetőséget. Ha szükséges, jelentkezzen be Azure-fiókjával.

1. Amikor a rendszer kéri, erősítse meg az Azure-előfizetést, az Azure-erőforráscsoportot, valamint az üzembe helyezéshez használni kívánt egyéb beállításokat, például a sablon paramétereinek átadásához használandó [paramétereket](../azure-resource-manager/templates/parameter-files.md) , majd válassza a **telepítés** lehetőséget.

   ![Központi telepítési beállítások megerősítése](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Ha megjelenik a **Paraméterek szerkesztése** mező, adja meg a logikai alkalmazás erőforrásának nevét az Azure-ban, majd válassza a **Mentés** lehetőséget.  

   ![Telepítési paraméterek szerkesztése](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Amikor az üzembe helyezés elindul, az alkalmazás üzembehelyezési állapota a Visual Studio **Kimenet** ablakában jelenik meg. Ha az állapot nem jelenik meg, nyissa meg a **Kimenet mutatása innen** listát, és válassza ki az Azure-erőforráscsoportot. Például:

   ![Kimeneti ablak](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Ha a logikai alkalmazásban lévő bármelyik kapcsolatnak meg kell adnia a bemenetét, a háttérben megnyílik egy PowerShell-ablak, és megkérdezi a szükséges jelszavakat vagy titkos kulcsokat. Miután megadta ezeket az adatokat, folytatódik az üzembe helyezés.

   ![Kapcsolatok hitelesítése](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Az üzembe helyezés befejeződése után a logikai alkalmazás közzé van téve, de nincs aktiválva a Azure Portal.

1. Ha készen áll a logikai alkalmazás aktiválására a Azure Portalban, keresse meg és nyissa meg a logikai alkalmazást a Logic app Designerben. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget, majd válassza az **Engedélyezés** lehetőséget.

1. Az ismétlődő munkafolyamatok futtatásának elkerüléséhez győződjön meg arról, hogy az eredeti folyamatot inaktiválja vagy törli.

További információ ezekről az üzembe helyezési lépésekről: rövid útmutató [: automatizált feladatok, folyamatok és munkafolyamatok létrehozása Azure Logic apps-Visual Studióval](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Következő lépések

* További információ az [Azure Logic apps-összekötők számára](../connectors/apis-list.md)
* További információ a [Azure Logic apps](../logic-apps/logic-apps-overview.md)
