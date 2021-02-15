---
title: Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban
description: Ez a cikk azt ismerteti, hogyan hajtható végre egy folyamat az Azure Data Factoryban akár igény szerint, akár eseményindító létrehozásával.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/05/2018
ms.openlocfilehash: bd36b589424a0d890fc5e1bbab3f234e9b3264c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374779"
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Folyamat-végrehajtás és eseményindítók az Azure Data Factoryban

> [!div class="op_single_selector" title1="Válassza ki a használni kívánt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-scheduling-and-execution.md)
> * [Aktuális verzió](concepts-pipeline-execution-triggers.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A _folyamatfuttatás_ egy folyamat-végrehajtás egy példányát határozza meg az Azure Data Factoryben. Tegyük fel például, hogy rendelkezik egy délelőtt 8:00-kor, 9:00-kor és 10:00-kor végbemenő folyamattal. Ebben az esetben a folyamat vagy a folyamat futtatásának három különálló futtatása van. Minden folyamathoz tartozik egy egyedi folyamatfuttatási azonosító. A futtatási azonosító az adott folyamatfuttatást egyedien meghatározó GUID.

A folyamatfuttatások példányosítása jellemzően az argumentumoknak a folyamatban meghatározott paraméterekhez történő továbbításával történik. A folyamatok manuálisan vagy egy _eseményindító_ használatával hajthatók végre. A cikk részletesen ismerteti a folyamatok végrehajtásának mindkét módját.

## <a name="manual-execution-on-demand"></a>Manuális végrehajtás (igény szerint)

Egy folyamat manuális végrehajtását _igény szerinti_ végrehajtásnak nevezik.

Tegyük fel például, hogy rendelkezik egy **copyPipeline** nevű alapszintű folyamattal, amelyet végre szeretne hajtani. A folyamat egyetlen tevékenységgel rendelkezik, amely egy Azure Blob Storage-ban lévő forrásmappából másol tartalmakat egy ugyanazon a tárterületen található célmappába. A következő JSON-definíció ezt a mintafolyamatot mutatja be:

```json
{
    "name": "copyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "name": "CopyBlobtoBlob",
                "inputs": [
                    {
                        "referenceName": "sourceBlobDataset",
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "sinkBlobDataset",
                        "type": "DatasetReference"
                    }
                ]
            }
        ],
        "parameters": {
            "sourceBlobContainer": {
                "type": "String"
            },
            "sinkBlobContainer": {
                "type": "String"
            }
        }
    }
}
```

A JSON-definícióban a folyamathoz két paraméter szükséges: **sourceBlobContainer** és **sinkBlobContainer**. Ezekhez a paraméterekhez futtatás közben adja meg az értékeket.

A következő módszerek egyikével manuálisan futtathatja a folyamatot:
- .NET SDK
- Azure PowerShell-modul
- REST API
- Python SDK

### <a name="rest-api"></a>REST API

A következő minta parancs bemutatja, hogyan futtathatja a folyamatot a REST API manuális használatával:

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```

Teljes minta: [Rövid útmutató: Adat-előállító létrehozása a REST API használatával](quickstart-create-data-factory-rest-api.md).

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A következő mintaparancs bemutatja, hogyan futtathatja manuálisan a folyamatot az Azure PowerShell használatával:

```powershell
Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

A kérelem hasznos adatainak törzsébe adja át a paramétereket. A .NET SDK, az Azure PowerShell és a Python SDK esetében az értékeket a hívás argumentumaként átadott szótárba adja át:

```json
{
  "sourceBlobContainer": "MySourceFolder",
  "sinkBlobContainer": "MySinkFolder"
}
```

A válasz hasznos adat a folyamatfuttatás egyedi azonosítója:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```

Teljes minta: [Rövid útmutató: Adat-előállító létrehozása az Azure PowerShell használatával](quickstart-create-data-factory-powershell.md).

### <a name="net-sdk"></a>.NET SDK

A következő példa azt mutatja be, hogyan futtathatja a folyamatot a .NET SDK manuális használatával:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Teljes minta: [Rövid útmutató: Adat-előállító létrehozása a .NET SDK használatával](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> A .NET SDK használatával Data Factory folyamatokat hívhat meg Azure Functionsokból, a webszolgáltatásokból és így tovább.

## <a name="trigger-execution"></a>Eseményindító végrehajtása

A folyamatok futtatása triggerek használatával is elvégezhető. Az eseményindítók olyan feldolgozási egységek, amelyek meghatározzák, hogy mikor kezdődjön egy folyamat végrehajtása. A Data Factory jelenleg a triggerek három típusát támogatja:

- Ütemezési eseményindító: a folyamatokat időpont alapú ütemezés szerint meghívó eseményindító.

- Átfedésmentes ablakos eseményindító: az állapot megőrzése mellett, rendszeres időközönként működő eseményindító.

- Eseményalapú trigger: olyan trigger, amely egy eseményre válaszol.

A folyamatok és az eseményindítók több-a-többhöz kapcsolattal rendelkeznek (kivéve a kieséses ablak eseményindítóját). Több eseményindító képes egyetlen folyamat elindítására, vagy egyetlen eseményindító képes több folyamat kiindítására. Az eseményindító alábbi definíciójában a **folyamatok** tulajdonság az adott eseményindító által aktivált folyamatok listájára vonatkozik. A tulajdonság meghatározása a folyamatparaméterek értékeit is tartalmazza.
### <a name="basic-trigger-definition"></a>Alap eseményindító meghatározása

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {...},
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
                    "<parameter 2 Name>": "<parameter 2 Value>"
                }
            }
        ]
    }
}
```

## <a name="schedule-trigger"></a>Ütemezési eseményindító
Az ütemezési eseményindító egy időpontalapú ütemezés szerint futtatja a folyamatokat. Ez az eseményindító támogatja az időszakos és speciális naptárbeállításokat. Az eseményindító például az ezekhez hasonló időközöket támogatja: „hetente” vagy „hétfőnként délután 5:00-kor és csütörtökönként este 9:00-kor”. Az ütemezési eseményindító rugalmas, mert az adatkészlet-mintázat független, és az eseményindító nem tesz különbséget az idősorozat-adatok és a nem idősorozat-adatok között.

További információ az eseményindítók ütemezett elindításáról, valamint példákat a [folyamat ütemezett futtatását futtató eseményindító létrehozása](how-to-create-schedule-trigger.md)című témakörben talál.

## <a name="schedule-trigger-definition"></a>Ütemezési eseményindító meghatározása
Amikor létrehoz egy ütemezési eseményindítót, a JSON-definíció segítségével megadhatja az ütemezést és az ismétlődést.

Ahhoz, hogy az ütemező eseményindító kiváltsa egy folyamat indítását, az eseményindító meghatározásába foglalja bele az adott folyamat referenciáját. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>, // How often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC",
        "schedule": { // Optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
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
        "<parameter 2 Name>": "<parameter 2 Value>"
      }
    }
  ]}
}
```

> [!IMPORTANT]
> A **paraméterek** tulajdonság a **folyamatok** elem kötelező tulajdonsága. Ha a folyamat nem használ paramétert, akkor is vegyen fel egy üres JSON-definíciót a **paraméterek** tulajdonságba.

### <a name="schema-overview"></a>A séma áttekintése
Az alábbi táblázat nagy vonalakban áttekintést nyújt az eseményindítóval kapcsolatos ismétlődés és ütemezés fő sémaelemeiről:

| JSON-tulajdonság | Description |
| --- | --- |
| **startTime** | Dátum-idő érték. Alapszintű ütemezések esetében a **startTime** tulajdonság értéke az első előfordulásra vonatkozik. Összetett ütemezéseknél az eseményindító nem indul korábban a megadott **startTime** értéknél. |
| **endTime** | Az eseményindító záró dátuma és időpontja. Az eseményindító nem lesz végrehajtva a megadott záró dátum és idő után. A tulajdonság értéke nem lehet múltbéli időpont. <!-- This property is optional. --> |
| **timeZone** | Az időzóna. A támogatott időzónák listáját a [folyamat ütemezett futtatását futtató trigger létrehozása](how-to-create-schedule-trigger.md#time-zone-option)című témakörben tekintheti meg. |
| **megismétlődésének** | Az eseményindítóhoz tartozó ismétlődési szabályokat megadó recurrence objektum. A recurrence objektum a **frequency**, **interval**, **endTime**, **count** és **schedule** elemeket támogatja. Recurrence objektum meghatározásakor a **frequency** elem megadása kötelező. A recurrence objektum többi elemének megadása nem kötelező. |
| **frequency** | Az eseményindító ismétlődésének gyakorisági egysége. A támogatott értékek például: minute, hour, day, week és month (percenként, óránként, naponta, hetente és havonta). |
| **időköz** | Pozitív egész szám, amely a **frequency** érték időközét jelöli. A **frequency** érték határozza meg a trigger futtatási gyakoriságát. Ha például az **interval** értéke 3, a **frequency** értéke pedig week, az eseményindító három hetente ismétlődik. |
| **menetrend** | Az eseményindító ismétlődési ütemezése. Egy megadott **frequency** értékkel rendelkező eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A **schedule** tulajdonságban az ismétlődéshez tartozó módosítások szerepelnek, amelyek alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál. |

### <a name="schedule-trigger-example"></a>Példa ütemezési eseményindítóra

```json
{
  "properties": {
    "name": "MyTrigger",
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": "Hour",
        "interval": 1,
        "startTime": "2017-11-01T09:00:00-08:00",
        "endTime": "2017-11-02T22:00:00-08:00"
      }
    },
    "pipelines": [{
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToBlobPipeline"
        },
        "parameters": {}
      },
      {
        "pipelineReference": {
          "type": "PipelineReference",
          "referenceName": "SQLServerToAzureSQLPipeline"
        },
        "parameters": {}
      }
    ]
  }
}
```

### <a name="schema-defaults-limits-and-examples"></a>Séma alapértékei, korlátai és példái

| JSON-tulajdonság | Típus | Kötelező | Alapértelmezett érték | Érvényes értékek | Példa |
| --- | --- | --- | --- | --- | --- |
| **startTime** | sztring | Yes | Nincs | ISO 8601 dátum-idő értékek | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **megismétlődésének** | object | Yes | Nincs | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **időköz** | szám | No | 1 | 1–1000 | `"interval":10` |
| **endTime** | sztring | Yes | Nincs | Egy jövőbeli időpontot jelölő dátum-idő érték | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **menetrend** | object | No | Nincs | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázatban látható, hogy a **startTime** tulajdonság hogyan irányítja a folyamatfuttatást:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
| --- | --- | --- |
| **A kezdés időpontja a múltban van** | Kiszámítja a kezdő időpont utáni első jövőbeli végrehajtási időpontot, és abban az időpontban fut.<br /><br />A további végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.<br /><br />Lásd a táblázat alatti példát. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul.<br /><br />Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |
| **A kezdő időpont jövőbeli, illetve az aktuális időpont** | Egyszer fut a megadott kezdési időpontban.<br /><br />A további végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki. | Az trigger _nem kezdődhet hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul.<br /><br />Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |

Tekintsünk meg egy példát arról, hogy mi történik, ha a kezdő időpont egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont 2017-04-08 13:00, a kezdési idő 2017-04-07 14:00, és a művelet kétnaponta ismétlődik. (Az **Ismétlődés** értékének meghatározása: a **Frequency** tulajdonság értéke "Day", az **Interval** tulajdonság pedig 2 lesz.) Figyelje meg, hogy a **kezdő időpont** értéke korábbi, és az aktuális időpont előtt következik be.

A fenti feltételek szerint az első végrehajtás 2017-04-09, 14:00. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Tehát ebben a forgatókönyvben a kezdő időpont 2017-04-07 14:00. A következő alkalom két napra esik ettől, azaz 2017-04-09 14:00.

Az első végrehajtási időpont akkor is ez, ha a **startTime** értéke 2017-04-05 14:00 vagy 2017-04-01 14:00. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Ezért a további végrehajtások a következő időpontokban történnek: 2017-04-11 14:00, 2017-04-13 14:00, majd 2017-04-15 14:00 és így tovább.

Végül, ha az aktiváláshoz nincs beállítva óra vagy perc, a rendszer az első végrehajtás óráját vagy perceit használja alapértelmezettként.

### <a name="schedule-property"></a>schedule tulajdonság
A **schedule** tulajdonsággal *korlátozhatja* a trigger végrehajtásainak számát. Ha például egy havi gyakoriságú trigger csak a 31. napon való futásra van ütemezve, akkor a trigger csak a harmincegy napos hónapokban fut.

A **schedule** tulajdonsággal *növelheti* is a trigger végrehajtásainak számát. Ha például egy havi gyakoriságú eseményindító a hónap 1. és 2. napján való futásra van ütemezve, akkor a trigger minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több **Schedule** elem is meg van adva, a kiértékelés sorrendje a legtöbbet a legkisebb ütemtervre van állítva: hét száma, hónap napja, hétköznap, óra, perc.

A következő táblázat részletesen ismerteti a **schedule** elemeit:

| JSON-elem | Description | Érvényes értékek |
| --- | --- | --- |
| **perc** | Az óra azon perce, amikor az eseményindító fut. |– Egész szám<br />– Egész számok tömbje |
| **óra** | A nap azon órái, amikor az eseményindító fut. |– Egész szám<br />– Egész számok tömbje |
| **weekDays** | A hét azon napjai, amelyeken az eseményindító fut. Az érték csak heti gyakorisággal adható meg.|<br />– Hétfő<br />– Kedd<br />– Szerda<br />– Csütörtök<br />– Péntek<br />– Szombat<br />– Vasárnap<br />– Nap értékek tömbje (a tömb maximális mérete 7)<br /><br />A napi értékek nem megkülönböztetik a kis-és nagybetűket |
| **monthlyOccurrences** | A hónap azon napjai, amelyeken az eseményindító fut. Az érték csak havi gyakorisággal adható meg. |– **MonthlyOccurrence** objektumok tömbje: `{ "day": day, "occurrence": occurrence }`<br />– A **day** attribútum a hét azon napja, amelyen a trigger fut. Például a `{Sunday}` értékű **day** attribútummal rendelkező **monthlyOccurrences** tulajdonság a hónap minden vasárnapját jelenti. A **day** attribútum megadása kötelező.<br />– Az **occurrence** attribútum a megadott **day** attribútum előfordulása a hónapban. Például a `{Sunday, -1}` értékű **day** és **occurrence** attribútumokkal rendelkező **monthlyOccurrences** tulajdonság a hónap utolsó vasárnapját jelenti. Az **occurrence** attribútum megadása nem kötelező. |
| **monthDays** | A hónap azon napja, amelyen az eseményindító lefut. Az érték csak havi gyakorisággal adható meg. |– Bármilyen érték -1 és -31 között<br />– Bármilyen érték 1 és 31 között<br />– Értékek tömbje |

## <a name="tumbling-window-trigger"></a>Átfedésmentes ablakos eseményindító
Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok.

További információ a kieséses ablak-eseményindítókkal kapcsolatban: a [kieséses ablak eseményindítójának létrehozása](how-to-create-tumbling-window-trigger.md).

## <a name="event-based-trigger"></a>Eseményalapú trigger

Egy eseményvezérelt eseményindító egy eseményre adott válaszként futtat folyamatokat, például egy fájl megérkezését vagy egy fájl törlését az Azure Blob Storageban.

További információkat az eseményalapú triggerekkel kapcsolatban az [eseményre válaszul folyamatot futtató trigger létrehozásával](how-to-create-event-trigger.md) kapcsolatos témakörben olvashat.

## <a name="examples-of-trigger-recurrence-schedules"></a>Eseményindító-ismétlődési ütemezések példái
Ez a szakasz az ismétlődésütemezésekre mutat be példákat. A **schedule** objektumra és annak elemeire koncentrál.

A példák azt feltételezik, hogy az **intervallum** értéke 1, és a **gyakoriság** értéke helyes az ütemezés definíciója szerint. Például nem rendelkezhet "Day" **Frequency** értékkel, és az **monthDays** módosítás is szerepel az **ütemezett** objektumban. Ezen korlátozások leírása az előző szakaszban található táblázatban szerepel.

| Példa | Leírás |
| --- | --- |
| `{"hours":[5]}` | Minden nap 05:00-kor fut le. |
| `{"minutes":[15], "hours":[5]}` | Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` | Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` | Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` | 15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le.<br /><br />Ez az eseményindító óránként fut le. A perceket a **startTime** értéke szabályozza, ha az érték meg van adva. Ha az érték nincs megadva, akkor a perceket a létrehozás ideje szabályozza. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor.<br /><br />Ez az ütemezés egyenértékű azzal, mintha egy hour értékű **frequency** attribútummal rendelkező eseményindító lenne, 1 **interval** értékkel, **schedule** nélkül. Ez az ütemezés különböző **frequency** és **interval** értékekkel használható egyéb eseményindítók létrehozásához. Ha például a **frequency** értéke month, az ütemezés egy hónapban csak egyszer fut, nem pedig minden nap, amikor a **frequency** értéke day. |
| `{"minutes":[0]}` | Minden óra kezdetén fut le.<br /><br />Ez az eseményindító minden óra kezdetén fut le, 00:00-kor, 01:00-kor, 02:00-kor és így tovább.<br /><br />Ez az ütemezés megegyezik egy olyan eseményindítóval, amely hour **frequency** értékkel és nulla perc **startTime** értékkel rendelkezik, és nincs **schedule** érték, de a **frequency** értéke day. Ha a **gyakoriság** értéke "Week" vagy "Month", az ütemterv csak a hét egy napját, vagy csak a hónap egy napját hajtja végre. |
| `{"minutes":[15]}` | Minden óra 15. percében fut le.<br /><br />Ez az eseményindító minden óra 15. percében fut le, 00:15-kor, 01:15-kor, 02:15-kor és így tovább, egészen 23:15-ig. |
| `{"hours":[17], "weekDays":["saturday"]}` | Minden héten szombaton, 17:00-kor fut le. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:00-kor fut le. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le 09:00 és 16:45 között. |
| `{"weekDays":["tuesday", "thursday"]}` | Keddenként és csütörtökönként, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap huszonnyolcadik napján, 06:00-kor fut (azt feltételezi, hogy a **frequency** month értékre van állítva). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Minden hónap utolsó napján, 06:00-kor fut le.<br /><br />Ha az adott eseményindítót a hónap utolsó napján kívánja futtatni, a 28., 29., 30. vagy 31. nap megadása helyett használjon -1-et. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján, 06:00-kor fut le. |
| `{monthDays":[1,14]}` | Minden hónap első és tizennegyedik. napján, a megadott kezdési időpontban fut. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, 05:00-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | A hónap végétől számított harmadik pénteken, minden hónapban, a megadott kezdési időpontban fut le. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Minden hónap ötödik péntekjén, a megadott kezdési időpontban fut le.<br /><br />Ha az adott hónapban nincs ötödik péntek, a folyamat nem fut le. Az eseményindító a hónap utolsó péntekjén való futtatásához az **occurrence** értékénél használjon -1-et 5 helyett. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="trigger-type-comparison"></a>Eseményindító-típusok összehasonlítása
Az átfedésmentes ablakos eseményindító és az ütemezési eseményindító is időszívverés alapján működik. Mi a különbség közöttük?

> [!NOTE]
> A kikapcsoló ablak triggere *megvárja az aktivált folyamat* befejeződését. A futtatási állapota az aktivált folyamat futtatásának állapotát tükrözi. Ha például egy aktivált folyamat futása megszakad, a rendszer megszakítja a megfelelő késleltetésű ablak-trigger futtatását. Ez különbözik az ütemezett trigger "tűz és elfelejtés" viselkedésével, amely sikeresnek van megjelölve, amíg a folyamat futtatása elindult.

Az alábbi táblázatban az átfedésmentes ablakos eseményindító és az ütemezési eseményindító összehasonlítása látható:

| Elem | Átfedésmentes ablakos eseményindító | Ütemezési eseményindító |
| --- | --- | --- |
| **Visszatöltési forgatókönyvek** | Támogatott. A folyamatfuttatások ütemezhetők múltbéli időszakokra. | Nem támogatott. A folyamatfuttatások csak az aktuális időszaktól vagy egy jövőbeli időszaktól hajthatók végre. |
| **Megbízhatóság** | 100%-os megbízhatóság. A folyamatfuttatások egy adott kezdési időponttól minden időszakhoz ütemezhetők, hézagok nélkül. | Kevésbé megbízható. |
| **Újrapróbálkozási képesség** | Támogatott. A sikertelen folyamatfuttatások alapértelmezett újrapróbálkozási szabályzata 0, vagy a felhasználó által az eseményindító definíciójában megadott szabályzat. Automatikusan újrapróbálkozik, ha a folyamat egyidejűségi/kiszolgálói/szabályozási korlátok miatt meghiúsul (azaz 400: felhasználói hiba, 429: túl sok kérés, 500: belső kiszolgálóhiba). | Nem támogatott. |
| **Egyidejűség** | Támogatott. A felhasználók explicit módon adhatják meg az eseményindító egyidejűségi korlátját. 1 és 50 közötti egyidejű aktivált folyamatfuttatást engedélyez. | Nem támogatott. |
| **Rendszerváltozók** | A @trigger (). scheduledTime és (). kezdő időponttal együtt a @trigger **WindowStart** és a **WindowEnd** rendszerváltozók használatát is támogatja. A felhasználók hozzáférhetnek a `trigger().outputs.windowStartTime` és `trigger().outputs.windowEndTime` értékhez az eseményindító rendszerváltozójaként az eseményindító meghatározásában. Az értékeket az időszak kezdési és záró időpontjaként használja a rendszer. Például a minden órában lefutó átfedésmentes ablakos eseményindító esetében az 01:00 és 02:00 közötti időszakban a meghatározás `trigger().outputs.windowStartTime = 2017-09-01T01:00:00Z` és `trigger().outputs.windowEndTime = 2017-09-01T02:00:00Z`. | @triggerA csak az alapértelmezett (). scheduledTime és @trigger (). kezdő időpontokra vonatkozó változókat támogatja. |
| **Folyamat–trigger kapcsolat** | Támogatja az egy-az-egyhez kapcsolatot. Csak egy folyamat indítható el. | Támogatja a több-a-többhöz kapcsolatokat. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is. |

## <a name="next-steps"></a>Következő lépések
Lásd az alábbi oktatóanyagokat:

- [Gyors útmutató: adat-előállító létrehozása a .NET SDK-val](quickstart-create-data-factory-dot-net.md)
- [Ütemezési eseményindító létrehozása](how-to-create-schedule-trigger.md)
- [Átfedésmentes ablak eseményindítójának létrehozása](how-to-create-tumbling-window-trigger.md)
