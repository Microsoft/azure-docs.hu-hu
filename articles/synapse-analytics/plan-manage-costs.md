---
title: Az Azure szinapszis Analytics költségeinek kezelésének megtervezése
description: Ismerje meg, hogyan tervezheti meg és kezelheti az Azure szinapszis Analytics költségeit a Azure Portal a Cost Analysis használatával.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 06586b5bf20619f57b2ad1c3d5de84dd61952261
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561245"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Az Azure szinapszis Analytics költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure szinapszis Analytics költségeit. Első lépésként az Azure díjszabási kalkulátor segítségével tervezze meg az Azure szinapszis költségeit, mielőtt bármilyen erőforrást felvesz a szolgáltatáshoz a költségek becslése érdekében. Ezután az Azure szinapszis-erőforrások hozzáadásakor tekintse át a becsült költségeket.

Az Azure szinapszis-erőforrások használatának elkezdése után Cost Management-funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. Az Azure szinapszis költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure-beli szinapszisok költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a külső szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis Cost Management támogatja a legtöbb Azure-fiók típusát, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>A költségek becslése az Azure szinapszis Analytics használata előtt

Az Azure- [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a költségeket az Azure szinapszis Analytics hozzáadása előtt.

Az Azure szinapszis különböző erőforrásokkal rendelkezik, amelyek az alábbi költségbecslés alapján eltérő költségekkel rendelkeznek. 

![Példa az Azure díjszabási számológépének becsült költségére](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Ismerje meg az Azure szinapszis Analytics teljes számlázási modelljét

Az Azure szinapszis olyan Azure-infrastruktúrán fut, amely az új erőforrás telepítésekor a költségeket és az Azure Szinapszisot is felhalmozza. Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Az Azure szinapszis Analytics szolgáltatással általában felmerülő költségek

Amikor erőforrásokat hoz létre az Azure Szinapszishoz, más Azure-szolgáltatások erőforrásai is létrejönnek. Ezek közé tartoznak például az alábbiak:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>A költségek az erőforrás törlése után merülhetnek fel

Az Azure szinapszis-erőforrások törlése után a következő erőforrások továbbra is létezhetnek. Amíg nem törli őket, továbbra is felmerülnek a költségek.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure-előfizetési kredit használata az Azure szinapszis használatával 

Az Azure szinapszis díjait az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel fizetheti ki. Az Azure-beli előfizetési kreditek azonban nem használhatók fel a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren fizetendő díjakért.

## <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

Az Azure szinapszis Analytics erőforrásainak létrehozásakor a becsült költségek láthatók. A munkaterület a munkaterülettel létrehozott kiszolgáló nélküli SQL-készlettel rendelkezik. A kiszolgáló nélküli SQL-készlet nem számít fel díjat, amíg le nem futtatja a lekérdezéseket. Az egyéb erőforrásokat, például a dedikált SQL-készleteket és a kiszolgáló nélküli Apache Spark készleteket a munkaterületen belül kell létrehozni.

A (z) létrehozása <ResourceName> és a becsült ár megtekintése:

1. Navigáljon a szolgáltatáshoz a Azure Portal.
2. Hozza létre az erőforrást.
3. Tekintse át az összegzésben látható becsült árat.
4. Fejezze be az erőforrás létrehozását.

![Példa a becsült költségekre az erőforrás létrehozása során](./media/plan-manage-costs/create-workspace-cost.png)


Ha az Azure-előfizetése költségkerettel rendelkezik, az Azure megakadályozza a kreditek összegének megadását. Az Azure-erőforrások létrehozásakor és használatakor a rendszer felhasználja a krediteket. Ha eléri a hitelkeretét, az üzembe helyezett erőforrások a számlázási időszak további részében le lesznek tiltva. A hitelkeret nem módosítható, de eltávolíthatja. További információ a költségkeretekről: az [Azure](../cost-management-billing/manage/spending-limit.md)költségkerete.

## <a name="monitor-costs"></a>Költségek figyelése

Az Azure-beli szinapszis-erőforrások használata során költségek merülnek fel. Az Azure Erőforrás-használati egység költségei időintervallumok (másodperc, perc, óra és nap), vagy egységenkénti használat (bájt, megabájt stb.) szerint változnak. Amint megkezdi az erőforrások használatát az Azure Szinapszisban, a költségek felmerülnek, és megtekintheti a költségeket a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatásban.

Ha a Cost Analysis szolgáltatást használja, az Azure szinapszis Analytics költségeit a különböző időintervallumok diagramjaiban és tábláiban tekintheti meg. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetésekkel és az előre jelzett költségekkel is megtekintheti. A hosszabb nézetekre való áttérés a kiadások trendjeinek azonosításához nyújt segítséget. Itt láthatja, hogy hol történt a túltöltés. Ha létrehozta a költségvetést, azt is megteheti, hogy megtekintheti a túllépések helyét.

Az Azure szinapszis költségeinek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az Azure portálra.
2. Nyissa meg a hatókört, vagy az előfizetést vagy az erőforráscsoportot, majd a menüben válassza a **Cost Analysis** (Azure Portal) elemet. Például lépjen az **előfizetések** elemre, válasszon ki egy előfizetést a listából, majd válassza a menü  **Cost Analysis** elemét. Válassza ki a **hatókört** , hogy másik hatókörre váltson a Cost Analysis szolgáltatásban.
3. Alapértelmezés szerint a szolgáltatások díjszabása az első fánk-diagramon látható. Válassza ki az Azure szinapszis nevű diagramot.

A tényleges havi költségek akkor jelennek meg, amikor először nyitja meg a Cost Analysis-t. Az alábbi példa az összes havi használati költséget mutatja be.

![Példa az előfizetés halmozott költségeire](./media/plan-manage-costs/actual-monthly-costs.png)

- Egy szolgáltatás (például az Azure szinapszis) költségeinek szűkítése érdekében válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget. Ezután válassza az **Azure szinapszis Analytics** lehetőséget.

Íme egy példa, amely csak az Azure szinapszis költségeit mutatja be.

![Példa a szolgáltatásnév halmozott költségeinek megjelenítésére](./media/plan-manage-costs/filtered-monthly-costs.png)

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók költségei (helyszínek) és az Azure szinapszis költségei az erőforráscsoport szerint is megjelennek. Itt megtekintheti saját költségeit.

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről a költségvetés létrehozásakor, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Az Azure szinapszisok költségeinek kezelésével és csökkentésével kapcsolatos egyéb módszerek 

### <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

Ha többet szeretne megtudni a kiszolgáló nélküli SQL-készlet költségeiről, tekintse meg a [kiszolgáló nélküli SQL-készlet költséghatékonyságát az Azure szinapszis Analyticsben](./sql/data-processed.md) című témakört.

### <a name="dedicated-sql-pool"></a>Dedikált SQL-készlet

A dedikált SQL-készlet költségeit a nem használt erőforrás szüneteltetésével szabályozhatja. Ha például nem fogja használni az adatbázist az éjszaka és a hétvégén, szüneteltetheti ezeket az időpontokban, és a nap folyamán folytathatja. További információ: [a számítási feladat szüneteltetése és folytatása a DEDIKÁLT SQL-készletben a Azure Portal használatával](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Kiszolgáló nélküli Apache Spark készlet

A kiszolgáló nélküli Apache Spark készlet költségeinek szabályozásához engedélyezze a kiszolgáló nélküli Apache Spark automatikus szüneteltetése funkciót, és ennek megfelelően állítsa be az időtúllépés értékét.  Ha a szinapszis Studiot használja a fejlesztéshez, a Studio egy életben tartási üzenetet küld, amely az aktív munkamenetet is beállíthatja, ezért az automatikus szüneteltetéshez egy rövid időtúllépési értéket kell beállítani.  Ha elkészült, zárd be a munkamenetet, és a Apache Spark készlet automatikusan szüneteltetve lesz az időtúllépési érték elérésekor.
 
A fejlesztés során hozzon létre több Apache Spark különböző méretű készlet-definíciókat.  Apache Spark készlet-definíciók létrehozása ingyenes, és csak a használatért kell fizetnie.  Az Azure Szinapszisban való használat Apache Spark virtuális mag óránként történik, és a percben számolva.  Használjon például kisebb méretű készleteket a kódok fejlesztéséhez és érvényesítéséhez, miközben nagyobb méretű készleteket használ a teljesítmény teszteléséhez.


### <a name="data-integration---pipelines-and-data-flows"></a>Adatintegráció – folyamatok és adatfolyamok 

További információ az adatintegrációs költségekről: [a Azure Data Factory költségeinek tervezése és kezelése](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.
- További információ a [Azure Machine learning](../machine-learning/concept-plan-manage-cost.md) költségeinek tervezéséről és kezeléséről