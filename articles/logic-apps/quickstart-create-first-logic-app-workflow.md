---
title: Rövid útmutató – Az első Logic Apps létrehozása – Azure Portal
description: Az első automatizált Logic Apps munkafolyamat létrehozása a Azure Portal a rövid útmutató segítségével. Ismerje meg a rendszerintegrációs és vállalati alkalmazásintegrációs (EAI) megoldások alapjait a Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: d05566c0734f95e14335c6165de0b2104fa19bc6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764854"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Rövid útmutató: Az első Logic Apps létrehozása – Azure Portal

Ez a rövid útmutató bemutatja, hogyan építhet ki első munkafolyamatát a Azure Logic Apps [a](logic-apps-overview.md) [Azure Portal.](https://portal.azure.com) Ez a bevezető útmutató a Logic Apps szolgáltatás alapfogalmait is ismerteti, beleértve egy új logikai alkalmazás létrehozásához, eseményindító és művelet hozzáadásához, valamint a logikai alkalmazás teszteléséhez. Ebben a rövid útmutatóban egy példa logikai alkalmazást hoz létre, amely rendszeresen ellenőrzi az RSS-hírcsatornát, és e-mailes értesítést küld az új elemekről. Az alábbi képernyőkép a példa logikai alkalmazás magas szintű munkafolyamatát mutatja be:

![Képernyőkép a Logic Apps Designerről, amely egy minta logikai alkalmazást mutat be, ahol az eseményindító egy RSS-hírcsatorna, és a művelet e-mailt küld.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Ha szeretné megtudni, hogyan hozhatja létre és kezelheti az első logikai alkalmazását más felületeken és alkalmazásokon keresztül, tekintse meg az alábbi Logic Apps rövid útmutatókat: 

* [Logikai alkalmazások létrehozása és kezelése az Azure Command-Line Interface (Azure CLI) használatával](quickstart-logic-apps-azure-cli.md)
* [Logikai alkalmazások létrehozása és kezelése a Visual Studio Code-ban](quickstart-create-logic-apps-visual-studio-code.md)
* [Logikai alkalmazások létrehozása és kezelése a Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs fiókja, regisztráljon egy ingyenes [Azure-fiókra.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* E-mail-fiók a Logic Apps által támogatott szolgáltatásból (például Office 365 Outlook vagy Outlook.com). Más támogatott e-mail-szolgáltatók esetén [tekintse át az összekötők listáját.](/connectors/)

    > [!IMPORTANT]
    > Ha a Gmail-összekötőt [használja,](/connectors/gmail/)vegye figyelembe, hogy csak a G Suite-fiókok használhatja ezt az összekötőt korlátozás nélkül a Logic Apps. Ha rendelkezik fogyasztói Gmail-fiókkal, ezt az összekötőt csak bizonyos Google által jóváhagyott szolgáltatásokkal használhatja, kivéve, ha létrehoz egy Google-ügyfélalkalmazást a Gmail-összekötővel [való hitelesítéshez.](/connectors/gmail/#authentication-and-bring-your-own-application) További információ: Adatbiztonsági és adatvédelmi szabályzatok [Google-összekötőkhöz a Azure Logic Apps.](../connectors/connectors-google-data-security-privacy-policy.md)

* Ha a logikai alkalmazásnak egy olyan tűzfalon keresztül kell kommunikálnia, amely  adott [](logic-apps-limits-and-config.md#inbound) IP-címekre korlátozza a forgalmat, a tűzfalnak hozzáférést kell engedélyeznie az Logic Apps szolgáltatás vagy a futásidő által használt bejövő és kimenő IP-címek számára is abban az Azure-régióban, ahol a logikai alkalmazás létezik. [](logic-apps-limits-and-config.md#outbound) Ha a logikai [](../connectors/managed.md)alkalmazás felügyelt összekötőket is használ , például az Office 365 Outlook-összekötőt vagy az  SQL-összekötőt, vagy egyéni összekötőket használ, a tűzfalnak a felügyelt összekötő összes kimenő [IP-címének](logic-apps-limits-and-config.md#outbound) hozzáférését is engedélyeznie kell a logikai alkalmazás Azure-régiójában. [](/connectors/custom-connectors/)

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A Azure Portal keresőmezőbe írja be a következőt: `logic apps` , majd válassza a **Logic Apps.**

   ![Képernyőkép a Azure Portal keresőmezőről, keresési kifejezésként a "logic apps" (logikai alkalmazások) és a "Logic Apps" kifejezéssel a kiválasztott keresési eredményként.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. A **Logic Apps** válassza a Hozzáadás **lehetőséget.**

   ![Képernyőkép a Logic Apps szolgáltatásoldaláról, Azure Portal logikai alkalmazások listáját és a "Hozzáadás" gombot mutatja.](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. A Logikai **alkalmazás panelen** adja meg a logikai alkalmazás alapvető adatait és beállításait. Hozzon létre [egy új erőforráscsoportot](../azure-resource-manager/management/overview.md#terminology) a példaként használt logikai alkalmazáshoz.

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*logic-app-name*> | A logikai alkalmazás neve, amelynek a régiók között egyedinek kell lennie. A név csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket ( ), zárójeleket ( , ) és `-` `_` `(` `)` pontokat ( `.` ) tartalmazhat. Ez a példa a "My-First-Logic-App" alkalmazást használja. |
   | **Előfizetés** | <*Azure-előfizetés neve*> | Az Azure-előfizetés neve. |
   | **Erőforráscsoport** | <*Azure-erőforráscsoport neve*> | Annak az [Azure-erőforráscsoportnak a neve,](../azure-resource-manager/management/overview.md#terminology) amelyben a logikai alkalmazást létrehozza. Az erőforráscsoport nevének régión belül egyedinek kell lennie. Ez a példa a "My-First-LA-RG" rg-t használja. |
   | **Hely** | <*Azure-régió*> | Az Azure-régió, ahol a logikai alkalmazás adatait tárolni kell. Ez a példa az "USA nyugati régiója" régiót használja. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózás beállítása, amely **alapértelmezés** szerint Ki van kapcsolva. Ebben a példában tartsa meg a **Ki** beállítást. |
   ||||

   ![Képernyőkép a Logic Apps oldalról, amely az új logikai alkalmazás részleteit tartalmazó panelt mutatja.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Ha elkészült, válassza az Áttekintés **+ létrehozás lehetőséget.** Erősítse meg a megadott adatokat, majd válassza a **Létrehozás lehetőséget.**

1. Miután az Azure sikeresen üzembe helyezett egy alkalmazást, válassza az **Ugrás az erőforráshoz lehetőséget.** Vagy megkeresheti és kiválaszthatja a logikai alkalmazást a keresőmezőbe begépelt névvel.

   ![Az erőforrás üzembe helyezésének oldalának képernyőképe, az "Erőforrás ugrás" gombjával.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** szakaszban válassza az **Üres logikai alkalmazás** lehetőséget.

   ![Képernyőkép a Logic Apps Designerről, amely a sablongyűjteményt és az "Üres logikai alkalmazás" sablont mutatja.](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Ezután adjon [hozzá egy eseményindítót a logikai alkalmazáshoz.](#add-rss-trigger)

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Az RSS-eseményindító hozzáadása

Minden logikai alkalmazásnak [](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work)egy eseményindítóval kell kezdődnie, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha teljesül egy adott feltétel. Minden alkalommal, amikor az eseményindító új elemeket talál, aktiválódik, és Logic Apps motor létrehoz egy logikaialkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. Ha az eseményindító nem talál új elemeket, az eseményindító nem aktiválódik, és nem hoz létre példányt, és nem futtatja a munkafolyamatot ezen az ellenőrzésen.

Ebben [a](#create-your-logic-app)rövid útmutatóban a logikai alkalmazás létrehozása után hozzáad egy eseményindítót, amely új elemeket keres egy RSS-hírcsatornában, és új elemek létrehozásakor aktiválódik. Logikai alkalmazásokat is létrehozhat különböző típusú eseményindítókhoz, például az automatikus jóváhagyási alapú munkafolyamatok létrehozását ismertető [oktatóanyagban.](tutorial-process-mailing-list-subscriptions-workflow.md)

1. A **Logikaialkalmazás-tervezőben** a keresőmező alatt válassza az Összes **lehetőséget.**

1. Az RSS-összekötő megkereséhez írja be a keresőmezőbe a következőt: `rss` . Az **Eseményindítók listából** válassza ki a Hírcsatornaelem közzétételekor **RSS-eseményindítót.**

   ![Képernyőkép az Logic Apps Designerről, amely a keresőmezőben az "rss" elemet és a kiválasztott RSS-eseményindítót (A hírcsatornaelem közzétételekor) mutatja.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Adja meg az eseményindító RSS-hírcsatorna URL-címét. Ezután határozza meg az eseményindító ütemezését az időköz és a gyakoriság beállításával.

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Az RSS-hírcsatorna URL-címe** | <*RSS-hírcsatorna URL-címe*> | A figyelni kívánt RSS-hírcsatorna URL-címe. Ez a példa a Wall Street Journal RSS-hírcsatornáját használja a következő oldalon: `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Ebben a példában azonban bármilyen OLYAN RSS-hírcsatornát használhat, amely nem igényel HTTP-hitelesítést. Olyan RSS-hírcsatornát válasszon, amely gyakran tesz közzé tartalmakat, hogy később könnyen tesztelni tudja a logikai alkalmazást. |
   | **Intervallum** | 1 | Az RSS-hírcsatorna ellenőrzései közötti várakozási időközök száma. Ez a példa 1 perces időközt használ. |
   | **Gyakoriság** | Minute | Az RSS-hírcsatorna ellenőrzései közötti intervallumok időegysége. Ez a példa 1 perces időközt használ. |
   ||||

   ![Képernyőkép a Logic Apps a Tervezőről, az RSS-eseményindító beállításaival, beleértve az RSS URL-címét, gyakoriságát és időközét.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Az eseményindító részleteinek összecsukása a címsorán belülre kattintva.

   ![A tervező Logic Apps összecsukott logikaialkalmazás-alakzatot bemutató képernyőkép.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást a tervező **eszköztár Mentés** gombjára kattintva.

A logikai alkalmazás most már élő, de csak az RSS-hírcsatornát ellenőrzi. Ezután adjon [hozzá egy műveletet,](#add-email-action) amely meghatározza, hogy mi történik, ha az eseményindító aktiválódik.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Az "e-mail küldése" művelet hozzáadása

Miután [hozzáadta a](#add-rss-trigger)eseményindítót a logikai [](../logic-apps/logic-apps-overview.md#logic-app-concepts) alkalmazáshoz, hozzá kell adni egy műveletet, amely meghatározza a választ, amikor a logikai alkalmazás ellenőrzi az RSS-hírcsatornát, és megjelenik egy új elem. Sokkal összetettebb műveletekkel is létrehozhat logikai alkalmazásokat, például az e-mailek feldolgozásával kapcsolatos oktatóanyagban [Logic Apps, Azure Functions és az Azure Storage használatával.](./tutorial-process-email-attachments-workflow.md)

> [!NOTE]
> Ez a példa az Office 365 Outlookot használja e-mail-szolgáltatásként. Ha egy másik támogatott e-mail-szolgáltatást használ a logikai alkalmazásban, a felhasználói felület eltérő lehet. A másik e-mail-szolgáltatáshoz való csatlakozás alapvető fogalmai azonban változatlanok maradnak.

1. A **Hírcsatornaelem közzétételekor eseményindító alatt** válassza az **Új lépés lehetőséget.**

   ![Képernyőkép a Logic Apps Designerről, az "Új lépés" gombra kattintva.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. A **Művelet kiválasztása és a** keresőmező alatt válassza az Összes **lehetőséget.**

1. A keresőmezőbe írja be a következőt: , hogy megtalálja a műveletet `send an email` ajánló összekötőket. A műveletek listájának egy adott alkalmazásra vagy szolgáltatásra való szűréséhez először kiválaszthatja az adott alkalmazást vagy szolgáltatást.

   Ha például munkahelyi vagy iskolai Microsoft-fiókot használ, és az Office 365 Outlookot szeretné használni, válassza az **Office 365 Outlook lehetőséget.** Ha személyes adatokat használ, Microsoft-fiók a Outlook.com. Ez a példa az Office 365 Outlookkal folytatódik:

   ![Képernyőkép a Logic Apps Designerről, amely a művelet lépését mutatja a kiválasztott "Office 365 Outlook" e-mail-összekötővel.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Mostantól könnyebben megkeresheti és kiválaszthatja a használni kívánt műveletet, `send an email` például:

   ![A Logic Apps Designer képernyőképe, az "Office 365 Outlook" e-mail-összekötő szűrt műveletekkel.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Ha a kiválasztott e-mail-összekötő az identitása hitelesítésére kéri, most ezt a lépést kell végrehajtania. Ahhoz, hogy ez a példa működjön, kapcsolatot kell létrehoznia a logikai alkalmazás és az e-mail szolgáltatás között. 

    > [!NOTE]
    > Ez a példa az Office 365 Outlook-összekötő manuális hitelesítését mutatja be. Más összekötők azonban különböző hitelesítési típusokat támogatnak.
    > A logikai alkalmazások hitelesítését a saját használatától függően különböző módokon is kezelheti. Ha például sablonokat Azure Resource Manager az üzembe helyezéshez, paraméterekkel javíthatja a gyakran megváltozó bemenetek biztonságát, például a kapcsolati adatokat. További információt az alábbi témakörökben talál:
   > * [Sablonparaméterek az üzembe helyezéshez](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)
   > * [Kapcsolatok hitelesítése a logikai alkalmazások üzembe helyezéséhez](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Az **E-mail küldése műveletben** adja meg az e-mail-értesítésbe foglalni kívánt adatokat.

   1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Ebben a példában használja az e-mail-címét.

        > [!NOTE]
        > A **Dinamikus tartalom hozzáadása lista** akkor jelenik meg, ha a **To** (To) mezőre kattint, és a Tervezőben megjelennek a Logic Apps beviteli mezői. Ez a példa egy későbbi lépésben dinamikus tartalmat használ. A **Dinamikus tartalom hozzáadása lista** megjeleníti az előző lépésben elérhető kimeneteket, amelyeket bemenetként használhat az aktuális művelethez.

   1. A Tárgy **mezőbe** írja be az e-mail-értesítés tárgyát. Ebben a példában írja be a következő szöveget záró üres szóközt: `New RSS item: `

      ![Képernyőkép a Logic Apps Designerről, amely az "E-mail küldése" műveletet és a kurzort mutatja a "Tárgy" tulajdonságmezőben.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. A Dinamikus **tartalom hozzáadása listában** válassza a **Hírcsatorna címe** lehetőséget, amely az eseményindító kimenete, a Hírcsatornaelem **közzétételekor.** Az e-mail-értesítés ezt a kimenetet használja az RSS-elem címének leküldésére.

      ![A Logic Apps Designer képernyőképe, amely az "E-mail küldése" műveletet mutatja, és a kurzort a "Tárgy" tulajdonságmezőn belül mutatja egy megnyitott dinamikus tartalomlistával és a kiválasztott kimenettel( "Hírcsatorna címe".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Ha a dinamikus tartalmak listájában nem jelennek meg kimenetek a Hírcsatornaelem közzétételekor eseményindítóból, a művelet fejléce mellett válassza **a** További **információ lehetőséget.**
      > 
      > ![Képernyőkép a Logic Apps Designerről, amely egy megnyitott dinamikus tartalomlistát és az eseményindítóhoz kiválasztott "Több megjelenítése" listában látható.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Amikor végzett, az e-mail tárgya ehhez a példához hasonlóan néz ki:

      ![A Logic Apps Designer képernyőképe, amely az "E-mail küldése" műveletet és egy e-mail-tárgyra mutat egy példát, a benne foglalt "Hírcsatorna címe" tulajdonsággal.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Ha egy "For each" hurok jelenik meg a tervezőben, akkor kiválasztott egy tokent egy tömbhöz; például a **categories-Item** token. Az ilyen tokentípusoknál a tervező automatikusan hozzáadja ezt a hurkot a tokenre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja ugyanazt a műveletet. A hurok eltávolításához kattintson a hurok címsorán található három **pontra** (**...**), majd válassza a **Törlés lehetőséget.**

   1. A Törzs **mezőbe írja** be az e-mail törzsének tartalmát. Ebben a példában a tartalom három tulajdonságot tartalmaz leíró szöveggel mindegyikhez: , a hírcsatorna `Title:` **címtulajdonság;** , a Tulajdonságon közzétett Hírcsatorna; és , az Elsődleges hírcsatorna hivatkozás `Date published:`  `Link:` tulajdonsága.  Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Hírcsatorna címe** | Az elem címe |
      | **Hírcsatorna közzétételének időpontja** | Az elem közzétételének dátuma és ideje |
      | **Elsődleges hírcsatorna-hivatkozás** | Az elem URL-címe |
      |||

      ![Képernyőkép a Logic Apps Designerről, amely az "E-mail küldése" műveletet és a "Törzs" mezőben kiválasztott tulajdonságokat mutatja.](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Mentse a logikai alkalmazást. A **tervezőmenüben** válassza a Mentés lehetőséget.

Ezután [tesztelje, hogy működik-e a logikai alkalmazás.](#test-logic-app)

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A példa logikai alkalmazás létrehozása után ellenőrizze, hogy a munkafolyamat megfelelően van-e konfigurálva. Megvárhatja, amíg a logikai alkalmazás a megadott ütemezés alapján ellenőrzi az RSS-hírcsatornát. A logikai alkalmazást manuálisan is  futtathatja a Logic Apps Designer eszköztár Futtatás lehetőségével, ahogyan az alábbi képernyőképen látható. 

Ha az RSS-hírcsatornában új elemek vannak, a logikai alkalmazás e-mailt küld minden új elemről. Ellenkező esetben a logikai alkalmazás megvárja a következő időközt, hogy ismét ellenőrizze az RSS-hírcsatornát. 

![Képernyőkép a Logic Apps Designerről, amely a tervező eszköztárán kiválasztott "Futtatás" gombot mutatja.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Az alábbi képernyőképen egy minta e-mail-értesítés látható a példa logikai alkalmazásból. Az e-mail tartalmazza a tervezőben kiválasztott RSS-hírcsatorna egyes elemének részleteit, valamint az egyes elemekhez hozzáadott leíró szöveget.

![Képernyőkép az Outlookról, amely az új RSS-hírcsatornaelem megjelenésekor kapott e-mail-üzenetet mutatja az elem címével, közzétételi dátumával és hivatkozásával.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Ha nem a várt módon fogad értesítő e-maileket a logikai alkalmazástól:

* Ellenőrizze az e-mail-fiók levélszemét- vagy levélszemét-mappáját arra az esetre, ha az üzenet helytelenül lett szűrve.
* Győződjön meg arról, hogy a használt RSS-hírcsatorna közzétett elemeket tett közzé az utolsó ütemezett vagy manuális ellenőrzés óta.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a minta logikai alkalmazás tesztelésével, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat a példához létrehozott erőforráscsoport törlésével.

> [!NOTE]
> Logikai alkalmazás [törlésekor a](manage-logic-apps-with-azure-portal.md#delete-logic-apps)rendszer nem példányosul új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

1. Az Azure keresőmezőbe írja be a következőt: `resource groups` , majd válassza az **Erőforráscsoportok lehetőséget.**

   ![Képernyőkép a Azure Portal keresőmezőről az "erőforráscsoportok" keresőkifejezéssel.](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Keresse meg és válassza ki a logikai alkalmazás erőforráscsoportját. Az Áttekintés **panelen** válassza az **Erőforráscsoport törlése lehetőséget.**

   ![Képernyőkép a Azure Portal erőforráscsoportról és az "Erőforráscsoport törlése" gombról.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Amikor megjelenik a megerősítési panel, adja meg az erőforráscsoport nevét, és válassza a **Törlés lehetőséget.**

   ![Képernyőkép a Azure Portal megerősítő panelről, és a törölni kívánt erőforráscsoport nevének megadva.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta első logikai alkalmazását a Azure Portal-ban, hogy ütemezett frissítéseket keressen egy RSS-hírcsatornában, és e-mailes értesítést küldjön minden új hírcsatornaelemről. 

A következő oktatóanyagból megtudhatja, hogyan hozhat létre speciálisabb ütemezésalapú munkafolyamatokat a Logic Apps-ban:

> [!div class="nextstepaction"]
> [Forgalom ellenőrzése ütemezésalapú logikai alkalmazás használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)