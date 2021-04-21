---
title: Tervezze meg a költségek kezelését a Azure Logic Apps
description: Megtudhatja, hogyan tervezhet a költségelemzési Azure Logic Apps és kezelheti a Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 03/24/2021
ms.openlocfilehash: ec2e1098df4c21704ee7c17852b893630cd3fd27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761816"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>A költségek megtervzésére és Azure Logic Apps

Ez a cikk segítséget nyújt a költségek tervezéséhez és kezeléséhez a Azure Logic Apps. Mielőtt erőforrásokat hoz létre vagy ad hozzá ezzel a szolgáltatással, becsülje meg a költségeit az Azure díjkalkulátorával. Az erőforrások használatának Logic Apps után a következővel állíthat be költségvetéseket és figyelheti a [Azure Cost Management.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Annak érdekében, hogy azonosítsa azokat a területeket, ahol érdemes lehet cselekedni, áttekintheti az előre jelzett költségeket, és figyelheti a kiadási trendeket.

Ne feledje, hogy a Logic Apps csak az Azure-számla havi költségeinek részét képezi. Bár ez a cikk bemutatja, hogyan becsülheti meg és kezelheti a Logic Apps költségeit, az Azure-előfizetésben használt összes Azure-szolgáltatásért és -erőforrásért, beleértve a harmadik féltől származó szolgáltatásokat is, ki kell fizetni. Ha már ismeri a költségkezelést a Logic Apps, hasonló módszereket alkalmazhat az előfizetésben használt összes Azure-szolgáltatás költségeinek kezelésére.

## <a name="prerequisites"></a>Előfeltételek

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Azure-fióktípusok legtöbb típusát támogatja. A támogatott fióktípusok megtekintéséhez tekintse meg [a következőt: Az Cost Management megtekintése.](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>A számlázási modellel kapcsolatos információk

Azure Logic Apps Azure-infrastruktúrán fut, amely [az](https://azure.microsoft.com/pricing/details/logic-apps/) új erőforrások üzembe helyezésekor költségekkel jár. Győződjön meg arról, hogy megértette a Logic Apps szolgáltatás számlázási modelljét és a kapcsolódó [Azure-erőforrásokat,](logic-apps-pricing.md)és kezelje a függőségek miatti költségeket, amikor módosítja az üzembe helyezett erőforrásokat.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>A jellemzően az előfizetéssel együtt Azure Logic Apps

A Logic Apps szolgáltatás különböző díjszabási modelleket alkalmaz a létrehozott és használt erőforrások alapján:

* A több-bérlős szolgáltatásban létrehozott és futtatott logikaialkalmazás-erőforrások Logic Apps [díjszabási modellt használ.](../logic-apps/logic-apps-pricing.md#consumption-pricing)

* Az integrációs szolgáltatási környezetben [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozott és futtatott logikaialkalmazás-erőforrások rögzített [díjszabási modellt használnak.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

Az egyéb erőforrások, amelyek költségekkel járnak a logikai alkalmazásokkal való használathoz való létrehozásukkor:

* Az [integrációs](../logic-apps/logic-apps-pricing.md#integration-accounts) fiók egy különálló erőforrás, amely a B2B-integrációk létrehozásához szükséges logikai alkalmazásokhoz kapcsolható. Az integrációs fiókok rögzített [díjszabási modellt használnak,](../logic-apps/logic-apps-pricing.md#integration-accounts) ahol a díjszabás a használt integrációs fiók típusán vagy *rétegen* alapul.

* Az [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) különálló erőforrás, amely üzembe helyezési helyként hozható létre a virtuális hálózat erőforrásaihoz közvetlen hozzáférést igényelő logikai alkalmazások számára. Az ISE-k rögzített [díjszabási modellt használnak,](../logic-apps/logic-apps-pricing.md#fixed-pricing) ahol a díjszabás a létrehozott ISE termékváltozaton és egyéb beállításokon alapul.

* Az [egyéni összekötők](../logic-apps/logic-apps-pricing.md#consumption-pricing) különálló erőforrások, REST API logikai alkalmazásokban nem használhat előre elkészített összekötőt. Az egyéni összekötő-végrehajtások használaton keresztüli [díjszabási](../logic-apps/logic-apps-pricing.md#consumption-pricing) modellt használnak, kivéve, ha ISE-hez használják őket.

* A több-bérlős Logic Apps adatmegőrzés és a tárhelyfelhasználás egy rögzített díjszabási modell használatával növeli a [költségeket.](../logic-apps/logic-apps-pricing.md#fixed-pricing) [](../logic-apps/logic-apps-pricing.md#data-retention) A futtatáselőzmények bemenetei és kimenetei például a háttérben tárolódnak, amely eltér a logikai alkalmazástól függetlenül létrehozott, kezelve és elérhető tárolási erőforrásoktól.

  Az ISE-kben az adatmegőrzés és a tárhelyfelhasználás nem jár költségekkel.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Az erőforrás törlése után felmerülhet a költségek felhalmozott költsége

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

A logikai alkalmazás törlése után a Logic Apps szolgáltatás nem hoz létre vagy futtat új munkafolyamat-példányokat. Az összes folyamatban lévő és függőben lévő futtatás azonban a befejezésig folytatódik. A futtatás számától függően ez a folyamat tovább is tart. További információ: [Logikai alkalmazások kezelése.](manage-logic-apps-with-azure-portal.md#delete-logic-apps)

Ha ezekkel az erőforrásokkal rendelkezik egy logikai alkalmazás törlése után, ezek az erőforrások továbbra is léteznek, és a törlésükig felhalmozott költségekkel járnak:

* Az ön által létrehozott és felügyelt Azure-erőforrások az erőforrásokhoz kapcsoló logikai alkalmazástól függetlenül , például Azure-függvényalkalmazások, eseményközpontok, eseményrácsok és így tovább

* Integrációs fiókok

* Integrációs szolgáltatási környezetek (ISE-k)

  Ha töröl [egy ISE-t,](ise-manage-integration-service-environment.md#delete-ise)a társított Azure-beli virtuális hálózat, alhálózatok és egyéb kapcsolódó erőforrások továbbra is léteznek. Az ISE törlése után előfordulhat, hogy a virtuális hálózat vagy az alhálózatok törlésének megpróbálása előtt akár egy bizonyos számú órát is várnia kell.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Pénzügyi kredit használata Azure Logic Apps

A pénzügyi Azure Logic Apps ea pénzügyikeret-jóváírással fizethet. Azonban nem használhatja az EA pénzügyikeret-kreditet a külső termékek és szolgáltatások díjának fizetésére, beleértve a pénzügyi Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>A költségek megbecslése

Mielőtt erőforrásokat hoz létre a Azure Logic Apps, becsülje meg a költségeit az [Azure díjkalkulátorával.](https://azure.microsoft.com/pricing/calculator/) További információ: [Díjszabási modell Azure Logic Apps.](../logic-apps/logic-apps-pricing.md)

1. Az [Azure díjkalkulátor oldalán a](https://azure.microsoft.com/pricing/calculator/)bal oldali menüben válassza az **Integrációs**  >  Azure Logic Apps.

   ![Képernyőkép az Azure díjkalkulátorról a kiválasztott "Azure Logic Apps" beállítással.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Görgessen lefelé az oldalon, amíg meg nem Azure Logic Apps díjkalkulátort. Az Azure-erőforrások különböző, a Azure Logic Apps-hez közvetlenül kapcsolódó szakaszaiban adja meg, hogy hány erőforrást tervez használni, és hogy hány intervallumon keresztül használhatja ezeket az erőforrásokat.

   Ezen a képernyőképen egy példa költségbecslés látható a kalkulátor használatával:

   ![Példa az Azure-díjkalkulátor becsült költségeire](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Az új kapcsolódó erőforrások létrehozása és használata során a költségbecslések frissítéséhez térjen vissza ehhez a kalkulátorhoz, és itt frissítse ezeket az erőforrásokat.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Költségvetések és riasztások létrehozása

Az Azure-fiók vagy -előfizetés költségeinek proaktív kezelése [](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) érdekében [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) költségvetéseket és riasztásokat hozhat létre a Azure Cost Management and Billing [szolgáltatás](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és képességek használatával.  Költségvetések és riasztások az Azure-előfizetések és -erőforráscsoportok számára vannak létrehozva, így egy általános költségfigyelési stratégia részeként hasznosak.

A költségvetéshez és a költségküszöbökhez képest a riasztások automatikusan értesítik az érdekelt feleket a kiadások rendellenességéről és a túlköltekezés kockázatával kapcsolatban. Ha részletesebb monitorozásra van szükség, olyan költségvetéseket is létrehozhat, amelyek szűrőket alkalmaznak az Azure adott erőforrásaihoz vagy szolgáltatásaihoz. A szűrőkkel győződjön meg arról, hogy nem hoz létre véletlenül olyan új erőforrásokat, amelyek többletköltséggel járnak. További információ a szűrési lehetőségekről: [Csoportosítási és szűrési beállítások.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Költségek figyelése

Az erőforrás-használati egység költségei időintervallumok ,például másodpercek, percek, órák és napok vagy egységhasználat (bájtok, megabájtok stb.) szerint változnak. Néhány példa nap, aktuális és előző hónap és év szerint. A hosszabb nézetekre való váltás segíthet a költési trendek azonosításában. A költségelemzési funkciók használata esetén a költségeket grafikonokként és táblákként, különböző időintervallumokban lehet megtekinteni. Ha költségvetéseket és költség-előrejelzéseket hozott létre, azt is könnyedén megkeresheti, hogy hol lépték túl a költségvetéseket, és hol történtek túlk függőben lévők.

Miután elkezdi az Azure-ban létrehozott vagy használatba kerülő erőforrások költségeit, a következő módokon áttekintheti és figyelheti ezeket a költségeket:

* [Logikai alkalmazások végrehajtásának és tárterület-használatának figyelése](#monitor-billing-metrics) a Azure Monitor

* Költségelemzés [futtatása](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) [a](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Azure Cost Management and Billing

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Logikai alkalmazások végrehajtásának és tárhelyfelhasználásának figyelése

A Azure Monitor a következő metrikákat egy adott logikai alkalmazáshoz használhatja:

* Számlázható műveletvégrehajtások
* Számlázható eseményindító-végrehajtások
* Számlázási használat natív műveletek végrehajtásakor
* Standard összekötő-végrehajtások számlázása
* A tárterület-használat számlázása
* Számlázható végrehajtások összesen

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Végrehajtási és tárhelyfelhasználási metrikák megtekintése

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást. A logikai alkalmazás menüjében a Figyelés **alatt** válassza a **Metrikák lehetőséget.**

1. A jobb oldali panel diagramcíme **alatt,** a metrikasávon nyissa meg a Metrika listát, és válassza ki a kívánt metrikát. 

   > [!NOTE]
   > A tárhelyfelhasználást a logikai alkalmazás által használt és számlázható tárolási egységek (GB) száma alapján mérjük. Előfordulhat, hogy az 500 MB-nál kisebb tárterületet használó futtatás nem jelenik meg figyelési nézetben, de továbbra is kiszámlázva vannak.

   ![Képernyőkép a Metrikák panelről a megnyitott "Metrikák" listával.](./media/logic-apps-pricing/select-metric.png)

1. A panel jobb felső sarkában válassza ki a kívánt időszakot.

1. Az egyéb tárhelyfelhasználási adatok, különösen a műveletbemenetek és -kimenetek méretének a logikai alkalmazás futtatáselőzményeibe való megtekintéséhez kövesse [az alábbi lépéseket.](#view-input-output-sizes)

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Műveletbemenetek és -kimenetek méretének megtekintése a futtatás előzményeiben

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza az Áttekintés **lehetőséget.**

1. A jobb oldali panelen a Futtatáselőzmények alatt **válassza** ki a megtekinteni kívánt bemeneteket és kimeneteket tartalmazó futtatásokat.

1. A **Logic App run (Logikai alkalmazás futtatása) alatt** válassza a Run Details **(Futtatás részletei) lehetőséget.**

1. A **Logikai alkalmazás futtatás részletei panel** műveletek táblájában, amely felsorolja az egyes műveletek állapotát és időtartamát, válassza ki a megtekinteni kívánt műveletet.

1. A **Logikai alkalmazás műveletpanelen** keresse meg a művelet bemenetének és kimenetének méretét. Az **Inputs link (Bemenetek) és** **a Outputs**(Kimenetek) hivatkozás alatt keresse meg a bemeneti és kimeneti adatokra mutató hivatkozásokat.

   > [!NOTE]
   > A hurkok esetén csak a legfelső szintű műveletek mutatják a bemeneteik és kimeneteik méretét. Beágyazott hurkon belüli műveletek esetén a bemenetek és kimenetek nulla méretet mutatnak, és nem tartalmaznak hivatkozásokat.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Költségelemzés futtatása a Azure Cost Management and Billing

A Logic Apps szolgáltatás költségeinek egy adott hatókör, például egy Azure-előfizetés alapján való [](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) áttekintéséhez használhatja a következőben: [Azure Cost Management and Billing.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

1. A Azure Portal nyissa meg a kívánt hatókört, például az Azure-előfizetését. A bal oldali menüben, a **Cost Management** válassza a **Költségelemzés lehetőséget.**

   Amikor először nyitja meg a költségelemzési panelt, a felső diagramon az aktuális hónap összes szolgáltatásának tényleges és előre jelzett használati költségei megjelenik.

   ![Képernyőkép a Azure Portal és költségelemzés panelről, amely egy előfizetés tényleges és előrejelző költségeit mutatja be példaként.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > A hatókörök módosításhoz a **Költségelemzés panelen** a szűrők sávon válassza a Hatókör **szűrőt.** A Hatókör **kiválasztása panelen** váltson a kívánt hatókörre.

   Alatta a fánkdiagramok az aktuális költségeket mutatják Azure-szolgáltatások, Azure-régió (hely) és erőforráscsoport szerint.

   ![Képernyőkép a Azure Portal és költségelemzés panelről a szolgáltatások, régiók és erőforráscsoportok példa fánkdiagramjaival.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Ha a diagramot egy adott területre, például szolgáltatásra vagy erőforrásra szeretne szűrni, a szűrők sávján válassza a **Szűrő hozzáadása lehetőséget.**

1. A bal oldali listában válassza ki a szűrőtípust, **például: Szolgáltatásnév.** A jobb oldali listában válassza ki a szűrőt, például a **logikai alkalmazásokat.** Ha végzett, kattintson a zöld pipa jelre.

   ![Képernyőkép a Azure Portal és a költségelemzés panelről a szűrők kiválasztásával.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Itt van például a Logic Apps eredménye:

   ![Képernyőkép a Azure Portal és költségelemzési panelről a "Logic Apps" alapján szűrt eredményekkel.](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Költségadatok exportálása

Ha további adatelemzést kell végzette [](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a költségeken, exportálhatja a költségadatokat egy tárfiókba. Egy pénzügyi csapat például az Excel vagy a Power BI. A költségeket napi, heti vagy havi ütemezés szerint exportálhatja, és egyéni dátumtartományt állíthat be. A költségadatkészletek lekérésének ajánlott módja a költségadatok exportálása.

## <a name="other-ways-to-manage-and-reduce-costs"></a>A költségek kezelésének és csökkentésének egyéb módjai

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

A logikai aps és a kapcsolódó erőforrások költségeinek csökkentéséhez próbálja ki az alábbi lehetőségeket:

* Ha lehetséges, használjon [beépített](../connectors/built-in.md)eseményindítókat és műveleteket, amelyek futtatása végrehajtásonként kevesebbe kerül, mint a felügyelt [összekötő eseményindítói és műveletei.](../connectors/managed.md)

  Például csökkentheti a költségeket, amikor más erőforrásokhoz fér hozzá a [HTTP-művelet](../connectors/connectors-native-http.md) vagy egy, a [Azure Functions](../azure-functions/functions-overview.md) szolgáltatás használatával létrehozott függvény hívásával és a beépített Azure Functions [művelet használatával.](../logic-apps/logic-apps-azure-functions.md) A Azure Functions azonban költségekkel is jár, ezért mindenképpen hasonlítsa össze a lehetőségeket.

* [Pontos eseményindító-feltételek megadása](logic-apps-workflow-actions-triggers.md#trigger-conditions) a munkafolyamat futtatásához.

  Megadhatja például, hogy az eseményindító csak akkor aktiválódik, ha a célként megadott webhely belső kiszolgálói hibát ad vissza. Az eseményindító JSON-definíciójában a tulajdonság használatával adjon meg egy feltételt, amely az eseményindító `conditions` állapotkódjára hivatkozik.

* Ha egy eseményindító lekérdezéses és webhookverzióval rendelkezik, próbálja ki a webhook verzióját, amely megvárja, amíg a megadott esemény bekövetkezik az aktiválás előtt, és nem ellenőrzi rendszeresen az eseményt.

* Hívja meg a logikai alkalmazást egy másik szolgáltatáson keresztül, hogy az eseményindító csak akkor aktiválódik, amikor a munkafolyamatnak futnia kell.

  A logikai alkalmazást például egy olyan függvényből hívhatja meg, amely a Azure Functions használatával lesz létrehozva és Azure Functions. Lásd például: Logikai alkalmazások hívása vagy aktiválása a Azure Functions [és Azure Service Bus.](logic-apps-scenario-function-sb-trigger.md)

* [Tiltsa le](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) azokat a logikai alkalmazásokat, amelyek nem kell folyamatosan futniuk, vagy törölje azokat a logikai alkalmazásokat, amelyekre már nincs is szüksége. [](manage-logic-apps-with-azure-portal.md#delete-logic-apps) Ha lehetséges, tiltsa le az összes olyan erőforrást, amelyekre nincs szüksége folyamatosan aktívként.

## <a name="next-steps"></a>Következő lépések

* [A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Költségek kezelése költségelemzés használatával](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Váratlan költségek megelőzése](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Az [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurzus