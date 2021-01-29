---
title: Ütemezett automatizálási munkafolyamatok létrehozása az Azure-ban
description: Oktatóanyag – olyan ütemezett, ismétlődő automatizálási munkafolyamatot hoz létre, amely integrálható a felhőalapú szolgáltatásokra Azure Logic Apps használatával.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/30/2020
ms.openlocfilehash: 95275e68d0c7674caf4dd2b20f5586db5193fd03
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054060"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Oktatóanyag: ütemezett és ismétlődő automatizálási munkafolyamatok létrehozása Azure Logic Apps

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan példaként szolgáló [logikai alkalmazást](../logic-apps/logic-apps-overview.md) , amely automatizálja az ismétlődő ütemterveken futó munkafolyamatokat. Ez a példa a logikai alkalmazás esetében ellenőrzi az utazási időt, beleértve a forgalmat, a két hely között, és minden hétköznap reggel. Ha az idő meghaladja a megadott korlátot, a logikai alkalmazás elküld egy e-mailt, amely tartalmazza az utazási időt és a célhelyre való megérkezéshez szükséges további időt. A munkafolyamat különféle lépéseket tartalmaz, amelyek egy ütemezett triggerrel kezdődnek, majd egy Bing Maps művelet, egy adatműveleti művelet, egy vezérlési folyamat és egy e-mail értesítési művelet.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Üres logikai alkalmazás létrehozása.
> * Adjon hozzá egy ismétlődési eseményindítót, amely meghatározza a logikai alkalmazás ütemtervét.
> * Adjon hozzá egy olyan Bing Maps műveletet, amely egy útvonal utazási idejét kapja meg.
> * Olyan műveletet adhat hozzá, amely létrehoz egy változót, az utazási időt másodpercről percre konvertálja, és a változót eredményezi.
> * Feltétel hozzáadása, amely összehasonlítja az utazási időt a megadott határértékkel.
> * Adjon hozzá egy műveletet, amely e-mailt küld, ha az utazási idő meghaladja a korlátot.

Az elkészült logikai alkalmazás nagyjából a következő munkafolyamathoz hasonlít:

![Képernyőkép, amely egy példa logikai alkalmazás munkafolyamatának magas szintű áttekintését jeleníti meg.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy Logic Apps által támogatott e-mail-szolgáltatótól (például Office 365 Outlook, Outlook.com vagy Gmail) származó e-mail-fiók. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). Ez a rövid útmutató az Office 365 Outlookot használja munkahelyi vagy iskolai fiókkal. Ha más e-mail-fiókot használ, az általános lépések változatlanok maradnak, de a felhasználói felület némileg eltérő lehet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Az útvonal megtételéhez szükséges idő lekéréséhez szükség van a Bing Térképek API hozzáférési kulcsára. A kulcs lekéréséhez kövesse a [Bing Térképek-kulcs lekérése](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key) lépéseit.

* Ha a logikai alkalmazásnak olyan tűzfalon keresztül kell kommunikálnia, amely adott IP-címekre korlátozza a forgalmat, *akkor a* tűzfalnak engedélyeznie kell a hozzáférést a Logic Apps szolgáltatás vagy futtatókörnyezet által használt [bejövő](logic-apps-limits-and-config.md#inbound) és [kimenő](logic-apps-limits-and-config.md#outbound) IP-címekhez abban az Azure-régióban, ahol a logikai alkalmazás létezik. Ha a logikai alkalmazás [felügyelt összekötőket](../connectors/apis-list.md#managed-api-connectors)is használ, például az Office 365 Outlook Connectort vagy az SQL Connectort, vagy [Egyéni összekötőket](/connectors/custom-connectors/)használ, a tűzfalnak engedélyeznie kell a hozzáférést a logikai alkalmazás Azure-régiójában lévő *összes* [felügyelt összekötő kimenő IP-címéhez](logic-apps-limits-and-config.md#outbound) .

## <a name="create-your-logic-app"></a>A logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival. Az Azure kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.

1. Az Azure Marketplace menüben válassza az **integrációs**  >  **logikai alkalmazás** lehetőséget.

   ![Képernyőfelvétel: az "integráció" és "logikai alkalmazás" beállítással rendelkező Azure Marketplace menü.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. A **logikai alkalmazás** ablaktáblán adja meg a létrehozni kívánt logikai alkalmazással kapcsolatos információkat.

   ![Képernyőkép a logikai alkalmazás létrehozási paneljéről és az új logikai alkalmazás számára megadható adatokról.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Előfizetés** | <*Azure-előfizetés – név*> | Az Azure-előfizetés neve. Ez a példa a következőt használja: `Pay-As-You-Go`. |
   | **Erőforráscsoport** | LA-TravelTime-RG | A kapcsolódó erőforrások rendszerezéséhez használt [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md)neve. Ez a példa egy nevű új erőforráscsoportot hoz létre `LA-TravelTime-RG` . |
   | **Név** | LA-TravelTime | A logikai alkalmazás neve, amely csak betűket, számokat, kötőjeleket () `-` , aláhúzásokat ( `_` ), zárójeleket ( `(` , `)` ) és pontokat ( `.` ) tartalmazhat. Ez a példa a következőt használja: `LA-TravelTime`. |
   | **Hely** | USA nyugati régiója | A logikai alkalmazás adatainak tárolására szolgáló régió. Ez a példa a következőt használja: `West US`. |
   | **Naplóelemzés** | Kikapcsolva | A diagnosztikai naplózáshoz maradjon a **Ki** beállításnál. |
   ||||

1. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. Miután az Azure érvényesíti a logikai alkalmazással kapcsolatos információkat, válassza a **Létrehozás** lehetőséget.

1. Miután az Azure üzembe helyezte az alkalmazást, válassza az **Ugrás az erőforráshoz** lehetőséget.

   Az Azure megnyitja a Logic Apps sablon kiválasztása ablaktáblát, amely bemutatja a bevezető videót, a gyakran használt eseményindítókat és a Logic app-sablonok mintáit.

1. Görgessen le a videó és az általános eseményindítók szakaszhoz a **sablonok** szakaszban, majd válassza az **üres logikai alkalmazás** lehetőséget.

   ![Az "üres logikai alkalmazás" beállítású Logic Apps sablon kijelölése ablaktáblát megjelenítő képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Ezután adja hozzá az ismétlődési [eseményindítót](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely egy megadott ütemezésen alapuló munkafolyamatot futtat. Minden logikai alkalmazást egy eseményindítónak kell indítania, amely akkor aktiválódik, ha egy adott esemény bekövetkezik, vagy ha az új adatok teljesítenek egy adott feltételt. További információkért lásd: [Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Az ismétlődési eseményindító hozzáadása

1. A Logic Apps Designer keresőmezőbe írja be a kifejezést `recurrence` , majd válassza ki az **Ismétlődés** nevű eseményindítót.

   ![Képernyőfelvétel: az "Ismétlődés" keresési kifejezést és az "eseményindítók" listáját tartalmazó Logic Apps Designer keresőmező, amely az "Ismétlődés" eseményindítót jeleníti meg.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Az **ismétlődési** alakzaton válassza a három pontot (**..**.), **majd az** **Átnevezés** lehetőséget. Nevezze át az eseményindítót a következő leírásra: `Check travel time every weekday morning`

   ![Képernyőkép, amely megjeleníti az ellipszisek gombot, a "beállítások" lista meg van nyitva, és az "Átnevezés" parancs van kiválasztva.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Az triggeren belül módosítsa ezeket a tulajdonságokat az itt leírtak szerint.

   ![Képernyőkép, amely megjeleníti az trigger intervallumának és gyakoriságának változásait.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Intervallum** | Yes | 1 | Az ellenőrzések között kivárt intervallumok száma |
   | **Gyakoriság** | Yes | Hét | Az ismétlődéshez használni kívánt időegység |
   |||||

1. Az **intervallum** és a **gyakoriság** területen nyissa meg az **új paraméter hozzáadása** listát, és válassza ki ezeket a tulajdonságokat az triggerhez való hozzáadáshoz.

   * **Ezeken a napokon**
   * **Ezekben az órákban**
   * **Ezekben a percekben**

   ![Képernyőkép: a megnyitott "új paraméter hozzáadása" és ezek a kiválasztott tulajdonságok: "ezen napokon", "ezekben az órákban" és "ezen a percekben".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Most állítsa be a további tulajdonságok értékeit az itt látható módon.

   ![A következő táblázatban leírtak szerint az értékekre beállított további tulajdonságokat bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Ezeken a napokon** | Hétfő, kedd, szerda, csütörtök, péntek | Ez a beállítás csak akkor érhető el, ha a **gyakoriságot** a **hétre** állítja be. |
   | **Ezekben az órákban** | 7, 8, 9 | Ez a beállítás csak akkor érhető el, ha a **gyakoriságot** **hét** vagy **nap** értékre állítja be. Ehhez az ismétlődéshez válassza ki a nap óráját. Ez a példa a (z) `7` , `8` , és `9` órás jelöléseket futtatja. |
   | **Ezekben a percekben** | 0, 15, 30, 45 | Ez a beállítás csak akkor érhető el, ha a **gyakoriságot** **hét** vagy **nap** értékre állítja be. Ehhez az ismétlődéshez válassza ki a nap percét. Ez a példa nulla órás jelöléssel kezdődik, és 15 percenként fut. |
   ||||

   Ez az eseményindító minden hétköznap, 15 percenként aktiválódik, 7:00-tól egészen 9:45-ig. Az **Előnézet** mező az ismétlődési ütemezést mutatja. További információkért lásd: [Feladatok és a munkafolyamatok ütemezése](../connectors/connectors-native-recurrence.md) és [Munkafolyamat-műveletek és eseményindítók](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Ha most el szeretné rejteni az trigger részleteit, az alakzat címsorára kattintva csukja össze az alakzatot.

   ![Képernyőkép, amely az összecsukott trigger alakzatát mutatja.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

A logikai alkalmazás most már a Azure Portalban van, de a megadott ütemterv alapján nem végez mást, mint a trigger. Most adjunk hozzá egy műveletet, amely az eseményindítóra válaszol.

## <a name="get-the-travel-time-for-a-route"></a>Útvonal megtételéhez szükséges idő lekérése

Most, hogy van eseményindítója, adjon hozzá egy olyan [műveletet](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely lekéri az utazási időt két pont között. A Logic Apps biztosít egy összekötőt a Bing Térképek API-hoz, hogy könnyedén lekérhesse ezt az információt. Mielőtt hozzákezdene ehhez a feladathoz, ellenőrizze, hogy rendelkezik-e az oktatóanyag előfeltételeiben említett Bing Térképek API-kulccsal.

1. A Logic app Designerben az ismétlődési eseményindító alatt válassza az **új lépés** lehetőséget.

1. A **válasszon műveletet** területen válassza a **standard** lehetőséget. A keresőmezőbe írja be a kifejezést `bing maps` , majd válassza ki a **Get Route** nevű műveletet.

   ![Képernyőfelvétel: a "Bing Maps" műveletekkel szűrt "válasszon műveletet" lista, valamint az "útvonal lekérése" művelet van kiválasztva.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Ha nem rendelkezik Bing Térképek-kapcsolattal, a rendszer arra kéri, hogy hozzon létre egyet. Adja meg a kapcsolat adatait az ábrán látható módon, majd válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép, amely megjeleníti a Bing Maps-kapcsolatok mezőt a megadott névvel és a Bing Maps API-kulccsal.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Kapcsolat neve** | Yes | BingMapsConnection | Adja meg a kapcsolat nevét. Ez a példa a következőt használja: `BingMapsConnection`. |
   | **API-kulcs** | Yes | <*Bing-Maps-API-Key*> | Adja meg a korábban kapott Bing Maps API-kulcsot. Ha nem rendelkezik Bing Térképek-kulccsal, tudja meg, [hogyan kérhet le kulcsot](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Nevezze át a műveletet a következő leírással: `Get route and travel time with traffic` .

1. A műveletben nyissa meg az **új paraméter hozzáadása listát**, és válassza ki ezeket a tulajdonságokat.

   * **Optimalizálás**
   * **Távolság mértékegysége**
   * **Közlekedési mód**

   ![Képernyőfelvétel: "útvonal lekérése..." művelet az "optimalizálás", a "távolsági egység" és az "utazási mód" tulajdonság kiválasztásával.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Most adja meg az itt látható és itt leírt tulajdonságok értékeit.

   ![Képernyőfelvétel: az "útvonal lekérése" művelet további tulajdonságértékek megjelenítése.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **1. útvonalpont** | Yes | <*Start – hely*> | Az útvonal forrása. Ez a példa egy példa kezdő lakcímet határoz meg. |
   | **2. útvonalpont** | Yes | <*végső hely*> | Az útvonal célhelye. Ez a példa egy példa célként megadott címet határoz meg. |
   | **Optimalizálás** | No | timeWithTraffic | Az útvonal optimalizálására szolgáló paraméter (például távolság, utazási idő a jelenlegi forgalom mellett stb.). Válassza ki a **timeWithTraffic** paraméter értékét. |
   | **Távolság mértékegysége** | No | <*saját preferencia*> | Az útvonalhoz használt távolság-mértékegység. Ez a példa a **Milet** használja egységként. |
   | **Közlekedési mód** | No | Vezetés | Az útvonalhoz használt közlekedési mód. Válassza a **vezetési** mód lehetőséget. |
   |||||

   További információ ezekről a paraméterekről és értékekről: [Route kiszámítása](/bingmaps/rest-services/routes/calculate-a-route).

1. A tervező eszköztárán válassza a **Mentés** lehetőséget.

Ezután hozzon létre egy változót a jelenlegi utazási idő átalakításához, és másodpercek helyett percekként való tárolásához. Így nem kell megismételnie az átalakítást, és egyszerűbben használhatja az értéket a későbbi lépésekben. 

## <a name="create-a-variable-to-store-travel-time"></a>Az utazási idő tárolására szolgáló változó létrehozása

Időnként előfordulhat, hogy a munkafolyamatban lévő adatokon szeretné futtatni a műveleteket, majd az eredményeket a későbbi műveletekben használja. Ha ezeket az eredményeket egyszerűen újra fel szeretné használni vagy hivatkozni szeretne, létrehozhat olyan változókat, amelyek az eredményeket a feldolgozás után tárolják. A logikai alkalmazásban csak a legfelső szinten hozhat létre változókat.

Alapértelmezés szerint az **Útvonal lekérése** művelet a jelenlegi utazási időt adja vissza az **utazási időtartam forgalmi** tulajdonságának másodpercben megadott forgalmával. Ha átalakítja, és inkább percekként tárolja az értéket, később átalakítás nélkül, egyszerűbben tudja felhasználni.

1. A tervezőben az **Útvonal lekérése** művelet alatt válassza az **új lépés** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be a kifejezést `variables` , majd válassza ki az **inicializálási változó** nevű műveletet.

   ![A "változó inicializálása" műveletet megjelenítő képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Nevezze át a műveletet a következő leírásra: `Create variable to store travel time`

1. Adja meg ezt az információt a változóhoz a táblázatban látható módon, valamint a táblázat alatti lépésekben:

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Yes | travelTime | A változó neve. Ez a példa a következőt használja: `travelTime`. |
   | **Típus** | Yes | Egész szám | A változó adattípusa |
   | **Érték** | No | Egy kifejezés, amely másodpercről percre alakítja át az aktuális utazási időt (lásd a táblázat lépéseit). | A változó kezdeti értéke |
   |||||

   1. Az **érték** tulajdonsághoz tartozó kifejezés létrehozásához kattintson a szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. Ha szükséges, bővítse a böngészőt, amíg meg nem jelenik a dinamikus lista. A dinamikus tartalom listában válassza a **kifejezés** lehetőséget, amely a kifejezés-szerkesztőt jeleníti meg.

      ![Képernyőfelvétel: "változó inicializálása" művelet, amely az "érték" tulajdonságban található kurzort jeleníti meg, amely megnyitja a dinamikus tartalom listáját.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      A dinamikus tartalom lista megjeleníti a korábbi műveletekből származó kimeneteket, amelyek elérhetők a munkafolyamatban a következő műveletek bemenetként való kiválasztásához. A dinamikus tartalom lista egy kifejezés-szerkesztőt tartalmaz, amely segítségével kiválaszthatja a kifejezésben műveleteket végrehajtó függvényeket. Ez a kifejezés-szerkesztő csak a dinamikus tartalmak listájában érhető el.

   1. A kifejezésszerkesztőbe írja be a következő kifejezést: `div(,60)`

      ![Képernyőkép, amely a "div (, 60)" kifejezéssel megadott kifejezés-szerkesztőt jeleníti meg.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. A kifejezésen belül vigye a kurzort a bal oldali zárójel (**(**) és a vessző (**,**) között, és válassza a **dinamikus tartalom** lehetőséget.

      ![Képernyőfelvétel: a "div (, 60)" kifejezésben a "dinamikus tartalom" lehetőséggel jelölt hely helye.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. A dinamikus tartalom listában válassza ki a tulajdonság értékét, majd az **utazási időtartam forgalmat**.

      ![Képernyőfelvétel: az "utazás időtartama forgalom" tulajdonság értéke kiválasztva.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Ha a tulajdonság értéke feloldódik a kifejezésen belül, válassza az **OK** gombot.

      ![Képernyőkép, amely a kijelölt "OK" gombot jeleníti meg.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      Az **érték** tulajdonság most megjelenik az itt látható módon:

      ![Képernyőkép, amely az "érték" tulajdonságot jeleníti meg a megoldott kifejezéssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Mentse a logikai alkalmazást.

Ezután adjon hozzá egy feltételt, amely ellenőrzi, hogy a jelenlegi utazás idő meghaladja-e a megadott határértéket.

## <a name="compare-the-travel-time-with-limit"></a>Az utazási idő összehasonlítása a korláttal

1. A **create változó az utazási idő tárolására** művelet alatt válassza az **új lépés** lehetőséget.

1. A **válasszon műveletet** területen válassza a **beépített** lehetőséget. A keresőmezőbe írja be a `condition` kifejezést. a műveletek listáról válassza ki a **feltétel** nevű műveletet.

   ![A "feltétel" kijelölt műveletet megjelenítő képernyőkép](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Nevezze át a feltételt a következő leírásra: `If travel time exceeds limit`

1. Hozzon létre egy olyan feltételt, amely ellenőrzi, hogy a **travelTime** tulajdonság értéke meghaladja-e a megadott korlátot az itt leírt módon:

   1. A feltétel bal oldalán kattintson az **érték választása** mezőre.

   1. A megjelenő dinamikus tartalmak listájából válassza ki a **travelTime** nevű tulajdonságot a **változók** területen.

      ![Képernyőfelvétel: a feltétel bal oldalán lévő "válasszon egy értéket" mező a dinamikus tartalmak listájából, a "travelTime" tulajdonság pedig be van jelölve.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. A középső összehasonlítás mezőben válassza ki a nevű operátort, **amely nagyobb, mint**.

   1. A feltétel jobb oldalán, a **Válasszon értéket** mezőben adja meg a következő korlátot: `15`

      Ha elkészült, a feltétel a következő példához hasonlóan néz ki:

      ![Képernyőfelvétel: az utazási idő a megadott korláttal való összehasonlításának befejezett állapota.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Mentse a logikai alkalmazást.

Ezután adja hozzá a futtatandó műveletet, ha az utazási idő meghaladja a korlátot.

## <a name="send-email-when-limit-exceeded"></a>E-mail küldése a határérték túllépésekor

Most adjon hozzá egy műveletet, amely e-mailt küld, ha az utazási idő meghaladja a korlátot. Az e-mail a jelenlegi utazási időt és a megadott útvonal teljesítéséhez szükséges többletidőt tartalmazza.

1. A feltétel **igaz** ágában válassza a **művelet hozzáadása** lehetőséget.

1. A **válasszon műveletet** területen válassza a **standard** lehetőséget. A keresőmezőbe írja be a `send email` kifejezést. A lista számos eredményt ad vissza, így a lista szűréséhez először válassza ki a kívánt e-mail-összekötőt.

   Ha például az Outlook e-mail fiókja van, válassza ki az összekötőt a fiókja típusaként:

   * Munkahelyi vagy iskolai Azure-fiókok esetében válassza az **Office 365 Outlook** lehetőséget.
   * Személyes Microsoft-fiókok esetében válassza az **Outlook.com** lehetőséget.

   Ez a példa az Office 365 Outlook kiválasztásával folytatódik.

   ![Képernyőfelvétel: "a műveletek listájának kiválasztása" a "standard" kategóriával és az "Office 365 Outlook" összekötő kiválasztásával.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Ha az összekötő műveletei megjelennek, válassza ki az e-mailt küldő műveletet, például:

   ![Az "e-mail küldése" művelet bejelölését bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Ha még nem rendelkezik kapcsolattal, jelentkezzen be, és hitelesítse a hozzáférést az e-mail-fiókjához, amikor a rendszer kéri.

   A Azure Logic Apps létrehozza az e-mail-fiókjához való kapcsolódást.

1. Nevezze át a műveletet a következő leírásra: `Send email with travel time`

1. A **to** tulajdonsághoz adja meg a címzett e-mail-címét. Tesztelési célból használhatja az e-mail-címét.

1. A **Tárgy** tulajdonsághoz adja meg az e-mail tárgyát, és az alábbi lépéseket követve foglalja bele a **travelTime** változót:

   1. Írja be a `Current travel time (minutes):` szöveget, záró szóközzel. Tartsa a kurzort a **Tárgy** mezőben, hogy a dinamikus tartalmak listája nyitva maradjon.

   1. A dinamikus tartalom lista **változók** fejlécében válassza a **több** megjelenítése lehetőséget, hogy megjelenjen a **travelTime** nevű változó.

      ![Képernyőfelvétel: a dinamikus tartalmak listája a "változók" szakasszal és a "továbbiak" beállítással jelenik meg.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > A dinamikus tartalom lista nem jeleníti meg automatikusan a **travelTime** változót, mert a **tulajdonos** tulajdonság egy sztring értéket vár, míg a **travelTime** egész érték.

      ![Képernyőkép, amely megjeleníti a dinamikus tartalmak listáját a kiválasztott "travelTime" változóval.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. A **Body (törzs** ) tulajdonság esetében az alábbi lépéseket követve határozza meg az e-mail törzsének tartalmát:

   1. Írja be a `Add extra travel time (minutes):` szöveget, záró szóközzel. Tartsa a kurzort a **törzs** mezőben, hogy a dinamikus tartalmak listája nyitva maradjon.

   1. A dinamikus tartalom listában válassza a **kifejezés** lehetőséget, amely a kifejezés-szerkesztőt jeleníti meg.

      ![Képernyőkép, amely a dinamikus tartalmak listáját jeleníti meg "Expression" jelöléssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. A kifejezés-szerkesztőben adja meg `sub(,15)` a határértéket meghaladó percek számát: 

      ![A "Sub (, 15)" kifejezéssel megadott kifejezés szerkesztőjét bemutató képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. A kifejezésen belül vigye a kurzort a bal oldali zárójel (**(**) és a vessző (**,**) között, és válassza a **dinamikus tartalom** lehetőséget.

      ![Képernyőfelvétel: a "Sub (, 15)" kifejezésben a "dinamikus tartalom" lehetőséggel jelölt hely helye.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. A **Változók** alatt válassza a **travelTime** elemet.

      ![Képernyőkép, amely megjeleníti a dinamikus tartalmak listáját a kiválasztott "travelTime" változóval.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Miután a tulajdonság feloldódik a kifejezésen belül, kattintson az **OK gombra**.

      ![A dinamikus tartalmak listáját és az "OK" elemet megjelenítő képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      Ekkor megjelenik a **Body (törzs** ) tulajdonság, ahogy az itt látható:

      ![Képernyőkép, amely megjeleníti a dinamikus tartalom listáját az e-mail-művelet "Body" tulajdonságában megoldott kifejezéssel.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Mentse a logikai alkalmazást.

Ezután tesztelje és futtassa a logikai alkalmazást, amely most az alábbi példához hasonlóan néz ki:

![A befejezett példa Logic app-munkafolyamatot bemutató képernyőkép](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>A logikai alkalmazás futtatása

A logikai alkalmazás manuális elindításához a tervező eszköztárán válassza a **Futtatás** lehetőséget.

* Ha a jelenlegi utazási idő a korlát alatt marad, a logikai alkalmazás nem tesz mást, és megvárja, vagy a következő intervallumot, mielőtt újra ellenőriz. 

* Ha a jelenlegi utazási idő meghaladja a korlátot, egy e-mailt kap az aktuális utazási idővel és a korlát feletti percek számával. Íme egy példa a logikai alkalmazás által küldött e-mailre:

  ![Képernyőfelvétel: az aktuális utazási időt és a megadott korlátot meghaladó utazási időt bemutató e-mail-cím.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Ha nem kap e-mailt, ellenőrizze a levélszemét mappát. Előfordulhat, hogy az ilyen típusú levelek fennakadnak a levélszemétszűrőn. Ha nem biztos abban, hogy a logikai alkalmazás megfelelően futott-e, tekintse meg a [logikai alkalmazás hibaelhárításával foglalkozó szakaszt](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulálunk, sikeresen létrehozott és futtatott egy ütemezésen alapuló, ismétlődő logikai alkalmazást. 

Az **ismétlődési** eseményindítót használó egyéb logikai alkalmazások létrehozásához tekintse meg ezeket a sablonokat, amelyek a logikai alkalmazás létrehozása után érhetők el:

* Napi emlékeztetők küldésének beállítása.
* Régebbi Azure-blobok törlése.
* Üzenet hozzáadása egy Azure Storage-üzenetsorhoz.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A logikai alkalmazás továbbra is fut, amíg le nem tiltja vagy nem törli az alkalmazást. Ha már nincs szüksége a minta logikai alkalmazásra, törölje a logikai alkalmazást és a kapcsolódó erőforrásokat tartalmazó erőforráscsoportot.

1. A Azure Portal keresési mezőjébe írja be a létrehozott erőforráscsoport nevét. Az eredmények között az **erőforráscsoportok** területen válassza ki az erőforráscsoportot.

   Ez a példa létrehozta a nevű erőforráscsoportot `LA-TravelTime-RG` .

   ![A "La-Travel-Time-RG" beírt és * * LA-TravelTime-RG * * elemet megjelenítő képernyőkép.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Ha az Azure kezdőlapján megjelenik az erőforráscsoport a **legutóbbi erőforrások** területen, akkor a kezdőlapon kiválaszthatja a csoportot.

1. Az erőforráscsoport menüben válassza az **Áttekintés** lehetőséget. Az **Áttekintés** ablaktábla eszköztárán válassza az **erőforráscsoport törlése** elemet.

   ![Képernyőfelvétel: az erőforráscsoport "áttekintés" panelje, valamint a panel eszköztárán az "erőforráscsoport törlése" lehetőség van kiválasztva.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. A megjelenő megerősítő panelen adja meg az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy logikai alkalmazást, amely egy megadott ütemterv (hétköznap reggel) alapján ellenőrzi a forgalmat, és végrehajtja a műveletet (e-mailt küld), ha az utazási idő meghaladja a megadott korlátot. Ebből a témakörből megtudhatja, hogyan hozhat létre olyan logikai alkalmazást, amely levelezési listákat küld a jóváhagyásra az Azure-szolgáltatások, a Microsoft-szolgáltatások és más szolgáltatott szoftveres (SaaS) alkalmazások integrálásával.

> [!div class="nextstepaction"]
> [Levelezési listára vonatkozó kérelmek kezelése](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
