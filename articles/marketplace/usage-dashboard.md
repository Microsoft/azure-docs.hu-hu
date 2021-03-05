---
title: Használati irányítópult a kereskedelmi piactér elemzésében | Azure piactér
description: Ismerje meg, hogyan érheti el az Azure Marketplace-en közzétett ajánlatok összes használati és mért számlázási mérőszámát.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: d898efb44bb92151a5d044a6699a4efb70fa9390
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215731"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>A Használat irányítópult a kereskedelmi piactér-elemzésekben

Ez a cikk a partner Center használati irányítópultján tartalmaz információkat. Ez az irányítópult megjeleníti az összes virtuális gépet (VM), amely normalizált használatot, nyers használatot és mért számlázási mérőszámokat kínál három külön lapon: a virtuális gép normalizált használata, a virtuális gép nyers használata és a mért számlázási használat.

A partner Center használati irányítópultjának **kereskedelmi piactér** területén való eléréséhez válassza a **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **használat** elemzése lehetőséget.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).

## <a name="usage-dashboard"></a>Használat irányítópult

Az **elemzés** menü **használati** irányítópultja megjeleníti az összes szolgáltatott szoftver (SaaS) aktuális rendeléseit. A következő elemek grafikus ábrázolásait tekintheti meg:

- Használati trend
- Normalizált használat ajánlatok szerint
- Normalizált használat más dimenziók szerint: virtuálisgép-méret, értékesítési csatornák és ajánlati típusok
- Használat földrajz szerint
- Részletes használati táblázat
- Megrendelések oldal szűrőinek

> [!NOTE]
> A partner Centerben a használati események létrehozása és a jelentéskészítés közötti maximális késés 48 óra.

## <a name="elements-of-the-usage-dashboard"></a>A használati irányítópult elemei

A következő szakaszok azt ismertetik, hogyan használható a használati irányítópult és hogyan lehet beolvasni az adatokat.

### <a name="month-range"></a>Hónap tartománya

Az egyes lapok jobb felső sarkában megtalálhatja a hónapok tartományának kijelölését. Szabja testre a **használati** oldal gráfok kimenetét úgy, hogy kijelöl egy hónapot az elmúlt 6 vagy 12 hónap alapján, vagy egy olyan egyéni hónap tartományt választ, amelynek maximális időtartama 12 hónap. Az alapértelmezett hónap tartománya (számítási időszak) hat hónap.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="A használati irányítópulton látható havi szűrők ábrázolása.":::

### <a name="usage-trend"></a>Használati trend

Ebben a szakaszban az ügyfelek által a kiválasztott számítási időszakban felhasznált összes ajánlat teljes használati idejét és trendjét fogja megtalálni. A mérőszámokat és a növekedési trendeket egy vonalas diagram jelképezi. Az egyes hónapok értékének megjelenítése a diagramon a vonal fölé helyezve. A widget használati metrikái alatti százalékos érték a kiválasztott számítási időszak növekedésének vagy csökkenésének mértékét jelzi.

Kétféle használati idő létezik: a virtuális gép normalizált használata és a virtuális gép nyers használata.

- A normalizált használati idő a virtuálisgép-magok számának ([virtuálisgép-magok száma] x [órányi nyers használat]) fiókban normalizált használati órákat határozza meg. A "SHAREDCORE" jelölésű virtuális gépek a 1/6 (vagy 0,1666) értéket használják [virtuálisgép-magok száma] szorzóként.
- A nyers használati órák határozzák meg, hogy a virtuális gépek mennyi idő alatt futnak.

[![A szokásos használati és nyers használati adatokat szemlélteti a használati irányítópulton.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Normalizált használat ajánlatok szerint

Ez a szakasz az Azure Marketplace-en a használaton alapuló ajánlatok teljes használati idejét és trendjét tartalmazza. Az ajánlatok táblázatos normalizált használatát alább találja.

- A **normalizált használat** a halmozott oszlopdiagram használatával megjeleníti az első 5 ajánlat normalizált használati idejének részletezését a kiválasztott számítási időszak alapján. Az első öt ajánlat egy gráfban jelenik meg, míg a REST az **összes** kategóriába van csoportosítva.
- A halmozott oszlopdiagram a kiválasztott dátumtartomány hónapról hónapra eső növekedési trendjét ábrázolja. A hónap oszlopai az ajánlatokban szereplő használati órákat jelölik az adott hónap legmagasabb használati idejével. A diagram a másodlagos Y tengelyen ábrázolt százalékos növekedési arányt ábrázolja.
- A jelmagyarázatban megadott ajánlatokat kiválasztva csak a gráfban található ajánlatokat jelenítheti meg.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Szemlélteti a normalizált használati adatokat a használati irányítópulton.":::

Kiválaszthat bármely ajánlatot, és legfeljebb három SKU-t az ajánlathoz és a kiválasztott SKU-hoz tartozó havi használati trend megtekintéséhez.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="A normalizált használati ajánlatokat és SKU-adatokat szemlélteti a használati irányítópulton.":::

### <a name="orders-by-offers-and-skus"></a>Megrendelések ajánlatok és SKU-i szerint

Az **ajánlatok és SKU-diagramok alapján** megjelenő megrendelések az összes ajánlat mértékeit és trendeit mutatják be. Vegye figyelembe a következőket:

- A legfontosabb ajánlatok a gráfban jelennek meg, a többi ajánlat pedig az **összes Rest**-ként van csoportosítva.
- A jelmagyarázatban megadott ajánlatokat kiválasztva csak a gráfban található ajánlatokat jelenítheti meg.
- Ha a diagramon egy szelet fölé viszi az egérmutatót, a megrendelések száma és az ajánlat százalékos aránya az összes ajánlathoz képest teljes számú rendelésre hasonlít.
- A **Orders by ajánlatok trend** a havi havi növekedési trendeket jeleníti meg. A hónap oszlop a megrendelések számát jelöli az ajánlat neve alapján. A diagram a z-tengelyen ábrázolt növekedési százalékos trendet jeleníti meg.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="A rendeléseket a használati irányítópulton található ajánlatok és SKU-diagramok alapján szemlélteti.":::

Az ajánlatra, a SKU-ra és a munkaállomásokra vonatkozó havi hónapot megtekintheti, és legfeljebb három SKU-t választhat ki.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="A rendeléseket a használati irányítópulton található ajánlatok és SKU-diagramok alapján szemlélteti. Megjelenik az ajánlat trend, a SKU trend és a seats trend.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Normalizált használat más dimenziók szerint: virtuális gépek mérete, értékesítési csatornák és ajánlat típusa

A méretek három lapból állnak: virtuális gép mérete, értékesítési csatornák és ajánlat típusa. Megtekintheti a használati metrikákat és a hónapon felüli trendeket az egyes dimenziók esetében.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="A normalizált használati egyéb dimenziók diagramot szemlélteti a használati irányítópulton.":::

### <a name="usage-by-geography"></a>Használat földrajz szerint

A kiválasztott számítási időszakra vonatkozóan a hő a földrajz dimenzión belüli teljes használatot jeleníti meg. A Térkép világos és sötét színe az ügyfelek számának alacsony és magas értékét jelöli. Válasszon ki egy rekordot a táblában az ország/régió nagyításához.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Bemutatja a normalizált használati ország diagramot a használati irányítópulton.":::

Vegye figyelembe a következőket:

- A térképet áthelyezve megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hő egy kiegészítő rácstal rendelkezik, amely az adott helyen megtekintheti az ügyfelek számát, a megrendelések számát és a normalizált használati időt.
- A rácson megkeresheti és kiválaszthatja az országot/régiót a térképen való nagyításhoz. A Térkép **Kezdőlap** gombjára kattintva térjen át az eredeti nézetre.

### <a name="usage-details-table"></a>Használat részletei tábla

A **használat részletei** tábla a legnépszerűbb 1 000-használati rekordok számozott listáját jeleníti meg a használat alapján rendezve. Vegye figyelembe a következőket:

- A rács minden oszlopa rendezhető.
- A rendszer kinyeri az adathalmazt. TSV vagy. CSV-fájl, ha a rekordok száma kisebb, mint 1 000.
- Ha a rekordok száma meghaladja a 1 000-et, az adatok exportálása aszinkron módon történik egy letöltési oldalon, amely a következő 30 napra elérhető lesz.
- Alkalmazzon szűrőket a **részletes használati adatokra** , hogy csak az Önt érdeklő adatokat jelenítse meg. Adatok szűrése ország/régió, értékesítési csatorna, piactér-licenc típusa, használati típus, ajánlat neve, ajánlat típusa, ingyenes próbaverziók, piactér-előfizetés azonosítója, ügyfél-azonosító és cégnév alapján.

_**1. táblázat: az adatszótár kifejezése**_

| Oszlop neve | Attribútum neve | Definíció |
| ------------ | ------------- | ------------- |
| Piactér-előfizetés azonosítója | Piactér-előfizetés azonosítója | Az Azure-előfizetéshez társított egyedi azonosító, amelyet az ügyfél a kereskedelmi Piactéri ajánlat megvásárlásához használt. Az azonosító korábban az Azure-előfizetés GUID-azonosítója volt. |
| MonthStartDate | Hónap kezdő dátuma | A hónap kezdő dátuma a vásárlás hónapját jelöli. |
| Ajánlat típusa | Ajánlat típusa | A kereskedelmi piactér kínálatának típusa. |
| Azure-licenc típusa | Azure-licenc típusa | Az ügyfelek által az Azure megvásárlásához használt licencelési szerződés típusa. Más néven csatorna. Lehetséges értékek:<ui><li>Felhőszolgáltató</li><li>Enterprise</li><li>Enterprise a viszonteladón keresztül</li><li>Utólagos fizetés</li></ul> |
| Marketplace-licenc típusa | Marketplace-licenc típusa | A kereskedelmi Piactéri ajánlat számlázási módszere. Lehetséges értékek:<ul><li>Az Azure-on keresztül számlázva</li><li>Saját licenc használata</li><li>Ingyenes</li><li>Microsoft mint viszonteladó</li></ul> |
| Termékváltozat | Termékváltozat | Az ajánlattal társított csomag. |
| Ügyfél országa | Ügyfél országa/régiója | Az ügyfél által megadott ország/régió neve. Az ország/régió eltérő lehet az ügyfél Azure-előfizetésében lévő országgal vagy régióval. |
| Előzetes verziójú SKU | Előzetes verziójú SKU | Az érték azt jelzi, hogy az SKU-t "Preview"-ként jelölte-e meg. Az érték "yes" lesz, ha az SKU-t ennek megfelelően címkézték, és csak az Ön által jóváhagyott Azure-előfizetések telepíthetők és használhatók. Az érték "nem" lesz, ha az SKU nem "Preview"-ként lett azonosítva. |
| SKU számlázási típusa | SKU számlázási típusa | Az ajánlatban szereplő egyes SKU-hoz társított számlázási típus. Lehetséges értékek:<ul><li>Ingyenes</li><li>Fizetős</li></ul> |
| IsInternal | Elavult | Elavult |
| Virtuális gép mérete | Virtuális gép mérete | A virtuális gépeken alapuló ajánlat típusok esetében ez az entitás az ajánlat SKU-jának megfelelő méretű virtuális gép méretét jelzi. |
| Felhőbeli példány neve | Felhőbeli példány neve | Az a Microsoft Cloud, amelyben a virtuális gép üzembe helyezése megtörtént. |
| ServicePlanName | Elavult | Elavult (ugyanaz a definíció, mint az SKU) |
| Offer Name | Offer Name | A kereskedelmi Piactéri ajánlat neve. |
| DeploymentMethod | Elavult | Elavult (ugyanaz a definíció, mint az ajánlat típusa)
 |
| Ügyfél vállalatának neve | Ügyfél vállalatának neve | Az ügyfél által megadott cég neve. A név különbözhet az ügyfél Azure-előfizetésében szereplő névvel. |
| Usage Date (Használat dátuma) | Usage Date (Használat dátuma) | A használati események generálásának dátuma a használati alapú eszközökhöz. |
| IsMultisolution | Többmegoldásos | Azt jelzi, hogy az ajánlat egy többmegoldásos ajánlat típusa-e. |
| Új ügyfél | Elavult | Elavult |
| Alapvető méret | Alapvető méret | A VM-alapú ajánlathoz társított magok száma. |
| Használati típus | Használati típus | Azt jelzi, hogy az ajánlathoz társított használati esemény a következők egyike-e:<ul><li>Normalizált használat</li><li>Nyers használat</li><li>Mért használat</li></ul> |
| Próbaverzió befejezési dátuma | Próbaverzió befejezési dátuma | Az a dátum, ameddig a rendelés próbaidőszaka lejár vagy véget ér. |
| Ügyfél pénzneme (CC) | Ügyfél pénzneme | Az ügyfél által a kereskedelmi Piactéri tranzakcióhoz használt pénznem. |
| Ár (CC) | Ár | Az ügyfél pénznemében megjelenő SKU egységára. |
| Kifizetési pénznem (PC) | Kifizetés pénzneme | A közzétevőt a közzétevő által konfigurált pénznemben az objektumhoz társított használati eseményekért kell fizetni. |
| Becsült ár (PC) | Becsült ár | Az SKU egységének árát a közzétevő által konfigurált pénznemben. |
| Használati útmutató | Használati útmutató | Egy összefűzött GUID-azonosító, amely a használati jelentés (kereskedelmi piactér-elemzésekben) a kifizetési tranzakciós jelentéssel való összekapcsolására szolgál. A használati segédlet a kifizetési tranzakciós jelentésben a Rendeléskód és a LineItemId kapcsolattal van összekötve. |
| Használati egység | Használati egység | Az SKU-hoz társított felhasználási egység. |
| Ügyfél-azonosító | Ügyfél-azonosító | Az ügyfélhez rendelt egyedi azonosító. Előfordulhat, hogy egy ügyfél nulla vagy több Azure Marketplace-előfizetéssel rendelkezik. |
| Számlázási fiók azonosítója | Számlázási fiók azonosítója | Annak a fióknak az azonosítója, amelyre a számlázást létrehozták. A **Számlázási fiók azonosítójának** hozzárendelése a **Vevőkódhez** a kifizetési tranzakciós jelentés összekapcsolásához az ügyfél, a megrendelés és a használati jelentések segítségével. |
| Használat mennyisége | Használat mennyisége | Az eszköz által az ügyfél által üzembe helyezett összes használati egység.<br>Ez a használat típusa elemen alapul. Ha például a használat típusa normalizált, akkor a használati mennyiség normalizált használatra szolgál. |
| NormalizedUsage | Normalizált használat | Az eszköz által az ügyfél által üzembe helyezett összes normalizált használati egység.<br>A normalizált használati idő a virtuálisgép-magok számának ([virtuálisgép-magok száma] x [órányi nyers használat]) fiókban normalizált használati órákat határozza meg. A "SHAREDCORE" jelölésű virtuális gépek a 1/6 (vagy 0,1666) értéket használják [virtuálisgép-magok száma] szorzóként. |
| MeteredUsage | Mért használat | Az ügyfél által üzembe helyezett ajánlattal konfigurált mérőszámok által felhasznált összes használati egység. |
| RawUsage | Nyers használat | Az eszköz által az ügyfél által üzembe helyezett összes nyers használati egység.<br>A nyers használati órák határozzák meg, hogy mennyi idő alatt futnak a virtuális gépek a használati egységekben. |
| Becsült kiterjesztett díj (CC) | Becsült kiterjesztett díj az ügyfél pénznemében | A használattal kapcsolatos díjakat jelzi. Az oszlop az ár (CC) és a nyers használat szorzata. |
| Becsült kiterjesztett díj (PC) | Becsült meghosszabbított díj a kifizetési pénznemben | A használattal kapcsolatos díjakat jelzi. Az oszlop a becsült ár (számítógép) és a nyers használat szorzata. |
|

### <a name="usage-page-filters"></a>Használati oldal szűrői

A **használati** oldal szűrői az Orders (megrendelések) lap szintjén lesznek alkalmazva. Kiválaszthat egy vagy több szűrőt a diagram megjelenítéséhez a megtekinteni kívánt feltételekhez, valamint a használati rendelések adatrács/exportálás című részében megjelenítendő adatokat. A szűrők a használati oldal jobb felső sarkában kiválasztott hónapra kinyert adatokon lesznek alkalmazva.

A virtuális gépek nyers használatára szolgáló widgetek és exportálási jelentés hasonló a virtuális gép normalizált használatának a következő különbségek esetén:

- A normalizált használati idő a virtuálisgép-magok számának ([virtuálisgép-magok száma] x [órányi nyers használat]) fiókban normalizált használati órákat határozza meg. A "SHAREDCORE" jelölésű virtuális gépek a 1/6 (vagy 0,1666) értéket használják [virtuálisgép-magok száma] szorzóként.
- A nyers használati órák határozzák meg, hogy mennyi idő alatt futnak a virtuális gépek a használati egységekben.

### <a name="metered-billing-usage"></a>Mért számlázási használat

A **mért használat** lap az ajánlati típusok használati adatait jeleníti meg, ahol a használat mérése a/mérési dimenzió alapján történik. Jelenleg az SaaS-ajánlat típusa meghaladja a következőt:. A lapon láthatók a túlterhelési trendek grafikus ábrázolása a SaaS által mért számlázási használat esetében:

- Túlhasználati **trend mérési dimenzió szerint**: az ajánlat kiválasztott mérési dimenziójához tartozó havi túlhasználati trendet jeleníti meg. Az X tengely a hónapot jelöli, és az Y tengely a túlhasználati felhasználás mennyiségét jelöli. Az egyéni fogyasztásmérő mértékegysége az Y tengelyen is megjelenik.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="A virtuális gép normalizált használati diagramjának ábrázolása a használati irányítópulton.":::

- Túlterhelési **trend SKU szerint**: a kiválasztott mérési dimenzió használati mennyiségének a SKU/Plans alapján történő használatának tendenciáját jelöli. Megjelenik az első öt csomag a kiválasztott ajánlat legmagasabb használati szintjével.

- **Többek közti trend az ügyfelektől**: a vásárlói Tanács a legmagasabb használati időt használó ügyfelek halmozott listáját jeleníti meg, és a _ranglistán_ jelenik meg, amelyet az egyéni fogyasztásmérő legmagasabb használata rangsorol. Válasszon ki egy ügyfelet a ranglistán a kiválasztott mérési dimenzió túlhasználati trendjeinak megtekintéséhez.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="A mért használatot mutatja az ügyfelek diagramon a használati irányítópulton.":::

Ha több egyéni mérőszámot használó ajánlata van, a mért számlázási használati jelentés az összes ajánlat használati adatait az egyéni mérési méretek alapján jeleníti meg.

> [!TIP]
> Az adatletöltéshez használhatja a letöltés ikont bármelyik widget jobb felső sarkában. Az egyes widgetekkel kapcsolatos visszajelzéseket a "hüvelykujj fel" vagy a "hüvelykujj lefelé" ikonra kattintva teheti meg.

## <a name="next-steps"></a>Következő lépések

- A kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [hozzáférés analitikus jelentések a kereskedelmi piactérhez a partner Centerben](./partner-center-portal/analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- További információ a rendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactéren – elemzés](./orders-dashboard.md)
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](usage-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./partner-center-portal/downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a Microsoft AppSource-on található ajánlatok felhasználói visszajelzésének összevont nézetét, tekintse meg a következő témakört: [ratings & Reviews Analytics](./partner-center-portal/ratings-reviews.md)
- A kereskedelmi piactér elemzésével és az adatkifejezések átfogó szótárával kapcsolatos gyakori kérdésekért lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).
