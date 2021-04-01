---
title: Feladatok és munkafolyamatok automatizálása a Visual Studio Code-ban
description: Logic app-munkafolyamati definíciók létrehozása vagy szerkesztése a Visual Studio Code használatával (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/28/2020
ms.openlocfilehash: f46c093bffcc26b5f7975d25ccaca648bdc527da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054686"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Rövid útmutató: Logic Apps-munkafolyamatdefiníciók létrehozása és kezelése a Visual Studio Code használatával

A [Azure Logic apps](../logic-apps/logic-apps-overview.md) és a Visual Studio Code használatával olyan logikai alkalmazásokat hozhat létre és kezelhet, amelyek segítségével automatizálhatja a feladatokat, munkafolyamatokat és folyamatokat az alkalmazások, adatok, rendszerek és szolgáltatások integrálásához a szervezetek és a vállalatok között. Ez a rövid útmutató bemutatja, hogyan hozhatja létre és szerkesztheti az alapul szolgáló munkafolyamat-definíciókat, amelyek JavaScript Object Notation (JSON) használnak a Logic apps számára a kód alapú felhasználói felületeken keresztül. Az Azure-ban már üzembe helyezett meglévő logikai alkalmazások is dolgozhatnak.

Habár ugyanezeket a feladatokat a [Azure Portal](https://portal.azure.com) és a Visual Studióban is elvégezheti, gyorsabban megkezdheti a Visual Studio Code-ban, ha már ismeri a Logic app-definíciókat, és közvetlenül a kódban szeretne dolgozni. Például letilthatja, engedélyezheti, törölheti és frissítheti a már létrehozott Logic apps-alkalmazásokat. Emellett a Logic apps és az integrációs fiókok bármilyen fejlesztői platformon használhatók, ahol a Visual Studio Code fut, például a Linux, a Windows és a Mac.

Ebben a cikkben ugyanezt a logikai alkalmazást hozhatja [létre ebből a](../logic-apps/quickstart-create-first-logic-app-workflow.md)rövid útmutatóból, amely az alapfogalmakra összpontosít. Azt is [megtudhatja, hogyan hozhatja létre a példás alkalmazást a Visual Studióban](quickstart-create-logic-apps-with-visual-studio.md), és [hogyan hozhat létre és kezelhet alkalmazásokat az Azure Command-Line felületen (Azure CLI)](quickstart-logic-apps-azure-cli.md). A Visual Studio Code-ban a logikai alkalmazás a következő példához hasonlóan néz ki:

![Példa a logikai alkalmazás munkafolyamatának definíciója](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekikezdene, győződjön meg arról, hogy rendelkezik a következőkkel:

* Ha nem rendelkezik Azure-fiókkal és-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapszintű ismeretek a [Logic app-munkafolyamatok definícióinak](../logic-apps/logic-apps-workflow-definition-language.md) és a JSON-vel leírt struktúrájáról

  Ha még nem ismeri a Logic Appst, próbálja [ki ezt a](../logic-apps/quickstart-create-first-logic-app-workflow.md)rövid útmutatót, amely létrehozza az első logikai alkalmazásait a Azure Portalban, és jobban összpontosít az alapfogalmakra.

* Hozzáférés a webhelyhez az Azure-ba való bejelentkezéshez és az Azure-előfizetéshez

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * A [Visual Studio Code 1.25.1 vagy újabb verziója](https://code.visualstudio.com/), amely ingyenes

  * Visual Studio Code-bővítmény a Azure Logic Appshoz

    Ezt a bővítményt letöltheti és telepítheti a [Visual Studio piactérről](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) , vagy közvetlenül a Visual Studio Code-ból. Győződjön meg arról, hogy a telepítés után újratölti a Visual Studio Code-ot.

    ![A "Visual Studio Code-bővítmény Azure Logic Apps" keresése](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Annak ellenőrzéséhez, hogy a bővítmény megfelelően van-e telepítve, válassza ki a Visual Studio Code eszköztárán megjelenő Azure-ikont.

    ![A bővítmény megfelelő telepítésének megerősítése](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    További információ: [bővítmény piactér](https://code.visualstudio.com/docs/editor/extension-gallery). A bővítmény nyílt forráskódú verziójának beszerzéséhez látogasson el a [Azure Logic apps-bővítményt a Visual Studio Code](https://github.com/Microsoft/vscode-azurelogicapps)-hoz a githubon.

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott IP-címekre korlátozza a forgalmat, *akkor a* tűzfalnak engedélyeznie kell a hozzáférést a Logic Apps szolgáltatás vagy futtatókörnyezet által használt [bejövő](logic-apps-limits-and-config.md#inbound) és [kimenő](logic-apps-limits-and-config.md#outbound) IP-címekhez abban az Azure-régióban, ahol a logikai alkalmazás létezik. Ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md#managed-api-connectors)is használ, például az Office 365 Outlook Connectort vagy az SQL Connectort, vagy [Egyéni összekötőket](/connectors/custom-connectors/)használ, a tűzfalnak engedélyeznie kell a hozzáférést a logikai alkalmazás Azure-régiójában lévő *összes* [felügyelt összekötő kimenő IP-címéhez](logic-apps-limits-and-config.md#outbound) .

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Az Azure elérése a Visual studióból

1. Nyissa meg a Visual Studio Code-ot. A Visual Studio Code eszköztáron válassza az Azure ikont.

   ![Válassza ki az Azure ikont a Visual Studio Code eszköztáron](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Az Azure ablak **Logic apps** területén válassza a bejelentkezés az **Azure**-ba lehetőséget. Ha a Microsoft bejelentkezési oldala kéri, jelentkezzen be az Azure-fiókjával.

   ![Válassza a "Bejelentkezés az Azure-ba" lehetőséget.](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Ha a bejelentkezés a szokásosnál hosszabb időt vesz igénybe, a Visual Studio Code egy eszköz kódjának megadásával kéri a bejelentkezést a Microsoft hitelesítési webhelyén keresztül. Ha ehelyett a kóddal szeretne bejelentkezni, válassza az **eszköz kódjának használata** lehetőséget.

      ![Folytatás az eszköz kódjával](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. A kód másolásához válassza a **másolás & Megnyitás** elemet.

      ![Az Azure-bejelentkezés kódjának másolása](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Új böngészőablak megnyitásához és a hitelesítési webhely folytatásához válassza a **hivatkozás megnyitása** lehetőséget.

      ![Böngésző megnyitásának megerősítése és a hitelesítési webhely megnyitása](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. A **Bejelentkezés a fiókba** lapon adja meg a hitelesítési kódot, és kattintson a **tovább** gombra.

      ![Adja meg az Azure-bejelentkezéshez használandó hitelesítési kódot](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Válassza ki az Azure-fiókját. A bejelentkezés után lezárhatja a böngészőt, és visszatérhet a Visual Studio Code-ba.

   Az Azure ablaktáblán a **Logic apps** és az **integrációs fiókok** szakaszban a fiókhoz társított Azure-előfizetések jelennek meg. Ha azonban nem látja a várt előfizetéseket, vagy ha a szakaszokban túl sok előfizetés látható, kövesse az alábbi lépéseket:

   1. Vigye az egérmutatót a **Logic apps** címkéjére. Amikor megjelenik az eszköztár, válassza az **előfizetések kiválasztása** (szűrő ikon) lehetőséget.

      ![Azure-előfizetések keresése vagy szűrése](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. A megjelenő listából válassza ki a megjeleníteni kívánt előfizetéseket.

1. A **Logic apps** területen válassza ki a kívánt előfizetést. Az előfizetés csomópontja kibontja és megjeleníti az előfizetésben található összes logikai alkalmazást.

   ![Válassza ki az Azure-előfizetését](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Az **integrációs fiókok** területen az előfizetés kiválasztásával az adott előfizetésben található összes integrációs fiók megjelenik.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Új logikai alkalmazás létrehozása

1. Ha még nem jelentkezett be Azure-fiókjába és-előfizetésbe a Visual Studio code-on belül, kövesse az [előző lépéseket a bejelentkezéshez](#access-azure).

1. A Visual Studio Code-ban **Logic apps** alatt nyissa meg az előfizetés helyi menüjét, és válassza a **logikai alkalmazás létrehozása** lehetőséget.

   ![Az előfizetés menüben válassza a "logikai alkalmazás létrehozása" lehetőséget.](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Megjelenik egy lista, amely megjeleníti az előfizetésben található összes Azure-erőforráscsoportot.

1. Az erőforráscsoport listából válassza az **Új erőforráscsoport létrehozása** vagy egy meglévő erőforráscsoport lehetőséget. Ebben a példában hozzon létre egy új erőforráscsoportot.

   ![Új Azure-erőforráscsoport létrehozása](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Adja meg az Azure-erőforráscsoport nevét, majd nyomja le az ENTER billentyűt.

   ![Adja meg az Azure-erőforráscsoport nevét](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Válassza ki azt az Azure-régiót, ahová el szeretné menteni a logikai alkalmazás metaadatait.

   ![Válassza ki az Azure-helyet a logikai alkalmazás metaadatainak mentéséhez](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Adja meg a logikai alkalmazás nevét, majd nyomja le az ENTER billentyűt.

   ![Adja meg a logikai alkalmazás nevét](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Az Azure-ablakban az Azure-előfizetése alatt megjelenik az új és üres logikai alkalmazás. A Visual Studio Code egy JSON-(.logicapp.js-) fájlt is megnyit, amely tartalmazza a logikai alkalmazás csontváz-munkafolyamatának definícióját. Most már megkezdheti a logikai alkalmazás munkafolyamat-definíciójának manuális készítését ebben a JSON-fájlban. A munkafolyamat-definíciók struktúrájával és szintaxisával kapcsolatos technikai útmutatóért tekintse [meg a Azure Logic apps munkafolyamat-definíciós nyelvi sémáját](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Üres logikai alkalmazás munkafolyamat-definíciós JSON-fájlja](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Itt látható például egy példa logikai alkalmazás munkafolyamat-definíciója, amely egy RSS-triggerrel és egy Office 365 Outlook-művelettel kezdődik. A JSON-elemek általában betűrendben jelennek meg az egyes szakaszokban. A minta azonban ezeket az elemeket nagyjából abban a sorrendben jeleníti meg, ahogy a logikai alkalmazás lépései megjelennek a tervezőben.

   > [!IMPORTANT]
   > Ha újra szeretné használni a minta logikai alkalmazás definícióját, szüksége lesz egy szervezeti fiókra, például: @fabrikam.com . Győződjön meg arról, hogy a fiktív e-mail-címet a saját e-mail-címére cseréli. Egy másik e-mail-összekötő, például a Outlook.com vagy a Gmail használatához cserélje le a `Send_an_email_action` műveletet egy olyan hasonló műveletre, [amely a Azure Logic apps által támogatott e-mail-összekötőn](../connectors/apis-list.md)érhető el.
   >
   > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. 
   > Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). 
   > További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. Ha elkészült, mentse a logikai alkalmazás munkafolyamat-definícióját. (Fájl menü > mentés vagy a CTRL + S billentyűkombináció lenyomása)

1. Ha a rendszer arra kéri, hogy töltse fel a logikai alkalmazást az Azure-előfizetésbe, válassza a **feltöltés** lehetőséget.

   Ez a lépés közzéteszi a logikai alkalmazást a [Azure Portalon](https://portal.azure.com), amely az Azure-ban él és futtatja a logikát.

   ![Új logikai alkalmazás feltöltése az Azure-előfizetésbe](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Logikai alkalmazás megtekintése a Designerben

A Visual Studio Code-ban a logikai alkalmazást csak olvasható tervezési nézetben nyithatja meg. Habár nem szerkesztheti a logikai alkalmazást a tervezőben, a logikai alkalmazás munkafolyamatát vizuálisan is megtekintheti a Tervező nézet használatával.

Az Azure ablak **Logic apps** területén nyissa meg a logikai alkalmazás helyi menüjét, és válassza a **Megnyitás a tervezőben** lehetőséget.

A csak olvasható tervező egy külön ablakban nyílik meg, és megjeleníti a logikai alkalmazás munkafolyamatát, például:

![Logikai alkalmazás megtekintése csak olvasható Designerben](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Megtekintés az Azure Portalon

A logikai alkalmazás Azure Portalban való áttekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) ugyanazzal az Azure-fiókkal és-előfizetéssel, amely a logikai alkalmazáshoz van társítva.

1. A Azure Portal keresési mezőjébe írja be a logikai alkalmazások nevét. Az eredmények listából válassza ki a logikai alkalmazást.

   ![Az új logikai alkalmazás a Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás letiltása vagy engedélyezése

Ha a Visual Studio Code-ban szerkeszt egy közzétett logikai alkalmazást, és menti a módosításokat, *felülírja* a már telepített alkalmazást. A logikai alkalmazás éles környezetben való megszakadásának elkerülése és a fennakadások csökkentése érdekében először inaktiválja a logikai alkalmazást. Ezután újra aktiválhatja a logikai alkalmazást, miután meggyőződött arról, hogy a logikai alkalmazás továbbra is működik.

1. Ha még nem jelentkezett be Azure-fiókjába és-előfizetésbe a Visual Studio code-on belül, kövesse az [előző lépéseket a bejelentkezéshez](#access-azure).

1. Az Azure ablakban **Logic apps** alatt bontsa ki az Azure-előfizetését, hogy megtekintse az adott előfizetéshez tartozó összes logikai alkalmazást.

   1. A használni kívánt logikai alkalmazás letiltásához nyissa meg a logikai alkalmazás menüjét, és válassza a **Letiltás** lehetőséget.

      ![Logikai alkalmazás letiltása](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Ha készen áll a logikai alkalmazás újraaktiválására, nyissa meg a logikai alkalmazás menüjét, és válassza az **Engedélyezés** lehetőséget.

      ![Logikai alkalmazás engedélyezése](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Üzembe helyezett logikai alkalmazás szerkesztése

A Visual Studio Code-ban megnyithatja és szerkesztheti az Azure-ban már üzembe helyezett logikai alkalmazások munkafolyamat-definícióját.

> [!IMPORTANT] 
> Mielőtt egy aktívan futó logikai alkalmazást szerkesszen az éles környezetben, ne a logikai alkalmazás megszakadásának kockázatát, és csökkentse a fennakadást a [logikai alkalmazás letiltásával](#disable-enable-logic-app).

1. Ha még nem jelentkezett be Azure-fiókjába és-előfizetésbe a Visual Studio code-on belül, kövesse az [előző lépéseket a bejelentkezéshez](#access-azure).

1. Az Azure ablakban **Logic apps** alatt bontsa ki az Azure-előfizetését, és válassza ki a kívánt logikai alkalmazást.

1. Nyissa meg a logikai alkalmazás menüjét, és válassza **a Megnyitás a szerkesztőben** lehetőséget. Vagy a logikai alkalmazás neve mellett kattintson a Szerkesztés ikonra.

   ![A meglévő logikai alkalmazás szerkesztőjének megnyitása](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   A Visual Studio Code megnyitja a .logicapp.jsa helyi ideiglenes mappában található fájlon, így megtekintheti a logikai alkalmazás munkafolyamat-definícióját.

   ![Közzétett logikai alkalmazás munkafolyamat-definíciójának megtekintése](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Végezze el a módosításokat a logikai alkalmazás munkafolyamat-definíciójában.

1. Miután végzett, mentse a módosításokat. (Fájl menü > mentés vagy a CTRL + S billentyűkombináció lenyomása)

1. Amikor a rendszer felszólítja a módosítások feltöltésére és a meglévő logikai alkalmazás *felülírására* a Azure Portal, válassza a **feltöltés** lehetőséget.

   Ez a lépés közzéteszi a frissítéseit a logikai alkalmazásban a [Azure Portalban](https://portal.azure.com).

   ![Az Azure-beli Logic app-definíció szerkesztésének feltöltése](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Más verziók megtekintése vagy előléptetése

A Visual Studio Code-ban megnyithatja és áttekintheti a logikai alkalmazás korábbi verzióit. A korábbi verziót is előléptetheti a jelenlegi verzióra.

> [!IMPORTANT] 
> Mielőtt egy aktívan futó logikai alkalmazást éles környezetben módosít, kerülje a logikai alkalmazás megszakításának kockázatát, és csökkentse a megszakadást a [logikai alkalmazás letiltásával](#disable-enable-logic-app).

1. Az Azure ablakban **Logic apps** alatt bontsa ki az Azure-előfizetését, hogy megtekintse az adott előfizetéshez tartozó összes logikai alkalmazást.

1. Az előfizetés alatt bontsa ki a logikai alkalmazást, és bontsa ki a **verziók** csomópontot.

   A **verziók** lista a logikai alkalmazás korábbi verzióit jeleníti meg, ha vannak ilyenek.

   ![A logikai alkalmazás korábbi verziói](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Egy korábbi verzió megtekintéséhez válassza az egyik lépést:

   * A JSON-definíció megtekintéséhez a **verziók** területen válassza ki az adott definíció verziószámát. Vagy nyissa meg a verzió helyi menüjét, és válassza **a Megnyitás a szerkesztőben** lehetőséget.

     Megnyílik egy új fájl a helyi számítógépen, és megjeleníti a verzió JSON-definícióját.

   * Ha a verziót a csak olvasható Tervező nézetben szeretné megtekinteni, nyissa meg a verzió helyi menüjét, és válassza a **Megnyitás a tervezőben** lehetőséget.

1. A korábbi verzióknak az aktuális verzióra való előléptetéséhez kövesse az alábbi lépéseket:

   1. A **verziók** területen nyissa meg a korábbi verzió helyi menüjét, és válassza az **előléptetés** lehetőséget.

      ![Korábbi verzió előléptetése](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Ha azt szeretné, hogy a Visual Studio Code megkérdezze a megerősítést, válassza az **Igen** lehetőséget.

      ![Korábbi verzió előléptetésének megerősítése](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      A Visual Studio Code a kiválasztott verziót a jelenlegi verzióra mozdítja elő, és új számot rendel hozzá a előléptetett verzióhoz. A korábban aktuális verzió már a előléptetett verzió alatt jelenik meg.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Állapot-nyilvántartó és állapot nélküli logikai alkalmazások létrehozása a Visual Studio Code-ban (előzetes verzió)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
