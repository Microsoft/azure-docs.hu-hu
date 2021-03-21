---
title: A Azure Logic Apps költségeinek kezelésének megtervezése
description: Ismerje meg, hogyan tervezheti meg és kezelheti Azure Logic Apps költségeit a Cost Analysis használatával a Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 44351497ed58c8d49404c094f6800b52186edabb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709631"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>A Azure Logic Apps költségeinek megtervezése és kezelése

Ez a cikk segítséget nyújt a Azure Logic Apps költségeinek megtervezéséhez és kezeléséhez. Mielőtt bármilyen erőforrást hozna létre vagy felvesz a szolgáltatás használatával, az Azure díjszabási kalkulátor használatával becsülje meg a költségeket. A Logic Apps-erőforrások használatának megkezdése után a [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)segítségével megadhatja a költségvetéseket és figyelheti a költségeket. A felmerülő területek azonosításához tekintse át az előre jelzett költségeket, és figyelje a kiadási trendeket.

Vegye figyelembe, hogy a Logic Apps költségei csak az Azure-számlán lévő havi költségek részét képezik. Bár ez a cikk ismerteti a Logic Apps költségeinek megbecslését és kezelését, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás, beleértve a harmadik féltől származó szolgáltatásokat is. Ha már ismeri a Logic Apps költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésében használt összes Azure-szolgáltatás költségeit.

## <a name="prerequisites"></a>Előfeltételek

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

A [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a legtöbb Azure-fiók típusát támogatja. Az összes támogatott fióktípus megtekintéséhez tekintse meg az [Cost Management-információk megismerése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört. A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához.

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>A számlázási modell megismerése

Azure Logic Apps olyan Azure-infrastruktúrán fut, amely új erőforrások telepítésekor [költségekkel jár](https://azure.microsoft.com/pricing/details/logic-apps/) . Győződjön meg arról, hogy tisztában van a [Logic Apps szolgáltatás számlázási modelljével, valamint a kapcsolódó Azure-erőforrásokkal](logic-apps-pricing.md), valamint a függőségek miatti költségek kezelésével, amikor módosításokat végez a telepített erőforrásokon.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Általában a Azure Logic Appsekkel felmerülő költségek

A Logic Apps szolgáltatás különböző díjszabási modelleket alkalmaz a létrehozott és használt erőforrások alapján:

* A több-bérlős Logic Apps szolgáltatásban létrehozott és futtatott Logic apps-erőforrások [fogyasztási díjszabási modellt](../logic-apps/logic-apps-pricing.md#consumption-pricing)használnak.

* Az [integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) létrehozott és futtatott Logic apps-erőforrások [rögzített díjszabási modellt](../logic-apps/logic-apps-pricing.md#fixed-pricing)használnak.

Az itt található további források a Logic apps szolgáltatással való használatra való létrehozásuk során felmerülő költségekkel járnak:

* Az [integrációs fiók](../logic-apps/logic-apps-pricing.md#integration-accounts) egy különálló erőforrás, amelyet Ön hoz létre, és a logikai alkalmazásokhoz kapcsolódik a B2B-integrációk létrehozásához. Az integrációs fiókok [rögzített díjszabási modellt](../logic-apps/logic-apps-pricing.md#integration-accounts) használnak, ahol a díjszabás a használt integrációs fiók típusától vagy *szintjétől* függ.

* Az [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) egy különálló erőforrás, amelyet olyan logikai alkalmazások üzembe helyezési helyeként hozhat létre, amelyeknek közvetlen hozzáférésre van szükségük a virtuális hálózat erőforrásaihoz. A ISEs olyan [rögzített díjszabási modellt](../logic-apps/logic-apps-pricing.md#fixed-pricing) használnak, amelyben a ráta a létrehozott ISE SKU-ra és egyéb beállításokra épül.

* Az [egyéni összekötő](../logic-apps/logic-apps-pricing.md#consumption-pricing) egy különálló erőforrás, amelyet egy olyan REST API hoz létre, amely nem rendelkezik előre elkészített összekötővel a logikai alkalmazásokban való használathoz. Az egyéni összekötők végrehajtása a használati [díjszabási modellt](../logic-apps/logic-apps-pricing.md#consumption-pricing) használja, kivéve, ha az ISE-ben használja őket.

* A több-bérlős Logic Apps szolgáltatásban az [adatmegőrzési és a tárolási felhasználás](../logic-apps/logic-apps-pricing.md#data-retention) költsége [rögzített díjszabási modell](../logic-apps/logic-apps-pricing.md#fixed-pricing)használatával esedékes. A futtatási előzményekből származó bemenetek és kimenetek például a színfalak mögötti tárolóban maradnak, ami eltér a logikai alkalmazástól függetlenül létrehozott, kezelt és elérhető tárolási erőforrásoktól.

  Az ISE-ben az adatmegőrzési és a tárolási felhasználás költségei nem merülnek fel.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>A költségek az erőforrás törlése után merülhetnek fel

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

A logikai alkalmazások törlését követően a Logic Apps szolgáltatás nem hoz létre vagy nem futtat új munkafolyamat-példányokat. Az összes folyamatban lévő és függőben lévő Futtatás azonban addig folytatódik, amíg be nem fejeződik. A futtatások számától függően ez a folyamat hosszabb időt is igénybe vehet. További információ: [Logic apps kezelése](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Ha ezeket az erőforrásokat a logikai alkalmazás törlése után is elvégezte, akkor ezek az erőforrások továbbra is fennállnak, és csak akkor válnak elérhetővé, ha törli őket:

* Az erőforrásokhoz kapcsolódó logikai alkalmazástól függetlenül létrehozott és felügyelt Azure-erőforrások, például az Azure Function apps, az Event hubok, az Event Gridek stb.

* Integrációs fiókok

* Integrációs szolgáltatási környezetek (ISEs)

  Ha [töröl egy ISE](ise-manage-integration-service-environment.md#delete-ise)-t, a társított Azure-beli virtuális hálózat, alhálózatok és egyéb kapcsolódó erőforrások továbbra is fennállnak. Az ISE törlése után előfordulhat, hogy a virtuális hálózat vagy az alhálózatok törlésének megkezdése előtt meg kell várnia egy adott számú órát.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Pénzügyi kreditek használata Azure Logic Apps

Az nagyvállalati szerződéssel rendelkező pénzügyi kötelezettségvállalással kapcsolatos díjakért Azure Logic Apps díjat is fizethet. Nem használhatja azonban az EA pénzügyi kötelezettségvállalását a harmadik féltől származó termékek és szolgáltatások díjainak kifizetésére, beleértve az Azure piactéren lévőket is.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>A költségek megbecslése

Mielőtt erőforrásokat hozna létre a Azure Logic Apps, az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/)használatával becsülje meg a költségeket. További információkért tekintse át [a Azure Logic apps díjszabási modelljét](../logic-apps/logic-apps-pricing.md).

1. Az [Azure díjszabási kalkulátor lapjának](https://azure.microsoft.com/pricing/calculator/)bal oldali menüjében válassza az **integráció**  >  **Azure Logic apps** elemet.

   ![Képernyőkép, amely az Azure díjszabási számológépét jeleníti meg "Azure Logic Apps" beállítással.](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Görgessen le az oldalra, amíg meg nem tekinti a Azure Logic Apps árképzési számológépet. A Azure Logic Apps közvetlenül kapcsolódó Azure-erőforrások különböző fejezeteiben adja meg a használni kívánt erőforrások számát, valamint azt, hogy hány intervallumon belül használhatja ezeket az erőforrásokat.

   Ez a képernyőkép egy példaként szolgáló költségbecslést mutat be a számológép használatával:

   ![Példa az Azure díjszabási számológépének becsült költségére](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Ha új kapcsolódó erőforrásokat hoz létre és használ, térjen vissza ehhez a kalkulátorhoz, és frissítse az erőforrásokat itt.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Költségvetések és riasztások létrehozása

Az Azure-fiók vagy-előfizetés költségeinek proaktív kezeléséhez létrehozhat [költségvetést](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a [Azure Cost Management és a számlázási](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) szolgáltatás és képességek segítségével.  Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak.

A költségvetés és a költségek küszöbértékeit összevetve a riasztások automatikusan értesítik az érintett feleket a rendellenességek elköltéséről és a kockázatok túlterheléséről. Ha részletesebben szeretné megfigyelni a figyelést, olyan költségvetéseket is létrehozhat, amelyek az Azure adott erőforrásaihoz vagy szolgáltatásaihoz szűrőket használnak. A szűrők segítenek biztosítani, hogy ne hozzon létre véletlenül új erőforrásokat, ami extra pénzbe kerül. A szűrési lehetőségekkel kapcsolatos további információkért lásd: [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Költségek figyelése

Az erőforrás-használati egység költségei időszakonként változnak, például másodperc, perc, óra és nap, vagy egységenkénti használat, például bájtok, megabájtok stb. Néhány példa: nap, aktuális és előző hónap, év. A hosszabb nézetekre való áttérés a kiadások trendjeinek azonosításához nyújt segítséget. A Cost Analysis funkcióinak használatakor grafikonként és táblázatként is megtekintheti a költségeket a különböző időintervallumokban. Ha költségvetést és költség-előrejelzést hozott létre, azt is megteheti, hogy a költségvetések túllépve vannak, és előfordulhat, hogy túllépték a túllépést.

Miután elindította a költségeket az Azure-ban létrehozott vagy elkezdett erőforrásokhoz, a következő módokon tekintheti át és figyelheti meg ezeket a költségeket:

* A [Logic app-végrehajtás és a tárterület-felhasználás figyelése](#monitor-billing-metrics) Azure monitor használatával

* A [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) futtatása [Azure Cost Management és számlázás](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) használatával

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Logikai alkalmazások végrehajtásának és tárterület-felhasználásának figyelése

A Azure Monitor használatával megtekintheti az adott logikai alkalmazás metrikáit:

* Számlázható műveletek végrehajtása
* Számlázható trigger-végrehajtások
* A natív művelet végrehajtásához használt számlázási használat
* A standard szintű összekötők végrehajtásának számlázási használata
* Számlázási használat a tárterület-felhasználáshoz
* Számlázandó végrehajtások összesen

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>A végrehajtás és a tárolás felhasználási metrikáinak megtekintése

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást. A logikai alkalmazás menüjében, a **figyelés** területen válassza a **metrikák** lehetőséget.

1. A jobb oldali ablaktábla **diagram címe** területén a metrika sávban nyissa meg a **metrika** listát, és válassza ki a kívánt mérőszámot.

   > [!NOTE]
   > A tárolási felhasználás mértékét a logikai alkalmazás által használt tárolási egységek száma (GB) alapján számítjuk fel. Előfordulhat, hogy az 500 MB-nál kevesebb tárhelyet használó futtatások nem jelennek meg a figyelés nézetben, de továbbra is számlázás alatt állnak.

   ![A metrikák ablaktáblán a megnyitott "metrika" listával megjelenített képernyőkép.](./media/logic-apps-pricing/select-metric.png)

1. A panel jobb felső sarkában válassza ki a kívánt időtartamot.

1. Az egyéb tárterület-használati adatok megtekintéséhez [hajtsa végre az alábbi lépéseket](#view-input-output-sizes)a logikai alkalmazás futtatási előzményeiben szereplő, konkrét bemeneti és kimeneti méretekkel.

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>A műveletek bemeneti és kimeneti méretének megtekintése a futtatási előzményekben

1. A Azure Portal keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget.

1. A jobb oldali ablaktábla futtatási **Előzmények** területén válassza ki a megtekinteni kívánt bemeneteket és kimeneteket tartalmazó futtatást.

1. A **logikai alkalmazás futtatása** területen válassza a **Futtatás részletek** lehetőséget.

1. Válassza ki a megtekinteni kívánt műveletet a **logikai alkalmazás futtatása részletek** panelen, a műveletek táblában, amely felsorolja az egyes műveletek állapotát és időtartamát.

1. A **logikai alkalmazás műveleti** ablaktábláján keresse meg a művelet bemeneteit és kimeneteit. A **bemenetek** és **kimenetek hivatkozás** alatt keresse meg a bemenetekre és kimenetekre mutató hivatkozásokat.

   > [!NOTE]
   > A hurkok esetében csak a legfelső szintű műveletek jelennek meg a bemenetek és kimenetek méretében. Beágyazott hurkok esetén a bemenetek és a kimenetek nulla méretet és hivatkozások nélkül jelennek meg.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>A Cost Analysis futtatása Azure Cost Management és számlázás használatával

Ha egy adott hatókör (például egy Azure-előfizetés) alapján szeretné áttekinteni a Logic Apps szolgáltatás költségeit, használhatja a [Azure Cost Management és a számlázás](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) funkcióit.

1. A Azure Portal nyissa meg a kívánt hatókört, például az Azure-előfizetését. A bal oldali menü **Cost Management** területén válassza a **Cost Analysis** elemet.

   Amikor először nyitja meg a Cost Analysis (költség elemzése) panelt, a felső diagram a tényleges és az előre jelzett használati költséget jeleníti meg az előfizetés összes szolgáltatásában az aktuális hónapra vonatkozóan.

   ![A Azure Portal és a Cost Analysis panelt bemutató képernyőkép, amely az előfizetések tényleges és előre jelzett költségeire mutat példát.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > A hatókörök módosításához a **Cost Analysis** ablaktáblán a szűrők sávon válassza ki a **hatókör** szűrőt. A **hatókör kiválasztása** ablaktáblán váltson a kívánt hatókörre.

   A fánk-diagramok alatt az Azure-szolgáltatások, az Azure-régió (hely) és az erőforráscsoport aktuális költségei láthatók.

   ![Képernyőkép a Azure Portal és a Cost Analysis panelről, például a szolgáltatások, a régiók és az erőforráscsoportok fánk-diagramjaival.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Ha szűrni szeretné a diagramot egy adott területre, például egy szolgáltatásra vagy erőforrásra, a szűrők sávon válassza a **szűrő hozzáadása** lehetőséget.

1. A bal oldali listában válassza ki a szűrő típusát, például: **szolgáltatásnév**. A jobb oldali listában válassza ki a szűrőt, például: **Logic apps**. Ha elkészült, válassza a zöld pipa jelet.

   ![Képernyőfelvétel: a Azure Portal és a Cost Analysis ablaktáblát jeleníti meg a szűrők kiválasztásával.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Például itt látható a Logic Apps szolgáltatás eredménye:

   ![Képernyőfelvétel: Azure Portal és Cost Analysis panel, amely a "Logic apps" által szűrt eredményeket jeleníti meg.](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Költségadatok exportálása

Ha további adatelemzésre van szüksége a költségekkel kapcsolatban, a [költségadatok](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) a Storage-fiókba exportálhatók. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti ezeket az adatforrásokat. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="other-ways-to-manage-and-reduce-costs"></a>A költségek kezelésének és csökkentésének egyéb módjai

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

A logikai APS és a kapcsolódó erőforrások költségeinek csökkentéséhez próbálja ki a következő lehetőségeket:

* Ha lehetséges, használjon [beépített eseményindítókat és műveleteket](../connectors/apis-list.md#built-in), amelyek költsége kevesebb, mint a [felügyelt összekötő-eseményindítók és műveletek](../connectors/apis-list.md#managed-connectors)végrehajtása.

  Előfordulhat például, hogy csökkenteni tudja a költségeket, amikor más erőforrásokhoz fér hozzá a [http-művelettel](../connectors/connectors-native-http.md) , vagy egy olyan függvényt hív meg, amelyet a [Azure functions szolgáltatással](../azure-functions/functions-overview.md) hozott létre, és a [beépített Azure functions műveletet](../logic-apps/logic-apps-azure-functions.md)használja. A Azure Functions használata azonban a költségeket is felhasználja, ezért ügyeljen arra, hogy összehasonlítsa a beállításait.

* Határozza meg a munkafolyamat futtatásának [pontos kiváltó feltételeit](logic-apps-workflow-actions-triggers.md#trigger-conditions) .

  Megadhatja például, hogy az eseményindító csak akkor induljon el, ha a célként megadott webhely belső kiszolgálóhiba. Az trigger JSON-definíciójában használja a `conditions` tulajdonságot egy olyan feltétel megadásához, amely hivatkozik az trigger állapotkódot.

* Ha egy eseményindító lekérdezési verzióval és webhook-verzióval rendelkezik, próbálja meg a webhook verzióját, amely megvárja, amíg a megadott esemény bekövetkezik az égetés előtt, és nem az esemény rendszeres ellenőrzéséhez.

* Hívja meg a logikai alkalmazást egy másik szolgáltatáson keresztül, hogy az eseményindító csak akkor fusson, amikor a munkafolyamatnak futnia kell.

  Meghívhatja például a logikai alkalmazást egy, a Azure Functions szolgáltatással létrehozott és futtatott függvényből. Lásd például: a [Logic apps meghívása vagy elindítása Azure functions és Azure Service Bus használatával](logic-apps-scenario-function-sb-trigger.md).

* [Tiltsa le azokat a logikai alkalmazásokat](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) , amelyeknek nem kell állandóan futniuk, vagy törölnie kell azokat a [logikai alkalmazásokat](manage-logic-apps-with-azure-portal.md#delete-logic-apps) , amelyekre már nincs szüksége. Ha lehetséges, tiltsa le az összes olyan erőforrást, amelyhez nem szükséges állandóan aktív.

## <a name="next-steps"></a>Következő lépések

* [A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Költségek kezelése a Cost Analysis használatával](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Váratlan költségek megelőzése](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Ismerkedjen meg a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamával