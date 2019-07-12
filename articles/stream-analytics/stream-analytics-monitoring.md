---
title: Feladat figyelése az Azure Stream Analytics ismertetése
description: Ez a cikk ismerteti az Azure Portalon az Azure Stream Analytics-feladatok figyelése.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 6/21/2018
ms.custom: seodec18
ms.openlocfilehash: a4fc6db300a24d483e88efe668645d2cdb62496b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612278"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>Megismerheti a Stream Analytics-feladat figyelése és lekérdezések figyelése

## <a name="introduction-the-monitor-page"></a>Bemutatása: A figyelő lap
Az Azure portál felület fő teljesítménymutatói monitorozást és hibaelhárítást végezhet a lekérdezés és a feladat teljesítményét használható. Ezek a metrikák megtekintéséhez tallózással keresse meg a Stream Analytics-feladat, a metrikák megjelenítésének iránt, és megtekintheti a **figyelés** szakasz az Áttekintés oldalon.  

![Stream Analytics-feladat figyelése hivatkozás](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

Az ablakban látható módon jelenik meg:

![Stream Analytics-feladat figyelési irányítópult](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>Stream Analytics-metrikáit
| Metrika                 | Meghatározás                               |
| ---------------------- | ---------------------------------------- |
| Várakozó bemeneti események       | Bemeneti vannak várakozó események száma. Ez a metrika nullától eltérő értéket, az azt jelenti, hogy a feladat nem tud lépést tartani a beérkező események száma. Ha ez az érték lassan növekvő vagy következetesen nullától eltérő, horizontális felskálázást a feladatot. További információkért látogasson el [ismertetése és módosítása a folyamatos átviteli egységek](stream-analytics-streaming-unit-consumption.md). |
| Adatkonverziós hibák | Nem konvertálható a várt kimeneti sémájának kimeneti események számát. Hibakezelési házirend "Drop" dobja el az ebben a forgatókönyvben előforduló események is módosítható. |
| Korai bemeneti események       | Események, amelyek alkalmazás-időbélyeggel korábbi, mint azok érkezési idő szerint több mint 5 perc. |
| Sikertelen függvénykérések | Nem sikerült az Azure Machine Learning függvényhívások (ha van ilyen) száma. |
| Függvényesemények        | Az Azure Machine Learning-függvény (ha van ilyen) küldött események száma. |
| Függvénykérések      | Az Azure Machine Learning-függvény (ha van ilyen)-hívások száma. |
| A deszerializálás bemeneti hibái       | Nem sikerült deszerializálni a bemeneti események számát.  |
| Bemeneti esemény bájtokban      | A Stream Analytics-feladatot (bájt) által fogadott adatok mennyisége. Ez használható ellenőrzése, hogy a bemeneti forrás eseményt kap. |
| Bemeneti események           | A bemeneti események deszerializálni rekordok száma. Ez a szám nem tartalmazza a bejövő eseményeket, amelyek a deszerializálási hibákat eredményezhet. |
| Fogadott bemeneti források       | A feladat által fogadott üzenetek száma. Az Eseményközpontok felé egy üzenet egy egyetlen EventData. BLOB egy üzenet egy blobot. Vegye figyelembe, hogy a bemeneti források előtt deszerializálás bájtjai számítanak. Deszerializálási hiba van, ha a bemeneti források lehet nagyobb, mint a bemeneti események. Ellenkező esetben lehet kisebb vagy egyenlő a bemeneti események, mivel egyes üzeneteket több esemény is tartalmazhat. |
| Késedelmes bemeneti események      | Később, mint a beállított késői érkezési tolerancia ablak érkező események. Tudjon meg többet [Azure Stream Analytics esemény rendelés szempontok](stream-analytics-out-of-order-and-late-events.md) . |
| Kimenő soron kívüli események    | Fogadott lettek eldobva, vagy egy módosított időbélyeget, az esemény rendezése házirend alapján adott üzemen kívüli események száma. Ez lehet negatív hatással lehet a konfigurációs beállítás Out of rendelés tolerancia ablakban. |
| Kimeneti események          | A kimeneti tárolóhoz az események száma a Stream Analytics-feladat által küldött adatok mennyisége. |
| Futásidejű hibák         | Lekérdezés-feldolgozás (kivéve a talált események feldolgozására, vagy eredmények írása közben hibák) kapcsolatos hibák teljes száma |
| SU százalékos kihasználtsága       | A folyamatos átviteli egység felhasználása a méretezés lapon, a feladat hozzárendelt feladathoz. Ez a kijelző elérhető 80 %-os, vagy a fenti nincs nagy valószínűséggel, hogy az események feldolgozása késhet, vagy megáll. |
| Vízjel-késleltetés       | A vízjel maximális késleltetés, a feladat összes kimeneti összes partíciójára. |

Használhatja a metrikák [a Stream Analytics-feladatok teljesítményének megfigyelése](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor). 

## <a name="customizing-monitoring-in-the-azure-portal"></a>Figyelés testreszabása az Azure Portalon
Módosíthatja a diagram, metrika is látható, típusát és időtartomány a diagram szerkesztése beállításaiban. További információkért lásd: [testreszabása figyelése annak](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md).

  ![Stream Analytics lekérdezési idő grafikon](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>Legújabb kimenet
A feladat figyeléséhez más érdekes adatpont az az idő az utolsó kimeneti az Áttekintés lapon látható.
Az idő az az alkalmazás idő (azaz az idő az eseményadatokat időbélyege használatával), a feladat legfrissebb kimenetét.

## <a name="get-help"></a>Segítségkérés
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)
