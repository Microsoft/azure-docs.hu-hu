---
title: A Azure SQL Database költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezheti meg és kezelheti Azure SQL Database költségeit a Azure Portal a Cost Analysis használatával.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734630"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>A Azure SQL Database költségeinek megtervezése és kezelése

Ez a cikk a Azure SQL Database költségeinek tervezését és kezelését ismerteti. Először az Azure díjszabási számológépét használja az Azure-erőforrások hozzáadásához és a becsült költségek áttekintéséhez. Azure SQL Database erőforrások használatának elkezdése után a Cost Management funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. A Azure SQL Database költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a Azure SQL Database költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is.


## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis a legtöbb Azure-fiók típusát támogatja, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. 

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>A kezdeti díjszabási megfontolások SQL Database

A Azure SQL Database használata során több költségtakarékos funkció is megtekinthető:


### <a name="vcore-or-dtu-purchasing-models"></a>Virtuális mag vagy DTU beszerzési modellek 

Azure SQL Database két beszerzési modellt támogat: a virtuális mag és a DTU. A felszámított díjak a beszerzési modellektől függően változnak, ezért fontos, hogy tisztában legyen azzal a modellel, amely a számítási feladatokhoz a költségek megtervezése és tervezése során a legjobban működik. További információ a virtuális mag és a DTU vásárlási modellekről: [választás a virtuális mag és a DTU beszerzési modelljei között](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Kiépített vagy kiszolgáló nélküli

A virtuális mag beszerzési modellje Azure SQL Database a számítási rétegek két típusát is támogatja: kiépített átviteli sebesség és kiszolgáló nélküli. Az egyes számítási szintek díjszabása eltérő lehet, ezért fontos tisztában lennie azzal, hogy mi a legmegfelelőbb a számítási feladatokhoz, amikor megtervezik és mérlegelik a költségeket. Részletekért lásd: [virtuális mag-modell áttekintése – számítási szintek](service-tiers-vcore.md#compute-tiers).

A virtuális mag-alapú vásárlási modell kiépített számítási szintjein a meglévő licencek kedvezményes díjszabásra válthatók. Részletekért lásd: [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Rugalmas készletek

A különböző és előre nem látható használati igényekkel rendelkező környezetekben a rugalmas készletek költségmegtakarítást biztosítanak az azonos mennyiségű önálló adatbázis kiépítéséhez képest. Részletekért lásd: [rugalmas készletek](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Azure SQL Database költségek becslése

Az [Azure díjszabási számológépének](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a különböző Azure SQL Database konfigurációk költségeit. Az alábbi képen látható információk és díjszabás kizárólag példaként szolgál:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Példa Azure SQL Database árképzési számológépre":::

Azt is megbecsülheti, hogy a különböző adatmegőrzési házirend-beállítások hogyan befolyásolják a költségeket. Az alábbi képen látható információk és díjszabás kizárólag példaként szolgál:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="A Azure SQL Database díjszabásának számítási példája a tároláshoz":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>A Azure SQL Database teljes számlázási modelljének megismerése

Azure SQL Database olyan Azure-infrastruktúrán fut, amely költségekkel jár együtt, Azure SQL Database az új erőforrás telepítésekor. Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie. 


A Azure SQL Database (a kiszolgáló nélküli) számlázása kiszámítható, óradíj alapján történik. Ha az SQL-adatbázis egy óránál kevesebb ideig aktív, a rendszer minden órában megszámolja az adatbázist, amely az adott órán belül alkalmazott legmagasabb szolgáltatási szintet, a kiosztott tárterületet és az i/o-t használja, függetlenül a használattól és attól, hogy az adatbázis egy óránál kevesebb ideig volt-e aktív.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Pénzügyi kreditek használata Azure SQL Database

Az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel Azure SQL Database díjat fizethet. Az Azure-beli előfizetési kreditek azonban nem használhatók fel a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren fizetendő díjakért.

## <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

A Azure SQL Database létrehozásának folyamata során a számítási szintek konfigurálása során a becsült költségeket láthatja. 

A képernyő eléréséhez válassza az **adatbázis konfigurálása** elemet a **create SQL Database** ( **alapok** ) lapon. Az alábbi képen látható információk és díjszabás kizárólag példaként szolgál:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Példa: költségbecslés a Azure Portalban":::



Ha az Azure-előfizetése költségkerettel rendelkezik, az Azure megakadályozza a kreditek összegének megadását. Az Azure-erőforrások létrehozásakor és használatakor a rendszer felhasználja a krediteket. Ha eléri a hitelkeretét, az üzembe helyezett erőforrások a számlázási időszak további részében le lesznek tiltva. A hitelkeret nem módosítható, de eltávolíthatja. További információ a költségkeretekről: az [Azure](../../cost-management-billing/manage/spending-limit.md)költségkerete.

## <a name="monitor-costs"></a>Költségek figyelése

A Azure SQL Database használatának megkezdése után a portálon megtekintheti a becsült költségeket. A következő lépésekkel tekintheti át a költségbecslést:

1. Jelentkezzen be a Azure Portalba, és navigáljon az Azure SQL Database-hez tartozó erőforráscsoporthoz. Az erőforráscsoportot úgy keresheti meg, hogy megnyitja az adatbázist, és kiválasztja az **erőforráscsoport** elemet az **Áttekintés** szakaszban.
1. A menüben válassza a **Cost Analysis** elemet.
1. Tekintse meg a **halmozott költségeket** , és állítsa a diagramot az alján a **szolgáltatás nevére**. Ez a diagram a jelenlegi SQL Database költségeinek becslését jeleníti meg. Ha a teljes oldalra szűkíteni szeretné a költségeket Azure SQL Database, válassza a **szűrő hozzáadása** lehetőséget, majd válassza a **Azure SQL Database** lehetőséget. Az alábbi képen látható információk és díjszabás kizárólag példaként szolgál:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Példa a Azure Portal halmozott költségeire":::

Itt megtekintheti saját költségeit. A különböző költség-elemzési beállításokkal kapcsolatos további információkért lásd: a [költségek elemzésének megkezdése](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Költségvetések létrehozása

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

A költségek kezeléséhez [költségvetéseket](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről, amikor költségvetést hoz létre, tekintse meg a [csoportosítási és szűrési beállítások](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Egyéb módszerek a Azure SQL Database költségeinek kezeléséhez és csökkentéséhez

A Azure SQL Database lehetővé teszi az erőforrások felfelé vagy lefelé méretezését az alkalmazás igényeinek megfelelő költségek szabályozása érdekében. Részletekért lásd: [adatbázis-erőforrások dinamikus méretezése](scale-resources.md).

A számítási erőforrások foglalásával egy-három évig megtakaríthatja a pénzt. Részletekért lásd: a [fenntartott kapacitással rendelkező erőforrások költségeinek megtakarítása](reserved-capacity-overview.md).


## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.