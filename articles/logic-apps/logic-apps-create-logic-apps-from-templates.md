---
title: Logikai alkalmazás-munkafolyamatok gyors létrehozása előre elkészített sablonok használatával
description: Logikai alkalmazások munkafolyamatainak gyors létrehozása a Azure Logic Apps által biztosított előre összeállított sablonok használatával
services: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 10/15/2017
ms.openlocfilehash: 42c592e6aede4537dc983fd2cff043a878f81f1c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593104"
---
# <a name="create-logic-app-workflows-from-prebuilt-templates"></a>Logikai alkalmazás munkafolyamatok létrehozása előre összeállított sablonokból

A munkafolyamatok gyorsabb létrehozásának megkezdéséhez Logic Apps biztosít sablonokat, amelyek előre elkészített logikai alkalmazások, amelyek gyakran használt mintákat követnek. Használja ezeket a sablonokat a megadott módon, vagy szerkessze őket, hogy illeszkedjenek a forgatókönyvhöz.

Néhány sablon-Kategória:

| Sablon típusa | Description | 
| ------------- | ----------- | 
| Vállalati felhőalapú sablonok | Az Azure Blob, a Dynamics CRM, a Salesforce, a Box és más összekötők integrálása a vállalati felhő igényei szerint. Ezekkel a sablonokkal például rendszerezheti az üzleti érdeklődőket, vagy biztonsági másolatot készíthet a vállalati fájlok adatairól. | 
| Személyes hatékonyságnövelő sablonok | A napi emlékeztetők megadásával javíthatja a személyes hatékonyságot, a fontos munkaelemeket a feladatlistába helyezheti, és a hosszadalmas feladatok automatizálását egyetlen felhasználói jóváhagyási lépéshez hajthatja végre. | 
| Fogyasztói felhőalapú sablonok | A közösségi média-szolgáltatások, például a Twitter, a Slack és az e-mailek integrálásához. Hasznos a közösségi média marketing-kezdeményezéseinek megerősítéséhez. Ezek a sablonok olyan feladatokat is tartalmaznak, mint például a Felhőbeli másolás, ami növeli a hatékonyságot a hagyományosan ismétlődő feladatokhoz való idő megtakarításával. | 
| Enterprise Integration Pack-sablonok | A VETER (validate, Extract, Transform, gazdagabbá, Route) folyamatok konfigurálásához, X12 EDI-dokumentum fogadásához az AS2 használatával és az XML-re való átalakításhoz, valamint a X12, a EDIFACT és az AS2-üzenetek kezelésére. | 
| Protokoll-minta sablonok | Olyan protokollok megvalósításához, mint például a kérés-válasz HTTP-n keresztül, valamint az FTP és az SFTP közötti integráció. Ezeket a sablonokat a megadott módon használhatja, vagy összetett protokoll-mintázatokra építhet rájuk. | 
||| 

Ha nem rendelkezik Azure-előfizetéssel, [regisztrálhat egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/) az eljárás megkezdése előtt. A logikai alkalmazások létrehozásával kapcsolatos további információkért lásd: [logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-logic-apps-from-templates"></a>Logikai alkalmazások létrehozása sablonból

1. Ha még nem tette meg, jelentkezzen be a [Azure Portalba](https://portal.azure.com "Azure Portal").

2. Az Azure fő menüjében válassza az **erőforrás létrehozása**  >  **Vállalati integráció**  >  **logikai alkalmazás** lehetőséget.

   ![Azure Portal, Új, Vállalati integráció, Logikai alkalmazás](./media/logic-apps-create-logic-apps-from-templates/azure-portal-create-logic-app.png)

3. Hozza létre a logikai alkalmazást a következő kép alatti táblázatban szereplő beállításokkal:

   ![A logikai alkalmazás részleteinek megadása](./media/logic-apps-create-logic-apps-from-templates/logic-app-settings.png)

   | Beállítás | Érték | Leírás | 
   | ------- | ----- | ----------- | 
   | **Név** | *a-logikai-alkalmazása-neve* | Adjon meg egy egyedi nevet a logikai alkalmazás számára. | 
   | **Előfizetés** | *az-Ön-Azure-előfizetésének-neve* | Válassza ki a használni kívánt Azure-előfizetést. | 
   | **Erőforráscsoport** | *az-Ön-Azure-erőforráscsoportjának-neve* | Hozzon létre vagy válasszon ki egy [Azure-erőforráscsoportot](../azure-resource-manager/management/overview.md) ehhez a logikai alkalmazáshoz, és rendezze az ehhez az alkalmazáshoz társított összes erőforrást. | 
   | **Hely** | *az-Ön-Azure-adatközpont-régiója* | Válassza ki az adatközpont-régiót a logikai alkalmazás üzembe helyezéséhez, például az USA nyugati régióját. | 
   | **Naplóelemzés** | **Kikapcsolva** (alapértelmezett **) vagy** | A logikai alkalmazás [diagnosztikai naplózásának](../logic-apps/monitor-logic-apps-log-analytics.md) beállítása [Azure monitor naplók](../azure-monitor/logs/log-query-overview.md)használatával. A használatához már van Log Analytics munkaterület. | 
   |||| 

4. Amikor végzett, válassza a **Rögzítés az irányítópulton** lehetőséget. Így a logikai alkalmazás automatikusan megjelenik az Azure-irányítópulton, és megnyílik az üzembe helyezés után. Válassza a **Létrehozás** lehetőséget.

   > [!NOTE]
   > Ha nem szeretné rögzíteni a logikai alkalmazást, a folytatáshoz manuálisan kell megkeresnie és megnyitnia a logikai alkalmazást az üzembe helyezés után.

   Miután az Azure üzembe helyezte a logikai alkalmazást, megnyílik a Logic Apps Designer, és egy bemutató videót tartalmazó oldalt jelenít meg. 
   A videó alatt megtalálja a gyakori logikaialkalmazás-minták sablonjait. 

5. Görgesse át a bevezető videót és a gyakori eseményindítókat a **sablonokhoz**. Válasszon egy előre elkészített sablont. Például:

   ![Logikai alkalmazás sablonjának kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   > [!TIP]
   > A logikai alkalmazás előzmények nélküli létrehozásához válassza az **üres logikai alkalmazás** lehetőséget.

   Egy előre elkészített sablon kiválasztásakor a sablonra vonatkozó további információk is megtekinthetők. 
   Például:

   ![Előre elkészített sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

6. A kiválasztott sablon folytatásához válassza a **sablon használata** lehetőséget. 

7. A sablon összekötői alapján a rendszer a következő lépések bármelyikét kéri:

   * Jelentkezzen be a hitelesítő adataival a sablon által hivatkozott rendszerekhez vagy szolgáltatásokhoz.

   * Hozzon létre kapcsolatokat a sablon által hivatkozott bármely szolgáltatáshoz vagy rendszerhez. A kapcsolatok létrehozásához adja meg a kapcsolódás nevét, és ha szükséges, válassza ki a használni kívánt erőforrást. 

   * Ha már beállította ezeket a kapcsolatokat, válassza a **Folytatás** lehetőséget.

   Például:

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Ha elkészült, megnyílik a logikai alkalmazás, és megjelenik a Logic Apps Designerben.

   > [!TIP]
   > A sablon megjelenítőhöz való visszatéréshez válassza a tervező eszköztár **sablonok** elemét. Ez a művelet elveti a nem mentett módosításokat, ezért megjelenik egy figyelmeztető üzenet, amely megerősíti a kérést.

8. Folytassa a logikai alkalmazás összeállítását.

   > [!NOTE] 
   > Számos sablon olyan összekötőket tartalmaz, amelyek már rendelkezhetnek előre feltöltött kötelező tulajdonságokkal. Előfordulhat azonban, hogy egyes sablonok esetében továbbra is meg kell adnia az értékeket, mielőtt megfelelően telepítené a logikai alkalmazást. Ha a hiányzó tulajdonságmezők befejezése nélkül próbálkozik a telepítéssel, hibaüzenet jelenik meg. 

## <a name="update-logic-apps-with-templates"></a>Logikai alkalmazások frissítése sablonokkal

1. A [Azure Portal](https://portal.azure.com "Azure Portal")a Logic app Designerben keresse meg és nyissa meg a logikai alkalmazást.

2. A tervező eszköztárán válassza a **sablonok** lehetőséget. Ez a művelet elveti a nem mentett módosításokat, ezért megjelenik egy figyelmeztető üzenet, amely megerősíti, hogy folytatja a műveletet. A megerősítéshez kattintson **az OK gombra**. Például:

   ![Válassza a "sablonok" lehetőséget.](./media/logic-apps-create-logic-apps-from-templates/logic-app-update-existing-with-template.png)

3. Görgesse át a bevezető videót és a gyakori eseményindítókat a **sablonokhoz**. Válasszon egy előre elkészített sablont. Például:

   ![Logikai alkalmazás sablonjának kiválasztása](./media/logic-apps-create-logic-apps-from-templates/choose-logic-app-template.png)

   Egy előre elkészített sablon kiválasztásakor a sablonra vonatkozó további információk is megtekinthetők. 
   Például:

   ![Előre elkészített sablon kiválasztása](./media/logic-apps-create-logic-apps-from-templates/logic-app-choose-prebuilt-template.png)

4. A kiválasztott sablon folytatásához válassza a **sablon használata** lehetőséget. 

5. A sablon összekötői alapján a rendszer a következő lépések bármelyikét kéri:

   * Jelentkezzen be a hitelesítő adataival a sablon által hivatkozott rendszerekhez vagy szolgáltatásokhoz.

   * Hozzon létre kapcsolatokat a sablon által hivatkozott bármely szolgáltatáshoz vagy rendszerhez. A kapcsolatok létrehozásához adja meg a kapcsolódás nevét, és ha szükséges, válassza ki a használni kívánt erőforrást. 

   * Ha már beállította ezeket a kapcsolatokat, válassza a **Folytatás** lehetőséget.

   ![Kapcsolatok létrehozása](./media/logic-apps-create-logic-apps-from-templates/logic-app-create-connection.png)

   Ekkor megnyílik a logikai alkalmazás, és megjelenik a Logic Apps Designerben.

8. Folytassa a logikai alkalmazás összeállítását. 

   > [!TIP]
   > Ha még nem mentette a módosításokat, elvetheti a munkáját, és visszatérhet az előző logikai alkalmazáshoz. A tervező eszköztárán válassza az **Elvetés** lehetőséget.

> [!NOTE] 
> Számos sablon olyan összekötőket tartalmaz, amelyek már előre feltöltött kötelező tulajdonságokkal rendelkezhetnek. Előfordulhat azonban, hogy egyes sablonok esetében továbbra is meg kell adnia az értékeket, mielőtt megfelelően telepítené a logikai alkalmazást. Ha a hiányzó tulajdonságmezők befejezése nélkül próbálkozik a telepítéssel, hibaüzenet jelenik meg.

## <a name="deploy-logic-apps-built-from-templates"></a>Sablonokból készített logikai alkalmazások üzembe helyezése

Miután elvégezte a módosításokat a sablonban, mentheti a módosításokat. Ez a művelet automatikusan közzéteszi a logikai alkalmazást is.

A tervező eszköztárán válassza a **Mentés** parancsot.

![Logikai alkalmazás mentése és közzététele](./media/logic-apps-create-logic-apps-from-templates/logic-app-save.png)  

## <a name="get-support"></a>Támogatás kérése

* Ha kérdése van, látogasson el a [Microsoft Q&a Azure Logic apps vonatkozó kérdés oldalára](/answers/topics/azure-logic-apps.html).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Következő lépések

Tudnivalók a logikai alkalmazások létrehozásáról példákkal, forgatókönyvekkel, vásárlói történetekkel és forgatókönyvekkel.

> [!div class="nextstepaction"]
> [A Logic apps-példák, forgatókönyvek és forgatókönyvek áttekintése](../logic-apps/logic-apps-examples-and-scenarios.md)
