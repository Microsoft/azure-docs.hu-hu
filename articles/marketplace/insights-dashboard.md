---
title: A Marketplace Insights irányítópult a kereskedelmi piactér-elemzésekben
description: Hozzáférés a piactér webes elemzési szolgáltatásához a partner Centerben, amely lehetővé teszi az ügyfelek részvételének mérését Microsoft AppSource és az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: a8a53a54d30df9a2f7079d705f754e554328c3f7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553935"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>A Marketplace Insights irányítópult a kereskedelmi piactér-elemzésekben

Ez a cikk a partner Center Piactéri információk irányítópultján nyújt információt. Ez az irányítópult a kereskedelmi piactér webes elemzési szolgáltatásának összegzését jeleníti meg, amely lehetővé teszi a kiadók számára, hogy a kereskedelmi piactér online üzleteiben (Microsoft AppSource és az Azure Marketplace-en) felsorolt termék részletes oldalain mérjék fel az ügyfelek engagement

Ha a **piactéren elérhető Piactéri** irányítópultot szeretné elérni a partner Centerben, a kereskedelmi piactér területen válassza a Marketplace- **[elemzések elemzése](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** lehetőséget  >  .

Az analitikai terminológia részletes leírását lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Marketplace Insights irányítópult

A Piactéri információk irányítópultja áttekintést nyújt az Azure Marketplace-ről és a AppSource üzleti teljesítményéről. Az irányítópult átfogó áttekintést nyújt a következőkről:

- Oldal látogatói trend
- Műveletek trendének meghívása
- Oldalak meglátogatása és az ajánlatok, a hivatkozó tartományok és a kampány-azonosítók közötti műveletek meghívása
- Marketplace-bepillantást földrajz szerint
- Marketplace-információk részleteinek táblázata

A Piactéri elemzések irányítópultja olyan kattintássorozat-adatforrásokat biztosít, amelyek nem korrelálnak a vezető célként megadott végponton létrehozott érdeklődőkkel.

> [!NOTE]
> Az Azure Marketplace-en és a AppSource-on keresztül meglátogatott felhasználók közötti maximális késés 48 óra.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>A Marketplace-beli ismeretek irányítópultjának elemei

A Piactéri információk irányítópultja az Azure Marketplace és a AppSource webes telemetria adatait két külön lapon jeleníti meg. A következő szakaszok azt ismertetik, hogyan használható a Marketplace elemzések irányítópultja, és hogyan lehet beolvasni az adatolvasást.

### <a name="month-range"></a>Hónap tartománya

Az egyes lapok jobb felső sarkában megtalálhatja a hónapok tartományának kijelölését. Az elmúlt 6 vagy 12 hónapon alapuló havi tartomány kiválasztásával testre szabhatja a Marketplace-beli **adatáttekintési** oldal diagramjainak kimenetét, vagy kiválaszthat egy legfeljebb 12 hónapos időtartamú egyéni hónapot. Az alapértelmezett hónap tartománya (számítási időszak) hat hónap.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="A havi szűrők ábrázolása a Marketplace-beli adatáttekintések irányítópultján.":::

> [!NOTE]
> A vizualizációs widgetek és exportálási jelentések összes mérőszáma a felhasználó által kiválasztott számítási időszakot tiszteletben tartja.

### <a name="page-visits-trends"></a>Oldal látogatói trendek

A Piactéri információk **látogatói** diagramon a kiválasztott számítási időszakra vonatkozó _látogatások_ és _egyedi látogatók_ száma látható.

**Oldalak meglátogatása**: Ez a szám a kiválasztott számítási időszakra vonatkozó ajánlati lista lapon (a termék részletes adatai oldalon) lévő különböző felhasználói munkamenetek számát jelöli. A piros és a zöld százalékos mutatók az oldalak látogatottságának százalékos arányát jelölik. A trend diagram a látogatások hónap és hónap közötti számát jelöli.

**Egyedi látogatók**: Ez a szám az Azure Marketplace-en és a AppSource található ajánlat (ok) esetében a kiválasztott számítási időszakban a különböző látogatók számát jelöli. Egy vagy több termékspecifikus oldalt felkereső látogató egy egyedi látogatónak számít.

[![A Piactéri eredmények irányítópultjának látogatói diagramját mutatja be.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Műveletek trendének meghívása

Ez a szám azt jelzi, **hogy a művelet hívása** gombra kattintások száma az ajánlati lista lapon (a termék részletes lapján) befejeződött. A _művelet meghívása_ akkor számít, ha a felhasználók kiválasztják a letöltés **most**, az **ingyenes próbaverzió**, a **Kapcsolatfelvétel** vagy a **Test Drive** gombokat.

[![A Piactéri elemzések irányítópultján a műveleti diagram hívása.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Oldalak meglátogatása és az ajánlatok, a hivatkozó tartományok és a kampány-azonosítók közötti műveletek meghívása

**Átirányítási tartományok**: egy adott hivatkozó tartomány kiválasztásakor a diagramon a jobb oldalon megjelenő látogatások és hívások havi trendje látható.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Az átirányítási tartomány diagramját mutatja be a Marketplace-alapú adatáttekintési irányítópulton.":::

**Ajánlatok**: válasszon ki egy adott ajánlatot, hogy megtekintse a látogatások havi trendjét, valamint a diagramon a jobb oldalon végrehajtandó műveletek meghívását.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Az ajánlat-alias diagramot mutatja be a Marketplace-adatáttekintési irányítópulton.":::

**Kampány-azonosítók**: egy adott kampány azonosítójának kiválasztásával megismerheti a kampány sikerességét. Minden kampánynál láthatja, hogy az oldal látogatói és a meghívások havi trendje látható a diagramon a jobb oldalon.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="A Marketplace-beli elemzése irányítópultjának kampány diagramját mutatja be.":::

### <a name="marketplace-insights-by-geography"></a>Marketplace-bepillantást földrajz szerint

A kiválasztott számítási időszakra vonatkozóan a hő megjeleníti a látogatások, az egyedi látogatók és a művelet meghívása (CTA) számát. A Térkép világos és sötét színe az egyedi látogatók alacsony és magas értékét jelöli. Válasszon ki egy rekordot a táblában az ország/régió nagyításához.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="A Piactéri adatáttekintési irányítópulton ábrázolja a földrajzi oldalpár diagramot.":::

Vegye figyelembe a következőket:

- A térképet áthelyezve megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hő egy kiegészítő rácstal rendelkezik, amely az adott helyen megtekintheti az ügyfelek számát, a megrendelések számát és a normalizált használati időt.
- A rácson megkeresheti és kiválaszthatja az országot/régiót a térképen való nagyításhoz. A Térkép **Kezdőlap** gombjára kattintva térjen át az eredeti nézetre.

### <a name="marketplace-insights-details-table"></a>Marketplace-információk részleteinek táblázata

Ez a táblázat a meglátogatott oldalak megtekintését, valamint a kiválasztott ajánlatokban szereplő lapok dátum szerint rendezve történő meghívását tartalmazza.

- A rendszer kinyeri az adathalmazt. TSV vagy. CSV-fájl, ha a rekordok száma kisebb, mint 1 000.
- Ha a rekordok száma meghaladja a 1 000-et, az exportált adatok aszinkron módon lesznek elhelyezve a letöltési oldalon a következő 30 napban.
- Az információk az ajánlatok nevei és a kampányok neve alapján szűrhetők, hogy megjelenjenek az Önt érdeklő információk.

> [!TIP]
> Az adatletöltéshez használhatja a letöltés ikont bármelyik widget jobb felső sarkában. Az egyes widgetekkel kapcsolatos visszajelzéseket a "hüvelykujj fel" vagy a "hüvelykujj lefelé" ikonra kattintva teheti meg.

| Oszlop neve a (z)<br>felhasználói felület | Attribútum neve | Definíció | A programozott oszlop neve<br>hozzáférési jelentések |
| ------------ | ------------- | ------------- | ------------- |
| Date | Látogatás dátuma | A lap látogatásának és/vagy CTA-nak az esemény létrehozása az Azure Marketplace piactéren és/vagy a AppSource. | Date |
| Offer Name | Offer Name | A kereskedelmi Piactéri ajánlat neve. | OfferName |
| Hivatkozó tartomány | Hivatkozó tartomány | Annak az átirányítási tartománynak a neve, ahonnan az oldal meglátogatása megtörtént. Ha az oldal felkereséséhez nincsenek beléptetési tartományok rögzítve, akkor a megfelelő bejegyzés "hivatkozó tartomány nincs jelen". |  ReferralDomain |
| Ország neve | Ország neve | Annak az országnak a neve, ahonnan az oldal meglátogatása megtörtént. | CountryName |
| Oldalak meglátogatása | Oldalak meglátogatása | Az adott dátumhoz tartozó ajánlat nevéhez társított oldal-látogatások száma. | PageVisits |
| Letöltés most | Letöltés most | Az ajánlat oldalának "Letöltés most" CTA-ra való kattintások száma egy adott dátumra vonatkozóan. | GetItNow |
| Kapcsolatfelvétel | Kapcsolatfelvétel | A "Contact me" CTA-ra való kattintások száma az ajánlat oldalán egy adott dátumra vonatkozóan. | ContactMe |
| Tesztverzió | Tesztverzió | A "tesztvezetés" CTA-re való kattintások száma az ajánlat lapján egy adott dátumra vonatkozóan. | TestDrive |
| Ingyenes próba | Ingyenes próba | Az "ingyenes próbaverzió" CTA-ra való kattintások száma az ajánlat oldalán egy adott dátumra vonatkozóan. | FreeTrial |
|||||

## <a name="next-steps"></a>Következő lépések

- A kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [hozzáférés analitikus jelentések a kereskedelmi piactérhez a partner Centerben](./partner-center-portal/analytics.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](./orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](./usage-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](./customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./partner-center-portal/downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource-on található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a következőt: [ratings & Reviews](./partner-center-portal/ratings-reviews.md)
- A kereskedelmi piactér elemzésével és az adatkifejezések átfogó szótárával kapcsolatos gyakori kérdésekért lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).