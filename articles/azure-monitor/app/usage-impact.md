---
title: Az Azure Application Insights használatának hatása | Microsoft docs
description: Elemezheti, hogy a különböző tulajdonságok milyen hatással lehetnek az alkalmazások egyes részeinek konverziós arányára.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 51f938743d8da3c9a1dea546320662701e2b88c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100583524"
---
# <a name="impact-analysis-with-application-insights"></a>Impact Analysis Application Insights

A hatás azt elemzi, hogy a betöltési idő és más tulajdonságok milyen hatással vannak az alkalmazás különböző részeinek konverziós arányára. Pontosabban fogalmazva azt észleli, hogy egy **oldal nézet**, egy **egyéni esemény** vagy **kérelem** **bármely dimenziója** hatással van-e egy másik **oldal** vagy **egyéni esemény** használatára. 

![Hatás eszköz](./media/usage-impact/0001-impact.png)

## <a name="still-not-sure-what-impact-does"></a>Még nem biztos benne, hogy milyen hatással van?

Az egyik lehetőség az, hogy a hatás úgy gondolja, mint a végső eszköz az argumentumok rendezésére valakivel a csapatával kapcsolatban, hogy a hely bizonyos aspektusa milyen mértékben befolyásolja a felhasználók körét. Míg a felhasználók bizonyos mennyiségű lassúságot elérhetik, a hatás azt mutatja be, hogy miként lehet a lehető legjobban kiegyensúlyozni az optimalizálást és a teljesítményt a felhasználók átalakításának maximalizálása érdekében.

A teljesítmény elemzése azonban csak a hatások egy részhalmaza lehet. Mivel a hatás támogatja az egyéni eseményeket és dimenziókat, a kérdések megválaszolásához hasonlóan a felhasználói böngésző választásának eltérő díjszabása is csupán néhány kattintásnyira van.

![Képernyőképek konvertálása böngészők szerint](./media/usage-impact/0004-browsers.png)

> [!NOTE]
> A Application Insights-erőforrásnak oldalletöltések vagy egyéni eseményeket kell tartalmaznia az Impact eszköz használatához. [Megtudhatja, hogyan állíthatja be az alkalmazást úgy, hogy automatikusan összegyűjtse a Application Insights JavaScript SDK-val](./javascript.md). Azt is vegye figyelembe, hogy mivel a korreláció elemzése, a minta méretének számítása.
>
>

## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>A lap betöltési ideje befolyásolja, hogy hány ember konvertálja a saját oldalra?

Ha szeretné megkezdeni a kérdések megválaszolását a hatás eszközzel, válassza ki a kezdeti oldal nézetet, az egyéni eseményt vagy a kérelmet.

![Képernyőkép, amely azt mutatja, hogy hol válassza ki a kezdeti oldal nézetet, az egyéni eseményt vagy a kérést.](./media/usage-impact/0002-dropdown.png)

1. Válassza ki az oldal nézetét az **oldal nézet** legördülő menüjéből.
2. Hagyja meg az **elemzés módját, hogy** a legördülő lista az alapértelmezett **időtartamot** válassza (ebben **a kontextusban az** **oldal betöltési ideje** alias).
3. A legördülő lista **használatának hatásaként** válassza ki az egyéni eseményt. Az eseménynek meg kell felelnie az 1. lépésben kiválasztott oldal nézet felhasználói felületi elemének.

![Az eredmények képernyőképe](./media/usage-impact/0003-results.png)

Ebben a példában a **Product (termék) oldal** betöltési ideje növeli az átváltási arányt a **termékek megvásárlására kattintva** A fenti eloszlás alapján a 3,5 másodperces optimális terhelési időtartam megcélozható a 55%-os lehetséges konverziós arány eléréséhez. Az 3,5 másodperc alatti betöltési idő csökkentése további teljesítménnyel kapcsolatos továbbfejlesztések jelenleg nem korrelálnak további konverziós előnyökkel.

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>Mi a teendő, ha egyéni módon követem nyomon az oldalletöltések vagy a betöltési időt?

A hatás a szabványos és az egyéni tulajdonságokat, valamint a méréseket is támogatja. Használja bármit, amit szeretne. Az időtartam helyett a szűrők használata az elsődleges és másodlagos eseményeken a pontosabb beszerzéséhez.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>Különböző országokból vagy régiókból származó felhasználók eltérő díjszabás szerint alakulnak?

1. Válassza ki az oldal nézetét az **oldal nézet** legördülő menüjéből.
2. Válassza az "ország vagy régió" lehetőséget az **elemzés** a legördülő menüben
3. A legördülő lista **használatának hatására** válasszon ki egy egyéni eseményt, amely az 1. lépésben kiválasztott oldal nézet felhasználói felületi elemének felel meg.

Ebben az esetben az eredmények már nem illeszkednek egy folytonos x tengelyes modellbe, ahogy az első példában is voltak. Ehelyett egy szegmentált tölcsérhez hasonló vizualizáció jelenik meg. Rendezés a **használat** alapján az egyéni eseményre az ország/régió alapján történő átalakítás változásának megtekintéséhez.


## <a name="how-does-the-impact-tool-calculate-these-conversion-rates"></a>Hogyan számítja ki a hatás eszköz ezeket az átváltási díjakat?

A motorháztető alatt az Impact eszköz a [Pearson korrelációs együtthatóra](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)támaszkodik. Az eredmények a-1 és 1 közötti értékkel vannak kiszámítva, ami negatív lineáris korrelációt jelöl, és 1 pozitív lineáris korrelációt jelképez.

A Impact Analysis működésének alapvető részletezése a következő:

Legyen _a_ = az első legördülő listában kiválasztott fő oldal nézet/egyéni esemény/kérelem. (**Az oldal nézethez**).

Legyen _B_ = a másodlagos lap nézete/egyéni kiválasztott esemény (**a használata hatással** van).

A hatás a kiválasztott időtartományban lévő felhasználók összes munkamenetének mintáját vizsgálja. Minden egyes munkamenet esetében az _a_ egyes előfordulásait keresi.

A munkamenetek két különböző típusú _almunkamenetre_ bonthatók, a következő két feltétel egyike alapján:

- Az átalakított almunkamenetek egy _b_ eseménnyel végződő munkamenetből állnak, és minden _olyan_ eseményt foglalnak magukban, amelyek a _b_ előtt történnek.
- A nem konvertált almunkamenet akkor következik be, amikor _az összes művelet a_ _B_ terminál nélkül történik.

A hatás végső kiszámításának módja attól függően változik, hogy metrika vagy dimenzió alapján elemezzük-e a rendszer. A metrikák esetében az összes _egy_ almunkamenetben átlaga. Míg a dimenziók esetében az egyes értékek értéke _1/N_ értékkel _járul hozzá a_ _B_ -hez rendelt értékhez, ahol _N_ az almunkamenetben _lévők_ száma.

## <a name="next-steps"></a>Következő lépések

- A használati tapasztalatok engedélyezéséhez kezdjen el [Egyéni eseményeket](./api-custom-events-metrics.md#trackevent) vagy [oldalletöltések](./api-custom-events-metrics.md#page-views)küldését.
- Ha már elküldte az egyéni eseményeket vagy a lapok nézeteit, tekintse meg a használati eszközöket, amelyekkel megismerheti, hogy a felhasználók miként használják a szolgáltatást.
    - [Tölcsérek](usage-funnels.md)
    - [Visszatartás](usage-retention.md)
    - [Felhasználókövetés](usage-flows.md)
    - [Munkafüzetek](../visualize/workbooks-overview.md)
    - [Felhasználói környezet hozzáadása](usage-send-user-context.md)

