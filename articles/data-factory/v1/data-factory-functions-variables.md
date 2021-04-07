---
title: Data Factory függvények és rendszerváltozók
description: A Azure Data Factory függvények és rendszerváltozók listáját tartalmazza
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 498f40a1783903b5dca0a2fe3204cc6aa25a2fec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786430"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory – functions és System változók
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory rendszerváltozói](../control-flow-system-variables.md)című témakört.

Ez a cikk a Azure Data Factory által támogatott függvényekről és változókról nyújt információkat.

## <a name="data-factory-system-variables"></a>Rendszerváltozók Data Factory

| Változó neve | Leírás | Objektum hatóköre | JSON-hatókör és-használati esetek |
| --- | --- | --- | --- |
| WindowStart |Az aktuális tevékenység futtatási ablakának időintervallumának kezdete |tevékenység |<ol><li>Az adatkijelölési lekérdezések meghatározása. Lásd az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkben hivatkozott összekötő cikkeket.</li> |
| WindowEnd |Az aktuális tevékenység futtatási időszakának vége |tevékenység |ugyanaz, mint a WindowStart. |
| SliceStart |A létrehozott adatszeletek időintervallumának kezdete |tevékenység<br/>adatkészlet |<ol><li>Az [Azure Blob](data-factory-azure-blob-connector.md) és a [fájlrendszerbeli adatkészletek](data-factory-onprem-file-system-connector.md)használatakor a dinamikus mappák elérési útját és fájlneveit is meg kell adni.</li><li>Adja meg a bemeneti függőségeket a adat-előállító függvényeknél a tevékenység bemenetek gyűjteményében.</li></ol> |
| SliceEnd |Az aktuális adatszelet időtartamának vége. |tevékenység<br/>adatkészlet |ugyanaz, mint a SliceStart. |

> [!NOTE]
> A jelenleg adat-előállító megköveteli, hogy a tevékenységben megadott ütemezése pontosan megfelel a kimeneti adatkészlet rendelkezésre állásában megadott ütemtervnek. Ezért a WindowStart, a WindowEnd, a SliceStart és a SliceEnd mindig ugyanarra az időszakra és egyetlen kimeneti szeletre mutatnak.
> 

### <a name="example-for-using-a-system-variable"></a>Példa rendszerváltozó használatára
A következő példában a **SliceStart** év, hónap, nap és időpont a **FolderPath** és a **fájlnév** tulajdonságai által használt különálló változókba kerül.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Data Factory függvények
Az adat-előállítóban a függvények a rendszerváltozókkal együtt a következő célokra használhatók:

1. Az adatkijelölési lekérdezések megadásával (lásd az [adatáthelyezési tevékenységek](data-factory-data-movement-activities.md) által hivatkozott összekötő-cikkeket).
   
   A adat-előállító függvény meghívására szolgáló szintaxis a **$$\<function>** következő: adatkijelölési lekérdezések és a tevékenység és az adatkészletek egyéb tulajdonságai.  
2. A bemeneti függőségek megadása a adat-előállító függvényeknél a tevékenység bemenetei gyűjteményben.
   
    a $ $ nem szükséges a bemeneti függőségi kifejezések megadásához.     

A következő példában a JSON-fájl **sqlReaderQuery** tulajdonsága a függvény által visszaadott értékhez van rendelve `Text.Format` . Ez a minta egy **WindowStart** nevű rendszerváltozót is használ, amely a tevékenység futtatási ablakának kezdési időpontját jelöli.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Tekintse meg az [Egyéni dátum-és időformátum-karakterláncok](/dotnet/standard/base-types/custom-date-and-time-format-strings) témakört, amely leírja a különböző formázási beállításokat (például: ay vagy nn). 

### <a name="functions"></a>Functions
A következő táblázatok a Azure Data Factory összes funkcióját felsorolják:

| Kategória | Függvény | Paraméterek | Leírás |
| --- | --- | --- | --- |
| Idő |AddHours (X, Y) |X: dátum és idő <br/><br/>Y: int |Y óra beadása a megadott időponthoz X. <br/><br/>Például: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Idő |AddMinutes (X, Y) |X: dátum és idő <br/><br/>Y: int |Y percet vesz fel X-re.<br/><br/>Például: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Idő |Óra kezdete (X) |X: dátum és idő |Az X óra összetevője által képviselt óra kezdő időpontjának beolvasása. <br/><br/>Például: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays (X, Y) |X: dátum és idő<br/><br/>Y: int |Y nappal adja hozzá az X-et. <br/><br/>Például: 9/15/2013 12:00:00 PM + 2 nap = 9/17/2013 12:00:00 PM.<br/><br/>Napokat is ki lehet vonni az Y negatív szám megadásával.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths (X, Y) |X: dátum és idő<br/><br/>Y: int |Y hónapot hoz létre az X értékhez.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>Hónapok is kivonhatók, ha az Y értéket negatív számként határozza meg.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters (X, Y) |X: dátum és idő <br/><br/>Y: int |Y * 3 hónapot adja hozzá X értékhez.<br/><br/>Például: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks (X, Y) |X: dátum és idő<br/><br/>Y: int |Y * 7 nap feladása X értékre<br/><br/>Például: 9/15/2013 12:00:00 PM + 1 hét = 9/22/2013 12:00:00 PM<br/><br/>Az Y negatív szám megadásával is kivonja a heteket.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears (X, Y) |X: dátum és idő<br/><br/>Y: int |Y évet adja hozzá az X-hez.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>Ha az Y értéket negatív számként határozza meg, akkor az év is kivonható.<br/><br/>Példa: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Nap (X) |X: dátum és idő |Az X nap összetevőjét kapja meg.<br/><br/>Példa: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek (X) |X: dátum és idő |Beolvassa az X hét napjának összetevőjét.<br/><br/>Példa: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear (X) |X: dátum és idő |Az X év összetevője által jelzett nap beolvasása.<br/><br/>Angol nyelvű Példák:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth (X) |X: dátum és idő |Beolvassa az X paraméter hónap összetevőjével jelölt hónap napjait.<br/><br/>Példa: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay (X) |X: dátum és idő |Az X nap (nap összetevője) végének dátumának és időpontjának beolvasása.<br/><br/>Példa: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth (X) |X: dátum és idő |Lekérdezi a hónap végét, amelyet az X paraméter a hónap összetevője képvisel. <br/><br/>Példa: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (a hónap szeptember végét jelölő dátum-idő) |
| Date |StartOfDay (X) |X: dátum és idő |Beolvassa az X paraméter Day összetevőjének napja által jelzett nap kezdetét.<br/><br/>Példa: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |Forrás: (X) |X: sztring |X sztring értelmezése dátum és idő szerint. |
| DateTime |Osztásjelek (X) |X: dátum és idő |Az X paraméter kullancsok tulajdonságának beolvasása. Egy Tick 100 nanoszekundumban. Ennek a tulajdonságnak az értéke a 12:00:00 éjfél óta eltelt kullancsok számát jelöli. január 1., 0,001. |
| Szöveg |Formátum (X) |X: string változó |A szöveg formázása (a `\\'` kombináció használata Escape `'` -karakterrel).|

> [!IMPORTANT]
> Ha egy függvényt egy másik függvényen belül használ, nincs szükség **$$** a belső függvény előtagjának használatára. Például: $ $Text. Format (' PartitionKey EQ \\ ' my_pkey_filter_value \\ ' és RowKey GE \\ ' {0: ÉÉÉÉ-HH-NN óó: PP: SS} \\ ' ', Time. AddHours (SliceStart,-6)). Ebben a példában azt láthatja, hogy **$$** a **Time. AddHours** függvény nem használja az előtagot. 

#### <a name="example"></a>Példa
A következő példában a kaptár tevékenységhez tartozó bemeneti és kimeneti paramétereket a `Text.Format` Function és a SliceStart rendszerváltozó használatával határozzuk meg. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>2\. példa

A következő példában a tárolt eljárási tevékenység DateTime paraméterét a szöveg használatával határozzuk meg. A Format függvény és a SliceStart változó. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>3\. példa
A SliceStart által jelölt nap helyett az előző naptól származó adatok beolvasásához használja a AddDays függvényt az alábbi példában látható módon: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Tekintse meg az [Egyéni dátum-és időformátum-karakterláncok](/dotnet/standard/base-types/custom-date-and-time-format-strings) témakört, amely leírja a különböző formázási beállításokat (például: éé vagy nn).