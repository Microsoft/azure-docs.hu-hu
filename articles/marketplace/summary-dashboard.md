---
title: A partner Center Analytics összegző irányítópultja a kereskedelmi piactéren
description: Megtudhatja, hogyan érheti el a Piactéri tevékenységeket összefoglaló összesített adatok gráfokat, trendeket és értékeket a partner Center összegző irányítópultján.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462981"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Az Összegzés irányítópult a kereskedelmi piactér-elemzésekben

Ez a cikk a partner Center összegző irányítópultján tartalmaz információkat. Ez az irányítópult az ajánlatok számára elérhető Piactéri tevékenységet összefoglaló összesített adatok grafikonokat, trendeket és értékeket jeleníti meg.

Ha a partner Centerben szeretné elérni az összegző irányítópultot, a **kereskedelmi piactér** területen válassza az **[elemzés](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **összegzése** elemet.

>[!NOTE]
> Az analitikai terminológia részletes leírását lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).

## <a name="summary-dashboard"></a>Összegzés irányítópult

Az összegző irányítópult áttekintést nyújt az Azure Marketplace-ről, és Microsoft AppSource üzleti teljesítményt kínál. Az irányítópult átfogó áttekintést nyújt a következőkről:

- Ügyfelek rendelései
- Ügyfelek
- Az ajánlatok ügyfelek általi használata
- Az ügyfelek oldalának meglátogatása az Azure Marketplace-en és a AppSource

## <a name="elements-of-the-summary-dashboard"></a>Az összegző irányítópult elemei

Az alábbi szakaszok azt ismertetik, hogyan használható az összegző irányítópult, és hogyan lehet beolvasni az adatelemzést.

### <a name="month-range"></a>Hónap tartománya

Az egyes lapok jobb felső sarkában megtalálhatja a hónapok tartományának kijelölését. Szabja testre az **összegzési** oldal gráfok kimenetét úgy, hogy kijelöl egy hónap tartományt az elmúlt 3, 6 vagy 12 hónap alapján, vagy egy olyan egyéni hónapot választ, amely legfeljebb 12 hónapig tart. Az alapértelmezett hónap tartománya (számítási időszak) hat hónap.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="A havi tartomány lehetőségeit mutatja be az összegző irányítópulton.":::

> [!NOTE]
> A vizualizációs widgetek és exportálási jelentések összes mérőszáma a felhasználó által kiválasztott számítási időszakot tiszteletben tartja.

### <a name="orders-widget"></a>Megrendelések widget

Az összefoglalás * * irányítópult orders (megrendelések) widgetje megjeleníti a Transact-alapú ajánlatok aktuális rendeléseit. Az Orders (megrendelések) widget megjeleníti a kiválasztott számítási időszakra vonatkozó összes megvásárolt megrendelés számát és trendjét (a megszakított megrendelések kivételével). A százalékos érték **megrendelése** a kiválasztott számítási időszak növekedésének mértékét jelöli.

[![A megrendelések widget bemutatása az összegző irányítópulton.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Az Orders (megrendelések) jelentést a widget bal alsó sarkában található **orders (megrendelések) irányítópultra** kattintva is megtekintheti.

### <a name="customers-widget"></a>Ügyfelek widget

Az **Összefoglalás** irányítópultjának **Customers** widgetje azon ügyfelek teljes számát jeleníti meg, akik a kiválasztott számítási időszakra vonatkozóan szerezték be az ajánlatokat. A Customers (ügyfelek) widget az aktív (beleértve az új és meglévő) ügyfelek számát és trendjét jeleníti meg a kiválasztott számítási időszakra vonatkozóan. Az **ügyfelek** alatti százalékos érték a kiválasztott számítási időszak növekedésének mértékét jelöli.

[![Az összegző irányítópulton található Customers widget bemutatása.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

A részletes ügyfelek jelentésre kattintva a widget bal alsó sarkában található **Customers (ügyfelek irányítópult)** hivatkozásra kattintva is megtekintheti a részleteket.

### <a name="usage-widget"></a>Használati widget

Az **Összefoglalás** irányítópultjának **használati** widgetje az összes Azure-beli virtuális gép (VM) ajánlat összes normalizált és nyers használati idejét mutatja. A használati widget a kiválasztott számítási időszakra vonatkozó teljes használati idő számát és trendjét jeleníti meg.

A használat összegzése tábla az összes megvásárolt ajánlat ügyfél-használati idejét jeleníti meg.

- A normalizált használati idő a virtuálisgép-magok számának ([virtuálisgép-magok száma] x [órányi nyers használat]) fiókban normalizált használati órákat határozza meg. A "SHAREDCORE" jelölésű virtuális gépek a 1/6 (vagy 0,1666) értéket használják [virtuálisgép-magok száma] szorzóként.
- A nyers használati órák határozzák meg, hogy a virtuális gépek mennyi idő alatt futnak.

A teljes használati idő alatti százalékos érték a kiválasztott számítási időszakon belül a használati órákban mért növekedés mennyiségét jelöli.

[![A használati widget bemutatása az összegző irányítópulton.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

A használati jelentést a widget bal alsó sarkában található **használati irányítópult** hivatkozásra kattintva is megtekintheti.

### <a name="marketplace-insights"></a>Marketplace-ismeretek

A Piactéri bepillantást láthatja, hogy hány felhasználó látogatta meg az ajánlatokat az Azure Marketplace-en és a AppSource. Az **oldal látogatások száma** a kereskedelmi piactér webes elemzési szolgáltatásának összegzését jeleníti meg, amely lehetővé teszi a kiadók számára, hogy a kereskedelmi piactér online áruházában felsorolt termék részletes oldalain szereplő, a Microsoft AppSource és az Azure piactéren megjelenő termékspecifikus lapok Ez a widget a kiválasztott számítási időszakon belül a teljes oldal meglátogatások számát és trendjét jeleníti meg.

[![Az összegző irányítópulton az oldal felkeresésének száma widget.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

A Marketplace-alapú adatellenőrzési jelentéshez a widget-alapú **Piactéri irányítópult** hivatkozását is kiválaszthatja, amely a widget bal alsó sarkában található.

### <a name="geographical-spread"></a>Földrajzi eloszlás

A kiválasztott számítási időszakra vonatkozóan a hő a vevők, megrendelések és normalizált használati órák teljes számát jeleníti meg a földrajz dimenzióban.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Az összefoglalás irányítópulton ábrázolja az országok spread widgetjét.":::

Vegye figyelembe a következőket:

- A térképet áthelyezve megtekintheti a pontos helyet.
- Egy adott helyre nagyíthat.
- A hő egy kiegészítő rácstal rendelkezik, amely az adott helyen megtekintheti az ügyfelek számát, a megrendelések számát és a normalizált használati időt.
- A rácson megkeresheti és kiválaszthatja az országot/régiót a térképen való nagyításhoz. A Térkép **Kezdőlap** gombjára kattintva térjen át az eredeti nézetre.

> [!TIP]
> Az adatletöltéshez használhatja a letöltés ikont bármelyik widget jobb felső sarkában. Az egyes widgetekkel kapcsolatos visszajelzéseket a "hüvelykujj fel" vagy a "hüvelykujj lefelé" ikonra kattintva adhatja meg.

## <a name="next-steps"></a>Következő lépések

- A kereskedelmi piactéren elérhető analitikai jelentések áttekintését lásd: [hozzáférés analitikus jelentések a kereskedelmi piactérhez a partner Centerben](./partner-center-portal/analytics.md).
- További információ a megrendelésekről grafikus és letölthető formátumban: [Orders Dashboard a kereskedelmi piactér elemzésében](orders-dashboard.md).
- A virtuális gép (VM) használati és mért számlázási mérőszámokat kínál, lásd [a használati irányítópultot a kereskedelmi piactér elemzésében](usage-dashboard.md).
- Az ügyfelekkel kapcsolatos részletes információkért, beleértve a növekedési trendeket, lásd [a kereskedelmi piactér elemzésének ügyfél-irányítópultját](customer-dashboard.md).
- Az elmúlt 30 napban a letöltési kérelmek listáját lásd: a [kereskedelmi piactér Analytics letöltések irányítópultja](./partner-center-portal/downloads-dashboard.md).
- Ha szeretné megtekinteni az Azure Marketplace-en és a AppSource-on található ajánlatokra vonatkozó felhasználói visszajelzések összevont nézetét, tekintse meg a következőt: [ratings & Reviews](./partner-center-portal/ratings-reviews.md)
- A kereskedelmi piactér elemzésével és az adatkifejezések átfogó szótárával kapcsolatos gyakori kérdésekért lásd: [kereskedelmi piactér analitikai terminológiája és gyakori kérdések](./analytics-faq.md).