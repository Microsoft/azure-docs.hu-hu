---
title: Nagyítási szintek és csemperács Azure Maps-|} A Microsoft Docs
description: Megismerheti a nagyítási szintek és csemperács Azure Maps-Közösséghez
author: jingjing-z
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e7dcdb960fbd9196aca8b667269a4c6e5a1fb8f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60794958"
---
# <a name="zoom-levels-and-tile-grid"></a>Nagyítási szintek és csemperács
Az Azure Maps használata a gömbös Mercator leképezése koordináta-rendszerére (EPSG: 3857).

A világ négyzetes csempék oszlik. Az Azure maps Raszter és vector csempék 23 nagyítási szinten, a 0 – 22-es számú biztosít. Nagyítási szint 0 az egész világ fér el egyetlen csempére:

![Globális csempe](./media/zoom-levels-and-tile-grid/world0.png)

Nagyítási szint 1 négy csempére használja a világ megjelenítése: 2 x 2 négyzetes

![Globális csempe felül a bal oldalon](media/zoom-levels-and-tile-grid/world1a.png)     ![Globális csempe jobb felső](media/zoom-levels-and-tile-grid/world1c.png) 

![Globális csempe alul a bal oldalon](media/zoom-levels-and-tile-grid/world1b.png)     ![Globális csempe alul a jobb oldalon](media/zoom-levels-and-tile-grid/world1d.png) 

Minden további nagyítási szint négyszeres-osztással, az előzőt, 2-es rács létrehozása a csempék<sup>nagyítás</sup> x 2<sup>nagyítás</sup>. Nagyítási szint 22-es rendszer 2 rács<sup>22-es</sup> x 2<sup>22-es</sup>, vagy 4,194,304 x 4,194,304 csempék (összesen 17,592,186,044,416 csempék).

A web-és Android-támogatás az Azure Maps interaktív térkép vezérlők nagyítási szintek 25 nagyítási szintek, 0 és 24 közötti számú. Bár a közúti adatok csak akkor érhető el a nagyítási szinten mikor érhetők el a csempéket.

Az alábbi táblázatban a teljes lista értékeit a nagyítási szint:

|Nagyítási szint|Mérőszámok/képpont|Ügyféloldali mérőszámok/csempe|
|--- |--- |--- |
|0|156543|40075008|
|1|78271.5|20037504|
|2|39135.8|10018764.8|
|3|19567.9|5009382.4|
|4|9783.9|2504678.4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611.5|156544|
|9|305.7|78259.2|
|10|152.9|39142.4|
|11|76.4|19558.4|
|12|38.2|9779.2|
|13|19.1|4889.6|
|14|9.6|2457.6|
|15|4.8|1228.8|
|16|2.4|614.4|
|17|1.2|307.2|
|18|a 0.6-os|152.8|
|19|0.3|76.4|
|20|0.15|38.2|
|21|0.075|19.1|
|22|0.0375|9.55|
|23|0.01875|4.775|
|24|0.009375|2.3875|

Csempék a csempe helyre a rácson a nagyítási szintnek megfelelő nagyítási szintjét, és az x és y koordinátái alapján nevezzük.

Eldönteni, melyik nagyítási szintjét használja, ne feledje, minden egyes hely van a rögzített helyzetben a csempére. Ez azt jelenti, hogy egy adott expanse terület megjelenítéséhez szükséges rögzíthető csempék számának függ, a világ rács zoom adott elhelyezését. Például ha két mutat 900 mérőszámok egymástól, azt *előfordulhat, hogy* három csempék megjelenítése egy útvonalat a közöttük 17 nagyítási szinten csak igénybe. Ha a nyugati pont jobb oldalán a csempét, és a keleti mutasson a csempe a bal oldali, eltarthat négy csempére:

![Nagyítás bemutató mértéke](media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

A nagyítási szint határozza meg, ha az x és y értékek számíthatók. A bal felső csempe minden nagyítás rács x = 0, y = 0; a jobb alsó csempe jelenleg x = 2<sup>-1 nagyítás</sup>, y = 2<sup>Nagyítás-1</sup>.

A Nagyítás rács nagyítási szintjének 1 az itt látható:

![Nagyítási szint 1 nagyítás rács](media/zoom-levels-and-tile-grid/api_x_y.png)
