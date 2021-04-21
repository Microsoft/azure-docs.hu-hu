---
title: Jóváhagyási alapú automatizált munkafolyamatok létrehozása
description: Oktatóanyag – Jóváhagyási alapú automatizált munkafolyamat létrehozása, amely levelezőlista-feliratkozásokat dolgozza fel a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777274"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Oktatóanyag: Automatizált jóváhagyási alapú munkafolyamatok létrehozása a Azure Logic Apps

Ez az oktatóanyag bemutatja, hogyan építhet ki egy példa [logikai alkalmazást,](../logic-apps/logic-apps-overview.md) amely automatizál egy jóváhagyásalapú munkafolyamatot. Ez a példa logikai alkalmazás a [MailChimp](https://mailchimp.com/) szolgáltatás által kezelt levelezőlistára vonatkozó előfizetési kérelmeket dolgozza fel. Ez a logikai alkalmazás különböző lépéseket tartalmaz, amelyek a kérések e-mail-fiókjának monitorozásával kezdődnek, elküldi ezeket a kérelmeket jóváhagyásra, ellenőrzik, hogy a kérelem megkapja-e a jóváhagyást, hozzáadja a jóváhagyott tagokat a levelezőlistához, és ellenőrzi, hogy új tagokat adnak-e hozzá a listához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Üres logikai alkalmazás létrehozása.
> * Trigger hozzáadása az e-mailes feliratkozási kérelmek monitorozására.
> * Művelet hozzáadása, amely elküldi az e-mailes kérelmeket jóváhagyásra vagy elutasításra.
> * Feltétel hozzáadása, amely ellenőrzi a jóváhagyási válaszokat.
> * Művelet hozzáadása, amely hozzáadja a jóváhagyott tagokat a levelezőlistához.
> * Feltétel hozzáadása, amely ellenőrzi, hogy a tagok sikeresen csatlakoztak-e a listához.
> * Művelet hozzáadása, amely megerősítő e-maileket küld arról, hogy az új tagok sikeresen csatlakoztak-e a listához.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Magas szintű befejezett logikai alkalmazások áttekintése](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Egy MailChimp-fiók, amelyben korábban létrehozott egy "test-members-ML" nevű listát, ahol a logikai alkalmazás hozzáadhat e-mail-címeket a jóváhagyott tagok számára. Ha nincs [fiókja,](https://login.mailchimp.com/signup/)regisztráljon egy ingyenes fiókra, majd ismerje meg, hogyan hozhat létre [MailChimp-listát.](https://us17.admin.mailchimp.com/lists/#)

* E-mail-fiók egy olyan e-mail-szolgáltatótól, amelyet támogatnak a Logic Apps, például az Office 365 Outlook, a Outlook.com vagy a Gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). Ez a rövid útmutató az Office 365 Outlookot használja munkahelyi vagy iskolai fiókkal. Ha másik e-mail-fiókot használ, az általános lépések ugyanazok maradnak, de a felhasználói felület kissé eltérő lehet.

* Egy e-mail-fiók az Office 365 Outlookban vagy Outlook.com, amely támogatja a jóváhagyási munkafolyamatokat. Ebben az oktatóanyagban az Office 365 Outlookot használjuk. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott  IP-címekre [](logic-apps-limits-and-config.md#outbound) korlátozza a forgalmat, a tűzfalnak engedélyeznie kell a hozzáférést az Logic Apps szolgáltatás vagy a futásidő által használt bejövő és kimenő IP-címek számára is abban az Azure-régióban, ahol a logikai alkalmazás létezik. [](logic-apps-limits-and-config.md#inbound) Ha a logikai [](../connectors/managed.md)alkalmazás felügyelt összekötőket is használ , például az Office 365 Outlook-összekötőt vagy az  SQL-összekötőt, vagy egyéni összekötőket használ, a tűzfalnak a felügyelt összekötő összes kimenő [IP-címének](logic-apps-limits-and-config.md#outbound) hozzáférését is engedélyeznie kell a logikai alkalmazás Azure-régiójában. [](/connectors/custom-connectors/)

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival. Az Azure kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**

1. A Azure Marketplace válassza az **Integrációs**  >  **logikai alkalmazás lehetőséget.**

   ![Képernyőkép a Azure Marketplace az "Integráció" és a "Logikai alkalmazás" beállítással.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. A **Logikai alkalmazás panelen** adja meg az itt leírt adatokat a létrehozni kívánt logikai alkalmazásról.

   ![Képernyőkép a logikai alkalmazás létrehozási panelről, valamint az új logikai alkalmazáshoz szükséges információkról.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés neve*> | Az Azure-előfizetés neve. Ez a példa a következőt használja: `Pay-As-You-Go`. |
   | **Erőforráscsoport** | LA-MailingList-RG | Az [Azure-erőforráscsoport neve,](../azure-resource-manager/management/overview.md)amely a kapcsolódó erőforrások rendszerezését jelenti. Ez a példa egy nevű új erőforráscsoportot hoz `LA-MailingList-RG` létre. |
   | **Név** | LA-MailingList | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket ( ), zárójeleket ( , ) és `-` `_` `(` `)` pontokat ( ) `.` tartalmazhat. Ez a példa a következőt használja: `LA-MailingList`. |
   | **Hely** | USA nyugati régiója | Az a régió, ahol a logikai alkalmazás adatait tárolni kell. Ez a példa a következőt használja: `West US`. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Ha végzett, válassza a Felülvizsgálat **+ létrehozás lehetőséget.** Miután az Azure érvényesíti a logikai alkalmazás adatait, válassza a **Létrehozás lehetőséget.**

1. Miután az Azure üzembe helyezett egy alkalmazást, válassza az **Ugrás az erőforráshoz lehetőséget.**

   Az Azure megnyitja Logic Apps sablonválasztási panelt, amely egy bevezető videót, a gyakran használt eseményindítókat és logikaialkalmazás-sablonmintákat mutat be.

1. Görgessen le a videó és a gyakori eseményindítók szakasz után a **Sablonok szakaszhoz,** és válassza az **Üres logikai alkalmazás lehetőséget.**

   ![Képernyőkép a Sablon Logic Apps panelről, az "Üres logikai alkalmazás" kijelöléssel.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Ezután adjon hozzá egy [Outlook-eseményindítót,](../logic-apps/logic-apps-overview.md#logic-app-concepts) amely az előfizetési kérésekkel beérkező e-maileket figyeli. Minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha egy új adat megfelel egy adott feltételnek. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Az e-mailek monitorozására szolgáló trigger hozzáadása

1. A Logic Apps Designer keresőmezőbe írja be a következőt: , majd válassza ki az Új e-mail `when email arrives` **érkezésekor nevű eseményindítót.**

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget.
   * Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget.

   Ez a példa az Office 365 Outlook kiválasztásával folytatódik.

   ![Képernyőkép a Logic Apps Designer keresőmezőről, amely az "e-mail érkezésekor" keresőkifejezést tartalmazza, és ki van jelölve az "Új e-mail érkezésekor" eseményindító.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Ha még nem rendelkezik kapcsolattal, jelentkezzen be és hitelesítse az e-mail-fiókjához való hozzáférést, amikor a rendszer erre kéri.

   Azure Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

1. Az eseményindítóban adja meg az új e-mailek ellenőrzésére vonatkozó feltételeket.

   1. Adja meg az e-mailek ellenőrzésére vonatkozó mappát, a többi tulajdonságot pedig tartsa meg az alapértelmezett értéken.

      ![Képernyőkép a tervezőről az "Új e-mail érkezésekor" művelet és a "Mappa" beállítás "Beérkezett üzenetek" beállításával.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Adja hozzá az eseményindító **Tárgyszűrő tulajdonságát,** hogy a tárgysor alapján szűrni tudja az e-maileket. Nyissa meg **az Add new parameter (Új paraméter hozzáadása) listát,** és válassza a Subject Filter **(Tárgyszűrő) lehetőséget.**

      ![A megnyitott "Új paraméter hozzáadása" listát a "Tárgyszűrő" beállítással bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Az eseményindító tulajdonságaival kapcsolatos további információkért tekintse meg az [Office 365 Outlook](/connectors/office365/) összekötő-referenciáját vagy a [Outlook.com összekötőre vonatkozó referenciát.](/connectors/outlook/)

   1. Miután a tulajdonság megjelenik az eseményindítóban, írja be a következő szöveget: `subscribe-test-members-ML`

      ![Képernyőkép a "Tárgyszűrő" tulajdonságról a "subscribe-test-members-ML" szöveg beírt szöveggel.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Az eseményindító részleteinek elrejtéshez csukja össze az alakzatot az alakzat címsorán belülre kattintva.

   ![Képernyőkép az összecsukott eseményindító alakzatról.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a Mentés **lehetőséget.**

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres a bejövő e-mailek között. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="send-approval-email"></a>Jóváhagyó e-mail küldése

Most, hogy van egy triggere, adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely elküld egy e-mailt a kérelem jóváhagyásához vagy elutasításához.

1. A Logic Apps Designerben, **az Új e-mail** érkezésekor eseményindító alatt válassza az Új **lépés lehetőséget.**

1. A **Művelet kiválasztása alatt a** keresőmezőbe írja be a következőt: , majd válassza a `send approval` Jóváhagyó e-mail küldése nevű **műveletet.**

   ![Képernyőkép a "Jóváhagyási műveletek" alapján szűrt "Művelet kiválasztása" listáról és a kijelölt "Jóváhagyó e-mail küldése" műveletről.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Most adja meg az itt bemutatott és leírt tulajdonságok értékeit. Az összes többit az alapértelmezett értéken hagyja. További információt ezekről a tulajdonságokról az [Office 365 Outlook-összekötők referenciájában](/connectors/office365/) vagy a [Outlook.com-összekötőkre vonatkozó Outlook.com tartalmaz.](/connectors/outlook/)

   ![A "Jóváhagyó e-mail küldése" tulajdonságokat bemutató képernyőkép](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Ide:** | <*jóváhagyási e-mail-cím*> | A jóváhagyó e-mail-címe. Tesztelési célokra használhatja a saját címét. Ez a példa a fiktív `sophiaowen@fabrikam.com` e-mail-címet használja. |
   | **Tárgy** | `Approve member request for test-members-ML` | Egy leíró e-mail-tárgy |
   | **Felhasználói választási lehetőségek** | `Approve, Reject` | Győződjön meg arról, hogy ez a tulajdonság határozza meg azokat a válaszbeállításokat, amelyeket a jóváhagyó kiválaszthat( Alapértelmezés szerint Jóváhagyás **vagy** **Elutasítás).** |
   ||||

   > [!NOTE]
   > Amikor rákattint egy szerkesztőmezőbe, megjelenik a dinamikus tartalmak listája, amelyet most figyelmen kívül hagyhat. Ez a lista a korábbi műveletek kimenetét jeleníti meg, amelyek kiválaszthatóak bemenetként a munkafolyamat későbbi műveleteibe.
 
1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amely ellenőrzi a jóváhagyó kiválasztott válaszát.

## <a name="check-approval-response"></a>Jóváhagyási válasz ellenőrzése

1. A Jóváhagyás küldése **e-mailben művelet** alatt válassza az **Új lépés lehetőséget.**

1. A **Művelet kiválasztása alatt válassza** a Beépített **lehetőséget.** A keresőmezőbe írja be a következőt: `condition` , majd válassza ki a Condition nevű **műveletet.**

   ![Képernyőkép a "Művelet kiválasztása" keresőmezőről, amely a "Beépített" és a "feltétel" kifejezést jeleníti meg keresési kifejezésként, miközben a "Feltétel" művelet van kiválasztva.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. A **Feltétel címsorában** válassza  a három pont **(...**) gombot, majd az **Átnevezés lehetőséget.** Nevezze át a feltételt a következő leírásra: `If request approved`

   ![Képernyőkép a kiválasztott három pontról, megnyitott "Beállítások" listával és a kijelölt "Átnevezés" paranccsal.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Állítson össze egy feltételt, amely ellenőrzi, hogy a jóváhagyó kiválasztotta-e a **Jóváhagyást.**

   1. A feltétel bal oldalán kattintson a Choose **a value (Érték kiválasztása) mezőre.**

   1. A megjelenő dinamikus tartalmak listájában, a **Jóváhagyó e-mail küldése alatt** válassza a **SelectedOption tulajdonságot.**

      ![Képernyőkép a dinamikus tartalmak listájáról, ahol a "Jóváhagyó e-mail küldése" szakaszban a "SelectedOption" kimenet van kiválasztva.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. A középső összehasonlító mezőben válassza az **is equal to operátort.**

   1. A feltétel jobb oldalán, az Érték kiválasztása mezőben adja meg **a** következő szöveget: `Approve` .

      Ha végzett, a feltétel az alábbi példához hasonlít:

      ![Képernyőkép a jóváhagyott kérelem példához kész feltételről](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Mentse a logikai alkalmazást.

Ezután adja meg a műveletet, amelyet a logikai alkalmazás végrehajt, amikor a felülvizsgáló jóváhagyja a kérést. 

## <a name="add-member-to-mailchimp-list"></a>Tag hozzáadása MailChimp-listához

Most adjon hozzá egy műveletet, amely hozzáadja a jóváhagyott tag a levelezőlistához.

1. A feltétel Igaz **ágában** válassza a **Művelet hozzáadása lehetőséget.**

1. A Művelet **kiválasztása keresőmező alatt** válassza az Összes **lehetőséget.** A keresőmezőbe írja be a következőt: , majd válassza a Tag hozzáadása `mailchimp` **listához nevű műveletet.**

   ![Képernyőkép a "Művelet kiválasztása" mezőről, a "mailchimp" keresőkifejezéssel és a kiválasztott "Tag hozzáadása listához" művelettel.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Ha még nem rendelkezik kapcsolattal a MailChimp-fiókjához, a rendszer felkéri a bejelentkezésre.

1. A Tag **hozzáadása listához műveletben** adja meg az itt ismertetett információkat:

   ![A "Tag hozzáadása listához" művelet információit bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Listaazonosító** | Yes | <*mailing-list-name*> | Válassza ki a MailChimp-levelezőlista nevét. Ez a példa a következőt használja: `test-members-ML`. |
   | **E-mail-cím** | Yes | <*új-tag-e-mail-cím*> | A megnyíló dinamikus tartalmú listában az Új e-mail érkezésekor szakaszban válassza **a** From lehetőséget, amely az eseményindító kimenete, és megadja az új tag e-mail-címét.  |
   | **Állapot** | Yes | <*member-subscription-status*> | Válassza ki az új taghoz beállítani kívánt előfizetési állapotot. Ez a példa a következőt `subscribed` választja: . <p>További információ: [Feliratkozók kezelése a MailChimp API-val](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   A Tag hozzáadása  listához művelettulajdonságokkal kapcsolatos további információkért tekintse meg a [MailChimp-összekötő referenciáját.](/connectors/mailchimp/)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amellyel ellenőrizheti, hogy az új tag sikeresen csatlakozott-e a levelezőlistájához. Így a logikai alkalmazás értesítést kaphat arról, hogy a művelet sikeres vagy sikertelen volt-e.

## <a name="check-for-success-or-failure"></a>Siker vagy sikertelenség ellenőrzése

1. A True **(Igaz)** ág Add member to list (Tag hozzáadása **listához) művelete** alatt válassza **a Művelet hozzáadása lehetőséget.**

1. A **Művelet kiválasztása alatt válassza a** Beépített **lehetőséget.** A keresőmezőbe írja be a következőt: `condition` , majd válassza ki a Condition nevű **műveletet.**

1. Nevezze át a feltételt a következő leírásra: `If add member succeeded`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához:

   1. A feltétel bal oldalán kattintson a Choose **a value (Érték kiválasztása) mezőre.** A megjelenő dinamikus tartalmú listából a Tag hozzáadása **listához** szakaszban válassza az **Állapot tulajdonságot.**

      A feltétel például az alábbi példához hasonló:

      ![Képernyőkép a feltétel bal oldalán található "Érték kiválasztása" mezőről az "Állapot" megadva.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. A középső összehasonlító mezőben válassza az **egyenlő operátort.**

   1. A feltétel jobb oldalán, az Érték kiválasztása mezőben írja be **a** következő szöveget: `subscribed`

      Ha végzett, a feltétel a következő példához hasonlít:

      ![Képernyőkép a sikeres vagy sikertelen előfizetés ellenőrzéséhez szükséges kész feltételről.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ezután állítsa be az e-maileket, amelyek akkor küldenek, ha a jóváhagyott tag sikeresen vagy sikertelenül csatlakozik a levelezőlistához.

## <a name="send-email-if-member-added"></a>E-mail küldése tag hozzáadásakor

1. Az **If add member succeeded (Sikeres** tag hozzáadása) feltétel true **ágában** válassza a **Művelet hozzáadása lehetőséget.**

   ![Képernyőkép az "If add member succeeded" feltétel "True" ágának a "Művelet hozzáadása" lehetőség be van jelölve.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. A Művelet **kiválasztása keresőmezőbe** írja be a következőt: , majd `outlook send email` válassza az E-mail küldése nevű **műveletet.**

   ![Képernyőkép a "Művelet kiválasztása" keresőmezőről, beírt "outlook e-mail küldése" szövegekkel és az értesítéshez kiválasztott "E-mail küldése" művelettel.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on success`

1. Az **E-mail küldése a sikeres** műveletről műveletben adja meg az itt ismertetett információkat:

   ![Az "E-mail küldése a sikeres műveletről" műveletet és a sikeres művelethez megadott információkat bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Törzs** | Yes | <*success-email-body*> | A sikeres műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban kövesse az alábbi lépéseket: <p>1. Írja be ezt a szöveget záró szóközvel: `New member has joined "test-members-ML":` <p>2. A megjelenő dinamikus tartalmú listából válassza ki az **E-mail-cím tulajdonságot.** <p>**Megjegyzés:** Ha ez a tulajdonság nem jelenik meg, a **Tag** hozzáadása listához szakaszfejléc mellett válassza a **További információ lehetőséget.** <p>3. A következő sorban írja be ezt a szöveget záró szóközt: `Member opt-in status: ` <p>4. A dinamikus tartalmak listájában, a Tag hozzáadása **listához alatt** válassza az **Állapot tulajdonságot.** |
   | **Tárgy** | Yes | <*success-email-subject*> | A sikeres műveletet jelző e-mail tárgya. Ebben az oktatóanyagban kövesse az alábbi lépéseket: <p>1. Írja be ezt a szöveget záró szóközvel: `Success! Member added to "test-members-ML": ` <p>2. A dinamikus tartalmak listájában, a Tag hozzáadása listához **alatt** válassza az **E-mail-cím tulajdonságot.** |
   | **Ide:** | Yes | <*saját e-mail-címe*> | Az e-mail-cím, amelyre a sikeres műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   |||||

1. Mentse a logikai alkalmazást.

## <a name="send-email-if-member-not-added"></a>E-mail küldése, ha a tag nem lett hozzáadva

1. Az **If add member succeeded (Sikeres** tag hozzáadása) feltétel False (Hamis) **ágában** válassza **a Művelet hozzáadása lehetőséget.**

   ![Képernyőkép az "If add member succeeded" feltétel "False" ágának "Művelet hozzáadása" lehetőségének kijelölve való hozzáadásáról.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. A Művelet **kiválasztása keresőmezőbe** írja be a következőt: , majd `outlook send email` válassza az E-mail küldése nevű **műveletet.**

   ![Képernyőkép a "Művelet kiválasztása" keresőmezőről, beírt "outlook e-mail küldése" szövegekkel és az "E-mail küldése" művelettel.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on failure`

1. Adja meg a művelet adatait az itt ismertetett módon:

   ![Képernyőkép a "Sikertelen e-mail küldése" műveletről és a sikertelen e-mailhez megadott információkról.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Törzs** | Yes | <*body-for-failure-email*> | A sikertelen műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Member might already exist. Check your MailChimp account.` |
   | **Tárgy** | Yes | <*subject-for-failure-email*> | A sikertelen műveletet jelző e-mail tárgya. Ebben az oktatóanyagban kövesse az alábbi lépéseket: <p>1. Írja be ezt a szöveget záró szóközvel: `Failed, member not added to "test-members-ML": ` <p>2. A dinamikus tartalmak listájában, a Tag hozzáadása listához **alatt** válassza az **E-mail-cím tulajdonságot.** |
   | **Ide:** | Yes | <*saját e-mail-címe*> | Az e-mail-cím, amelyre a sikertelen műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   |||||

1. Mentse a logikai alkalmazást. 

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![Képernyőkép a példa kész logikaialkalmazás-munkafolyamatról.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldjön magának egy e-mailes kérelmet a saját levelezőlistájához való csatlakozásról. Várja meg, amíg a kérelem megjelenik a bejövő levelei között.

1. A logikai alkalmazás manuális indításhoz a tervező eszköztárán válassza a **Futtatás lehetőséget.** 

   Ha az e-mail tárgya megfelel a trigger tárgyszűrőjének, a logikai alkalmazás küld egy e-mailt arról, hogy van egy jóváhagyásra váró feliratkozási kérelme.

1. A kapott jóváhagyási e-mailben válassza a Jóváhagyás **lehetőséget.**

1. Ha a feliratkozó e-mail-címe még nem szerepel a levelezőlistában, akkor a logikai alkalmazás hozzáadja az adott személy e-mail-címét, és az alábbi példához hasonló e-mailt küld Önnek:

   ![A sikeres előfizetéshez példa e-mail-címet bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Ha a logikai alkalmazás nem tudja hozzáadni a feliratkozót, akkor az alábbi példához hasonló e-mailt küld Önnek:

   ![A sikertelen előfizetéshez példa e-mail-címet bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely adatokat integrál különböző Azure- és Microsoft-szolgáltatások, illetve egyéb SaaS-alkalmazások között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A logikai alkalmazás addig fut, amíg le nem tiltja vagy törli. Ha már nincs szüksége a logikai mintaalkalmazásra, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. A Azure Portal keresőmezőbe írja be a létrehozott erőforráscsoport nevét. Az eredmények közül az **Erőforráscsoportok alatt** válassza ki az erőforráscsoportot.

   Ebben a példában létrehoztuk a nevű `LA-MailingList-RG` erőforráscsoportot.

   ![Képernyőkép az Azure keresőmezőről, beírt "la-mailinglist-rg" és **LA-MailingList-RG** elemekkel.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Ha az Azure kezdőlapján az erőforráscsoport a **Legutóbbi** erőforrások alatt látható, kiválaszthatja a csoportot a kezdőlapról.

1. Az erőforráscsoport menüjében jelölje be az **Áttekintés lehetőséget.** Az Áttekintés **panel eszköztárán** válassza az **Erőforráscsoport törlése lehetőséget.**

   ![Képernyőkép az erőforráscsoport "Áttekintés" paneljről, és a panel eszköztárán az "Erőforráscsoport törlése" elem van kiválasztva.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. A megjelenő megerősítési panelen adja meg az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy logikai alkalmazást, amely a levelezőlista-kérelmek jóváhagyását kezeli. Most megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely e-mail-mellékleteket dolgoz fel és tárol Azure-szolgáltatások, például az Azure Storage és az Azure Functions integrálásával.

> [!div class="nextstepaction"]
> [E-mail-mellékletek feldolgozása](../logic-apps/tutorial-process-email-attachments-workflow.md)
