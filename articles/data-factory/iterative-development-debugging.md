---
title: Ismétlődő fejlesztés és hibakeresés a Azure Data Factoryban
description: Ismerje meg, hogyan fejlesztheti és hibakeresési Data Factory folyamatok iteratív az ADF UX-ben
ms.date: 09/11/2020
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.openlocfilehash: e4c66055184b2ef0113aa0e25c02ad8635feddb3
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031007"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratív fejlesztés és hibakeresés az Azure Data Factoryval
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory lehetővé teszi, hogy az adatintegrációs megoldások fejlesztése során iteratív fejlesszen és hibakeresésen Data Factory folyamatokat. Ezek a funkciók lehetővé teszik a módosítások tesztelését a lekéréses kérelem létrehozása vagy az adatfeldolgozó szolgáltatásba való közzététel előtt. 

A szolgáltatás nyolc perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Folyamat hibakeresése

A folyamat vásznon való létrehozásakor a **hibakeresési** képesség használatával tesztelheti a tevékenységeket. A tesztek futtatásakor nem kell közzétennie a módosításokat az adatgyárban a **hibakeresés**kiválasztása előtt. Ez a funkció olyan esetekben hasznos, amikor azt szeretné, hogy a módosítások a várt módon működjenek, mielőtt frissíti a adatfeldolgozó munkafolyamatot.

![Hibakeresési képesség a folyamat vásznon](media/iterative-development-debugging/iterative-development-1.png)

Ahogy a folyamat fut, az egyes tevékenységek eredményét a folyamat vászon **kimenet** lapján tekintheti meg.

A teszt eredményeinek megtekintése a folyamat vászonjának **output (kimenet** ) ablakában.

![A folyamat vászonjának kimeneti ablaka](media/iterative-development-debugging/iterative-development-2.png)

A tesztek sikeres futtatása után további tevékenységeket adhat hozzá a folyamathoz, és ismétlődő módon folytathatja a hibakeresést. Egy teszt futtatása is **megszakítható** , amíg folyamatban van.

> [!IMPORTANT]
> A **hibakeresés** kiválasztásával a folyamat ténylegesen fut. Ha például a folyamat másolási tevékenységet tartalmaz, a teszt futtatása a forrásról a célhelyre másolja az adatokból. Ennek eredményeképpen javasolt a másolási tevékenységek és más tevékenységek tesztelési mappáinak használata a hibakereséshez. A folyamat hibakeresése után váltson át a normál műveletekben használni kívánt tényleges mappákra.

### <a name="setting-breakpoints"></a>Töréspontok beállítása

Azure Data Factory lehetővé teszi egy folyamat hibakeresését, amíg nem ér el egy adott tevékenységet a folyamat vásznon. Helyezzen egy töréspontot a tevékenységre, amíg meg nem kívánja vizsgálni, majd válassza a **hibakeresés**lehetőséget. Data Factory biztosítja, hogy a teszt csak a folyamat vásznon lévő töréspont tevékenységig fusson. Ez a *hibakeresés addig* nem hasznos, ha nem szeretné tesztelni a teljes folyamatot, de csak a folyamaton belüli tevékenységek egy részhalmazát.

![Töréspontok a folyamat vásznon](media/iterative-development-debugging/iterative-development-3.png)

Töréspont beállításához válasszon ki egy elemet a folyamat vásznon. Egy *hibakeresés, amíg* a lehetőség üres vörös körként jelenik meg a elem jobb felső sarkában.

![Mielőtt beállította a töréspontot a kijelölt elemre](media/iterative-development-debugging/iterative-development-4.png)

Miután kiválasztotta a *hibakeresést, amíg* a lehetőség be nem fejeződik, a Töréspont engedélyezésének jelzésére a kitöltött piros kör változik.

![Miután beállította a töréspontot a kijelölt elemen](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Hibakeresési hibák figyelése

Amikor futtat egy folyamat hibakeresési futtatását, az eredmények megjelennek a folyamat vászonjának **output (kimenet** ) ablakában. A kimenet lap csak az aktuális böngésző-munkamenet során bekövetkezett legutóbbi futtatást fogja tartalmazni. 

![A folyamat vászonjának kimeneti ablaka](media/iterative-development-debugging/iterative-development-2.png)

Ha meg szeretné tekinteni a hibakeresési futtatások előzményeit, vagy megtekintheti az összes aktív hibakeresési Futtatás listáját, megtekintheti a **figyelési** élményt. 

![Válassza az aktív hibakeresési futtatások megtekintése ikont.](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> A Azure Data Factory szolgáltatás 15 napig csak hibakeresési futtatási előzményeket tart fenn. 

## <a name="debugging-mapping-data-flows"></a>A leképezési adatfolyamatok hibakeresése

Az adatfolyamatok leképezése lehetővé teszi, hogy olyan kód nélküli Adatátalakítási logikát hozzon létre, amely nagy léptékben fut. A logika létrehozásakor bekapcsolhatja a hibakeresési munkamenetet, hogy interaktív módon működjön együtt az adataival egy élő Spark-fürt használatával. További tudnivalókért olvassa el a következő témakört: az [adatforgalom hibakeresési módjának leképezése](concepts-data-flow-debug-mode.md).

A **figyelő** felületén figyelheti az aktív adatfolyam-hibakeresési munkameneteket a gyáron belül.

![Adatfolyam-hibakeresési munkamenetek megtekintése](media/iterative-development-debugging/view-dataflow-debug-sessions.png)
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Folyamat hibakeresése adatfolyam-tevékenységgel

Ha hibakeresési kísérletet futtat egy adatfolyammal, két lehetőség közül választhat, amelyeken számítást kell használni. Használhat meglévő hibakeresési fürtöt, vagy felvehet egy új, igény szerinti fürtöt az adatforgalmához.

A meglévő hibakeresési munkamenetek nagy mértékben csökkentik az adatfolyamatok indítási idejét, mivel a fürt már fut, de nem ajánlott összetett vagy párhuzamos számítási feladatokhoz, mivel ez a művelet sikertelen lehet, ha egyszerre több feladat fut. 

A tevékenység-futtatókörnyezet használatával új fürtöt hoz létre az egyes adatfolyam-tevékenységek integrációs moduljában megadott beállításokkal. Ez lehetővé teszi az egyes feladatok elkülönítését, és az összetett számítási feladatokhoz vagy a teljesítmény teszteléséhez használhatók.

![Folyamat futtatása adatfolyam](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Következő lépések

A változtatások tesztelése után a [folyamatos integráció és üzembe helyezés révén a Azure Data Factoryban](continuous-integration-deployment.md)népszerűsítheti őket magasabb környezetekben.
