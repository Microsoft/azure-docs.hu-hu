---
title: Ütemterv-eseményindítók létrehozása a Azure Data Factoryban
description: Megtudhatja, hogyan hozhat létre olyan triggert Azure Data Factoryban, amely ütemezett folyamatokat futtat.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 3673dd9eba717d2bdb569b4248936bbb59a8eae7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387580"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Egy ütemezett folyamatot futtató trigger létrehozása
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az ütemezett triggerről és az ütemezett triggerek létrehozásának, indításának és figyelésének lépéseiről nyújt információkat. Más típusú eseményindítók esetén lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

Az ütemezések eseményindítójának létrehozásakor meg kell adnia egy ütemtervet (kezdő dátum, ismétlődés, befejezési dátum stb.) az eseményindítóhoz, és társíthat egy folyamathoz. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

A következő szakaszokban ismertetjük az ütemterv-triggerek különböző módokon történő létrehozásának lépéseit. 

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete
Létrehozhat egy **ütemezett triggert** egy folyamat rendszeres időközönkénti futtatásához (óránként, naponta stb.). 

> [!NOTE]
> A folyamat létrehozásával, valamint a folyamat futtatásával és figyelésével kapcsolatos ütemezett triggerek teljes áttekintését a következő témakörben tekintheti meg: gyors útmutató [: adatelőállító létrehozása Data Factory felhasználói felület használatával](quickstart-create-data-factory-portal.md).

1. Váltson a **Szerkesztés** lapra, amely egy ceruza szimbólummal jelenik meg. 

    ![Váltás a Szerkesztés lapra](./media/how-to-create-schedule-trigger/switch-edit-tab.png)

1. Válassza az **aktiválás** lehetőséget a menüben, majd válassza az **új/szerkesztés** lehetőséget. 

    ![Új eseményindító menü](./media/how-to-create-schedule-trigger/new-trigger-menu.png)

1. Az **Eseményindítók hozzáadása** lapon válassza az **eseményindító kiválasztása...**, majd az **+ új** lehetőséget. 

    ![Eseményindítók hozzáadása – új eseményindító](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)

1. Az **új trigger** oldalon hajtsa végre a következő lépéseket: 

    1. Ellenőrizze, hogy be van-e jelölve az **ütemterv** a **típushoz**.
    1. A **kezdő dátumhoz tartozó trigger kezdő időpontjának** és dátumának megadása. Alapértelmezés szerint az egyezményes világidő (UTC) szerint az aktuális dátum/idő értékre van állítva.
    1. Itt adhatja meg azt az időzónát, amelyben a trigger létre lesz hozva. Az időzóna beállítása a **kezdő dátumra**, a **befejezési dátumra** és az **ütemezett végrehajtási** időpontokra lesz érvényes a speciális ismétlődési beállításokban. Az időzóna-beállítás módosítása nem fogja automatikusan módosítani a kezdő dátumot. Győződjön meg arról, hogy a kezdő dátum helyes a megadott időzónában. Vegye figyelembe, hogy az eseményindító ütemezett végrehajtási ideje a kezdő dátum után fog megjelenni (Győződjön meg róla, hogy a kezdési dátum a 1minute kisebb, mint a végrehajtás időpontjában, a folyamat a következő ismétlődéskor aktiválódik). 

        > [!NOTE]
        > A nyári időszámítást megfigyelő időzónák esetében az aktiválási idő automatikusan igazodik az év kétszeri változásához. Ha ki szeretné kapcsolni a nyári mentési változást, válasszon olyan időzónát, amely nem figyeli a nyári időmegtakarítást, például: UTC

    1. Az eseményindító **ismétlődésének** megadása. Válassza ki az egyik értéket a legördülő listából (percenként, óránként, naponta, hetente és havonta). Adja meg a szorzót a szövegmezőben. Ha például azt szeretné, hogy a trigger 15 percenként egyszer fusson, válassza a **percenként** lehetőséget, majd a szövegmezőbe írja be a **15** értéket. 
    1. A befejezési dátum időpontjának megadásához válassza a **befejezési dátum megadása** lehetőséget, majd adja meg _a végződik_ lehetőséget, majd kattintson **az OK gombra**. Minden egyes folyamatfuttatás költséggel jár. Ha teszteli, érdemes lehet biztosítani, hogy a folyamat csak néhány alkalommal induljon el. Arról is győződjön meg, hogy elegendő idő áll rendelkezésre a folyamat futtatásához a közzététel időpontja és a befejezés időpontja között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem.

        ![Eseményindító-beállítások](./media/how-to-create-schedule-trigger/trigger-settings-01.png)

        ![Trigger beállításai a befejezési dátumhoz](./media/how-to-create-schedule-trigger/trigger-settings-02.png)

1. Az **új trigger** ablakban válassza az **Igen** lehetőséget az **aktivált** lehetőségnél, majd kattintson az **OK gombra**. Ezt a jelölőnégyzetet követve inaktiválhatja az triggert később. 

    ![Eseményindító-beállítások – Tovább gomb](./media/how-to-create-schedule-trigger/trigger-settings-next.png)

1. Az **új trigger** ablakban tekintse át a figyelmeztető üzenetet, majd kattintson az **OK gombra**.

    ![Eseményindító-beállítások – Befejezés gomb](./media/how-to-create-schedule-trigger/new-trigger-finish.png)

1. Válassza az **összes közzététele** lehetőséget a módosítások Data Factory való közzétételéhez. Amíg nem teszi közzé a Data Factory módosításait, az trigger nem indítja el a folyamat futtatását. 

    ![Közzététel gomb](./media/how-to-create-schedule-trigger/publish-2.png)

1. Váltson a bal oldali **folyamat-futtatások** lapra, majd a lista frissítéséhez kattintson a **frissítés** elemre. Ekkor megjelenik az ütemezett trigger által aktivált folyamat futtatása. Figyelje meg az **Aktiválva a következő alapján** oszlop értékeit. Ha az **aktiválás most** lehetőséget választja, a listában megjelenik a manuális trigger futtatása. 

    ![Aktivált futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)

1. Váltson az **trigger futtatásának**  \  **ütemezett** nézetére. 

    ![Eseményindító-futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebből a szakaszból megtudhatja, hogyan használhatja a Azure PowerShellt az ütemterv-triggerek létrehozásához, elindításához és figyeléséhez. A minta működésének megtekintéséhez először tekintse át a gyors üzembe helyezési útmutatót [: hozzon létre egy adatgyárat Azure PowerShell használatával](quickstart-create-data-factory-powershell.md). Ezután adja hozzá a következő kódot a Main metódushoz, amely egy 15 percenként futó ütemezett triggert hoz létre és indít el. Az trigger társítva van egy **Adfv2QuickStartPipeline** nevű folyamattal, amelyet a rövid útmutató részeként hoz létre.

1. Hozzon létre egy **MyTrigger.js** nevű JSON-fájlt a C:\ADFv2QuickStartPSH\ mappában a következő tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt állítsa be az időponthoz **tartozó elem értékét** az aktuális UTC-időre. Állítsa a **befejezési** elem értékét egy órára az aktuális UTC időpontnál.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00Z",
                    "endTime": "2017-12-08T01:00:00Z",
                    "timeZone": "UTC"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    A JSON-kódrészletben:
    - Az trigger **Type** elemének értéke "ScheduleTrigger".
    - A **Frequency** elem "minute" értékre van állítva, az **intervallum** elem pedig 15. Ennek megfelelően a trigger 15 percenként futtatja a folyamatot a kezdő és a záró időpont között.
    - Az **timeZone** elem azt az időzónát adja meg, amelyben az trigger létre lett hozva. Ez a beállítás a **kezdő** és **a záró időpont is** befolyásolja.
    - A **befejezési** elem a **kezdő** elem értéke után egy óra. Ennek megfelelően a trigger 15 percet, 30 percet és 45 percet futtat a kezdési időpont után. Ne felejtse el frissíteni a kezdési időpontot az aktuális UTC-időpontra, a befejezési időt pedig egy órával a kezdési időpontig. 

        > [!IMPORTANT]
        > Az UTC időzóna esetében az időpontnak és a befejezésnek a következő formátumot kell követnie: "éééé-hh-NNTóó: PP: mm **Z**", míg más időzónák esetében a kezdő időpont és a befejezési idő a "éééé-hh-NNTóó: PP: SS". 
        > 
        > ISO 8601 standard, a _Z_ utótag, amely az időbélyeget a DateTime értékre (UTC), az időzónát pedig használhatatlanná teszi. Amíg az UTC-időzóna esetében hiányzik a _Z_ utótag, az _aktiváláskor_ hibaüzenetet fog eredményezni.

    - Az trigger a **Adfv2QuickStartPipeline** folyamathoz van társítva. Több folyamat egy triggerrel való hozzárendeléséhez vegyen fel további **pipelineReference** szakaszt.
    - A rövid útmutatóban szereplő folyamat két **paramétert** használ: **inputPath** és **outputPath**. Ezen paraméterek értékeit a triggerből adja át.

1. Hozzon létre egy triggert a **set-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

1. Győződjön meg arról, hogy az trigger állapota **leállt** a **Get-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1. Győződjön meg arról, hogy az trigger állapota a **Get-AzDataFactoryV2Trigger** parancsmag használatával **indul el** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

1.  A Get **-AzDataFactoryV2TriggerRun** parancsmag használatával lekérheti a trigger futtatását Azure PowerShellban. Az trigger futtatásával kapcsolatos információk lekéréséhez futtassa rendszeresen a következő parancsot. Frissítse a **TriggerRunStartedAfter** és a **TriggerRunStartedBefore** értékeket az trigger definíciójában szereplő értékeknek megfelelően:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    > [!NOTE]
    > Az ütemezett eseményindítók eseményindító időpontját az UTC időbélyegzőben kell megadni. A _TriggerRunStartedAfter_ és a _TRIGGERRUNSTARTEDBEFORE_ is az UTC időbélyegét várja

    Az trigger futtatásának és a folyamat futtatásának figyeléséhez a Azure Portalban tekintse meg a [folyamatok futtatásának figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)című témakört.



## <a name="net-sdk"></a>.NET SDK
Ez a szakasz bemutatja, hogyan lehet triggert létrehozni, elindítani és figyelni a .NET SDK használatával. A minta működésének megtekintéséhez először folytassa a gyors üzembe helyezési útmutatóval [: hozzon létre egy adatfeldolgozót a .net SDK használatával](quickstart-create-data-factory-dot-net.md). Ezután adja hozzá a következő kódot a Main metódushoz, amely egy 15 percenként futó ütemezett triggert hoz létre és indít el. Az trigger társítva van egy **Adfv2QuickStartPipeline** nevű folyamattal, amelyet a rövid útmutató részeként hoz létre.

15 percenként futó ütemezett trigger létrehozásához és elindításához adja hozzá a következő kódot a Main metódushoz:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Ha más időzónában kíván eseményindítókat létrehozni, az UTC-től eltérően, a következő beállításokat kell megadnia:
```csharp
<<ClientInstance>>.SerializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.SerializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
<<ClientInstance>>.SerializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateParseHandling = DateParseHandling.None;
<<ClientInstance>>.DeserializationSettings.DateFormatHandling = Newtonsoft.Json.DateFormatHandling.IsoDateFormat;
<<ClientInstance>>.DeserializationSettings.DateTimeZoneHandling = Newtonsoft.Json.DateTimeZoneHandling.Unspecified;
```

Egy trigger futtatásának figyeléséhez adja hozzá a következő kódot a `Console.WriteLine` minta utolsó utasítása előtt:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Az trigger futtatásának és a folyamat futtatásának figyeléséhez a Azure Portalban tekintse meg a [folyamatok futtatásának figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)című témakört.


## <a name="python-sdk"></a>Python SDK
Ez a szakasz bemutatja, hogyan lehet triggert létrehozni, elindítani és figyelni a Python SDK használatával. A minta működésének megtekintéséhez először ugorjon végig a gyors üzembe helyezési útmutatóban [: hozzon létre egy adatgyárat a PYTHON SDK használatával](quickstart-create-data-factory-python.md). Ezután adja hozzá a következő kódrészletet a "folyamat-Futtatás figyelése" blokk után a Python-szkriptben. Ez a kód egy ütemezett triggert hoz létre, amely 15 percenként fut a megadott kezdési és befejezési időpontok között. Frissítse a **start_time** változót az aktuális UTC-időpontra, és az **end_time** VÁLTOZÓt az aktuális UTC időpontnál is korábbira.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00Z', end_time='2017-12-12T05:00:00Z', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Az trigger futtatásának és a folyamat futtatásának figyeléséhez a Azure Portalban tekintse meg a [folyamatok futtatásának figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)című témakört.

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Trigger létrehozásához használhat Azure Resource Manager sablont. Részletes útmutatásért lásd: Azure-beli [adatelőállító létrehozása Resource Manager-sablonnal](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>A trigger indítási idejének átadása egy folyamatnak
Az 1. verzió Azure Data Factory támogatja a particionált információk olvasását vagy írását a rendszerváltozók használatával: **SliceStart**, **SliceEnd**, **WindowStart** és **WindowEnd**. A Azure Data Factory aktuális verziójában ezt a viselkedést egy folyamat paraméter használatával érheti el. Az trigger indítási időpontja és ütemezett időpontja a folyamat paraméterének értékeként van beállítva. A következő példában az trigger ütemezett ideje a folyamat **scheduledRunTime** paraméterének értékeként lesz átadva:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON-séma
A következő JSON-definíció bemutatja, hogyan hozhat létre ütemezési és ismétlődési ütemezési eseményindítót:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurrences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A **paraméterek** tulajdonság a **folyamatok** elem kötelező tulajdonsága. Ha a folyamat nem használ paramétert, akkor is vegyen fel egy üres JSON-definíciót a **paraméterek** tulajdonságba.


### <a name="schema-overview"></a>A séma áttekintése
Az alábbi táblázat magas szintű áttekintést nyújt az eseményindítóval kapcsolatos ismétlődés és ütemezés fő sémaelemeiről:

| JSON-tulajdonság | Description |
|:--- |:--- |
| **startTime** | Dátum-idő érték. Egyszerű ütemezések esetében a **startTime** tulajdonság értéke az első előfordulásra vonatkozik. Összetett ütemezéseknél az eseményindító nem indul korábban a megadott **startTime** értéknél. <br> UTC időzóna esetén a formátum a `'yyyy-MM-ddTHH:mm:ssZ'` következő:, más időzóna esetén pedig a Format érték `'yyyy-MM-ddTHH:mm:ss'` . |
| **endTime** | Az eseményindító záró dátuma és időpontja. Az eseményindító nem lesz végrehajtva a megadott záró dátum és idő után. A tulajdonság értéke nem lehet múltbéli időpont. Ez a tulajdonság nem kötelező.  <br> UTC időzóna esetén a formátum a `'yyyy-MM-ddTHH:mm:ssZ'` következő:, más időzóna esetén pedig a Format érték `'yyyy-MM-ddTHH:mm:ss'` . |
| **timeZone** | Az az időzóna, amelyben a trigger létrejött. Ez a beállítás a kezdési, a **befejezési** és az **ütemezett** időpontot **befolyásolja.** Tekintse meg [a támogatott időzóna listáját](#time-zone-option) |
| **megismétlődésének** | Az eseményindítóhoz tartozó ismétlődési szabályokat megadó recurrence objektum. A recurrence objektum a **frequency**, **interval**, **endTime**, **count** és **schedule** elemeket támogatja. Recurrence objektum meghatározásakor a **frequency** elem megadása kötelező. A recurrence objektum többi elemének megadása nem kötelező. |
| **frequency** | Az eseményindító ismétlődésének gyakorisági egysége. A támogatott értékek például: „minute”, „hour”, „day”, „week”, és „month” (percenként, óránként, naponta, hetente és havonta). |
| **időköz** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **interval** 3, a **frequency** pedig „week”, az eseményindító 3 hetente ismétlődik. |
| **menetrend** | Az eseményindító ismétlődési ütemezése. Egy megadott **frequency** értékkel rendelkező eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A **schedule** tulajdonságban az ismétlődéshez tartozó módosítások szerepelnek, amelyek alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál.

> [!IMPORTANT]
> Az UTC időzóna esetében az időpontnak és a befejezésnek a következő formátumot kell követnie: "éééé-hh-NNTóó: PP: mm **Z**", míg más időzónák esetében a kezdő időpont és a befejezési idő a "éééé-hh-NNTóó: PP: SS". 
> 
> ISO 8601 standard, a _Z_ utótag, amely az időbélyeget a DateTime értékre (UTC), az időzónát pedig használhatatlanná teszi. Amíg az UTC-időzóna esetében hiányzik a _Z_ utótag, az _aktiváláskor_ hibaüzenetet fog eredményezni.

### <a name="schema-defaults-limits-and-examples"></a>Séma alapértékei, korlátai és példái

| JSON-tulajdonság | Típus | Kötelező | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Sztring | Yes | Nincs | ISO-8601 dátum-idők | UTC időzóna esetén `"startTime" : "2013-01-09T09:30:00-08:00Z"` <br> egyéb időzóna esetén `"2013-01-09T09:30:00-08:00"` |
| **timeZone** | Sztring | Yes | Nincs | [Időzóna-értékek](#time-zone-option)  | `"UTC"` |
| **megismétlődésének** | Objektum | Yes | Nincs | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **időköz** | Szám | No | 1 | 1–1000 | `"interval":10` |
| **endTime** | Sztring | Yes | Nincs | Egy jövőbeli időpontot jelölő dátum-idő érték. | UTC időzóna esetén `"endTime" : "2013-02-09T09:30:00-08:00Z"` <br> egyéb időzóna esetén `"endTime" : "2013-02-09T09:30:00-08:00"`|
| **menetrend** | Objektum | No | Nincs | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="time-zone-option"></a>Időzóna-beállítás

Íme néhány olyan időzóna, amely az ütemezett eseményindítók esetében támogatott:

| Időzóna | UTC-eltolás (nem napfényes mentés) | timeZone értéke | A nyári mentés megtartása | Időbélyegző formátuma |
| :--- | :--- | :--- | :--- | :--- |
| Egyezményes világidő | 0 | `UTC` | No | `'yyyy-MM-ddTHH:mm:ssZ'`|
| Csendes-óceáni idő (PT) | -8 | `Pacific Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Központi idő (CT) | -6 | `Central Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Keleti idő (ET) | -5 | `Eastern Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Greenwichi középidő (GMT) | 0 | `GMT Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Közép-európai téli idő | +1 | `W. Europe Standard Time` | Yes | `'yyyy-MM-ddTHH:mm:ss'` |
| Indiai téli idő (IST) | + 5:30 | `India Standard Time` | No | `'yyyy-MM-ddTHH:mm:ss'` |
| Kínai téli idő | + 8 | `China Standard Time` | No | `'yyyy-MM-ddTHH:mm:ss'` |

Ez a lista nem teljes. Az időzóna-beállítások teljes listáját a Data Factory portál trigger- [létrehozási lapján](#data-factory-ui) tekintheti meg

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázatban látható, hogy a **startTime** tulajdonság hogyan irányítja a folyamatfuttatást:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |
| Múltbeli kezdési időpont | Kiszámítja a kezdőidőpontot követő első jövőbeli végrehajtási időpontot, és abban az időpontban fut.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.<br/><br/>Lásd a táblázat alatti példát. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |
| Jövőbeli vagy aktuális kezdési időpont | Egyszer fut a megadott kezdési időpontban.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki. | Az trigger _nem kezdődhet hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |

Tekintsünk meg egy példát arról, hogy mi történik, ha a kezdési időpont egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont `2017-04-08 13:00`, a kezdési idő `2017-04-07 14:00`, és az eseményindító kétnaponta ismétlődik. (Az **Ismétlődés** értékének meghatározása: a **Frequency** tulajdonság értéke "Day", az **Interval** tulajdonság pedig 2 lesz.) Figyelje meg, hogy a **kezdő időpont** értéke korábbi, és az aktuális időpont előtt következik be.

A fenti feltételek szerint az első végrehajtás a következő `2017-04-09` címen érhető el: `14:00` . Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Ebben a forgatókönyvben a kezdési időpont `2017-04-07` a `2:00pm` következő:, így a következő példány két nap ettől az időponttól kezdődően `2017-04-09` `2:00pm` .

Az első végrehajtási időpont akkor is ez, ha a **startTime** értéke `2017-04-05 14:00` vagy `2017-04-01 14:00`. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Ezért a következő végrehajtások a címen, majd a címen, majd a címen, `2017-04-11` `2:00pm` `2017-04-13` `2:00pm` `2017-04-15` `2:00pm` és így tovább.

Végül, ha az eseményindító ütemezésében nincs megadva az óra vagy perc, a rendszer alapértelmezés szerint az első végrehajtás óra vagy perc értékeit használja.

### <a name="schedule-property"></a>schedule tulajdonság
A schedule használata egyrészt korlátozhatja a eseményindító végrehajtásainak számát. Ha például egy havi gyakoriságú eseményindító csak a 31. napon való futásra van ütemezve, akkor az eseményindító csak a 31 napos hónapokban fut.

Ezzel szemben az ütemezés növelheti is az eseményindító végrehajtásának számát. Ha például egy havi gyakoriságú eseményindító a hónap 1. és 2. napján való futásra van ütemezve, akkor az eseményindító minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több **schedule** elem is meg van adva, a kiértékelési sorrend a legnagyobb ütemezési beállítástól halad a legkisebb felé. A kiértékelés a hét számával kezdődik, majd ezt követi a hónap napja, a hét napja, az óra és végül a perc.

A következő táblázat részletesen ismerteti a **schedule** elemeit:


| JSON-elem | Description | Érvényes értékek |
|:--- |:--- |:--- |
| **perc** | Az óra azon perce, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
| **óra** | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul> |
| **weekDays** | A hét azon napjai, amelyeken az eseményindító fut. Az érték csak heti gyakorisággal adható meg. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday (Hétfő, Kedd, Szerda, Csütörtök, Péntek, Szombat, Vasárnap)</li><li>A nap értékek tömbje (a tömb maximális értéke 7)</li><li>A nap értékek nem tesznek különbséget a kis- és nagybetű között</li></ul> |
| **monthlyOccurrences** | A hónap azon napjai, amelyeken az eseményindító fut. Az érték csak havi gyakorisággal adható meg. | <ul><li>**MonthlyOccurrence** objektumok tömbje: `{ "day": day,  "occurrence": occurrence }` .</li><li>A **day** attribútum a hét azon napja, amelyen az eseményindító fut. Például a `{Sunday}` értékű **day** attribútummal rendelkező **monthlyOccurrences** tulajdonság a hónap minden vasárnapját jelenti. A **day** attribútum megadása kötelező.</li><li>Az **occurrence** attribútum a megadott **day** attribútum előfordulása a hónapban. Például a `{Sunday, -1}` értékű **day** és **occurrence** attribútumokkal rendelkező **monthlyOccurrences** tulajdonság a hónap utolsó vasárnapját jelenti. Az **occurrence** attribútum megadása nem kötelező.</li></ul> |
| **monthDays** | A hónap azon napja, amelyen az eseményindító lefut. Az érték csak havi gyakorisággal adható meg. | <ul><li>Bármilyen érték -1 és -31 között</li><li>Bármilyen érték 1 és 31 között</li><li>Értékek tömbje</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Eseményindító-ismétlődési ütemezések példái
Ez a szakasz az ismétlődésütemezésekre mutat példákat, és a **schedule** objektumra és annak elemeire koncentrál.

Ez a példa azt feltételezi, hogy az **interval** értéke 1, és hogy a **frequency** értéke helyes, az ütemezés meghatározása szerint. Nem lehet például a **frequency** értéke „day”, ha a **schedule** objektum „monthDays” értékre lett módosítva. Ez és az ehhez hasonló korlátozások az előző szakaszban található táblázatban szerepelnek.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` | Minden nap 05:00-kor fut le. |
| `{"minutes":[15], "hours":[5]}` | Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` | Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` | Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` | 15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le. Ez az eseményindító óránként fut le. A perceket a **startTime** értéke szabályozza, ha az érték meg van adva. Ha az érték nincs megadva, akkor a perceket a létrehozás ideje szabályozza. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor.<br/><br/>Ez az ütemterv egyenértékű az "Hour **" értékkel** rendelkező, az 1. **intervallum** értékkel rendelkező triggerrel, és nincs **ütemterv**.  Ez az ütemezés különböző **frequency** és **interval** értékekkel használható egyéb eseményindítók létrehozásához. Ha például a **Frequency** értéke "Month", az ütemterv csak havonta egyszer fut, nem pedig minden nap, amikor a **Frequency** értéke "Day". |
| `{"minutes":[0]}` | Minden óra kezdetén fut le. Ez az eseményindító minden óra kezdetén fut le, 00:00-kor, 01:00-kor, 02:00-kor és így tovább.<br/><br/>Ez az ütemezés megegyezik egy olyan eseményindítóval, amely „hour” **frequency** értékkel és nulla perc **startTime** értékkel rendelkezik, vagy nincs **schedule** érték, de a **frequency** értéke „day”. Ha a **frequency** értéke „week” vagy „month”, az ütemezés csak egy héten egyszer vagy egy hónapban egyszer fut le. |
| `{"minutes":[15]}` | Minden óra 15. percében fut le. Ez az eseményindító minden óra 15. percében fut le, 00:15-kor, 01:15-kor, 02:15-kor és így tovább, egészen 23:15-ig. |
| `{"hours":[17], "weekDays":["saturday"]}` | Minden héten szombaton, 17:00-kor fut le. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:00-kor fut le. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le 09:00 és 16:45 között. |
| `{"weekDays":["tuesday", "thursday"]}` | Keddenként és csütörtökönként, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap 28. napján, 06:00-kor fut le (azt feltételezi, hogy a **frequency** „month” értékre van állítva). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Minden hónap utolsó napján, 06:00-kor fut le. Ha az adott eseményindítót a hónap utolsó napján kívánja futtatni, a 28., 29., 30. vagy 31. nap megadása helyett használjon -1-et. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján, 06:00-kor fut le. |
| `{monthDays":[1,14]}` | Minden hónap első és 14. napján, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, 05:00-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | A hónap végétől számított harmadik pénteken, minden hónapban, a megadott kezdési időpontban fut le. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Minden hónap ötödik péntekjén, a megadott kezdési időpontban fut le. Ha az adott hónapban nincs ötödik péntek, a folyamat nem fut le, mivel kizárólag a minden ötödik pénteken történő futásra van ütemezve. Az eseményindító a hónap utolsó péntekjén való futtatásához az **occurrence** értékénél használjon -1-et 5 helyett. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |


## <a name="next-steps"></a>Következő lépések
Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#trigger-execution).
