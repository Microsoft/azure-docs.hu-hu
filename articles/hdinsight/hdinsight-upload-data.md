---
title: Adatok feltöltése Apache Hadoop feladatok számára a HDInsight-ben
description: Megtudhatja, hogyan tölthet fel és férhet hozzá Apache Hadoop feladatok adataihoz a HDInsight-ben. Használja a klasszikus Azure CLI-t, Azure Storage Explorer, Azure PowerShell, a Hadoop parancssort vagy a Sqoop.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020
ms.date: 04/27/2020
ms.openlocfilehash: a8d21b88298b6092c869b89d48fe4c259c2365b6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931344"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Adatok feltöltése Apache Hadoop feladatok számára a HDInsight-ben

A HDInsight Hadoop elosztott fájlrendszert (HDFS) biztosít az Azure Storage-ban, és Azure Data Lake Storage. Ez a tároló tartalmazza a Gen1 és a Gen2. Az Azure Storage és a Data Lake Storage Gen1 és a Gen2 HDFS-bővítményként lettek kialakítva. Lehetővé teszik, hogy a Hadoop-környezetben található összetevők teljes készlete közvetlenül az általa kezelt adaton működjön. Az Azure Storage, a Data Lake Storage Gen1 és a Gen2 különböző fájlrendszerek. A rendszerek az adatok és számítások tárolásához vannak optimalizálva. Az Azure Storage használatának előnyeiről az [Azure Storage és a HDInsight használata](hdinsight-hadoop-use-blob-storage.md)című témakörben olvashat bővebben. Lásd még: [Data Lake Storage Gen1 használata a HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md), és [a Data Lake Storage Gen2 használata a HDInsight használatával](hdinsight-hadoop-use-data-lake-storage-gen2.md).

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt vegye figyelembe a következő követelményeket:

* Egy Azure-beli HDInsight-fürt. Útmutatásért lásd: Ismerkedés [Az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
* A következő cikkek ismerete:
    * [Az Azure Storage és a HDInsight használata](hdinsight-hadoop-use-blob-storage.md)
    * [Data Lake Storage Gen1 használata a HDInsight](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Data Lake Storage Gen2 használata a HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Adatok feltöltése az Azure Storage-ba

### <a name="utilities"></a>Segédprogramok

A Microsoft az alábbi segédprogramokat biztosítja az Azure Storage-hoz való együttműködéshez:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |
| [Hadoop parancs](#hadoop-command-line) |✔ |✔ |✔ |

> [!NOTE]  
> A Hadoop parancs csak a HDInsight-fürtön érhető el. A parancs csak a helyi fájlrendszerből származó adatok Azure Storage szolgáltatásba való betöltését teszi lehetővé.  

### <a name="hadoop-command-line"></a>Hadoop parancssor

A Hadoop parancssor csak akkor hasznos az Azure Storage-blobba történő adattároláshoz, ha az adat már szerepel a fürt fő csomópontján.

A Hadoop parancs használatához először [SSH vagy Putty](hdinsight-hadoop-linux-use-ssh-unix.md)használatával kell csatlakoznia a átjárócsomóponthoz.

A csatlakozás után a következő szintaxissal tölthet fel egy fájlt a tárolóba.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel a HDInsight alapértelmezett fájlrendszere Az Azure Storage-ban található, az/example/Data/data.txt valójában az Azure Storage-ban érhető el. A fájlt a következő néven is megtekintheti:

`wasbs:///example/data/data.txt`

vagy

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

A fájlokkal dolgozó egyéb Hadoop-parancsok listáját itt tekintheti meg: [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Az Apache HBase-fürtökön az adatíráskor használt alapértelmezett blokk mérete 256 KB. Habár ez jól működik a HBase API-k vagy a REST API-k használata esetén, a vagy a parancs használatával a `hadoop` `hdfs dfs` ~ 12 GB-nál nagyobb adatbevitel hibát eredményez. További információ: Storage- [kivétel a blobon való íráshoz](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob).

### <a name="graphical-clients"></a>Grafikus ügyfelek

Több alkalmazás is rendelkezésre áll, amelyek grafikus felületet biztosítanak az Azure Storage-ban való használathoz. Az alábbi táblázat néhány ilyen alkalmazást felsorol:

| Ügyfél | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [HDInsight készült Microsoft Visual Studio-eszközök](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |✔ |✔ |✔ |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |✔ |✔ |✔ |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |✔ |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |✔ |
| [A Microsoft Azure törpemálna Explorer](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |✔ |
| [Cyberduck](https://cyberduck.io/) | |✔ |✔ |

## <a name="mount-azure-storage-as-local-drive"></a>Az Azure Storage csatlakoztatása helyi meghajtóként

Lásd: [Az Azure Storage csatlakoztatása helyi meghajtóként](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive).

## <a name="upload-using-services"></a>Feltöltés a szolgáltatásokkal

### <a name="azure-data-factory"></a>Azure Data Factory

A Azure Data Factory szolgáltatás egy teljes körűen felügyelt szolgáltatás, amely az adattárolást, a feldolgozást és a mozgási szolgáltatásokat egyszerűsített, alkalmazkodó és megbízható adatfeldolgozási folyamatokra fordítja.

|Tárolási típus|Dokumentáció|
|----|----|
|Azure Blob Storage|[Adatok másolása az Azure Blob Storage-be vagy onnan máshová az Azure Data Factoryvel](../data-factory/connector-azure-blob-storage.md)|
|1. generációs Azure Data Lake Storage|[Adatok másolása Azure Data Lake Storage Gen1ba vagy onnan a Azure Data Factory használatával](../data-factory/connector-azure-data-lake-store.md)|
|2. generációs Azure Data Lake Storage |[Betöltés az Azure Data Lake Storage Gen2ba Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

A Sqoop olyan eszköz, amely a Hadoop és a kapcsolati adatbázisok közötti adatátvitelre szolgál. Ezzel a beállítással adatok importálhatók a RDBMS (például SQL Server, MySQL vagy Oracle). Ezután a Hadoop Distributed File System (HDFS) rendszerbe. Alakítsa át a Hadoop-ben lévő és a MapReduce vagy a kaptárban található adatfájlokat, majd exportálja vissza az adatRDBMS.

További információ: [Sqoop használata a HDInsight](hadoop/hdinsight-use-sqoop.md)-mel.

### <a name="development-sdks"></a>Fejlesztői SDK-k

Az Azure Storage egy Azure SDK-val is elérhető a következő programozási nyelvekből:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Az Azure SDK-k telepítésével kapcsolatos további információkért lásd: [Azure-letöltések](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Következő lépések

Most, hogy megértette, hogyan szerezhet be információkat a HDInsight-ba, olvassa el a következő cikkeket az elemzés elsajátításához:

* [Ismerkedés az Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop feladatok programozott beküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)