---
title: Kieséses ablakos eseményindítók létrehozása Azure Data Factory
description: Megtudhatja, hogyan hozhat létre olyan triggert a Azure Data Factoryban, amely egy folyamatot futtat egy kieséses ablakban.
author: chez-charlie
ms.author: chez
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: f5bc9951229c61dd988f44b06b8fcd40881226ae
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100393700"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Átfedésmentes ablakban folyamatot futtató trigger létrehozása
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a bukdácsoló ablakos triggerek létrehozásának, indításának és figyelésének lépéseit ismerteti. Az eseményindítókkal és a támogatott típusokkal kapcsolatos általános információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok. A bukdácsoló ablakos trigger egy-az-egyhez kapcsolattal rendelkezik egy folyamattal, és csak egy külön folyamatra hivatkozhat. Az ablakos eseményindító egy nagyobb súlyú alternatíva, amely összetett forgatókönyvekhez kínál különféle funkciókat ([más kieséses ablakos eseményindítókat](#tumbling-window-trigger-dependency), a [Sikertelen feladatok](tumbling-window-trigger-dependency.md#monitor-dependencies) újraindítását és a [folyamatokhoz való felhasználói újrapróbálkozás beállítását](#user-assigned-retries-of-pipelines)). Ha szeretné jobban megismerni az ütemezett trigger és a kieséses ablak trigger közötti különbséget [, tekintse](concepts-pipeline-execution-triggers.md#trigger-type-comparison)meg a következőt:.

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

1. Ha a Data Factory felhasználói felületén létre szeretne hozni egy kieséses ablak-eseményindítót, válassza az **Eseményindítók** fület, majd válassza az **új** lehetőséget. 
1. Miután megnyitotta az aktiválási konfiguráció ablaktáblát, válassza a **kiesési ablak** lehetőséget, majd adja meg a kikapcsolt ablak triggerének tulajdonságait. 
1. Amikor elkészült, válassza a **Mentés** lehetőséget.

![Kieséses ablakos trigger létrehozása a Azure Portalban](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Kiváltott ablak típusú trigger tulajdonságai

A kiesési ablak a következő típusú trigger-tulajdonságokkal rendelkezik:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "parameter1": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter2": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
            }
        }
    }
}
```

Az alábbi táblázat áttekintést nyújt azokról a fő JSON-elemekről, amelyek a kieséses ablak eseményindítójának ismétlődésével és ütemezésével kapcsolatosak:

| JSON-elem | Leírás | Típus | Megengedett értékek | Kötelező |
|:--- |:--- |:--- |:--- |:--- |
| **típusa** | Az trigger típusa. A típus a "TumblingWindowTrigger" rögzített érték. | Sztring | "TumblingWindowTrigger" | Yes |
| **runtimeState** | Az trigger futási idejének jelenlegi állapota.<br/>**Megjegyzés**: ez az elem a következő: \<readOnly> . | Sztring | "Started", "leállítva", "Letiltva" | Yes |
| **frequency** | Az a gyakorisági egység (perc vagy óra) jelölő sztring, amelynél az trigger ismétlődik. Ha a **kezdő** időpontok értékének részletessége nagyobb, mint a **gyakoriság** értéke, a rendszer a **kezdő** időpontokat veszi figyelembe az ablak határainak kiszámításakor. Ha például a **gyakoriság** értéke óránként, a **kezdő időpont** pedig 2017-09-01T10:10:10Z, az első ablak a következő: (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Sztring | "minute", "Hour"  | Yes |
| **időköz** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **intervallum** 3, és a **gyakoriság** értéke "Hour", az trigger 3 óránként ismétlődik. <br/>**Megjegyzés**: a minimális ablak intervalluma 5 perc. | Egész szám | Pozitív egész szám. | Yes |
| **startTime**| Az első előfordulás, amely múltbeli lehet. Az első trigger időköze a következő **: (Kezdés**, **kezdő**  +  **időköz**). | DateTime | Egy DateTime érték. | Yes |
| **endTime**| A legutóbbi előfordulás, amely múltbeli lehet. | DateTime | Egy DateTime érték. | Yes |
| **késedelem** | Az ablak adatfeldolgozásának megkezdését késleltető idő. A folyamat futtatása a várt végrehajtási idő és a **késleltetés** mennyisége után indul el. A **késleltetés** határozza meg, hogy mennyi ideig várakozik az indítás a határidő lejártakor az új Futtatás elindítása előtt. A **késleltetés** nem változtatja meg az ablak **kezdő** időkeretét. Például az 00:10:00-as **késleltetési** érték 10 percet vesz igénybe. | Időtartomány<br/>(óó: PP: SS)  | Egy TimeSpan érték, amely az alapértelmezett 00:00:00. | No |
| **maxConcurrency** | Azon egyidejű trigger-futtatások száma, amelyek készen állnak a Windows rendszerre. Például a tegnapi értékre való kitöltés óránkénti futtatásához 24 Windowson. Ha a **maxConcurrency** = 10, az aktiválási események csak az első 10 Windows rendszerre (00:00-01:00-09:00-10:00) vannak kirúgva. Az első 10 aktivált folyamat befejezése után a trigger-futtatások a következő 10 Windows rendszerre (10:00-11:00-19:00-20:00) lesznek kirúgva. Ha a **maxConcurrency** = 10 esetében ebben a példában 10 Windows áll rendelkezésre, 10 teljes folyamat fut. Ha csak 1 ablak áll készen, csak 1 folyamat fut. | Egész szám | 1 és 50 közötti egész szám. | Yes |
| **retryPolicy: darabszám** | Az újrapróbálkozások száma, mielőtt a folyamat futása "sikertelen" állapotúként van megjelölve.  | Egész szám | Egész szám, ahol az alapértelmezett érték 0 (nincs újrapróbálkozás). | No |
| **retryPolicy: intervalInSeconds** | A másodpercben megadott újrapróbálkozási kísérletek közötti késleltetés. | Egész szám | Azon másodpercek száma, amelyekben az alapértelmezett érték 30. | No |
| **dependsOn: típus** | A TumblingWindowTriggerReference típusa. Kötelező, ha függőség van beállítva. | Sztring |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | No |
| **dependsOn: méret** | A függőséget jelző ablak mérete | Időtartomány<br/>(óó: PP: SS)  | Pozitív TimeSpan érték, amelynél az alapértelmezett érték a gyermek trigger ablakának mérete  | No |
| **dependsOn: eltolás** | A függőségi trigger eltolása. | Időtartomány<br/>(óó: PP: SS) |  Egy TimeSpan érték, amelynek negatívnak kell lennie az önfüggőségben. Ha nincs megadva érték, az ablak ugyanaz, mint maga az trigger. | Önálló függőség: igen<br/>Egyéb: nem  |

> [!NOTE]
> Miután közzétett egy ablakos triggert, az **intervallum** és a **gyakoriság** nem szerkeszthető.

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart és WindowEnd rendszerváltozók

Használhatja a **WindowStart** és a **WindowEnd** rendszerváltozóit a **folyamat** definíciójában (azaz egy lekérdezés egy részénél). A rendszerváltozókat paraméterként adja át a folyamatnak az **trigger** definíciójában. Az alábbi példa bemutatja, hogyan továbbíthatja ezeket a változókat paraméterekként:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipelineName"
            },
            "parameters": {
                "MyWindowStart": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                },
                "MyWindowEnd": {
                    "type": "Expression",
                    "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                }
            }
        }
    }
}
```

Ha a **WindowStart** és a **WindowEnd** rendszerváltozó értékét szeretné használni a folyamat definíciójában, használja a "MyWindowStart" és a "MyWindowEnd" paramétereket ennek megfelelően.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>A Windows végrehajtási sorrendje backfill-forgatókönyvben

Ha a trigger kezdő időpont a múltban, akkor ez a képlet (M = (CurrentTime-TriggerStartTime)/TumblingWindowSize) alapján a trigger a (z) {M} backfill (múlt) értékkel párhuzamosan fut, és megtartja az indítás párhuzamosságát, mielőtt végrehajtja a jövőbeli futtatásokat. A Windows végrehajtásának sorrendje determinisztikus, a legrégebbi és a legújabb intervallumok között. Ez a viselkedés jelenleg nem módosítható.

### <a name="existing-triggerresource-elements"></a>Meglévő TriggerResource elemek

A meglévő **TriggerResource** elemek frissítésére a következő pontok érvényesek:

* Az trigger (vagy  az ablakméret) értékének az **intervallum** elemmel együtt nem módosítható az trigger létrehozása után. Ez a triggerRun-ismétlések és a függőségi értékelések megfelelő működéséhez szükséges
* Ha az indítás **utolsó** eleme elemének értéke (hozzáadott vagy frissített), a már feldolgozott Windows-állapot *nem* áll alaphelyzetbe. Az trigger tiszteletben tartja az új **utolsó** értéket. Ha az új **befejezési** érték a már végrehajtott Windows előtt van, az trigger leáll. Ellenkező esetben az aktiválás leáll, amikor az új **befejezési** érték észlelhető.

### <a name="user-assigned-retries-of-pipelines"></a>Felhasználó által hozzárendelt újrapróbálkozások a folyamatokhoz

Folyamat meghibásodása esetén a kiesést jelző ablakos trigger automatikusan újra megpróbálkozhat a hivatkozott folyamat végrehajtásával, és ugyanazokat a bemeneti paramétereket használhatja a felhasználói beavatkozás nélkül. Ezt megadhatja a "retryPolicy" tulajdonsággal az trigger definíciójában.

### <a name="tumbling-window-trigger-dependency"></a>Kiesési ablak trigger-függősége

Ha azt szeretné, hogy a rendszer csak akkor hajtson végre egy késleltetésű ablakos triggert, ha egy másik, az adatgyárban lévő kiesést kiváltó ablak-trigger sikeres végrehajtását követően [létrehoz egy ablakos trigger-függőséget](tumbling-window-trigger-dependency.md).

### <a name="cancel-tumbling-window-run"></a>A kieséses ablak futtatásának megszakítása

A futó ablakos triggerek futtatását megszakíthatja, ha az adott ablak _várakozik_, _függőségre várakozik_ vagy _futó_ állapotú.

* Ha az ablak **fut** állapotban van, szakítsa meg a társított _folyamat futtatását_, és az indító Futtatás később _megszakítva_ lesz.
* Ha az ablak **várakozik** , vagy **a függőségi** állapotra vár, megszakíthatja az ablakot a figyelésből:

![Kieséses ablak-trigger megszakítása a figyelési lapról](media/how-to-create-tumbling-window-trigger/cancel-tumbling-window-trigger.png)

Egy megszakított ablakot is újra lehet futtatni. Az újrafuttatással az trigger _legújabb_ közzétett definíciói szerepelnek, és a rendszer _újból kiértékeli_ a megadott időszakra vonatkozó függőségeket.

![A korábban megszakított futtatások kiesését kiváltó ablakos trigger újrafuttatása](media/how-to-create-tumbling-window-trigger/rerun-tumbling-window-trigger.png)

## <a name="sample-for-azure-powershell"></a>Példa Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a szakasz bemutatja, hogyan használható a Azure PowerShell egy trigger létrehozásához, elindításához és figyeléséhez.

1. Hozzon létre egy **MyTrigger.js** nevű JSON-fájlt a C:\ADFv2QuickStartPSH\ mappában a következő tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt állítsa be az időponthoz **tartozó elem értékét** az aktuális UTC-időre. Állítsa a **befejezési** elem értékét egy órára az aktuális UTC időpontnál.

    ```json
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```

2. Hozzon létre egy triggert a **set-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Győződjön meg arról, hogy az trigger állapota **leállt** a **Get-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Győződjön meg arról, hogy az trigger állapota a **Get-AzDataFactoryV2Trigger** parancsmag használatával **indul el** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. A Get **-AzDataFactoryV2TriggerRun** parancsmag használatával lekérheti a trigger futtatását Azure PowerShellban. Az trigger futtatásával kapcsolatos információk lekéréséhez futtassa rendszeresen a következő parancsot. Frissítse a **TriggerRunStartedAfter** és a **TriggerRunStartedBefore** értékeket az trigger definíciójában szereplő értékeknek megfelelően:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Az trigger-futtatások és a folyamat-futtatások figyeléséhez a Azure Portalban lásd: [folyamatok futtatásának figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Következő lépések

* Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Függőség létrehozása átfedésmentes ablak eseményindítójához](tumbling-window-trigger-dependency.md)
