---
title: Azure App Service-díjak csökkentése fenntartott kapacitással
description: Ismerje meg, hogyan takaríthat meg Azure App Service Premium v3 fenntartott példányok díjait és az elkülönített Stamp díját.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 92a315121ad8ae6fadcadbf6d531eb3e99ae69a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374541"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Költségek megtakarítása Azure App Service fenntartott példányokkal

Ez a cikk azt ismerteti, hogyan menthető Azure App Service fenntartott példányok a Premium v3-példányok és az elkülönített Stamp-díjak alapján.

## <a name="save-with-premium-v3-reserved-instances"></a>Mentés prémium v3 fenntartott példányokkal

Ha Azure App Service Premium v3 fenntartott példányra véglegesít, pénzt takaríthat meg. A foglalási kedvezményt a rendszer automatikusan alkalmazza a foglalási hatókörnek és attribútumoknak megfelelő futó példányok számára. A kedvezmények beszerzéséhez nincs szükség foglalás hozzárendelésére egy példányhoz.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>A vásárlás előtt határozza meg a megfelelő fenntartott példány méretét

A foglalás megvásárlása előtt meg kell határoznia a szükséges prémium v3 fenntartott példány méretét. A következő fejezetek segítséget nyújtanak a prémium v3 fenntartott példány méretének meghatározásában.

### <a name="use-reservation-recommendations"></a>Foglalási javaslatok használata

A foglalási javaslatok segítségével megadhatja a megvásárolni kívánt foglalásokat.

- A vásárlási javaslatok és a javasolt mennyiség akkor jelenik meg, ha a Azure Portalban egy Premium v3 fenntartott példányt vásárol.
- Azure Advisor az egyes előfizetésekre vonatkozó vásárlási javaslatokat tartalmaz.
- Az API-k használatával vásárlási javaslatokat kaphat a megosztott hatókörhöz és az egyszeri előfizetések hatóköréhez is. További információ: [fenntartott példányok vásárlására vonatkozó ajánlás API-k nagyvállalati ügyfelek](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation)számára.
- A Nagyvállalati Szerződés (EA) és a Microsoft Customer Agreement (MCA) ügyfelek esetében a megosztott és az egyszeri előfizetési hatókörökkel kapcsolatos vásárlási javaslatok a [Azure Consumption Insights Power bi a Content Pack csomaggal](/power-bi/service-connect-to-azure-consumption-insights)érhetők el.

### <a name="analyze-your-usage-information"></a>A használati adatok elemzése

A használati adatok elemzésével megállapíthatja, hogy mely foglalásokat kell megvásárolnia. A használati adatok a használati fájlban és az API-kon érhetők el. Ezeket együtt használva meghatározhatja, hogy melyik foglalást szeretné megvásárolni. Ellenőrizze, hogy vannak-e olyan prémium v3-példányok, amelyek napi szinten magas kihasználtsággal rendelkeznek a megvásárolni kívánt foglalások mennyiségének meghatározásához.

A használati fájl számlázási időszak és napi használat szerint jeleníti meg a díjakat. A használati fájl letöltésével kapcsolatos információkért tekintse meg [Az Azure-beli használat és díjak megtekintését és letöltését](../understand/download-azure-daily-usage.md)ismertető témakört. Ezt követően a használati fájl adatai alapján [meghatározhatja, hogy milyen foglalást szeretne megvásárolni](determine-reservation-purchase.md).


## <a name="buy-a-premium-v3-reserved-instance"></a>Premium v3 fenntartott példány vásárlása

A [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D)a fenntartott prémium v3 fenntartott példányt vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](prepare-buy-reservation.md). Ezek a követelmények a Premium v3 fenntartott példány vásárlására vonatkoznak:

- Legalább egy EA-előfizetéshez vagy egy utólagos elszámolású előfizetéshez tartozó tulajdonosi szerepkörrel kell rendelkeznie.
- Az EA-előfizetések esetében engedélyezni kell a **fenntartott példányok hozzáadása** beállítást az [EA portálon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program esetében csak a rendszergazdai ügynökök vagy értékesítési ügynökök vásárolhatnak foglalásokat.

Példány vásárlása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Új foglalás megvásárlásához válassza a **Hozzáadás** lehetőséget, majd kattintson a **példány** elemre.
4. Töltse ki a kötelező mezőket. A prémium szintű v3 fenntartott példányok futtatása, amelyek megfelelnek a kiválasztott attribútumoknak, a foglalási kedvezményhez. A prémium v3 fenntartott példányok tényleges száma, amely a kedvezményt kapja, a kiválasztott hatókörtől és mennyiségtől függ.

Ha nagyvállalati szerződéssel rendelkezik, a további példányok gyors hozzáadásához használhatja a **továbbiak hozzáadása lehetőséget** is. A beállítás más előfizetési típusok esetében nem érhető el.

| **Mező** | **Leírás** |
|---|---|
| Előfizetés | A foglalás kifizetéséhez használt előfizetés. Az előfizetésnél rögzített fizetési mód szerint lesznek számlázva a foglalás költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy Microsoft ügyfél-szerződés vagy egyéni előfizetés utólagos elszámolású díjszabással (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A díjak a pénzügyi keretek egyenlegéből lesznek levonva (ha van ilyen), vagy kerettúllépésként lesznek számlázva. Az utólagos elszámolású előfizetések díjait a hitelkártyára vagy a számla fizetési módjára kell fizetni az előfizetésben. |
| Hatókör | A foglalás hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <ul><li>**Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza. </li><li>**Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.</li><li>**Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. Az EA-ügyfelek esetében a számlázási környezet a beléptetés. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.</li></ul> |
| Region | A foglalás által érintett Azure-régió. |
| Premium v3 fenntartott példány mérete | A prémium v3 fenntartott példányok mérete. |
| Időszak | Egy vagy három év. A HBv2 Premium v3 fenntartott példányain 5 éves időszak is elérhető. |
| Mennyiség | A foglalás keretében megvásárolt példányok száma. A mennyiség a számlázási kedvezményt elérő prémium v3 fenntartott példányok száma. Ha például 10 standard \_ D2 Premium v3 fenntartott példányt futtat az USA keleti régiójában, akkor a mennyiséget 10-re kell megadnia, hogy maximalizálja az összes, a prémium szintű v3 lefoglalt példány kedvezményét. |

## <a name="save-with-isolated-stamp-fees"></a>Mentés elkülönített Stamp-díjakkal

Pénzt takaríthat meg az Azure App Service-beli izolált bélyegek díjából, ha három évre előre kötelezettséget vállal egy bélyeghasználat foglalására. Fenntartott kapacitás izolált bélyegdíjhoz történő vásárlásához ki kell választania a bélyeg üzembe helyezésének helyeként szolgáló Azure-régiót és a megvásárolni kívánt bélyegek számát.

Foglalás vásárlásakor a foglalás jellemzőivel egyező izolált bélyegdíj használatáért a továbbiakban nem használatalapú díjakat kell fizetnie. A rendszer automatikusan alkalmazza a foglalást a fenntartott kapacitás hatókörével és régiójával egyező izolált bélyegekre. Az izolált bélyegekhez nem kell foglalást rendelni. A foglalás nem érvényes a feldolgozókra, így a bélyeghez társított egyéb erőforrásokért külön kell fizetnie.

A fenntartott kapacitás lejáratakor az izolált bélyegek tovább futnak, azonban használatalapú díjjal lesznek számlázva. A foglalások nem újulnak meg automatikusan.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>A megfelelő elszigetelt Stamp foglalásának meghatározása a vásárláshoz

A foglalás megvásárlásával kötelezettséget vállal a lefoglalt mennyiségek használatára a következő három évre. Tekintse meg a használati adatokat annak meghatározásához, hány olyan izolált bélyeggel rendelkezik az App Service-ben, amelyeket konzisztensen használ, és esetlegesen használni fog a jövőben.

Emellett tekintse át, hogy az izolált bélyegek használata hogyan jelenik meg a linuxos vagy windowsos fogyasztásmérőkön.

- Alapértelmezés szerint egy üres izolált bélyeg használata a windowsos bélyeg-fogyasztásmérőn jelenik meg. Ha például nincsenek üzembe helyezve feldolgozók. Ha a bélyegen üzembe vannak helyezve windowsos feldolgozók, akkor a bélyeghasználat továbbra is az adott fogyasztásmérőn jelenik meg.
- Ha üzembe helyez egy Linux-feldolgozót, akkor a mérő a Linux-tranzakciómérőszámra vált át.
- Ha linuxos és windowsos feldolgozók is üzembe vannak helyezve, a bélyeghasználat a windowsos fogyasztásmérőn jelenik meg.

A bélyeg-fogyasztásmérő tehát váltani tud a Windows és a Linux rendszer között a bélyeg élettartama során.

Ha egy vagy több windowsos feldolgozóval rendelkezik a bélyegen, akkor Windows rendszerű bélyegfoglalásokat vásároljon. Linux rendszerű bélyegfoglalást csak akkor érdemes vásárolnia, ha a bélyegen _kizárólag_ linuxos feldolgozókat kíván használni.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Fenntartott kapacitás vásárlása izolált bélyeghez

Izolált bélyeghez az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) vásárolhat fenntartott kapacitást. A foglalásért fizethet [előre vagy havi részletekben](./prepare-buy-reservation.md). Fenntartott kapacitás vásárlásához legalább egy nagyvállalati vagy egy használatalapú fizetéses, egyéni előfizetés tulajdonosi szerepkörével kell rendelkeznie.

- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Illetve ha ez a beállítás le van tiltva, akkor Önnek EA-rendszergazdának kell lennie.
- A felhőszolgáltatói (CSP-) program esetében csak a felügyeleti ügynökök és az értékesítési ügynökök vásárolhatnak fenntartott Azure Synapse Analytics-kapacitást.

**A vásárláshoz:**

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Válasszon egy előfizetést. Az **Előfizetés** listából válassza ki a fenntartott kapacitás kifizetéséhez használt előfizetést. Az előfizetésnél megadott fizetési mód szerint lesznek számlázva a fenntartott példány költségei. Az előfizetés kétféle típusú lehet: nagyvállalati szerződés (ajánlatszám: MS-AZR-0017P vagy MS-AZR-0148P) vagy használatalapú fizetéses (ajánlatszám: MS-AZR-0003P vagy MS-AZR-0023P) vagy CSP-előfizetés.
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó Azure-előrefizetés (korábbi nevén pénzügyi keret) egyenlegéből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.
1. Az előfizetés hatókörének kiválasztásához válasszon ki egy **Hatókört**.
    - **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
    - **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.
    - **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.
1. A fenntartott kapacitás által fedezett Azure-régió kiválasztásához válasszon ki egy **Régiót**, és adja hozzá a foglalást a kosárhoz.
1. Válassza ki az izolált csomagok egyik típusát, majd kattintson a **Kiválasztás** lehetőségre.  
    ![Példa ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Adja meg az App Service-beli izolált bélyegek lefoglalni kívánt mennyiségét. Ha például hármat ad meg mennyiségként, akkor három lefoglalt bélyeget kap a régióban. Kattintson a **Tovább: Áttekintés + vásárlás** lehetőségre.
1. Tekintse át a beállításokat, és kattintson a **Vásárlás** lehetőségre.

A vásárlás után a [Foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) terület megnyitásával bármikor megtekintheti és monitorozhatja a vásárlás állapotát.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>A kedvezmény alkalmazása a használati adatokban

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. A használati adatok megjelenítik az egyes foglalásokban található bélyegpéldányokra vonatkozó foglalási kedvezményt.

A foglalási kedvezmény a használati adatokban történő megjelenésével kapcsolatos további információkért tekintse meg [a Nagyvállalati Szerződés foglalási költségeit és használati adatait](understand-reserved-instance-usage-ea.md) ismertető cikket, ha Ön Nagyvállalati Szerződéssel rendelkező (EA-) ügyfél. Minden más esetben tekintse meg a [használatalapú fizetéses egyedi előfizetés Azure-beli foglalási használatát](understand-reserved-instance-usage.md) ismertető cikket.

## <a name="next-steps"></a>További lépések

- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [Az Azure App Service-beli izolált bélyegekre érvényes foglalási kedvezmény alkalmazása](reservation-discount-app-service.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
