---
title: Ütemezésalapú automatizálási munkafolyamatok létrehozása az Azure-ral
description: Oktatóanyag – Ütemezésalapú, ismétlődő automatizálási munkafolyamat létrehozása, amely felhőszolgáltatások között integrálható a Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 0dab2f49043f3a59dd4f06633e3f0228fc39a424
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792092"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Oktatóanyag: Ütemezésalapú és ismétlődő automatizálási munkafolyamatok létrehozása Azure Logic Apps

Ez az oktatóanyag bemutatja, hogyan lehet egy ismétlődő ütemezés szerint futó munkafolyamatot automatizálni egy példa logikai alkalmazás létrehozásához. [](../logic-apps/logic-apps-overview.md) Ez a példa logikai alkalmazás ellenőrzi az utazási időt, beleértve a forgalmat két hely között, és hétköznap reggel fut. Ha az idő meghaladja a megadott korlátot, a logikai alkalmazás e-mailt küld Önnek, amely tartalmazza az utazási időt és a célhoz való érkezéshez szükséges többletidőt. A munkafolyamat különböző lépéseket tartalmaz, amelyek egy ütemezésalapú eseményindítóval kezdődnek, amelyet egy Bing Térképek-művelet, egy adatműveleti művelet, egy folyamvezérlési művelet és egy e-mail-értesítési művelet követ.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Adjon hozzá egy Recurrence eseményindítót, amely meghatározza a logikai alkalmazás ütemezését.
> * Adjon hozzá egy Bing Térképek-műveletet, amely lekérte egy útvonal utazási idejét.
> * Adjon hozzá egy műveletet, amely létrehoz egy változót, átalakítja az utazási időt másodpercről percre, és tárolja a változót eredményező adatokat.
> * Feltétel hozzáadása, amely összehasonlítja az utazási időt a megadott határértékkel.
> * Művelet hozzáadása, amely e-mailt küld, ha az utazási idő meghaladja a határértéket.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Képernyőkép egy példa logikaialkalmazás-munkafolyamat magas szintű áttekintésére.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* E-mail-fiók egy olyan e-mail-szolgáltatótól, amelyet támogatnak a Logic Apps, például az Office 365 Outlook, a Outlook.com vagy a Gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). Ez a rövid útmutató az Office 365 Outlookot használja munkahelyi vagy iskolai fiókkal. Ha másik e-mail-fiókot használ, az általános lépések ugyanazok maradnak, de a felhasználói felület kissé eltérő lehet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, csak a G-Suite üzleti fiókok használhatja ezt az összekötőt korlátozás nélkül a logikai alkalmazásokban. Ha rendelkezik Gmail-felhasználói fiókkal, használhatja ezt az összekötőt csak bizonyos Google által jóváhagyott szolgáltatásokkal, vagy létrehozhat egy Google-ügyfélalkalmazást a Gmail-összekötővel való [hitelesítéshez.](/connectors/gmail/#authentication-and-bring-your-own-application) További információ: Adatbiztonsági és adatvédelmi szabályzatok [Google-összekötőkhöz a Azure Logic Apps.](../connectors/connectors-google-data-security-privacy-policy.md)

* Az útvonal megtételéhez szükséges idő lekéréséhez szükség van a Bing Térképek API hozzáférési kulcsára. A kulcs lekéréséhez kövesse a [Bing Térképek-kulcs lekérése](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) lépéseit.

* Ha a logikai alkalmazásnak egy olyan tűzfalon keresztül kell kommunikálnia, amely  adott [](logic-apps-limits-and-config.md#inbound) IP-címekre korlátozza a forgalmat, a tűzfalnak hozzáférést kell engedélyeznie az Logic Apps szolgáltatás vagy a futásidő által használt bejövő és kimenő IP-címek számára is abban az Azure-régióban, ahol a logikai alkalmazás létezik. [](logic-apps-limits-and-config.md#outbound) Ha a logikai [](../connectors/managed.md)alkalmazás felügyelt összekötőket is használ , például az Office 365 Outlook-összekötőt vagy az  SQL-összekötőt, vagy egyéni összekötőket használ, a tűzfalnak a felügyelt összekötő összes kimenő [IP-címének](logic-apps-limits-and-config.md#outbound) hozzáférését is engedélyeznie kell a logikai alkalmazás Azure-régiójában. [](/connectors/custom-connectors/)

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival. Az Azure kezdőlapján válassza az **Erőforrás létrehozása lehetőséget.**

1. A Azure Marketplace válassza az **Integrációs**  >  **logikai alkalmazás lehetőséget.**

   ![Képernyőkép a Azure Marketplace az "Integráció" és a "Logikai alkalmazás" beállítással.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. A **Logikai alkalmazás panelen** adja meg az itt leírt adatokat a létrehozni kívánt logikai alkalmazásról.

   ![Képernyőkép a logikai alkalmazás létrehozási panelről, valamint az új logikai alkalmazáshoz szükséges információkról.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés neve*> | Az Azure-előfizetés neve. Ez a példa a következőt használja: `Pay-As-You-Go`. |
   | **Erőforráscsoport** | LA-TravelTime-RG | Az [Azure-erőforráscsoport neve,](../azure-resource-manager/management/overview.md)amely a kapcsolódó erőforrások rendszerezését jelenti. Ez a példa egy nevű új erőforráscsoportot hoz `LA-TravelTime-RG` létre. |
   | **Név** | LA-TravelTime | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket ( ), aláhúzásjeleket ( ), zárójeleket ( , ) és `-` `_` `(` `)` pontokat ( ) `.` tartalmazhat. Ez a példa a következőt használja: `LA-TravelTime`. |
   | **Hely** | USA nyugati régiója | Az a régió, ahol a logikai alkalmazás adatait tárolni kell. Ez a példa a következőt használja: `West US`. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Ha végzett, válassza a Felülvizsgálat **+ létrehozás lehetőséget.** Miután az Azure érvényesíti a logikai alkalmazás adatait, válassza a **Létrehozás lehetőséget.**

1. Miután az Azure üzembe helyezett egy alkalmazást, válassza az **Ugrás az erőforráshoz lehetőséget.**

   Az Azure megnyitja Logic Apps sablonválasztási panelt, amely egy bevezető videót, a gyakran használt eseményindítókat és logikaialkalmazás-sablonmintákat mutat be.

1. Görgessen le a videó és a gyakori eseményindítók szakasz mellett a **Sablonok** szakaszhoz, és válassza az **Üres logikai alkalmazás lehetőséget.**

   ![Képernyőkép a sablon Logic Apps panelről, az "Üres logikai alkalmazás" kijelöléssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ezután adja hozzá az Ismétlődés [eseményindítót,](../logic-apps/logic-apps-overview.md#logic-app-concepts)amely a megadott ütemezés alapján futtatja a munkafolyamatot. Minden logikai alkalmazást egy eseményindítónak kell indítania, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha az új adatok teljesítenek egy adott feltételt. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Az Ismétlődés eseményindító hozzáadása

1. A Logic Apps Designer keresőmezőbe írja be a következőt: , majd `recurrence` válassza ki az Ismétlődés nevű **eseményindítót.**

   ![Képernyőkép a Logic Apps Designer keresőmezőről, amely az "ismétlődés" keresési kifejezést tartalmazza, és az "Eseményindítók" listában az "Ismétlődés" eseményindító van kiválasztva.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Az **Ismétlődés alakzaton** válassza a három **pont** **(...**) gombot, majd az **Átnevezés lehetőséget.** Nevezze át az eseményindítót a következő leírásra: `Check travel time every weekday morning`

   ![A kiválasztott három pont gombot, a "Beállítások" lista megnyitását és a kijelölt "Átnevezés" parancsot bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Az eseményindítón belül módosítsa ezeket a tulajdonságokat az itt leírt módon.

   ![Képernyőkép az eseményindító időközének és gyakoriságának változásairól.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Intervallum** | Yes | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Gyakoriság** | Yes | Hét | Az ismétlődéshez használni kívánt időegység |
   |||||

1. Az **Időköz és** a Gyakoriság **alatt** nyissa meg az Új paraméter **hozzáadása** listát, és válassza ki ezeket a tulajdonságokat az eseményindítóhoz való hozzáadáshoz.

   * **Ezeken a napokon**
   * **Ezekben az órákban**
   * **Ezekben a percekben**

   ![Képernyőkép a megnyitott "Új paraméter hozzáadása" listáról és a kiválasztott tulajdonságokról: "Ezeken a napokon", "Ezekben az órákban" és "Ezekben a percekben".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Most állítsa be a további tulajdonságok értékeit az itt ismertetett módon.

   ![Képernyőkép az alábbi táblázatban leírt értékekhez beállított további tulajdonságokról.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Ezeken a napokon** | Hétfő, kedd, szerda, csütörtök, péntek | Ez a beállítás csak akkor érhető el, ha a **Gyakoriság** beállítást **Hétre adja meg.** |
   | **Ezekben az órákban** | 7, 8, 9 | Ez a beállítás csak akkor érhető el, ha a **Gyakoriság** beállítása **Hét** vagy **Nap.** Ehhez az ismétlődéshez válassza ki a nap óráját. Ez a példa a `7` , `8` és `9` -hour jelölésekkel fut. |
   | **Ezekben a percekben** | 0, 15, 30, 45 | Ez a beállítás csak akkor érhető el, ha a **Gyakoriság** beállítása **Hét** vagy **Nap.** Ehhez az ismétlődéshez válassza ki a nap perceket. Ez a példa a nulla órás jellel kezdődik, és 15 percenként fut le. |
   ||||

   Ez az eseményindító minden hétköznap, 15 percenként aktiválódik, 7:00-tól egészen 9:45-ig. Az **Előnézet** mező az ismétlődési ütemezést mutatja. További információkért lásd: [Feladatok és a munkafolyamatok ütemezése](../connectors/connectors-native-recurrence.md) és [Munkafolyamat-műveletek és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Az eseményindító részleteinek elrejtéshez csukja össze az alakzatot az alakzat címsorán belülre kattintva.

   ![Az összecsukott eseményindító alakzatot bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés lehetőséget.**

A logikai alkalmazás most már a Azure Portal, de csak a megadott ütemezésen alapuló eseményindítót csinál. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="get-the-travel-time-for-a-route"></a>Útvonal megtételéhez szükséges idő lekérése

Most, hogy van eseményindítója, adjon hozzá egy olyan [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely lekéri az utazási időt két pont között. A Logic Apps biztosít egy összekötőt a Bing Térképek API-hoz, hogy könnyedén lekérhesse ezt az információt. Mielőtt hozzákezdene ehhez a feladathoz, ellenőrizze, hogy rendelkezik-e az oktatóanyag előfeltételeiben említett Bing Térképek API-kulccsal.

1. A Logikaialkalmazás-tervező Ismétlődés eseményindítója alatt válassza az Új **lépés lehetőséget.**

1. A **Művelet kiválasztása alatt válassza a** Standard **lehetőséget.** A keresőmezőbe írja be a következőt: `bing maps` , majd válassza az Útvonal le **szolgáltatása nevű műveletet.**

   ![Képernyőkép a "Bing Térképek" műveletekkel szűrt "Művelet kiválasztása" listáról, és a kiválasztott "Útvonal lekért" műveletről.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Ha nem rendelkezik Bing Térképek-kapcsolattal, a rendszer arra kéri, hogy hozzon létre egyet. Adja meg a kapcsolat részleteit az itt ismertetett módon, majd válassza a **Létrehozás lehetőséget.**

   ![Képernyőkép a Bing Térképek kapcsolatmezőről a megadott kapcsolatnévvel és a Bing Térképek API-kulccsal.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Yes | BingMapsConnection | Adja meg a kapcsolat nevét. Ez a példa a következőt használja: `BingMapsConnection`. |
   | **API-kulcs** | Yes | <*Bing Maps-API-kulcs*> | Adja meg a korábban kapott Bing Térképek API-kulcsot. Ha nem rendelkezik Bing Térképek-kulccsal, tudja meg, [hogyan kérhet le kulcsot](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Nevezze át a műveletet a következő leírással: `Get route and travel time with traffic` .

1. A műveletben nyissa meg az **Új paraméter hozzáadása listát,** és válassza ki ezeket a tulajdonságokat.

   * **Optimalizálja**
   * **Távolság mértékegysége**
   * **Közlekedési mód**

   ![Az "Útvonal lekért..." művelet az "Optimalizálás", "Távolság egység" és "Utazási mód" tulajdonságokkal.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Most adja meg az itt bemutatott és leírt tulajdonságok értékeit.

   ![Képernyőkép a "Get route" művelet további tulajdonságértékeivel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **1. útvonalpont** | Yes | <*start-location*> | Az útvonal eredete. Ez a példa egy példa kezdőcímet ad meg. |
   | **2. útvonalpont** | Yes | <*végpont helye*> | Az útvonal célja. Ez a példa egy példa célcímet ad meg. |
   | **Optimalizálja** | No | timeWithTraffic | Az útvonal optimalizálására szolgáló paraméter (például távolság, utazási idő a jelenlegi forgalom mellett stb.). Válassza ki a **timeWithTraffic paraméterértéket.** |
   | **Távolság mértékegysége** | No | <*saját preferencia*> | Az útvonalhoz használt távolság-mértékegység. Ez a példa a **Mérföld** mértékegységet használja. |
   | **Közlekedési mód** | No | Vezetés | Az útvonalhoz használt közlekedési mód. Válassza **az Autós** mód lehetőséget. |
   |||||

   További információ ezekről a paraméterekről és értékekről: [Útvonal kiszámítása.](/bingmaps/rest-services/routes/calculate-a-route)

1. A tervező eszköztárán válassza a Mentés **lehetőséget.**

Ezután hozzon létre egy változót a jelenlegi utazási idő átalakításához, és másodpercek helyett percekként való tárolásához. Így nem kell megismételnie az átalakítást, és egyszerűbben használhatja az értéket a későbbi lépésekben. 

## <a name="create-a-variable-to-store-travel-time"></a>Változó létrehozása az utazási idő tárolására

Előfordulhat, hogy műveleteket szeretne futtatni a munkafolyamat adatain, majd az eredményeket későbbi műveletekben használni. Az eredmények egyszerű újrafelhasználása vagy hivatkozás céljából történő mentéshez olyan változókat hozhat létre, amelyek a feldolgozás után tárolják ezeket az eredményeket. A logikai alkalmazásban csak a legfelső szinten hozhat létre változókat.

Alapértelmezés szerint a **Get route művelet** az aktuális utazási időt adja vissza másodpercben megadott forgalommal az Utazás időtartama Forgalom **tulajdonságból.** Ha átalakítja, és inkább percekként tárolja az értéket, később átalakítás nélkül, egyszerűbben tudja felhasználni.

1. A tervező Útvonal be **lekérdezése művelete** alatt válassza az Új **lépés lehetőséget.**

1. A **Művelet kiválasztása alatt válassza a** Beépített **lehetőséget.** A keresőmezőbe írja be a következőt: `variables` , majd válassza ki a változó **inicializálása nevű műveletet.**

   ![Képernyőkép a kiválasztott "Változó inicializálása" műveletről.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Nevezze át a műveletet a következő leírásra: `Create variable to store travel time`

1. Adja meg ezt az információt a változóhoz az alábbi táblázatban és a táblázat alatti lépésekben látható módon:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Yes | travelTime | A változó neve. Ez a példa a következőt használja: `travelTime`. |
   | **Típus** | Yes | Egész szám | A változó adattípusa |
   | **Érték** | No | Egy kifejezés, amely az aktuális utazási időt másodpercekké alakítja (lásd a táblázat alatti lépéseket). | A változó kezdeti értéke |
   |||||

   1. Az Érték tulajdonság kifejezésének **létrehozásához** kattintson a mezőn belülre, hogy megjelenjen a dinamikus tartalomlista. Ha szükséges, a dinamikus lista megjelenéseig széles körben használja a böngészőt. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget, amely megjeleníti a kifejezésszerkesztőt.

      ![Képernyőkép a "Változó inicializálása" műveletről, a kurzorral a "Value" tulajdonságon belül, amely megnyitja a dinamikus tartalomlistát.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      A dinamikus tartalmak listája a korábbi műveletek kimenetét jeleníti meg, amelyek közül választhat bemenetként a munkafolyamat későbbi műveletekhez. A dinamikus tartalmak listája tartalmaz egy kifejezésszerkesztőt, amely a kifejezésben műveleteket végző függvények kiválasztására használható. Ez a kifejezésszerkesztő csak a dinamikus tartalmak listájában érhető el.

   1. A kifejezésszerkesztőbe írja be a következő kifejezést: `div(,60)`

      ![Képernyőkép a kifejezésszerkesztőről, a megadott "div(,60)" kifejezéssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. A kifejezésen belül helyezze a kurzort a bal oldali zárójel (**(**) és a vessző (**,**) közé, majd válassza a **Dinamikus tartalom lehetőséget.**

      ![A "div(,60)" kifejezés kurzorának a kijelölt "Dinamikus tartalom" beállítással való mozgatásához választott kurzort bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. A dinamikus tartalmak listájában, a alatt válassza ki az Utazási idő **forgalma tulajdonságértéket.**

      ![Képernyőkép a kiválasztott "Utazási időtartam forgalma" tulajdonságról.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Miután a tulajdonság értéke feloldódik a kifejezésen belül, kattintson az **OK gombra.**

      ![Képernyőkép a kiválasztott "OK" gombról.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Az **Érték** tulajdonság most az alábbi módon jelenik meg:

      ![Képernyőkép a "Value" tulajdonságról a feloldott kifejezéssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amely ellenőrzi, hogy a jelenlegi utazás idő meghaladja-e a megadott határértéket.

## <a name="compare-the-travel-time-with-limit"></a>Az utazási idő és a korlát összehasonlítása

1. A **Create variable to store travel time (Változó létrehozása az utazási idő tárolására) művelet** alatt válassza az **Új lépés lehetőséget.**

1. A **Művelet kiválasztása alatt válassza a** Beépített **lehetőséget.** A keresőmezőbe írja be a `condition` kifejezést. A műveletek listájából válassza ki a Condition nevű **műveletet.**

   ![Képernyőkép a kiválasztott "Condition" műveletről](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Nevezze át a feltételt a következő leírásra: `If travel time exceeds limit`

1. Állítson össze egy feltételt, amely ellenőrzi, hogy **a travelTime** tulajdonság értéke meghaladja-e a megadott határértéket az itt ismertetett és bemutatott módon:

   1. A feltétel bal oldalán kattintson a Choose a value (Érték **kiválasztása) mezőre.**

   1. A megjelenő dinamikus tartalmú listából a **Változók alatt** válassza ki a **travelTime nevű tulajdonságot.**

      ![Képernyőkép a feltétel bal oldalán található "Érték kiválasztása" mezőről, megnyitott dinamikus tartalomlistával és a "travelTime" tulajdonsággal.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. A középső összehasonlító mezőben válassza ki a nagyobb, mint nevű **operátort.**

   1. A feltétel jobb oldalán, az Érték kiválasztása mezőben adja meg **a** következő korlátot: `15`

      Ha végzett, a feltétel a következő példához hasonlít:

      ![Képernyőkép az utazási idő és a megadott korlát összehasonlítására vonatkozó kész feltételről.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Mentse a logikai alkalmazást.

Ezután adja hozzá azt a műveletet, amely akkor fut le, ha az utazási idő meghaladja a határértéket.

## <a name="send-email-when-limit-exceeded"></a>E-mail küldése a határérték túllépésekor

Most adjon hozzá egy műveletet, amely e-mailt küld, ha az utazási idő meghaladja a határértéket. Az e-mail a jelenlegi utazási időt és a megadott útvonal teljesítéséhez szükséges többletidőt tartalmazza.

1. A feltétel Igaz **ágában** válassza a **Művelet hozzáadása lehetőséget.**

1. A **Művelet kiválasztása alatt válassza a** Standard **lehetőséget.** A keresőmezőbe írja be a `send email` kifejezést. A lista számos eredményt ad vissza, ezért a lista szűréséhez először válassza ki a kívánt e-mail-összekötőt.

   Ha például outlookos e-mail-fiókkal rendelkezik, válassza ki a fióktípus összekötőjét:

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget.
   * Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget.

   Ez a példa az Office 365 Outlook kiválasztásával folytatódik.

   ![Képernyőkép a "Műveletlista kiválasztása" lehetőségről, a "Standard" kategóriával és az "Office 365 Outlook" összekötővel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Amikor megjelenik az összekötő művelete, válassza ki az e-mailt küldeni kívánt műveletet, például:

   ![A kiválasztott "E-mail küldése" műveletet bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Ha még nem rendelkezik kapcsolattal, jelentkezzen be, és hitelesítse a hozzáférést az e-mail-fiókjához, amikor a rendszer erre kéri.

   Azure Logic Apps létrehoz egy kapcsolatot az e-mail-fiókjával.

1. Nevezze át a műveletet a következő leírásra: `Send email with travel time`

1. A **Címzett tulajdonsághoz** adja meg a címzett e-mail-címét. Tesztelési célokra használhatja az e-mail-címét.

1. A **Tárgy tulajdonságnál** adja meg az e-mail tárgyát, és adja meg **a travelTime** változót az alábbi lépésekkel:

   1. Írja be a `Current travel time (minutes):` szöveget, záró szóközzel. Tartsa a kurzort a **Tárgy mezőben,** hogy a dinamikus tartalmak listája nyitva maradjon.

   1. A dinamikus tartalmak listájának **Változók** fejlécében válassza a További információ lehetőséget, hogy megjelenjen a  **travelTime nevű** változó.

      ![Képernyőkép a dinamikus tartalmak listájáról, a "Változók" szakasz és a "Több" elem kijelölve.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > A dinamikus tartalomlista nem mutatja automatikusan a **travelTime** változót, mert a **Subject** tulajdonság sztringértéket vár, a **travelTime** pedig egész értéket.

      ![A dinamikus tartalmak listáját a "travelTime" változóval kijelölve bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. A **Törzs tulajdonsághoz** adja meg az e-mail törzsének tartalmát az alábbi lépésekkel:

   1. Írja be a `Add extra travel time (minutes):` szöveget, záró szóközzel. Tartsa a kurzort a **Törzs mezőben,** hogy a dinamikus tartalmak listája nyitva maradjon.

   1. A dinamikus tartalmak listájában válassza a **Kifejezés** lehetőséget, amely megjeleníti a kifejezésszerkesztőt.

      ![Képernyőkép a dinamikus tartalmak listájáról a "Kifejezés" beállítással.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. A kifejezésszerkesztőben írja be a következőt, hogy kiszámítsa a korlátot meghaladó `sub(,15)` percek számát: 

      ![Képernyőkép a kifejezésszerkesztőről a megadott "sub(,15)" kifejezéssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. A kifejezésen belül helyezze a kurzort a bal oldali zárójel (**(**) és a vessző (**,**) közé, majd válassza a **Dinamikus tartalom lehetőséget.**

      ![A "sub(,15)" kifejezés kurzorának a kijelölt "Dinamikus tartalom" beállítással való mozgatásához választott kurzort bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. A **Változók** alatt válassza a **travelTime** elemet.

      ![A dinamikus tartalmak listáját a "travelTime" változóval kijelölve bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Miután a tulajdonság feloldódik a kifejezésen belül, kattintson az **OK gombra.**

      ![A dinamikus tartalmak listáját és az "OK" beállítást kijelölve bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A **Törzs** tulajdonság most az alábbi módon jelenik meg:

      ![Képernyőkép a dinamikus tartalmak listájáról, az e-mail-művelet "Törzs" tulajdonságában feloldott kifejezéssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Mentse a logikai alkalmazást.

Ezután tesztelje és futtassa a logikai alkalmazást, amely most az alábbi példához hasonlóan néz ki:

![A kész példa logikaialkalmazás-munkafolyamatot bemutató képernyőkép](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális indításhoz a tervező eszköztárán válassza a **Futtatás lehetőséget.**

* Ha az aktuális utazási idő a korlát alatt marad, a logikai alkalmazás semmi mást nem tesz, és várakozik, vagy a következő időközt az újraellenőrzés előtt. 

* Ha a jelenlegi utazási idő meghaladja a határértéket, egy e-mailt kap, amely a jelenlegi utazási időt és a korlát feletti percek számát is bekérte. Íme egy példa a logikai alkalmazás által küldött e-mailre:

  ![Képernyőkép egy e-mail-példáról, amely az aktuális utazási időt és a megadott korlátot meghaladó utazási időt jelzi.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy ütemezésen alapuló, ismétlődő logikai alkalmazást. 

Ha az Ismétlődés eseményindítót használva más logikai alkalmazásokat is létre kell hoznia, tekintse meg ezeket a sablonokat, amelyek a logikai alkalmazás létrehozása után érhetők el: 

* Napi emlékeztetők küldésének beállítása.
* Régebbi Azure-blobok törlése.
* Üzenet hozzáadása egy Azure Storage-üzenetsorhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A logikai alkalmazás addig fut, amíg le nem tiltja vagy törli. Ha már nincs szüksége a logikai mintaalkalmazásra, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. A Azure Portal keresőmezőbe írja be a létrehozott erőforráscsoport nevét. Az eredmények közül az **Erőforráscsoportok alatt** válassza ki az erőforráscsoportot.

   Ebben a példában létrehoztuk a nevű `LA-TravelTime-RG` erőforráscsoportot.

   ![Képernyőkép az Azure keresőmezőről a "la-travel-time-rg" beírt és a **LA-TravelTime-RG** elem be van jelölve.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Ha az Azure kezdőlapján az erőforráscsoport a **Legutóbbi** erőforrások alatt látható, kiválaszthatja a csoportot a kezdőlapról.

1. Az erőforráscsoport menüjében jelölje be az **Áttekintés lehetőséget.** Az Áttekintés **panel** eszköztárán válassza az **Erőforráscsoport törlése lehetőséget.**

   ![Az erőforráscsoport "Áttekintés" paneljének képernyőképe, a panel eszköztárán pedig az "Erőforráscsoport törlése" elem van kiválasztva.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. A megjelenő megerősítési panelen adja meg az erőforráscsoport nevét, majd válassza a **Törlés lehetőséget.**

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy logikai alkalmazást, amely ellenőrzi a forgalmat egy megadott ütemezés szerint (hétköznap reggelente), és műveletet végez (e-mailt küld), ha az utazási idő meghaladja a megadott korlátot. Most megtudhatja, hogyan építhet ki egy olyan logikai alkalmazást, amely levelezőlista-kérelmeket küld jóváhagyásra az Azure-szolgáltatások, a Microsoft-szolgáltatások és más szolgáltatott szoftver (SaaS) alkalmazások integrálásával.

> [!div class="nextstepaction"]
> [Levelezési listára vonatkozó kérelmek kezelése](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
