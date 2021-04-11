---
title: Az Azure Cosmos DB költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezheti meg és kezelheti Azure Cosmos DB költségeit a Cost Analysis használatával Azure Portalban.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/05/2021
ms.openlocfilehash: 98e849791acd71ea8bf3ac9cb1949da9f562e749
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490828"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Az Azure Cosmos DB költségeinek megtervezése és kezelése
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Ez a cikk a Azure Cosmos DB költségeinek megtervezését és kezelését ismerteti. Először a Azure Cosmos DB kapacitás-kalkulátor használatával becsülje meg a számítási feladatok költségeit, mielőtt erőforrásokat hozna létre. Később megtekintheti a becsült költségeket, és megkezdheti az erőforrások létrehozását.

Azure Cosmos DB erőforrások használatának elkezdése után a Cost Management funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. A Azure Cosmos DB költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a Azure Cosmos DB költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a külső szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

### <a name="provisioned-throughput-or-serverless"></a>Kiosztott átviteli sebesség vagy kiszolgáló nélküli

A Azure Cosmos DB kétféle kapacitási módot támogat: [kiépített átviteli sebességet](set-throughput.md) és [kiszolgáló](serverless.md)nélküli teljesítményt. A Azure Cosmos DB használatának felszámított módja a két mód között sokat változik, ezért fontos kiválasztani a számítási feladathoz legjobban megfelelőt. További útmutatásért és javaslatokért tekintse meg a [kiosztott átviteli sebesség és a kiszolgáló](throughput-serverless.md) nélküli cikk közötti választást ismertető cikket.

### <a name="cost-analysis"></a>Költségelemzés

A Cost Analysis Cost Management támogatja a legtöbb Azure-fiók típusát, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-cosmos-db"></a>A költségek becslése a használata előtt Azure Cosmos DB

Azure Cosmos DB két különböző kapacitású üzemmódban érhető el: kiépített átviteli sebesség és kiszolgáló nélküli. Mindkét módban ugyanazokat az adatbázis-műveleteket hajthatja végre, de a műveletek számlázásának módja eltér.

### <a name="estimate-provisioned-throughput-costs"></a>Kiosztott átviteli sebesség becslése

Ha Azure Cosmos DB kiépített átviteli sebességű módban szeretné használni, akkor az Azure Cosmos-fiók erőforrásainak létrehozása előtt a [Azure Cosmos db kapacitás-kalkulátor](https://cosmos.azure.com/capacitycalculator/) használatával becsülje meg a költségeket. A kapacitás-kalkulátor segítségével megbecsülheti a számítási feladatok szükséges átviteli sebességét és költségeit. Az Azure Cosmos-adatbázisok és-tárolók konfigurálása a számítási feladatokhoz megfelelő mennyiségű kiépített átviteli sebességgel vagy a [kérelmek egységével (ru/s)](request-units.md)a számítási feladatokhoz elengedhetetlen, hogy optimalizálja a költségeket és a teljesítményt. Olyan részleteket kell beírnia, mint például az API-típus, a régiók száma, az elem mérete, az olvasási/írási kérelmek másodpercenkénti száma, a teljes tárolt adat a költségbecslés érdekében. Ha többet szeretne megtudni a kapacitás-kalkulátorról, tekintse meg a [becslést](estimate-ru-with-capacity-planner.md) ismertető cikket.

Az alábbi képernyőfelvételen az átviteli sebesség és a költségbecslés látható a kapacitás-kalkulátor használatával:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés a Azure Cosmos DB Capacity kalkulátorban":::

### <a name="estimate-serverless-costs"></a><a id="estimating-serverless-costs"></a> Kiszolgáló nélküli költségek becslése

Ha kiszolgáló nélküli módban tervezi használni a Azure Cosmos DBt, meg kell becsülnie, hogy hány [kérelem-egység](request-units.md) és GB tárterületet használ fel havi rendszerességgel. A kért egységek mennyiségét a havonta kiállított adatbázis-műveletek számának kiértékelésével becsülheti meg, és a megfelelő RU-költségeket megszorozva. A következő táblázat az általános adatbázis-műveletek becsült költségét sorolja fel:

| Művelet | Becsült költségek | Jegyzetek |
| --- | --- | --- |
| Elem létrehozása | 5 RUs | 5 KB-nál kevesebb tulajdonsággal rendelkező 1 KB-os elemek átlagos díja |
| Elem módosítása | 10 RU | 5 KB-nál kevesebb tulajdonsággal rendelkező 1 KB-os elemek átlagos díja |
| Egyedi elemek olvasása az azonosító és a partíciós kulcs (pont – olvasás) alapján | 1 RU | 1 KB-os elemek átlagos díja |
| Elem törlése | 5 RUs | |
| Lekérdezés végrehajtása | 10 RU | Egy olyan lekérdezés átlagos díja, amely teljes mértékben kihasználja az [indexelést](index-overview.md) , és visszaadja a 100 vagy kevesebb eredményt |

> [!IMPORTANT] 
> Figyeljen a fenti táblázat megjegyzéseit. A műveletek tényleges költségeinek pontosabb becsléséhez használhatja a [Azure Cosmos db emulátort](local-emulator.md) , és megbecsülheti a [műveleteinek pontos ru költségét](find-request-unit-charge.md). Bár a Azure Cosmos DB-emulátor nem támogatja a kiszolgáló nélküli használatát, az adatbázis-műveletekhez standard RU-díjat jelent, és használható a becsléshez.

Miután kiszámította a kérelmek teljes számát és a GB-nyi tárterületet, amelyet valószínűleg egy hónap alatt felhasznál, a következő képlet visszaadja a költségbecslést: **([a kérelmek száma]/1 000 000 * $0,25) + ([GB tárterület] * $0,25)**.

> [!NOTE]
> Az előző példában bemutatott költségek csak demonstrációs célokat szolgálnak. A legfrissebb díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="understand-the-full-billing-model"></a>A teljes számlázási modell ismertetése

Azure Cosmos DB olyan Azure-infrastruktúrán fut, amely új erőforrások telepítésekor költségekkel jár. Fontos tisztában lenni azzal, hogy lehetséges, hogy további infrastrukturális költségek merülhetnek fel.

### <a name="how-youre-charged-for-azure-cosmos-db"></a>A Azure Cosmos DB díja

Azure Cosmos DB erőforrások létrehozásakor vagy használatakor a következő mérőszámokra lehet fizetni:

* **Adatbázis-műveletek** – a kiosztott vagy felhasznált kérési egységek (ru/s) alapján kell fizetnie:
  * Standard (manuális) kiépített átviteli sebesség – a tárolón vagy adatbázison kiépített RU/s óradíjat számoljuk fel.
  * Kiépített átviteli sebesség – a rendszer a maximálisan engedélyezett RU/mp-k száma alapján számlázza.

* **Felhasznált tárterület** – az adatai és az indexek egy adott órán belül felhasznált teljes tárolási mennyiségét (GB-ban) számítjuk fel.

A Azure Cosmos DB szolgáltatások, például a biztonsági mentési tár, az analitikai tárolás, a rendelkezésre állási zónák és a többrégiós írások használata esetén további díjat számítunk fel. A számlázási ciklus végén az egyes mérőszámok díjait összegzi a rendszer. A számla vagy számla az összes Azure Cosmos DB költség szakaszát mutatja. Az egyes fogyasztásmérők külön sorban szerepelnek. További információt a [díjszabási modellről](how-pricing-works.md) szóló cikkben talál.

### <a name="using-azure-prepayment"></a>Azure-előfizetések használata

Az Azure-előfizetéssel ellátott kreditek esetén Azure Cosmos DB díjat is fizethet. Az Azure-beli előfizetési kreditek azonban nem használhatók fel a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren fizetendő díjakért.

## <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

A Azure Portal Azure Cosmos DB erőforrásainak használatának megkezdése után megtekintheti a becsült költségeket. A következő lépésekkel tekintheti át a költségbecslést:

1. Jelentkezzen be a Azure Portalba, és navigáljon az Azure Cosmos-fiókjához.
1. Ugrás az **Áttekintés** szakaszra.
1. A **Cost** diagram alján keresse meg a következőt:. Ez a diagram a jelenlegi költségeket mutatja be egy konfigurálható időszakra vonatkozóan:
1. Hozzon létre egy új tárolót, például egy Graph-tárolót.
1. Adja meg a számítási feladatokhoz szükséges átviteli sebességet, például 400 RU/s. Az átviteli sebesség értékének megadása után a díjszabási becslést a következő képernyőképen látható módon jelenítheti meg:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Költségbecslés Azure Portal":::

Ha az Azure-előfizetése költségkerettel rendelkezik, az Azure megakadályozza a kreditek összegének megadását. Az Azure-erőforrások létrehozásakor és használatakor a rendszer felhasználja a krediteket. Ha eléri a hitelkeretét, az üzembe helyezett erőforrások a számlázási időszak további részében le lesznek tiltva. A hitelkeret nem módosítható, de eltávolíthatja. További információ a költségkeretekről: az [Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)költségkerete.

Az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel Azure Cosmos DB díjat fizethet. Az Azure-előfizetési kreditek azonban nem használhatók fel a harmadik féltől származó termékekkel és szolgáltatásokkal, például az Azure Marketplace-en keresztül fizetendő díjakért.

## <a name="monitor-costs"></a>Költségek figyelése

A Azure Cosmos DBekkel rendelkező erőforrások használatakor költségek merülnek fel. Az erőforrás-használati egység költségei az időintervallumok (másodperc, perc, óra és nap) vagy a kérési egység használata szerint változnak. Amint a Azure Cosmos DB használata megkezdődik, a költségek felmerülnek, és a Azure Portal [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) ablaktáblájában láthatja őket.

A Cost Analysis használatakor különböző időintervallumok esetén megtekintheti a Azure Cosmos DBi költségeket gráfokban és táblázatokban. Néhány példa: nap, aktuális, előző hónap és év. A költségeket a költségvetések és az előre jelzett költségek között is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, azt is megteheti, hogy a megadottak hol vannak túllépve.

Azure Cosmos DB költségek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg a hatókört a Azure Portalban, és válassza a menü **Cost Analysis** elemét. Például lépjen az **előfizetések** elemre, válasszon ki egy előfizetést a listából, majd válassza a menü  **Cost Analysis** elemét. Válassza ki a **hatókört** , hogy másik hatókörre váltson a Cost Analysis szolgáltatásban.

1. Alapértelmezés szerint az összes szolgáltatás díja megjelenik az első fánk-diagramon. Válassza ki a diagramot a "Azure Cosmos DB" címkével.

1. Egy szolgáltatás, például a Azure Cosmos DB költségeinek szűkítéséhez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget. Ezután válassza ki a **Azure Cosmos db** elemet a listából. Íme egy példa, amely csak a Azure Cosmos DBra vonatkozó költségeket mutatja:

   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Költségek figyelése a Cost Analysis ablaktáblával":::

Az előző példában a február hónapra vonatkozóan a Azure Cosmos DB aktuális díja látható. A diagramok a hely és az erőforráscsoport szerint Azure Cosmos DB költségeket is tartalmazzák.

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről, amikor költségvetést hoz létre, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="other-ways-to-manage-and-reduce-costs"></a>A költségek kezelésének és csökkentésének egyéb módjai

A következő ajánlott eljárások a költségek csökkentéséhez használhatók:

* A [kiépített átviteli sebesség optimalizálása](optimize-cost-throughput.md) – ez a cikk részletesen ismerteti az átviteli sebesség optimalizálásának ajánlott eljárásait. Leírja, hogy mikor kell kiépíteni az átviteli sebességet a tároló szintjén és az adatbázis szintjén a munkaterhelés típusa alapján.

* A [kérések optimalizálásának optimalizálása](optimize-cost-reads-writes.md) – ez a cikk azt ismerteti, hogy az olvasási és írási kérelmek milyen módon fordíthatók le a kérési egységekre, és hogyan optimalizálható a kérelmek díja.

* A [tárolási költségeket](optimize-cost-storage.md) tároló díjszabásának optimalizálása a használat alapján történik. Megtudhatja, hogyan optimalizálhatja a tárolási költségeket az elemek méretével, az indexelési szabályzattal, ha olyan szolgáltatásokat használ, mint például a hírcsatorna és az idő megváltozása.

* [Többrégiós díj optimalizálása](optimize-cost-regions.md) – ha egy vagy több használaton kívüli olvasási régióval rendelkezik, hajtsa végre a lépéseket, hogy az olvasási régiókban lévő, a hírcsatornát a beolvasott régión belüli maximális kihasználtságot használja, vagy helyezze át egy másik másodlagosra, ha túlzott kihasználtságú.

* A [fejlesztési és tesztelési költségeket optimalizálhatja](optimize-dev-test.md) – megtudhatja, hogyan optimalizálhatja a fejlesztési költségeket a helyi emulátor, a Azure Cosmos db ingyenes réteg, az ingyenes Azure-fiók és néhány egyéb lehetőség használatával.

* A [költségek optimalizálása fenntartott kapacitással](cosmos-db-reserved-capacity.md) – megtudhatja, hogyan használhatja a fenntartott kapacitást úgy, hogy pénzt takarítson meg, ha egy vagy három évre Azure Cosmos db erőforrásokra vonatkozó foglalást tesz elérhetővé.

## <a name="next-steps"></a>Következő lépések

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket Azure Cosmos DB:

* [Díjszabási modell az Azure Cosmos DB-ben](how-pricing-works.md)
* Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
* További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.