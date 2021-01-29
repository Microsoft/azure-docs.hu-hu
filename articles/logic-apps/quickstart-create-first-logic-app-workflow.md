---
title: Rövid útmutató – az első Logic Apps munkafolyamat létrehozása – Azure Portal
description: Hozza létre az első automatizált Logic Apps munkafolyamatát a Azure Portal a rövid útmutató segítségével. Ismerkedjen meg Logic Apps rendszerintegrációs és Enterprise Application Integration (EAI) megoldások alapjaival.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: d90b9e38158d951990fffc21a43317c688da12c9
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052042"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Rövid útmutató: az első Logic Apps munkafolyamat létrehozása – Azure Portal

Ez a rövid útmutató ismerteti, hogyan hozhatja létre az első munkafolyamatát [Azure Logic apps](logic-apps-overview.md) a [Azure Portalon](https://portal.azure.com)keresztül. Ez a bevezető útmutató ismerteti a Logic Apps szolgáltatás alapfogalmait is, beleértve az új logikai alkalmazások létrehozását, az trigger és a művelet hozzáadását a logikai alkalmazáshoz, valamint a logikai alkalmazás tesztelését. Ebben a rövid útmutatóban egy példaként szolgáló logikai alkalmazást hozhat létre, amely rendszeresen ellenőrzi az RSS-hírcsatornát, és e-mailben értesítést küld az új elemekről. Az alábbi képernyőfelvételen a példa logikai alkalmazás magas szintű munkafolyamata látható:

![Képernyőkép a Logic Apps Designerről, amely bemutatja a minta logikai alkalmazást, ahol az trigger egy RSS-hírcsatorna, és a művelet elküld egy e-mailt.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Ha szeretné megismerni, hogyan hozhatja létre és kezelheti az első logikai alkalmazását más felületeken és alkalmazásokon keresztül, tekintse meg a következő Logic Apps rövid útmutatókat: 

* [Logikai alkalmazások létrehozása és kezelése az Azure Command-Line Interface (Azure CLI) használatával](quickstart-logic-apps-azure-cli.md)
* [Logikai alkalmazások létrehozása és kezelése a Visual Studio Code-ban](quickstart-create-logic-apps-visual-studio-code.md)
* [Logikai alkalmazások létrehozása és kezelése a Visual Studióban](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nem rendelkezik ilyennel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Egy Logic Apps által támogatott szolgáltatásból (például Office 365 Outlook vagy Outlook.com) származó e-mail-fiók. Más támogatott e-mail-szolgáltatók esetén [tekintse át az összekötők listáját](/connectors/).

    > [!IMPORTANT]
    > Ha a [Gmail-összekötőt](/connectors/gmail/)használja, vegye figyelembe, hogy csak a G Suite-fiókok használhatják ezt az összekötőt Logic apps korlátozás nélkül. Ha rendelkezik egy fogyasztói Gmail-fiókkal, akkor csak a Google által jóváhagyott szolgáltatásokkal használhatja ezt az összekötőt, kivéve, ha [létrehoz egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel való hitelesítéshez használ](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott IP-címekre korlátozza a forgalmat, *akkor a* tűzfalnak engedélyeznie kell a hozzáférést a Logic Apps szolgáltatás vagy futtatókörnyezet által használt [bejövő](logic-apps-limits-and-config.md#inbound) és [kimenő](logic-apps-limits-and-config.md#outbound) IP-címekhez abban az Azure-régióban, ahol a logikai alkalmazás létezik. Ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md#managed-api-connectors)is használ, például az Office 365 Outlook Connectort vagy az SQL Connectort, vagy [Egyéni összekötőket](/connectors/custom-connectors/)használ, a tűzfalnak engedélyeznie kell a hozzáférést a logikai alkalmazás Azure-régiójában lévő *összes* [felügyelt összekötő kimenő IP-címéhez](logic-apps-limits-and-config.md#outbound) .

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. A Azure Portal keresőmezőbe írja be a kifejezést `logic apps` , majd válassza a **Logic apps** lehetőséget.

   ![Képernyőkép a Azure Portalről, amely a keresési kifejezés és a "Logic Apps" kifejezést jeleníti meg a kiválasztott keresési eredményként.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. A **Logic apps** lapon válassza a **Hozzáadás** lehetőséget.

   ![Képernyőfelvétel: Logic Apps szolgáltatás lapja Azure Portal, a Logic apps-lista és a kiválasztott gomb, "Hozzáadás".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. A **logikai alkalmazás** ablaktáblán adja meg a logikai alkalmazás alapvető adatait és beállításait. Hozzon létre egy új [erőforráscsoportot](../azure-resource-manager/management/overview.md#terminology) a példában szereplő logikai alkalmazáshoz.

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Név** | <*logikai alkalmazás neve*> | A logikai alkalmazás neve, amelynek egyedinek kell lennie a régiók között. A név csak betűket, számokat, kötőjeleket ( `-` ), aláhúzásokat ( `_` ), zárójeleket ( `(` , `)` ) és pontokat ( `.` ) tartalmazhat. Ez a példa a "My-First-Logic-app" kifejezést használja. |
   | **Előfizetés** | <*Azure-előfizetés – név*> | Az Azure-előfizetés neve. |
   | **Erőforráscsoport** | <*Azure-Erőforrás-csoport-név*> | Azon [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md#terminology) neve, amelyben a logikai alkalmazást létrehozza. Az erőforráscsoport nevének egyedinek kell lennie a régiók között. Ez a példa a "My-First-LA-RG" kifejezést használja. |
   | **Hely** | <*Azure-régió*> | Az Azure-régió, ahol a logikai alkalmazás adatait tárolni szeretné. Ez a példa a "West US"-t használja. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózás beállítása, amely alapértelmezés szerint **ki van kapcsolva** . Ebben a példában tartsa meg a **kikapcsolás** beállítást. |
   ||||

   ![Képernyőkép Logic Apps létrehozási oldalról, amely megjeleníti az új logikai alkalmazás részleteit tartalmazó ablaktáblát.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** lehetőséget. Erősítse meg a megadott adatokat, és válassza a **Létrehozás** lehetőséget.

1. Miután az Azure sikeresen üzembe helyezte az alkalmazást, válassza az **Ugrás az erőforráshoz** lehetőséget. Vagy megkeresheti és kiválaszthatja a logikai alkalmazást úgy, hogy beírja a nevet a keresőmezőbe.

   ![Képernyőkép az erőforrás-telepítésről lap kijelölt gombjának "Ugrás erőforrás" gombjára.](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Megnyílik a Logikaialkalmazás-tervező, és egy bemutató videót és a gyakran használt triggereket tartalmazó oldalt jelenít meg. A **Sablonok** szakaszban válassza az **Üres logikai alkalmazás** lehetőséget.

   ![Képernyőkép a Logic Apps Designerről, amely megjeleníti a sablon-gyűjteményt és a kiválasztott sablont, "üres logikai alkalmazás".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Ezután [adjon hozzá egy triggert a logikai alkalmazáshoz](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Az RSS-trigger hozzáadása

Minden logikai alkalmazásnak egy [eseményindítóval](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work)kell kezdődnie, amely egy adott esemény bekövetkezésekor következik be, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor az eseményindító új elemeket talál, a Logic Apps motor létrehoz egy logikai alkalmazás-példányt, amely elindítja és futtatja a munkafolyamatot. Ha az trigger nem talál új elemeket, az trigger nem tűz, és nem hoz létre példányt, vagy nem futtatja a munkafolyamatot.

Ebben a rövid útmutatóban a [logikai alkalmazás létrehozása](#create-your-logic-app)után olyan eseményindítót ad hozzá, amely egy RSS-hírcsatorna új elemeit ellenőrzi, és új elemek megnyitásakor következik be. A logikai alkalmazásokat különböző típusú eseményindítókkal is létrehozhatja, például az [automatikus jóváhagyási alapú munkafolyamatok létrehozásához](tutorial-process-mailing-list-subscriptions-workflow.md)használható oktatóanyagban.

1. A **Logic app Designerben** a keresőmező alatt válassza az **összes** lehetőséget.

1. Az RSS-összekötő megtalálásához a keresőmezőbe írja be a következőt: `rss` . Az **Eseményindítók** listából válassza ki az RSS-eseményindítót **egy hírcsatorna-elem közzétételekor**.

   ![Képernyőfelvétel: Logic Apps Designer "RSS" a keresőmezőbe, valamint a kiválasztott RSS-trigger, "hírcsatorna-elem közzétételekor".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Adja meg az aktiváláshoz tartozó RSS-hírcsatorna URL-címét. Ezután adja meg az trigger ütemtervét az intervallum és a gyakoriság beállításával.

   | Tulajdonság | Érték | Leírás |
   | -------- | ----- | ----------- |
   | **Az RSS-hírcsatorna URL-címe** | <*RSS-hírcsatorna – URL*> | A figyelni kívánt RSS-hírcsatorna URL-címe. Ez a példa a Wall Street Journal RSS-hírcsatornáját használja a következő helyen: `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Ebben a példában azonban bármilyen RSS-hírcsatornát használhat, amely nem igényel HTTP-hitelesítést. Válasszon egy gyakran közzétett RSS-hírcsatornát, hogy később is tesztelje a logikai alkalmazást. |
   | **Intervallum** | 1 | Az RSS-hírcsatornák ellenőrzése közötti várakozási intervallumok száma. Ez a példa 1 percenkénti intervallumokat használ. |
   | **Gyakoriság** | Minute | Az RSS-hírcsatornák közötti időszakban az egyes intervallumok időegysége. Ez a példa 1 percenkénti intervallumokat használ. |
   ||||

   ![Képernyőfelvétel: Logic Apps Designer RSS-triggeri beállításokkal, beleértve az RSS URL-címet, a gyakoriságot és az intervallumot.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Az trigger részleteinek összecsukása a címsoron belülre kattintva.

   ![Képernyőfelvétel: Logic Apps Designer összecsukott logikai alkalmazás alakzatával.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást a tervező eszköztár **Mentés** gombjára kattintva.

A logikai alkalmazás most már él, azonban nem végez mást, mint az RSS-hírcsatornát. Ezután [adjon hozzá egy műveletet](#add-email-action) , amely meghatározza, hogy mi történik az eseményindító tüzeknél.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Az "e-mail küldése" művelet hozzáadása

Miután [hozzáadott egy triggert a logikai alkalmazáshoz](#add-rss-trigger), hozzá kell adnia egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a válasz meghatározásához, amikor a logikai alkalmazás ellenőrzi az RSS-hírcsatornát, és megjelenik egy új elem. A logikai alkalmazásokat sokkal összetettebb műveletekkel is létrehozhatja, mint például az [e-mailek feldolgozásához Logic apps, Azure functions és az Azure Storage](./tutorial-process-email-attachments-workflow.md)használatával.

> [!NOTE]
> Ez a példa az Office 365 Outlookot használja e-mail-szolgáltatásként. Ha más támogatott e-mail szolgáltatást használ a logikai alkalmazásban, a felhasználói felület eltérő lehet. A másik e-mail szolgáltatáshoz való kapcsolódás alapvető fogalmai azonban változatlanok maradnak.

1. A **hírcsatorna-elem közzétételének időpontja** alatt válassza az **új lépés** lehetőséget.

   ![Képernyőkép a Logic Apps Designerről, amely egy, a kiválasztott gomb "új lépés" nevű munkafolyamatát mutatja be.](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. A **művelet kiválasztása** és a keresőmező területen válassza az **összes** lehetőséget.

1. A keresőmezőbe írja be a kifejezést, `send an email` hogy megtalálja a műveletet támogató összekötőket. Ha szűrni szeretné a műveletek listáját egy adott alkalmazásra vagy szolgáltatásra, először kiválaszthatja az alkalmazást vagy a szolgáltatást.

   Ha például Microsoft munkahelyi vagy iskolai fiókot használ, és az Office 365 Outlookot szeretné használni, válassza az **office 365 Outlook** lehetőséget. Ha személyes Microsoft-fiók használ, a Outlook.com is kiválaszthatja. Ez a példa az Office 365 Outlookot folytatja:

   ![Képernyőkép a Logic Apps Designerről, amely a kiválasztott e-mail-összekötő, az "Office 365 Outlook" művelet lépését jeleníti meg.](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Így könnyebben megtalálhatja és kiválaszthatja a használni kívánt műveletet, például `send an email` :

   ![Képernyőkép a Logic Apps Designerről, amely az e-mail összekötő "Office 365 Outlook" nevű szűrt műveleteinek listáját jeleníti meg.](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Ha a kiválasztott e-mail-összekötő felszólítja az identitás hitelesítésére, végezze el ezt a lépést most. A példa működéséhez létre kell hoznia egy kapcsolatot a logikai alkalmazás és az e-mail-szolgáltatás között. 

    > [!NOTE]
    > Ez a példa az Office 365 Outlook Connector manuális hitelesítését mutatja be. Más összekötők azonban különböző hitelesítési típusokat is támogatnak.
    > A logikai alkalmazások hitelesítését a használati esettől függően többféleképpen is kezelheti. Ha például az Azure Resource Manager-sablonok használata az üzembe helyezéshez lehetőséget használja, a parametrizálja a gyakran változó, például a kapcsolódási adatok biztonságát is növelheti. További információt az alábbi témakörökben talál:
   > * [Az üzembe helyezéshez használt sablon paraméterei](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [OAuth-kapcsolatok engedélyezése](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Hozzáférés hitelesítése felügyelt identitásokkal](../logic-apps/create-managed-service-identity.md)
   > * [Kapcsolatok hitelesítése a Logic apps üzembe helyezéséhez](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. Az **E-mail küldése** műveletben adja meg az e-mail-értesítésbe felvenni kívánt adatokat.

   1. A **Címzett** mezőben adja meg a címzett e-mail-címét. Ebben a példában használja az e-mail-címét.

        > [!NOTE]
        > A **dinamikus tartalom hozzáadása** lista akkor jelenik meg, ha a **to** (a to box) elemre kattint, és a Logic apps Designer bizonyos egyéb beviteli mezői is szerepelnek benne. Ez a példa egy későbbi lépésben dinamikus tartalmat használ. A **dinamikus tartalom hozzáadása** lista megjeleníti az előző lépésben elérhető összes kimenetet, amelyet bemenetként használhat az aktuális művelethez.

   1. A **Tárgy** mezőbe írja be az e-mail-értesítés tárgyát. Ebben a példában adja meg a következő szöveget egy záró üres szóközzel: `New RSS item: `

      ![Képernyőkép a Logic Apps Designerről, amely az "e-mailek küldése" műveletet és kurzort jeleníti meg a "tárgy" tulajdonságon belül.](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. A **dinamikus tartalom hozzáadása** listáról válassza ki a **hírcsatorna címe** elemet, amely az triggerből kimenetként jelenik meg a **hírcsatorna közzétételekor**. Az e-mail-értesítés ezt a kimenetet használja az RSS-elemek címének lekéréséhez.

      ![Képernyőkép a Logic Apps Designerről, amely az "e-mail küldése" műveletet és a kurzort a "tulajdonos" tulajdonságban, egy nyílt dinamikus tartalmú listával és a kiválasztott kimenettel, a "hírcsatorna címével" együtt mutatja be.](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Ha a dinamikus tartalom listában nem jelenik meg kimenet a **hírcsatorna-elem közzétételének** időpontjában, akkor a művelet fejléce mellett válassza a **továbbiak** lehetőséget.
      > 
      > ![Képernyőkép a Logic Apps Designerről, amely a megnyitott dinamikus tartalmak listáját és a "továbbiak" elemet jeleníti meg az triggerhez.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Amikor végzett, az e-mail tárgya ehhez a példához hasonlóan néz ki:

      ![Képernyőkép a Logic Apps Designerről, amely az "e-mail küldése" műveletet és egy példaként szolgáló e-mailt tartalmaz a "hírcsatorna címe" tulajdonsággal.](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Ha egy "for each" ciklus megjelenik a tervezőben, akkor egy tömbhöz tartozó jogkivonatot választott ki. például a **Categories-Item** token. Az ilyen tokentípusoknál a tervező automatikusan hozzáadja ezt a hurkot a tokenre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja ugyanazt a műveletet. A hurok eltávolításához válassza a hurok **címsorán lévő három** pontot (**..**.), majd válassza a **Törlés** lehetőséget.

   1. A **törzs** mezőben adja meg az e-mail törzsének tartalmát. Ebben a példában a tartalom három, leíró szöveggel ellátott tulajdonságot tartalmaz: `Title:` , a **hírcsatorna címe** tulajdonságot `Date published:` , a tulajdonságban **közzétett hírcsatornát** , valamint `Link:` az **elsődleges hírcsatorna hivatkozás** tulajdonságát. Ha üres sorokat kíván hozzáadni a szerkesztőmezőkhöz, nyomja le a Shift + Enter billentyűkombinációt.

      | Tulajdonság | Leírás |
      |----------|-------------|
      | **Hírcsatorna címe** | Az elem címe |
      | **Hírcsatorna közzétételének időpontja** | Az elem közzétételének dátuma és ideje |
      | **Elsődleges hírcsatorna-hivatkozás** | Az elem URL-címe |
      |||

      ![Képernyőkép a Logic Apps Designerről, amely az "e-mail küldése" műveletet és a kijelölt tulajdonságokat mutatja a "törzs" mezőben.](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Mentse a logikai alkalmazást. Válassza a tervező menü **Mentés** elemét.

Ezt követően [tesztelje, hogy a logikai alkalmazás működik-e](#test-logic-app).

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A példa logikai alkalmazás létrehozása után ellenőrizze, hogy a munkafolyamat megfelelően van-e konfigurálva. Megvárhatja, hogy a logikai alkalmazás a megadott ütemterv alapján vizsgálja meg az RSS-hírcsatornát. A logikai alkalmazást manuálisan is futtathatja a Logic Apps Designer eszköztáron a **Futtatás** lehetőség kiválasztásával, ahogy az alábbi képernyőképen is látható. 

Ha az RSS-hírcsatornában új elemek vannak, a logikai alkalmazás e-mailt küld minden új elemről. Ellenkező esetben a logikai alkalmazás addig vár, amíg a következő intervallum újra be nem vizsgálja az RSS-hírcsatornát. 

![Képernyőkép a Logic Apps Designerről, amelyen a tervező eszköztáron a Futtatás gomb van kiválasztva.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

A következő képernyőfelvétel egy példaként szolgáló e-mail-értesítést mutat be ebből a logikai alkalmazásból. Az e-mail tartalmazza a tervezőben kiválasztott egyes RSS-hírcsatornák részleteit, valamint az egyes elemeknél hozzáadott leíró szöveget.

![Képernyőkép az Outlookról, amely megjeleníti a minta e-mail-üzeneteit, amikor megjelenik az új RSS-hírcsatorna elem címe, a közzététel dátuma és a hivatkozás.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Ha nem kap értesítő e-maileket a logikai alkalmazástól a várt módon:

* Győződjön meg róla, hogy az e-mail fiókja levélszemét vagy Levélszemét mappában van-e, ha az üzenet helytelenül lett szűrve.
* Győződjön meg arról, hogy az Ön által használt RSS-hírcsatorna a legutóbbi ütemezett vagy kézi ellenőrzés óta közzétett elemeket tartalmaz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a minta logikai alkalmazás tesztelésével, törölje a logikai alkalmazást és az összes kapcsolódó erőforrást az ehhez a példához létrehozott erőforráscsoport törlésével.

> [!NOTE]
> [Logikai alkalmazás törlésekor a](manage-logic-apps-with-azure-portal.md#delete-logic-apps)rendszer nem hoz létre új futtatásokat. A rendszer minden folyamatban lévő és függő futtatást megszakít. Ha több ezer futtatása van, a megszakítás jelentős ideig eltarthat.

1. Az Azure Search mezőbe írja be a kifejezést `resource groups` , majd válassza az **erőforráscsoportok** lehetőséget.

   ![A "erőforráscsoportok" keresési kifejezéssel rendelkező Azure Portal keresőmezőt ábrázoló képernyőkép.](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Keresse meg és válassza ki a logikai alkalmazáshoz tartozó erőforráscsoportot. Az **Áttekintés** panelen válassza az **erőforráscsoport törlése** elemet.

   ![Képernyőfelvétel: Azure Portal a kiválasztott erőforráscsoporthoz és a "erőforráscsoport törlése" gombra kattintva.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Amikor megjelenik a megerősítő ablaktábla, adja meg az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

   ![Képernyőfelvétel: Azure Portal a megerősítő ablaktáblával, és a törlendő erőforráscsoport-név.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta az első logikai alkalmazását a Azure Portalban, amely egy RSS-hírcsatornát keres egy ütemezett frissítésekhez, és e-mailben értesítést küld az egyes új hírcsatorna-elemekről. 

A következő oktatóanyagban megtudhatja, hogyan hozhat létre fejlettebb, ütemezett munkafolyamatokat a Logic Appsban:

> [!div class="nextstepaction"]
> [Forgalom ellenőrzése ütemezésalapú logikai alkalmazás használatával](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)