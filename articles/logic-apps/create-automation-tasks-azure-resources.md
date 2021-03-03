---
title: Automatizálási feladatok létrehozása Azure-erőforrások kezeléséhez és figyeléséhez
description: Olyan automatizált feladatokat állíthat be, amelyek segítségével felügyelheti az Azure-erőforrásokat, és figyelheti a költségeket a Azure Logic Appson futó munkafolyamatok létrehozásával.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719049"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Azure-erőforrások kezelése és a költségek figyelése automatizálási feladatok létrehozásával (előzetes verzió)

> [!IMPORTANT]
> Ez a képesség nyilvános előzetes verzióban érhető el, nem vonatkozik rá szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az [Azure-erőforrások](../azure-resource-manager/management/overview.md#terminology) egyszerűbb kezelése érdekében az Automation-feladatütemezés használatával automatizált felügyeleti feladatokat hozhat létre egy adott erőforráshoz vagy erőforráscsoporthoz, amely az erőforrástípus alapján eltérő lehet. Egy [Azure Storage-fiók](../storage/common/storage-account-overview.md)esetében például beállíthat egy Automation-feladatot, amely elküldi Önnek az adott Storage-fiók havi költségét. Azure-beli [virtuális gépek](https://azure.microsoft.com/services/virtual-machines/)esetén létrehozhat egy automatizálási feladatot, amely bekapcsolja vagy kikapcsolja a virtuális gépet egy előre meghatározott ütemterv szerint.

A háttérben egy automatizálási feladat tulajdonképpen egy olyan munkafolyamat, amely a [Azure Logic apps](../logic-apps/logic-apps-overview.md) szolgáltatáson fut, és az azonos [díjszabási](https://azure.microsoft.com/pricing/details/logic-apps/) és [díjszabási modell](../logic-apps/logic-apps-pricing.md)alapján számítjuk fel a díjat. A feladat létrehozása után megtekintheti és szerkesztheti az alapul szolgáló munkafolyamatot. Ehhez nyissa meg a feladatot a Logic app Designerben. Ha egy feladat legalább egy futtatást végzett, megtekintheti az egyes futtatások állapotát, előzményeit, bemeneteit és kimeneteit.

Ebben az előzetes verzióban a jelenleg elérhető feladatok sablonjai láthatók:

| Erőforrás típusa | Automatizálási feladatok sablonjai |
|---------------|---------------------------|
| Azure-erőforráscsoportok | **Az erőforrás törlésekor** |
| Minden Azure-erőforrásban | **Erőforrás havi költségének elküldése** |
| Azure-beli virtuális gépek | Továbbá: <p>- **Virtuális gép kikapcsolása** <br>- **Virtuális gép indítása** |
| Azure Storage-fiókok | Továbbá: <p>- **Régi Blobok törlése** |
| Azure Cosmos DB | Emellett <p>- **Lekérdezési eredmény küldése e-mailben** |
|||

Ez a cikk bemutatja, hogyan végezheti el a következő feladatokat:

* [Hozzon létre egy Automation-feladatot](#create-automation-task) egy adott Azure-erőforráshoz.

* [Tekintse át a tevékenységek előzményeit](#review-task-history), beleértve a futtatási állapotot, a bemeneteket, a kimeneteket és az egyéb múltbeli információkat.

* [Szerkessze a](#edit-task) feladatot úgy, hogy frissítse a feladatot, vagy szabja testre a feladat alapjául szolgáló munkafolyamatot a Logic app Designerben.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Hogyan térnek el az Automation-feladatok a Azure Automation?

Jelenleg csak az erőforrás szintjén hozhat létre Automation-feladatokat, megtekintheti a feladat futtatási előzményeit, és szerkesztheti a feladat alapjául szolgáló logikai alkalmazás munkafolyamatát, amelyet a [Azure Logic apps](../logic-apps/logic-apps-overview.md) szolgáltatás működtet. Az automatizálási feladatok több alapszintű és könnyű, mint [Azure Automation](../automation/automation-intro.md).

Ezzel szemben az Azure Automation egy felhőalapú automatizálási és konfigurációs szolgáltatás, amely támogatja az Azure-beli és nem Azure-beli környezetek egységes felügyeletét. A szolgáltatás folyamat- [automatizálást biztosít a folyamatok](../automation/automation-intro.md#process-automation) [runbookok](../automation/automation-runbook-execution.md), a [változások követésével és leltározásával](../automation/change-tracking/overview.md), a frissítési felügyelettel, a megosztott képességekkel és a heterogén funkciókkal való összeszereléshez. Az Automation szolgáltatás teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.

## <a name="prerequisites"></a>Előfeltételek

* Azure-fiók és -előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* A felügyelni kívánt Azure-erőforrás. Ez a cikk egy Azure Storage-fiókot használ példaként.

* Office 365-fiók, ha követni szeretné a példát, amely e-mailt küld az Office 365 Outlook használatával.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Automatizálási feladat létrehozása

1. A [Azure Portal](https://portal.azure.com)keresse meg a kezelni kívánt erőforrást.

1. Az erőforrás menüben görgessen az **Automation** szakaszhoz, és válassza a **feladatok** lehetőséget.

   ![Képernyőkép a Azure Portal és a Storage-fiók erőforrásának menüjéről, ahol az "Automation" szakaszban a "feladatok" menüpont van kiválasztva.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. A **feladatok** ablaktáblán válassza a **Hozzáadás** lehetőséget, hogy kiválassza a feladathoz tartozó sablont.

   ![Képernyőfelvétel: a Storage-fiók "feladatok" ablaktáblája, ahol az eszköztáron a "Hozzáadás" lehetőség van kiválasztva](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. A **feladat hozzáadása** ablaktábla **sablon kiválasztása** területén válassza ki a létrehozni kívánt feladat sablonját. Ha a következő lap nem jelenik meg, válassza a **következő: hitelesítés** lehetőséget.

   Ez a példa továbbra is fennáll, ha a **havi költség küldése erőforrás** -feladathoz sablont választja.

   ![Képernyőkép, amely a következő beállításokat jeleníti meg: "erőforrás havi költségének küldése" és "Next: Authentication"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. A **hitelesítés** alatt a **kapcsolatok** szakaszban válassza a **Létrehozás** lehetőséget minden olyan kapcsolathoz, amely megjelenik a feladatban, így hitelesítő adatokat adhat meg az összes kapcsolathoz. Az egyes feladatokban található kapcsolatok típusai a feladat alapján változnak.

   Ez a példa csak a feladat által igényelt kapcsolatok egyikét jeleníti meg.

   ![A Azure Resource Manager-kapcsolatok kiválasztott "Create" beállítását bemutató képernyőkép](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Amikor a rendszer kéri, jelentkezzen be az Azure-fiókja hitelesítő adataival.

   ![A "Bejelentkezés" lehetőséget megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Az egyes sikeresen hitelesített kapcsolatok a következő példához hasonlóan néz ki:

   ![A sikeresen létrehozott kapcsolatokat bemutató képernyőkép](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Az összes kapcsolat hitelesítése után válassza a **Tovább: konfigurálás** lehetőséget, ha a következő lap nem jelenik meg.

1. A **konfiguráció** területen adja meg a feladat nevét és a feladathoz szükséges egyéb információkat. Ha elkészült, válassza a **Létrehozás** lehetőséget.

   > [!NOTE]
   > A feladat neve nem módosítható a létrehozás után, ezért olyan nevet adjon meg, amely továbbra is érvényes, ha [szerkeszti az alapul szolgáló munkafolyamatot](#edit-task-workflow). Az alapul szolgáló munkafolyamaton végrehajtott módosítások csak a létrehozott feladatra vonatkoznak, nem a sablonra.
   >
   > Ha például a feladat nevét adja meg `Send monthly cost` , de később hetente szeretné futtatni az alapul szolgáló munkafolyamatot, a feladat neve nem módosítható `Send weekly cost` .

   Az e-mail-értesítéseket küldő feladatok e-mail-címet igényelnek.

   ![A kiválasztott feladathoz szükséges adatokat megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   A létrehozott feladat, amely automatikusan élő és fut, megjelenik az **Automation-feladatok** listáján.

   ![Az Automation-feladatok listáját megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Ha a feladat nem jelenik meg azonnal, próbálja meg frissíteni a feladatok listáját, vagy várjon egy kicsit a frissítés előtt. Az eszköztáron válassza a **frissítés** lehetőséget.

   A kiválasztott feladat futtatása után egy e-mailt kap, amely a következő példához hasonlít:

   ![A feladat által küldött e-mail-értesítéseket megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Feladat előzményeinek áttekintése

Az alábbi lépéseket követve megtekintheti a feladatok előzményeit, valamint azok állapotát, bemeneteit, kimeneteit és egyéb információit:

1. A [Azure Portal](https://portal.azure.com)keresse meg azt az erőforrást, amelynek a feladatait át szeretné tekinteni.

1. Az erőforrás menü **Beállítások** területén válassza az **automatizálási feladatok** elemet.

1. A feladatok listában keresse meg az áttekinteni kívánt feladatot. A feladat **futtatások** oszlopában válassza a **nézet** lehetőséget.

   ![A feladatot és a kiválasztott "View" lehetőséget megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   A futtatási **Előzmények** ablaktábla a feladat összes futtatását megjeleníti az állapotukkal, a kezdési időponttal, az azonosítókkal és a futtatási időtartamokkal együtt.

   ![A feladatok futtatását, állapotát és egyéb információit megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Itt láthatja a Futtatás lehetséges állapotát:

   | Állapot | Leírás |
   |--------|-------------|
   | **Megszakítva** | A feladatot a rendszer futás közben megszakította. |
   | **Sikertelen** | A feladat legalább egy sikertelen művelettel rendelkezik, de a hiba kezeléséhez nem létezett további művelet. |
   | **Futó** | A feladat jelenleg fut. |
   | **Sikeres** | Az összes művelet sikeres volt. Egy feladat akkor is sikeresen befejeződik, ha egy művelet meghiúsult, de egy további művelet is létezett a hiba kezeléséhez. |
   | **Várakozó** | A Futtatás még nincs elindítva, és a rendszer szünetelteti, mert a feladat egy korábbi példánya továbbra is fut. |
   |||

   További információ: a [futtatási előzmények áttekintése](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. A futtatási lépések állapotának és egyéb információinak megtekintéséhez válassza a Futtatás lehetőséget.

   Megnyílik a **logikai alkalmazás futtatási** panelje, és megjeleníti a futtatott alapul szolgáló munkafolyamatot.

   * A munkafolyamatok mindig [*triggerrel*](../connectors/apis-list.md#triggers-actions)kezdődnek. Ehhez a feladathoz a munkafolyamat az [ **ismétlődési** eseményindítóval](../connectors/connectors-native-recurrence.md)kezdődik.

   * Az egyes lépések megjelenítik az állapotukat és a Futtatás időtartamát. A 0 másodperces időtartamú lépések futtatása 1 másodpercnél kevesebb időt vett igénybe.

   ![A futtatási, az állapot-és a futtatási időtartam egyes lépéseit megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Az egyes lépésekhez tartozó bemenetek és kimenetek áttekintéséhez válassza ki a lépést, amely kibontja a következőt:.

   Ez a példa az ismétlődési eseményindító bemeneteit jeleníti meg, amelyek nem rendelkeznek kimenetekkel, mert az eseményindító csak a munkafolyamat futásakor adja meg a folyamatot, és nem ad kimenetet a későbbi műveletek végrehajtásához.

   ![A kibontott triggert és bemeneteket megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Ezzel szemben az **e-mailek küldése** művelettel a munkafolyamatban és a kimenetben lévő korábbi műveletek bemenetei szerepelnek.

   ![A kibontott műveletet, bemeneteket és kimeneteket megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Annak megismeréséhez, hogy miként hozhat létre saját automatizált munkafolyamatokat, így az alkalmazások, az adatok, a szolgáltatások és a rendszerek integrálása az Azure-erőforrások automatizálási feladatainak kontextusán kívül: gyors útmutató [: az első integrációs munkafolyamat létrehozása Azure Logic apps-Azure Portal használatával](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>A feladat szerkesztése

A feladatok módosításához a következő lehetőségek közül választhat:

* [Szerkessze a "beágyazott" feladatot](#edit-task-inline) , hogy megváltoztassa a tevékenység tulajdonságait, például a kapcsolatok adatait vagy a konfigurációs adatokat, például az e-mail-címét.

* [Szerkessze a feladat alapjául szolgáló munkafolyamatot](#edit-task-workflow) a Logic app Designerben.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>A feladat beágyazott szerkesztése

1. A [Azure Portal](https://portal.azure.com)keresse meg a frissíteni kívánt feladatot tartalmazó erőforrást.

1. Az erőforrás menü **automatizálás** területén válassza a **feladatok** elemet.

1. A feladatok listában keresse meg a frissíteni kívánt feladatot. Nyissa meg a feladat ellipsziseit (**..**.), és válassza **a Szerkesztés a következőben: beágyazott** lehetőséget.

   ![Képernyőkép, amely megjeleníti a megnyitott ellipszisek menüt és a kijelölt lehetőséget, a "beágyazott szerkesztés" elemet.](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Alapértelmezés szerint a **hitelesítés** lap jelenik meg, és megjeleníti a meglévő kapcsolatokat.

1. Új hitelesítési hitelesítő adatok hozzáadásához vagy a csatlakozáshoz használt különböző meglévő hitelesítő adatok kiválasztásához nyissa meg a kapcsolatok ellipsziseit (**..**.), és válassza az **új csatlakozás hozzáadása** vagy ha elérhető, különböző hitelesítési hitelesítő adatok lehetőséget.

   ![A hitelesítés lapot, a meglévő kapcsolatokat és a kijelölt ellipszisek menüt megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. A többi feladat tulajdonságainak frissítéséhez válassza a **Tovább: konfigurálás** lehetőséget.

   Az ebben a példában szereplő feladathoz a szerkesztéshez csak az e-mail-cím használható.

   ![A konfiguráció lapot megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>A feladat alapjául szolgáló munkafolyamat szerkesztése

Amikor megváltoztatja egy automatizálási feladat hátterében lévő munkafolyamatot, a módosítások csak a létrehozott feladatra, és nem a feladatot létrehozó sablonra vannak hatással. A módosítások elvégzése és mentése után előfordulhat, hogy az eredeti feladathoz megadott név nem pontosan leírja a feladatot, ezért előfordulhat, hogy újra létre kell hoznia a feladatot egy másik névvel.

> [!TIP]
> Az ajánlott eljárás az alapul szolgáló munkafolyamat klónozása, így helyette szerkesztheti a másolt verziót. Ily módon elvégezheti és tesztelheti a módosításokat a másolaton, miközben az eredeti automatizálási feladat továbbra is működni fog, és nem kockáztatja a megszakadást, és nem szakítja meg a meglévő funkciókat. Miután befejezte a módosításokat, és meggyőződött arról, hogy az új verzió sikeresen lefut, letilthatja vagy törölheti az eredeti automatizálási feladatot, és használhatja az Automation-feladat klónozott verzióját is. A következő lépések a munkafolyamat klónozásával kapcsolatos információkat tartalmazzák.

1. A [Azure Portal](https://portal.azure.com)keresse meg a frissíteni kívánt feladatot tartalmazó erőforrást.

1. Az erőforrás menü **automatizálás** területén válassza a **feladatok** elemet.

1. A feladatok listában keresse meg a frissíteni kívánt feladatot. Nyissa meg a feladat ellipsziseit (**..**.), majd válassza a **Megnyitás lehetőséget Logic apps**.

   ![Képernyőkép, amely megjeleníti a megnyitott ellipszisek menüt és a "Megnyitás a Logic Appsban" lehetőséget.](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   A feladat alapjául szolgáló munkafolyamat megnyílik a Azure Logic Apps szolgáltatásban, és megjeleníti az **Áttekintés** ablaktáblát, ahol megtekintheti a feladathoz elérhető futtatási előzményeket.

   ![Képernyőfelvétel: a feladat megjelenítése Azure Logic Apps nézetben a kijelölt áttekintés ablaktáblán](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Az alapul szolgáló munkafolyamat a Logic app Designerben való megnyitásához a logikai alkalmazás menüjében válassza a **Logic app Designer** elemet.

   ![Képernyőkép a "Logic app Designer" menüpont kiválasztásáról és tervezői felületéről az alapul szolgáló munkafolyamattal](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Mostantól szerkesztheti a munkafolyamat triggerének és műveleteinek tulajdonságait, valamint szerkesztheti az aktiválást és a saját munkafolyamatot definiáló műveleteket is. Az ajánlott eljárás szerint azonban kövesse a munkafolyamat klónozásához szükséges lépéseket, hogy a módosításokat egy másolaton végezze el, miközben az eredeti munkafolyamat továbbra is működik és fut.

1. A munkafolyamat klónozásához és a másolt verzió szerkesztéséhez kövesse az alábbi lépéseket:

   1. A logikai alkalmazás munkafolyamatának menüjében válassza az **Áttekintés** lehetőséget.

   1. Az Áttekintés ablaktábla eszköztárán válassza a **klónozás** elemet.

   1. A logikai alkalmazás létrehozása ablaktáblán a **név** területen adjon meg egy új nevet a másolt logikai alkalmazás munkafolyamatának.

      A **logikai alkalmazás állapotának** kivételével a többi tulajdonság nem érhető el szerkesztésre. 
      
   1. A **logikai alkalmazás állapota** területen válassza a **Letiltva** lehetőséget, hogy a klónozott munkafolyamat ne fusson, amíg a módosításokat végzi. Engedélyezheti a munkafolyamatot, amikor készen áll a módosítások tesztelésére.

   1. Miután az Azure befejezte a klónozott munkafolyamat üzembe helyezését, keresse meg és nyissa meg ezt a munkafolyamatot a Logic app Designerben.

1. Az trigger vagy művelet tulajdonságainak megtekintéséhez bontsa ki az triggert vagy a műveletet.

   Például megváltoztathatja az ismétlődési eseményindítót úgy, hogy hetente fusson, nem pedig havonta.

   ![Képernyőkép a kibontott ismétlődési eseményindítóról, amelyen a frekvencia lista meg van nyitva az elérhető gyakorisági beállítások megjelenítéséhez](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   További információ az ismétlődési eseményindítóról: [ismétlődő feladatok és munkafolyamatok létrehozása, ütemezése és futtatása az ismétlődési eseményindítóval](../connectors/connectors-native-recurrence.md). További információ a használható egyéb eseményindítókkal és műveletekkel kapcsolatban: [összekötők Azure Logic Appshoz](../connectors/apis-list.md).

1. A módosítások mentéséhez a tervező eszköztárán válassza a **Mentés** lehetőséget.

   ![A tervező eszköztárat és a kiválasztott "Mentés" parancsot megjelenítő képernyőkép](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. A frissített munkafolyamat teszteléséhez és futtatásához a tervező eszköztárán válassza a **Futtatás** lehetőséget.

   A Futtatás befejezése után a tervező megjeleníti a munkafolyamat futtatásának részleteit.

   ![A munkafolyamat futtatási részleteit megjelenítő képernyőkép a tervezőben](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Ha le szeretné tiltani a munkafolyamatot, hogy a feladat ne fusson, tekintse meg [a logikai alkalmazások kezelése a Azure Portalban](../logic-apps/manage-logic-apps-with-azure-portal.md)című témakört.

## <a name="provide-feedback"></a>Visszajelzés küldése

Szeretnék hallani! A hibák jelentéséhez, visszajelzések megadásához vagy az előzetes verzióval kapcsolatos kérdések feltevéséhez [forduljon a Azure Logic apps csapatához](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Következő lépések

* [Logikai alkalmazások kezelése a Azure Portalban](../logic-apps/manage-logic-apps-with-azure-portal.md)
