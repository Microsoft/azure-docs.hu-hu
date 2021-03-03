---
title: Keresési tevékenység a Azure Data Factory
description: Ismerje meg, hogyan kereshet meg egy külső forrásból származó értéket a keresési tevékenység használatával. Ezt a kimenetet a sikeres tevékenységek továbbra is hivatkozhatják.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2021
ms.openlocfilehash: 4b2fb49899b6a676520fe0912dd122dd72cce023
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712912"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Keresési tevékenység a Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A keresési tevékenység lekérheti az adatkészletet a Azure Data Factory által támogatott adatforrásokból. azt is megteheti, hogy dinamikusan meghatározza, hogy mely objektumok működhetnek egy későbbi tevékenységben, ahelyett, hogy az objektum nevét keményen kellene kezelni. Néhány objektum például fájlok és táblák.

A keresési tevékenység beolvassa és visszaadja egy konfigurációs fájl vagy tábla tartalmát. Egy lekérdezés vagy tárolt eljárás végrehajtásának eredményét is visszaadja. A kimenet lehet egy egypéldányos érték vagy attribútumok tömbje, amely egy későbbi másolási, átalakítási vagy vezérlési folyamat tevékenységekben, például a ForEach tevékenységben is felhasználható.

## <a name="supported-capabilities"></a>Támogatott képességek

Vegye figyelembe a következőket:

- A keresési tevékenység legfeljebb **5000 sort** tud visszaadni; Ha az eredményhalmaz több rekordot tartalmaz, a rendszer az első 5000 sort adja vissza.
- A keresési tevékenység kimenete legfeljebb **4 MB** méretű, a tevékenységek sikertelenek lesznek, ha a méret meghaladja a korlátot. 
- Az időkorlát előtti keresési tevékenység leghosszabb időtartama **24 óra**.
- Ha lekérdezési vagy tárolt eljárást használ az adatkereséshez, ügyeljen arra, hogy a rendszer visszaadja az egyik eredményhalmaz értékét. Ellenkező esetben a keresési tevékenység sikertelen lesz.

A keresési tevékenység a következő adatforrásokat támogatja. 

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Típus tulajdonságai

Név | Leírás | Típus | Kötelező?
---- | ----------- | ---- | --------
adatkészlet | A keresés adatkészlet-hivatkozását adja meg. Az egyes kapcsolódó összekötők című cikk **adatkészlet tulajdonságai** szakaszában talál részleteket. | Kulcs/érték pár | Igen
source | Adatkészlet-specifikus forrás tulajdonságokat tartalmaz, ugyanazokat a másolási tevékenység forrását. A részletek a **másolási tevékenység tulajdonságai** szakaszban olvashatók a kapcsolódó összekötők cikkeiben. | Kulcs/érték pár | Igen
firstRowOnly | Azt jelzi, hogy csak az első sort vagy az összes sort kívánja-e visszaadni. | Logikai | Nem. A mező alapértelmezett értéke: `true`.

> [!NOTE]
> 
> * A **ByteArray** típusú forrásoldali oszlopok nem támogatottak.
> * A **struktúra** nem támogatott az adatkészlet-definíciókban. Szöveges formátumú fájlok esetében a fejlécsor használatával adja meg az oszlop nevét.
> * Ha a keresési forrás egy JSON-fájl, a `jsonPathDefinition` JSON-objektum átformálásának beállítása nem támogatott. A rendszer lekéri a teljes objektumot.

## <a name="use-the-lookup-activity-result"></a>Keresési tevékenység eredményének használata

A keresési eredmény a `output` tevékenység futtatási eredményének szakaszában lesz visszaadva.

* **Ha a `firstRowOnly` értéke `true` (alapértelmezett)**, a kimeneti formátum a következő kódban látható. A keresési eredmény egy rögzített kulcs alatt található `firstRow` . Ha a következő tevékenység eredményét szeretné használni, használja a mintázatát  `@{activity('LookupActivity').output.firstRow.table}` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* Ha a értéke, a kimeneti formátum a következő kódban látható. **`firstRowOnly` `false`** Egy `count` mező jelzi, hogy a rendszer hány rekordot ad vissza. A részletes értékek egy rögzített tömb alatt jelennek meg `value` . Ilyen esetben a keresési tevékenységet egy [foreach-tevékenység](control-flow-for-each-activity.md)követi. A `value` tömböt a foreach tevékenység mezőjébe kell átadni `items` a mintázatának használatával `@activity('MyLookupActivity').output.value` . A tömb elemeinek eléréséhez `value` használja a következő szintaxist: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Például: `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Példa

Ebben a példában a folyamat két tevékenységet tartalmaz: **Keresés** és **Másolás**. A másolási tevékenység az Azure SQL Database-példányban található SQL-táblából másolja az Azure Blob Storage-ba. Az SQL-tábla neve egy JSON-fájlban tárolódik a blob Storage-ban. A keresési tevékenység futásidőben megkeresi a tábla nevét. A JSON dinamikusan módosul a módszer használatával. Nem kell újratelepítenie a folyamatokat vagy adatkészleteket. 

Ez a példa csak az első sor keresését mutatja be. Az összes sorhoz való kereséshez, valamint az eredmények ForEach-tevékenységgel való láncolásához tekintse meg a [több táblázat másolása tömegesen a Azure Data Factory használatával](tutorial-bulk-copy.md)című témakör mintáit.


### <a name="pipeline"></a>Folyamat

- A keresési tevékenység a **LookupDataset** használatára van konfigurálva, amely az Azure Blob Storage-ban található helyre hivatkozik. A keresési tevékenység beolvassa az SQL-táblázat nevét egy olyan JSON-fájlból, amely ezen a helyen található. 
- A másolási tevékenység a keresési tevékenység kimenetét használja, amely az SQL-tábla neve. A **SourceDataset** **Táblanév** tulajdonsága a keresési tevékenység kimenetének használatára van konfigurálva. A másolási tevékenység az SQL-táblából másolja az adatait az Azure Blob Storage egyik helyére. A helyet a **SinkDataset** tulajdonság határozza meg. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Keresési adatkészlet

A **keresési** adatkészlet a **AzureBlobStorageLinkedService** típus által megadott Azure Storage keresési mappában található fájl **sourcetable.js** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>Másolási tevékenység **forrás** -adatkészlete

A **forrás** -adatkészlet a keresési tevékenység kimenetét használja, amely az SQL-tábla neve. A másolási tevékenység az adott SQL-táblából másolja át az adatait az Azure Blob Storage egyik helyére. **A helyet a fogadó adatkészlet adja** meg. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Másolási tevékenység fogadó **adatkészlete**

A másolási tevékenység átmásolja az adatait az SQL-táblából az Azure Storage **CSV** -mappájában lévő **filebylookup.csv** fájlba. A fájlt a **AzureBlobStorageLinkedService** tulajdonság határozza meg. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jsbekapcsolva

A következő két típusú formátumot használhatja a **sourcetable.js** fájlhoz:.

#### <a name="set-of-objects"></a>Objektumok halmaza

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Objektumok tömbje

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Korlátozások és megkerülő megoldások

Íme néhány korlátozás a keresési tevékenységhez és a javasolt megkerülő megoldásokhoz.

| Korlátozás | Áthidaló megoldás |
|---|---|
| A keresési tevékenység legfeljebb 5 000 sort tartalmaz, és legfeljebb 4 MB méretűnek kell lennie. | Tervezzen olyan kétszintű folyamatot, amelyben a külső folyamat egy belső folyamaton keresztül történik, amely nem haladja meg a maximálisan megengedett sorokat vagy méretet. |
| | |

## <a name="next-steps"></a>Következő lépések
Tekintse meg a Data Factory által támogatott egyéb vezérlési folyamatokat: 

- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [ForEach tevékenység](control-flow-for-each-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
