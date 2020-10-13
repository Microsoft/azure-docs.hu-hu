---
title: Az egyéni Azure-előfizetéshez tartozó számla áttekintése
description: Megtudhatja, hogyan értelmezheti a számláját és erőforrás-használatát, és hogyan ellenőrizheti saját Azure-előfizetésének díjtételeit, beleértve a használatalapú fizetést is.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: banders
ms.openlocfilehash: 95af762e0ff1986f9d1395e787c73b3a886a7a2e
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2020
ms.locfileid: "91653281"
---
# <a name="tutorial-review-your-individual-azure-subscription-bill"></a>Oktatóanyag: Az egyéni Azure-előfizetéshez tartozó számla áttekintése

Ez a cikk segítséget nyújt a használatalapú fizetéses vagy a Visual Studio Azure-előfizetéséhez tartozó számlájának értelmezésében és áttekintésében, beleértve a használatalapú fizetést és a Visual Studiót is. Az egyes számlázási időszakok esetében általában e-mailben kapja meg a számlát. Ez a számla az Azure-számlájának felel meg. A számlán szereplő költségadatok elérhetők az Azure Portalon is. Ebben az oktatóanyagban össze fogja hasonlítani a számlát a részletes napi használati adatok fájljával és a költségelemzéssel az Azure Portalon.

Ez az oktatóanyag csak azokra az Azure-ügyfelekre vonatkozik, akik egyéni szerződéssel rendelkeznek. Az általános egyéni előfizetésekre használatalapú díjszabás vonatkozik, és közvetlenül az Azure webhelyén vásárolhatók meg.

Ha segítségre van szüksége a váratlan költségek értelmezéséhez: [Váratlan költségek elemzése](analyze-unexpected-charges.md). Ha le szeretné mondani Azure-előfizetését, tekintse meg [Az Azure-előfizetés lemondása](../manage/cancel-azure-subscription.md) című témakört.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A kiszámlázott díjtételek összehasonlítása a használati adatok fájljával
> * A kiszámlázott díjtételek összehasonlítása a költségelemzésben szereplő használati adatokkal

## <a name="prerequisites"></a>Előfeltételek

Fizetős számlázási *Microsoft Online Services Program*-fiókkal kell rendelkeznie. A fiók létrehozása az Azure webhelyén keresztül, az Azure-regisztráció alkalmával történik. Ilyen eset, ha rendelkezik [használatalapú fizetést használó fiókkal](https://azure.microsoft.com/offers/ms-azr-0003p/) vagy [Visual Studio-előfizető](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Az [ingyenes Azure-fiókokhoz](https://azure.microsoft.com/offers/ms-azr-0044p/) csak a havi kreditösszeg túllépése esetén készülnek számlák.

30 napnál több időnek kell eltelnie el az Azure-előfizetés életbe lépése óta. Az Azure a számlázási időszak végén küld Önnek számlát.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="compare-billed-charges-with-your-usage-file"></a>A kiszámlázott díjtételek összehasonlítása a használati adatok fájljával

<a name="charges"></a>

A használati adatok és a költségek összehasonlításának első lépése a számla és használati adatok fájljának letöltése. A részletes használatot tartalmazó CSV-fájl a számlázási időszak és a napi használat szerint jeleníti meg a díjakat. Ez nem tartalmazza az adóinformációkat. A fájlok letöltéséhez fiókadminisztrátornak kell lennie vagy Tulajdonos szerepkörrel kell rendelkeznie.

Az Azure Portalon írja be az *előfizetések* szöveget a keresőmezőbe, majd kattintson az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) elemre.

[![Lépjen az előfizetések területére](./media/review-individual-bill/navigate-subscriptions.png)](./media/review-individual-bill/navigate-subscriptions.png#lightbox)

Az előfizetések listájában kattintson rá egy előfizetésre.

A **Számlázás** területen kattintson a **Számlák** elemre.

A számlák listájában keresse meg a letölteni kívánt számlát, majd kattintson a letöltési szimbólumra. Lehetséges, hogy módosítania kell az időtartományt a régebbi számlák megtekintéséhez. A használati adatok fájljának és a számla létrehozása eltarthat néhány percig.

![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját ábrázoló képernyőkép](./media/review-individual-bill/download-invoice.png)

A Felhasználási adatok és díjak letöltése ablakban kattintson a **CSV letöltése** és a **Számla letöltése** elemre.

![Képernyőkép a Számlák és használati adatok letöltése oldalról](./media/review-individual-bill/usageandinvoice.png)

Ha a **Nem érhető el** üzenet jelenik meg, számos oka lehet annak, hogy nem jelenik meg a használati adatok fájlja vagy a számla:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.
- Az adott számlázási időszakhoz nem tartoznak használati adatok.
- A számla még nem lett létrehozva. Várja meg a számlázási időszak végét.
- Nem rendelkezik engedéllyel a számlák megtekintéséhez. Előfordulhat, hogy nem látja a régi számlákat, ha nem Ön a fiókadminisztrátor. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](../manage/manage-billing-access.md).
- Ha ingyenes próbaverziós előfizetéssel rendelkezik, vagy nem haladta meg az előfizetésében foglalt jóváírás havi keretét, csak abban az esetben fog számlát kapni, ha Microsoft-ügyfélszerződéssel rendelkezik.

A következő lépés a díjtételek áttekintése. A számlája az adókat és a használati díjakat tartalmazza.

![Példa Azure-számlára](./media/review-individual-bill/invoice-usage-charge.png)

Nyissa meg a letöltött, használati adatokat tartalmazó CSV-fájlt. A fájl végén adja össze az összes tétel értékét a *Költség* oszlopban.

![Példa összegzett költséget tartalmazó használati fájlra](./media/review-individual-bill/usage-file-usage-charges.png)

 Az összegzett *Költség* értéknek pontosan meg kell egyeznie a *használati díjak* esetében kiszámlázott költséggel.

A használati díjak a mérési szinten jelennek meg. A következő kifejezések a számlában és a részletes használati fájlban is ugyanazt a dolgot jelentik. A számlán fellelhető számlázási ciklus például megegyezik a részletes használati fájlban feltüntetett számlázási időszakkal.

| Számla (PDF) | Részletes használat (CSV)|
| --- | --- |
|Billing Cycle (Számlázási ciklus) | BillingPeriodStartDate (SzámlázásiIdőszakKezdőDátuma) BillingPeriodEndDate (SzámlázásiIdőszakZáróDátuma) |
|Name (Név) |Meter Category (Mérési kategória) |
|Típus |Meter Subcategory (Mérési alkategória) |
|Erőforrás |MeterName |
|Régió |MeterRegion |
|Consumed (Felhasznált mennyiség) | Mennyiség |
|Tartalmazza |Included Quantity (Bennefoglalt mennyiség) |
|Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |
|Rate (Egységár) | EffectivePrice|
| Érték | Költségek |

A számla **Használati díjak** szakasza a számlázási időszakban feltüntetett egyes mérőszámok teljes fogyasztási értékét (költségét) jeleníti meg. Az alábbi képen például az Azure Storage szolgáltatás *P10-lemezek* erőforrásra vonatkozó használati díja látható.

![A számlán feltüntetett használati díjak](./media/review-individual-bill/invoice-usage-charges.png)

A használati adatokat tartalmazó CSV-fájlban szűrjön a *MeterName* értékre a számlán szereplő megfelelő erőforrás esetében. Ezután adja össze az oszlop elemeinek *Költség* értékeit. Íme egy példa, amely a számlán szereplő sorelemnek megfelelő szolgáltatásmérő nevére (P10-lemezek) fókuszál.

![A használati fájl összegzett értéke a MeterName változó esetében](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Az összegzett *Költség* értéknek pontosan meg kell egyeznie az egyedi erőforrás *használati díjai* esetében kiszámlázott költséggel.

További információért lásd: [Az Azure-számlák ismertetése](understand-invoice.md) és [Az Azure részletes használatának ismertetése](understand-usage.md).

## <a name="compare-billed-charges-and-usage-in-cost-analysis"></a>A kiszámlázott díjtételek összehasonlítása a költségelemzésben szereplő használati adatokkal

Az Azure Portalon való költségelemzés szintén segíthet a díjtételek ellenőrzésében. A kiszámlázott használat és díjtételek gyors áttekintéséhez válassza ki előfizetését az Azure Portal [Előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Ezután kattintson a **Költségelemzés** elemre, majd a nézetlistában a **Számla részletei** elemre.

![A Számla részletei lehetőség kiválasztását bemutató példa](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Következő lépésként a dátumtartomány listájában válassza ki a számlázási időszakot. Adjon hozzá egy számlaszámszűrőt, majd válassza ki a számlájának megfelelő InvoiceNumber értéket. A költségelemzés a kiszámlázott tételek költségadatait jeleníti meg.

![Példa a kiszámlázott költségadatok megjelenítésére a költségelemzés során](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

A költségelemzésben megjelenített költségeknek pontosan meg kell egyezniük az egyedi erőforrás *használati díjai* esetében kiszámlázott költségekkel.

![A számlán feltüntetett használati díjak](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external-marketplace-services-are-billed-separately"></a>A Marketplace külső szolgáltatásait külön számlázzuk

<a name="external"></a>

A külső szolgáltatások vagy a piactér díjai olyan erőforrásokra vonatkoznak, amelyeket külső szoftverszállítók készítettek. Ezek az erőforrások az Azure Marketplace-ből érhetők el. A Barracuda-tűzfal például egy harmadik fél által kínált Azure Marketplace-erőforrás. A tűzfal és a hozzá tartozó mérőszámok összes díja külső szolgáltatási díjként jelenik meg.

A külső szolgáltatási díjakat külön számlázzuk. Ezek a díjak nincsenek feltüntetve az Azure-számlán. További tudnivalókért lásd [az Azure külső szolgáltatásaival kapcsolatos díjak ismertetését](understand-azure-marketplace-charges.md).

### <a name="resources-are-billed-by-usage-meters"></a>Az erőforrások számlázása a használat mérésére szolgáló eszközök alapján történik

Az Azure nem közvetlenül az erőforrásköltségek alapján számláz. Az erőforrások költségeit egy vagy több mérőszám alapján számítjuk ki. A mérőszámokkal az erőforrás kihasználtságát mérjük az erőforrás teljes élettartama során. Ezen mérőszámok alapján számítjuk ki a számlán szereplő költséget.

Ha egyetlen Azure-erőforrást (például virtuális gépet) hoz létre, akkor ahhoz kapcsolódóan egy vagy több mérőeszközpéldány lett létrehozva. A mérőszámok az erőforrás adott idő alatti kihasználtságát mérik. Minden mérőeszköz használati adatokat vesz fel, amelyek alapján az Azure kiszámítja a számlán szereplő összeget.

Az Azure-ban létrehozott egyetlen virtuális gép használatának nyomon követéséhez például a következő mérőeszközök hozhatók létre:

- Üzemóra
- IP-cím szolgáltatásórái
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard felügyelt lemez
- Standard felügyeltlemez-műveletek
- Standard IO-lemez
- Standard IO – blokkblob-olvasás
- Standard IO – blokkblob-írás
- Szabványos IO – blokkblob-törlés

A virtuális gép létrehozásakor minden mérőeszköz elkezd használati adatokat szolgáltatni. Ezt a használatot és a mérőeszköz díjszabását az Azure-mérési rendszerben követjük nyomon.

A számla összegének kiszámításához használt mérőeszközök a használati adatok CSV-fájljában láthatók, ahogy a korábbi példában láthatta.

## <a name="pay-your-bill"></a>A számla befizetése

<a name="payment"></a>

Ha hitelkártyás fizetést állít be fizetési módként, a rendszer a számlázási időszak lejárta után 10 napon belül automatikusan felszámítja az összeget. A hitelkártya-kivonatban a költségsorban a következő szerepel: **MSFT Azure**.

A megterhelt hitelkártya megváltoztatásához tekintse meg a [hitelkártya az Azure-ban történő hozzáadását, frissítését vagy eltávolítását](../manage/change-credit-card.md) ismertető szakaszt.

[Számlás fizetés](../manage/pay-by-invoice.md) esetén küldje az utalást a számla alján megadott helyre.

Az utalás állapotának ellenőrzéséhez [hozzon létre egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A kiszámlázott díjtételek összehasonlítása a használati adatok fájljával
> * A kiszámlázott díjtételek összehasonlítása a költségelemzésben szereplő használati adatokkal

A költségelemzés használatának megkezdéséhez tekintse át a gyorsútmutatót.

> [!div class="nextstepaction"]
> [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md)
