---
title: Automatizálási feladatok létrehozása az Azure-erőforrások kezeléséhez és monitorához
description: Olyan automatizált feladatokat állíthat be, amelyek segítenek az Azure-erőforrások kezelésében és a költségek monitorzában a felhőben futó Azure Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 0a98f9e4b108d2498fa19bc0b041f9d52272c7d2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774916"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Azure-erőforrások kezelése és a költségek monitorizálása automatizálási feladatok létrehozásával (előzetes verzió)

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az [Azure-erőforrások](../azure-resource-manager/management/overview.md#terminology) könnyebben kezelhetők, ha automatizált felügyeleti feladatokat hoz létre egy adott erőforráshoz vagy erőforráscsoporthoz automatizálási feladatsablonok használatával, amelyek az erőforrástípustól függően eltérő rendelkezésre állás szerint változnak. Például egy [Azure](../storage/common/storage-account-overview.md)Storage-fiókhoz létrehozhat egy automatizálási feladatot, amely elküldi Önnek a tárfiók havi költségeit. [Azure-beli virtuális gépekhez](https://azure.microsoft.com/services/virtual-machines/)létrehozhat egy automatizálási feladatot, amely előre meghatározott ütemezés szerint bekapcsolja vagy kikapcsolja a virtuális gépet.

A színfalak mögött az automatizálási feladat valójában egy, a [Azure Logic Apps](../logic-apps/logic-apps-overview.md) szolgáltatáson [](https://azure.microsoft.com/pricing/details/logic-apps/) futó munkafolyamat, amely ugyanazokkal a díjszabási díjszabással és díjszabási modellel [lesz számlázva.](../logic-apps/logic-apps-pricing.md) A feladat létrehozása után a logikaialkalmazás-tervezőben megnyitva megtekintheti és szerkesztheti a mögöttes munkafolyamatot. Ha egy feladat legalább egy futtatás után befejeződik, áttekintheti az egyes futtatás állapotát, előzményeit, bemenetét és kimenetét.

Az előzetes verzióban jelenleg elérhető feladatsablonok a következőek:

| Erőforrás típusa | Automation-feladatsablonok |
|---------------|---------------------------|
| Azure-erőforráscsoportok | **Erőforrás törlésekor** |
| Minden Azure-erőforrásban | **Erőforrás havi költségének küldése** |
| Azure-beli virtuális gépek | Továbbá: <p>- **Virtuális gép kikapcsolása** <br>- **Virtuális gép elindítani** |
| Azure Storage-fiókok | Továbbá: <p>- **Régi blobok törlése** |
| Azure Cosmos DB | Továbbá <p>- **Lekérdezési eredmény elküldése e-mailben** |
|||

Ez a cikk a következő feladatok elvégzését mutatja be:

* [Automatizálási feladat létrehozása](#create-automation-task) egy adott Azure-erőforráshoz.

* [Tekintse át a tevékenységek előzményeit,](#review-task-history)beleértve a futtatás állapotát, a bemeneteket, a kimeneteket és az egyéb előzményadatokat.

* [Szerkessze a](#edit-task) feladatot a feladat frissítéséhez, vagy szabja testre a feladat alapjául szolgáló munkafolyamatot a Logikaialkalmazás-tervezőben.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Miben különböznek az automatizálási feladatok a Azure Automation?

Jelenleg csak az erőforrás szintjén hozhat létre automatizálási feladatot, megtekintheti annak futtatáselőzményét, és szerkesztheti a tevékenység mögöttes logikaialkalmazás-munkafolyamatát, amelyet a Azure Logic Apps [működtet.](../logic-apps/logic-apps-overview.md) Az automatizálási feladatok alapszintűek és egyszerűsítettek, mint [Azure Automation.](../automation/automation-intro.md)

Ezzel szemben a Azure Automation egy felhőalapú automatizálási és konfigurációs szolgáltatás, amely támogatja az Azure- és nem Azure-környezetek egységes felügyeletét. A szolgáltatás [](../automation/automation-intro.md#process-automation) folyamatautomatizálást tartalmaz a folyamatok [runbookokkal](../automation/automation-runbook-execution.md)való vezényléhez, a konfigurációkezelés változáskövetéssel és leltárral, [](../automation/change-tracking/overview.md)frissítéskezeléssel, megosztott képességekkel és heterogén funkciókkal. Az Automation teljes körű vezérlést biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A kezelni kívánt Azure-erőforrás. Ez a cikk egy Azure Storage-fiókot használ példaként.

* Office 365-fiók, ha követni szeretné a példát, amely e-mailt küld Önnek az Office 365 Outlook használatával.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Automatizálási feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)keresse meg a kezelni kívánt erőforrást.

1. Az erőforrásmenüben görgessen az **Automation szakaszhoz,** és válassza a Feladatok **lehetőséget**

   ![Képernyőkép a Azure Portal és egy tárfiók-erőforrásmenüről, ahol az "Automation" szakaszban ki van jelölve a "Feladatok" menüpont.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. A Feladatok **panelen** válassza a Hozzáadás **lehetőséget,** hogy kiválaszthat egy feladatsablont.

   ![Képernyőkép a tárfiók "Feladatok" panelről, ahol az eszköztáron ki van jelölve a "Hozzáadás" elem](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. A Feladat **hozzáadása panelEn** **a Sablon** kiválasztása alatt válassza ki a létrehozni kívánt feladat sablonját. Ha a következő oldal nem jelenik meg, válassza a **Tovább: Hitelesítés lehetőséget.**

   A példa folytatódik az Erőforrás-feladat havi **költségének küldése sablon** kiválasztásával.

   ![Képernyőkép a "Havi erőforrásköltség küldése" és a "Következő: Hitelesítés" beállításról](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. A **Hitelesítés** területen, a  Kapcsolatok szakaszban válassza a Létrehozás lehetőséget a feladatban megjelenő összes kapcsolathoz, hogy minden kapcsolathoz meg tudja adni a hitelesítő adatokat.  Az egyes feladatokban a kapcsolattípusok a feladattól függően eltérőek lehetnek.

   Ez a példa csak az egyik olyan kapcsolatot mutatja be, amely ehhez a feladathoz szükséges.

   ![Képernyőkép a kiválasztott "Létrehozás" lehetőségről a Azure Resource Manager kapcsolatban](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Amikor a rendszer kéri, jelentkezzen be Az Azure-fiókja hitelesítő adataival.

   ![A "Bejelentkezés" kijelölést bemutató képernyőkép](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Minden sikeresen hitelesített kapcsolat az alábbi példához hasonló:

   ![A sikeresen létrehozott kapcsolatot bemutató képernyőkép](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Az összes kapcsolat hitelesítése után válassza a **Tovább:** Konfigurálás lehetőséget, ha a következő oldal nem jelenik meg.

1. A **Konfiguráció alatt** adja meg a feladat nevét és a feladathoz szükséges egyéb információkat. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   > [!NOTE]
   > A feladat neve létrehozás után nem módosítható, ezért a mögöttes munkafolyamat szerkesztésekor vegye figyelembe, hogy a név továbbra is [érvényes.](#edit-task-workflow) A mögöttes munkafolyamaton végrehajtott módosítások csak a létrehozott feladatra vonatkoznak, a feladatsablonra nem.
   >
   > Ha például nevet ad a feladatnak, de később úgy szerkeszti a mögöttes munkafolyamatot, hogy hetente fusson, nem módosíthatja a feladat nevét a `Send monthly cost` következőre: `Send weekly cost` .

   Az e-mail-értesítéseket küldünk feladatokhoz e-mail-címre van szükség.

   ![A kiválasztott feladathoz szükséges információkat bemutató képernyőkép](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   A létrehozott, automatikusan élő és futó feladat megjelenik az **Automation-feladatok listájában.**

   ![Képernyőkép az Automatizálási feladatok listájáról](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Ha a feladat nem jelenik meg azonnal, próbálja meg frissíteni a feladatlistát, vagy várjon egy kicsit a frissítés előtt. Az eszköztáron válassza a Frissítés **lehetőséget.**

   A kiválasztott feladat futtatása után az alábbi példához hasonló e-mailt kap:

   ![Képernyőkép a feladat által küldött e-mail-értesítésekről](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Feladatelőzmények áttekintése

A tevékenységek futtatás előzményeinek, valamint azok állapotának, bemenetének, kimenetének és egyéb információinak megtekintéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)keresse meg azt az erőforrást, amely az áttekintni kívánt tevékenységelőzményeket is megtalálja.

1. Az erőforrás menüjének Beállítások területén **válassza** az **Automation-feladatok lehetőséget.**

1. A feladatok listájában keresse meg az áttekintni kívánt feladatot. A tevékenység Futtatás **oszlopában** válassza a Nézet **lehetőséget.**

   ![Képernyőkép egy feladatról és a kiválasztott "Megtekintés" lehetőségről](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   A **Futtatás előzményei** panel megjeleníti a feladat összes futtatását azok állapotával, kezdési idejével, azonosítóival és futási időtartamával együtt.

   ![Képernyőkép egy tevékenység futtatásáról, azok állapotáról és egyéb információkról](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Itt a futtatás lehetséges állapotai:

   | Állapot | Leírás |
   |--------|-------------|
   | **Törölt** | A feladat futás közben megszakadt. |
   | **Sikertelen** | A tevékenységnek legalább egy sikertelen művelete van, de nem léteztek további műveletek a hiba kezelésére. |
   | **Futó** | A feladat jelenleg fut. |
   | **Sikeres** | Minden művelet sikeres volt. Egy feladat akkor is sikeresen befejeződhet, ha egy művelet sikertelen volt, de a hiba kezelésére egy későbbi művelet is létezik. |
   | **Várakozó** | A futtatás még nem indult el, és szüneteltetve van, mert a feladat egy korábbi példánya még fut. |
   |||

   További információ: Futtatás [előzményeinek áttekintése](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Egy futtatás egyes lépésének állapot- és egyéb információit a futtatás állapotának és egyéb információinak megtekintéséhez válassza ki.

   Megnyílik **a logikai alkalmazás futtatás** panelje, és megjeleníti a futtatott mögöttes munkafolyamatot.

   * A munkafolyamatok mindig egy eseményindítóval [*indulnak.*](../connectors/apis-list.md#triggers) Ehhez a feladathoz a munkafolyamat az Ismétlődés [ **eseményindítóval** indul.](../connectors/connectors-native-recurrence.md)

   * Minden lépés az állapotát és a futtatás időtartamát jeleníti meg. A 0 másodperces időtartamú lépések futtatása 1 másodpercnél kevesebb időt vett át.

   ![Képernyőkép a futtatás, az állapot és a futtatás időtartamának egyes lépésiről](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Az egyes lépés bemenetek és kimenetek áttekintéséhez válassza ki a kibontott lépést.

   Ez a példa az Ismétlődés eseményindító bemenetét mutatja be, amelynek nincsenek kimenetei, mert az eseményindító csak azt határozza meg, hogy mikor fut a munkafolyamat, és nem ad kimenetet a további feldolgozható műveletekhez.

   ![Képernyőkép a kibontott eseményindítóról és bemenetről](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Ezzel szemben az **E-mail** küldése művelet a munkafolyamat korábbi műveleteiből és kimeneteiből származó bemeneteket is biztosít.

   ![Képernyőkép egy kibontott műveletről, bemenetről és kimenetről](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Ha meg szeretne ismerkedni azzal, hogyan hozhat létre saját automatizált munkafolyamatokat, hogy alkalmazásokat, adatokat, szolgáltatásokat és rendszereket integrálhat az Azure-erőforrások automatizálási feladatainak kontextusán kívül, olvassa el a rövid útmutató: Az első integrációs munkafolyamat létrehozása az [Azure Logic Apps használatával – Azure Portal.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="edit-task"></a>

## <a name="edit-the-task"></a>A feladat szerkesztése

A feladatok a következő beállításokkal változtatják meg a feladatot:

* [Szerkessze a feladatot "beágyazottan",](#edit-task-inline) így módosíthatja a feladat tulajdonságait, például a kapcsolati vagy konfigurációs adatokat, például az e-mail-címét.

* [Szerkessze a feladat mögöttes munkafolyamatát](#edit-task-workflow) a Logikaialkalmazás-tervezőben.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>A feladat beágyazott szerkesztése

1. A [Azure Portal](https://portal.azure.com)keresse meg azt az erőforrást, amely a frissíteni kívánt feladattal rendelkezik.

1. Az erőforrás menüjének Automatizálás **területén** válassza a Feladatok **lehetőséget.**

1. A feladatok listájában keresse meg a frissíteni kívánt feladatot. Nyissa meg a tevékenység három pont (**...**) menüjét, és válassza a **Szerkesztés a következő sorban lehetőséget:**.

   ![Képernyőkép a megnyitott három pont menüről és a kiválasztott "Szerkesztés a következőben" lehetőségről](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Alapértelmezés szerint **megjelenik a** Hitelesítés lap, és megjeleníti a meglévő kapcsolatokat.

1. Új hitelesítő adatok hozzáadásához vagy egy kapcsolat meglévő hitelesítő adatainak kiválasztásához nyissa meg a kapcsolat három  pont **(...**) menüjét, és válassza az Új kapcsolat hozzáadása lehetőséget, vagy ha elérhető, más hitelesítő adatokat.

   ![Képernyőkép a Hitelesítés lapról, a meglévő kapcsolatokról és a kiválasztott három pont menüről](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Más feladattulajdonságok frissítéséhez válassza a **Tovább: Konfiguráció lehetőséget.**

   A példában található feladat egyetlen szerkeszthető tulajdonsága az e-mail-cím.

   ![A Konfiguráció lapot bemutató képernyőkép](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>A feladat alapjául szolgáló munkafolyamat szerkesztése

Amikor módosítja egy automatizálási feladat mögöttes munkafolyamatát, a módosítások csak a létrehozott feladatpéldányra vannak hatással, a feladatot létrehozó sablonra nem. Miután végrehajtotta és elmenti a módosításokat, előfordulhat, hogy az eredeti feladathoz megadott név már nem írja le pontosan a feladatot, ezért előfordulhat, hogy újra létre kell hoznia a feladatot egy másik névvel.

> [!TIP]
> Ajánlott eljárásként klónozza a mögöttes munkafolyamatot, hogy ehelyett a másolt verziót tudja szerkeszteni. Így a módosításokat a másolaton is futtathatja és tesztelheti, miközben az eredeti automatizálási feladat továbbra is működik és fut anélkül, hogy ez megzavarja vagy működéskimaradást okoz. Miután befejezte a módosításokat, és elégedett az új verzió sikeres futásával, letilthatja vagy törölheti az eredeti automatizálási feladatot, és használhatja a klónozott verziót az automatizálási feladathoz. A következő lépések a munkafolyamat klónozásának mikéntjére vonatkozó információkat tartalmaznak.

1. A [Azure Portal](https://portal.azure.com)keresse meg azt az erőforrást, amely a frissíteni kívánt feladattal rendelkezik.

1. Az erőforrás menüjének Automatizálás **területén** válassza a Feladatok **lehetőséget.**

1. A feladatok listájában keresse meg a frissíteni kívánt feladatot. Nyissa meg a tevékenység három pont (**...**) menüjét, és válassza a **Megnyitás** a Logic Apps.

   ![Képernyőkép a megnyitott három pont menüről és a "Megnyitás az Logic Apps" lehetőségről](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   A tevékenység mögöttes munkafolyamata megnyílik a Azure Logic Apps  szolgáltatásban, és megjeleníti az Áttekintés panelt, ahol megtekintheti a feladathoz elérhető futtatáselőzményeket.

   ![Képernyőkép a feladatról a Azure Logic Apps nézetben, az Áttekintés panel kijelölve](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. A mögöttes munkafolyamat a Logikaialkalmazás-tervezőben való megnyitásához válassza a logikai alkalmazás menüjének **Logikaialkalmazás-tervező elemét.**

   ![Képernyőkép a "Logikaialkalmazás-tervező" menüpont kiválasztott és a tervezőfelületről az alapul szolgáló munkafolyamattal](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Most szerkesztheti a munkafolyamat eseményindítója és a műveletek tulajdonságait, valamint szerkesztheti az eseményindítót és a munkafolyamatot meghatározó műveleteket. Ajánlott eljárásként azonban kövesse a munkafolyamat klónozásának lépéseit, hogy a módosításokat egy példányon hajtsa végre, miközben az eredeti munkafolyamat továbbra is működik és fut.

1. A munkafolyamat klónozásához és a másolt verzió szerkesztéséhez kövesse az alábbi lépéseket:

   1. A logikai alkalmazás munkafolyamat-menüjében válassza az Áttekintés **lehetőséget.**

   1. Az áttekintő panel eszköztárán válassza a **Klónozás lehetőséget.**

   1. A logikai alkalmazás létrehozási panelen a **Név alatt** adjon meg egy új nevet a másolt logikai alkalmazás munkafolyamatának.

      A Logikai **alkalmazás állapota kivételével** a többi tulajdonság nem szerkeszthető. 
      
   1. A **Logikai alkalmazás állapota alatt** válassza a **Letiltva** lehetőséget, hogy a klónozott munkafolyamat ne fusson a módosítások közben. Ha készen áll a módosítások tesztelésére, engedélyezheti a munkafolyamatot.

   1. Miután az Azure befejezte a klónozott munkafolyamat üzembezását, keresse meg és nyissa meg a munkafolyamatot a Logic App Designerben.

1. Az eseményindító vagy művelet tulajdonságainak megtekintéséhez bontsa ki az eseményindítót vagy műveletet.

   Például módosíthatja az Ismétlődés eseményindítót úgy, hogy ne havonta, hanem hetente fusson.

   ![Képernyőkép a kibontott Ismétlődés eseményindítóról, megnyitott Gyakoriság listával az elérhető gyakorisági beállításokhoz](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Az Ismétlődés eseményindítóval kapcsolatos további információkért lásd: Ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és [futtatása az Ismétlődés eseményindítóval.](../connectors/connectors-native-recurrence.md) További információ az egyéb használható eseményindítókról és műveletekről: [Összekötők](../connectors/apis-list.md)a Azure Logic Apps.

1. A módosítások mentéséhez a tervező eszköztárán válassza a Mentés **lehetőséget.**

   ![A tervező eszköztárát és a kiválasztott "Mentés" parancsot bemutató képernyőkép](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. A frissített munkafolyamat teszteléséhez és futtatásához a tervező eszköztárán válassza a **Futtatás lehetőséget.**

   A futtatás befejezése után a tervező megjeleníti a munkafolyamat futtatásának részleteit.

   ![A munkafolyamat tervezőben való futtatásának részleteit bemutató képernyőkép](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Ha le szeretné tiltani a munkafolyamatot, hogy a feladat ne folytatódjon, tekintse meg a Logikai alkalmazások kezelése szakaszt a [Azure Portal.](../logic-apps/manage-logic-apps-with-azure-portal.md)

## <a name="provide-feedback"></a>Visszajelzés küldése

Szívesen meghallgatjuk a hangod! Ha hibát szeretne jelenteni, visszajelzést szeretne küldeni, vagy kérdéseket szeretne feltenni ezzel az előzetes verziójú képességgel kapcsolatban, lépjen kapcsolatba [a Azure Logic Apps csapatával.](mailto:logicappspm@microsoft.com)

## <a name="next-steps"></a>Következő lépések

* [Logikai alkalmazások kezelése a Azure Portal](../logic-apps/manage-logic-apps-with-azure-portal.md)
