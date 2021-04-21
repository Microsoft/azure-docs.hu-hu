---
title: Feladatok és munkafolyamatok automatizálása a Visual Studio Code-kóddal
description: Logikaialkalmazás-munkafolyamat-definíciók létrehozása vagy szerkesztése a Visual Studio Code (VS Code) használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 03cfb49dabd5039453f84ef0e636d3948af70d8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764836"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Rövid útmutató: Logic Apps-munkafolyamatdefiníciók létrehozása és kezelése a Visual Studio Code használatával

Az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) és az Visual Studio Code használatával olyan logikai alkalmazásokat hozhat létre és kezelhet, amelyek segítségével automatizálhatja az alkalmazások, adatok, rendszerek és szolgáltatások integrálásának feladatait, munkafolyamatait és folyamatait a szervezetek és vállalatok között. Ez a rövid útmutató bemutatja, hogyan hozhatja létre és szerkesztheti az alapul szolgáló munkafolyamat-definíciókat, amelyek JavaScript Object Notation (JSON) használatával, kódalapú felhasználói élményben használják a logikai alkalmazásokat. Az Azure-ban már üzembe helyezett meglévő logikai alkalmazásokkal is dolgozhat.

Bár ugyanezeket a feladatokat a [Azure Portal-ban](https://portal.azure.com) és a Visual Studio-ban is elvégezheti, a Visual Studio Code-ban gyorsabban dolgozhat, ha már ismeri a logikai alkalmazás definícióit, és közvetlenül a kódban szeretne dolgozni. Letilthatja, engedélyezheti, törölheti és frissítheti például a már létrehozott logikai alkalmazásokat. Emellett logikai alkalmazásokkal és integrációs fiókokkal is dolgozhat bármely olyan fejlesztői platformról, ahol Visual Studio Code fut, például Linux, Windows és Mac.

Ebben a cikkben létrehozhatja ugyanazt a [](../logic-apps/quickstart-create-first-logic-app-workflow.md)logikai alkalmazást ebből a rövid útmutatóból, amely az alapfogalmakra összpontosít. Azt is [megtanulhatja,](quickstart-create-logic-apps-with-visual-studio.md)hogyan hozhat létre példaalkalmazást a Visual Studio-ban, és megtanulhatja, hogyan hozhat létre és kezelhet alkalmazásokat az [Azure Command-Line Interface (Azure CLI) használatával.](quickstart-logic-apps-azure-cli.md) A Visual Studio Code-ban a logikai alkalmazás a következő példához hasonlít:

![Példa a logikai alkalmazás munkafolyamat-definícióira](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekikezdene, győződjön meg arról, hogy rendelkezik a következőkkel:

* Ha nem rendelkezik Azure-fiókkal és -előfizetéssel, regisztráljon egy [ingyenes Azure-fiókra.](https://azure.microsoft.com/free/)

* A logikai alkalmazás [munkafolyamat-definícióinak](../logic-apps/logic-apps-workflow-definition-language.md) és szerkezetének alapszintű ismerete a JSON-nal leírt módon

  Ha még csak most kezd el [](../logic-apps/quickstart-create-first-logic-app-workflow.md)Logic Apps, próbálja ki ezt a rövid útmutatót, amely létrehozza az első logikai alkalmazásait a Azure Portal, és jobban összpontosít az alapfogalmakra.

* Hozzáférés az internethez az Azure-ba és az Azure-előfizetésbe való bejelentkezéshez

* Ha még nincsenek telepítve, töltse le és telepítse az alábbi eszközöket:

  * [Visual Studio Code 1.25.1-es](https://code.visualstudio.com/)vagy újabb verziója, amely ingyenes

  * Visual Studio kódbővítmény Azure Logic Apps

    Ezt a bővítményt letöltheti és telepítheti a [Visual Studio Marketplace-ről](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) vagy közvetlenül a Visual Studio Code-ból. A telepítés után győződjön meg arról, hogy Visual Studio code-kódot.

    ![Keresse meg a "Visual Studio Code extension for Azure Logic Apps" Azure Logic Apps](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    A bővítmény megfelelő telepítésének ellenőrzéséhez válassza az Azure ikont, amely megjelenik a Visual Studio Kód eszköztárán.

    ![Bővítmény megfelelő telepítésének ellenőrzése](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    További információ: [Extension Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). A bővítmény nyílt forráskódú verziójában való közreműködéshez látogasson el a [GitHubon Azure Logic Apps Code Visual Studio bővítményét.](https://github.com/Microsoft/vscode-azurelogicapps)

* Ha a logikai alkalmazásnak egy olyan tűzfalon keresztül kell kommunikálnia, amely  adott [](logic-apps-limits-and-config.md#inbound) IP-címekre korlátozza a forgalmat, a tűzfalnak hozzáférést kell engedélyeznie az Logic Apps szolgáltatás vagy a futásidő által használt bejövő és kimenő IP-címek számára is abban az Azure-régióban, ahol a logikai alkalmazás létezik. [](logic-apps-limits-and-config.md#outbound) Ha a logikai [](../connectors/managed.md)alkalmazás felügyelt összekötőket is használ , például az Office 365 Outlook-összekötőt vagy az  SQL-összekötőt, vagy egyéni összekötőket használ, a tűzfalnak a felügyelt összekötő összes kimenő [IP-címének](logic-apps-limits-and-config.md#outbound) hozzáférését is engedélyeznie kell a logikai alkalmazás Azure-régiójában. [](/connectors/custom-connectors/)

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Az Azure elérése Visual Studio

1. Nyissa meg a Visual Studio Code-ot. A Visual Studio kód eszköztárán válassza az Azure ikont.

   ![Válassza az Azure ikont Visual Studio Kód eszköztáron](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. Az Azure ablakában, a **Logic Apps** válassza **a Bejelentkezés az Azure-ba lehetőséget.** Amikor a Microsoft bejelentkezési oldala kéri, jelentkezzen be Azure-fiókjával.

   ![Válassza a "Bejelentkezés az Azure-ba" lehetőséget](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Ha a bejelentkezés a szokásosnál több időt vesz igénybe, a Visual Studio Code arra kéri, hogy jelentkezzen be egy Microsoft-hitelesítési webhelyen keresztül egy eszközkóddal. Ha inkább a kóddal jelentkezik be, válassza az **Eszközkód használata lehetőséget.**

      ![Folytassa inkább az eszközkóddal](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. A kód másoláshoz válassza a **Copy (Másolás) & Open (Megnyitás) lehetőséget.**

      ![Kód másolása az Azure-ba való bejelentkezéshez](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Egy új böngészőablak megnyitásához és a hitelesítési webhelyre való folytatáshoz válassza a **Hivatkozás megnyitása lehetőséget.**

      ![Erősítse meg a böngésző megnyitását és a hitelesítési webhely megnyitását](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. A Bejelentkezés **a fiókba oldalon** adja meg a hitelesítési kódot, majd válassza a Tovább **lehetőséget.**

      ![Adja meg az Azure-ba való bejelentkezéshez szükséges hitelesítési kódot](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Válassza ki Azure-fiókját. Miután bejelentkezik, bezárhatja a böngészőt, és visszatérhet a Visual Studio Code-hoz.

   Az Azure panelen az **Logic Apps** **és** integrációs fiókok szakaszban most a fiókjához társított Azure-előfizetések vannak megmutatja. Ha azonban nem látja a várt előfizetéseket, vagy ha a szakaszok túl sok előfizetést mutatnak, kövesse az alábbi lépéseket:

   1. Helyezze a mutatót a **Logic Apps** címkére. Amikor megjelenik az eszköztár, válassza az **Előfizetések kiválasztása** lehetőséget (szűrő ikon).

      ![Azure-előfizetések megkereshetők vagy szűrhetők](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. A megjelenő listából válassza ki a kívánt előfizetéseket.

1. A **Logic Apps** válassza ki a kívánt előfizetést. Az előfizetés csomópontja kibővul, és megjeleníti az előfizetésben található logikai alkalmazásokat.

   ![Válassza ki az Azure-előfizetését](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Az **Integrációs fiókok alatt** válassza ki az előfizetést, és megjelenik az előfizetésben található integrációs fiókok.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Új logikai alkalmazás létrehozása

1. Ha még nem jelentkezett be Azure-fiókjába és -előfizetéséhez a Visual Studio Code-ban, az előző lépésekkel jelentkezzen [be most.](#access-azure)

1. A Visual Studio Code Logic Apps **alatt** nyissa meg az előfizetés helyi menüjét, és válassza a **Logikai alkalmazás létrehozása lehetőséget.**

   ![Az előfizetés menüjében válassza a "Logikai alkalmazás létrehozása" lehetőséget](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Megjelenik egy lista, amely az előfizetésében található összes Azure-erőforráscsoportot megjeleníti.

1. Az erőforráscsoportok listájában válassza az **Új erőforráscsoport** létrehozása vagy egy meglévő erőforráscsoport lehetőséget. Ebben a példában hozzon létre egy új erőforráscsoportot.

   ![Új Azure-erőforráscsoport létrehozása](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Adja meg az Azure-erőforráscsoport nevét, majd nyomja le az ENTER billentyűt.

   ![Adja meg az Azure-erőforráscsoport nevét](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Válassza ki azt az Azure-régiót, ahová a logikai alkalmazás metaadatait menteni szeretné.

   ![Az Azure-hely kiválasztása a logikai alkalmazás metaadatainak mentéséhez](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Adja meg a logikai alkalmazás nevét, majd nyomja le az Enter billentyűt.

   ![Adja meg a logikai alkalmazás nevét](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   Az Azure-ablakban az Azure-előfizetés alatt megjelenik az új és üres logikai alkalmazás. Visual Studio Code egy JSON- (.logicapp.json) fájlt is megnyit, amely tartalmazza a logikai alkalmazás munkafolyamat-definíciójának vázát. Most már manuálisan is elkezdheti létrehozni a logikai alkalmazás munkafolyamat-definícióját ebben a JSON-fájlban. A munkafolyamat-definíciók szerkezetével és szintaxisával kapcsolatos műszaki útmutatóért lásd a munkafolyamat-definíciós nyelv sémáját [a Azure Logic Apps.](../logic-apps/logic-apps-workflow-definition-language.md)

   ![Üres logikai alkalmazás munkafolyamat-definíciós JSON-fájlja](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Példa egy logikai alkalmazás munkafolyamat-definíciójára, amely egy RSS-eseményindítóval és egy Office 365 Outlook-művelettal kezdődik. A JSON-elemek általában betűrendben jelennek meg az egyes szakaszban. Ez a minta azonban nagyjából abban a sorrendben mutatja be ezeket az elemeket, ahogy a logikai alkalmazás lépései megjelennek a tervezőben.

   > [!IMPORTANT]
   > Ha újra fel szeretné használni ezt a minta logikaialkalmazás-definíciót, szüksége lesz egy szervezeti fiókra, @fabrikam.com például: . Mindenképpen cserélje le a fiktív e-mail-címet a saját e-mail-címére. Ha másik e-mail-összekötőt (például Outlook.com vagy Gmailt) használ, cserélje le a műveletet egy, a által támogatott e-mail-összekötőből elérhető hasonló `Send_an_email_action` [Azure Logic Apps.](../connectors/apis-list.md)
   >
   > Ha a Gmail-összekötőt szeretné használni, csak a G-Suite üzleti fiókok használhatja ezt az összekötőt korlátozás nélkül a logikai alkalmazásokban. 
   > Ha rendelkezik Gmail-felhasználói fiókkal, használhatja ezt az összekötőt csak bizonyos Google által jóváhagyott szolgáltatásokkal, vagy létrehozhat egy Google-ügyfélalkalmazást a Gmail-összekötővel való [hitelesítéshez.](/connectors/gmail/#authentication-and-bring-your-own-application) 
   > További információ: Adatbiztonsági és adatvédelmi szabályzatok [Google-összekötőkhöz a Azure Logic Apps.](../connectors/connectors-google-data-security-privacy-policy.md)

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

1. Ha végzett, mentse a logikai alkalmazás munkafolyamat-definícióját. (A Fájl menüben válassza > Mentés lehetőséget, vagy nyomja le a Ctrl+S billentyűkombinációt)

1. Amikor a rendszer arra kéri, hogy töltse fel a logikai alkalmazást az Azure-előfizetésbe, válassza a **Feltöltés lehetőséget.**

   Ez a lépés közzéteszi a logikai alkalmazást a [Azure Portal,](https://portal.azure.com)amely élővé és az Azure-ban futóvé teszi a logikát.

   ![Új logikai alkalmazás feltöltése az Azure-előfizetésbe](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Logikai alkalmazás megtekintése a tervezőben

A Visual Studio Code-ban megnyithatja a logikai alkalmazást csak olvasható tervezési nézetben. Bár a logikai alkalmazást nem szerkesztheti a tervezőben, vizuálisan is ellenőrizheti a logikai alkalmazás munkafolyamatát a tervező nézetben.

Az Azure ablak Logic Apps **alatt** nyissa meg a logikai alkalmazás helyi menüjét, és válassza a Megnyitás a **tervezőben lehetőséget.**

A csak olvasható tervező egy külön ablakban nyílik meg, és megjeleníti a logikai alkalmazás munkafolyamatát, például:

![Logikai alkalmazás megtekintése a csak olvasható tervezőben](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Megtekintés az Azure Portalon

A logikai alkalmazás áttekintéséhez kövesse Azure Portal következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) a logikai alkalmazáshoz társított Azure-fiókkal és -előfizetéssel.

1. A Azure Portal keresőmezőbe írja be a logikai alkalmazások nevét. Az eredmények listájából válassza ki a logikai alkalmazást.

   ![Az új logikai alkalmazás a Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Logikai alkalmazás letiltása vagy engedélyezése

Ha a Visual Studio Code-ban szerkeszt egy közzétett logikai  alkalmazást, és menti a módosításokat, felülírja a már üzembe helyezett alkalmazást. A logikai alkalmazás éles környezetben való megszakításának és a megszakítás minimalizálásának elkerülése érdekében először inaktiválja a logikai alkalmazást. Ezután reaktívan reagálhat a logikai alkalmazásra, miután megerősítette, hogy a logikai alkalmazás továbbra is működik.

1. Ha még nem jelentkezett be az Azure-fiókjába és -előfizetéséhez a Visual Studio Code-ban, kövesse az előző lépéseket a [bejelentkezéshez.](#access-azure)

1. Az Azure ablakában, **a** Logic Apps bontsa ki az Azure-előfizetését, hogy megtekintse az előfizetésben található összes logikai alkalmazást.

   1. A kívánt logikai alkalmazás letiltásához nyissa meg a logikai alkalmazás menüjét, és válassza a **Letiltás lehetőséget.**

      ![A logikai alkalmazás letiltása](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Amikor készen áll a logikai alkalmazás újraaktiválra, nyissa meg a logikai alkalmazás menüjét, és válassza az **Engedélyezés lehetőséget.**

      ![A logikai alkalmazás engedélyezése](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Üzembe helyezett logikai alkalmazás szerkesztése

A Visual Studio Code-ban megnyithatja és szerkesztheti egy már üzembe helyezett logikai alkalmazás munkafolyamat-definícióját az Azure-ban.

> [!IMPORTANT] 
> Mielőtt szerkeszt egy aktívan futó logikai alkalmazást éles környezetben, kerülje a logikai alkalmazás megszakításának kockázatát, és minimalizálja a megszakítást a logikai alkalmazás [letiltásával.](#disable-enable-logic-app)

1. Ha még nem jelentkezett be az Azure-fiókjába és -előfizetéséhez a Visual Studio Code-ban, kövesse az előző lépéseket a [bejelentkezéshez.](#access-azure)

1. Az Azure ablakában, **a** Logic Apps bontsa ki az Azure-előfizetését, és válassza ki a kívánt logikai alkalmazást.

1. Nyissa meg a logikai alkalmazás menüjét, és válassza a **Megnyitás a szerkesztőben lehetőséget.** Vagy a logikai alkalmazás neve mellett válassza a szerkesztés ikont.

   ![Szerkesztő megnyitása meglévő logikai alkalmazáshoz](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code megnyitja .logicapp.jsfájlt a helyi ideiglenes mappában, így megtekintheti a logikai alkalmazás munkafolyamat-definícióját.

   ![Közzétett logikai alkalmazás munkafolyamat-definíciójának megtekintése](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. A módosításokat a logikai alkalmazás munkafolyamat-definíciójában kell eltenni.

1. Miután végzett, mentse a módosításokat. (A Fájl menüben > Mentés lehetőséget, vagy nyomja le a Ctrl+S billentyűkombinációt)

1. Amikor a rendszer arra kéri,  hogy töltse fel a módosításokat, és írja felül a meglévő logikai alkalmazást a Azure Portal válassza a **Feltöltés lehetőséget.**

   Ez a lépés közzéteszi a frissítéseket a logikai alkalmazásban a [Azure Portal.](https://portal.azure.com)

   ![Szerkesztések feltöltése logikai alkalmazás definícióiba az Azure-ban](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Egyéb verziók megtekintése vagy megléptetve

Az Visual Studio Code-ban megnyithatja és áttekintheti a logikai alkalmazás korábbi verzióit. Korábbi verziót is előléptethet az aktuális verzióra.

> [!IMPORTANT] 
> Mielőtt módosít egy aktívan futó logikai alkalmazást éles környezetben, kerülje a logikai alkalmazás megszakításának kockázatát, és minimalizálja a megszakítást a logikai alkalmazás első [letiltásával.](#disable-enable-logic-app)

1. Az Azure ablak Logic Apps bontsa ki az Azure-előfizetését, hogy megtekintse az előfizetésben található összes logikai alkalmazást.

1. Az előfizetés alatt bontsa ki a logikai alkalmazást, majd bontsa ki **a Verziók et.**

   A **Verziók** lista megjeleníti a logikai alkalmazás korábbi verzióit, ha vannak ilyenek.

   ![A logikai alkalmazás korábbi verziói](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Egy korábbi verzió megtekintéséhez válassza ki a következő két lépést:

   * A JSON-definíció megtekintéséhez a **Verziók** alatt válassza ki a definíció verziószámát. Vagy nyissa meg a verzió helyi menüjét, és válassza a **Megnyitás a szerkesztőben lehetőséget.**

     Megnyílik egy új fájl a helyi számítógépen, amely megjeleníti a verzió JSON-definícióját.

   * A verzió csak olvasható tervezői nézetben való megtekintéséhez nyissa meg a verzió helyi menüjét, és válassza a **Megnyitás a tervezőben lehetőséget.**

1. Ha egy korábbi verziót is előléptet az aktuális verzióra, kövesse az alábbi lépéseket:

   1. A **Verziók alatt** nyissa meg a korábbi verzió helyi menüjét, és válassza az **Előléptetése lehetőséget.**

      ![Korábbi verzió megléptetve](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Ha azt követően folytatja, Visual Studio Code megerősítést kér, válassza az **Igen lehetőséget.**

      ![Erősítse meg a korábbi verzió népszerűsítése](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code előlépteti a kiválasztott verziót az aktuális verzióra, és új számot rendel hozzá az előléptetett verzióhoz. A korábban aktuális verzió megjelenik az előléptetett verzió alatt.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Állapot- és állapot nélküli logikai alkalmazások létrehozása a Visual Studio Kódban (előzetes verzió)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
