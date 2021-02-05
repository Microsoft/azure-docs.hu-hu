---
title: A App Service költségeinek kezelésének megtervezése
description: Megtudhatja, hogyan tervezheti meg és kezelheti Azure App Service költségeit a Azure Portal a Cost Analysis használatával.
ms.custom: subject-cost-optimization
ms.service: app-service
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 3df08705859678525526f8fef198826f58249d8b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573364"
---
# <a name="plan-and-manage-costs-for-azure-app-service"></a>A Azure App Service költségeinek megtervezése és kezelése

<!-- Check out the following published examples:
- [https://docs.microsoft.com/azure/cosmos-db/plan-manage-costs](../cosmos-db/plan-manage-costs.md)
- [https://docs.microsoft.com/azure/storage/common/storage-plan-manage-costs](../storage/common/storage-plan-manage-costs.md)
- [https://docs.microsoft.com/azure/machine-learning/concept-plan-manage-cost](../machine-learning/concept-plan-manage-cost.md)
-->

<!-- Note for Azure service writer: Links to Cost Management articles are full URLS with the ?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn campaign suffix. Leave those URLs intact. They're used to measure traffic to Cost Management articles.
-->

<!-- Note for Azure service writer: Modify the following for your service. -->

Ez a cikk a Azure App Service költségeinek tervezését és kezelését ismerteti. Először is az Azure díjszabási kalkulátor segítségével tervezze meg a App Service költségeket, mielőtt felveszi a költségek kiszámításához szükséges erőforrásokat a szolgáltatáshoz. Ezután az Azure-erőforrások hozzáadásakor tekintse át a becsült költségeket. App Service erőforrások használatának elkezdése után a [Cost Management](../cost-management-billing/index.yml?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. A Azure App Service költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a App Service költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a külső szolgáltatásokat is.

## <a name="relevant-costs-for-app-service"></a>A App Service vonatkozó költségek

App Service az Azure-infrastruktúrán fut, amely a költségeket felhalmozza. Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie.

### <a name="costs-that-accrue-with-azure-app-service"></a>A Azure App Service felmerülő költségek

Attól függően, hogy milyen szolgáltatást használ a App Serviceban, a következő felmerülő költségeket lehet létrehozni:

- **App Service terv**  App Service alkalmazás üzemeltetéséhez szükséges.
- **Elkülönített rétegek**  Egy App Service-környezethez [Virtual Network](../virtual-network/index.yml) szükséges.
- **Biztonsági mentés**  A biztonsági másolatok készítéséhez szükség van egy [Storage-fiókra](../storage/index.yml) .
- **Diagnosztikai naplók**  Naplózási lehetőségként kiválaszthatja a [Storage-fiókot](../storage/index.yml) , vagy integrálhatja az [Azure log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md)-t.
- **Tanúsítványok app Service**  Az Azure-ban vásárolt tanúsítványokat [Azure Key Vault](../key-vault/index.yml)kell fenntartani.

A App Service egyéb Cost-erőforrásai (a részletekért lásd a [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/) ):

- [Tartományok app Service](manage-custom-dns-buy-domain.md)  Az előfizetést évente kell kifizetni a tartomány regisztrálásához, ha engedélyezi az automatikus megújítást.
- [Tanúsítványok app Service](configure-ssl-certificate.md#import-an-app-service-certificate)  Egyszeri díj a vásárlás időpontjában. Ha több altartománnyal rendelkezik a biztonság biztosításához, akkor csökkentheti a költségeket, ha több standard tanúsítvány helyett egy helyettesítő karaktert vásárol.
- [IP-alapú tanúsítvány kötései](configure-ssl-bindings.md#create-binding)  A kötés az alkalmazás szintjén konfigurált tanúsítványon van konfigurálva. A költségek az egyes kötések esetében esedékesek. A **standard** és újabb verziók esetében az első IP-alapú kötés nem számít fel díjat.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Az erőforrás törlése után felmerülhető költségek

Ha egy App Service-csomagban lévő összes alkalmazást törli, a terv a konfigurált díjszabási szintjétől és a példányok számától függően továbbra is felmerül. A nemkívánatos költségek elkerülése érdekében törölje a csomagot, vagy méretezze át az **ingyenes** csomagra.

Azure App Service erőforrások törlése után a kapcsolódó Azure-szolgáltatásokból származó erőforrások továbbra is létezhetnek. Amíg nem törli őket, továbbra is felmerülnek a költségek. Például:

- Az **elkülönített** csomaghoz app Service tervhez létrehozott Virtual Network
- Biztonsági másolatok vagy diagnosztikai naplók tárolására létrehozott Storage-fiókok
- App Service tanúsítványok tárolására létrehozott Key Vault
- A diagnosztikai naplók elszállítására létrehozott naplózási elemzési névterek
- Olyan App Service [példány-vagy Stamp-foglalások](#azure-reservations) , amelyek még nem jártak le

### <a name="using-monetary-credit-with-azure-app-service"></a>Pénzügyi kreditek használata Azure App Service

Az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel Azure App Service díjat fizethet. Az Azure-előfizetési kreditek azonban nem használhatók a harmadik féltől származó termékek és szolgáltatások díjainak kifizetésére, beleértve az Azure piactéren lévőket is.

## <a name="estimate-costs"></a>A költségek megbecslése

Az [Azure díjszabási kalkulátorának](https://azure.microsoft.com/pricing/calculator/)használatával egyszerűen megbecsülheti és optimalizálhatja a app Service költségét.

A díjszabási számológép használatához kattintson **app Service** a **Products (termékek** ) lapon. Ezután görgessen le a számológéptel való együttműködéshez. A következő képernyőkép egy példát mutat be, és nem tükrözi a jelenlegi díjszabást.

![Példa az Azure díjszabási számológépének becsült költségére](media/overview-manage-costs/pricing-calculator.png)

### <a name="review-estimated-costs-in-the-azure-portal"></a>A becsült költségek áttekintése az Azure Portalon

App Service-alkalmazás vagy egy App Service-csomag létrehozásakor a becsült költségek láthatók.

Hozzon létre egy alkalmazást, és tekintse meg a becsült árat:

1. A létrehozás lapon görgessen le **app Service tervre**, majd kattintson az **új létrehozása** elemre.
1. Adjon meg egy nevet, majd kattintson **az OK gombra**.
1. A **SKU és a size** elem mellett kattintson a **méret módosítása** gombra.
1. Tekintse át az összegzésben látható becsült árat. A következő képernyőkép egy példát mutat be, és nem tükrözi a jelenlegi díjszabást.

    ![Az egyes díjszabási szintek becsült költségének áttekintése a portálon](media/overview-manage-costs/pricing-estimates.png)

Ha az Azure-előfizetése költségkerettel rendelkezik, az Azure megakadályozza a kreditek összegének megadását. Az Azure-erőforrások létrehozásakor és használatakor a rendszer felhasználja a krediteket. Ha eléri a hitelkeretét, az üzembe helyezett erőforrások a számlázási időszak további részében le lesznek tiltva. A hitelkeret nem módosítható, de eltávolíthatja. További információ a költségkeretekről: az [Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)költségkerete.

## <a name="optimize-costs"></a>Költségek optimalizálása

Alapszintű szinten App Service alkalmazásokat az azokat üzemeltető App Service-csomag számítja fel. A App Service üzembe helyezésével kapcsolatos költségek néhány fő tényezőtől függenek:

- **Díjszabási**  csomag  Más néven a App Service terv SKU-jának. A magasabb szintű processzorok nagyobb CPU-mag, memória, tárterület vagy szolgáltatások, vagy ezek kombinációi.
- A **példányok számának** dedikált szintjei (alapszintű vagy magasabb) méretezhetők, és mindegyik kibővíthető példány felmerül.
- **Bélyegző díja**  Az elkülönített szinten a App Service-környezetből átalány-díjat számítunk fel, függetlenül attól, hogy hány alkalmazást vagy feldolgozói példányt futtatnak.

Egy App Service-csomag több alkalmazást is képes tárolni. Az üzembe helyezéstől függően több alkalmazást is megtakaríthat egy App Service csomagon (például az alkalmazások üzemeltetése kevesebb App Service-csomagon).

Részletekért lásd: [app Service terv áttekintése](overview-hosting-plans.md)

### <a name="non-production-workloads"></a>Nem éles számítási feladatok

A App Service vagy a megoldás teszteléséhez, miközben alacsony vagy minimális költséget ér el, megkezdheti a két, a megosztott példányokon üzemeltetett, **szabadon** és **közösen** használt belépési szintű árképzési szint használatát. Ha a jobb teljesítményű dedikált példányokon szeretné tesztelni az alkalmazást, az **alapszintű** csomagra frissíthet, amely támogatja a Windows-és Linux-alkalmazásokat is. 

> [!NOTE]
> Az **Azure dev/test díjszabása**  Ha olyan üzem előtti számítási feladatokat szeretne tesztelni, amelyek magasabb szintű szintet igényelnek (az **elkülönítettek** kivételével), a Visual Studio-előfizetők is igénybe vehetik az [Azure dev/test díjszabását](https://azure.microsoft.com/pricing/dev-test/), amely jelentős kedvezményeket kínál.
>
> Az **ingyenes** és a **közös** szint, valamint az Azure dev/test díjszabási kedvezményei nem vállalnak pénzügyi FELELŐSSÉGgel vállalt SLA-t.

### <a name="production-workloads"></a>Éles számítási feladatok

Az éles számítási feladatokhoz a dedikált **standard** díjszabási szint vagy újabb javaslat vonatkozik. Míg a magasabb szintű csomagok ára növekszik, több memóriát és tárterületet és nagyobb teljesítményű hardvert is biztosít, így a számítási példányok esetében nagyobb az alkalmazások sűrűsége. Ez az adott számú alkalmazásnál alacsonyabb példányszámot fordít, ezért alacsonyabb költségeket jelent. Valójában a **prémium v3** (a legmagasabb, nem **elszigetelt** szint) a leghatékonyabb módszer az alkalmazás nagy léptékű kiszolgálására. A megtakarításokhoz való hozzáadáshoz részletes kedvezményeket kaphat a [Premium v3 foglalásokkal](#azure-reservations)kapcsolatban.

> [!NOTE]
> A **Premium v3** a Windows-tárolókat és a Linux-tárolókat is támogatja. 

Miután kiválasztotta a kívánt díjszabási szintet, csökkentse az üresjárati példányokat. A kibővíthető üzembe helyezés során pénzt pazarolhat a kihasználatlan számítási példányokra. Konfigurálnia kell az automatikus [skálázást](../azure-monitor/platform/autoscale-get-started.md), amely a **standard** szintű és a fentiekben is elérhető. A kibővíthető ütemtervek, valamint a metrikus kibővített szabályok létrehozásával csak azokat a példányokat kell fizetnie, amelyekhez adott időpontban szüksége van.

### <a name="azure-reservations"></a>Azure Reservations

Ha azt tervezi, hogy egy vagy több évre vonatkozóan ismert minimális számú számítási példányt használ, érdemes kihasználni a **prémium v3** -as szintet, és a példányok díjszabását drasztikusan kell kiszolgálni a példányok 1 vagy 3 éves növekményekben történő visszafoglalásával. A havi költségmegtakarítás akár 55%-ot is eredményezhet. A foglalások két típusa lehetséges:

- **Windows (vagy platform agnosztikus)**  Az előfizetésében Windows-vagy Linux-példányokra is alkalmazható.
- **Linux-specifikus**  Csak az előfizetésben található Linux-példányokra vonatkozik.

A fenntartott példány díjszabása az előfizetés megfelelő példányaira vonatkozik, a fenntartott példányok számáig. A fenntartott példányok számlázási anyagok, és nem meghatározott számítási példányokhoz vannak kötve. Ha kevesebb példányt futtat, mint amennyit a foglalási időszakban bármikor lefoglal, a fenntartott példányok továbbra is fizetnie kell. Ha a foglalási időszak során bármely időpontban fenntartott több példányt futtat, akkor a további példányok esetében a szokásosan esedékes költségeket kell megfizetnie.

Az **elkülönített** csomag (App Service környezet) a kedvezményes díjszabás szerint 1 és 3 éves foglalást is támogat. További információ: [a foglalási kedvezmények a Azure app Servicera vonatkoznak](../cost-management-billing/reservations/reservation-discount-app-service.md).

## <a name="monitor-costs"></a>Költségek figyelése

Az Azure-erőforrások App Service használatával történő használatakor a költségek is felmerülnek. Az Azure Erőforrás-használati egység költségei időintervallumok szerint változnak (másodperc, perc, óra és nap). Amint App Service a használat megkezdésekor, a költségek felmerülnek, és megtekintheti a költségek [elemzését](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

A Cost Analysis használatakor a különböző időintervallumokhoz tartozó grafikonok és táblák App Service költségeit tekintheti meg. Néhány példa: nap, aktuális és előző hónap, év. A költségeket a költségvetésekkel és az előre jelzett költségekkel is megtekintheti. A hosszabb nézetekre való áttérés a kiadások trendjeinek azonosításához nyújt segítséget. Itt láthatja, hogy hol történt a túltöltés. Ha létrehozta a költségvetést, azt is megteheti, hogy megtekintheti a túllépések helyét.
    
App Service költségek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az Azure portálra.
2. Nyissa meg a hatókört a Azure Portalban, és válassza a menü **Cost Analysis** elemét. Például lépjen az **előfizetések** elemre, válasszon ki egy előfizetést a listából, majd válassza a menü  **Cost Analysis** elemét. Válassza ki a **hatókört** , hogy másik hatókörre váltson a Cost Analysis szolgáltatásban.
3. Alapértelmezés szerint a szolgáltatások díjszabása az első fánk-diagramon látható. Válassza ki a diagram App Service feliratú részét.

A tényleges havi költségek akkor jelennek meg, amikor először nyitja meg a Cost Analysis-t. Az alábbi példa az összes havi használati költséget mutatja be.

![Példa az előfizetés halmozott költségeire](media/overview-manage-costs/all-costs.png)

Egy szolgáltatás (például a App Service) szűk költségeihez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév** lehetőséget. Ezután válassza a **app Service** lehetőséget.

Íme egy példa, amely csak a App Service költségeire mutat.

![Példa a szolgáltatásnév halmozott költségeinek megjelenítésére](media/overview-manage-costs/service-specific-costs.png)

Az előző példában a szolgáltatás aktuális díja látható. Az Azure-régiók (helyszínek) és a App Service az erőforráscsoport költségei is megjelennek. Itt megtekintheti saját költségeit.

## <a name="create-budgets"></a>Költségvetések létrehozása

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy ne hozzon létre véletlenül új erőforrásokat, ami extra pénzbe kerül. A költségvetés létrehozásakor elérhető szűrési lehetőségekkel kapcsolatos további információkért lásd: [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="next-steps"></a>Következő lépések

- További információ arról, hogyan működik az árképzés az Azure Storage-ban. Lásd: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).
- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.

<!-- Insert links to other articles that might help users save and manage costs for you service here.

Create a table of contents entry for the article in the How-to guides section where appropriate. -->