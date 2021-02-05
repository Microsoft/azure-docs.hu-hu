---
title: Az Azure API Management költségeinek megtervezése és kezelése
description: Ismerje meg, hogyan tervezheti meg és kezelheti az Azure-API Management költségeit a Azure Portalban a Cost Analysis használatával.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 1ebb89ae318e57f1d4e0708a08019515ca43158d
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581329"
---
# <a name="plan-and-manage-costs-for-api-management"></a>A API Management költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure-API Management költségeit. Először is az Azure díjszabási kalkulátor segítségével tervezze meg a API Management költségeket, mielőtt felveszi a költségek kiszámításához szükséges erőforrásokat a szolgáltatáshoz. API Management erőforrások használatának elkezdése után a Cost Management funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. 

A API Management költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a API Management költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a külső szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis Cost Management támogatja a legtöbb Azure-fiók típusát, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>A költségek becslése a használata előtt API Management

Az [Azure díjszabási kalkulátorával](https://azure.microsoft.com/pricing/calculator/) a API Management hozzáadása előtt megbecsülheti a költségeket. 

1. Keressen *API Management*, vagy válassza az **integrációs**  >  **API Management** lehetőséget.
1. Válassza a **nézet** lehetőséget egy API Management szolgáltatási példány alapértelmezett költségbecslés hozzáadásához.

> [!NOTE]
> Az ebben a példában bemutatott költségek kizárólag demonstrációs célokat szolgálnak. A legfrissebb díjszabási információkért tekintse meg [API Management díjszabását](https://azure.microsoft.com/pricing/details/api-management/) .

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Fejlesztői szintű költségek becslése":::

* Az alapértelmezett költségbecslés egy API Management Service-példányon alapul a **fejlesztői** [szolgáltatási](api-management-features.md) szinten 1 [Kapacitási egységgel](api-management-capacity.md). A fejlesztői réteg nem üzemi célú használati esetekre és értékelésekre szolgál. A szolgáltatói szerződés nem támogatja.

* A további kapacitási egységek vagy egy másik szolgáltatási csomag költségeinek megbecsléséhez válassza az **egységek** és a **rétegek** legördülő lista egyéb lehetőségek elemét.

* A szolgáltatás rendelkezésre állási és szolgáltatási szintjétől függően további díjak is vonatkozhatnak a saját üzemeltetésű [átjárók](self-hosted-gateway-overview.md)használatára.

A díjszabással és a szolgáltatással kapcsolatos további részletekért lásd:

* [Díjszabás API Management](https://azure.microsoft.com/pricing/details/api-management/)
* [Az Azure API Management szintjeinek szolgáltatáson alapuló összehasonlítása](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Pénzügyi kreditek használata API Management

Az Azure-előfizetéssel (korábbi nevén pénzügyi kötelezettségvállalással) járó API Management díjakat fizethet. Azonban nem használhatja az Azure-előfizetési kreditet, hogy díjat fizessen a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren.

## <a name="monitor-costs"></a>Költségek figyelése

Az Azure-erőforrások API Management használatával történő használatakor a költségek is felmerülnek. Az Azure Erőforrás-használati egység költségei időintervallumok (másodperc, perc, óra és nap) vagy egységenkénti használat szerint változnak (bájt, megabájt stb.). Amint API Management a használat megkezdésekor, a költségek felmerülnek, és megtekintheti a költségek [elemzését](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

A Cost Analysis használatakor a különböző időintervallumokhoz tartozó grafikonok és táblák API Management költségeit tekintheti meg. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetésekkel és az előre jelzett költségekkel is megtekintheti. A hosszabb nézetekre való áttérés a kiadások trendjeinek azonosításához nyújt segítséget. Itt láthatja, hogy hol történt a túltöltés. Ha létrehozta a költségvetést, azt is megteheti, hogy megtekintheti a túllépések helyét.

> [!NOTE]
> Az ebben a példában bemutatott költségek kizárólag demonstrációs célokat szolgálnak. A költségek az erőforrás-használattól és a jelenlegi díjszabástól függően változhatnak.

API Management költségek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az [Azure Portalra](https://azure.microsoft.com).
1. Nyissa meg a **Cost Management + számlázás** ablakot, válassza ki a menüből a **Cost Management** elemet, majd válasszon ki egy **Számlázási hatókört**. Válasszon ki például egy előfizetést a listából.
1. Válassza a menü **Cost Management** elemét, majd válassza a **Cost Analysis** elemet.
1. Alapértelmezés szerint az összes szolgáltatás havi költségei az első fánk-diagramon jelennek meg. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Az előfizetés havi díja":::

1. Egy szolgáltatás (például API Management) szűk költségeinek kiválasztásához válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget. Ezután válassza a **API Management** lehetőséget.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Példa a API Management összesített költségeire":::

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók (helyszínek) és a API Management az erőforráscsoport költségei is megjelennek. Itt megtekintheti saját költségeit.

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről, amikor költségvetést hoz létre, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha további adatelemzésre van szüksége a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Egyéb módszerek a API Management költségeinek kezeléséhez és csökkentéséhez

### <a name="choose-tier"></a>Válasszon szintet

Tekintse át az [Azure API Management-rétegek szolgáltatás-alapú összehasonlítását](api-management-features.md) , hogy eldöntse, melyik szolgáltatási réteg lehet megfelelő a forgatókönyvekhez. A különböző szolgáltatási rétegek különböző használati esetekhez, különböző költségekkel kialakított funkciók és képességek kombinációit támogatják. 

* A **fogyasztási** szolgáltatási csomag egy egyszerű, kiszolgáló nélküli lehetőséget biztosít, amely nem tartalmaz rögzített költséget. A számlázás az adott küszöbérték feletti szolgáltatáshoz tartozó API-hívások száma alapján történik. A kapacitás a szolgáltatás terhelése alapján automatikusan is méretezhető.
* A **fejlesztői**, **alapszintű**, **standard** és **prémium** szintű API Management csomagok havi költségekkel járnak, és nagyobb átviteli sebességet és gazdagabb szolgáltatásokat biztosítanak a kiértékeléshez és a termelési feladatokhoz. Bármikor [frissíthet](upgrade-and-scale.md) egy másik szolgáltatási szintre.

### <a name="scale-using-capacity-units"></a>Méretezés a kapacitási egységek használatával

A fogyasztási szolgáltatási szinten kívül a API Management a [*Kapacitási egységek*](api-management-capacity.md)hozzáadásával vagy eltávolításával támogatja a skálázást. Mivel a terhelés nő egy API Management-példányon, a kapacitási egységek hozzáadása gazdaságosabb lehet, mint a magasabb szolgáltatási szintre való frissítés. Az egységek maximális száma a szolgáltatási szinttől függ.

Minden egyes kapacitási egység egy bizonyos kérelmek feldolgozási képességgel rendelkezik, amely a szolgáltatás szintjétől függ. Az alapszintű csomag egy egysége például körülbelül 1 000 kérelem másodpercenkénti becsült maximális átviteli sebességgel rendelkezik. 

Az egységek hozzáadásakor vagy eltávolításakor a kapacitás és a költséghatékonyság arányos. A standard szintű csomag két egysége például körülbelül 2 000 kérelem másodpercenkénti becsült átviteli sebességét biztosítja. A tényleges átviteli sebesség a kérések és válaszok mérete, a kapcsolatok mintája, a kérelmeket kérő ügyfelek száma és egyéb tényezők miatt eltérő lehet.

A API Management-példány kapacitási metrikájának [figyelésével](api-management-howto-use-azure-monitor.md) megadhatja, hogy egy API Management példány méretezése vagy frissítése nagyobb terhelést biztosítson.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.
- A API Management [kapacitásának](api-management-capacity.md)megismerése.
- Tekintse át a API Management méretezésének és frissítésének lépéseit a [Azure Portal](upgrade-and-scale.md)használatával, és ismerkedjen meg az automatikus [skálázással](api-management-howto-autoscale.md).
