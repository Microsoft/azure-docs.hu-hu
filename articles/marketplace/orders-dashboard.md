---
title: A partner Center Orders irányítópultja a kereskedelmi piactér elemzésében | Microsoft AppSource és az Azure Marketplace
description: Megtudhatja, hogyan érheti el a kereskedelmi Piactéri ajánlatokra vonatkozó analitikai jelentéseket grafikus és letölthető formában.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: ed12e470f3f3d8c1035c1e4e2e0fa7a3b33e2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561381"
---
# <a name="orders-dashboard-in-commercial-marketplace-analytics"></a>A Rendelések irányítópult a kereskedelmi piactér-elemzésekben

Ez a cikk a partner Center orders (megrendelések) irányítópultján tartalmaz információkat. Ez az irányítópult a rendelésekkel kapcsolatos információkat jeleníti meg, beleértve a növekedési trendeket, amelyek grafikus és letölthető formátumban jelennek meg.

Ha az Orders (megrendelések) irányítópultot szeretné elérni a partner Centerben, **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** a **kereskedelmi piactér** területen válassza a  >  **megrendelések** elemzése lehetőséget.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).

## <a name="orders-dashboard"></a>Rendelések irányítópult

Az Orders (megrendelések) irányítópult az összes szolgáltatásként kínált szoftver (SaaS) aktuális rendeléseit jeleníti meg. A következő elemek grafikus ábrázolásait tekintheti meg:

- Megrendelések trend
- Orders/Seat és site trend
- Megrendelések ajánlatok és SKU-i szerint
- Megrendelések földrajz szerint
- Részletes megrendelések táblázata
- Megrendelések oldal szűrőinek

> [!NOTE]
> A partneri központban az ügyfelek beszerzése és a jelentéskészítés közötti maximális késés 48 óra.

## <a name="elements-of-the-orders-dashboard"></a>A megrendelések irányítópultjának elemei

A következő szakaszok azt ismertetik, hogyan használhatja a megrendelések irányítópultot, és hogyan olvashatja el az adatelemzést.

### <a name="month-range"></a>Hónap tartománya

Az egyes lapok jobb felső sarkában megtalálhatja a hónapok tartományának kijelölését. Szabja testre a **rendelések** oldal gráfok kimenetét úgy, hogy kijelöl egy hónap tartományt az elmúlt 6 vagy 12 hónap alapján, vagy egy olyan egyéni hónap tartományt választ, amelynek maximális időtartama 12 hónap. Az alapértelmezett hónap tartománya (számítási időszak) hat hónap.

:::image type="content" source="./media/orders-dashboard/month-filters.png" alt-text="Az Orders (megrendelések) irányítópult hónap szűrőinek ábrázolása.":::

> [!NOTE]
> A vizualizációs widgetek és exportálási jelentések összes mérőszáma a felhasználó által kiválasztott számítási időszakot tiszteletben tartja.

### <a name="orders-trend"></a>Megrendelések trend

Ebben a szakaszban megtalálja a **megrendelések** diagramot, amely megjeleníti az aktív és a megszakított megrendelések trendjét a kiválasztott számítási időszakra vonatkozóan. A mérőszámokat és a növekedési trendeket egy vonalas diagram jelképezi, és az egyes hónapok értékeit a diagramon a vonal fölé húzva jeleníti meg. A widget orders (megrendelések) mérőszáma alatti százalékos érték a kiválasztott számítási időszak növekedésének vagy csökkenésének mértékét jelzi.

Két Orders számláló van: _aktív_ és _megszakítva_.

- Az **aktív** érték megegyezik azon megrendelések számával, amelyeket az ügyfelek jelenleg használnak a kiválasztott dátumtartomány során.
- A **megszakított** érték megegyezik a korábban megvásárolt és a kijelölt dátumtartomány során megszakított megrendelések számával.

[![A megrendelések irányítópulton megjelenő orders (megrendelések) widget ábrázolása, amely az aktív és a megszakított megrendelések trendjét jeleníti meg.](./media/orders-dashboard/orders-trend.png)](./media/orders-dashboard/orders-trend.png#lightbox)

### <a name="orders-by-per-seat-and-site-trend"></a>Megrendelések munkaállomásonkénti és hely szerinti trend alapján

Az munkaállomásonkénti és a hely szerinti vonalas diagramon megjelenő **Orders** az ügyfelek által megvásárolt, a helyszínen SaaS-és az ügyfél által megvásárolt SaaS-megrendelések mérőszámát és trendjét jelenti (ez a diagram megszakított rendeléseket is tartalmaz).

[![A megrendelések irányítópulton megjelenő orders (megrendelések) widget ábrázolása, amely a rendeléseket és a helyek trendjét jeleníti meg.](./media/orders-dashboard/seats-sites.png)](./media/orders-dashboard/seats-sites.png#lightbox)

Az SaaS-ajánlatok a következő két díjszabási modell egyikét használhatják: átalánydíjas (hely-alapú) vagy felhasználónként (ülőhely-alapú).

- **Átalány**: az ajánlathoz való hozzáférés engedélyezése egyetlen havi vagy éves átalány-díjszabással. Ezt más néven a hely alapú díjszabásnak is nevezzük.
- **Felhasználónkénti**: az ajánlathoz való hozzáférés engedélyezése az ajánlathoz hozzáférő felhasználók számán vagy a foglalt ülőhelyek megtartásán alapuló díjszabás alapján. Ezzel a használaton alapuló modellel beállíthatja a csomag által támogatott felhasználók minimális és maximális számát. Több tervet is létrehozhat a különböző díjszabások konfigurálásához a felhasználók száma alapján. Ezeket a mezőket nem kötelező megadni. Ha nem jelöli be a jelölőnégyzetet, a rendszer a felhasználók számát úgy értelmezi, hogy nem rendelkezik korláttal (a szolgáltatás által támogatott szolgáltatások közül min. 1 és Max). Ezeket a mezőket a csomag frissítésének részeként lehet szerkeszteni.
- **Mért számlázás**: az átalány díjszabása alapján. Ezzel az árképzési modellel meghatározhat olyan mért csomagokat is, amelyek a Piactéri mérési szolgáltatás API-ját használják a nem az átalány által jelzett használati díjakra.

Az ülések, a helyek és a mért alapú számlázás további részleteiért lásd: [SaaS-ajánlat tervezése a kereskedelmi piactéren](plan-saas-offer.md).

### <a name="orders-by-offers-and-skus"></a>Megrendelések ajánlatok és SKU-i szerint

Az ajánlatok és SKU-diagramok szerinti megrendelések az összes ajánlat mértékeit és trendjét mutatják:

- A legfontosabb ajánlatok a gráfban jelennek meg, a többi ajánlat pedig az **összes Rest**-ként van csoportosítva.
- A jelmagyarázatban szereplő ajánlatok közül kiválaszthatja, hogy csak az ajánlat és a hozzá tartozó SKU jelenjen meg a gráfban.
- Ha a diagramon egy szelet fölé viszi az egérmutatót, a megrendelések száma és az ajánlat százalékos aránya az összes ajánlathoz képest teljes számú rendelésre hasonlít.
- A **Orders by ajánlatok trend** a havi havi növekedési trendeket jeleníti meg. A hónap oszlop a megrendelések számát jelöli az ajánlat neve alapján. A diagram egy z-tengelyen ábrázolt százalékos növekedési arányt jeleníti meg.

:::image type="content" source="./media/orders-dashboard/orders-by-offers.png" alt-text="A Orders by ajánlatok diagramot ábrázolja az Orders (megrendelések) irányítópulton.":::

Az ajánlatra, a SKU-ra és a munkaállomásokra vonatkozó havi hónapot megtekintheti, és legfeljebb három SKU-t választhat ki.

### <a name="orders-by-geography"></a>Megrendelések földrajz szerint

A kiválasztott számítási időszakra vonatkozóan a hő megjeleníti a megrendelések teljes számát, valamint az újonnan hozzáadott megrendelések növekedési arányát egy földrajzi helyhez képest.  A Térkép világos és sötét színe az ügyfelek számának alacsony és magas értékét jelöli. Válasszon egy rekordot a táblában egy adott ország vagy régió nagyításához.

[![Az Orders (megrendelések) irányítópulton ábrázolja a földrajzi oldalpár diagramot.](./media/orders-dashboard/geographical-spread.png)](./media/orders-dashboard/geographical-spread.png#lightbox)

Vegye figyelembe a következőket:

- A térképet áthelyezve megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hő egy kiegészítő rácstal rendelkezik, amely az adott helyen megtekintheti az ügyfelek számát, a megrendelések számát és a normalizált használati időt.
- A rácson megkeresheti és kiválaszthatja az országot/régiót a térképen való nagyításhoz. A Térkép **Kezdőlap** gombjára kattintva térjen át az eredeti nézetre.

### <a name="orders-details-table"></a>Megrendelések részletei tábla

Az Order Details (megrendelés részletei) tábla a 1 000 Top orders (beszerzési dátum szerint rendezve) számozott listát jeleníti meg.

- A rács minden oszlopa rendezhető.
- A rendszer kinyeri az adathalmazt. CSV vagy. TSV-fájl, ha a rekordok száma kisebb, mint 1 000.
- Ha a 1 000-es számú rekordok száma, az exportált adatok aszinkron módon lesznek elhelyezve a letöltési oldalon a következő 30 napban.
- Alkalmazzon szűrőket az **Order details** táblára, hogy csak azokat az adatokat jelenítse meg, amelyekre kíváncsi. Szűrés ország/régió szerint, Azure-licenc típusa, kereskedelmi piactér licencelési típusa, ajánlat típusa, megrendelés állapota, ingyenes nyomvonalak, kereskedelmi piactér-előfizetés azonosítója, ügyfél-azonosító, és a vállalat neve.
- Ha a rendelést egy védett ügyfél vásárolja meg, a **megrendelések részletes adatainak** maszkolása (* * * * * * * * * * * *).

***1. táblázat: az adatszótár kifejezése***

| Oszlop neve a (z)<br>felhasználói felület | Attribútum neve | Definíció | A programozott oszlop neve<br>hozzáférési jelentések |
| ------------ | ------------- | ------------- | ------------- |
| Piactér-előfizetés azonosítója | Piactér-előfizetés azonosítója | Az Azure-előfizetéshez társított egyedi azonosító, amelyet az ügyfél a kereskedelmi Piactéri ajánlat megvásárlásához használt. Az infrastruktúra-ajánlatok esetében ez az ügyfél Azure-előfizetési GUID-azonosítója. Az SaaS-ajánlatok esetében ez nulla értékként jelenik meg, mivel az SaaS-vásárlásokhoz nincs szükség Azure-előfizetésre. | Piactér-előfizetés azonosítója |
| MonthStartDate | Hónap kezdő dátuma | A hónap kezdő dátuma a vásárlás hónapját jelöli. A formátum éééé-hh-nn. | MonthStartDate |
| Ajánlat típusa | Ajánlat típusa | A kereskedelmi piactér kínálatának típusa. | OfferType |
| Azure-licenc típusa | Azure-licenc típusa | Az ügyfelek által az Azure megvásárlásához használt licencelési szerződés típusa. Más néven csatorna. Lehetséges értékek:<ul><li>Felhőszolgáltató</li><li>Enterprise</li><li>Enterprise a viszonteladón keresztül</li><li>Utólagos fizetés</li></ul> | AzureLicenseType |
| Marketplace-licenc típusa | Marketplace-licenc típusa | A kereskedelmi Piactéri ajánlat számlázási módszere. A különböző értékek a következők:<ul><li>Az Azure-on keresztül számlázva</li><li>Saját licenc használata</li><li>Ingyenes</li><li>Microsoft mint viszonteladó</li></ul> | MarketplaceLicenseType |
| Termékváltozat | Termékváltozat | Az ajánlattal társított csomag | Termékváltozat |
| Ügyfél országa | Ügyfél országa/régiója | Az ügyfél által megadott ország/régió neve. Az ország/régió eltérő lehet az ügyfél Azure-előfizetésében lévő országgal vagy régióval. | CustomerCountry |
| Előzetes verziójú SKU | Előzetes verziójú SKU | Az érték tudatja Önnel, ha az SKU-t "Preview"-ként jelölte meg. Az érték "yes" lesz, ha az SKU-t ennek megfelelően címkézték, és csak az Ön által jóváhagyott Azure-előfizetések telepíthetők és használhatók. Az érték "nem" lesz, ha az SKU nem "Preview"-ként lett azonosítva. | IsPreviewSKU |
| Rendelésazonosító | Rendelésazonosító | A kereskedelmi piactér szolgáltatáshoz tartozó ügyfél-rendelés egyedi azonosítója. A virtuális gépek használaton alapuló ajánlatai nincsenek rendeléshez társítva. | OrderId |
| Megrendelés mennyisége | Megrendelés mennyisége | Az aktív rendelésekhez tartozó rendelés-AZONOSÍTÓhoz társított eszközök száma | OrderQuantity |
| Felhőbeli példány neve | Felhőbeli példány neve | Az a Microsoft Cloud, amelyben a virtuális gép üzembe helyezése megtörtént. | CloudInstanceName |
| Új ügyfél | Új ügyfél | Az érték azt határozza meg, hogy egy új ügyfél első alkalommal vásárolt-e egy vagy több ajánlatot. Az érték "yes" lesz, ha a "date beszerzett" dátummal megegyező naptári hónapon belül van. Az érték "nem" lesz, ha az ügyfél a jelentett naptári hónap előtt megvásárolta az ajánlatait. | IsNewCustomer |
| Megrendelés állapota | Megrendelés állapota | Egy kereskedelmi Piactéri megrendelés állapota az adatgyűjtés utolsó frissítésekor. | OrderStatus |
| Megrendelés megszakításának dátuma | Megrendelés megszakításának dátuma | Az a dátum, amikor a kereskedelmi Piactéri rendelést megszakították. | OrderCancelDate |
| Ügyfél vállalatának neve | Ügyfél vállalatának neve | Az ügyfél által megadott cég neve. A név nem lehet más, mint az ügyfél Azure-előfizetésének városa. | CustomerCompanyName |
| Megrendelés vásárlásának dátuma | Megrendelés vásárlásának dátuma | Az a dátum, amikor a kereskedelmi Piactéri rendelés létrejött. A formátum éééé-hh-nn. | OrderPurchaseDate |
| Offer Name | Offer Name | A kereskedelmi Piactéri ajánlat neve. | OfferName |
| Próbaverzió befejezési dátuma | Próbaverzió befejezési dátuma | Az a dátum, ameddig a rendelés próbaidőszaka lejár vagy véget ér. | TrialEndDate |
| Ügyfél-azonosító | Ügyfél-azonosító | Az ügyfélhez rendelt egyedi azonosító. Előfordulhat, hogy egy ügyfél nulla vagy több Azure Marketplace-előfizetéssel rendelkezik. | CustomerId |
| Számlázási fiók azonosítója | Számlázási fiók azonosítója | Annak a fióknak az azonosítója, amelyre a számlázást létrehozták. A **Számlázási fiók azonosítójának** hozzárendelése a **Vevőkódhez** a kifizetési tranzakciós jelentés összekapcsolásához az ügyfél, a megrendelés és a használati jelentések segítségével. | BillingAccountId |
| AssetCount | Eszközök száma | A megrendelési AZONOSÍTÓhoz társított eszközök száma. | Elavult |
|||||

### <a name="orders-page-filters"></a>Megrendelések oldal szűrőinek

Az **orders (megrendelések** ) oldal szűrői az Orders (megrendelések) lap szintjén lesznek alkalmazva. Kiválaszthat egy vagy több szűrőt a diagram megjelenítéséhez a megtekinteni kívánt feltételekhez, valamint a "részletes megrendelések adatrács/exportálás" területen megjelenítendő információk megjelenítéséhez. A szűrők a megrendelések oldal jobb felső sarkában kiválasztott hónapra kinyert adattartományon lesznek alkalmazva.

> [!TIP]
> Az adatletöltéshez használhatja a letöltés ikont bármelyik widget jobb felső sarkában. Az egyes widgetekkel kapcsolatos visszajelzéseket a "hüvelykujj fel" vagy a "hüvelykujj lefelé" ikonra kattintva teheti meg.

## <a name="next-steps"></a>Következő lépések

- A kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [hozzáférés analitikus jelentések a kereskedelmi piactérhez a partner Centerben](./partner-center-portal/analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./partner-center-portal/downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource-on található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a következőt: [ratings & Reviews](./partner-center-portal/ratings-reviews.md)
- A kereskedelmi piactér elemzésével és az adatkifejezések átfogó szótárával kapcsolatos gyakori kérdésekért lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).