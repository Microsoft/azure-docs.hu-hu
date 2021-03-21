---
title: Az adatátalakítás a Databricks Pythonral
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át adatátalakítást egy Databricks Python-tevékenység Azure Data Factory-folyamatban való futtatásával.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373946"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Az adatátalakítást egy Python-tevékenység futtatásával Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A [Data Factory folyamat](concepts-pipelines-activities.md) Azure Databricks Python-tevékenysége egy Python-fájlt futtat a Azure Databricks-fürtben. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről. A Azure Databricks felügyelt platform a Apache Spark futtatásához.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python-tevékenység definíciója

Itt látható a Databricks Python-tevékenység JSON-definíciója:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python-tevékenység tulajdonságai

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|---|---|---|
|name|A folyamatban szereplő tevékenység neve.|Yes|
|leírás|A tevékenység működését leíró szöveg|No|
|típus|A Databricks Python-tevékenység esetén a tevékenység típusa DatabricksSparkPython.|Yes|
|linkedServiceName|Annak a Databricks társított szolgáltatásnak a neve, amelyen a Python-tevékenység fut. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.|Yes|
|pythonFile|A végrehajtandó Python-fájl URI-ja. Csak DBFS elérési utak támogatottak.|Yes|
|parameters|A Python-fájlnak átadandó parancssori paraméterek. Ez a karakterláncok tömbje.|No|
|kódtárak|Azoknak a táraknak a listája, amelyek a feladatot végrehajtó fürtön lesznek telepítve. <sztring, objektum> tömbje lehet.|No|

## <a name="supported-libraries-for-databricks-activities"></a>Támogatott kódtárak a databricks-tevékenységekhez

A fenti Databricks-tevékenység definíciójában a következő típustár-típusokat adhatja meg: *jar*, *Egg*, *Maven*, *PyPI*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

További részletekért tekintse meg a [Databricks dokumentációját](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) .

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése a Databricks-ben

### <a name="you-can-use-the-workspace-ui"></a>Használhatja a munkaterület felhasználói felületét:

1. [A Databricks-munkaterület felhasználói felületének használata](/azure/databricks/libraries/#create-a-library)

2. A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja az [DATABRICKS CLI](/azure/databricks/dev-tools/cli/#install-the-cli)-t.

   A jar-kódtárak általában a dbfs:/FileStore/tégelyek alatt tárolódnak, miközben a felhasználói felületet használják. A CLI-n keresztül listázhatja az összeset: *databricks FS ls dbfs:/FileStore/Job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Vagy használhatja a Databricks CLI-t is:

1. [A könyvtár másolásának követése a DATABRICKS CLI használatával](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. A Databricks CLI használata [(telepítési lépések)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Példa a JAR dbfs való másolására: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
