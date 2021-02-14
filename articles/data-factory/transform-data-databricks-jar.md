---
title: Az adatátalakítás a Databricks jar-val
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át egy Databricks jar-t egy Azure Data Factory folyamaton belül.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374014"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Az adatátalakításhoz jar-tevékenység fut Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A [Data Factory folyamat](concepts-pipelines-activities.md) Azure Databricks jar-tevékenysége egy Spark jar-t futtat a Azure Databricks-fürtben. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről. A Azure Databricks felügyelt platform a Apache Spark futtatásához.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks jar-tevékenység definíciója

Itt látható a Databricks jar-tevékenység JSON-definíciója:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks jar-tevékenység tulajdonságai

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|:--|---|:-:|
|name|A folyamatban szereplő tevékenység neve.|Yes|
|leírás|A tevékenység működését leíró szöveg|No|
|típus|A Databricks jar tevékenység esetén a tevékenység típusa DatabricksSparkJar.|Yes|
|linkedServiceName|Annak a Databricks társított szolgáltatásnak a neve, amelyen a jar-tevékenység fut. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.|Yes|
|mainClassName|A végrehajtandó fő metódust tartalmazó osztály teljes neve. Ennek az osztálynak szerepelnie kell egy könyvtárként megadott JAR-fájlban. A JAR-fájlok több osztályt is tartalmazhatnak. Mindegyik osztály tartalmazhatja a fő metódust.|Yes|
|parameters|A Main metódusnak átadott paraméterek. Ez a tulajdonság karakterláncok tömbje.|No|
|kódtárak|Azoknak a táraknak a listája, amelyek a feladatot végrehajtó fürtön lesznek telepítve. <sztring, objektum> tömbje lehet.|Igen (legalább egy mainClassName metódust tartalmaz)|

> [!NOTE]
> **Ismert probléma** – ha ugyanazt az [interaktív fürtöt](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) használja az egyidejű Databricks jar-tevékenységek futtatásához (a fürt újraindítása nélkül), akkor ismert probléma van a Databricks, ahol az első tevékenység paramétereit is a következő tevékenységek használják majd. Emiatt a rendszer helytelen paramétereket ad át a következő feladatoknak. Ezzel a megoldással csökkentheti a [feladatok fürtjét](compute-linked-services.md#example---using-new-job-cluster-in-databricks) .

## <a name="supported-libraries-for-databricks-activities"></a>Támogatott kódtárak a databricks-tevékenységekhez

Az előző Databricks-tevékenység definíciójában a következő típustár-típusokat adta meg:,,, `jar` `egg` `maven` `pypi` , `cran` .

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

További információt a [Databricks dokumentációjában](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) talál.

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése a Databricks-ben

### <a name="you-can-use-the-workspace-ui"></a>Használhatja a munkaterület felhasználói felületét:

1. [A Databricks-munkaterület felhasználói felületének használata](/azure/databricks/libraries/#create-a-library)

2. A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja az [DATABRICKS CLI](/azure/databricks/dev-tools/cli/#install-the-cli)-t.

   A jar-kódtárak általában a dbfs:/FileStore/tégelyek alatt tárolódnak, miközben a felhasználói felületet használják. A CLI-n keresztül listázhatja az összeset: *databricks FS ls dbfs:/FileStore/Job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Vagy használhatja a Databricks CLI-t is:

1. [A könyvtár másolásának követése a DATABRICKS CLI használatával](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. A Databricks CLI használata [(telepítési lépések)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Példa a JAR dbfs való másolására: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Következő lépések

A szolgáltatás tizenegy perces bemutatása és bemutatása esetén tekintse meg a [videót](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
