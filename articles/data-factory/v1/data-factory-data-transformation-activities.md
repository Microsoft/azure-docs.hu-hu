---
title: 'Adatátalakítás: az Adatátalakítási folyamat & '
description: Megtudhatja, hogyan alakíthatja át az adatkezelési és-adatfeldolgozási Azure Data Factory Hadoop, Azure Machine Learning Studio (klasszikus) vagy Azure Data Lake Analytics használatával.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: c9818bfd2a9519cd14d34ecc810179d66aa57e52
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363984"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Az adatátalakítás Azure Data Factory 1. verzióban
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (klasszikus)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Tárolt eljárás](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [.NET – egyéni](data-factory-use-custom-activities.md)

## <a name="overview"></a>Áttekintés
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg a Data Factory Adatátalakítási tevékenységeit](../transform-data.md)ismertető témakört.

Ez a cikk a Azure Data Factory Adatátalakítási tevékenységeit ismerteti, amelyekkel a nyers adatait átalakíthatja és feldolgozhatja a jóslatok és az elemzések során. Az átalakítási tevékenységek olyan számítástechnikai környezetben hajthatók végre, mint például az Azure HDInsight-fürt vagy egy Azure Batch. Az egyes átalakítási tevékenységekre vonatkozó részletes információkat tartalmazó cikkekre mutató hivatkozásokat tartalmaz.

A Data Factory a következő Adatátalakítási tevékenységeket támogatja, amelyeket akár egyénileg, akár egy másik tevékenységgel összekapcsolva is hozzáadhat a [folyamatokhoz](data-factory-create-pipelines.md) .

> [!NOTE]
> Részletes útmutatást a [folyamat létrehozása struktúra-átalakítással](data-factory-build-your-first-pipeline.md) című cikkben talál.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight-struktúra tevékenység
A Data Factory folyamat HDInsight-struktúrájának tevékenysége a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a kaptár-lekérdezéseket. A tevékenység részleteiért lásd a [kaptári tevékenységről](data-factory-hive-activity.md) szóló cikket. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-tevékenység
A HDInsight Pig-tevékenység egy Data Factory folyamat során a Pig-lekérdezéseket a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenység részleteiért lásd a [Pig-tevékenységről](data-factory-pig-activity.md) szóló cikket. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-tevékenység
A Data Factory-folyamat HDInsight MapReduce-tevékenysége a MapReduce-programokat saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre. A tevékenység részleteiért tekintse meg a [MapReduce-tevékenységről](data-factory-map-reduce.md) szóló cikket.

## <a name="hdinsight-streaming-activity"></a>HDInsight streaming-tevékenység
A Data Factory-folyamat HDInsight adatfolyam-továbbítási tevékenysége a saját vagy igény szerinti Windows/Linux-alapú HDInsight-fürtön hajtja végre a Hadoop streaming-programokat. A tevékenység részleteiért lásd: [HDInsight streaming Activity](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-tevékenység
A Data Factory folyamat HDInsight Spark-tevékenysége Spark-programokat hajt végre a saját HDInsight-fürtön. Részletekért lásd: [Spark-programok Meghívása Azure Data Factoryról](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (klasszikus) tevékenységek
A Azure Data Factory lehetővé teszi, hogy könnyedén hozzon létre egy közzétett Azure Machine Learning Studio (klasszikus) webszolgáltatást használó folyamatokat a prediktív elemzésekhez. A [Batch-végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) egy Azure Data Factory folyamat során meghívja a Studio (klasszikus) webszolgáltatás használatát, hogy előrejelzéseket készítsen a Batch-ben lévő adatairól.

Az idő múlásával a Studio (klasszikus) pontozási kísérletek prediktív modelljeit új bemeneti adatkészletek használatával kell áttanítani. Miután végzett az újraképzéssel, frissítenie kell a pontozási webszolgáltatást az áttelepített gépi tanulási modellel. Az [erőforrás frissítése tevékenységgel](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) frissítheti az újonnan betanított modellt használó webszolgáltatást.  

A Studio (klasszikus) tevékenységek részletes ismertetését lásd: [Azure Machine learning Studio (klasszikus) tevékenységek használata](data-factory-azure-ml-batch-execution-activity.md) . 

## <a name="stored-procedure-activity"></a>Tárolt eljárási tevékenység
A Data Factory folyamat SQL Server tárolt eljárása tevékenységgel egy tárolt eljárást hívhat meg a következő adattárak egyikében: Azure SQL Database, Azure szinapszis Analytics, SQL Server adatbázis a vállalatban vagy egy Azure-beli virtuális gépen. Részletekért lásd a [tárolt eljárási tevékenységről](data-factory-stored-proc-activity.md) szóló cikket.  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Data Lake Analytics tevékenység
Data Lake Analytics U-SQL-tevékenység egy U-SQL-szkriptet futtat egy Azure Data Lake Analytics-fürtön. Részletekért lásd: [adatelemzési U-SQL-tevékenység](data-factory-usql-activity.md) . 

## <a name="net-custom-activity"></a>.NET egyéni tevékenység
Ha olyan módon kell adatátalakítást végeznie, amelyet a Data Factory nem támogat, létrehozhat egy egyéni tevékenységet a saját adatfeldolgozási logikával, és felhasználhatja a folyamat tevékenységeit. Az egyéni .NET-tevékenységeket beállíthatja úgy, hogy az Azure Batch vagy egy Azure HDInsight-fürtön fusson. További részletekért tekintse meg az [egyéni tevékenységek használata](data-factory-use-custom-activities.md) című cikket. 

Létrehozhat egy egyéni tevékenységet R-parancsfájlok futtatására egy olyan HDInsight-fürtön, amelyen telepítve van az R. Lásd: [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (R-parancsfájl futtatása az Azure Data Factory használatával). 

## <a name="compute-environments"></a>Számítási környezetek
Hozzon létre egy társított szolgáltatást a számítási környezethez, majd használja a társított szolgáltatást egy átalakítási tevékenység definiálásakor. Data Factory által támogatott számítási környezeteknek két típusa van. 

1. **Igény szerinti**: ebben az esetben a számítástechnikai környezetet teljes mértékben a Data Factory felügyeli. A Data Factory szolgáltatás automatikusan hozza létre, mielőtt egy feladatot elküld az adatok feldolgozásához, és eltávolítja azokat a feladatok befejezésekor. Konfigurálhatja és szabályozhatja az igény szerinti számítási környezet részletes beállításait a feladatok végrehajtásához, a fürtözés kezeléséhez és a rendszerindítás műveleteihez. 
2. **Saját** környezet használata: ebben az esetben regisztrálhat saját számítástechnikai környezetet (például HDInsight-fürtöt) a Data Factory társított szolgáltatásként. A számítástechnikai környezetet Ön felügyeli, és a Data Factory szolgáltatás ezt használja a tevékenységek végrehajtásához. 

A Data Factory által támogatott számítási szolgáltatások megismeréséhez tekintse meg a [számítási társított szolgáltatások](data-factory-compute-linked-services.md) című cikket. 

## <a name="summary"></a>Összefoglalás
Azure Data Factory a következő Adatátalakítási tevékenységeket és a tevékenységek számítási környezeteit támogatja. Az átalakítási tevékenységek hozzáadhatók a folyamatokhoz, akár külön, akár egy másik tevékenységgel.

| Adatátalakítási tevékenység | Számítási környezet |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Azure Machine Learning Studio (klasszikus) tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Tárolt eljárás](data-factory-stored-proc-activity.md) |Azure SQL, Azure szinapszis Analytics vagy SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] vagy Azure Batch |

