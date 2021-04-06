---
title: Az Azure ExpressRoute költségeinek kezelésének megtervezése
description: Megtudhatja, hogyan tervezheti meg és kezelheti az Azure ExpressRoute költségeit a Azure Portal a Cost Analysis használatával.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: de8405477611d62b8a46e8b6b645887cc4d30099
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98784241"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Az Azure ExpressRoute költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a ExpressRoute költségeit. Első lépésként az Azure díjszabási kalkulátor segítségével megtervezheti a ExpressRoute költségeket, mielőtt a szolgáltatáshoz bármilyen erőforrást felvesz a költségek becslése érdekében. Az Azure-erőforrások hozzáadásakor tekintse át a becsült költségeket. 

A ExpressRoute-erőforrások használatának elkezdése után Cost Management funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. 

Ne feledje, hogy a ExpressRoute költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a ExpressRoute költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis Cost Management támogatja a legtöbb Azure-fiók típusát, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. 

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Helyi és standard és prémium

A ExpressRoute három különböző áramköri SKU-val rendelkezik: [*helyi*](./expressroute-faqs.md#expressroute-local), *standard* és [*prémium*](./expressroute-faqs.md#expressroute-premium). A ExpressRoute használatáért felszámított módszer a következő három SKU-típustól függ. A helyi SKU-val automatikusan korlátlan számú adatcsomaggal számolunk fel díjat. A standard és a prémium SKU esetében a mért vagy a korlátlan adatcsomag közül választhat. Az összes bejövő adatforgalom díjmentes, kivéve a Global Reach bővítmény használatakor. Fontos megérteni, hogy mely SKU-típusok és adatcsomagok működnek a legmegfelelőbben a számítási feladatokhoz a költségek és a költségvetés optimális optimalizálása érdekében.

## <a name="estimate-costs"></a>A költségek megbecslése

A ExpressRoute-kör létrehozása előtt használja az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator/) a költségek becsléséhez. 

1. A bal oldalon válassza a **hálózatkezelés**, majd az **Azure ExpressRoute** lehetőséget. 

1. Válassza ki a megfelelő *zónát* a társítási helytől függően. A legördülő lista megfelelő *zónájának* kiválasztásához tekintse meg a [ExpressRoute-kapcsolat szolgáltatóit](./expressroute-locations-providers.md#partners) . 

1. Ezután válassza ki az *SKU*-t, az *áramkör sebességét* és azt az *adatcsomagot* , amelyre vonatkozóan becslést szeretne készíteni. 

1. A *további kimenő* adatforgalomban adjon meg egy becslést GB-ban, amely a hónap folyamán felhasználható kimenő adatok mennyiségét adja meg. 

1. Végül hozzáadhatja az *Global REACH bővítményt* a becsléshez.

A következő képernyőkép a kalkulátor használatával mutatja be a költségbecslést:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="ExpressRoute az Azure kalkulátorban":::

További információ: az [Azure ExpressRoute díjszabása](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>ExpressRoute-átjáró becsült díja

Ha ExpressRoute-átjárót használ egy virtuális hálózat ExpressRoute-áramkörhöz való összekapcsolásához, az alábbi lépésekkel becsülheti meg az átjárók használatának költségeit.

1. A bal oldalon válassza a **hálózatkezelés** lehetőséget, majd a kezdéshez válassza a **VPN Gateway** lehetőséget. 

1. Válassza ki az átjáró *régióját* , majd módosítsa a *típust* a **ExpressRoute-átjárók** elemre.

1. Válassza ki az *átjáró típusát* a legördülő menüből.

1. Adja meg az *átjáró óráját*. (720 óra = 30 nap)

## <a name="understand-the-full-billing-model-for-expressroute"></a>A ExpressRoute teljes számlázási modelljének megismerése

A ExpressRoute olyan Azure-infrastruktúrán fut, amely az új erőforrás telepítésekor felmerülő költségekkel és a ExpressRoute együtt. Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Általában a ExpressRoute felmerülő költségek

ExpressRoute kör létrehozásakor dönthet úgy, hogy létrehoz egy ExpressRoute-átjárót a virtuális hálózat áramkörhöz kapcsolásához. Az átjárókat óradíjas díj és egy ExpressRoute-áramkör díja alapján számítjuk fel. Tekintse meg a [ExpressRoute díjszabását](https://azure.microsoft.com/en-us/pricing/details/expressroute) , és válassza a ExpressRoute-átjárók lehetőséget a különböző átjárók díjszabásának megtekintéséhez.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>A költségek az erőforrás törlése után merülhetnek fel

Ha a ExpressRoute áramkör törlése után rendelkezik ExpressRoute-átjáróval, a költséget a törlésig továbbra is felszámítjuk.

### <a name="using-azure-prepayment-credit"></a>Azure-előfizetési kredit használata

Az Azure-előfizetéssel (a korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel ExpressRoute díjat számítunk fel. Azonban nem használhatja az Azure-előfizetési kreditet, hogy díjat fizessen a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren.

## <a name="monitor-costs"></a>Költségek figyelése

Az Azure-erőforrások ExpressRoute való használatakor a költségek is felmerülnek. Az Azure Erőforrás-használati egység költségei időintervallumok (másodperc, perc, óra és nap), vagy egységenkénti használat (bájt, megabájt stb.) szerint változnak. Amint a ExpressRoute használata megkezdődik, a költségek felmerülnek, és a költségek [elemzése](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)során megtekintheti a költségeket.

A Cost Analysis használatakor a diagramokban és táblákban megjelenő ExpressRoute a különböző időintervallumokra vonatkozóan tekintheti meg. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetésekkel és az előre jelzett költségekkel is megtekintheti. A hosszabb nézetekre való áttérés a kiadások trendjeinek azonosításához nyújt segítséget. Itt láthatja, hogy hol történt a túltöltés. Ha létrehozta a költségvetést, azt is megteheti, hogy megtekintheti a túllépések helyét.

A ExpressRoute költségeinek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az Azure Portalra.

1. Lépjen az **előfizetések** elemre, válasszon ki egy előfizetést a listából, majd válassza a menü  **Cost Analysis** elemét. Válassza ki a **hatókört** , hogy másik hatókörre váltson a Cost Analysis szolgáltatásban. Alapértelmezés szerint a szolgáltatások díjszabása az első fánk-diagramon látható.

    A tényleges havi költségek akkor jelennek meg, amikor először nyitja meg a Cost Analysis-t. Az alábbi példa az összes havi használati költséget mutatja be.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Példa az előfizetés halmozott költségeire":::
    

1.  Egy szolgáltatás (például a Expressroute) szűk költségeihez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget. Ezután válassza a **ExpressRoute** lehetőséget.

    Íme egy példa, amely csak ExpressRoute költségeket mutat be.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Példa a ExpressRoute halmozott költségeinek megjelenítésére":::

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók (helyszínek) és az ExpressRoute költségei az erőforráscsoport szerint is megjelennek. Itt megtekintheti saját költségeit.

## <a name="create-budgets-and-alerts"></a>Költségvetések és riasztások létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről a költségvetés létrehozásakor, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="next-steps"></a>Következő lépések

- További információ a díjszabás működéséről az Azure ExpressRoute. Lásd: az [Azure ExpressRoute áttekintésének díjszabása](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.