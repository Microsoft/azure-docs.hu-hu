---
title: Frissített fájlok nyomon követése Azure Automation figyelőkkel feladat
description: Ez a cikk azt ismerteti, hogyan hozhat létre megfigyelő feladatot a Azure Automation fiókban egy mappában létrehozott új fájlok megtekintéséhez.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: ca4c4013e0044811a5bac8786996761b8a5c45f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97682746"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Frissített fájlok követése figyelő feladattal

A Azure Automation egy megfigyelői feladattal keres eseményeket, és elindítja a műveleteket a PowerShell-runbookok használatával. A figyelő feladat két részből áll: a figyelőkből és a műveletből. A figyelők runbook a figyelő feladatban meghatározott időközönként futnak, és adatokat küld egy műveleti runbook.

> [!NOTE]
> Az Azure China Vianet 21 nem támogatja a figyelők feladatait.

> [!IMPORTANT]
> A 2020-as verziótól kezdődően a Azure Logic Apps használata javasolt és támogatott módszer az események figyelésére, az ismétlődő feladatok ütemezett feladatokra és a műveletek elindítására. Lásd: [ismétlődő automatizált feladatok, folyamatok és munkafolyamatok ütemezett és futtatásának Azure Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Ez a cikk végigvezeti egy figyelő feladat létrehozásán, amely figyeli, hogy új fájlt adnak-e hozzá egy címtárhoz. Az alábbiak végrehajtásának módját ismerheti meg:

* Figyelők runbook importálása
* Automation-változó létrehozása
* Művelet runbook létrehozása
* Figyelőfeladat létrehozása
* Figyelő indítása
* A kimenet vizsgálata

## <a name="prerequisites"></a>Előfeltételek

A cikk elvégzéséhez a következők szükségesek:

* Egy Azure-előfizetés. Ha még nem rendelkezik fiókkal, [aktiválhatja MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), illetve [regisztrálhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-fiók](./index.yml) a megfigyelő és a műveleti runbookok és a figyelő feladat tárolására.
* Egy [hibrid runbook](automation-hybrid-runbook-worker.md) -feldolgozó, amelyben a figyelő tevékenység fut.
* PowerShell-runbookok. A figyelők feladatai nem támogatják a PowerShell-munkafolyamatok runbookok.

## <a name="import-a-watcher-runbook"></a>Figyelők runbook importálása

Ez a cikk a **Watch-NewFile** nevű figyelő runbook használatával keres új fájlokat egy címtárban. A figyelő runbook lekéri az utolsó ismert írási időt a mappában található fájlokra, és a vízjelnél újabb fájlokat keres.

A runbook a [Azure Automation GitHub-szervezetből](https://github.com/azureautomation)töltheti le.

1. Navigáljon a [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook)Azure Automation GitHub-szervezet oldalára.
2. A runbook a GitHubról való letöltéséhez válassza a **kód** lehetőséget az oldal jobb oldalán, majd a zip-fájlban lévő teljes kód letöltéséhez válassza a **zip letöltése** lehetőséget.
3. Bontsa ki a tartalmat, és [importálja a runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Ezt a runbook a portál Automation-fiókjába is importálhatja a következő lépések végrehajtásával.

1. Nyissa meg az Automation-fiókját, és kattintson a Runbookok lapra.
2. Kattintson a **Tallózás** katalógus lehetőségre, és a **forrás** legördülő listában válassza a **GitHub** lehetőséget.
3. Keresse meg a **figyelők runbook**, válassza ki a **figyelők runbook, amely az új fájlokat keresi egy könyvtárban**, és kattintson az **Importálás** elemre.
4. Adja meg a runbook nevét és leírását, majd az **OK** gombra kattintva importálja a Runbook az Automation-fiókjába.
5. Válassza a **Szerkesztés** lehetőséget, majd kattintson a **Közzététel** elemre. Ha a rendszer kéri, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="create-an-automation-variable"></a>Automation-változó létrehozása

Az [Automation változó](./shared-resources/variables.md) az előző runbook által beolvasott és az egyes fájlokban tárolt időbélyegek tárolására szolgál.

1. Válasszon **változókat** a **megosztott erőforrások** területen, majd kattintson **a + változó hozzáadása** lehetőségre.
1. Adja meg a **NewFileTimestamp** nevet a név mezőben.
1. A típushoz válassza a DateTime értéket.
1. Az Automation-változó létrehozásához kattintson a **Létrehozás** gombra.

## <a name="create-an-action-runbook"></a>Művelet runbook létrehozása

A figyelő feladatban egy runbook, amely az átadott adatoknak a megfigyelő runbook való működéséhez használatos. A [Azure Automation GitHub-szervezetből](https://github.com/azureautomation)a **Process-NewFile** nevű előre definiált műveleti runbook kell importálnia.

Művelet runbook létrehozása:

1. Navigáljon a [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook)Azure Automation GitHub-szervezet oldalára.
2. A runbook a GitHubról való letöltéséhez válassza a **kód** lehetőséget az oldal jobb oldalán, majd a zip-fájlban lévő teljes kód letöltéséhez válassza a **zip letöltése** lehetőséget.
3. Bontsa ki a tartalmat, és [importálja a runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

Ezt a runbook az Automation-fiókjába is importálhatja a Azure Portal:

1. Navigáljon az Automation-fiókjához, és válassza a **runbookok** lehetőséget a **folyamat automatizálása** alatt.
1. Kattintson a **Tallózás** katalógus lehetőségre, és a **forrás** legördülő listában válassza a **GitHub** lehetőséget.
1. Keresse meg a **figyelők műveletet**, válassza a **figyelők művelet lehetőséget, amely feldolgozza a megfigyelő runbook által aktivált eseményeket**, majd kattintson az **Importálás** gombra.
1. Adja meg a runbook nevét és leírását, majd az **OK** gombra kattintva importálja a Runbook az Automation-fiókjába.
1. Válassza a **Szerkesztés** lehetőséget, majd kattintson a **Közzététel** elemre. Ha a rendszer kéri, kattintson az **Igen** gombra a runbook közzétételéhez.

## <a name="create-a-watcher-task"></a>Figyelőfeladat létrehozása

Ebben a lépésben az előző szakaszokban meghatározott figyelőre és műveleti runbookok hivatkozó figyelő feladatot konfigurálja.

1. Navigáljon az Automation-fiókjához, és válassza a **figyelő feladatok** lehetőséget a **folyamat automatizálása** alatt.
1. Válassza ki a megfigyelő tevékenységek lapot, és kattintson a **+ figyelők hozzáadása feladat** elemre.
1. Adja meg a **WatchMyFolder** nevet.

1. Válassza a **figyelő konfigurálása** lehetőséget, és válassza ki a **Watch-NewFile** runbook.

1. Adja meg a következő értékeket a paraméterekhez:

   * **FOLDERPATH** – a hibrid Runbook-feldolgozó azon mappája, ahol új fájlok jönnek létre (például **d:\examplefiles**).
   * **Bővítmény** – bővítmény a konfigurációhoz. Hagyja üresen az összes fájlkiterjesztés feldolgozását.
   * **Reátok** – rekurzív művelet. Hagyja meg az alapértelmezett értéket.
   * **Futtatási beállítások** – a runbook futtatásának beállítása. Válassza ki a hibrid feldolgozót.

1. Kattintson az **OK gombra**, majd **válassza a lehetőséget** , ha vissza szeretne térni a figyelők oldalára.
1. Válassza a **művelet beállítása** elemet, és válassza a **Process-NewFile** runbook.
1. Adja meg a következő értékeket a paraméterekhez:

   * **EVENTDATA** -események. Hagyja üresen. Az adatok bekerülnek a figyelő runbook.
   * **Futtatási beállítások** – a runbook futtatásának beállítása. Hagyja meg az Azure-t, mivel ez a runbook Azure Automation fut.

1. Kattintson az **OK gombra**, majd **válassza a lehetőséget** , ha vissza szeretne térni a figyelők oldalára.
1. A figyelő feladat létrehozásához kattintson **az OK** gombra.

    ![Figyelő művelet konfigurálása a felhasználói felületen](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Figyelő indítása

Az alább leírtak szerint futtatnia kell egy tesztet annak biztosításához, hogy a figyelő tevékenység a várt módon működjön. 

1. Távoli a hibrid Runbook-feldolgozóval.
2. Nyissa meg a **PowerShellt** , és hozzon létre egy tesztoldalt a mappában.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

A következő példa a várt kimenetet mutatja.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>A kimenet vizsgálata

1. Navigáljon az Automation-fiókjához, és válassza a **figyelő feladatok** lehetőséget a **folyamat automatizálása** alatt.
1. Válassza ki a figyelő tevékenység **WatchMyFolder**.
1. Kattintson a **figyelő streamek megtekintése** **streamek** alatt lehetőségre, hogy a figyelő megtalálta az új fájlt, és elindította a művelet runbook.
1. A művelet runbook feladatainak megtekintéséhez kattintson a **figyelő műveleti feladatok megtekintése** elemre. Mindegyik feladatot kiválaszthatja a feladatok részleteinek megtekintéséhez.

   ![Figyelő műveleti feladatai a felhasználói felületen](media/automation-watchers-tutorial/WatcherActionJobs.png)

Az új fájl észlelésekor várható kimenet az alábbi példában látható:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a saját runbook létrehozásáról, tekintse meg [a PowerShell-Runbook létrehozását](learn/automation-tutorial-runbook-textual-powershell.md)ismertető témakört.
