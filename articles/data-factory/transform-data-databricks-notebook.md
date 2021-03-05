---
title: Adat átalakítása Databricks Notebooktal
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át egy Databricks notebookot a Azure Data Factoryban.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: fea572c2e75f62b5e7e7b4634e37da348bdcdaf1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183488"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Adat átalakítása Databricks-jegyzetfüzet futtatásával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A [Data Factory folyamat](concepts-pipelines-activities.md) Azure Databricks notebook tevékenysége egy Databricks-jegyzetfüzetet futtat a Azure Databricks munkaterületen. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről. A Azure Databricks felügyelt platform a Apache Spark futtatásához.

## <a name="databricks-notebook-activity-definition"></a>Databricks-jegyzetfüzet tevékenységének definíciója

Itt látható a Databricks-jegyzetfüzetek tevékenységének JSON-definíciója:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks-jegyzetfüzet tevékenység tulajdonságai

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|---|---|---|
|name|A folyamatban szereplő tevékenység neve.|Igen|
|leírás|A tevékenység működését leíró szöveg|Nem|
|típus|A Databricks notebook tevékenység esetén a DatabricksNotebook a tevékenység típusa.|Igen|
|linkedServiceName|Azon Databricks társított szolgáltatás neve, amelyen a Databricks-jegyzetfüzet fut. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.|Igen|
|notebookPath|A Databricks-munkaterületen futtatandó jegyzetfüzetek abszolút elérési útja. Ezt az elérési utat perjeltel kell kezdeni.|Igen|
|baseParameters|Key-Value pár tömbje. Az alapparaméterek az egyes tevékenységek futtatásához használhatók. Ha a jegyzetfüzet nem megadott paramétert használ, a rendszer a jegyzetfüzet alapértelmezett értékét fogja használni. További információ a Databricks- [jegyzetfüzetekben](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)található paraméterekről.|Nem|
|kódtárak|Azoknak a táraknak a listája, amelyek a feladatot végrehajtó fürtön lesznek telepítve. A tömbje lehet \<string, object> .|Nem|

## <a name="supported-libraries-for-databricks-activities"></a>Támogatott kódtárak a Databricks-tevékenységekhez

A fenti Databricks-tevékenység definíciójában a következő típustár-típusokat adhatja meg: *jar*, *Egg*, *WHL*, *Maven*, *PyPI*, *Cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Paraméterek átadása notebookok és Data Factory között

Az adat-előállító paramétereit átadhatja jegyzetfüzeteknek a databricks tevékenység *baseParameters* tulajdonságának használatával.

Bizonyos esetekben szükség lehet arra, hogy bizonyos értékeket továbbítson a jegyzetfüzetből a adat-előállítónak, amely a adat-előállító vezérlési folyamatához (feltételes ellenőrzésekhez) használható, vagy az alsóbb rétegbeli tevékenységek által felhasználható (legfeljebb 2 MB méretű).

1. A jegyzetfüzetben hívhatja a [dbutils. notebook. Exit ("ReturnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) és a megfelelő "ReturnValue" értéket a rendszer a adat-előállítónak.

2. A kimenetet az adatgyárban használhatja kifejezés (például) használatával `@{activity('databricks notebook activity name').output.runOutput}` . 

   > [!IMPORTANT]
   > Ha JSON-objektumot továbbít, az értékeket a tulajdonságok nevének hozzáfűzésével kérheti le. Például: `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése a Databricks-ben

### <a name="you-can-use-the-workspace-ui"></a>Használhatja a munkaterület felhasználói felületét:

1. [A Databricks-munkaterület felhasználói felületének használata](/azure/databricks/libraries/#create-a-library)

2. A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja az [DATABRICKS CLI](/azure/databricks/dev-tools/cli/#install-the-cli)-t.

   A jar-kódtárak általában a dbfs:/FileStore/tégelyek alatt tárolódnak, miközben a felhasználói felületet használják. A CLI-n keresztül listázhatja az összeset: *databricks FS ls dbfs:/FileStore/Job-jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Vagy használhatja a Databricks CLI-t is:

1. [A könyvtár másolásának követése a DATABRICKS CLI használatával](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. A Databricks CLI használata [(telepítési lépések)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Példa a JAR dbfs való másolására: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
