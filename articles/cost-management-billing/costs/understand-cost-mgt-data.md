---
title: Az Azure Cost Management adatainak ismertetése
description: Ez a cikk segít az Azure Cost Managementben található adatok, valamint azok feldolgozási, gyűjtési, megjelenítési és lezárási gyakoriságának jobb megértésében.
author: bandersmsft
ms.author: banders
ms.date: 01/17/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: ad099fc7dfcee168186ef5229785933f4b1c5a90
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650881"
---
# <a name="understand-cost-management-data"></a>A Cost Management adatainak értelmezése

Ez a cikk segít az Azure az Azure Cost Managementben található költség- és használati adatainak jobb megértésében. Ismerteti az adatok feldolgozásának, gyűjtésének, megjelenítésének és lezárásának gyakoriságát. Az Azure-használat számlázása havonta történik. Bár a számlázási ciklusok havi időszakok, a ciklusok kezdő- és záródátuma előfizetési típusonként eltér. Az, hogy a Cost Management milyen gyakran kap használati adatokat, különböző tényezőktől függ. Az ilyen tényezők közé tartozik például az, hogy mennyi ideig tart az adatok feldolgozása, illetve hogy az Azure-szolgáltatások milyen gyakran szolgáltatnak használati adatokat a számlázási rendszernek.

A Cost Management magában foglalja az összes használatot és vásárlást, beleértve a Nagyvállalati Szerződéses (EA-) fiókokhoz tartozó foglalásokat és külső ajánlatokat. A Microsoft Ügyfélszerződéshez tartozó fiókok és használatalapú díjszabású egyéni előfizetések csak az Azure- és Marketplace-szolgáltatások használatát tartalmazzák. A támogatási és egyéb költségeket nem tartalmazza. A számla létrehozásáig a költségek csak becsült összegek, és nem veszik figyelembe a jóváírásokat.

Új előfizetés esetén nem használhatja azonnal a Cost Management szolgáltatásait. Akár 48 órára is szükség lehet, hogy a Cost Management összes szolgáltatását használhassa.

## <a name="supported-microsoft-azure-offers"></a>Támogatott Microsoft Azure-ajánlatok

Az alábbi információk azokat a [Microsoft Azure-ajánlatokat](https://azure.microsoft.com/support/legal/offer-details/) jelenítik meg, amelyek az Azure Cost Managementben jelenleg támogatottak. Az Azure-ajánlat az Ön Azure-előfizetésének típusa. Az adatok az **Adatok elérhetőségének kezdete** dátumtól kezdődően érhetők el a Cost Managementben. Ha egy előfizetés másik ajánlatra vált, akkor a másik ajánlatra való váltás dátuma előtti költségek nem érhetők el.

| **Kategória**  | **Ajánlat neve** | **Kvótaazonosító** | **Ajánlatszám** | **Adatok elérhetőségének kezdete** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014. május<sup>1</sup> |
| **Azure Government** | Azure Government – használatalapú fizetés | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P | 2018. október 2.<sup>2</sup> |
| **Nagyvállalati Szerződés (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014. május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014. május<sup>1</sup> |
| **Microsoft-ügyfélszerződés** | Microsoft Azure-csomag | EnterpriseAgreement_2014-09-01 | N/A | 2019. március<sup>3</sup> |
| **Microsoft-ügyfélszerződés** | Microsoft Azure-csomag fejlesztéshez/teszteléshez | MSDNDevTest_2014-09-01 | N/A | 2019. március<sup>3</sup> |
| **Partnerek által támogatott Microsoft Ügyfélszerződés** | Microsoft Azure-csomag | CSP_2015-05-01, CSP_MG_2017-12-01 és CSPDEVTEST_2018-05-01<br><br>A rendszer a Microsoft Ügyfélszerződéses és az örökölt CSP-előfizetések esetében újrahasználja a kvótaazonosítót. Jelenleg csak a Microsoft Ügyfélszerződéses előfizetések támogatottak. | N/A | 2019. október |
| **Microsoft Developer Network (MSDN)** | MSDN-platformok<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Utólagos, használatalapú fizetés                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Pay-As-You-Go Dev/Test         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Ingyenes próba<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Azure in Open licencprogram<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018. október 2.<sup>2</sup> |
| **Használatalapú fizetés** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018. október 2.<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018. október 2.<sup>2</sup> |

_<sup>**1**</sup> A 2014 májusa előtti adatokért látogasson el az [Azure Enterprise Portalra](https://ea.azure.com)._

_<sup>**2**</sup> az 2018-es október 2. előtt az Azure Government-fiókokhoz tartozó globális fiókok és [Azure Fiókközpont gov](https://account.windowsazure.us/subscriptions) [Azure Fiókközpont](https://account.azure.com/subscriptions) ._

_<sup>**3**</sup> A Microsoft Ügyfélszerződések 2019 márciusában kezdődtek, így ennél korábbi adatokkal nem rendelkeznek._

_<sup>**4**</sup> Előfordulhat, hogy a kreditalapú és előre fizetett előfizetések korábbi adatai nem egyeznek meg a számlán szereplő adatokkal. Lásd lejjebb: [Előfordulhat, hogy a korábbi adatok nem egyeznek meg a számlán szereplő adatokkal](#historical-data-might-not-match-invoice)._

Az alábbi ajánlatok még nem támogatottak:

| Kategória  | **Ajánlat neve** | **Kvótaazonosító** | **Ajánlatszám** |
| --- | --- | --- | --- |
| **Azure Germany** | Azure Germany – használatalapú fizetés | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Felhőszolgáltató (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Felhőszolgáltató (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Felhőszolgáltató (CSP)** | Azure Germany – CSP for Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Használatalapú fizetés**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Használatalapú fizetés** | Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Használatalapú fizetés**                 | Microsoft Azure szponzorálás | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Támogatási csomagok** | Standard szintű támogatás                    | Default_2014-09-01 | MS-AZR-0041P |
| **Támogatási csomagok** | Közvetlen professzionális támogatás         | Default_2014-09-01 | MS-AZR-0042P |
| **Támogatási csomagok** | Fejlesztői támogatás                   | Default_2014-09-01 | MS-AZR-0043P |
| **Támogatási csomagok** | Németországi támogatási csomag                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Támogatási csomagok** | Azure Government Standard Support   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Támogatási csomagok** | Azure Government Pro-Direct Support | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Támogatási csomagok** | Azure Government Developer Support  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Ingyenes próbalehetőség használatalapú fizetésre való frissítéshez

Ha többet szeretne tudni az ingyenes szintű szolgáltatások rendelkezésre állásáról, miután frissített az ingyenes próbaverzióról a használatalapú fizetéses verzióra, tekintse meg az [ingyenes Azure-fiókkal kapcsolatos gyakori kérdéseket](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Az ajánlattípus meghatározása

Ha nem látja az előfizetéshez tartozó adatokat, és meg szeretné állapítani, hogy az előfizetése a támogatott ajánlatok közé tartozik-e, ellenőrizheti az előfizetés támogatottságát. Annak ellenőrzéséhez, hogy egy adott Azure-előfizetés támogatott-e, jelentkezzen be az Azure Portalra. A bal oldali menüpanelen válassza a **Minden szolgáltatás** lehetőséget. A szolgáltatások listájában válassza az **Előfizetések** elemet. Az előfizetések listájának menüjében válassza ki az ellenőrizni kívánt előfizetést. Az előfizetés megjelenik az Áttekintés lapon, ahol az **Ajánlatot** és az **Ajánlat azonosítóját** is láthatja. Az alábbi képen egy példa látható.

![Az előfizetés Áttekintés lapját az Ajánlattal és az Ajánlat azonosítójával együtt mutató példa](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>A Cost Managementben elérhető költségek

Az alábbi táblázatok azt mutatják, hogy a Cost Management mely adatokat tartalmazza, illetve melyeket nem. A számla létrehozásáig minden költség csak becsült költség. A feltüntetett költségek nem tartalmazzák az ingyenes és előre fizetett jóváírásokat.

| **Tartalmazza** | **Nem tartalmazza** |
| --- | --- |
| Azure-szolgáltatás használata<sup>5</sup>        | Támogatási díjak – További információért tekintse meg a [számlában használatos kifejezések ismertetését](../understand/understand-invoice.md). |
| Marketplace-ajánlatok használati adatai<sup>6</sup> | Adók – További információért tekintse meg a [számlában használatos kifejezések ismertetését](../understand/understand-invoice.md). |
| Marketplace-vásárlások<sup>6</sup>      | Jóváírások – További információért tekintse meg a [számlában használatos kifejezések ismertetését](../understand/understand-invoice.md). |
| Foglalásvásárlások<sup>7</sup>      |  |
| A foglalásvásárlások amortizációja<sup>7</sup>      |  |

_<sup>**5**</sup> Az Azure-szolgáltatás használata a foglaláson és a megegyezés szerinti árakon alapul._

_<sup>**6**</sup> A Marketplace-vásárlások jelenleg nem érhetők el MSDN- és Visual Studio-ajánlatokhoz._

_<sup>**7**</sup> A foglalásvásárlások jelenleg csak nagyvállalati szerződéses (EA-) és Microsoft-ügyfélszerződéses fiókokhoz érhetők el._

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Címkék használata költség- és használati adatokban

Az Azure Cost Management a címkéket az egyes szolgáltatások által küldött használati adatok részeként fogadja. Ezekre a címkékre a következő korlátozások érvényesek:

- A címkéket közvetlenül az erőforrásokra kell alkalmazni, és nem öröklődnek implicit módon a szülő erőforráscsoportból.
- Az erőforráscímkék csak az erőforráscsoportokban üzembe helyezett erőforrások esetén támogatottak.
- Előfordulhat, hogy néhány üzembe helyezett erőforrás nem támogatja a címkéket vagy nem tartalmaz címkéket a használati adatokban.
- Az erőforráscímkék csak a címke alkalmazásakor szerepelnek a használati adatokban, így az előzményadatokra nem lesznek alkalmazva.
- Az erőforráscímkék csak az adatok frissítése után érhetők el a Cost Managementben.
- Az erőforráscímkék csak akkor érhetők el a Cost Managementben, ha az erőforrás aktív/fut, és használati adatokat hoz létre. Például akkor, ha a virtuális gép fel van szabadítva.
- A címkék kezeléséhez minden erőforrás esetében közreműködői hozzáférés szükséges.
- A címkeszabályzatok kezeléséhez tulajdonosi vagy szabályzat-közreműködői hozzáférés szükséges egy felügyeleti csoporthoz, egy előfizetéshez vagy egy erőforráscsoporthoz.
    
Ha nem talál egy adott címkét a Cost Managementben, gondolja át az alábbi kérdéseket:

- A címke közvetlenül az erőforrásra lett alkalmazva?
- A címke több mint 24 órával ezelőtt lett alkalmazva?
- Támogatja az erőforrás típusa a címkéket? Az alábbi erőforrástípusok 2019. december 1-től nem támogatják a címkéket a használati adatokban. A támogatott címkék teljes listája: [Címkék támogatása az Azure-erőforrásokban](../../azure-resource-manager/management/tag-support.md).
    - Azure Active Directory B2C-címtárak
    - Azure Bastion
    - Azure-tűzfalak
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Terheléselosztók
    - Network Watcher
    - Notification Hubs
    - Service Bus
    - Time Series Insights
    
Íme, néhány tipp a címkék használatához:

- Előre tervezhet és meghatározhat egy címkézési stratégiát, amely lehetővé teszi a költségek szervezet, alkalmazás, környezet stb. szerinti lebontását.
- Az Azure Policy használatával az erőforráscsoportok címkéit egyéni erőforrásokra másolhatja, és biztosíthatja a címkézési stratégia betartását.
- A Tags API és a Query vagy a UsageDetails API együttes használatával az összes költség lekérhető az aktuális címkék alapján.

## <a name="cost-and-usage-data-updates-and-retention"></a>Költség- és használati adatok frissítése és megőrzése

A költség- és használati adatok általában 8-24 órán belül válnak elérhetővé az Azure Portal Költségkezelés + Számlázás területén és a támogató API-kban. A költségek áttekintésekor tartsa szem előtt a következő szempontokat:

- Minden Azure-szolgáltatás (például a Storage, a Compute és az SQL) különböző időközönként bocsát ki használati adatokat – előfordulhat, hogy egyes szolgáltatások adatait hamarabb látja, mint másokét.
- Az aktuális számlázási időszak becsült költségei naponta hatszor frissülnek.
- Az egyre több használattal az aktuális számlázási időszak becsült költségei változhatnak.
- Az egyes frissítések kumulatívak, így az előző frissítésből származó összes sorelemet és információt tartalmazzák.
- Az Azure a számlázási időszak vége után legfeljebb 72 órával véglegesíti vagy _zárja le_ az aktuális számlázási időszakot.
- A nyitott hónap időszakában (nem számlázott időszak) a költségkezelési adatokat becslésként kell kezelni. Néhány esetben előfordulhat, hogy a költségek késve érkeznek meg a rendszerbe a tényleges használat után.

Az alábbi példák bemutatják, hogyan érhetnek véget a számlázási időszakok:

* Nagyvállalati Szerződéses (EA-) előfizetések – Ha a számlázási hónap május 31-én ér véget, a becsült díjak legfeljebb 72 órával később frissülnek. Ebben a példában április 4. éjfélig (UTC).
* Használatalapú fizetéses előfizetések – Ha a számlázási hónap május 15-én ér véget, előfordulhat, hogy a becsült díjak legfeljebb 72 órával később frissülnek. Ebben a példában május 19. éjfélig (UTC).

Miután a költség- és használati adatok elérhetővé váltak a Költségkezelés + Számlázás területen, a rendszer legalább hét éven át megőrzi őket.

### <a name="rerated-data"></a>Újraszámolt adatok

Függetlenül attól, hogy az Cost Management API-kat, Power BIokat vagy az Azure Portal az adatok lekérésére használja-e, várhatóan az aktuális számlázási időszak díjait számítjuk fel. A díjak változhatnak, amíg a számla le nem zárul.

## <a name="cost-rounding"></a>Költségek kerekítése

A Cost Managementben megjelenített költségek kerekítve vannak. A Query API által visszaadott költségek nincsenek kerekítve. Például:

- Költségelemzés az Azure Portalon – A díjak kerekítése standard kerekítési szabályok szerint történik: a 0,5-nél nagyobb értékeket felfelé, az egyéb költségeket lefelé kerekíti a rendszer. Kerekítés csak értékek megjelenítésekor történik. Az adatfeldolgozás és -összesítés során nem történik kerekítés. A költségelemzés a következőképpen összesíti a költségeket:
  -    1\. díj: 0,004 dollár
  - 2\. díj: 0,004 dollár
  -    A díjak összesítésének megjelenítése: 0,004 + 0,004 = 0,008. A megjelenő díj 0,01 dollár.
- Query API – A díj nyolc tizedesjegyig jelenik meg, és nem történik kerekítés.

## <a name="historical-data-might-not-match-invoice"></a>Előfordulhat, hogy a korábbi adatok nem egyeznek meg a számlán szereplő adatokkal

Előfordulhat, hogy a kreditalapú és előre fizetett ajánlatok korábbi adatai nem egyeznek meg a számlán szereplő adatokkal. Bizonyos Azure-beli használatalapú fizetéses, MSDN- és Visual Studio-ajánlatok esetében lehetséges, hogy Azure-kreditek és előzetes kifizetések vannak alkalmazva a számlára. A Cost Managementban megjelenő korábbi adatok csak a becsült fogyasztási díjakon alapulnak. A Cost Management korábbi adatai nem tartalmazzák a kifizetéseket és krediteket. Előfordulhat, hogy a következő ajánlatokban megjelenített korábbi adatértékek nem egyeznek pontosan a számlával.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Ingyenes próba (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="next-steps"></a>További lépések

- Ha még nem végezte el a Cost Management első lépéseit, itt megtekintheti a [költségelemzés elkezdésének](./quick-acm-cost-analysis.md) lépéseit.