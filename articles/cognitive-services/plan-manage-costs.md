---
title: Az Azure Cognitive Services költségeinek kezelésének megtervezése
description: Ismerje meg, hogyan tervezheti meg és kezelheti az Azure-Cognitive Services költségeit a Azure Portalban a Cost Analysis használatával.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 661af89ad223978abbefd71dd8008577475c5875
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221377"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Az Azure Cognitive Services költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure-Cognitive Services költségeit. Először is az Azure díjszabási kalkulátor segítségével tervezze meg a Cognitive Services költségeket, mielőtt felveszi a költségek kiszámításához szükséges erőforrásokat a szolgáltatáshoz. Ezután az Azure-erőforrások hozzáadásakor tekintse át a becsült költségeket. A Cognitive Services-erőforrások használatának elkezdése (például beszéd, Computer Vision, LUIS, Text Analytics, Translator stb.) használata után Cost Management funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. A Cognitive Services költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a Cognitive Services költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a külső szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis Cost Management támogatja a legtöbb Azure-fiók típusát, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>A költségek becslése a használata előtt Cognitive Services

Az [Azure díjszabási kalkulátorával](https://azure.microsoft.com/pricing/calculator/) a Cognitive Services hozzáadása előtt megbecsülheti a költségeket.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Az Azure díjszabási kalkulátora Cognitive Services" border="true":::

Amikor új erőforrásokat ad hozzá a munkaterülethez, térjen vissza ehhez a kalkulátorhoz, és adja hozzá ugyanezt az erőforrást a költségbecslés frissítéséhez.

További információ: [Azure Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>A Cognitive Services teljes számlázási modelljének megismerése

Cognitive Services az Azure-infrastruktúrán fut, amely az új erőforrás telepítésekor [felmerülő költségekkel jár](https://azure.microsoft.com/pricing/details/cognitive-services/) . Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Általában a Cognitive Servicesekkel felmerülő költségek

Az Azure-erőforrások üzembe helyezése után általában a díjszabási szintet és az API-hívásokat a végponton kell meghatározni. Ha az Ön által használt szolgáltatás kötelezettségvállalási szinten van, akkor az adott szinten eljuttatott hívások túllépik a túlterhelést.

A szolgáltatások használatakor további költségek merülhetnek fel:

#### <a name="qna-maker"></a>QnA Maker

Ha QnA Maker erőforrásokat hoz létre, az egyéb Azure-szolgáltatások erőforrásai is létrehozhatók. Ezek közé tartoznak például az alábbiak:

- [Azure App Service (a futtatókörnyezethez)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (az adatkezeléshez)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Az erőforrás törlése után felmerülhető költségek

#### <a name="qna-maker"></a>QnA Maker

QnA Maker erőforrások törlését követően a következő erőforrások továbbra is létezhetnek. Amíg nem törli őket, továbbra is felmerülnek a költségek.

- [Azure App Service (a futtatókörnyezethez)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (az adatkezeléshez)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Azure-előfizetési kredit használata Cognitive Services

Az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel Cognitive Services díjat fizethet. Az Azure-beli előfizetési kreditek azonban nem használhatók fel a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren fizetendő díjakért.

## <a name="monitor-costs"></a>Költségek figyelése

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Az Azure-erőforrások Cognitive Services használatával történő használatakor a költségek is felmerülnek. Az Azure Erőforrás-használati egység költségei időintervallumok (másodperc, perc, óra és nap) vagy egységenkénti használat szerint változnak (bájt, megabájt stb.). A kognitív szolgáltatás (vagy Cognitive Services) elindulásakor a költségek felmerülnek, és a költségek [elemzésében](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)láthatja a költségeket.

A Cost Analysis használatakor a különböző időintervallumokhoz tartozó grafikonok és táblák Cognitive Services költségeit tekintheti meg. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetésekkel és az előre jelzett költségekkel is megtekintheti. A hosszabb nézetekre való áttérés a kiadások trendjeinek azonosításához nyújt segítséget. Itt láthatja, hogy hol történt a túltöltés. Ha létrehozta a költségvetést, azt is megteheti, hogy megtekintheti a túllépések helyét.

Cognitive Services költségek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az Azure portálra.
2. Nyissa meg a hatókört a Azure Portalban, és válassza a menü **Cost Analysis** elemét. Például lépjen az **előfizetések** elemre, válasszon ki egy előfizetést a listából, majd válassza a menü  **Cost Analysis** elemét. Válassza ki a **hatókört** , hogy másik hatókörre váltson a Cost Analysis szolgáltatásban.
3. Alapértelmezés szerint a szolgáltatások díjszabása az első fánk-diagramon látható. Válassza ki a diagram Cognitive Services feliratú részét.

A tényleges havi költségek akkor jelennek meg, amikor először nyitja meg a Cost Analysis-t. Az alábbi példa az összes havi használati költséget mutatja be.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Példa az előfizetés halmozott költségeire":::

- Egy szolgáltatás (például a Cognitive Services) szűk költségeihez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget. Ezután válassza a **Cognitive Services** lehetőséget.

Íme egy példa, amely csak a Cognitive Services költségeire mutat.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Példa a Cognitive Services összesített költségeire":::

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók (helyszínek) és a Cognitive Services az erőforráscsoport költségei is megjelennek. Itt megtekintheti saját költségeit.

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről, amikor költségvetést hoz létre, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. A pénzügyi csapat például az Excelben vagy a Power BI használatával elemezheti az adatforrást. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.