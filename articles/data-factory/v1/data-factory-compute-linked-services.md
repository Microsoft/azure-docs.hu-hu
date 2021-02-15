---
title: Azure Data Factory 1-es verzió által támogatott számítási környezetek
description: Ismerje meg azokat a számítási környezeteket, amelyeket Azure Data Factory folyamatokban (például Azure HDInsight) használhat az adatok átalakításához és feldolgozásához.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 07e13036a427ff7ff4f0cbbd2dfd6fbe272bf915
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377193"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Azure Data Factory 1-es verzió által támogatott számítási környezetek
> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a következőt: a [társított szolgáltatások számítása a](../compute-linked-services.md)alkalmazásban.

Ez a cikk az adatok feldolgozásához és átalakításához használható számítási környezeteket ismerteti. Emellett részletesen ismerteti a különböző konfigurációkat (igény szerint és saját maga is), amelyet a Data Factory támogat, ha olyan társított szolgáltatásokat konfigurál, amelyek összekapcsolják ezeket a számítási környezeteket egy Azure-beli adat-előállítóval.

Az alábbi táblázat felsorolja a Data Factory által támogatott számítási környezeteket, valamint a rajtuk futó tevékenységeket. 

| Számítási környezet                      | Tevékenységek                               |
| ---------------------------------------- | ---------------------------------------- |
| [Igény szerinti Azure HDInsight-fürt](#azure-hdinsight-on-demand-linked-service) vagy [saját HDInsight-fürt](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [kaptár](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klasszikus)](#azure-machine-learning-studio-classic-linked-service) | [Studio (klasszikus) tevékenységek: kötegelt végrehajtás és erőforrás frissítése](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure szinapszis Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>A Data Factory támogatott HDInsight-verziók
Az Azure HDInsight egyszerre több Hadoop-fürt verzióját is támogatja. Minden támogatott verzió a Hortonworks adatplatform (HDP) terjesztésének egy adott verzióját, valamint az elosztásban található összetevőket is létrehoz. 

A Microsoft frissíti a támogatott HDInsight-verziók listáját a legújabb Hadoop-ökoszisztéma-összetevőkkel és-javításokkal. Részletes információkért lásd: [támogatott HDInsight-verziók](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> A Linux-alapú HDInsight 3,3-es verziójának kivonulása 2017. július 31-én megszűnt. A HDInsight újabb verziójának teszteléséhez és frissítéséhez az 1. verziójú, igény szerinti HDInsight társított szolgáltatások ügyfeleinket 2017. december 15-én adták meg. Data Factory A Windows-alapú HDInsight 2018. július 31-én megszűnik.
>
> 

### <a name="after-the-retirement-date"></a>A kivonulási dátum után 

2017. december 15. után:

- Már nem hozhat létre a Linux-alapú HDInsight 3,3-es (vagy korábbi verziójú) fürtöket egy igény szerinti HDInsight társított szolgáltatás használatával Data Factory az 1. verzióban. 
- Ha a [ **osType** és a **verzió** tulajdonságai](#azure-hdinsight-on-demand-linked-service) nincsenek explicit módon meghatározva egy meglévő Data Factory 1. verziójú, igény szerinti HDInsight társított szolgáltatáshoz, az alapértelmezett érték a **verzió = 3.1, osType = Windows** – **Version = \<latest HDI default version\> ( https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , osType = Linux**) értékre módosul.

2018. július 31-ig:

- A Windows-alapú HDInsight-fürtöket már nem hozhatja létre egy igény szerinti HDInsight társított szolgáltatás használatával Data Factory az 1. verzióban. 

### <a name="recommended-actions"></a>Ajánlott műveletek

- Annak érdekében, hogy a legújabb Hadoop ökoszisztéma-összetevőket és-javításokat is használhassa, frissítse az érintett Data Factory 1. verziójú, igény szerinti HDInsight társított szolgáltatási definíciók [ **OsType** és **verziójának** tulajdonságait](#azure-hdinsight-on-demand-linked-service) az újabb Linux-alapú HDInsight-verzióra (HDInsight 3,6). 
- 2017. december 15. előtt Data Factory tesztelje az 1. verziójú kaptár, a Pig, a MapReduce és a Hadoop streaming tevékenységeket, amelyek az érintett társított szolgáltatásra hivatkoznak. Győződjön meg arról, hogy kompatibilisek az új **osType** és a **verzió** alapértelmezett értékeivel (**Version = 3.6**, **OsType = Linux**) vagy a explicit HDInsight-verzióval és az operációs rendszer típusával, amelyre frissíteni szeretne. 
  További információ a kompatibilitásról: [áttelepítés Windows-alapú HDInsight-fürtről Linux-alapú fürtre](../../hdinsight/index.yml) , valamint [a HDInsight-hez elérhető Hadoop-összetevők és-verziók](../../hdinsight/hdinsight-component-versioning.md). 
- Ha **a Windows-** alapú HDInsight-2017 fürtök létrehozásához Data Factory egy igény szerinti HDInsight **társított szolgáltatással** kívánja tovább használni az 1 Javasoljuk, hogy 2018. július 31-ig telepítsen Linux-alapú HDInsight-fürtökre. 
- Ha igény szerinti HDInsight társított szolgáltatást használ a (z) Data Factory 1. verziójú DotNet egyéni tevékenység végrehajtásához, akkor frissítse a DotNet egyéni tevékenység JSON-definícióját, hogy ehelyett egy Azure Batch társított szolgáltatást használjon. További információ: [egyéni tevékenységek használata Data Factory folyamatokban](./data-factory-use-custom-activities.md). 

> [!Note]
> Ha meglévő, saját fürthöz tartozó HDInsight társított eszközt használ a Data Factory 1. vagy egy saját és igény szerinti HDInsight társított szolgáltatást Azure Data Factoryban, nincs szükség beavatkozásra. Ezekben az esetekben a HDInsight-fürtök legújabb támogatási szabályzata már érvényben van. 
>
> 


## <a name="on-demand-compute-environment"></a>Igény szerinti számítási környezet
Az igény szerinti konfigurációban Data Factory teljes mértékben felügyeli a számítási környezetet. A Data Factory automatikusan létrehozza a számítási környezetet, mielőtt a rendszer elküld egy feladatot az adatok feldolgozásához. Ha a feladatot elvégezte, Data Factory eltávolítja a számítási környezetet. 

Egy igény szerinti számítási környezethez társított szolgáltatást is létrehozhat. A társított szolgáltatással konfigurálhatja a számítási környezetet, és szabályozhatja a feladatok végrehajtásához, a fürtözéshez és a rendszerindítási műveletekhez tartozó részletes beállításokat.

> [!NOTE]
> Jelenleg az igény szerinti konfiguráció csak HDInsight-fürtök esetén támogatott.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight igény szerinti társított szolgáltatás
A Data Factory képes automatikusan létrehozni egy Windows-alapú vagy Linux-alapú igény szerinti HDInsight-fürtöt az adatfeldolgozáshoz. A fürt ugyanabban a régióban jön létre, mint a fürthöz társított Storage-fiók. A fürt létrehozásához használja a JSON **linkedServiceName** tulajdonságot.

Vegye figyelembe az igény szerinti HDInsight társított szolgáltatással kapcsolatos következő *főbb* pontokat:

* Az igény szerinti HDInsight-fürt nem jelenik meg az Azure-előfizetésében. A Data Factory szolgáltatás felügyeli az igény szerinti HDInsight-fürtöt az Ön nevében.
* Az igény szerinti HDInsight-fürtön futó feladatok naplóit a rendszer a HDInsight-fürthöz társított Storage-fiókba másolja. A naplók eléréséhez nyissa meg a Azure Portal a **tevékenység futtatása részletek** ablaktáblán. További információ: [folyamatok figyelése és kezelése](data-factory-monitor-manage-pipelines.md).
* Csak arra az időre számítunk fel díjat, ameddig a HDInsight-fürt működik.

> [!IMPORTANT]
> Az igény szerinti HDInsight-fürt kiépítése általában *20 percet* vesz igénybe.
>
> 

### <a name="example"></a>Példa
A következő JSON egy Linux-alapú igény szerinti HDInsight társított szolgáltatást definiál. Az Data Factory automatikusan létrehoz egy *Linux-alapú HDInsight-* fürtöt, amikor adatszeletet dolgoz fel. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> A HDInsight-fürt létrehoz egy *alapértelmezett tárolót* a JSON **linkedServiceName** tulajdonságban megadott Azure Blob Storage-tárolóban. A HDInsight nem törli ezt a tárolót a fürt törlése után. Egy igény szerinti HDInsight társított szolgáltatás esetén a rendszer minden alkalommal létrehoz egy HDInsight-fürtöt, amikor egy szeletet fel kell dolgozni, kivéve, ha van meglévő élő fürt (**TimeToLive**). A rendszer törli a fürtöt a feldolgozás befejezésekor. 
>
> Ahogy egyre több szeletet dolgoz fel, a blob Storage-ban sok tároló jelenik meg. Ha nincs szüksége a tárolók hibaelhárítási feladatokhoz, érdemes törölni a tárolókat a tárolási díjak csökkentése érdekében. A tárolók neve a következő mintát követi: `adf<your Data Factory name>-<linked service name>-<date and time>`. A blob Storage tárolóban lévő tárolók törléséhez használhatja a [Microsoft Azure Storage Explorer](https://storageexplorer.com/) eszközt.
>
> 

### <a name="properties"></a>Tulajdonságok
| Tulajdonság                     | Leírás                              | Kötelező |
| ---------------------------- | ---------------------------------------- | -------- |
| típus                         | Állítsa a Type (típus) tulajdonságot **HDInsightOnDemand** értékre. | Yes      |
| clusterSize                  | A fürtben található feldolgozó és adatcsomópontok száma. A HDInsight-fürt két fő csomóponttal jön létre a tulajdonsághoz megadott munkavégző csomópontok száma mellett. A csomópontok mérete Standard_D3, amely 4 magot tartalmaz. A 4 munkavégző csomópont-fürt 24 magot (4 \* 4 = 16 magot a feldolgozó csomópontok számára, valamint 2 \* 4 = 8 mag a főcsomópontokhoz) tart. A Standard_D3 szintjével kapcsolatos további információkért lásd: [Linux-alapú Hadoop-fürtök létrehozása a HDInsight-ben](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Yes      |
| timeToLive                   | Az igény szerinti HDInsight-fürt számára engedélyezett üresjárati idő. Meghatározza, hogy az igény szerinti HDInsight-fürt mennyi ideig maradjon életben, ha a tevékenység futtatása befejeződött, ha nincsenek más aktív feladatok a fürtben.<br /><br />Ha például egy tevékenység futása 6 percet vesz igénybe, és a **TimeToLive** 5 percre van állítva, a fürt a tevékenység futtatásának 6 perce után 5 perccel továbbra is életben marad. Ha egy másik tevékenység futtatása a 6 perces ablakban történik, akkor azt ugyanazon fürt dolgozza fel.<br /><br />Egy igény szerinti HDInsight-fürt létrehozása költséges művelet (eltarthat egy darabig). Ezt a beállítást igény szerint használhatja az adat-előállító teljesítményének növeléséhez egy igény szerinti HDInsight-fürt újrafelhasználásával.<br /><br />Ha a **TimeToLive** értéket **0-ra** állítja, akkor a rendszer a tevékenység futtatása után azonnal törli a fürtöt. Ha azonban magas értéket állít be, előfordulhat, hogy a fürt tétlen maradhat, ami szükségtelenül magas költségekkel jár. Fontos, hogy az igényeinek megfelelően állítsa be a megfelelő értéket.<br /><br />Ha a **TimeToLive** értéke megfelelően van beállítva, több folyamat is megoszthatja az igény szerinti HDInsight-fürt példányát. | Yes      |
| version                      | A HDInsight-fürt verziója. Az engedélyezett HDInsight-verziók esetében lásd: [támogatott HDInsight-verziók](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Ha ez az érték nincs megadva, a rendszer a [legújabb HDI alapértelmezett verziót](../../hdinsight/hdinsight-component-versioning.md) használja. | No       |
| linkedServiceName            | Az Azure Storage társított szolgáltatása, amelyet az igény szerinti fürt az adattároláshoz és-feldolgozáshoz használ. A HDInsight-fürt ugyanabban a régióban jön létre, mint a Storage-fiók.<p>Jelenleg nem hozhat létre Azure Data Lake Store tárolóként használó igény szerinti HDInsight-fürtöt. Ha az HDInsight feldolgozásból származó eredményeket szeretné tárolni Data Lake Storeban, a másolási tevékenység használatával másolja át az adatait a blob Storage-ból a Data Lake Storeba. </p> | Yes      |
| additionalLinkedServiceNames | További Storage-fiókok megadása a HDInsight társított szolgáltatáshoz. Data Factory regisztrálja a Storage-fiókokat az Ön nevében. Ezeknek a tárolási fiókoknak ugyanabban a régióban kell lenniük, mint a HDInsight-fürtnek. A HDInsight-fürt ugyanabban a régióban jön létre, mint a **linkedServiceName** tulajdonság által megadott Storage-fiók. | No       |
| osType                       | Az operációs rendszer típusa. Az engedélyezett értékek a **Linux** és a **Windows**. Ha ez az érték nincs megadva, a rendszer a **Linux** -t használja.  <br /><br />A Linux-alapú HDInsight-fürtök használatát javasoljuk. A Windows HDInsight kivonulási dátuma 2018. július 31-ig tart. | No       |
| hcatalogLinkedServiceName    | Annak az Azure SQL társított szolgáltatásnak a neve, amely a HCatalog-adatbázisra mutat. Az igény szerinti HDInsight-fürt létrehozásához az SQL Database-t használja metaadattár. | No       |

#### <a name="example-linkedservicenames-json"></a>Példa: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Speciális tulajdonságok
Az igény szerinti HDInsight-fürt részletes konfigurálásához a következő tulajdonságokat adhatja meg:

| Tulajdonság               | Leírás                              | Kötelező |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Meghatározza a létrehozandó HDInsight-fürt alapvető konfigurációs paramétereit (core-site.xml). | No       |
| hBaseConfiguration     | Megadja a HDInsight-fürt HBase-konfigurációs paramétereit (hbase-site.xml). | No       |
| hdfsConfiguration      | Megadja a HDInsight-fürt HDFS-konfigurációs paramétereit (hdfs-site.xml). | No       |
| hiveConfiguration      | Meghatározza a HDInsight-fürt struktúrájának konfigurációs paramétereit (hive-site.xml). | No       |
| mapReduceConfiguration | Megadja a HDInsight-fürt MapReduce-konfigurációs paramétereit (mapred-site.xml). | No       |
| oozieConfiguration     | Megadja a HDInsight-fürt Oozie-konfigurációs paramétereit (oozie-site.xml). | No       |
| stormConfiguration     | Megadja a HDInsight-fürt Storm konfigurációs paramétereit (storm-site.xml). | No       |
| yarnConfiguration      | Megadja a HDInsight-fürthöz tartozó szál konfigurációs paramétereit (yarn-site.xml). | No       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Példa: igény szerinti HDInsight-fürt speciális tulajdonságokkal

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Csomópontok méretei
A fej-, az adatés a ZooKeeper-csomópontok méretének megadásához használja a következő tulajdonságokat: 

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Beállítja a fő csomópont méretét. Az alapértelmezett érték **Standard_D3**. Részletekért lásd a [csomópontok méretének megadása](#specify-node-sizes)című témakört. | No       |
| dataNodeSize      | Az adatcsomópont méretének beállítása. Az alapértelmezett érték **Standard_D3**. | No       |
| zookeeperNodeSize | Beállítja a ZooKeeper-csomópont méretét. Az alapértelmezett érték **Standard_D3**. | No       |

#### <a name="specify-node-sizes"></a>Csomópontok méretének meghatározása
Az előző szakaszban ismertetett tulajdonságokkal megadható karakterlánc-értékekhez lásd: [virtuális gépek méretei](../../virtual-machines/sizes.md). Az értékeknek meg kell felelniük a [virtuális gépek méreteiben](../../virtual-machines/sizes.md)hivatkozott parancsmagoknak és API-knak. A nagyméretű (alapértelmezett) adatcsomópont-méret 7 GB memóriával rendelkezik. Előfordulhat, hogy ez nem elegendő a forgatókönyvhöz. 

Ha D4 méretű főcsomópontokat és munkavégző csomópontokat szeretne létrehozni, akkor a **headNodeSize** és a **dataNodeSize** tulajdonságok értékének megadásához a **Standard_D4** értéket kell megadnia: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Ha helytelen értéket állít be ezekhez a tulajdonságokhoz, a következő üzenet jelenhet meg:

  Nem sikerült létrehozni a fürtöt. Kivétel: Nem sikerült befejezni a fürt létrehozásának műveletét. A művelet 400-as kóddal meghiúsult. A fürt állapota: „hiba”. Üzenet: "PreClusterCreationValidationFailure". 
  
Ha ezt az üzenetet látja, győződjön meg róla, hogy a parancsmagot és az API-neveket használja a táblából a [virtuális gépek méreteiben](../../virtual-machines/sizes.md).  

> [!NOTE]
> A Data Factory jelenleg nem támogatja a Data Lake Store elsődleges tárolóként használó HDInsight-fürtöket. Használja az Azure Storage-t elsődleges tárolóként a HDInsight-fürtökhöz. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Saját számítási környezet
A Data Factory-ben társított szolgáltatásként regisztrálhat egy meglévő számítási környezetet. A számítási környezet kezelése. A Data Factory szolgáltatás a számítási környezetet használja a tevékenységek végrehajtásához.

Az ilyen típusú konfiguráció a következő számítási környezetekben támogatott:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klasszikus)
* Azure Data Lake Analytics
* Azure SQL Database, Azure szinapszis Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight társított szolgáltatás
Létrehozhat egy HDInsight társított szolgáltatást, hogy regisztrálja a saját HDInsight-fürtöt Data Factory használatával.

### <a name="example"></a>Példa

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| típus              | Állítsa a Type (típus) tulajdonságot **HDInsight** értékre. | Yes      |
| clusterUri        | A HDInsight-fürt URI-ja.        | Yes      |
| username          | A meglévő HDInsight-fürthöz való kapcsolódáshoz használandó felhasználói fiók neve. | Yes      |
| jelszó          | A felhasználói fiók jelszava.   | Yes      |
| linkedServiceName | A HDInsight-fürt által használt BLOB-tárolóra hivatkozó Storage társított szolgáltatás neve. <p>Jelenleg nem adhat meg Data Lake Store társított szolgáltatást ehhez a tulajdonsághoz. Ha a HDInsight-fürt hozzáfér a Data Lake Storehoz, akkor a struktúra vagy a Pig parancsfájlok Data Lake Store adatait is elérheti. </p> | Yes      |

## <a name="azure-batch-linked-service"></a>Társított szolgáltatás Azure Batch
Létrehozhat egy batch-társított szolgáltatást, amely a virtuális gépek (VM-EK) batch-készletét egy adatgyárba regisztrálja. Microsoft .NET egyéni tevékenységeket a Batch vagy a HDInsight használatával is futtathatja.

Ha új a Batch szolgáltatás használatához:

* Ismerkedjen meg [Azure batch alapjaival](../../azure-sql/database/sql-database-paas-overview.md).
* A [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)) parancsmag megismerése. Ezzel a parancsmaggal hozhat létre batch-fiókot. A Batch-fiókot a [Azure Portal](../../batch/batch-account-create-portal.md)használatával is létrehozhatja. A parancsmag használatával kapcsolatos részletes információkért lásd: a [Batch-fiók kezelése a PowerShell használatával](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account).
* A [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)) parancsmag megismerése. Ezzel a parancsmaggal hozhat létre batch-készletet.

### <a name="example"></a>Példa

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

A **accountName** tulajdonsághoz fűzze a következőt: **. \<region name\>** a Batch-fiók nevével. Például:

```json
"accountName": "mybatchaccount.eastus"
```

Egy másik lehetőség, hogy megadja a **batchUri** -végpontot. Például:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság          | Leírás                              | Kötelező |
| ----------------- | ---------------------------------------- | -------- |
| típus              | Állítsa a Type (típus) tulajdonságot **AzureBatch** értékre. | Yes      |
| accountName       | A Batch-fiók neve.         | Yes      |
| accessKey         | A Batch-fiók elérési kulcsa.  | Yes      |
| poolName          | A virtuális gépek készletének neve.    | Yes      |
| linkedServiceName | Az ehhez a kötegelt társított szolgáltatáshoz társított Storage társított szolgáltatás neve. Ez a társított szolgáltatás a tevékenység futtatásához szükséges átmeneti fájlokra, valamint a tevékenység-végrehajtási naplók tárolására szolgál. | Yes      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Azure Machine Learning Studio (klasszikus) társított szolgáltatás
Létrehozhat egy Azure Machine Learning Studio (klasszikus) társított szolgáltatást a Studio (klasszikus) batch pontozási végpontjának egy adatgyárba való regisztrálásához.

### <a name="example"></a>Példa

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Tulajdonságok
| Tulajdonság   | Leírás                              | Kötelező |
| ---------- | ---------------------------------------- | -------- |
| Típus       | Állítsa a Type (típus) tulajdonságot **AzureML** értékre. | Yes      |
| mlEndpoint | A Batch-pontozási URL-cím.                   | Yes      |
| apiKey     | A közzétett munkaterület-modell API-ját.     | Yes      |

## <a name="azure-data-lake-analytics-linked-service"></a>Társított szolgáltatás Azure Data Lake Analytics
Létrehozhat egy Data Lake Analytics társított szolgáltatást egy Data Lake Analytics számítási szolgáltatás Azure-beli adatgyárhoz való összekapcsolásához. A folyamat Data Lake Analytics U-SQL tevékenysége erre a társított szolgáltatásra hivatkozik. 

A következő táblázat a JSON-definícióban használt általános tulajdonságokat ismerteti:

| Tulajdonság                 | Leírás                              | Kötelező                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| típus                 | Állítsa a Type (típus) tulajdonságot **AzureDataLakeAnalytics** értékre. | Yes                                      |
| accountName          | A Data Lake Analytics fiók neve.  | Yes                                      |
| dataLakeAnalyticsUri | A Data Lake Analytics URI-ja.           | No                                       |
| subscriptionId       | Az Azure-előfizetés azonosítója.                    | No<br /><br />(Ha nincs megadva, a rendszer az adatgyár-előfizetést használja.) |
| resourceGroupName    | Az Azure-erőforráscsoport neve.                | No<br /><br /> (Ha nincs megadva, a rendszer az adatgyári erőforráscsoportot használja.) |

### <a name="authentication-options"></a>Hitelesítési lehetőségek
A Data Lake Analytics társított szolgáltatás esetében a hitelesítés egy egyszerű szolgáltatásnév vagy egy felhasználói hitelesítő adatok használatával választható.

#### <a name="service-principal-authentication-recommended"></a>Egyszerű szolgáltatásnév hitelesítése (ajánlott)
Az egyszerű szolgáltatás hitelesítésének használatához regisztráljon egy alkalmazás-entitást Azure Active Directory (Azure AD). Ezután engedélyezze az Azure AD-hozzáférést Data Lake Storehoz. A részletes lépésekért lásd: [szolgáltatások közötti hitelesítés](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:
* Alkalmazásazonosító
* Alkalmazás kulcsa 
* Bérlőazonosító

Az egyszerű szolgáltatás hitelesítését a következő tulajdonságok megadásával használhatja:

| Tulajdonság                | Leírás                              | Kötelező |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Az alkalmazás ügyfél-azonosítója.     | Yes      |
| servicePrincipalKey | Az alkalmazás kulcsa.           | Yes      |
| Bérlő              | A bérlői információ (tartománynév vagy bérlő azonosítója), ahol az alkalmazás található. Ezen információk beszerzéséhez vigye az egérmutatót a Azure Portal jobb felső sarkában. | Yes      |

**Példa: egyszerű szolgáltatásnév hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Felhasználói hitelesítő adatok hitelesítése
A Data Lake Analytics felhasználói hitelesítő adatainak hitelesítéséhez a következő tulajdonságokat kell megadnia:

| Tulajdonság          | Leírás                              | Kötelező |
| :---------------- | :--------------------------------------- | :------- |
| engedélyezés | Data Factory szerkesztőben kattintson az **Engedélyezés** gombra. Adja meg azt a hitelesítő adatot, amely az automatikusan generált engedélyezési URL-címet rendeli hozzá ehhez a tulajdonsághoz. | Yes      |
| sessionId     | A OAuth munkamenet-azonosítója a OAuth-engedélyezési munkamenetből. Az egyes munkamenet-AZONOSÍTÓk egyediek, és csak egyszer használhatók fel. A beállítás automatikusan létrejön, amikor Data Factory szerkesztőt használ. | Yes      |

**Példa: felhasználói hitelesítő adatok hitelesítése**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Jogkivonat lejárata
Az **Engedélyezés** gomb kiválasztásával létrehozott engedélyezési kód egy megadott intervallum lejárta után lejár. 

A hitelesítési jogkivonat lejárta után a következő hibaüzenet jelenhet meg: 

  Hitelesítő adatok műveleti hibája: invalid_grant-AADSTS70002: hiba történt a hitelesítő adatok érvényesítése során. AADSTS70008: a megadott hozzáférési engedély lejárt vagy visszavont. Nyomkövetési azonosító: d18629e8-af88-43c5-88e3-d8419eb1fca1 korrelációs azonosítója: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 timestamp: 2015-12-15 21:09:31Z

A következő táblázat a felhasználói fiók típusa szerint jár le: 

| Felhasználó típusa                                | Lejárat után lejár                            |
| :--------------------------------------- | :--------------------------------------- |
| Az Azure AD által *nem* kezelt felhasználói fiókok (Hotmail, élő stb.) | 12 óra.                                 |
| Az Azure AD *által kezelt felhasználói* fiókok | 14 nappal az utolsó szelet futtatása után. <br /><br />90 nap, ha egy OAuth-alapú társított szolgáltatáson alapuló szelet 14 naponta legalább egyszer fut. |

A hiba elkerüléséhez vagy megoldásához engedélyezze az **Engedélyezés** gombot, ha a jogkivonat lejár. Ezután telepítse újra a társított szolgáltatást. A **munkamenet** -azonosító és az **engedélyezési** tulajdonságok értékeit programozott módon is létrehozhatja a következő kód használatával:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Az ebben a kódban használt Data Factory osztályok részleteiért lásd:
* [AzureDataLakeStoreLinkedService osztály](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [AzureDataLakeAnalyticsLinkedService osztály](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [AuthorizationSessionGetResponse osztály](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Adja meg a **WindowsFormsWebAuthenticationDialog** osztály Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll hivatkozását. 

## <a name="azure-sql-linked-service"></a>Azure SQL Database társított szolgáltatás
Létrehozhat egy SQL társított szolgáltatást, és használhatja a [tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) egy tárolt eljárás meghívásához egy Data Factory folyamatból. További információ: [Azure SQL Connector](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-synapse-analytics-linked-service"></a>Azure szinapszis Analytics társított szolgáltatás
Létrehozhat egy Azure szinapszis Analytics társított szolgáltatást, és a [tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) felhasználhatja a tárolt eljárás meghívását egy Data Factory folyamatból. További információ: [Azure szinapszis Analytics-összekötő](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Társított szolgáltatás SQL Server
Létrehozhat egy SQL Server társított szolgáltatást, és használhatja a [tárolt eljárási tevékenységgel](data-factory-stored-proc-activity.md) egy tárolt eljárás meghívásához egy Data Factory folyamatból. További információ: SQL Server- [összekötő](data-factory-sqlserver-connector.md#linked-service-properties).