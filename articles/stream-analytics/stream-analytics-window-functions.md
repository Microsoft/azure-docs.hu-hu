---
title: Az Azure Stream Analytics ablakkezelési függvényeket bemutatása
description: Ez a cikk azt ismerteti, négy ablakkezelési függvényeket (átfedésmentes, segítségével tehetjük meg, a késleltetett, munkamenet) az Azure Stream Analytics-feladatok által használt.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 530ff8d09d6c580a31ae26929fafcec5bb5b471b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621596"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Bevezetés a Stream Analytics ablakkezelési függvényeket

Forgatókönyvek idejű streamelés műveleteket hajt végre a historikus windows szereplő adatok a gyakori minta. Stream Analytics ablakkezelési függvényeket, natív támogatással rendelkezik, így a fejlesztők szerzője összetett adatfolyam-feldolgozás feladatokat, minimális erőfeszítéssel.

A historikus windows választhat négy fő típusba sorolhatók: [**Átfedésmentes**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [ **segítségével tehetjük meg**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [ **késleltetett**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics), és [ **munkamenet**  ](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) windows.  Ablak nyújtott funkciók használata a [ **GROUP BY** ](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) a lekérdezés szintaxisa a Stream Analytics-feladatok záradékában. Események összesítése használatával több windows keresztül a [ **Windows()** függvény](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Az összes a [ablakkezelési](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) műveleteket, eredményeket a **záró** az ablak. A kimeneti ablak egyszeri esemény használja az aggregate függvény alapján lesz. A kimeneti esemény lesz az ablak végén időbélyegzőjét, és minden ablakfüggvények rögzített hosszúságú lettek definiálva. 

![Stream Analytics ablakban-függvények alapelvei](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Átfedésmentes ablak
Átfedésmentes ablak Funkciók adatstream szegmentálja a distinct időpontja részekre, és alkalmasság őket, mint például az alábbi példa segítségével. Az Átfedésmentes ablakok kulcs versenytársaink is, hogy azok ismételje meg, ehhez nincs átfedés, és az esemény nem tartozhat egynél több átfedésmentes ablak.

![Stream Analytics átfedésmentes ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Ugróablak
Ablak Funkciók Ugrás előre meg időben által meghatározott időtartamra. Elképzelhető, hogy könnyen gondol rájuk az Átfedésmentes ablakok, amely átfedésben is, így az események is tartozhat egynél több Hopping ablak eredményhalmaz. Hopping ablak ugyanaz, mint egy Átfedésmentes ablak az Ugrás mérete legyen ugyanaz, mint az ablak mérete adja meg. 

![Stream Analytics ugróablak](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Csúszóablakszerűen történik
Mozgó ablakfüggvények Átfedésmentes vagy segítségével tehetjük meg a windows, eltérően kimenetet **csak** -esemény bekövetkezésekor. Minden időszak kell legalább egy eseményt, és az ablak folyamatosan előrelépés egy "," (epszilon) által. Egynél több csúszóablakban windows meg, például eseményeket is tartozhat.

![Stream Analytics csúszóablakban](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Munkameneti ablakban
Munkamenet ablakfüggvények csoportosítása hasonló időpontokban érkező események szűri ki a időszakokra, ha nem szerepel megjeleníthető adat. Három fő paraméterekkel rendelkezik: időtúllépés, maximális időtartama és particionálókulcs (nem kötelező).

![Stream Analytics munkameneti ablakban](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

A munkameneti ablakban megkezdi az első esemény bekövetkeztekor. Ha a megadott időkorláton belül egy másik eseményt a legutóbbi feldolgozott esemény következik be, majd az ablak kiterjedjen az új esemény. Ellenkező esetben ha nincsenek események történnek az időkorláton belül, majd az időszak vége, az időkorlátot.

Ha eseményeket a megadott időkorláton belül tartani, a munkameneti ablakban fog tartani kiterjeszti a maximális időtartam eléréséig. A maximális időtartam ellenőrzése intervallumok akkora, mint a megadott maximális időtartam vannak beállítva. Például, ha a maximális időtartam 10, akkor az ellenőrzések, ha az ablak haladhatja meg a maximális időtartam fog történik a t = 0, 10, 20, 30, stb.

Egy partíciókulcsot áll rendelkezésre, ha az események együtt a kulcs szerint vannak csoportosítva, és a munkameneti ablakban függetlenül van alkalmazva minden egyes csoportra. Ezt a particionálása esetén hasznos esetekben, ahol különböző munkamenet a windows különböző felhasználók vagy eszközök számára.


## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

