---
title: Az adatfolyamatok monitorozása és kezelése – Azure
description: Ismerje meg, hogyan használható a figyelési és felügyeleti alkalmazás az Azure-beli adatüzemek és-folyamatok figyeléséhez és kezeléséhez.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 3a9eb73aab381f90ae51300bff1250348916c2ab
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779596"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Azure Data Factory folyamatok monitorozása és kezelése a figyelési és felügyeleti alkalmazás használatával
> [!div class="op_single_selector"]
> * [Azure Portal/Azure PowerShell használata](data-factory-monitor-manage-pipelines.md)
> * [Figyelési és felügyeleti alkalmazás használata](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory folyamatok figyelése és kezelése](../monitor-visually.md)című témakört.

Ez a cikk azt ismerteti, hogyan használható a figyelési és felügyeleti alkalmazás a Data Factory folyamatok figyeléséhez, kezeléséhez és hibakereséséhez. Az alkalmazás használatának első lépései a következő videóban olvashatók:

> [!NOTE]
> Előfordulhat, hogy a videóban látható felhasználói felület nem egyezik pontosan a portálon láthatóval. Ez valamivel régebbi, de a fogalmak változatlanok maradnak. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>A figyelési és felügyeleti alkalmazás elindítása
A figyelő és a felügyeleti alkalmazás elindításához kattintson a **figyelő & kezelés** csempére az adatfeldolgozó **Data Factory** paneljén.

![Figyelési csempe a Data Factory kezdőlapján](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

A figyelési és felügyeleti alkalmazást külön ablakban kell megnyitnia.  

![Figyelési és felügyeleti alkalmazás](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Ha úgy látja, hogy a webböngésző az "engedélyezés..." helyen található, törölje a jelet a **harmadik féltől származó cookie-k és a hely adattípusának tiltása** jelölőnégyzetből – vagy hagyja bejelölve, hozzon létre kivételt a **login.microsoftonline.com**, majd próbálja meg újból megnyitni az alkalmazást.


A középső ablaktáblán a tevékenység Windows listájában megjelenik egy tevékenység ablak a tevékenységek minden egyes futtatásához. Ha például az ütemezve van, hogy az öt órán át óránként fusson, öt, öt adatszelethez társított tevékenység jelenik meg. Ha nem látja a tevékenység ablakait a lenti listában, tegye a következőket:
 
- Frissítse a **kezdési időt** és a **befejezési időpontot** , hogy az megfeleljen a folyamat kezdési és befejezési idejének, majd kattintson az **alkalmaz** gombra.  
- A tevékenység Windows-listája nem frissül automatikusan. Kattintson a **refresh (frissítés** ) gombra a **tevékenység Windows** -listájának eszköztárán.  

Ha nem rendelkezik Data Factory alkalmazással a lépések teszteléséhez, tegye az oktatóanyagot: [adatok másolása blob Storageból SQL Database Data Factory használatával](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>A figyelési és felügyeleti alkalmazás megismerése
A bal oldalon három lap található: **erőforrás-kezelő**, **figyelési nézetek** és **riasztások**. Alapértelmezés szerint az első lap (**erőforrás-kezelő**) van kiválasztva.

### <a name="resource-explorer"></a>erőforrás-kezelő
A következőket látja:

* A erőforrás-kezelő **fanézet** a bal oldali ablaktáblán.
* A **diagram nézet** a középső ablaktáblán felül
* A középső ablaktábla alján található **tevékenység-Windows** lista.
* A **Tulajdonságok**, a **tevékenység ablak-kezelője** és a **szkript** lapjai a jobb oldali ablaktáblán.

Erőforrás-kezelő az adat-előállító összes erőforrását (folyamatait, adatkészleteit, társított szolgáltatásait) látja fanézetben. Amikor kiválaszt egy objektumot a erőforrás-kezelőban:

* A társított Data Factory entitás ki van emelve a diagram nézetben.
* A [társított tevékenységek ablakait](data-factory-scheduling-and-execution.md) a rendszer alul található tevékenység Windows-listájában emeli ki.  
* A kijelölt objektum tulajdonságai a jobb oldali ablaktábla Tulajdonságok ablak láthatók.
* A kiválasztott objektum JSON-definíciója látható, ha van ilyen. Például: egy társított szolgáltatás, egy adatkészlet vagy egy folyamat.

![erőforrás-kezelő](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Tekintse meg az [ütemezési és végrehajtási](data-factory-scheduling-and-execution.md) cikket, amely részletesen ismerteti a tevékenységekkel kapcsolatos Windows-információkat.

### <a name="diagram-view"></a>Diagramnézet
Egy adatelőállító diagram nézete egyetlen üvegtábla-panelt biztosít az adatok előállítójának és eszközeinek figyeléséhez és kezeléséhez. Ha a diagram nézetben kiválaszt egy Data Factory entitást (adatkészlet/folyamat):

* A Refactory entitás a fanézetben van kiválasztva.
* A társított tevékenység ablakai a tevékenység Windows listán vannak kiemelve.
* A kijelölt objektum tulajdonságai a Tulajdonságok ablak láthatók.

Ha a folyamat engedélyezve van (nem szüneteltetett állapotban van), akkor zöld vonallal jelenik meg:

![Futó folyamat](./media/data-factory-monitor-manage-app/PipelineRunning.png)

A folyamat szüneteltetéséhez, folytatásához vagy leállításához válassza ki azt a diagram nézetben, és használja a parancssáv gombait.

![Szüneteltetés/folytatás a parancssorban](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
A diagram nézetben három parancssáv gomb található a folyamathoz. A második gomb használatával szüneteltetheti a folyamatot. A felfüggesztés nem állítja le a jelenleg futó tevékenységeket, és lehetővé teszi, hogy a befejezés után folytassanak. A harmadik gomb szünetelteti a folyamatot, és leállítja a meglévő végrehajtási tevékenységeket. Az első gomb folytatja a folyamatot. Ha a folyamat szüneteltetve van, a folyamat színe megváltozik. Egy szüneteltetett folyamat például a következő képen látható módon jelenik meg: 

![A folyamat szüneteltetve](./media/data-factory-monitor-manage-app/PipelinePaused.png)

A CTRL billentyű használatával több folyamat is kijelölhető. A parancssáv gombjaival egyszerre több folyamat is szüneteltethető vagy folytatható.

Azt is megteheti, hogy a jobb gombbal egy folyamatra kattint, és kiválasztja a kívánt folyamat felfüggesztését, folytatását vagy megszakítását. 

![A folyamat helyi menüje](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kattintson a **folyamat megnyitása** lehetőségre a folyamat összes tevékenységének megtekintéséhez. 

![Folyamat megnyitása menü](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

A megnyitott folyamat nézetben a folyamat összes tevékenysége látható. Ebben a példában csak egy tevékenység van: másolási tevékenység. 

![Folyamat megnyitva](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Ha vissza szeretne térni az előző nézetre, kattintson a felső navigációs menü adatelőállító nevére.

Ha a folyamat nézetben kiválaszt egy kimeneti adatkészletet, vagy ha az egérmutatót a kimeneti adatkészleten helyezi át, az adott adatkészlet tevékenységhez tartozó Windows előugró ablaka jelenik meg.

![Tevékenység Windows előugró ablak](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

A tevékenység ablakára kattintva megtekintheti a részleteket a jobb oldali ablaktábla **Tulajdonságok** ablakában.

![Tevékenység ablakának tulajdonságai](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

A jobb oldali ablaktáblán váltson a **tevékenység ablak Explorer** lapjára a további részletek megtekintéséhez.

![Képernyőkép, amely bemutatja, hogyan lehet hozzáférni a tevékenység ablak Explorer lapjához.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

A **kísérletek** szakaszban a tevékenységekhez tartozó egyes Futtatási kísérletek **feloldott változói** is láthatók.

![Megoldott változók](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Váltson a **parancsfájl** lapra, és tekintse meg a kiválasztott objektum JSON-parancsfájljának definícióját.   

![Parancsfájl lap](./media/data-factory-monitor-manage-app/ScriptTab.png)

Három helyen láthatja a tevékenységek ablakait:

* A tevékenység Windows előugró ablaka a diagram nézetben (középső ablaktábla).
* A jobb oldali ablaktábla tevékenység ablakának kezelője.
* Az alsó ablaktáblán található tevékenység Windows-lista.

A tevékenység a Windows előugró ablak és a tevékenység ablakban az előző hétre és a következő hétre görgetheti a bal és a jobb nyilat.

![Tevékenység ablak-tallózó bal/jobb nyíl](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

A diagram nézet alján láthatja a következő gombokat: nagyítás, kicsinyítés, nagyítás a mérethez, nagyítás 100%, zárolási elrendezés. A **zárolási elrendezés** gomb megakadályozza, hogy a diagram nézetben véletlenül áthelyezze a táblákat és a folyamatokat. Alapértelmezés szerint ez a beállítás. Kikapcsolhatja és áthelyezheti az entitásokat a diagramon. Ha kikapcsolja, az utolsó gomb használatával automatikusan elhelyezheti a táblákat és a folyamatokat. Az egér kerekének használatával is nagyíthatja és kicsinyítheti is.

![Diagram nézet nagyítási parancsai](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Tevékenységablakok lista
A középső ablaktábla alján található tevékenység Windows-lista megjeleníti a erőforrás-kezelő vagy a diagram nézetben kiválasztott adatkészlet összes tevékenység-ablakát. Alapértelmezés szerint a lista csökkenő sorrendben van, ami azt jelenti, hogy a legfelső szintű tevékenység ablak jelenik meg.

![Tevékenységablakok lista](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ez a lista nem frissül automatikusan, ezért az eszköztáron található frissítés gombbal manuálisan frissítheti.  

A tevékenység-Windows a következő állapotok egyike lehet:

<table>
<tr>
    <th align="left">Állapot</th><th align="left">Részállapot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozó</td><td>ScheduleTime</td><td>A tevékenység ablak futtatásának ideje.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>A felsőbb rétegbeli függőségek nem állnak készen.</td>
</tr>
<tr>
<td>ComputeResources</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Az összes tevékenységi példány foglalt, és más tevékenység-ablakokat futtat.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szüneteltetve van, és nem futtathatja a tevékenység ablakait egészen addig, amíg újra nem fut.</td>
</tr>
<tr>
<td>Ismét</td><td>A tevékenység végrehajtásának újrapróbálása folyamatban van.</td>
</tr>
<tr>
<td>Érvényesítés</td><td>Az érvényesítés még nincs elindítva.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Az érvényesítés az újrapróbálkozásra vár.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Folyamatban</td><td>Érvényesítése</td><td>Az érvényesítés folyamatban van.</td>
</tr>
<td>-</td>
<td>A tevékenység ablak feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Sikertelen</td><td>Időtúllépés</td><td>A tevékenység végrehajtása hosszabb időt vett igénybe, mint amit a tevékenység engedélyez.</td>
</tr>
<tr>
<td>Megszakítva</td><td>A felhasználói művelet megszakította a tevékenység ablakát.</td>
</tr>
<tr>
<td>Érvényesítés</td><td>Az érvényesítés sikertelen volt.</td>
</tr>
<tr>
<td>-</td><td>Nem sikerült létrehozni vagy érvényesíteni a tevékenység ablakát.</td>
</tr>
<td>Kész</td><td>-</td><td>A tevékenység ablak készen áll a felhasználásra.</td>
</tr>
<tr>
<td>Kimarad</td><td>-</td><td>A tevékenység ablaka nem lett feldolgozva.</td>
</tr>
<tr>
<td>Nincsenek</td><td>-</td><td>Eltérő állapotú, de alaphelyzetbe állított tevékenységi időszak.</td>
</tr>
</table>


Ha a lista egy tevékenység ablakára kattint, a **tevékenység a Windows Intézőben** vagy a jobb oldalon található **Tulajdonságok** ablakban tekintheti meg.

![Képernyőkép, amely bemutatja, hogyan lehet megtekinteni a tevékenység ablakának részleteit.](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Frissítési tevékenység ablakai
A részletek nem frissülnek automatikusan, ezért a parancssorban használja a frissítés gombot (a második gomb) a tevékenység Windows-listájának manuális frissítéséhez.  

### <a name="properties-window"></a>Tulajdonságok ablak
A Tulajdonságok ablak a figyelési és felügyeleti alkalmazás jobb szélső ablaktábláján található.

![Tulajdonságok ablak](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Megjeleníti a erőforrás-kezelő (fanézet), a diagram nézet vagy a tevékenység Windows-listájában kiválasztott elem tulajdonságait.

### <a name="activity-window-explorer"></a>Activity Window Explorer
A **tevékenység ablak Intéző** ablak a figyelési és felügyeleti alkalmazás jobb szélső ablaktábláján található. Megjeleníti a tevékenység Windows előugró ablakban vagy a tevékenység Windows-listán kiválasztott tevékenység ablakának részleteit.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Átválthat egy másik tevékenység ablakába úgy, hogy rákattint a felül található naptár nézetben. A felül található bal nyíl/jobbra mutató nyilat is használhatja az előző hét vagy a következő hét tevékenységi időszakának megtekintéséhez.

Az alsó ablaktáblán található eszköztárgombok használatával újra futtathatja a tevékenység ablakát, vagy frissítheti a részleteket a panelen.

### <a name="script"></a>Script
A **parancsfájl** lapon megtekintheti a kijelölt Data Factory entitás (társított szolgáltatás, adatkészlet vagy folyamat) JSON-definícióját.

![Parancsfájl lap](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Rendszernézetek használata
A figyelési és felügyeleti alkalmazás olyan előre elkészített rendszernézeteket tartalmaz (**legutóbbi tevékenységek**, Windows, **sikertelen tevékenységek** Windows, **folyamatban lévő tevékenységek Windows**), amelyekkel megtekintheti a legutóbbi/sikertelen/folyamatban lévő tevékenységek windowsját a saját adatelőállítója számára.

Kattintson rá a bal oldali **figyelési nézetek** lapra.

![Figyelési nézetek lap](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Jelenleg három rendszernézet támogatott. Válassza ki azt a lehetőséget, hogy megjelenjenek a legutóbbi tevékenységek Windows, sikertelen tevékenység Windows vagy folyamatban lévő tevékenységek Windows a tevékenység Windows-listájában (a középső ablaktábla alján).

Ha a **Legutóbbi tevékenység Windows** lehetőséget választja, a legutóbbi **kísérlet időpontjának** csökkenő sorrendjében láthatja az összes legutóbbi tevékenység-időablakot.

A **sikertelen tevékenység Windows** nézet használatával megtekintheti a listában szereplő összes sikertelen tevékenység ablakát. A listából válassza ki a meghibásodott tevékenység ablakát, és tekintse meg a **Tulajdonságok** ablakban vagy a **tevékenység ablak Explorerben** a részleteket. A sikertelen tevékenységek ablakának naplóit is letöltheti.

## <a name="sort-and-filter-activity-windows"></a>Tevékenységek rendezése és szűrése a Windowsban
Módosítsa a parancssáv **indítási** és **befejezési** időpontjának beállításait a tevékenység-ablakok szűréséhez. Miután módosította a kezdési időt és a befejezési időpontot, kattintson a befejezési időpont melletti gombra a tevékenység Windows-listájának frissítéséhez.

![Kezdési és befejezési idő](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Jelenleg az összes idő UTC formátumban van a figyelési és felügyeleti alkalmazásban.
>
>

A **tevékenység Windows listán** kattintson az oszlop nevére (például: állapot).

![Tevékenység Windows-lista oszlop menü](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Az lehetőségek érhetők el:

* Növekvő sorrendbe rendezés.
* Rendezés csökkenő sorrendben.
* Szűrés egy vagy több érték alapján (Ready, waited stb.).

Amikor szűrőt ad meg egy oszlophoz, az adott oszlophoz engedélyezett szűrő gomb jelenik meg, amely azt jelzi, hogy az oszlopban szereplő értékek szűrve vannak.

![Szűrés a tevékenységhez tartozó Windows-lista egy oszlopán](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

A szűrők törléséhez használhatja ugyanezt az előugró ablakot. Ha törölni szeretné a tevékenység Windows-lista összes szűrőjét, kattintson a parancssáv törlés szűrő gombjára.

![Az összes szűrő törlése a tevékenység Windows-listájában](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Batch-műveletek végrehajtása
### <a name="rerun-selected-activity-windows"></a>Kijelölt tevékenységek újrafuttatása Windows
Válassza ki a tevékenység ablakát, kattintson az első parancssáv gombján látható lefelé mutató nyílra, **és válassza az** újrafuttatás az újrafuttatással a  /  **folyamaton** keresztül lehetőséget. Ha kiválasztja az **Újrafuttatás a felfelé a folyamaton** lehetőséget, akkor az összes felsőbb rétegbeli tevékenység ablakát is Újrafuttatja.
    ![Tevékenység ablakának újrafuttatása](./media/data-factory-monitor-manage-app/ReRunSlice.png)

A listában több tevékenység ablak is kijelölhető, majd újra futtathatja őket. Előfordulhat, hogy a tevékenység ablakait az állapot alapján kell szűrni (például: **sikertelen**) –, majd újra kell futtatnia a sikertelen tevékenység időpontját, miután kijavította a tevékenység Windows-meghibásodását okozó problémát. Tekintse meg a következő szakaszt, amely részletesen ismerteti a listában szereplő tevékenységek Windows rendszerbeli szűrését.  

### <a name="pauseresume-multiple-pipelines"></a>Több folyamat szüneteltetése/folytatása
A CTRL billentyű lenyomásával több folyamat is kijelölhető. Az alábbi képen látható vörös négyszögben a parancssáv gombjait is használhatja a szüneteltetéshez/folytatáshoz.

![Szüneteltetés/folytatás a parancssorban](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
