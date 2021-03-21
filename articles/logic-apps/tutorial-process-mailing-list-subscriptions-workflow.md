---
title: Jóváhagyási alapú automatizált munkafolyamatok létrehozása
description: Oktatóanyag – jóváhagyási alapú automatikus munkafolyamat létrehozása, amely a levelezőlista-előfizetéseket dolgozza fel Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/07/2020
ms.openlocfilehash: 1690b8d143b86e5caa691f5f8f479f715f57f0c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054653"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Oktatóanyag: automatikus jóváhagyási alapú munkafolyamatok létrehozása Azure Logic Apps használatával

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy példaként szolgáló [logikai alkalmazást](../logic-apps/logic-apps-overview.md) , amely automatizálja a jóváhagyási alapú munkafolyamatokat. Ez a példa a logikai alkalmazás a [MailChimp](https://mailchimp.com/) szolgáltatás által kezelt levelezőlistára vonatkozó előfizetési kérelmeket dolgozza fel. Ez a logikai alkalmazás számos lépést tartalmaz, amelyek a kérelmekhez tartozó e-mail-fiókok figyelésével kezdődnek, elküldi a kéréseket jóváhagyásra, ellenőrzi, hogy a kérelem megkapja-e a jóváhagyást, hozzáadja a jóváhagyott tagokat a levelezőlistához, és megerősíti, hogy a rendszer hozzáadja-e az új tagokat a listához.

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

![Magas szintű kész logikai alkalmazások – áttekintés](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Egy MailChimp-fiók, amelyben korábban létrehozott egy "test-Members-ML" nevű listát, ahol a logikai alkalmazás hozzáadhat e-mail-címeket a jóváhagyott tagokhoz. Ha nem rendelkezik fiókkal, [regisztráljon egy ingyenes fiókra](https://login.mailchimp.com/signup/), és Ismerje meg, [hogyan hozhat létre MailChimp-listát](https://us17.admin.mailchimp.com/lists/#).

* Egy Logic Apps által támogatott e-mail-szolgáltatótól (például Office 365 Outlook, Outlook.com vagy Gmail) származó e-mail-fiók. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). Ez a rövid útmutató az Office 365 Outlookot használja munkahelyi vagy iskolai fiókkal. Ha más e-mail-fiókot használ, az általános lépések változatlanok maradnak, de a felhasználói felület némileg eltérő lehet.

* Egy e-mail-fiók az Office 365 Outlook vagy Outlook.com, amely támogatja a jóváhagyási munkafolyamatokat. Ebben az oktatóanyagban az Office 365 Outlookot használjuk. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott IP-címekre korlátozza a forgalmat, *akkor a* tűzfalnak engedélyeznie kell a hozzáférést a Logic Apps szolgáltatás vagy futtatókörnyezet által használt [bejövő](logic-apps-limits-and-config.md#inbound) és [kimenő](logic-apps-limits-and-config.md#outbound) IP-címekhez abban az Azure-régióban, ahol a logikai alkalmazás létezik. Ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md#managed-api-connectors)is használ, például az Office 365 Outlook Connectort vagy az SQL Connectort, vagy [Egyéni összekötőket](/connectors/custom-connectors/)használ, a tűzfalnak engedélyeznie kell a hozzáférést a logikai alkalmazás Azure-régiójában lévő *összes* [felügyelt összekötő kimenő IP-címéhez](logic-apps-limits-and-config.md#outbound) .

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival. Az Azure kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.

1. Az Azure Marketplace menüben válassza az **integrációs**  >  **logikai alkalmazás** lehetőséget.

   ![Képernyőfelvétel: az "integráció" és "logikai alkalmazás" beállítással rendelkező Azure Marketplace menü.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. A **logikai alkalmazás** ablaktáblán adja meg a létrehozni kívánt logikai alkalmazással kapcsolatos információkat.

   ![Képernyőkép a logikai alkalmazás létrehozási paneljéről és az új logikai alkalmazás számára megadható adatokról.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés – név*> | Az Azure-előfizetés neve. Ez a példa a következőt használja: `Pay-As-You-Go`. |
   | **Erőforráscsoport** | LA-MailingList-RG | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md)neve. Ez a példa egy nevű új erőforráscsoportot hoz létre `LA-MailingList-RG` . |
   | **Név** | LA-MailingList | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket () `-` , aláhúzásokat ( `_` ), zárójeleket ( `(` , `)` ) és pontokat ( `.` ) tartalmazhat. Ez a példa a következőt használja: `LA-MailingList`. |
   | **Hely** | USA nyugati régiója | A logikai alkalmazás adatainak tárolására szolgáló régió. Ez a példa a következőt használja: `West US`. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. Miután az Azure érvényesíti a logikai alkalmazással kapcsolatos információkat, válassza a **Létrehozás** lehetőséget.

1. Miután az Azure üzembe helyezte az alkalmazást, válassza az **Ugrás az erőforráshoz** lehetőséget.

   Az Azure megnyitja a Logic Apps sablon kiválasztása ablaktáblát, amely bemutatja a bevezető videót, a gyakran használt eseményindítókat és a Logic app-sablonok mintáit.

1. Görgessen le a videó és az általános eseményindítók szakaszhoz a **sablonok** szakaszban, majd válassza az **üres logikai alkalmazás** lehetőséget.

   ![Az "üres logikai alkalmazás" beállítású Logic Apps sablon kijelölése ablaktáblát megjelenítő képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Ezután adjon hozzá egy Outlook- [triggert](../logic-apps/logic-apps-overview.md#logic-app-concepts) , amely figyeli a beérkező e-maileket az előfizetési kérelmekkel. Minden logikai alkalmazásnak egy eseményindítóval kell kezdődnie, amely egy adott esemény bekövetkezésekor következik be, vagy amikor az új adatértékek megfelelnek egy adott feltételnek. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Az e-mailek monitorozására szolgáló trigger hozzáadása

1. A Logic Apps Designer keresőmezőbe írja be a kifejezést `when email arrives` , majd válassza ki az **új e-mail-üzenet érkezésekor** megnevezett triggert.

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget.
   * Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget.

   Ez a példa az Office 365 Outlook kiválasztásával folytatódik.

   ![Képernyőfelvétel: a "Ha e-mailben érkezik" keresési kifejezést tartalmazó Logic Apps Designer keresőmező, valamint az "új e-mail-üzenet érkezésekor" trigger jelenik meg.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Ha még nem rendelkezik kapcsolattal, jelentkezzen be, és hitelesítse a hozzáférést az e-mail-fiókjához, amikor a rendszer kéri.

   A Azure Logic Apps létrehozza az e-mail-fiókjához való kapcsolódást.

1. Az triggerben adja meg az új e-mailek ellenőrzésének feltételeit.

   1. Adja meg az e-mailek ellenőrzésének mappáját, és tartsa meg a többi tulajdonságot az alapértelmezett értékekhez.

      ![Képernyőkép, amely megjeleníti a tervezőt az "új e-mail érkezésekor" művelet és a "mappa" beállítással a "beérkezett fájlok" elemre.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Adja hozzá az trigger **tulajdonos szűrő** tulajdonságát, hogy az e-maileket a Tárgy sor alapján szűrheti. Nyissa meg az **új paraméter hozzáadása** listát, és válassza a **tulajdonos szűrő** elemet.

      ![Képernyőfelvétel: a megnyitott "új paraméter hozzáadása" lista a "tulajdonos szűrő" beállítással.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Az trigger tulajdonságaival kapcsolatos további információkért tekintse meg az [Office 365 Outlook Connector-referenciát](/connectors/office365/) vagy az [Outlook.com-összekötő referenciáját](/connectors/outlook/).

   1. Miután a tulajdonság megjelenik az triggerben, írja be a következő szöveget: `subscribe-test-members-ML`

      ![Képernyőkép, amely a "subscribe-test-Members-ML" szöveggel ellátott "tulajdonos szűrő" tulajdonságot jeleníti meg.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Ha most el szeretné rejteni az trigger részleteit, az alakzat címsorára kattintva csukja össze az alakzatot.

   ![Képernyőkép, amely az összecsukott trigger alakzatát mutatja.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

A logikai alkalmazás most már működőképes, de mindössze annyit csinál, hogy új elemeket keres a bejövő e-mailek között. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="send-approval-email"></a>Jóváhagyó e-mail küldése

Most, hogy van egy triggere, adjon hozzá egy [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely elküld egy e-mailt a kérelem jóváhagyásához vagy elutasításához.

1. Kattintson az új **lépés** gombra a Logic apps Designerben, **Amikor új e-mail érkezik** az aktiválásra.

1. A **válasszon műveletet** területen a keresőmezőbe írja be a kifejezést `send approval` , majd válassza ki a **jóváhagyás elküldése e-mailben** nevű műveletet.

   ![A "jóváhagyás" művelet által szűrt "művelet kiválasztása" lista, valamint a "jóváhagyó e-mail küldése" művelet bejelölését bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Most adja meg az itt látható és itt leírt tulajdonságok értékeit. az összes többi meghagyása az alapértelmezett értékeken. További információ ezekről a tulajdonságokról: [Office 365 Outlook Connector-hivatkozás](/connectors/office365/) vagy a [Outlook.com-összekötő leírása](/connectors/outlook/).

   ![A "jóváhagyó e-mail küldése" tulajdonságot megjelenítő képernyőkép](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Ide:** | <*jóváhagyás – e-mail-cím*> | A jóváhagyó e-mail-címe. Tesztelési célokra használhatja a saját címét. Ez a példa a kitalált `sophiaowen@fabrikam.com` e-mail-címet használja. |
   | **Tárgy** | `Approve member request for test-members-ML` | Egy leíró e-mail-tárgy |
   | **Felhasználói választási lehetőségek** | `Approve, Reject` | Győződjön meg arról, hogy ez a tulajdonság határozza meg a jóváhagyó által kiválasztható válasz beállításokat, amelyeket a rendszer alapértelmezés szerint **jóváhagy** vagy **visszautasít** . |
   ||||

   > [!NOTE]
   > Ha rákattint egy szerkesztési mezőre, a dinamikus tartalom lista jelenik meg, amelyet most figyelmen kívül hagyhat. Ez a lista azokat a korábbi műveletekből származó kimeneteket jeleníti meg, amelyek elérhetők a munkafolyamatban a következő műveletek bemenetként való kiválasztásához.
 
1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amely ellenőrzi a jóváhagyó kiválasztott válaszát.

## <a name="check-approval-response"></a>Jóváhagyási válasz ellenőrzése

1. A **jóváhagyó E-mail küldése** művelet alatt válassza az **új lépés** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be a kifejezést `condition` , majd válassza ki a **feltétel** nevű műveletet.

   ![Képernyőfelvétel: "a művelet kiválasztása" keresőmező "beépített" kiválasztott és "feltétel" keresési kifejezésként, míg a "feltétel" művelet be van jelölve.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. A **feltétel** címsorában **válassza a három** pontot (**...**), majd az **Átnevezés** lehetőséget. Nevezze át a feltételt a következő leírásra: `If request approved`

   ![Képernyőkép, amely megjeleníti a "beállítások" listán kiválasztott három ponttal jelölt gombot, és a "Átnevezés" parancs van kiválasztva.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyó kiválasztotta-e a **jóváhagyást**.

   1. A feltétel bal oldalán kattintson a **válasszon Value (érték választása** ) mezőre.

   1. A megjelenő dinamikus tartalom listából válassza ki a **SelectedOption** tulajdonságot a **jóváhagyó e-mail küldése** területen.

      ![Képernyőfelvétel: a dinamikus tartalmak listájának megjelenítése, ahol a "jóváhagyó e-mail küldése" szakaszban a "SelectedOption" kimenet jelenik meg.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. A középső összehasonlítás mezőben válassza az **egyenlő** operátorként lehetőséget.

   1. A feltétel jobb oldalán, a **Válasszon értéket** mezőben adja meg a szöveget `Approve` .

      Ha elkészült, a feltétel a következő példához hasonlóan néz ki:

      ![A jóváhagyott kérelem befejezett feltételét bemutató képernyőkép](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Mentse a logikai alkalmazást.

Ezután adja meg a műveletet, amelyet a logikai alkalmazás végrehajt, amikor a felülvizsgáló jóváhagyja a kérést. 

## <a name="add-member-to-mailchimp-list"></a>Tag hozzáadása MailChimp-listához

Most adjon hozzá egy műveletet, amely hozzáadja a jóváhagyott tagot a levelezési listához.

1. A feltétel **igaz** ágában válassza a **művelet hozzáadása** lehetőséget.

1. A művelet keresésének **kiválasztása** mezőben válassza az **összes** lehetőséget. A keresőmezőbe írja be a kifejezést `mailchimp` , majd válassza ki a **tag hozzáadása a listához** nevű műveletet.

   ![A "MailChimp" keresési kifejezést és a "tag hozzáadása a listához" műveletet megjelenítő képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Ha még nem rendelkezik csatlakozással a MailChimp-fiókjához, a rendszer felszólítja, hogy jelentkezzen be.

1. A **tag hozzáadása a listához** művelethez adja meg az itt látható és leírt adatokat:

   ![Képernyőkép, amely a "tag hozzáadása a listához" művelet információit jeleníti meg.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Listaazonosító** | Yes | <*levelezési lista – név*> | Válassza ki a MailChimp levelezési lista nevét. Ez a példa a következőt használja: `test-members-ML`. |
   | **E-mail-cím** | Yes | <*új tag – e-mail-cím*> | A megnyíló dinamikus tartalmak listájában, az **új e-mail érkezésekor** szakaszban válassza a **from** elemet, amely a triggerből származik, és megadja az új tag e-mail-címét. |
   | **Állapot** | Yes | <*tag – előfizetés – állapot*> | Válassza ki az új tag számára beállítani kívánt előfizetési állapotot. Ez a példa kiválasztja a következőt: `subscribed` . <p>További információ: [Feliratkozók kezelése a MailChimp API-val](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   A **tag hozzáadása a listához** művelet tulajdonságaival kapcsolatos további információkért lásd a [MailChimp-összekötő referenciáját](/connectors/mailchimp/).

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amellyel ellenőrizheti, hogy az új tag sikeresen csatlakozott-e a levelezőlistájához. Így a logikai alkalmazás értesítést kaphat arról, hogy a művelet sikeres vagy sikertelen volt-e.

## <a name="check-for-success-or-failure"></a>Siker vagy sikertelenség ellenőrzése

1. Az **igaz** ág a **tag hozzáadása a listához** művelet alatt válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be a kifejezést `condition` , majd válassza ki a **feltétel** nevű műveletet.

1. Nevezze át a feltételt a következő leírásra: `If add member succeeded`

1. Hozzon létre egy feltételt, amely ellenőrzi, hogy a jóváhagyott tag sikeresen csatlakozott-e a levelezőlistához:

   1. A feltétel bal oldalán kattintson a **válasszon Value (érték választása** ) mezőre. A megjelenő dinamikus tartalom listából válassza ki az **állapot** tulajdonságot a **tag hozzáadása a listához** szakaszban.

      A feltétel például a következő példához hasonlít:

      ![A feltételt jelző, "status" értékű "válasszon egy értéket" mező jelenik meg.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. A középső összehasonlítás mezőben válassza az **egyenlő** operátorként lehetőséget.

   1. A feltétel jobb oldalán, a **Válasszon értéket** mezőben adja meg a következő szöveget: `subscribed`

      Ha elkészült, a feltétel a következő példához hasonlóan néz ki:

      ![A sikeres vagy sikertelen előfizetés ellenőrzésének befejezett állapotát bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Ezután állítsa be az elküldött e-maileket, ha a jóváhagyott tag sikeresen vagy sikertelenül csatlakozik a levelezőlistához.

## <a name="send-email-if-member-added"></a>E-mail küldése tag hozzáadásakor

1. Ha a **tag hozzáadása sikeres** feltétel, az **igaz** ág területen válassza a **művelet hozzáadása** lehetőséget.

   ![Képernyőfelvétel: "Ha a tag hozzáadása sikeres" állapot értéke "true", a "művelet hozzáadása" lehetőség van kiválasztva.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. A művelet keresésének **kiválasztása** mezőbe írja be `outlook send email` a kifejezést, majd válassza ki az **e-mail küldése** nevű műveletet.

   ![Képernyőkép: a "válasszon egy műveletet" keresőmezőt az "Outlook e-mail-küldése" megadásával és az értesítéshez kiválasztott "e-mail küldése" művelettel.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on success`

1. Az **E-mail küldése a sikerhez** műveletnél adja meg az itt látható és az itt leírt információkat:

   ![Képernyőkép: az "e-mail küldése a sikerhez" művelet és a sikeres e-mailhez megadott információ.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Törzs** | Yes | <*sikeres – e-mail – törzs*> | A sikeres műveletet jelző e-mail szövegtörzse. Ehhez az oktatóanyaghoz kövesse az alábbi lépéseket: <p>1. adja meg ezt a szöveget egy záró szóközzel: `New member has joined "test-members-ML":` <p>2. a megjelenő dinamikus tartalmak listájából válassza ki az **E-mail cím** tulajdonságot. <p>**Megjegyzés**: Ha ez a tulajdonság nem jelenik meg, a **tag hozzáadása a listához** szakasz fejléc mellett válassza a **továbbiak** lehetőséget. <p>3. a következő sorban adja meg ezt a szöveget egy záró szóközzel: `Member opt-in status: ` <p>4. a dinamikus tartalmak listájában a **tag hozzáadása a listához** területen válassza ki az **állapot** tulajdonságot. |
   | **Tárgy** | Yes | <*sikeres – e-mailek tárgya*> | A sikeres műveletet jelző e-mail tárgya. Ehhez az oktatóanyaghoz kövesse az alábbi lépéseket: <p>1. adja meg ezt a szöveget egy záró szóközzel: `Success! Member added to "test-members-ML": ` <p>2. a dinamikus tartalmak listájából válassza ki az **E-mail cím** tulajdonságot a **tag hozzáadása a listához** területen. |
   | **Ide:** | Yes | <*saját e-mail cím*> | Az e-mail-cím, amelyre a sikeres műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   |||||

1. Mentse a logikai alkalmazást.

## <a name="send-email-if-member-not-added"></a>E-mail küldése, ha a tag nem lett hozzáadva

1. Ha a **tag hozzáadása sikeres** feltétel, a **hamis** ág alatt válassza a **művelet hozzáadása** lehetőséget.

   ![Képernyőfelvétel: "Ha a tag hozzáadása sikeres" feltétel "hamis" ág a "művelet hozzáadása" beállítással.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. A művelet keresésének **kiválasztása** mezőbe írja be `outlook send email` a kifejezést, majd válassza ki az **e-mail küldése** nevű műveletet.

   ![Képernyőkép: a "válasszon egy műveletet" keresőmezőt az "Outlook e-mail-küldése" művelettel, és az "e-mail küldése" műveletet jelölte be.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Nevezze át a műveletet a következő leírásra: `Send email on failure`

1. Adja meg a művelet adatait az itt bemutatott módon:

   ![Képernyőkép: az "e-mailek küldése hiba esetén" művelet és a hiba e-mail-címéhez megadott információ.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Törzs** | Yes | <*törzs – hiba – e-mail*> | A sikertelen műveletet jelző e-mail szövegtörzse. Ebben az oktatóanyagban adja meg a következő szöveget: <p>`Member might already exist. Check your MailChimp account.` |
   | **Tárgy** | Yes | <*hiba tárgya – e-mail*> | A sikertelen műveletet jelző e-mail tárgya. Ehhez az oktatóanyaghoz kövesse az alábbi lépéseket: <p>1. adja meg ezt a szöveget egy záró szóközzel: `Failed, member not added to "test-members-ML": ` <p>2. a dinamikus tartalmak listájából válassza ki az **E-mail cím** tulajdonságot a **tag hozzáadása a listához** területen. |
   | **Ide:** | Yes | <*saját e-mail cím*> | Az e-mail-cím, amelyre a sikertelen műveletet jelző e-mail érkezik. Tesztelési célokra használhatja a saját e-mail-címét. |
   |||||

1. Mentse a logikai alkalmazást. 

Következő lépésként tesztelje a logikai alkalmazást, amely most az alábbi példára hasonlít:

![Képernyőkép, amely a logikai alkalmazás munkafolyamatát mutatja be.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

1. Küldjön magának egy e-mailes kérelmet a saját levelezőlistájához való csatlakozásról. Várja meg, amíg a kérelem megjelenik a bejövő levelei között.

1. A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** lehetőséget. 

   Ha az e-mail tárgya megfelel a trigger tárgyszűrőjének, a logikai alkalmazás küld egy e-mailt arról, hogy van egy jóváhagyásra váró feliratkozási kérelme.

1. A kapott jóváhagyási e-mailben válassza a **jóváhagyás** lehetőséget.

1. Ha a feliratkozó e-mail-címe még nem szerepel a levelezőlistában, akkor a logikai alkalmazás hozzáadja az adott személy e-mail-címét, és az alábbi példához hasonló e-mailt küld Önnek:

   ![A sikeres előfizetés példáját bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Ha a logikai alkalmazás nem tudja hozzáadni a feliratkozót, akkor az alábbi példához hasonló e-mailt küld Önnek:

   ![A sikertelen előfizetésre vonatkozó példát bemutató képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely adatokat integrál különböző Azure- és Microsoft-szolgáltatások, illetve egyéb SaaS-alkalmazások között.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A logikai alkalmazás továbbra is fut, amíg le nem tiltja vagy nem törli az alkalmazást. Ha már nincs szüksége a minta logikai alkalmazásra, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. A Azure Portal keresési mezőjébe írja be a létrehozott erőforráscsoport nevét. Az eredmények között az **erőforráscsoportok** területen válassza ki az erőforráscsoportot.

   Ez a példa létrehozta a nevű erőforráscsoportot `LA-MailingList-RG` .

   ![A "La-mailinglist-RG" beírt és * * LA-MailingList-RG * * elemet tartalmazó képernyőkép.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Ha az Azure kezdőlapján megjelenik az erőforráscsoport a **legutóbbi erőforrások** területen, akkor a kezdőlapon kiválaszthatja a csoportot.

1. Az erőforráscsoport menüben válassza az **Áttekintés** lehetőséget. Az **Áttekintés** ablaktábla eszköztárán válassza az **erőforráscsoport törlése** elemet.

   ![Képernyőfelvétel: az erőforráscsoport "áttekintés" panelje, valamint a panel eszköztárán az "erőforráscsoport törlése" lehetőség van kiválasztva.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. A megjelenő megerősítő panelen adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy logikai alkalmazást, amely a levelezőlistára vonatkozó kérések jóváhagyásait kezeli. Most megismerheti, hogyan hozhat létre egy logikai alkalmazást, amely e-mail-mellékleteket dolgoz fel és tárol Azure-szolgáltatások, például az Azure Storage és az Azure Functions integrálásával.

> [!div class="nextstepaction"]
> [E-mail-mellékletek feldolgozása](../logic-apps/tutorial-process-email-attachments-workflow.md)
