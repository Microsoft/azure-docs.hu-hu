---
title: Folyamatok monitorozása és kezelése a Azure Portal és a PowerShell használatával
description: Megtudhatja, hogyan használhatja a Azure Portal és Azure PowerShell a létrehozott Azure-beli adatüzemek és-folyamatok figyelésére és felügyeletére.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 0744a7d915d6bee868b160abc29964d58947dd28
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104779630"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure Data Factory folyamatok monitorozása és kezelése a Azure Portal és a PowerShell használatával
> [!div class="op_single_selector"]
> * [Azure Portal/Azure PowerShell használata](data-factory-monitor-manage-pipelines.md)
> * [Figyelési és felügyeleti alkalmazás használata](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory folyamatok figyelése és kezelése](../monitor-visually.md)című témakört.

Ez a cikk a folyamatok figyelését, kezelését és hibakeresését ismerteti Azure Portal és PowerShell használatával.

> [!IMPORTANT]
> A monitoring & felügyeleti alkalmazás jobb támogatást nyújt az adatfolyamatok monitorozásához és kezeléséhez, valamint az esetleges problémák megoldásához. További információ az alkalmazás használatáról: [Data Factory folyamatok figyelése és felügyelete a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Az 1. verzió Azure Data Factory mostantól az új [Azure Monitor riasztási infrastruktúrát](../../azure-monitor/alerts/alerts-metric.md)használja. A régi riasztási infrastruktúra elavult. Ennek eredményeképpen az 1. verziójú adatüzemekhez konfigurált meglévő riasztások már nem működnek. A v1-es adatüzemek meglévő riasztásait a rendszer nem telepíti át automatikusan. Ezeket a riasztásokat újra létre kell hozni az új riasztási infrastruktúrán. Jelentkezzen be a Azure Portalba, és válassza a **figyelő** lehetőséget, hogy új riasztásokat hozzon létre a metrikák (például sikertelen futtatások vagy sikeres futtatások) számára az 1. verziójú adat-előállítók számára.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Folyamatok és tevékenységek állapotának ismertetése
A Azure Portal használatával a következőket teheti:

* Az adatgyárat diagramként tekintheti meg.
* Egy folyamat tevékenységeinek megtekintése.
* Bemeneti és kimeneti adatkészletek megtekintése.

Ez a szakasz azt is leírja, hogy az adatkészlet-szeletek hogyan alakulnak át az egyik állapotból egy másikba.   

### <a name="navigate-to-your-data-factory"></a>Navigáljon a saját adatgyárhoz
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a bal oldali menüben található **adatüzemek** elemre. Ha nem jelenik meg, kattintson a **További szolgáltatások >** elemre, majd kattintson az **intelligencia és Analitika** kategóriába tartozó **adat** -előállítók elemre.

   ![Az összes >-adatgyár tallózása](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. **Az adatüzemek** panelen válassza ki azt az adatelőállítót, amelyre kíváncsi.

    ![Adat-előállító kiválasztása](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Ekkor meg kell jelennie az adatelőállító kezdőlapjának.

   ![Adat-előállító panel](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Az adatgyár diagram nézete
Az adatok előállítójának **diagram** nézete egyetlen panelt biztosít az adatok előállítójának és eszközeinek figyeléséhez és kezeléséhez. Az adatgyár **diagram** nézetének megjelenítéséhez kattintson a **diagram** elemre az adatelőállító kezdőlapján.

![Diagramnézet](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

A nagyításhoz, a kicsinyítéshez, a nagyításhoz, a nagyításhoz 100%-ra, a diagram elrendezésének zárolására, valamint a folyamatok és adatkészletek automatikus elhelyezésére használható. Megtekintheti az adatbányászati adatokat is (azaz a kijelölt elemek felsőbb rétegbeli és alárendelt elemeinek megjelenítését).

### <a name="activities-inside-a-pipeline"></a>Folyamaton belüli tevékenységek
1. Kattintson a jobb gombbal a folyamatra, majd kattintson a folyamat **megnyitása** lehetőségre a folyamat összes tevékenységének megtekintéséhez, valamint a tevékenységekhez tartozó bemeneti és kimeneti adatkészletek megjelenítéséhez. Ez a funkció akkor hasznos, ha a folyamat több tevékenységet is tartalmaz, és szeretné megismerni egy adott folyamat működési irányát.

    ![Folyamat megnyitása menü](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. A következő példában egy másolási tevékenység jelenik meg a folyamaton egy bemenettel és egy kimenettel. 

    ![Folyamaton belüli tevékenységek](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. A bal felső sarokban található **adatfeldolgozó** hivatkozásra kattintva visszatérhet az adatelőállító kezdőlapján is.

    ![Visszatérés a refactoryba](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Az egyes tevékenységek állapotának megtekintése egy folyamaton belül
A tevékenységek aktuális állapotát úgy tekintheti meg, hogy megtekinti a tevékenység által létrehozott adathalmazok állapotát.

Ha duplán kattint a **OutputBlobTable** a **diagramon**, megtekintheti a különböző tevékenységek által létrehozott összes szeletet egy folyamaton belül. Láthatja, hogy a másolási tevékenység sikeresen futott az elmúlt nyolc órában, és **kész** állapotban hozta létre a szeleteket.  

![A folyamat állapota](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Az adat-előállítóban található adatkészlet-szeletek a következő állapotok egyikével rendelkezhetnek:

<table>
<tr>
    <th align="left">Állapot</th><th align="left">Alállapotot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozó</td><td>ScheduleTime</td><td>A szelet futtatásához szükséges idő.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>A felsőbb rétegbeli függőségek nem állnak készen.</td>
</tr>
<tr>
<td>ComputeResources</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Az összes tevékenységi példány más szeletek futtatásával van elfoglalva.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szüneteltetve van, és a tevékenység folytatása előtt nem futtathatók a szeletek.</td>
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
<td>A szelet feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Sikertelen</td><td>Időtúllépés</td><td>A tevékenység végrehajtása hosszabb időt vett igénybe, mint amit a tevékenység engedélyez.</td>
</tr>
<tr>
<td>Megszakítva</td><td>A szeletet a felhasználói művelet megszakította.</td>
</tr>
<tr>
<td>Érvényesítés</td><td>Az érvényesítés sikertelen volt.</td>
</tr>
<tr>
<td>-</td><td>A szelet létrehozása és/vagy ellenőrzése nem sikerült.</td>
</tr>
<td>Kész</td><td>-</td><td>A szelet készen áll a felhasználásra.</td>
</tr>
<tr>
<td>Kimarad</td><td>Nincsenek</td><td>A szelet feldolgozása nem történik meg.</td>
</tr>
<tr>
<td>Nincsenek</td><td>-</td><td>Egy olyan szelet, amely más állapottal is létezik, de alaphelyzetbe lett állítva.</td>
</tr>
</table>



A szeletek részleteit úgy tekintheti meg, ha rákattint egy szeletre a **legutóbb frissített szeletek** panelen.

![Szelet részletei](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Ha a szelet többször is végre lett hajtva, több sor jelenik meg a **tevékenység-futtatások** listájában. A tevékenységek futtatásával kapcsolatos részleteket a **tevékenység** futtatások listájának Futtatás bejegyzésére kattintva tekintheti meg. A lista megjeleníti az összes naplófájlt, valamint egy hibaüzenetet, ha van ilyen. Ez a funkció hasznos lehet a naplók megtekintésére és hibakeresésére anélkül, hogy el kellene hagynia az adatgyárat.

![Tevékenységfuttatás részletei](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Ha a szelet nem **üzemkész** állapotban van, láthatja, hogy a felsőbb rétegbeli szeletek nem állnak készen, és blokkolja az aktuális szeletet a **nem kész listán szereplő felsőbb rétegbeli szeleteken** . Ez a funkció akkor hasznos, ha a szelet **várakozási** állapotban van, és meg szeretné ismerni, hogy milyen felsőbb rétegbeli függőségek várnak a szeletre.

![Felsőbb rétegbeli szeletek, amelyek nem állnak készen](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Adatkészlet állapotának diagramja
Miután üzembe helyezte az adat-előállítót, és a folyamatok érvényes aktív időtartammal rendelkeznek, az adatkészlet-szeletek az egyik állapotból a másikba térnek át. A szelet állapota jelenleg a következő állapot diagramot követi:

![Állapot diagramja](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Az adatkészlet állapotának átváltási folyamata az adat-előállítóban a következő: folyamatban lévő > várakozási idő/folyamatban (érvényesítés) – > Ready/failed.

A szelet **várakozási** állapotba kerül, és arra vár, hogy a végrehajtás előtt meg lehessen felelni az előfeltételeknek. Ezután a tevékenység elindítja a végrehajtást, és a szelet **folyamatban** állapotba kerül. A tevékenység végrehajtása sikeres vagy sikertelen lehet. A szelet **készként** vagy **sikertelenként** van megjelölve a végrehajtás eredménye alapján.

Alaphelyzetbe állíthatja a szeletet, hogy visszalépjen a **Ready** vagy a **failed** állapotból a **várakozási** állapotba. Azt is megteheti, hogy **kihagyja** a szelet állapotát, ami megakadályozza a tevékenység végrehajtását, és nem dolgozza fel a szeletet.

## <a name="pause-and-resume-pipelines"></a>Folyamatok szüneteltetése és folytatása
A folyamatokat Azure PowerShell használatával kezelheti. Például szüneteltetheti és folytathatja a folyamatokat Azure PowerShell-parancsmagok futtatásával. 

> [!NOTE] 
> A diagram nézet nem támogatja a folyamatok szüneteltetését és folytatását. Ha felhasználói felületet szeretne használni, használja az alkalmazás figyelését és felügyeletét. További információ az alkalmazás használatáról: [Data Factory folyamatok figyelése és kezelése a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md) című cikk. 

A **AzDataFactoryPipeline PowerShell-** parancsmag használatával szüneteltetheti vagy felfüggesztheti a folyamatokat. Ez a parancsmag akkor lehet hasznos, ha nem kívánja futtatni a folyamatokat, amíg nem rögzíti a problémát. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Például:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

A probléma a folyamattal való javítása után a következő PowerShell-parancs futtatásával folytathatja a felfüggesztett folyamatot:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Például:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Folyamatok hibakeresése
A Azure Data Factory sokoldalú funkciókat biztosít a folyamatok hibakereséséhez és hibaelhárításához a Azure Portal és a Azure PowerShell használatával.

> [!NOTE] 
> A figyelési & felügyeleti alkalmazással sokkal egyszerűbb a hibák troubleshot. További információ az alkalmazás használatáról: [Data Factory folyamatok figyelése és kezelése a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md) című cikk. 

### <a name="find-errors-in-a-pipeline"></a>Hibák keresése egy folyamatban
Ha a tevékenység futtatása meghiúsul egy folyamat során, a folyamat által előállított adatkészlet hibás állapotba kerül, a hiba miatt. A következő módszerekkel végezheti el a hibakeresést és a hibák elhárítását Azure Data Factoryban.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Hibák hibakeresése a Azure Portal használatával
1. A **tábla** panelen kattintson arra a problémás szeletre, amelynél az **állapot** értéke **sikertelen**.

   ![Táblázat panel a probléma szelettel](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Az **adatszelet** panelen kattintson a sikertelen művelet futtatására.

   ![Adatszelet hibával](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. A **tevékenység futtatása részletek** panelen letöltheti a HDInsight-feldolgozáshoz társított fájlokat. A hiba részleteit tartalmazó naplófájl letöltéséhez kattintson az állapot/stderr **letöltése** elemre.

   ![A tevékenység futtatási részletei panel hibával](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Hiba hibakeresése a PowerShell használatával
1. Indítsa el a **PowerShellt**.
2. Futtassa a **Get-AzDataFactorySlice** parancsot a szeletek és állapotuk megjelenítéséhez. Egy olyan szeletnek kell megjelennie, amelynek állapota **sikertelen**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Például:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Cserélje le a **StartDateTime** a folyamat kezdési idejére. 
3. Most futtassa a **Get-AzDataFactoryRun** parancsmagot a szelet tevékenységi futtatásával kapcsolatos részletekért.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Például:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    A StartDateTime értéke az előző lépésben feljegyzett hiba/probléma szelet kezdési időpontja. A dátum-és időpontot idézőjelek közé kell foglalni.
4. A kimenetnek a következőhöz hasonló hibával kell megjelennie:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. A **Save-AzDataFactoryLog** parancsmagot futtathatja a kimenetből látható azonosító értékkel, és letöltheti a naplófájlokat a **-DownloadLogsoption** for the parancsmag használatával.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Folyamatban lévő hibák újrafuttatása

> [!IMPORTANT]
> A figyelési & felügyeleti alkalmazással egyszerűbb a hibák elhárítása és a sikertelen szeletek újrafuttatása. További információ az alkalmazás használatáról: [Data Factory folyamatok figyelése és felügyelete a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
A folyamatok hibáinak elhárítása és hibakeresése után újrafuttathatja a hibákat, ha a hiba szeletre navigál, és a parancssáv **Futtatás** gombjára kattint.

![Sikertelen szelet újrafuttatása](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Abban az esetben, ha a szelet házirend-meghibásodás miatt sikertelen volt (például ha az adatmennyiség nem érhető el), a parancssorban a **validate (érvényesítés** ) gombra kattintva javítsa ki a hibát, és ismételje meg az érvényesítést.

![Hibajavítások és érvényesítés](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell használatával
A hibákat a **set-AzDataFactorySliceStatus** parancsmag használatával futtathatja újra. A parancsmag szintaxisát és egyéb részleteit lásd a [set-AzDataFactorySliceStatus](/powershell/module/az.datafactory/set-azdatafactoryslicestatus) témakörben.

**Példa**

A következő példa a "DAWikiAggregatedData" tábla összes szeletének állapotát a "Waiting" értékre állítja a "WikiADF" Azure-beli adat-előállítóban.

A "frissítés típusa" értéke "UpstreamInPipeline", ami azt jelenti, hogy a táblához tartozó egyes szeletek állapota és az összes függő (felsőbb rétegbeli) tábla "Waiting" értékre van állítva. A paraméter másik lehetséges értéke az "egyéni".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Riasztások létrehozása a Azure Portalban

1.  Jelentkezzen be a Azure Portalba, és válassza a **figyelő-> riasztások** lehetőséget a riasztások lap megnyitásához.

    ![Nyissa meg a riasztások lapot.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Új riasztás létrehozásához válassza az **+ új riasztási szabály** lehetőséget.

    ![Új riasztás létrehozása](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Adja meg a **riasztási feltételt**. (Ügyeljen arra, hogy a **szűrés erőforrás típusa** mezőben válassza az **adatüzemek** elemet.) Megadhatja a **dimenziók** értékeit is.

    ![A riasztási feltétel meghatározása – cél kiválasztása](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![A riasztási feltétel meghatározása – riasztási feltételek hozzáadása](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![A riasztási feltétel meghatározása – riasztási logika hozzáadása](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Adja meg a **riasztás részleteit**.

    ![A riasztás részleteinek megadása](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  A **műveleti csoport** definiálása.

    ![A műveleti csoport definiálása – új műveleti csoport létrehozása](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![A műveleti csoport tulajdonságainak megadása](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![A műveleti csoport definiálása – új műveleti csoport létrehozva](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Egy adatgyár áthelyezése másik erőforráscsoporthoz vagy előfizetésbe
Az adatgyárat egy másik erőforráscsoporthoz vagy egy másik előfizetésbe helyezheti át az adatelőállító kezdőlapján található parancssáv **áthelyezése** gombbal.

![Az adatfeldolgozó áthelyezése](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

A kapcsolódó erőforrásokat (például az adat-előállítóhoz társított riasztásokat) is áthelyezheti az adat-előállítóval együtt.

![Erőforrások áthelyezése párbeszédpanel](./media/data-factory-monitor-manage-pipelines/MoveResources.png)