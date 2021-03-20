---
title: Skálázhatóság és teljesítmény – személyre szabás
titleSuffix: Azure Cognitive Services
description: 'A nagy teljesítményű és a nagy forgalmú webhelyeknek és alkalmazásoknak két fő szempontja van, amely a méretezhetőség és a teljesítmény érdekében a rugalmasságot és a teljesítményt is figyelembe veszi: késés és betanítás.'
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 52d22fce64a26267e73cb9a1df8614129bb96c3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132687"
---
# <a name="scalability-and-performance"></a>Méretezhetőség és teljesítmény

A nagy teljesítményű és nagy forgalmú webhelyekhez és alkalmazásokhoz két fő tényezőt kell figyelembe venni a méretezhetőség és a teljesítmény személyre szabása érdekében:

* Alacsony késés a rangsor API-hívások készítésekor
* Győződjön meg arról, hogy a betanítási átviteli sebesség lépést tart az esemény bemenetével

A személyre szabás gyorsan, a REST APIon keresztüli kommunikációhoz dedikált hívási időtartam nagy részét is visszaállíthatja. Az Azure automatikusan méretezi a kérések gyors reagálásának lehetőségét.

##  <a name="low-latency-scenarios"></a>Kis késleltetésű forgatókönyvek

Egyes alkalmazások esetében alacsony késésre van szükség, amikor egy rangot ad vissza. Kis késleltetés szükséges:

* Annak érdekében, hogy a felhasználó a rangsorolt tartalom megjelenítése előtt ne várjon észrevehető időtartamot.
* Annak érdekében, hogy a szélsőséges forgalmat észlelő kiszolgálók elkerüljék a szűkös számítási idő és a hálózati kapcsolatok összekapcsolását.


## <a name="scalability-and-training-throughput"></a>Skálázhatóság és betanítási teljesítmény

A személyre szabás úgy működik, hogy frissít egy olyan modellt, amely a személyre szabott, a rangsor és a jutalmazási API-k után aszinkron módon küldött üzeneteken alapul. Ezeket az üzeneteket az alkalmazás Azure-EventHub küldik el.

 Nem valószínű, hogy a legtöbb alkalmazás eléri a személyre szabáshoz való csatlakozás és a betanítás maximális átviteli sebességét. Ha eléri ezt a maximumot, nem lassítja le az alkalmazást, azt feltételezi, hogy az Event hub-várólistákat a rendszer a tisztításhoz leggyorsabban kitölti.

## <a name="how-to-estimate-your-throughput-requirements"></a>Az átviteli sebességre vonatkozó követelmények becslése

* A környezeti és a műveleti JSON-dokumentumok hosszának kiszámításával megbecsülheti az egyes rangsorolási események bájtjainak átlagos számát.
* A becsült átlagos bájtok száma (MB/s)

Ha például az átlagos adattartalom 500 funkcióval rendelkezik, és mindegyik 20 karakterből áll, akkor minden esemény körülbelül 10kb. A becslések szerint a 20 000 000/10 000 = 2 000 Events/sec, amely körülbelül 173 000 000 esemény/nap. 

Ha eléri ezeket a korlátokat, forduljon a támogatási csapathoz architektúra-tanácsadásért.

## <a name="next-steps"></a>Következő lépések

[Személyre szabás létrehozása és konfigurálása](how-to-settings.md).