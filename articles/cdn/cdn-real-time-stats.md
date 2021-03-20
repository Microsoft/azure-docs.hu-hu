---
title: Valós idejű statisztikák a Azure CDNban | Microsoft Docs
description: A valós idejű statisztikák valós idejű adatokat biztosítanak a Azure CDN teljesítményéről, amikor tartalmat továbbítanak az ügyfeleknek.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c7989340-1172-4315-acbb-186ba34dd52a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 3af2e849aa6658e539b0b5bdbda4428cc28e5ce5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84887225"
---
# <a name="real-time-stats-in-microsoft-azure-cdn"></a>Microsoft Azure CDN valós idejű statisztikái
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés
Ez a dokumentum a Microsoft Azure CDN valós idejű statisztikáit mutatja be.  Ez a funkció valós idejű adatokat biztosít, például a sávszélességet, a gyorsítótár állapotát és a CDN-profilhoz való egyidejű kapcsolatokat, amikor tartalmat továbbít az ügyfeleknek. Ez bármikor lehetővé teszi a szolgáltatás állapotának folyamatos figyelését, beleértve a Go-Live eseményeket is.

A következő diagramok érhetők el:

* [Sávszélesség](#bandwidth)
* [Állapotkódok](#status-codes)
* [Gyorsítótár állapota](#cache-statuses)
* [Kapcsolatok](#connections)

## <a name="accessing-real-time-stats"></a>Valós idejű statisztika elérése
1. Az [Azure Portalon](https://portal.azure.com)keresse meg a CDN-profilját.
   
    ![CDN-profil panel](./media/cdn-real-time-stats/cdn-profile-blade.png)
2. A CDN-profil panelen kattintson a **kezelés** gombra.
   
    ![CDN-profil panel kezelése gomb](./media/cdn-real-time-stats/cdn-manage-btn.png)
   
    Megnyílik a CDN felügyeleti portál.
3. Vigye az egérmutatót az **elemzés** lapra, majd vigye a kurzort a **valós idejű statisztika** menü fölé.  Kattintson a **nagyméretű http-objektumra**.
   
    ![CDN felügyeleti portál](./media/cdn-real-time-stats/cdn-premium-portal.png)
   
    Megjelenik a valós idejű statisztika-diagramok.

A diagramok mindegyike valós idejű statisztikát jelenít meg a kiválasztott időtartományra vonatkozóan, az oldal betöltésekor.  A gráfok néhány másodpercenként automatikusan frissülnek.  A **gráf frissítése** gomb, ha van, törli a diagramot, amely után csak a kijelölt adatelemeket fogja megjeleníteni.

## <a name="bandwidth"></a>Sávszélesség
![Sávszélesség gráf](./media/cdn-real-time-stats/cdn-bandwidth.png)

A **sávszélesség** grafikonon az aktuális platformhoz használt sávszélesség mennyisége látható a kiválasztott időtartományon. A gráf árnyékolt része a sávszélesség-használatot jelzi. A jelenleg használatban lévő sávszélesség pontos mennyisége közvetlenül a vonalas diagram alatt jelenik meg.

## <a name="status-codes"></a>Állapotkódok
![Állapotkód gráf](./media/cdn-real-time-stats/cdn-status-codes.png)

Az **állapotkódok** gráf azt jelzi, hogy milyen gyakran fordul elő bizonyos http-válaszok a kiválasztott időtartományon.

> [!TIP]
> Az egyes HTTP-állapotkódok beállítás leírását lásd: [Azure CDN HTTP-állapotkódok](/previous-versions/azure/mt759238(v=azure.100)).
> 
> 

A HTTP-állapotkódok listája közvetlenül a gráf felett jelenik meg. Ez a lista minden olyan állapotkódot jelez, amely szerepelhet a vonalas diagramban, valamint az adott állapotkód számára másodpercenként előforduló előfordulások számát. Alapértelmezés szerint a diagramon minden egyes állapotkód esetében sor jelenik meg. Dönthet azonban úgy is, hogy csak a CDN-konfigurációhoz különleges jelentőséggel rendelkező állapotkódot figyeli. Ehhez jelölje ki a kívánt állapotkódot, és törölje az összes többi beállítást, majd kattintson a **Graph frissítése** elemre. 

Egy adott állapotkód esetében átmenetileg elrejtheti a naplózott adatokat.  A jelmagyarázat alatt közvetlenül a gráf alatt kattintson az elrejteni kívánt állapotkódot. Az állapotkód azonnal el lesz rejtve a gráfból. Ha ismét rákattint erre az állapotkódre, akkor a beállítás újból megjelenik.

## <a name="cache-statuses"></a>Gyorsítótár állapota
![Gyorsítótár-állapotok gráf](./media/cdn-real-time-stats/cdn-cache-status.png)

A **cache statuss (gyorsítótár állapota** ) gráf azt jelzi, hogy milyen gyakran fordul elő bizonyos típusú gyorsítótár-állapot a kiválasztott időtartományon. 

> [!TIP]
> Az egyes gyorsítótár-állapotkódok beállítás leírását lásd: gyorsítótár-állapotkódok [Azure CDN](/previous-versions/azure/mt759237(v=azure.100)).
> 
> 

A gyorsítótár-állapotkódok listája közvetlenül a gráf felett jelenik meg. Ez a lista minden olyan állapotkódot jelez, amely szerepelhet a vonalas diagramban, valamint az adott állapotkód számára másodpercenként előforduló előfordulások számát. Alapértelmezés szerint a diagramon minden egyes állapotkód esetében sor jelenik meg. Dönthet azonban úgy is, hogy csak a CDN-konfigurációhoz különleges jelentőséggel rendelkező állapotkódot figyeli. Ehhez jelölje ki a kívánt állapotkódot, és törölje az összes többi beállítást, majd kattintson a **Graph frissítése** elemre. 

Egy adott állapotkód esetében átmenetileg elrejtheti a naplózott adatokat.  A jelmagyarázat alatt közvetlenül a gráf alatt kattintson az elrejteni kívánt állapotkódot. Az állapotkód azonnal el lesz rejtve a gráfból. Ha ismét rákattint erre az állapotkódre, akkor a beállítás újból megjelenik.

## <a name="connections"></a>Kapcsolatok
![Kapcsolatok gráf](./media/cdn-real-time-stats/cdn-connections.png)

Ez a gráf azt jelzi, hogy hány kapcsolat lett létrehozva a peremhálózati kiszolgálókhoz. Egy adategységre vonatkozó minden kérelem, amely a CDN-eredményeken keresztül halad a kapcsolatok között.

## <a name="next-steps"></a>Következő lépések
* [Valós idejű riasztások](cdn-real-time-alerts.md) beszerzése Azure CDN
* Mélyebb kiásás a [speciális http-jelentésekkel](cdn-advanced-http-reports.md)
* [Használati minták](cdn-analyze-usage-patterns.md) elemzése

