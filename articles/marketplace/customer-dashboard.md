---
title: Ügyfelek irányítópult a Microsoft kereskedelmi piactéren a partner Centerben, az Azure Marketplace-en és a Microsoft AppSource
description: Megtudhatja, hogyan érheti el az ügyfelekkel kapcsolatos információkat, beleértve a növekedési trendeket, a kereskedelmi piactér elemzésében az ügyfelek irányítópultjának használatával.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c5c50787ef3e287d164e051ece26da4e83199d47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555635"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Az ügyfelek irányítópultja a kereskedelmi piactér elemzésében

Ez a cikk a partner Center ügyfelek irányítópultján tartalmaz információkat. Ez az irányítópult az ügyfelekkel kapcsolatos információkat jeleníti meg, beleértve a növekedési trendeket, amelyek grafikus és letölthető formátumban jelennek meg.

Ha az ügyfelek irányítópultot szeretné elérni a partner Centerben, a **kereskedelmi piactér** területen válassza az **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **ügyfelek** elemzése lehetőséget.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).

## <a name="customers-dashboard"></a>Ügyfelek irányítópultja

A Customers (ügyfelek) irányítópulton az ajánlatokat beszerzett ügyfelek adatai jelennek meg. A következő elemek grafikus ábrázolásait tekintheti meg:

- Aktív és átadott ügyfelek trendje
- Ügyfelek növekedési trendje, beleértve a meglévő, az új és a megjelenő ügyfeleket
- Ügyfelek megrendelés és használat szerint
- Ügyfelek százalékos értéke 
- Ügyfél típusa megrendelések és használat szerint
- Ügyfelek földrajz szerint
- Ügyfelek adatai tábla
- Ügyfelek oldal szűrői

> [!NOTE]
> A partneri központban az ügyfelek beszerzése és a jelentéskészítés közötti maximális késés 48 óra.

## <a name="elements-of-the-customers-dashboard"></a>Az ügyfelek irányítópultjának elemei

Az alábbi szakaszok azt ismertetik, hogyan használható az ügyfelek irányítópultja, és hogyan lehet beolvasni az adatelemzést.

### <a name="month-range"></a>Hónap tartománya

Az egyes lapok jobb felső sarkában megtalálhatja a hónapok tartományának kijelölését. Szabja testre az **ügyfelek** oldal gráfok kimenetét az elmúlt 6 vagy 12 hónap alapján, vagy válasszon ki egy olyan egyéni hónapot, amelynek maximális időtartama 12 hónap. Az alapértelmezett hónap tartománya (számítási időszak) hat hónap.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="A hónap szűrőit mutatja be az ügyfelek lapon.":::

> [!NOTE]
> A vizualizációs widgetek és exportálási jelentések összes mérőszáma a felhasználó által kiválasztott számítási időszakot tiszteletben tartja.

### <a name="active-and-churned-customers-trend"></a>Aktív és átadott ügyfelek trendje

Ebben a szakaszban megtalálhatja az ügyfelek növekedési trendjét a kiválasztott számítási időszakra vonatkozóan. A mérőszámokat és a növekedési trendeket egy vonalas diagram jelképezi, és az egyes hónapok értékét a diagramon a vonal fölé húzva jeleníti meg. A widget **aktív ügyfelei** alatti százalékos érték a kiválasztott számítási időszak növekedésének mértékét jelöli. Az alábbi képernyőkép például a kiválasztott számítási időszakra vonatkozóan 0,92%-os növekedést mutat.

[![A Customers widget bemutatása az ügyfelek lapon.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Három ügyfél- _típus_ létezik: új, meglévő és átvesző.

- Egy új ügyfél egy vagy több ajánlatát a kiválasztott hónapban első alkalommal szerezte be.
- Egy meglévő ügyfél a kiválasztott hónap előtt legalább egy ajánlatot beszerzett.
- Egy megszakított ügyfél megszakította az összes korábban megvásárolt ajánlatot. A felkapott ügyfelek a trend widget negatív tengelyén jelennek meg.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Ügyfelek növekedési trendje, beleértve a meglévő, az új és a megjelenő ügyfeleket

Ebben a szakaszban az összes ügyfél, köztük az új, a meglévő és a megszakította tendenciáit és számát fogja találni, havi növekedési trendekkel.

- A vonalas diagram az ügyfelek teljes növekedési arányát jelöli.
- A hónap oszlop az új, meglévő és átvett ügyfelek által halmozott ügyfelek számát jelöli.
- Az átvett ügyfelek száma az X tengely negatív irányában jelenik meg.
- Az egyes jelmagyarázat-elemek kiválasztásával részletesebb nézeteket jeleníthet meg. Válassza például az új ügyfelek lehetőséget a jelmagyarázatban, hogy csak az új ügyfeleket jelenítse meg.
- A diagram egy oszlopára mutató egérmutató csak a hónap részleteit jeleníti meg.

[![A Customers (ügyfelek) trend widget bemutatása az ügyfelek lapon.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Ügyfelek rendelés/használat szerint

A **megrendelések/használati diagramok ügyfeleinek** három lapja van: megrendelések, normalizált használat és nyers használat. Válassza a **megrendelések** fület a rendelés részleteinek megjelenítéséhez.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Az ügyfelek lapon található megrendelések és használati widget alapján mutatja be az ügyfelek orders (megrendelések) lapját.":::

Vegye figyelembe a következőket:

- A vezető testület a megrendelések száma alapján rangsorolja az ügyfelek részleteit. Az ügyfél kiválasztása után az ügyfél adatai a "Részletek", az "Orders by SKUs" és a "SKU by seats" szakaszban jelennek meg.
- Az ügyfél profiljának adatai ezen a helyen jelennek meg, amikor a közzétevők egy tulajdonosi szerepkörrel jelentkeznek be. Ha a közzétevők közreműködő szerepkörrel jelentkeznek be, az ebben a szakaszban található részletek nem lesznek elérhetők.
- Az SKU-ra vonatkozó **rendelések alapján** megjelenő megrendelések a csomagok esetében megvásárolt megrendelések részletezését jelenítik meg. Megjelenik az első öt csomag a legmagasabb megrendelésekkel, míg a többi megrendelés az **összes Rest** alatt van csoportosítva.
- Az **SKUs by seats** fánk-diagram megjeleníti a csomagok számára rendezett helyek részletezését. Az első öt terv a legmagasabb számú hellyel jelenik meg, míg a többi megrendelés az **összes Rest** alatt van csoportosítva.

A használati adatok megtekintéséhez a **normalizált használat** vagy a **nyers használat** lapot is kiválaszthatja.

- A vezető testület a használati órák száma alapján rangsorolja az ügyfelek részleteit. Miután kiválasztotta az ügyfelet, az ügyfél adatai a szomszédos "Részletek", a "normalizált használati ajánlatok" és a "virtuális gép (VM) mérete (VM)" szakasz alapján jelennek meg.
- Az ügyfél profiljának adatai ezen a helyen jelennek meg, amikor a közzétevők egy tulajdonosi szerepkörrel vannak bejelentkezve. Ha a közzétevők közreműködői szerepkörrel vannak bejelentkezve, az ebben a szakaszban található részletek nem lesznek elérhetők.
- Az **ajánlatok által használt normalizált használat** a fánk diagramon az ajánlatok által felhasznált használat részletezését jeleníti meg. Az első öt csomag a legmagasabb kihasználtsági számmal jelenik meg, míg a további ajánlatok az **összes Rest** alatt vannak csoportosítva.
- A virtuálisgép **-méret (ek) alapján történő normalizált használat** a különböző virtuálisgép-méretek által felhasznált használat részletezését jeleníti meg. Megjelenik az első öt virtuálisgép-méret a legmagasabb normalizált használattal, míg a használat többi része az **összes Rest** elem alatt van csoportosítva.

### <a name="top-customers-percentile"></a>Legfontosabb ügyfelek százalékos értéke

A **legfontosabb ügyfelek percentilis** -diagram három fület tartalmaz: "Orders", "normalizált használat" és "nyers használat". A _Top Customer percentilis_ az x tengely mentén jelenik meg, a megrendelések száma alapján. Az y tengely megjeleníti az ügyfél rendelésének darabszámát. A másodlagos y tengely (vonalas gráf) megjeleníti a megrendelések teljes számának összesített százalékos arányát. A részletek megjelenítéséhez vigye az egérmutatót a vonalak fölé a diagram mentén.

[![A Top Customer percentilis widget orders (megrendelések) lapját mutatja az ügyfelek lapon.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Ügyfél típusa megrendelések és használat szerint

Az **ügyfél típusa diagram megrendelések/használat szerint** megjeleníti a megrendelések számát, a normalizált használatot és a nyers használati időt, amely az új ügyfelek és a meglévő ügyfelek között oszlik meg. a diagramon a megrendelések, a normalizált és a nyers használat alapján.

Ez a diagram a következőket mutatja be:

- Egy új ügyfél egy vagy több ajánlatát vagy jelentett használatát első alkalommal, ugyanazon a naptári hónapban (y tengely) belül szerezte be.
- Egy meglévő ügyfél korábban az y tengelyen jelentett naptári hónap előtt ajánlatot kapott vagy jelentett a használatból.

[![Az ügyfelek lapon megjelenő Orders by Customer Type widget orders (megrendelések) lapját mutatja.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Ügyfelek földrajz szerint

A kiválasztott számítási időszakra vonatkozóan a hő megjeleníti az ügyfelek teljes számát, valamint az újonnan hozzáadott ügyfelek százalékos arányát a földrajz dimenzióval. A Térkép világos és sötét színe az ügyfelek számának alacsony és magas értékét jelöli. Egy adott országban vagy régióban való nagyításhoz válasszon ki egy rekordot a táblában.

[![A Orders by földrajz widget orders (megrendelések) lapját mutatja az ügyfelek lapon.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Vegye figyelembe a következőket:

- A térképet áthelyezve megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hő egy kiegészítő rácstal rendelkezik, amely az adott helyen megtekintheti az ügyfelek számát, a rendelések számát, a normalizált használati időt.
- A rácson megkeresheti és kiválaszthatja az országot/régiót a térképen való nagyításhoz. A Térkép **Kezdőlap** gombjára kattintva térjen át az eredeti nézetre.

### <a name="customer-details-table"></a>Ügyfél részletei tábla

A **Customer Details (ügyfél részletei** ) tábla a legnépszerűbb 1 000-ügyfelek számozott listáját jeleníti meg, amelyet az egyik ajánlatának első beszerzése után rendeztek. Az egyes szakaszok kibontásához válassza a bővítés ikont a részletek menüszalagon.

Vegye figyelembe a következőket:

- Az ügyfél személyes adatai csak akkor lesznek elérhetők, ha az ügyfél beleegyezett. Ezeket az adatokat csak akkor tekintheti meg, ha a tulajdonosi szerepkör jogosultsági szintjével jelentkezett be.
- A rács minden oszlopa rendezhető.
- A rendszer kinyeri az adathalmazt. CSV vagy. TSV-fájl, ha a rekordok száma kisebb, mint 1 000.
- Ha a rekordok száma meghaladja a 1 000-et, az exportált adatok aszinkron módon lesznek elhelyezve a letöltési oldalon a következő 30 napban.
- Alkalmazzon szűrőket a táblára, hogy csak az Önt érdeklő információk jelenjenek meg. Adatok szűrése a vállalat neve, az ügyfél azonosítója, a piactér-előfizetés azonosítója, az Azure-licenc típusa, a beszerzett idő, a dátum elvesztése, az ügyfél e-mail-címe, az ügyfél országa/régiója/állam/város/zip, az ügyfél nyelve
- Ha egy védett ügyfél vásárolja meg az ajánlatot, a rendszer maszkba veszi az **ügyfelek részletes adatait** (* * * * * * * * * * * * *).
- Az ügyfél dimenziójának részletei, például a vállalat neve, az ügyfél neve és az ügyfél e-mail-címe a szervezet azonosító szintjén, nem az Azure Marketplace-en vagy Microsoft AppSource tranzakciós szinten.

_**1. táblázat: az adatszótár kifejezése**_

| Oszlop neve a (z)<br>felhasználói felület | Attribútum neve | Definíció | A programozott oszlop neve<br>hozzáférési jelentések |
| ------------ | ------------- | ------------- | ------------- |
| Piactér-előfizetés azonosítója | Piactér-előfizetés azonosítója | Az Azure-előfizetéshez társított egyedi azonosító, amelyet az ügyfél a kereskedelmi Piactéri ajánlat megvásárlásához használt. Az infrastruktúra-ajánlatok esetében ez az ügyfél Azure-előfizetési GUID-azonosítója. Az SaaS-ajánlatok esetében ez nulla értékként jelenik meg, mivel az SaaS-vásárlásokhoz nincs szükség Azure-előfizetésre. | MarketplaceSubscriptionId |
| DateAcquired | Beszerzés dátuma | Az első dátum, amikor az ügyfél megvásárolta a közzétett ajánlatot. | DateAcquired |
| DateLost | Elveszett dátum | Az utolsó időpont, amikor az ügyfél megszakította az utolsó összes korábban megvásárolt ajánlatot. | DateLost |
| Szolgáltató neve | Szolgáltató neve | A Microsoft és az ügyfél közötti kapcsolatban részt vevő szolgáltató neve. Ha az ügyfél a viszonteladón keresztüli vállalat, akkor ez lesz a viszonteladó. Ha egy felhőalapú megoldás-szolgáltató (CSP) is részt vesz, ez lesz a CSP. | ProviderName |
| Szolgáltatói E-mail cím | Szolgáltatói E-mail cím | A Microsoft és az ügyfél közötti kapcsolatban részt vevő szolgáltató e-mail-címe. Ha az ügyfél a viszonteladón keresztüli vállalat, akkor ez lesz a viszonteladó. Ha egy felhőalapú megoldás-szolgáltató (CSP) is részt vesz, ez lesz a CSP. | ProviderEmail |
| FirstName | Ügyfél utóneve | Az ügyfél által megadott keresztnév. A név nem lehet azonos az ügyfél Azure-előfizetésében megadott névvel. | FirstName |
| LastName | Ügyfél vezetékneve | Az ügyfél által megadott utolsó név. A név nem lehet azonos az ügyfél Azure-előfizetésében megadott névvel. | LastName |
| E-mail | Ügyfél e-mail-címe | A végfelhasználó által megadott e-mail-cím. Az e-mail nem lehet az ügyfél Azure-előfizetésében szereplő e-mail-cím. | E-mail |
| Ügyfél vállalatának neve | Ügyfél vállalatának neve | Az ügyfél által megadott cég neve. A név nem lehet más, mint az ügyfél Azure-előfizetésének városa. | CustomerCompany neve |
| CustomerCity | Customer City | Az ügyfél által megadott város neve. A város eltérő lehet az ügyfél Azure-előfizetésében lévő várostól. | CustomerCity |
| Ügyfél postai irányítószáma | Ügyfél postai irányítószáma | Az ügyfél által megadott postai irányítószám. A kód különbözhet az ügyfél Azure-előfizetésében megadott postai kóddal. | CustomerPostal-kód |
| CustomerCommunicationCulture | Ügyfél-kommunikációs nyelv | Az ügyfél által a kommunikációhoz előnyben részesített nyelv. | CustomerCommunicationCulture |
| CustomerCountryRegion | Ügyfél országa/régiója | Az ügyfél által megadott ország/régió neve. Az ország/régió eltérő lehet az ügyfél Azure-előfizetésében lévő országgal vagy régióval. | CustomerCountryRegion |
| AzureLicenseType | Azure-licenc típusa | Az ügyfelek által az Azure megvásárlásához használt licencelési szerződés típusa. Más néven _csatorna_. Lehetséges értékek:<ul><li>Felhőszolgáltató</li><li>Enterprise</li><li>Enterprise a viszonteladón keresztül</li><li>Utólagos fizetés</li></ul> | AzureLicenseType |
| PromotionalCustomers | A promóciós kapcsolatfelvételi lehetőség | Az érték tudatja Önnel, hogy az ügyfél proaktív módon választotta-e a közzétevők számára a promóciós kapcsolatfelvételt. Jelenleg nem vesszük igénybe az ügyfeleket, ezért a táblán a "nem" jelölés szerepel. A szolgáltatás üzembe helyezése után a frissítés ennek megfelelően kezdődik. | PromotionalCustomers |
| CustomerState | Ügyfél állapota | Az ügyfél által biztosított tartózkodási állapot. Az állapot különbözhet az ügyfél Azure-előfizetésében megadott állapottól. | CustomerState |
| CommerceRootCustomer | Kereskedelmi gyökér ügyfél | Egy számlázási fiókazonosító több ügyfél-azonosítóval is társítható.<br>A Számlázási fiók AZONOSÍTÓjának és ügyfél-AZONOSÍTÓjának egy kombinációja több kereskedelmi piactér-előfizetéshez is társítható.<br>A kereskedelmi gyökér ügyfél az előfizetés ügyfelének nevét jelzi. | CommerceRootCustomer |
| Ügyfél-azonosító | Ügyfél-azonosító | Az ügyfélhez rendelt egyedi azonosító. Előfordulhat, hogy egy ügyfél nulla vagy több Azure Marketplace-előfizetéssel rendelkezik. | CustomerId |
| Számlázási fiók azonosítója | Számlázási fiók azonosítója | Annak a fióknak az azonosítója, amelyre a számlázást létrehozták. A **Számlázási fiók azonosítójának** hozzárendelése a **Vevőkódhez** a kifizetési tranzakciós jelentés összekapcsolásához az ügyfél, a megrendelés és a használati jelentések segítségével. | BillingAccountId |
|||||

### <a name="customers-page-filters"></a>Ügyfelek oldal szűrői

Az ügyfelek oldal szűrői az ügyfelek oldal szintjén lesznek alkalmazva. Több szűrőt is kiválaszthat, hogy a diagramot a megtekinteni kívánt feltételekhez és a "részletes megrendelések adattípusa" rács/exportáláshoz tartozó információk megjelenítéséhez. A szűrőket az ügyfelek oldal jobb felső sarkában kiválasztott hónapra kinyert adatmennyiségre alkalmazza a rendszer.

> [!TIP]
> Az adatletöltéshez használhatja a letöltés ikont bármelyik widget jobb felső sarkában. Az egyes widgetekkel kapcsolatos visszajelzéseket a "hüvelykujj fel" vagy a "hüvelykujj lefelé" ikonra kattintva teheti meg.

## <a name="next-steps"></a>Következő lépések

- A kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [hozzáférés analitikus jelentések a kereskedelmi piactérhez a partner Centerben](./partner-center-portal/analytics.md).
- Az ajánlathoz tartozó Piactéri tevékenységet összefoglaló összesített adatok grafikonok, trendek és értékek esetében lásd: [az összefoglaló irányítópult a kereskedelmi piactér elemzésében](./summary-dashboard.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](./orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./partner-center-portal/downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a Microsoft AppSource-on található ajánlatok felhasználói visszajelzésének összevont nézetét, tekintse meg a következő témakört: [ratings & Reviews Analytics](./partner-center-portal/ratings-reviews.md)
- A kereskedelmi piactér elemzésével és az adatkifejezések átfogó szótárával kapcsolatos gyakori kérdésekért lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).
