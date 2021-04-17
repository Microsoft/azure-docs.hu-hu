---
title: Tervezze meg a költségek kezelését a Azure Synapse Analytics
description: Megtudhatja, hogyan tervezhet és kezelhet költségelemzést a Azure Synapse Analytics költségelemzéssel a Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 15ac6ce6a1a49bbbd15849adec373dd0fcd42c10
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568522"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>A költségek megterve és kezelése a Azure Synapse Analytics

Ez a cikk a költségek tervezését és kezelését ismerteti a Azure Synapse Analytics. Először az Azure díjkalkulátor használatával tervezze meg a Azure Synapse költségeit, mielőtt további erőforrásokat ad hozzá a szolgáltatáshoz a költségek becsléséhez. Ezután az erőforrások hozzáadása Azure Synapse tekintse át a becsült költségeket.

Az erőforrások használatának Azure Synapse után a Cost Management szolgáltatásokat használhatja a költségvetések beállításhoz és a költségek figyelésére. Emellett áttekintheti az előrejel jelzett költségeket, és azonosíthatja a költségtrendeket, így azonosíthatja azokat a területeket, ahol érdemes lehet cselekedni. A Azure Synapse az Azure-számla havi költségeinek csak egy részét. Bár ez a cikk bemutatja, hogyan tervezze meg és kezelje a Azure Synapse költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatásért és -erőforrásért, beleértve a külső szolgáltatásokat is, ki kell fizetni.

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés a Cost Management támogatja a legtöbb Azure-fióktípust, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási jogosultságra van szüksége egy Azure-fiókhoz. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>A költségek becslése a Azure Synapse Analytics

Az [Azure-díjkalkulátor használatával](https://azure.microsoft.com/pricing/calculator/) megbecsülheti a költségeket, mielőtt hozzáadja Azure Synapse Analytics.

Azure Synapse erőforrásokkal rendelkezik, amelyek különböző díjakat számolnak fel az alábbi költségbecslésben látható módon. 

![Példa az Azure-díjkalkulátor becsült költségeire](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>A teljes számlázási modell Azure Synapse Analytics

Azure Synapse azure-infrastruktúrán futnak, amely az új erőforrás üzembe helyezésekor Azure Synapse felhalmozott költségeket is. Fontos megérteni, hogy további infrastruktúra is felmerülhet a költségekben. Ezt a költséget akkor kell kezelnie, amikor módosítja az üzembe helyezett erőforrásokat. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>A költségek jellemzően a Azure Synapse Analytics

Amikor erőforrásokat hoz létre a Azure Synapse, más Azure-szolgáltatások erőforrásai is létrejönnek. Ezek közé tartoznak például az alábbiak:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Az erőforrás törlése után felmerülhet a költségek

A Azure Synapse törlése után a következő erőforrások továbbra is létezhetnek. A költségek továbbra is felhalmozottak, amíg Ön nem törli őket.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Azure-előrefizetési kredit használata Azure Synapse 

A díjakért Azure Synapse Azure-előrefizetési kredittel (korábban pénzügyi kötelezettségvállalás) fizethet. Az Azure-előrefizetési kreditek azonban nem használhatók fel harmadik féltől származó termékek és szolgáltatások díjának kifizetése érdekében, beleértve a Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

Amikor erőforrásokat hoz létre a Azure Synapse Analytics, láthatja a becsült költségeket. A munkaterülethez a munkaterülettel létrehozott kiszolgáló nélküli SQL-készlet tartozik. A kiszolgáló nélküli SQL-készlet nem fog költségeket fizetni, amíg nem futtat lekérdezéseket. Más erőforrásokat, például dedikált SQL-készleteket és kiszolgáló nélküli Apache Spark készleteket a munkaterületen kell létrehozni.

A létrehozása <ResourceName> és a becsült ár megtekintése:

1. Lépjen a szolgáltatáshoz a Azure Portal.
2. Hozza létre az erőforrást.
3. Tekintse át az összefoglalásban látható becsült árat.
4. Fejezze be az erőforrás létrehozását.

![Példa a becsült költségekre az erőforrás létrehozása során](./media/plan-manage-costs/create-workspace-cost.png)


Ha az Azure-előfizetés rendelkezik költségkeretgel, az Azure megakadályozza, hogy a kreditösszeget túllépve költsen. Az Azure-erőforrások létrehozásakor és használata esetén a kreditek felhasználása is meg fog felerősedni. A kreditkorlát elérésekor az üzembe helyezett erőforrások az adott számlázási időszak hátralévő részére le vannak tiltva. A kreditkeretet nem módosíthatja, de eltávolíthatja. További információ a költségkeretről: [Azure-költségkeret.](../cost-management-billing/manage/spending-limit.md)

## <a name="monitor-costs"></a>Költségek figyelése

Az erőforrások Azure Synapse költségekkel jár. Az Azure-erőforrás-használati egység költségei időintervallumok (másodpercek, percek, órák és napok) vagy egységhasználat (bájt, megabájt stb.) alapján változnak. Amint elkezdi használni az erőforrásokat a Azure Synapse költségek merülnek fel, és a költségek a [költségelemzésben is láthatóak lesznek.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

Költségelemzés használata esetén a költségelemzési költségek Azure Synapse Analytics különböző időintervallumok szerint gráfokban és táblákban. Néhány példa nap, aktuális és előző hónap és év szerint. Emellett megtekintheti a költségeket a költségvetésekkel és az előrejel jelzett költségekkel szemben. A hosszabb nézetekre való váltás segíthet a költési trendek azonosításában. Azt is láthatja, hogy a túlk függőben lévő események hol történtek. Ha költségvetéseket hozott létre, azt is könnyen láthatja, hol lépi túl a költségvetéseket.

A költségelemzés Azure Synapse költségek megtekintése:

1. Jelentkezzen be az Azure Portalra.
2. Nyissa meg a hatókört (az előfizetést vagy az erőforráscsoportot) a Azure Portal válassza a **Költségelemzés** lehetőséget a menüben. Például válassza az **Előfizetések** lehetőséget, válasszon ki egy előfizetést a listából, majd válassza a  **Költségelemzés** lehetőséget a menüben. Válassza **a Hatókör** lehetőséget egy másik hatókörre való váltáshoz a költségelemzésben.
3. Alapértelmezés szerint a szolgáltatások költségei az első fánkdiagramon jelennek meg. Válassza ki a diagramon a következő címkével Azure Synapse.

A tényleges havi költségek a költségelemzés első megnyitásakor jelennek meg. Az alábbi példa az összes havi használati költséget mutatja be.

![Példa egy előfizetés halmozott költségeire](./media/plan-manage-costs/actual-monthly-costs.png)

- Ha egy szolgáltatás költségeit szeretné leszűkíteni( például a Azure Synapse, válassza a **Szűrő** hozzáadása, majd a **Szolgáltatásnév lehetőséget.** Ezután válassza a **Azure Synapse Analytics** lehetőséget.

Az alábbi példában csak a Azure Synapse.

![Példa a ServiceName halmozott költségeire](./media/plan-manage-costs/filtered-monthly-costs.png)

Az előző példában a szolgáltatás aktuális költsége látható. A költségek Azure-régiók (helyek) és Azure Synapse erőforráscsoport szerint jelennek meg. Innen saját maga is megismerheti a költségeket.

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Költségvetések és riasztások az Azure-előfizetések és -erőforráscsoportok számára vannak létrehozva, így egy általános költségfigyelési stratégia részeként hasznosak. 

A költségvetések adott erőforrásokra vagy szolgáltatásokra vonatkozó szűrőkkel is létrehozhatóak az Azure-ban, ha részletesebb monitorozást szeretne. A szűrőkkel biztosíthatja, hogy ne hozzon létre véletlenül olyan új erőforrásokat, amelyek további pénzbe kerülnek. A költségvetés létrehozásakor elérhető szűrési beállításokról további információt a Csoportosítási [és szűrési beállításokat(](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)) tartalmaz.

## <a name="export-cost-data"></a>Költségadatok exportálása

A [költségadatokat egy tárfiókba](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) is exportálhatja. Ez akkor lehet hasznos, ha vagy másoknak kell további adatelemzést végzette a költségekről. A pénzügyi csapatok például elemezheti az adatokat az Excel vagy a Power BI. A költségeket napi, heti vagy havi ütemezés szerint exportálhatja, és egyéni dátumtartományt állíthat be. A költségadatkészletek lekérésének ajánlott módja a költségadatok exportálása.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Egyéb módszerek a költségek kezelésére és csökkentésére a Azure Synapse 

### <a name="serverless-sql-pool"></a>Kiszolgáló nélküli SQL-készlet

További információ a kiszolgáló nélküli SQL-készlet költségeiről: [Cost Management for serverless SQL pool in Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Dedikált SQL-készlet

A dedikált SQL-készlet költségeit úgy szabályozhatja, hogy szünetelteti az erőforrást, amikor az nincs használatban. Ha például nem használja az adatbázist éjszaka és hétvégeken, szüneteltetheti az idő alatt, és a nap folyamán folytathatja. További információ: Számítási feladatok szüneteltetése és folytatása dedikált [SQL-készletben a Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Kiszolgáló nélküli Apache Spark készlet

A kiszolgáló nélküli Apache Spark költségeinek szabályozása érdekében engedélyezze a kiszolgáló nélküli Apache Spark felfüggesztés funkciót, és ennek megfelelően állítsa be az időtúllépési értéket.  Ha a Synapse Studio használ, a Studio életben tartás üzenetet küld, hogy a munkamenet aktív maradjon, amely szintén konfigurálható, ezért állítson be egy rövid időtúllépési értéket az automatikus szüneteltetéshez.  Ha végzett, zárja be a munkamenetet, és a Apache Spark automatikusan szünetel az időtúllépési érték elérésekor.
 
A fejlesztés során hozzon létre Apache Spark különböző méretű készletdefiníciókat.  A Apache Spark készletdefiníciók létrehozása ingyenes, és csak a használatért kell fizetnie.  Apache Spark használati Azure Synapse virtuális mag óránként van felszámítva, és perc szerint idő szerint van elszámítva.  Használhat például kis méretű készleteket a kódfejlesztéshez és -ellenőrzéshez, míg a nagyobb készletméreteket a teljesítménytteszteléshez.


### <a name="data-integration---pipelines-and-data-flows"></a>Adatintegráció – folyamatok és adatfolyamok 

További információ az adatintegrációs [költségekről: Az](../data-factory/plan-manage-costs.md) adatintegrációs költségek Azure Data Factory

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan optimalizálhatja felhővel való befektetését a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- További információ a költségek költségelemzéssel [való kezeléséről.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Ismerje meg, hogyan [előzheti meg a váratlan költségeket.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Vegyen részt [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oktató kurzusán.
- Tudnivalók a költségek tervezéséről [és kezeléséről](../machine-learning/concept-plan-manage-cost.md) a Azure Machine Learning