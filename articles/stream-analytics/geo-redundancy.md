---
title: Geo-redundancia elérése Azure Stream Analytics feladatokhoz
description: Ez a cikk azt ismerteti, hogyan lehet a földrajzi feladatátvétel helyett a Azure Stream Analytics feladatok geo-redundanciát elérni.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/26/2020
ms.openlocfilehash: 86a90cbcbb3381e853dcea766364642a89dafd7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015521"
---
# <a name="achieve-geo-redundancy-for-azure-stream-analytics-jobs"></a>Geo-redundancia elérése Azure Stream Analytics feladatokhoz

A Azure Stream Analytics nem biztosít automatikus földrajzi feladatátvételt, de a Geo-redundancia eléréséhez több Azure-régióban is azonos Stream Analytics feladatok helyezhetők üzembe. Minden egyes feladathoz egy helyi bemeneti és egy helyi kimeneti forrás csatlakozik. Az alkalmazás feladata, hogy mindkét helyi bemenetbe küldje a bemeneti adatokat, és összehangolja a két regionális kimenetet. A Stream Analytics feladatok két különálló entitás.

Az alábbi ábrán egy, a Geo-redundáns Stream Analytics feladatok üzembe helyezése látható az Event hub bemenetével és az Azure Database kimenetével.

:::image type="content" source="media/geo-redundancy/geo-redundant-jobs.png" alt-text="Geo-redundáns stream Analytics-feladatok diagramja":::

## <a name="primarysecondary-strategy"></a>Elsődleges/másodlagos stratégia

Az alkalmazásnak kezelnie kell a régió kimeneti adatbázisát, amely az elsődleges, és a másodlagosnak számít. Az elsődleges régió meghibásodásakor az alkalmazás átvált a másodlagos adatbázisra, és elkezdi a frissítések olvasását az adott adatbázisból. Az ismétlődő olvasások minimalizálását lehetővé tevő tényleges mechanizmus az alkalmazástól függ.Ezt a folyamatot leegyszerűsítheti további információk megírásával a kimenetre. Hozzáadhat például egy időbélyeget vagy egy sorszámot az egyes kimenetekhez, így az ismétlődő sorok kihagyása triviális művelet. Az elsődleges régió visszaállítása után a rendszer felveszi a másodlagos adatbázist a hasonló mechanika használatával.

Bár a különböző bemeneti és kimeneti típusok különböző földrajzi replikálási lehetőségeket tesznek lehetővé, javasoljuk, hogy az ebben a cikkben ismertetett mintát használva a Geo-redundancia eléréséhez, mivel rugalmasságot és irányítást biztosít a két esemény gyártója és az esemény felhasználói számára.

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics feladatok figyelése és kezelése a PowerShell-lel](stream-analytics-monitor-and-manage-jobs-use-powershell.md)
* [Adatvezérelt hibakeresés a Azure Stream Analyticsban](stream-analytics-job-diagram-with-metrics.md)