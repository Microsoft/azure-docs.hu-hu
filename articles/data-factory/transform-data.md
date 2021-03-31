---
title: Adatok átalakítása
description: Az adatátalakítást vagy az adatfeldolgozást Azure Data Factory Hadoop, Azure Machine Learning Studio (klasszikus) vagy Azure Data Lake Analytics használatával.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 0a1eb593e9f9f15f88aefb2fe06706153a4b74a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100361400"
---
# <a name="transform-data-in-azure-data-factory"></a>Adatok átalakítása az Azure Data Factoryben

> [!div class="op_single_selector"]
> * [Adatfolyam hozzárendelése](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (klasszikus)](transform-data-using-machine-learning.md) 
> * [Tárolt eljárás](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks notebook](transform-data-databricks-notebook.md)
> * [Databricks jar](transform-data-databricks-jar.md)
> * [Databricks Python](transform-data-databricks-python.md)
> * [.NET – egyéni](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Áttekintés
Ez a cikk a Azure Data Factory Adatátalakítási tevékenységeit ismerteti, amelyekkel a nyers adatait átalakíthatja és feldolgozhatja az előrejelzések és az elemzések során. Az átalakítási tevékenységek egy számítástechnikai környezetben hajthatók végre, például Azure Databricks vagy Azure HDInsight. Az egyes átalakítási tevékenységekre vonatkozó részletes információkat tartalmazó cikkekre mutató hivatkozásokat tartalmaz.

A Data Factory a következő Adatátalakítási tevékenységeket támogatja, amelyeket akár egyénileg, akár egy másik tevékenységgel összekapcsolva is hozzáadhat a [folyamatokhoz](concepts-pipelines-activities.md) .

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>A Azure Data Factory natív módon történő átalakítása adatfolyamatokkal

### <a name="mapping-data-flows"></a>Adatfolyamok leképezése

Az adatfolyamatok leképezése vizuálisan tervezett adatátalakítások Azure Data Factoryban. Az adatforgalom lehetővé teszi, hogy az adatmérnökök programkód írása nélkül fejlesszenek grafikus Adatátalakítási logikát. Az eredményül kapott adatfolyamatok olyan Azure Data Factory-folyamatokon belül lesznek végrehajtva, amelyek felskálázást használó Spark-fürtöket használnak. Az adatfolyam-tevékenységek a meglévő Data Factory ütemezési, vezérlési, folyamat-és figyelési képességein keresztül is működőképesek. További információ: [az adatfolyamatok leképezése](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Az adathuzavona

A Power Query a Azure Data Factory lehetővé teszi a felhőalapú adatmennyiség huzavona, amely lehetővé teszi a kód nélküli adatelőkészítést a felhőalapú méretezési iteratív. Az adatok huzavona integrálható [Power Query online](/power-query/) -nal, és a Spark-végrehajtással elérhetővé Power Query teszi az adatok huzavona a felhőben. További információ: az [adatok huzavona az ADF-ben](wrangling-overview.md).

## <a name="external-transformations"></a>Külső átalakítások

Opcionálisan saját kezűleg is átalakíthatja az átalakításokat, és saját maga is kezelheti a külső számítási környezetet.

### <a name="hdinsight-hive-activity"></a>HDInsight-struktúra tevékenység
A Data Factory folyamat HDInsight-struktúrájának tevékenysége a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket. A tevékenység részleteiért lásd a [kaptári tevékenységről](transform-data-using-hadoop-hive.md) szóló cikket. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A HDInsight Pig-tevékenység egy Data Factory folyamat során a Pig-lekérdezéseket a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenység részleteiért lásd a [Pig-tevékenységről](transform-data-using-hadoop-pig.md) szóló cikket. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A Data Factory-folyamat HDInsight MapReduce-tevékenysége a MapReduce-programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenység részleteiért tekintse meg a [MapReduce-tevékenységről](transform-data-using-hadoop-map-reduce.md) szóló cikket.

### <a name="hdinsight-streaming-activity"></a>HDInsight streaming-tevékenység
A Data Factory-folyamat HDInsight adatfolyam-továbbítási tevékenysége a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Hadoop streaming-programokat. A tevékenység részleteiért lásd: [HDInsight streaming Activity](transform-data-using-hadoop-streaming.md) .

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A Data Factory folyamat HDInsight Spark-tevékenysége Spark-programokat hajt végre a saját HDInsight-fürtön. Részletekért lásd: [Spark-programok Meghívása Azure Data Factoryról](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (klasszikus) tevékenységek
A Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre egy közzétett Azure Machine Learning Studio (klasszikus) webszolgáltatást használó folyamatokat a prediktív elemzésekhez. A [Batch-végrehajtási tevékenység](transform-data-using-machine-learning.md) egy Azure Data Factory folyamat során meghívja a Studio (klasszikus) webszolgáltatás használatát, hogy előrejelzéseket készítsen a Batch-ben lévő adatairól.

Az idő múlásával a Studio (klasszikus) pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Miután végzett az újraképzéssel, frissítenie kell a pontozási webszolgáltatást az áttelepített gépi tanulási modellel. Az [erőforrás frissítése tevékenységgel](update-machine-learning-models.md) frissítheti az újonnan betanított modellt használó webszolgáltatást.  

A Studio (klasszikus) tevékenységek részletes ismertetését lásd: [Azure Machine learning Studio (klasszikus) tevékenységek használata](transform-data-using-machine-learning.md) . 

### <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
A Data Factory folyamat SQL Server tárolt eljárása tevékenységgel egy tárolt eljárást hívhat meg a következő adattárak egyikében: Azure SQL Database, Azure szinapszis Analytics, SQL Server adatbázis a vállalatban vagy egy Azure-beli virtuális gépen. Részletekért lásd a [tárolt eljárási tevékenységről](transform-data-using-stored-procedure.md) szóló cikket.  

### <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Data Lake Analytics tevékenység
Data Lake Analytics U-SQL-tevékenység egy U-SQL-szkriptet futtat egy Azure Data Lake Analytics-fürtön. Részletekért lásd: [adatelemzési U-SQL-tevékenység](transform-data-using-data-lake-analytics.md) . 

### <a name="databricks-notebook-activity"></a>Databricks-jegyzetfüzet tevékenység

A Data Factory folyamat Azure Databricks notebook tevékenysége egy Databricks-jegyzetfüzetet futtat a Azure Databricks munkaterületen. A Azure Databricks felügyelt platform a Apache Spark futtatásához. Lásd: [az adatátalakítás Databricks notebook futtatásával](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks jar-tevékenység

A Data Factory folyamat Azure Databricks jar-tevékenysége egy Spark jar-t futtat a Azure Databricks-fürtben. A Azure Databricks felügyelt platform a Apache Spark futtatásához. Lásd: az [Adatátalakítások jar-tevékenység futtatásával Azure Databricksban](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks Python-tevékenység

A Data Factory folyamat Azure Databricks Python-tevékenysége egy Python-fájlt futtat a Azure Databricks-fürtben. A Azure Databricks felügyelt platform a Apache Spark futtatásához. Lásd: az [adatátalakítás a Azure Databricks Python-tevékenység futtatásával](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Egyéni tevékenység
Ha olyan módon kell adatátalakítást végeznie, amelyet a Data Factory nem támogat, létrehozhat egy egyéni tevékenységet a saját adatfeldolgozási logikával, és felhasználhatja a folyamat tevékenységeit. Az egyéni .NET-tevékenységeket beállíthatja úgy, hogy az Azure Batch vagy egy Azure HDInsight-fürtön fusson. További részletekért tekintse meg az [egyéni tevékenységek használata](transform-data-using-dotnet-custom-activity.md) című cikket. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

### <a name="compute-environments"></a>Számítási környezetek
Hozzon létre egy társított szolgáltatást a számítási környezethez, majd használja a társított szolgáltatást egy átalakítási tevékenység definiálásakor. Data Factory által támogatott számítási környezeteknek két típusa van. 

- **Igény szerinti**: ebben az esetben a számítástechnikai környezetet teljes mértékben a Data Factory felügyeli. A Data Factory szolgáltatás automatikusan hozza létre, mielőtt egy feladatot elküld az adatok feldolgozásához, és eltávolítja azokat a feladatok befejezésekor. Konfigurálhatja és szabályozhatja az igény szerinti számítási környezet részletes beállításait a feladatok végrehajtásához, a fürtözés kezeléséhez és a rendszerindítás műveleteihez. 
- **Saját** környezet használata: ebben az esetben regisztrálhat saját számítástechnikai környezetet (például HDInsight-fürtöt) a Data Factory társított szolgáltatásként. A számítástechnikai környezetet Ön felügyeli, és a Data Factory szolgáltatás ezt használja a tevékenységek végrehajtásához. 

A Data Factory által támogatott számítási szolgáltatások megismeréséhez tekintse meg a [számítási társított szolgáltatások](compute-linked-services.md) című cikket. 

## <a name="next-steps"></a>Következő lépések
Tekintse át a következő oktatóanyagot, amely egy átalakítási tevékenység használatát szemlélteti: [oktatóanyag: az adatgyűjtés a Spark használatával](tutorial-transform-data-spark-powershell.md)
