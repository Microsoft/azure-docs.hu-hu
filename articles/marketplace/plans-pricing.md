---
title: A kereskedelmi Piactéri ajánlatok csomagjai és díjszabása
description: Ismerje meg a Microsoft kereskedelmi piactérhez készült terveket a partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: e97171e61814b832e78f7bd815d8d6f341a90840
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91858172"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>A kereskedelmi Piactéri ajánlatok csomagjai és díjszabása

A terv meghatározza az ajánlat hatókörét és korlátait, valamint a kapcsolódó díjszabást, ha alkalmazható. Az ajánlat típusától függően például kiválaszthatja a regionális piacokat, és kiválaszthatja, hogy a csomag látható-e a nyilvános vagy csak a privát közönség számára. Egyes ajánlati típusok támogatják az ismétlődő előfizetéseket, néhány támogatást a használaton alapuló díjszabáshoz, és egyesével lehetővé teszik, hogy az ügyfél az ajánlatot közvetlenül a közzétevőtől vásárolt licenccel vásárolja meg. Ez rugalmasságot biztosít az ügyfelek számára különböző technikai és díjszabási lehetőségekkel, ha alkalmazható.

Akár 100 tervet is létrehozhat a csomagokat támogató összes ajánlathoz, és akár 45-ig is lehet [privát](#plan-visibility). Csak az Azure-beli virtuálisgép-ajánlatokhoz, az Azure-alkalmazásokhoz (felügyelt alkalmazásokhoz) és a szolgáltatott szoftverekhez (SaaS) használható fizetős csomagok hozhatók létre. Ha a Microsoft által kínált ajánlatok bármelyikének értékesítését választja, akkor legalább egy csomagot létre kell hoznia. Létrehozhat terveket néhány egyéb ajánlat típushoz, de az ajánlati típusok csomagjai nem tartalmazzák a díjszabási lehetőségeket.

> [!TIP]
> Egy olyan, a Microsoft által biztosított ajánlat, amelynek keretében a Microsoft megkönnyíti a szoftvergyártók számára a licencek cseréjét a kiadó nevében.

## <a name="plans-by-offer-type"></a>Csomagok ajánlat típusa szerint

Az alábbi táblázat az egyes ajánlati típusok tervezési lehetőségeit mutatja be. Ezekről a lehetőségekről a következő szakaszokban talál további információt. 

| Csomag típusa | Díjszabási lehetőségeket tartalmazó csomagok | Díjszabási lehetőségek nélküli csomagok | Privát célközönség lehetőség |
| ------------ | ------------- | ------------- | ------------- |
| Azure-beli felügyelt alkalmazás | &#10004; | | &#10004; |
| Azure-megoldás sablonja | | &#10004; | &#10004; |
| Azure-tároló | | &#10004; (BYOL) | |
| IoT Edge modul |  | &#10004; |  |
| Felügyelt szolgáltatás |  | &#10004; (BYOL) | &#10004; |
| Szolgáltatott szoftver | &#10004; |  | &#10004; |
| Azure virtuális gép | &#10004; |  | &#10004; |
|||||

A csomagok a következő típusú ajánlatokban nem támogatottak:

- Tanácsadási szolgáltatás
- Dynamics 365 Business Central
- Dynamics 365 Customer engagement & PowerApps
- Dynamics 365 for Operations
- Power BI alkalmazás

## <a name="plan-information"></a>Információ megtervezése

Az egyes ajánlattételi típusok különböző információkat igényelnek, amikor új csomagot hoz létre. A [közzétételi útmutatóban](publisher-guide-by-offer-type.md)található ajánlat-specifikus cikkekre mutató hivatkozásokat talál. Miután létrehozta az új csomagot a **terv áttekintő** oldalán, különböző lapokat fog látni, mint például a **lista** vagy a **díjszabás és a rendelkezésre állás** a csomag különböző részleteinek konfigurálásához. Az egyes lapok hiányos vagy befejezett állapotot jelenítenek meg a mezőkön végzett munka során.

![Bemutatja a partner Center terv listája lapját. A terv neve, a terv összegzése és a terv leírása mezők ki vannak emelve.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Egy új csomagra vonatkozóan néhány gyakori részletet kell elvégeznie:

- **Csomag azonosítója**: hozzon létre egy egyedi azonosítót az ajánlat minden csomaghoz. Legfeljebb 50 karaktert használhat: csak kisbetűket, alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhat. Ez az azonosító látható lesz az ügyfelek számára a termék URL-címében és Azure Resource Manager-sablonokban (ha vannak ilyenek). Az ajánlat közzététele után nem módosítható ez az azonosító.
- **Csomag neve**: (a fenti képen 1. ábrafelirat.) Minden csomaghoz hozzon létre egyedi nevet az ajánlatban. Legfeljebb 50 karaktert használhat. A csomag neve az adott ajánlat részét képező szoftvercsomagok megkülönböztetésére szolgál (például ajánlat neve: standard csomag és nagyvállalati csomag). Az ügyfelek ezt a nevet fogják látni, amikor azt döntik el, hogy melyik tervet kell kiválasztani az ajánlaton belül.
- **Terv összegzése**: (a fenti képen 2. ábrafelirat) Ez az összefoglalás az Azure Marketplace keresési eredményei között jelenik meg, és akár 100 karaktert is tartalmazhat.
   > [!NOTE]
   > Ez a mező nem vonatkozik az SaaS-ajánlatokra.
- **Terv leírása**: (a fenti képen 3. ábrafelirat) Adja meg a terv leírását, amely ismerteti, hogy mi teszi ezt a csomagot az ajánlat más csomagjaitól. Legfeljebb 500 karaktert használhat. Ez a tartalom megjelenik az ügyfeleknek az ajánlat listázása oldalon (i), ahogy a böngészés közben megkeresi az ajánlatot, és kiválasztja az ajánlathoz tartozó csomagot.

A terv neve és leírása megjelenik a kereskedelmi piactér online áruház (ok) ajánlati lista lapján. Az alábbi képernyőfelvételen három, az Azure piactéren elérhető SaaS-ajánlatra vonatkozó terv látható.

![A partner Center ajánlati lista lapját mutatja be. Három csomag látható.](./media/commercial-marketplace-plans/offer-listing-page.png)

A csomagok létrehozása után a **terv áttekintő** oldala a csomagok nevének, azonosítójának, egyéb részleteinek, jelenlegi közzétételi állapotának és bármely elérhető műveletnek a listáját jeleníti meg. Az elérhető műveletek a csomag állapotától függenek, és a következők lehetnek:

- Ha a terv állapota **Piszkozat** – delete Piszkozat.
- Ha a terv állapota **élő** – a terv értékesítésének leállítása vagy a privát célközönség szinkronizálása.

Az alábbi képernyőfelvételen két vázlatos ajánlat látható.

![A partner Center terv áttekintés lapját mutatja be. Két csomag jelenik meg.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Díjszabás és rendelkezésre állás

A kereskedelmi piactér egy ügynökségi modellen működik, amellyel a kiadók díjszabást, Microsoft-számlákat és a Microsoftot fizetnek a kiadók számára, miközben az ügynökség díját is megtartják. Az ajánlat piacai, láthatósága és díjszabása (ha alkalmazható) a **díjszabás és a rendelkezésre** állás vagy a **rendelkezésre állás** lapon határozható meg.

- **Piacok**: minden csomagnak legalább egy piacon elérhetőnek kell lennie.  Lehetősége van arra, hogy csak a "átutalt adó" országokat válassza, amelyekben a Microsoft az Ön nevében kifizeti az értékesítési és a használati adót.
- **Díjszabás**: az árképzési modellek csak az Azure által felügyelt alkalmazásokra, SaaS-re és Azure-beli virtuális gépekre vonatkozó csomagokra vonatkoznak. Az ugyanahhoz az ajánlathoz tartozó összes csomagnak ugyanazt az árképzési modellt kell használnia.  
- **Terv láthatósága**: az ajánlat típusától függően megadhat egy privát célközönséget, vagy elrejtheti az ajánlatot vagy a csomagot az Azure piactéren. Ezt részletesebben is ismertetjük a jelen cikk későbbi részében [látható láthatósági tervekben](#plan-visibility) .

> [!TIP]
> Javasoljuk, hogy olyan csomagokat hozzon létre, amelyek leginkább a célként megadott ügyfélkör használati mintáinak felelnek meg. Ez csökkenti a felhasználókat a használatban lévő változások alapján a gyakori váltási tervekben. A három forgalmi díjas szolgáltatással rendelkező SaaS-ajánlatra példaként tekintse meg a [minta ajánlatot](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Díjszabási modellek

A következő típusú csomagokhoz tartozó díjszabási modellt kell rendelnie. Minden ilyen típusú ajánlat különböző díjszabási modellel rendelkezik:

- **Azure által felügyelt alkalmazás**: átalánydíjas (havi) és használaton alapuló díjszabás (mérési szolgáltatási méretek).
- **Szolgáltatásként szolgáló szoftver**: átalánydíjas (havi vagy éves), felhasználónkénti és használati díjszabás (mérési szolgáltatási méretek). 
- **Azure virtuális gép**: saját licenc (BYOL) és használati alapú díjszabás használata. A használaton alapuló díjszabási modell esetében alapszintű, alapméretű vagy piaci és alapméret szerint lehet díjat fizetni. A BYOL-licencek modellje nem teszi lehetővé a további, használaton alapuló költségek használatát.   (A BYOL virtuális gépekre vonatkozó ajánlatok nem igényelnek díjszabási modellt.)

Az ugyanahhoz az ajánlathoz tartozó összes csomagnak ugyanazt az árképzési modellt kell használnia. Egy SaaS-ajánlat például nem rendelkezhet egy átalánydíjas csomaggal, és felhasználónként egy másik csomag. Részletes információkért tekintse meg a konkrét ajánlat dokumentációját.

Ha már beállította a csomag árát Egyesült Államok dollárban (USD), és egy másik piaci helyet ad hozzá, az új piac díját az aktuális árfolyamok alapján számítjuk ki. A módosítások mentése után megjelenik egy **exportálási ár (xlsx)** hivatkozás, amelynek segítségével áttekintheti és módosíthatja az egyes piacok árát a közzététel előtt.

> [!IMPORTANT]
> Az ajánlat közzététele után a díjszabási modell választása nem módosítható.

A átalánydíjas SaaS-ajánlatok és a felügyelt alkalmazások a piactér-mérési szolgáltatás segítségével értékelt számlázási támogatást biztosítanak. Ez egy használaton alapuló számlázási modell, amely lehetővé teszi, hogy a nem szabványos egységeket (például a sávszélességet vagy e-maileket) definiálja, amelyeket az ügyfelek fogyasztási szinten fognak fizetni. A [felügyelt alkalmazások](./partner-center-portal/azure-app-metered-billing.md) és [SaaS-alkalmazások](./partner-center-portal/saas-metered-billing.md)mért számlázásával kapcsolatos további információkért tekintse meg a kapcsolódó dokumentációt.

## <a name="custom-prices"></a>Egyéni árak

Egyéni árak egyéni piacon történő beállításához exportálja, módosítsa, majd importálja a díjszabási táblázatot. Ön felelős a díjszabás ellenőrzésével és a beállítások tulajdonlásával.

A közzététel előtt körültekintően tekintse át az árakat, mivel bizonyos korlátozások vonatkoznak a csomag közzététele után megjelenő változásokra:

- A csomag közzététele után a díjszabási modell nem módosítható.
- Egy csomagra vonatkozó számlázási időszak közzététele után később nem távolítható el.
- Miután közzétette a csomag egy piacának árát, később nem módosítható.

A Egyesült Államok dollárban beállított díjak (USD) az összes kiválasztott piac helyi pénznemére lesznek átalakítva, az aktuális árfolyamok alapján, a mentéskor. A közzététel előtt érvényesítse ezeket az árakat az árképzési táblázat exportálásával, és tekintse át az árat a kiválasztott piacokon.

1. Először mentenie kell a díjszabási módosításokat, hogy engedélyezze a díjszabási adatai exportálását. A **díjszabás és rendelkezésre állás** lap alján kattintson a **Piszkozat mentése** gombra.
1. A **díjszabás** területen válassza ki az **exportálási díjszabási** hivatkozást.
1. Nyissa meg a exportedPrice.xlsx fájlt a Microsoft Excelben.
1. A számolótáblában végezze el a kívánt frissítéseket a díjszabási adatokhoz, majd mentse a következőt:. CSV-fájl.
    > [!NOTE]
    > Előfordulhat, hogy a fájl frissítése előtt engedélyeznie kell a szerkesztést az Excelben.
1. A díjszabás **és rendelkezésre állás** lapon, a **díjszabás** területen válassza a **díjszabási adat importálása** hivatkozást.
1. A megjelenő párbeszédpanelen válassza az **Igen** lehetőséget.
1. Válassza ki a frissített exportedPrice.xlsx fájlt, majd kattintson a **Megnyitás** gombra.

## <a name="plan-visibility"></a>Terv láthatósága

Terveket készíthet a nyilvánosan elérhető, vagy csak egy adott (privát) közönség számára elérhető típusú ajánlatokhoz. A privát csomagokkal rendelkező ajánlatok közzé lesznek téve a Azure Portal. További információ a [Microsoft kereskedelmi piactéren](private-offers.md)elérhető privát csomagokról.

Az ajánlat típusától függően az Azure-bérlői azonosítók vagy az Azure-előfizetési azonosítók használatával határozhatja meg a saját célközönségét. Egy. CSV-fájllal akár 10 azonosítót is megadhat, vagy akár 10, 00 előfizetés-azonosítót vagy 20 000 bérlői azonosítót is importálhat. A tanácsadási szolgáltatásokhoz és a Dynamics 365-ajánlatokhoz is meghatározhat privát célközönséget, amelyek nem rendelkeznek csomagokkal.

Miután közzétette az ajánlatát egy privát csomaggal, frissítheti a célközönséget, vagy dönthet úgy, hogy a tervet mindenki számára elérhetővé teszi. Ha a csomagot mindenki számára láthatóvá teszi, mindenki számára láthatónak kell maradnia, és nem konfigurálható újra saját csomagként.

> [!NOTE]
> Egy privát közönség eltér az előzetes verzió célközönségtől. Egyes ajánlati típusok **rendelkezésre állási** oldalán meghatározhatja azt a célközönséget, aki előzetesen megtekintheti az ajánlatát a kereskedelmi piactéren élőben közzétett ajánlat előtt. Bár a privát célközönségek megjelölése csak egy adott csomagra vonatkozik, az előzetes verzió célközönsége megtekintheti az összes csomagot (privát vagy nem), de csak a korlátozott előzetes verzióban, a terv tesztelése és ellenőrzése közben.

Dönthet úgy is, hogy a tervet teljes mértékben az Azure piactéren is elrejti, ha az ajánlat egy felügyelt alkalmazás vagy megoldás sablonjának részeként lesz telepítve.

## <a name="free-trials"></a>Ingyenes próbaverziók

Az ingyenes próbaverziót engedélyezheti az Azure-beli virtuális gépek és SaaS-ajánlatok számára elérhető tervekben.

> [!NOTE]
> Ez a szakasz a Microsofton keresztül eladott ajánlatokra vonatkozó fizetett csomagokra vonatkozó ingyenes próbaverziókat ismerteti. Ez különbözik az ingyenes próbaverziós listáktól, akik a tranzakciók egymástól független feldolgozását választják. A következő típusú ajánlatokhoz létrehozhat egy ingyenes próbaverziós listát (amely nincs tervhez társítva):
> - Azure virtuális gép 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 a Customer engagement & PowerApps
> - Dynamics 365 for Operations
>
> A listázási lehetőségekkel kapcsolatos további információkért lásd: [közzétételi lehetőség meghatározása](determine-your-listing-type.md).

Az ingyenes próbaverziók az összes számlázási modell esetében támogatottak, a mért csomagok kivételével. Az SaaS-csomagok 1 hónapos ingyenes próbaverziókat tesznek lehetővé. Az Azure-beli virtuálisgép-csomagok 1, 3 vagy 6 hónapos ingyenes próbaverziókat is lehetővé tesznek.

Ha az ügyfél egy ingyenes próbaverziót választ ki, akkor a számlázási adatokat gyűjtjük, de nem kezdjük el az ügyfél számlázását, amíg a próbaverziót nem konvertáljuk fizetős előfizetésre. Az ingyenes próbaverziók automatikusan átváltanak fizetős előfizetésre a próbaverzió végén, kivéve, ha az ügyfél a próbaidőszak lejárta előtt megszakítja az előfizetést.

A próbaidőszak alatt az ügyfelek kiértékelik a támogatott csomagok bármelyikét ugyanazon az ajánlaton belül, ahol az ingyenes próbaverzió engedélyezve van. Ha egy másik próbaverzióra váltanak ugyanazon az ajánlaton belül, a próbaidőszak nem indul újra. Ha például egy ügyfél 15 napig ingyenes próbaverziót használ, majd ugyanarra az ajánlatra vált egy másik ingyenes próbaidőszakra, az új próbaidőszak 15 napig érvényes lesz. Az ingyenes próbaverzió, amelyet az ügyfél kiértékelt, amikor a próbaidőszak véget ér, az automatikusan fizetett előfizetésre lett konvertálva.

Miután egy ügyfél kiválaszt egy ingyenes próbaverziót egy csomagra, a próbaidőszak végéig nem konvertálhat fizetős előfizetésre erre a csomagra. Ha egy ügyfél úgy dönt, hogy az ajánlaton belül egy másik csomagra vált, amely nem rendelkezik ingyenes próbaverzióval, akkor az átalakítás megtörténik, de az ingyenes próbaverzió azonnal megszűnik, és az összes adatvesztés megszűnik.

> [!NOTE]
> Miután egy ügyfél megkezdte a csomag kifizetését, nem kaphat ingyenes próbaidőszakot ugyanarra az ajánlatra, még akkor is, ha az ingyenes próbaverziókat támogató másik csomagra vált.

Az ingyenes próbaverzióban jelenleg részt vevő ügyfél-előfizetésekkel kapcsolatos információk beszerzéséhez használja az új API-tulajdonságot `isFreeTrial` , amely igaz vagy hamis értékként lesz megjelölve. További információ: [SaaS Get előfizetés API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

## <a name="next-steps"></a>Következő lépések

- Ha meg szeretné tudni, hogyan adhat hozzá vagy frissíthet egy meglévő ajánlatot, tekintse meg [a meglévő ajánlat frissítése a kereskedelmi piactéren](./partner-center-portal/update-existing-offer.md)című témakört.
- További információ a tranzakciós lehetőségekről és a kapcsolódó díjszabási modellekről: [kereskedelmi Piactéri Transact-képességek](./marketplace-commercial-transaction-capabilities-and-considerations.md).
