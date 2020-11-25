---
title: Adat átalakítása Databricks Notebooktal
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át egy Databricks notebook futtatásával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018545"
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
|name|A folyamatban szereplő tevékenység neve.|Yes|
|leírás|A tevékenység működését leíró szöveg|No|
|típus|A Databricks notebook tevékenység esetén a DatabricksNotebook a tevékenység típusa.|Yes|
|linkedServiceName|Azon Databricks társított szolgáltatás neve, amelyen a Databricks-jegyzetfüzet fut. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.|Yes|
|notebookPath|A Databricks-munkaterületen futtatandó jegyzetfüzetek abszolút elérési útja. Ezt az elérési utat perjeltel kell kezdeni.|Yes|
|baseParameters|Key-Value pár tömbje. Az alapparaméterek az egyes tevékenységek futtatásához használhatók. Ha a jegyzetfüzet nem megadott paramétert használ, a rendszer a jegyzetfüzet alapértelmezett értékét fogja használni. További információ a Databricks- [jegyzetfüzetekben](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)található paraméterekről.|No|
|kódtárak|Azoknak a táraknak a listája, amelyek a feladatot végrehajtó fürtön lesznek telepítve. A tömbje lehet \<string, object> .|No|


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

További részletekért tekintse meg a [Databricks dokumentációját](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) .

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Paraméterek átadása notebookok és Data Factory között

Az adat-előállító paramétereit átadhatja jegyzetfüzeteknek a databricks tevékenység *baseParameters* tulajdonságának használatával. 

Bizonyos esetekben szükség lehet arra, hogy bizonyos értékeket továbbítson a jegyzetfüzetből a adat-előállítónak, amely a adat-előállító vezérlési folyamatához (feltételes ellenőrzésekhez) használható, vagy az alsóbb rétegbeli tevékenységek által felhasználható (legfeljebb 2 MB méretű). 

1. A jegyzetfüzetben hívhatja a [dbutils. notebook. Exit ("ReturnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) és a megfelelő "ReturnValue" értéket a rendszer a adat-előállítónak.

2. A kimenetet az adatgyárban használhatja kifejezés (például) használatával `'@activity('databricks notebook activity name').output.runOutput'` . 

   > [!IMPORTANT]
   > Ha JSON-objektumot továbbít, az értékeket a tulajdonságok nevének hozzáfűzésével kérheti le. Például: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése a Databricks-ben

#### <a name="using-databricks-workspace-ui"></a>[Databricks-munkaterület felhasználói felületének használata](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja a [DATABRICKS parancssori felületet (telepítés)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

A jar-kódtárak általában a dbfs:/FileStore/tégelyek alatt tárolódnak, miközben a felhasználói felületet használják. A CLI: *databricks FS ls dbfs:/FileStore/tégelyek* segítségével listázhatja az összeset.



#### <a name="copy-library-using-databricks-cli"></a>[Könyvtár másolása a Databricks CLI használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Például: *databricks FS CP sparkpi-Assembly-0,1. jar dbfs:/FileStore/tégelyek*
