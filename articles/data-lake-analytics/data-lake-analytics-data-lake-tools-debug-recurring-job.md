---
title: Ismétlődő feladatok hibakeresése Azure Data Lake Analytics
description: Ismerje meg, hogyan használható a Visual studióhoz készült Azure Data Lake Tools a rendellenes ismétlődő feladatok hibakereséséhez.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 05/20/2018
ms.openlocfilehash: b95d4b0a082e8eac191d610c9516e5ce72180b4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219173"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Rendellenes ismétlődő feladat hibaelhárítása

Ez a cikk bemutatja, hogyan használható a [Visual Studio Azure Data Lake-eszközei](https://aka.ms/adltoolsvs) az ismétlődő feladatokkal kapcsolatos problémák elhárításához. További információ a [Azure Data Lake és az Azure HDInsight blogban](/archive/blogs/azuredatalake/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy)található folyamatokról és ismétlődő feladatokról.

Az ismétlődő feladatok általában ugyanazokat a lekérdezési logikát és hasonló bemeneti adatokat használják. Tegyük fel például, hogy van egy ismétlődő feladatokkal, amely hétfőn reggel 8 órakor fut. a múlt heti aktív felhasználó számának megszámlálása. Ezeknek a feladatoknak a parancsfájljai egy parancsfájl-sablont osztanak meg, amely tartalmazza a lekérdezési logikát. Ezen feladatok bemenetei az elmúlt hét használati adatai. Ugyanaz a lekérdezési logika és a hasonló bevitel általában azt jelenti, hogy a feladatok teljesítménye hasonló és stabil. Ha az ismétlődő feladatok egyike hirtelen rendellenesen, meghiúsul, vagy lelassítja a műveletet, akkor érdemes lehet:

- Tekintse meg az ismétlődő feladatok előző futtatásának statisztikai jelentéseit, és tekintse meg, mi történt.
- Hasonlítsa össze a rendellenes feladatot egy normál értékkel, hogy megtudja, mi változott.

A Azure Data Lake Tools for Visual Studio **kapcsolódó feladatokkal kapcsolatos nézete** segítséget nyújt a hibaelhárítási folyamat felgyorsításához mindkét esetben.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>1. lépés: ismétlődő feladatok keresése és kapcsolódó feladatok megnyitása

Ha egy ismétlődő feladattal kapcsolatos probléma elhárításához kapcsolódó feladatok nézettel szeretne segítséget használni, először meg kell keresnie az ismétlődő feladatot a Visual Studióban, majd meg kell nyitnia a kapcsolódó feladatok nézetet.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>1. eset: az ismétlődő feladatokhoz tartozó URL-cím

Az **eszközök**  >  **Data Lake**  >  a **feladatok nézet** használatával beillesztheti a feladatok URL-címét a Visual Studióban a feladatok nézet megnyitásához. Válassza a **kapcsolódó feladatok megtekintése** elemet a kapcsolódó feladat nézet megnyitásához.

![Kapcsolódó feladatok hivatkozásának megtekintése Data Lake Analytics eszközökben](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>2. eset: az ismétlődő feladatokhoz tartozó folyamat nem az URL-cím

A Visual Studióban megnyithatja a folyamat böngészőjét a Server Explorerrel > a Azure Data Lake Analytics-fiókját > **folyamatokat**. (Ha nem találja ezt a csomópontot a Server Explorerben, [töltse le a legújabb beépülő modult](https://aka.ms/adltoolsvs).) 

![A folyamatok csomópont kiválasztása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

A folyamat böngészőjében a Data Lake Analytics fiók összes folyamata megjelenik a bal oldalon. A folyamatok kibontásával megkeresheti az összes ismétlődő feladatot, majd kiválaszthatja, hogy milyen problémákkal rendelkezik. A kapcsolódó feladatok nézete a jobb oldalon nyílik meg.

![Folyamat kiválasztása és a kapcsolódó feladatok nézetének megnyitása](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>2. lépés: statisztikai jelentés elemzése

Egy összefoglaló és egy statisztikai jelentés a kapcsolódó feladatok nézetének tetején látható. Itt megtalálhatja a probléma lehetséges kiváltó okát. 

1.  A jelentésben az X tengely megjeleníti a feladatok beküldésének idejét. Ezzel a művelettel megkeresheti a rendellenes feladatot.
2.  A következő ábrán található folyamat segítségével tekintse át a statisztikákat, és tájékozódjon a problémáról és a lehetséges megoldásokról.

![Feldolgozási diagram a statisztikák ellenőrzéséhez](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>3. lépés: a rendellenes feladatok összevetése normál feladatokkal

Az összes elküldött ismétlődő feladat a kapcsolódó feladatok nézetének alján található feladatlistán keresztül tekinthető meg. További megállapítások és lehetséges megoldások kereséséhez kattintson a jobb gombbal a rendellenes feladatokra. Használja a feladatok diff nézetet, hogy összehasonlítsa a rendellenes feladatot egy korábbi normál eggyel.

![Feladatok összehasonlítására szolgáló helyi menü](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Figyeljen a két feladat közötti nagy különbségekre. Ezek a különbségek valószínűleg a teljesítménnyel kapcsolatos problémákat okozzák. További információért kövesse az alábbi ábrán látható lépéseket:

![Feldolgozási diagram a feladatok közötti különbségek ellenőrzéséhez](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Következő lépések

* [Adathibák elhárítása](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Felhasználó által definiált C# kód hibakeresése a sikertelen U-SQL-feladatokhoz](data-lake-analytics-debug-u-sql-jobs.md)