---
title: Az Azure Data Factory vizuális monitorozása
description: Ismerje meg, hogyan figyelheti vizuálisan az Azure-beli adatüzemeket
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 4248969a5de384810f9718648ef8ac8f5f6d4129
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373164"
---
# <a name="visually-monitor-azure-data-factory"></a>Az Azure Data Factory vizuális monitorozása

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Miután létrehozta és közzétett egy folyamatot Azure Data Factoryban, társíthatja egy triggerhez, vagy manuálisan elindíthatja az ad hoc futtatást. Az összes folyamatot a Azure Data Factory felhasználói élményben natív módon figyelheti. A figyelési élmény megnyitásához válassza a **figyelő & kezelés** csempét a [Azure Portal](https://portal.azure.com/)adatgyár paneljén. Ha már szerepel az ADF UX-ben, kattintson a bal oldali oldalsávon látható **figyelés** ikonra.

Alapértelmezés szerint a rendszer az összes adatfeldolgozó-futtatást a böngésző helyi időzónájában jeleníti meg. Ha megváltoztatja az időzónát, az összes dátum/idő mező a kiválasztotthoz van igazítva.

## <a name="monitor-pipeline-runs"></a>Folyamatfuttatások monitorozása

Az alapértelmezett figyelési nézet az aktivált folyamatok listája a kiválasztott időszakban. Az időtartomány és a szűrés állapot, folyamat neve vagy jegyzet alapján módosítható. Vigye az egérmutatót az adott folyamat futtatásához, és futtassa a Futtatás-specifikus műveleteket, például futtassa újra és a felhasználási jelentést.

![A figyelési folyamat futtatási listájának nézete](media/monitor-visually/pipeline-runs.png)

A folyamat futtatása rács a következő oszlopokat tartalmazza:

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Folyamat neve | A folyamat neve |
| Futtatás indítása | A folyamat futtatásának kezdési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Futtatás vége | A folyamat futtatásának befejezési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Időtartam | Futtatás időtartama (óó: PP: SS) |
| Aktiválta: | A folyamatot elindító trigger neve |
| Állapot | **Sikertelen**, **sikeres**, **folyamatban**, **megszakítva** vagy **várólistán** lévő |
| Széljegyzetek | Egy folyamathoz társított szűrhető Címkék  |
| Paraméterek | A folyamat futtatásának paraméterei (név/érték párok) |
| Hiba | Ha a folyamat nem sikerült, a futtatási hiba |
| Futtatási azonosító | A folyamat futtatásának azonosítója |

A folyamat-és tevékenység-futtatások listájának frissítéséhez manuálisan kell kijelölnie a **frissítés** gombot. Az AutoFrissítés jelenleg nem támogatott.

![Frissítés gomb](media/monitor-visually/refresh.png)

A hibakeresési műveletek eredményének megtekintéséhez válassza a **hibakeresés** lapot.

![Válassza az aktív hibakeresési futtatások megtekintése ikont.](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Tevékenységfuttatások monitorozása

Ha részletes képet szeretne kapni egy adott folyamat futtatásának egyes tevékenységeiről, kattintson a folyamat nevére.

![Tevékenységfuttatások megtekintése](media/monitor-visually/view-activity-runs.png)

A listanézet azokat a tevékenység-futtatásokat jeleníti meg, amelyek megfelelnek az egyes folyamatoknak. Vigye az egérmutatót az adott tevékenység futtatására, hogy a futtatásra vonatkozó információkat, például a JSON-bemenetet, a JSON-kimenetet és a tevékenységek részletes figyelési élményeit kapja meg.

![A SalesAnalyticsMLPipeline, majd a tevékenységek futtatására vonatkozó információk szerepelnek.](media/monitor-visually/activity-runs.png)

| **Oszlop neve** | **Leírás** |
| --- | --- |
| Tevékenységnév | A folyamaton belüli tevékenység neve |
| Művelettípus | A tevékenység típusa, például **Másolás**, **ExecuteDataFlow** vagy **AzureMLExecutePipeline** |
| Műveletek | Ikonok, amelyekkel megtekintheti a JSON bemeneti adatait, a JSON kimeneti adatait vagy a tevékenységekre vonatkozó részletes figyelési tapasztalatokat | 
| Futtatás indítása | A tevékenység futtatásának kezdési dátuma és időpontja (hh/nn/éééé, óó: PP: SS AM/PM) |
| Időtartam | Futtatás időtartama (óó: PP: SS) |
| Állapot | **Sikertelen**, **sikeres**, **folyamatban** vagy **megszakítva** |
| Integrációs modul | Integration Runtime a tevékenység futtatása |
| Felhasználói tulajdonságok | A tevékenység felhasználó által definiált tulajdonságai |
| Hiba | Ha a tevékenység nem sikerült, a futtatási hiba |
| Futtatási azonosító | A tevékenység futtatásának azonosítója |

Ha egy tevékenység nem sikerült, a hiba oszlopban látható ikonra kattintva a részletes hibaüzenet jelenik meg. 

![Ekkor megjelenik egy értesítés, amely tartalmazza a hibakódot, a hiba típusát és a hiba részleteit.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>A figyelni kívánt felhasználói tulajdonságok előléptetése

Minden folyamat tevékenység tulajdonságának előléptetése felhasználói tulajdonságként, hogy az a figyelő entitás legyen. Például előléptetheti a folyamat másolási tevékenységének **forrás** -és **cél** tulajdonságait felhasználói tulajdonságokként.

> [!NOTE]
> Felhasználói tulajdonságokként legfeljebb öt folyamat tevékenység-tulajdonságokat lehet előléptetni.

![Felhasználói tulajdonságok létrehozása](media/monitor-visually/promote-user-properties.png)

Miután létrehozta a felhasználói tulajdonságokat, megfigyelheti őket a figyelési lista nézeteiben.

![Oszlopok hozzáadása a felhasználói tulajdonságokhoz a tevékenység-futtatási listához](media/monitor-visually/choose-user-properties.png)

 Ha a másolási tevékenység forrása egy tábla neve, akkor a forrástábla neve a tevékenység futtatásához tartozó listanézet oszlopával figyelhető meg.

![Tevékenység-futtatási lista a felhasználói tulajdonságok oszlopaival](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Folyamatok és tevékenységek újrafuttatása

Ha egy olyan folyamatot szeretne újrafuttatni, amely korábban már futott a Start menüből, vigye a kurzort az adott folyamat futtatására, és válassza az **újra** lehetőséget. Ha több folyamatot választ ki, az **újra** gombra kattintva futtathatja az összeset.

![Folyamat újrafuttatása](media/monitor-visually/rerun-pipeline.png)

Ha újra szeretné futtatni egy adott ponttól kezdődően, ezt a tevékenység futtatása nézetből végezheti el. Válassza ki a kezdéshez használni kívánt tevékenységet, majd válassza az **újrafuttatás a tevékenységből** lehetőséget. 

![Tevékenység futtatásának újrafuttatása](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Újrafuttatás sikertelen tevékenységből

Ha egy tevékenység meghiúsul, időtúllépés vagy megszakítva, a folyamat a **sikertelen tevékenységből** való újrafuttatásával újrafuttathatja a folyamatot.

![Sikertelen művelet újrafuttatása](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Újrafuttatási előzmények megtekintése

A lista nézetben megtekintheti az összes folyamat futtatási előzményeit.

![Előzmények megtekintése](media/monitor-visually/rerun-history-1.png)

Egy adott folyamat futtatásához is megtekintheti az újrafuttatási előzményeket.

![Folyamat futtatási előzményeinek megtekintése](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Használat figyelése

A folyamat által használt erőforrásokat a Futtatás elem melletti fogyasztás ikonra kattintva tekintheti meg. 

![Képernyőkép, amely megmutatja, hol láthatja a folyamat által felhasznált erőforrásokat.](media/monitor-visually/monitor-consumption-1.png)

Az ikonra kattintva megnyithatja az adott folyamat által használt erőforrások felhasználási jelentését. 

![Használat figyelése](media/monitor-visually/monitor-consumption-2.png)

Ezeket az értékeket az [Azure díjszabási számológépében](https://azure.microsoft.com/pricing/details/data-factory/) csatlakoztathatja a folyamat futtatási költségének megbecsléséhez. További információ a Azure Data Factory díjszabásáról: a [díjszabás ismertetése](pricing-concepts.md).

> [!NOTE]
> A díjszabási számológép által visszaadott értékek becslést tartalmaznak. Nem tükrözi azt a pontos összeget, amelyet a Azure Data Factory számláz majd 

## <a name="gantt-views"></a>Gantt-nézetek

A Gantt-diagram olyan nézet, amely lehetővé teszi a futtatási előzmények megtekintését egy adott időtartományon belül. Ha átvált egy Gantt-nézetre, akkor az összes folyamat a sávok szerint csoportosított néven jelenik meg, a Futtatás időtartama szerint. A folyamaton létrehozott jegyzetek/címkék alapján is csoportosíthatja a csoportokat. A Gantt-nézet a tevékenység futtatási szintjén is elérhető.

![Példa Gantt-diagramra](media/monitor-visually/select-gantt.png)

A sáv hossza tájékoztatja a folyamat időtartamát. A sáv kiválasztásával további részleteket is megtudhat.

![Gantt-diagram időtartama](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Riasztások

Data Factory a támogatott mérőszámokra vonatkozó riasztásokat is kiemelheti. A   >  kezdéshez válassza a **riasztások figyelése & mérőszámok** lehetőséget a Data Factory figyelése lapon.

![A adatgyár-figyelő lapja](media/monitor-visually/start-page.png)

A szolgáltatás hét perces bevezetéséhez és bemutatásához tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Riasztások létrehozása

1.  Új riasztás létrehozásához válassza az **új riasztási szabály** lehetőséget.

    ![Új riasztási szabály gomb](media/monitor-visually/new-alerts.png)

1.  Adja meg a szabály nevét, és válassza ki a riasztás súlyosságát.

    ![A szabály nevének és súlyosságának mezői](media/monitor-visually/name-and-severity.png)

1.  Válassza ki a riasztási feltételeket.

    ![A célként megadott feltételekhez tartozó mező](media/monitor-visually/add-criteria-1.png)

    ![Képernyőkép, amely megmutatja, hogy a riasztási feltétel beállításához hol válasszon ki egy metrikát.](media/monitor-visually/add-criteria-2.png)

    ![Feltételek listája](media/monitor-visually/add-criteria-3.png)

    Riasztásokat hozhat létre különböző mérőszámokon, beleértve az ADF-entitások száma/mérete, a tevékenységek/folyamatok/trigger-futtatások, Integration Runtime (IR) CPU-kihasználtság/memória/csomópontok száma/üzenetsor, valamint a SSIS-csomagok végrehajtásához és a SSIS IR indítási/leállítási műveletekhez.

1.  Adja meg a riasztási logikát. Riasztást hozhat létre a kijelölt metrikához az összes folyamathoz és a kapcsolódó tevékenységekhez. Kiválaszthat egy adott tevékenység típusát, a tevékenység nevét, a folyamat nevét vagy a hiba típusát is.

    ![A riasztási logika konfigurálásának lehetőségei](media/monitor-visually/alert-logic.png)

1.  Az e-mailek, SMS-, leküldéses és hangalapú értesítések konfigurálása a riasztáshoz. Hozzon létre egy műveleti csoportot, vagy válasszon ki egy meglévőt a riasztási értesítésekhez.

    ![Az értesítések konfigurálásának beállításai](media/monitor-visually/configure-notification-1.png)

    ![Értesítések hozzáadásának beállításai](media/monitor-visually/configure-notification-2.png)

1.  Hozzon létre egy riasztási szabályt.

    ![Riasztási szabály létrehozásának lehetőségei](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Következő lépések

A folyamatok figyelésével és kezelésével kapcsolatos információkért tekintse meg a [folyamatok programozott figyelése és kezelése](./monitor-programmatically.md) című cikket.