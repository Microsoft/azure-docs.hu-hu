---
title: Stream Analytics feladatok autoskálázása
description: Ez a cikk azt ismerteti, hogyan lehet a Stream Analytics feladatokat az előre meghatározott ütemterv vagy a feladatok metrikái értékei alapján átméretezni.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: a8e089e302e9d40c69cf7ff2a3480c17894e1463
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016286"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Stream Analytics feladatok autoskálázása Azure Automation használatával

Az autoskálázás konfigurálásával optimalizálhatja Stream Analytics feladatok költségeit. Az automatikus skálázás növeli vagy csökkenti a feladatok folyamatos átviteli egységeit (SUs) a bemeneti terhelés változásának megfelelően. A feladatok túlzott kiosztása helyett igény szerint fel-vagy leskálázást végez. A feladatokat kétféleképpen állíthatja be az automatikusan méretezhetővé:
1. **Előre definiálhatja az ütemtervet** , ha kiszámítható bemeneti terheléssel rendelkezik. Például nagyobb mennyiségű bemeneti eseményt várhat a nappaliban, és azt szeretné, hogy a feladata több SUs-sel fusson.
2. **Felskálázási és leskálázási műveletek elindítása a feladatok metrikái alapján** , ha nem rendelkezik előre jelezhető bemeneti terheléssel. Dinamikusan módosíthatja az SUs számát a feladatok mérőszámai alapján, például a bemeneti események száma vagy a várakozó bemeneti eseményei.

## <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az automatikus skálázás konfigurálását a feladatokhoz, hajtsa végre az alábbi lépéseket.
1. A feladatok [párhuzamos topológiával](./stream-analytics-parallelization.md)vannak optimalizálva. Ha a futás közben módosítható a feladatok skálázása, akkor a feladatainak párhuzamos topológiával kell rendelkezniük, és beállítható az autoskálázás.
2. [Hozzon létre egy Azure Automation fiókot](../automation/automation-create-standalone-account.md) az "RunAsAccount" kapcsolóval. Ennek a fióknak engedélyekkel kell rendelkeznie a Stream Analytics feladatok kezeléséhez.

## <a name="set-up-azure-automation"></a>Azure Automation beállítása
### <a name="configure-variables"></a>Változók konfigurálása
Adja hozzá a következő változókat a Azure Automation fiókban. Ezeket a változókat a következő lépésekben ismertetett runbookok fogja használni a rendszer.

| Név | Típus | Érték |
| --- | --- | --- |
| **jobName** | Sztring | Az átméretezni kívánt Stream Analytics-feladatok neve. |
| **resourceGroupName** | Sztring | Azon erőforráscsoport neve, amelyben a feladattípus található. |
| **subId** | Sztring | Az előfizetés-azonosító, amelyben a feladata található. |
| **increasedSU** | Egész szám | A magasabb SU érték, amelynek a feladatainak a méretezését ütemezni szeretné. Ennek az értéknek az egyik érvényes SU-beállításnak kell lennie, amelyet a feladatok **skálázási** beállításaiban láthat a futása közben. |
| **decreasedSU** | Egész szám | Az alsó SU érték, amelynek a feladatainak a méretezését ütemezni szeretné. Ennek az értéknek az egyik érvényes SU-beállításnak kell lennie, amelyet a feladatok **skálázási** beállításaiban láthat a futása közben. |
| **maxSU** | Egész szám | A maximális SU érték, amelyet a feladatoknak a betöltéssel történő automatikus skálázás esetén kell méreteznie. Ennek az értéknek az egyik érvényes SU-beállításnak kell lennie, amelyet a feladatok **skálázási** beállításaiban láthat a futása közben. |
| **minSU** | Egész szám | A minimális SU érték, amelyet a feladatoknak a betöltéssel történő automatikus skálázás esetén kell méreteznie. Ennek az értéknek az egyik érvényes SU-beállításnak kell lennie, amelyet a feladatok **skálázási** beállításaiban láthat a futása közben. |

![Változók hozzáadása a Azure Automationban](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Runbookok létrehozása
A következő lépés az, hogy két PowerShell-runbookok hozzon létre. Az egyik a vertikális felskálázáshoz, a másik pedig a skálázási műveletekhez.
1. A Azure Automation-fiókjában lépjen a **runbookok** alatt a **folyamat automatizálása**  területen, majd válassza a **Runbook létrehozása** lehetőséget.
2. Nevezze el az első runbook- *ScaleUpRunbook* , a típust pedig a PowerShell értékre. Használja a GitHubon elérhető [ScaleUpRunbook PowerShell-szkriptet](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1) . Mentse és tegye közzé.
3. Hozzon létre egy *ScaleDownRunbook* nevű másik Runbook a PowerShell típussal. Használja a GitHubon elérhető [ScaleDownRunbook PowerShell-szkriptet](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1) . Mentse és tegye közzé.

![Runbookok Azure Automationban való méretezése](./media/autoscale/runbooks.png)

Most már rendelkezik olyan runbookok, amely automatikusan elindítja a vertikális Felskálázási és-skálázási műveleteket a Stream Analytics feladatokon. Ezek a runbookok előre meghatározott ütemterv használatával indíthatók el, vagy dinamikusan állíthatók be a feladatok metrikái alapján.

## <a name="autoscale-based-on-a-schedule"></a>Ütemterv alapján történő autoskálázás
Azure Automation lehetővé teszi az ütemterv konfigurálását a runbookok elindításához.
1. A Azure Automation-fiókban válassza az **ütemtervek** lehetőséget a **megosztott erőforrások** területen. Ezután válassza **az ütemezett Hozzáadás** lehetőséget.
2. Létrehozhat például két ütemtervet. Ez azt jelenti, hogy ha azt szeretné, hogy a feladat vertikális felskálázást és egy másikat, amely akkor jelenik meg, ha azt szeretné, hogy a feladat le legyen skálázás Megadhatja az ütemezések ismétlődését.

   ![Ütemtervek Azure Automation](./media/autoscale/schedules.png)

3. Nyissa meg a **ScaleUpRunbook** , majd válassza az **erőforrások** területen az **ütemtervek** lehetőséget. Ezután összekapcsolhatja a runbook az előző lépésekben létrehozott ütemtervtel. Ugyanazzal a runbook több ütemterv is társítható, amelyek hasznosak lehetnek, ha ugyanazt a skálázási műveletet szeretné futtatni a nap különböző pontjain.

![Runbookok ütemezése Azure Automation](./media/autoscale/schedulerunbook.png)

1. Ismételje meg az előző lépést a **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Automatikus skálázás terhelés alapján
Előfordulhatnak olyan esetek, amikor nem lehet előre jelezni a bemeneti terhelést. Ilyen esetekben a minimális és a maximális korláton belül optimálisan méretezhető fel-és leskálázás. A Stream Analytics-feladatokban beállíthatja a riasztási szabályokat úgy, hogy kiváltsa a runbookok, ha a feladat metrikái a küszöbérték felett vagy alatt haladnak.
1. A Azure Automation-fiókjában hozzon létre két több Integer változót a **minSU** és a **maxSU** néven. Ezzel beállítja azokat a határokat, amelyeken belül a feladatnak a lépésein át kell méreteznie.
2. Hozzon létre két új runbookok. Használhatja a [StepScaleUp PowerShell-parancsfájlt](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) , amely a **maxSU** értékig növeli a feladatok SUs értékét. A [StepScaleDown PowerShell-szkriptet](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) is használhatja, amely csökkenti a feladatok SUs értékét a lépésekben, amíg a **minSU** érték el nem éri. Azt is megteheti, hogy az előző szakasz runbookok is használhatja, ha konkrét SU-értékeket szeretne méretezni.
3. A Stream Analyticsi feladatban válassza a **figyelés** területen a **riasztási szabályok** elemet. 
4. Hozzon létre két műveleti csoportot. Egy a vertikális Felskálázási művelethez és egy másik a skálázási művelethez. Válassza a **műveletek kezelése** lehetőséget, majd kattintson a **művelet csoport hozzáadása** elemre. 
5. Töltse ki a kötelező mezőket. Válassza az **Automation Runbook** lehetőséget a **művelet típusának** kiválasztásakor. Válassza ki azt a runbook, amelyet a riasztás tüzek esetén aktiválni szeretne. Ezután hozza létre a műveleti csoportot.

   ![Műveletcsoport létrehozása](./media/autoscale/create-actiongroup.png)
6. Hozzon létre egy [**új riasztási szabályt**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) a feladatokban. Válasszon egy feltételt a választott mérőszám alapján. A [ *bemeneti események*, a *su%-kihasználtság* vagy a *várakozó bemeneti eseményei*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) az automatikus skálázási logika definiálásához javasolt mérőszámok. Azt is javasoljuk, hogy a vertikális Felskálázási műveletek elindításához 1 perces *összesítési részletességet* és *értékelési gyakoriságot* használjon. Így biztosíthatja, hogy a feladatnak elegendő erőforrása legyen a bemeneti kötet nagy tüskékkel való megbirkózásához.
7. Válassza ki az utolsó lépésben létrehozott műveleti csoportot, és hozza létre a riasztást.
8. Ismételje meg a 2 – 4. lépést minden további, a feladatra vonatkozó mérőszámok feltétele alapján elindítható méretezési műveletnél.

Az ajánlott eljárás a méretezési tesztek futtatása a feladatok éles környezetben való futtatása előtt. Ha a feladatát különböző bemeneti terhelések ellen teszteli, azzal a feladattal, hogy hány SUs-erőforrásra van szüksége a különböző bemeneti átviteli sebességhez. Ez tájékoztathatja a riasztási szabályokban definiált feltételeket a vertikális Felskálázási és leskálázási műveletek elindításához. 

## <a name="next-steps"></a>Következő lépések
* [Párhuzamosítható-lekérdezések létrehozása a Azure Stream Analyticsban](stream-analytics-parallelization.md)
* [Azure Stream Analytics feladatok méretezése az átviteli sebesség növelése érdekében](stream-analytics-scale-jobs.md)