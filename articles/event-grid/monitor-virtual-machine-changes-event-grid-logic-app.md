---
title: Virtuális gépek változásainak figyelése – Azure Event Grid & Logic Apps
description: A virtuális gépek (VM-EK) változásainak keresése Azure Event Grid és Logic Apps használatával
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 1a5d8c36382433024efd1f1cc6ba9fd878d28ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329525"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Oktatóanyag: Virtuális gépek módosításainak monitorozása az Azure Event Grid és a Logic Apps használatával

Az Azure-erőforrásokban vagy harmadik féltől származó erőforrásokban megjelenő egyes események figyeléséhez és megválaszolásához a feladatokat a minimális kódot használó [logikai alkalmazások](../logic-apps/logic-apps-overview.md) létrehozásával automatizálhatja és futtathatja munkafolyamatként. Ezek az erőforrások az eseményeket az [Azure Event Gridben](../event-grid/overview.md)tehetik közzé. Az eseményrács ezután leküldi az eseményeket a végpontként üzenetsorokkal, webhookokkal vagy [eseményközpontokkal](../event-hubs/event-hubs-about.md) rendelkező feliratkozókra. Előfizetőként a logikai alkalmazás megvárhatja ezeket az eseményeket az Event gridből, mielőtt automatizált munkafolyamatokat futtat a feladatok végrehajtásához.

Például íme néhány esemény, amelyeket a közzétevők az Azure Event Grid szolgáltatáson keresztül küldhetnek a feliratkozóknak:

* Erőforrás létrehozása, olvasása, frissítése vagy törlése. Például monitorozhatja az olyan változásokat, amelyek költségekkel terhelhetik Azure-előfizetését, és befolyásolhatják az egyenlegét.

* Személy hozzáadása vagy eltávolítása egy Azure-előfizetésben.

* Az alkalmazás végrehajt egy bizonyos műveletet.

* Egy új üzenet jelenik meg egy üzenetsorban.

Ez az oktatóanyag egy logikai alkalmazást hoz létre, amely figyeli a virtuális gép módosításait, és e-maileket küld a változásokról. Amikor létrehoz egy logikai alkalmazást egy eseményfeliratkozással egy Azure-erőforrásra, az erőforrás eseményei az eseményrácson keresztül adódnak át a logikai alkalmazásnak. Az oktatóanyag ennek a logikai alkalmazásnak a létrehozását mutatja be:

![Képernyőkép a Logic Apps Designerről, amely a virtuális gép figyelésére szolgáló munkafolyamatot mutatja Event Grid használatával.](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az eseményeket egy eseményrácson keresztül monitorozó logikai alkalmazás létrehozása.
> * Feltétel hozzáadása, amely kifejezetten a virtuális gépek változásait figyeli.
> * E-mailek küldése a virtuális gép változása esetén.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az Logic Apps által támogatott e-mail-szolgáltatótól származó e-mail-fiók értesítések küldéséhez, például Office 365 Outlook, Outlook.com vagy gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/).

  Ez az oktatóanyag egy Office 365 Outlook-fiókot használ. Ha más e-mail-fiókot használ, az általános lépések ugyanazok, a felhasználói felület azonban némiképp eltérhet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Egy [virtuális gép](https://azure.microsoft.com/services/virtual-machines) , amely önmagában a saját Azure-erőforráscsoport. Ha még nem tette meg, hozzon létre egy virtuális gépet a virtuális gép [létrehozása oktatóanyag](../virtual-machines/windows/quick-create-portal.md)használatával. Ahhoz, hogy a virtuális gép eseményeket tegyen közzé, [semmi egyebet nem kell tennie](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Üres logikai alkalmazás létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure-fiókja hitelesítő adataival.

1. Az Azure fő menüjében válassza az **erőforrás létrehozása**  >  **integrációs**  >  **logikai alkalmazás** lehetőséget.

   ![Képernyőkép a Azure Portalről, amely megjeleníti a gombot egy logikai alkalmazás erőforrásának létrehozásához.](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. A **logikai alkalmazás** területen adja meg a logikai alkalmazás erőforrásával kapcsolatos információkat. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   ![Képernyőkép a Logic apps létrehozási menüjéről, amely a következő adatokat jeleníti meg: név, előfizetés, erőforráscsoport és hely.](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Név** | Yes | <*logikai alkalmazás neve*> | Adjon egyedi nevet a logikai alkalmazásnak. |
   | **Előfizetés** | Yes | <*Azure-előfizetés – név*> | Válassza ki ugyanazt az Azure-előfizetést az oktatóanyag összes szolgáltatásához. |
   | **Erőforráscsoport** | Yes | <*Azure-Erőforrás-csoport*> | Az Azure-erőforráscsoport neve a logikai alkalmazáshoz, amelyet az oktatóanyag összes szolgáltatásához kiválaszthat. |
   | **Hely** | Yes | <*Azure-régió*> | Válassza ugyanazt a régiót az oktatóanyagban szereplő minden szolgáltatáshoz. |
   |||

1. Miután az Azure üzembe helyezte a logikai alkalmazást, a Logic Apps Designer egy bemutató videót és gyakran használt eseményindítókat tartalmazó oldalt jelenít meg. Görgessen át a videón és a triggereken.

1. A **Sablonok** szakaszban válassza az **Üres logikai alkalmazás** lehetőséget.

   ![Képernyőkép a Logic Apps-sablonokról, amely megjeleníti a kijelölést üres logikai alkalmazás létrehozásához.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   A Logic Apps Designer most megjeleníti a logikai alkalmazás elindításához használható [*eseményindítókat*](../logic-apps/logic-apps-overview.md#logic-app-concepts) . Mindegyik logikai alkalmazásnak egy eseményindítóval kell indulnia, amelyet egy adott esemény vagy adott feltételek teljesülése aktivál. A Azure Logic Apps a logikai alkalmazást futtató munkafolyamat-példányt hoz létre minden alkalommal, amikor az eseményindító bekövetkezik.

## <a name="add-an-event-grid-trigger"></a>Event Grid trigger hozzáadása

Most adja hozzá a Event Grid triggert, amelyet a virtuális géphez tartozó erőforráscsoport figyelésére használ.

1. A tervezőben, a keresőmezőbe írja be `event grid` szűrőként a kifejezést. Az eseményindítók listából válassza ki az **erőforrás-esemény bekövetkeztekor** eseményindítót.

   ![Képernyőkép a Logic Apps Designerről, amely a Event Grid eseményindító kiválasztását mutatja egy adott erőforrás-eseményen.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Ha a rendszer kéri, jelentkezzen be Azure Event Grid az Azure-fiókja hitelesítő adataival. A **bérlő** listában, amely az Azure-előfizetéshez társított Azure Active Directory bérlőt jeleníti meg, győződjön meg arról, hogy a megfelelő bérlő jelenik meg, például:

   ![Képernyőkép a Logic Apps Designerről, amely az Azure bejelentkezési kérését mutatja be a Event Gridhoz való kapcsolódáshoz.](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Ha személyes Microsoft-fiókkal jelentkezett be (például @outlook.com vagy @hotmail.com), az Event Grid-trigger esetleg nem megfelelően jelenik meg. Megkerülő megoldásként válassza a [kapcsolat az egyszerű szolgáltatásnév](../active-directory/develop/howto-create-service-principal-portal.md)használatával lehetőséget, vagy az Azure-előfizetéshez társított Azure Active Directory tagjaként hitelesítse magát, például: *Felhasználónév* @emailoutlook.onmicrosoft.com .

1. Most fizessen elő a logikai alkalmazást a közzétevő eseményeire. Adja meg az esemény-előfizetés részleteit az alábbi táblázatban leírtak szerint, például:

   ![Képernyőkép a Logic Apps Designerről, amely megjeleníti az eseményindítók részletes szerkesztőjét, ha erőforrás-esemény történik.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Előfizetés** | Yes | <*Event-Publisher-Azure-előfizetés-név*> | Válassza ki az *esemény-közzétevőhöz* társított Azure-előfizetés nevét. Ebben az oktatóanyagban válassza ki a virtuális géphez tartozó Azure-előfizetés nevét. |
   | **Erőforrás típusa** | Yes | <*esemény – közzétevő – Azure-Resource típusú*> | Válassza ki az esemény-közzétevőhöz tartozó Azure-erőforrás típusát. Az Azure-erőforrásokkal kapcsolatos további információkért lásd: [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md). Ebben az oktatóanyagban válassza ki az `Microsoft.Resources.ResourceGroups` Azure-erőforráscsoportok figyelésére szolgáló értéket. |
   | **Erőforrás neve** |  Yes | <*esemény – közzétevő – Azure-Resource-Name*> | Válassza ki az esemény-közzétevőhöz tartozó Azure-erőforrás nevét. Ez a lista a kiválasztott erőforrástípus alapján változik. Ebben az oktatóanyagban válassza ki a virtuális gépet tartalmazó Azure-erőforráscsoport nevét. |
   | **Eseménytípus eleme** |  No | <*eseménytípus*> | Válasszon ki egy vagy több olyan eseménytípus-típust, amelyet szűrni szeretne, majd küldje el az Event Gridnek. Megadhatja például, hogy az ilyen típusú eseménytípus felderítse az erőforrások módosításait vagy törlését: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>További információt az alábbi témakörökben talál: <p><p>- [Erőforráscsoportok Azure Event Grid](../event-grid/event-schema-resource-groups.md) <br>- [Az események szűrésének megismerése](../event-grid/event-filtering.md) <br>- [Event Grid eseményeinek szűrése](../event-grid/how-to-filter-events.md) |
   | Opcionális tulajdonságok hozzáadásához válassza az **új paraméter hozzáadása** lehetőséget, majd válassza ki a kívánt tulajdonságokat. | No | {lásd a leírásokat} | * **Előtag-szűrő**: ebben az oktatóanyagban hagyja üresen ezt a tulajdonságot. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy előtagsztringet is, például egy elérési útvonalat és egy adott erőforrás paraméterét. <p>* **Utótag-szűrő**: ebben az oktatóanyagban hagyja üresen ezt a tulajdonságot. Az alapértelmezett beállítás minden értéket megenged. Azonban megadhat szűrőként egy utótagsztringet is, például egy fájlnévkiterjesztést, ha csak adott fájltípusokra kíváncsi. <p>* **Előfizetés neve**: ebben az oktatóanyagban megadhatja az esemény-előfizetés egyedi nevét. |
   |||

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget. A művelet részleteinek összecsukása és elrejtése a logikai alkalmazásban kattintson a művelet címsorára.

   ![Képernyőkép a Logic Apps Designerről, amely a Mentés gombot mutatja a munkafolyamat-módosítások mentéséhez.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Amikor ment egy eseményrács-triggerrel rendelkező logikai alkalmazást, az Azure automatikusan létrehoz egy eseményfeliratkozást a logikai alkalmazás számára a kiválasztott erőforráshoz. Így amikor az erőforrás közzétesz egy eseményt az eseményrácson, az eseményrács automatikusan leküldi az eseményt a logikai alkalmazásnak. Az esemény aktiválja a logikai alkalmazást, majd létrehozza és futtatja a következő lépésekben meghatározott munkafolyamat egy példányát.

A logikai alkalmazás most már működőképes, és figyeli az eseményrácsról érkező eseményeket, de amíg fel nem vesz a munkafolyamatba műveleteket, semmit nem csinál.

## <a name="add-a-condition"></a>Feltétel hozzáadása

Ha azt szeretné, hogy a logikai alkalmazás csak akkor fusson, amikor egy adott esemény vagy művelet történik, adjon hozzá egy feltételt, amely ellenőrzi a `Microsoft.Compute/virtualMachines/write` műveletet. Ha ez a feltétel teljesül, a logikai alkalmazás e-mailben elküldi a frissített virtuális gép adatait.

1. A Logic app Designerben az Event Grid eseményindító alatt válassza az **új lépés** lehetőséget.

   ![Képernyőkép a Logic Apps Designerről, amely a gombra kattintva új lépést adhat hozzá a munkafolyamathoz.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. A **válasszon műveletet** területen, a keresőmezőbe írja be `condition` szűrőként a kifejezést. A műveletek listából válassza ki a **feltétel** műveletet.

   ![Képernyőkép a Logic Apps Designerről, amely megjeleníti a gombot egy feltétel művelet hozzáadásához.](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   A Logic App Designer hozzáad egy üres feltételt a munkafolyamathoz, beleértve a feltétel igaz vagy hamis értékei esetén követendő műveleti útvonalakat.

   ![Képernyőkép a Logic Apps Designerről, amely a munkafolyamathoz hozzáadott üres feltételt jeleníti meg.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Nevezze át a feltétel címét a következőre: `If a virtual machine in your resource group has changed` . A feltétel címsorán válassza a három pontot (**...**), majd az **Átnevezés** lehetőséget.

   ![Képernyőkép a Logic Apps Designerről, amely megjeleníti a feltétel-szerkesztő helyi menüjét az Átnevezés lehetőség kiválasztásával.](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Hozzon létre egy olyan feltételt, amely egy olyan objektum eseményét ellenőrzi, `body` `data` amelyben a `operationName` tulajdonság egyenlő a `Microsoft.Compute/virtualMachines/write` művelettel. További tudnivalók az [Event Grid eseménysémáról](../event-grid/event-schema.md).

   1. Az **Ls** feltétel alatti első sorban kattintson a bal oldali mezőbe. A megjelenő dinamikus tartalmak listájában válassza a **kifejezés** lehetőséget.

      ![Képernyőkép a Logic Apps Designerről, amely megjeleníti a feltételt a kifejezés-szerkesztővel.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. A kifejezés-szerkesztőben adja meg ezt a kifejezést, amely visszaadja a művelet nevét az triggerből, majd kattintson **az OK gombra**:

      `triggerBody()?['data']['operationName']`

      Például:

      ![Képernyőkép a Logic Apps designerről, a művelet nevének kinyerése kifejezéssel.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. A középső mezőben tartsa meg az **is equal to** (egyenlő) operátort.

   1. A jobb oldali mezőben adja meg ezt az értéket, amely a figyelni kívánt művelet:

      `Microsoft.Compute/virtualMachines/write`

   A befejezett állapot most a következő példához hasonlít:

   ![Képernyőkép a Logic Apps Designerről, amely egy olyan feltételt mutat be, amely összehasonlítja a műveletet.](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Ha a tervező nézetből a kód nézetre vált, és vissza a Tervező nézetre, a feltételben megadott kifejezés az **adatok. operationName** tokenre van feloldva:

   ![Képernyőkép a Logic Apps Designerről, amely egy feloldott tokenekkel rendelkező feltételt mutat.](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Mentse a logikai alkalmazást.

## <a name="send-email-notifications"></a>E-mail-értesítések küldése

Most adjon hozzá egy [*műveletet*](../logic-apps/logic-apps-overview.md#logic-app-concepts) , hogy e-mailt kapjon, ha a megadott feltétel igaz.

1. A feltétel **Ha igaz** szakaszában válassza a **Művelet hozzáadása** lehetőséget.

   ![Képernyőkép a Logic Apps Designer feltételének szerkesztőjéről, majd a gomb egy művelet hozzáadásához, ha a feltétel igaz.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. A **válasszon műveletet** területen, a keresőmezőbe írja be `send an email` szűrőként a kifejezést. Az e-mail-szolgáltató alapján keresse meg és válassza ki a megfelelő összekötőt. Ezután válassza ki az „e-mail küldése” műveletet az összekötőn. Például:

   * Azure munkahelyi vagy iskolai fiókok esetében válassza az Office 365 Outlook-összekötőt.

   * Személyes Microsoft-fiókok esetében válassza az Outlook.com-összekötőt.

   * Gmail-fiókok esetében válassza a Gmail-összekötőt.

   Ez az oktatóanyag az Office 365 Outlook Connectort is folytatja. Ha más szolgáltatót használ, a lépések változatlanok maradnak, de a felhasználói felülete némileg eltérő lehet.

   ![Képernyőkép a Logic Apps Designerről, amely az Office 365 Outlook Connector E-mail küldési műveletének keresése című részében található.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Ha még nem csatlakozik e-mail-szolgáltatóhoz, jelentkezzen be e-mail-fiókjába, amikor a rendszer a hitelesítő adatok megadását kéri.

1. Nevezze át az e-mail küldése műveletet erre a címre: `Send email when virtual machine updated`

1. Adja meg az e-mailre vonatkozó információkat az alábbi táblázatban megadott módon:

   ![Képernyőkép a Logic Apps Designerről, amely azt mutatja be, hogy a dinamikus tartalom hálózatvezérlőt az e-mailek tárgyát képező sorba.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > A munkafolyamat előző lépéseiből származó kimenet kiválasztásához kattintson a szövegmezőbe, hogy megjelenjen a dinamikus tartalom lista, vagy válassza a **dinamikus tartalom hozzáadása** lehetőséget. További találatok esetén válassza a **továbbiak** lehetőséget a lista egyes szakaszaiban. A dinamikus tartalom listájának bezárásához válassza ismét a **dinamikus tartalom hozzáadása** lehetőséget.

   | Tulajdonság | Kötelező | Érték | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Ide:** | Yes | <*címzett \@ tartománya*> | Adja meg a címzett e-mail-címét. Tesztelési célokra használhatja a saját e-mail-címét. |
   | **Tárgy** | Yes | `Resource updated:`**Tárgy** | Adja meg az e-mail tárgymezőjének tartalmát. Ebben az oktatóanyagban adja meg a megadott szöveget, majd válassza ki az esemény **Tárgy** mezőjét. Itt az e-mail tárgya a frissített erőforrás (virtuális gép) nevét tartalmazza. |
   | **Törzs** | Yes | `Resource:` **Témakör** <p>`Event type:` **Eseménytípus**<p>`Event ID:` **ID (Azonosító)**<p>`Time:`**Esemény időpontja** | Adja meg az e-mail törzsének tartalmát. Ebben az oktatóanyagban adja meg a megadott szöveget, és válassza ki az esemény **témakörét**, az **eseménytípus**, az **azonosító** és az **esemény időpontja** mezőket, hogy az e-mail-cím tartalmazza az eseményt, az esemény típusát, az esemény időbélyegét és az esemény azonosítóját a frissítéshez. Ebben az oktatóanyagban az erőforrás az a triggerben kiválasztott Azure-erőforráscsoport. <p>Ha üres sorokat kíván beszúrni a tartalomba, nyomja le a Shift + Enter billentyűkombinációt. |
   ||||

   > [!NOTE]
   > Ha olyan mezőt választ ki, amely egy tömböt jelöl, a tervező automatikusan hozzáad egy **Mindegyikre** hurkot a tömbre hivatkozó művelet köré. Így a logikai alkalmazás a tömb mindegyik elemén végrehajtja az adott műveletet.

   Most az e-mail-művelet a következő példához hasonló lehet:

   ![Képernyőkép a Logic Apps Designerről, amely a virtuális gép frissítésekor e-mailben küldendő kimeneteket mutatja be.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   A befejezett logikai alkalmazás pedig a következő példához hasonló lehet:

   ![Képernyőkép a Logic Apps Designerről, amely az trigger és a műveletek részleteit tartalmazó létrehozott logikai alkalmazást jeleníti meg.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Mentse a logikai alkalmazást. Az egyes műveletek részleteinek összecsukása és elrejtése a logikai alkalmazásban válassza ki a művelet címsorát.

   A logikai alkalmazás most már működőképes, de mielőtt bármit is tenne, vár a virtuális gép változásaira. A logikai alkalmazás teszteléséhez folytassa a következő szakasszal.

## <a name="test-your-logic-app-workflow"></a>A logikai alkalmazás munkafolyamatának tesztelése

1. Annak ellenőrzéséhez, hogy a logikai alkalmazás megkapja-e a megadott eseményeket, frissítse a virtuális gépet.

   Például átméretezheti a virtuális gépet az Azure Portalon vagy az [Azure PowerShell használatával](../virtual-machines/windows/resize-vm.md).

   Pár pillanat múlva egy e-mailt kell kapnia. Például:

   ![Képernyőkép az Outlook e-mail-címéről, amely a virtuális gép frissítésének részleteit jeleníti meg.](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. A logikai alkalmazás futtatások és triggerek előzményeinek áttekintéséhez válassza az **Áttekintés** lehetőséget a logikai alkalmazás menüjében. A futtatással kapcsolatos további részletek megtekintéséhez válassza ki a futtatási sort.

   ![A logikai alkalmazás Áttekintés lapjának képernyőképe, amelyen a sikeres Futtatás van kiválasztva.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Az egyes lépések be- és kimeneteinek megtekintéséhez bontsa ki az áttekinteni kívánt egyes lépéseket. Ezek az információk segítenek diagnosztizálni és elhárítani a logikai alkalmazás hibáit.

   ![Képernyőkép a logikai alkalmazás futtatási előzményeiről, és megjeleníti az egyes futtatások részleteit.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Gratulálunk, sikeresen létrehozott és futtatott egy logikai alkalmazást, amely erőforrás-eseményeket monitoroz egy eseményrácson keresztül, és e-mailt küld az ilyen események bekövetkeztekor. Azt is megtanulta, milyen egyszerű a folyamatokat automatizáló, valamint a rendszereket és felhőalapú szolgáltatásokat integráló munkafolyamatok létrehozása.

Más konfigurációváltozásokat is monitorozhat eseményrácsok és logikai alkalmazások segítségével, például:

* Egy virtuális gép megkapja az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) jogosultságait.
* Egy hálózati adapter (NIC) egy hálózati biztonsági csoportja (NSG) módosul.
* Lemezeket adnak hozzá vagy törölnek egy virtuális gépen.
* Egy nyilvános IP-címet rendelnek egy virtuális gép hálózati adapteréhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóprogram olyan erőforrásokat és műveleteket alkalmaz, amelyek költségekkel terhelik Azure-előfizetését. Ezért miután végzett az oktatóanyaggal és a teszteléssel, mindenképp tiltsa le vagy inaktiválja azokat az erőforrásokat, amelyek költségeit nem szeretné fizetni.

* Ha a munkája törlése nélkül kívánja leállítani a logikai alkalmazást, akkor tiltsa le. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Az eszköztáron válassza a **Letiltás** lehetőséget.

  ![Képernyőkép a Logic app áttekintéséről, amely a logikai alkalmazás letiltásához a Letiltás gombot választotta.](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Ha nem látja a logikai alkalmazás menüjét, próbáljon meg visszatérni az Azure-irányítópultra, és nyissa meg ismét a logikai alkalmazást.

* A logikai alkalmazás végleges törléséhez a logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Az eszköztáron válassza a **Törlés** lehetőséget. Erősítse meg, hogy törölni kívánja a logikai alkalmazást, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Egyéni események létrehozása és átirányítása az Event Griddel](../event-grid/custom-event-quickstart.md)
