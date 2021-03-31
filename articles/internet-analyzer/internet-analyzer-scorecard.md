---
title: A scorecard értelmezése | Microsoft Docs
description: Útmutató a scorecard értelmezéséhez. A scorecard lapon a tesztek összesített és elemzett eredményei láthatók.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: how-to
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f43d094193fb266d1ecec7089b44d8b3fd5e9b43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91330213"
---
# <a name="interpreting-your-scorecard"></a>A stratégiai mutatószámrendszer értelmezése

A scorecard lapon a tesztek összesített és elemzett eredményei láthatók. Minden teszt saját scorecardokkal rendelkezik. A scorecardok gyors és hasznos összefoglalókat biztosítanak a mérési eredményekről, amelyek adatvezérelt eredményeket biztosítanak a hálózati követelményekhez. Az Internet Analyzer gondoskodik az elemzésről, így a döntésre koncentrálhat.

A scorecard lap az Internet Analyzer erőforrás menüjében található. 


## <a name="filters"></a>Szűrők

* ***Teszt:*** Válassza ki azt a tesztet, amelynek eredményeit meg szeretné tekinteni – az egyes tesztekhez saját scorecard tartozik. Az elemzések elvégzéséhez elegendő mennyiségű információ jelenik meg, a legtöbb esetben pedig 24 órán belül kell lennie. 
* Időtartam ***& befejezési dátum:*** Naponta három scorecard jön létre – az egyes scorecardok eltérő összesítési időszakot (nap), a megelőző 24 órában, a hét napja (hét) és a 30 nappal korábbi (hónap) időpontot tükrözik. A "befejezési dátum" szűrő használatával válassza ki a megtekinteni kívánt időszak utolsó napját. 
* ***Ország:*** Minden olyan ország esetében, amelynek végfelhasználói, scorecard jön létre. A globális szűrő minden végfelhasználót tartalmaz.

## <a name="measurement-count"></a>Mérések száma

A mérések száma az elemzés megbízhatóságát befolyásolja. Minél nagyobb a szám, annál pontosabb az eredmény. Legalább a teszteknek legalább 100 mérést kell végezniük napi szinten. Ha a mérési számok túl alacsonyak, konfigurálja úgy a JavaScript-ügyfelet, hogy gyakrabban fusson az alkalmazásában. Az A és B végpontok mérési számának nagyon hasonlónak kell lennie, bár a vártnál kisebb eltérések várhatók és rendben vannak. Nagy különbségek esetén az eredményeket nem szabad megbízhatóként megadni.

## <a name="percentiles"></a>Percentilisek

A késés (ezredmásodpercben mérve) egy népszerű metrika a forrás és a cél közötti sebesség méréséhez az interneten. A késési adatok általában nem oszlanak el (azaz nem követi a "harang-görbét"), mert a nagy késési értékek "hosszú farok" értékkel vannak elferdítve, amikor olyan statisztikai adatokat használnak, mint például a számtani középérték. Egy másik lehetőségként a percentilis "terjesztés – ingyenes" módszert biztosít az adatelemzéshez. Például a medián vagy 50 százalékos érték összegzi az eloszlás közepét, az értékek fele pedig az alatta lévő értékeket, a fele pedig alatta van. A 75th percentilis érték azt jelenti, hogy a terjesztésben az összes érték 75%-a nagyobb. Az Internet Analyzer a gyorsírásban a P50, a P75 és a P95 értékre hivatkozik.

Az Internet Analyzer percentilis a _minta mérőszáma_. Ez ellentétben áll a valódi _populációs metrikával_. Például a Dél-Kalifornia Egyetem és a Microsoft között a tanulók közötti napi átlagos késési arány az adott nap során az összes kérelem középértékes késése. A gyakorlatban a kérelmek értékének mérése nem praktikus, ezért feltételezzük, hogy egy ésszerűen nagy minta a valódi populációra jellemző.

Elemzési célokra a P50 (medián) a késési eloszlás várt értékeként hasznos. A magasabb százalékos érték, például a P95, hasznos lehet annak azonosítására, hogy milyen magas a késés a legrosszabb esetekben. Ha szeretné, hogy az ügyfelek késése általánosságban megértse, a P50 a megfelelő metrika. Ha aggódik a legrosszabb teljesítményt nyújtó ügyfelek teljesítményének megismerése érdekében, akkor a P95 a fókusznak kell lennie. A P75 a kettő közötti egyensúly.


## <a name="deltas"></a>Eltérések

A delta érték az A és B végpontok metrikai értékeinek különbsége. a különbözetek kiszámítása úgy történik, hogy a "B" előnyeit jeleníti meg. a pozitív értékek azt jelzik, hogy a B teljesítménye nagyobb, míg A negatív értékek a B teljesítményét jelzik. A különbözet abszolút lehet (például 10 ezredmásodperc) vagy relatív (5%).

## <a name="confidence-interval"></a>Megbízhatósági intervallum 

A megbízhatósági intervallumok (CI) olyan értékek, amelyek valószínűsége a populációs metrika, például a medián, a P75 vagy az átlag. Követjük a 95% CI használatával kapcsolatos közös statisztikai konvenciót.

Az Internet Analyzer esetében a szűk megbízhatósági intervallum jó, mert a minta metrikája valószínűleg nagyon közel van a tényleges populációs mérőszámhoz. A széles körű megbízhatósági intervallum kevésbé biztos, hogy a minta mérőszáma az igazi populációs mérőszámot tükrözi. A CI javításának legjobb módja a mérések számának növelése.

## <a name="time-series"></a>idősorok 

Az idősorozatok azt mutatják be, hogyan változik a metrika az idő múlásával. Az interneten számos olyan időbeli tényező van, amely hatással van a teljesítményre, például a maximális forgalmú időszakokra, a hétköznapok populációbeli különbségekre és a nyaralásokra.


## <a name="next-steps"></a>Következő lépések

További információkért tekintse meg az [Internet Analyzer áttekintése](internet-analyzer-overview.md)című témakört.
