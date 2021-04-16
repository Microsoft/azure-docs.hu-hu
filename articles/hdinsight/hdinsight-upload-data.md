---
title: Adatok feltöltése Apache Hadoop-feladatokhoz a HDInsightban
description: Megtudhatja, hogyan tölthet fel és férhet hozzá az Apache Hadoop-feladatok adataihoz a HDInsightban. Használja a klasszikus Azure cli Azure Storage Explorer, Azure PowerShell, a Hadoop parancssort vagy a Sqoopot.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdiseo17may2017,seoapr2020, devx-track-azurecli
ms.date: 04/27/2020
ms.openlocfilehash: d58d194e8dbf011eec949602e4f8cd2e084a0d98
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482109"
---
# <a name="upload-data-for-apache-hadoop-jobs-in-hdinsight"></a>Adatok feltöltése Apache Hadoop-feladatokhoz a HDInsightban

A HDInsight egy Hadoop elosztott fájlrendszert (HDFS) biztosít az Azure Storage-ban, és Azure Data Lake Storage. Ez a tároló magában foglalja a Gen1-et és a Gen2-t. Az Azure Storage és Data Lake Storage Gen1 Gen2 HDFS-bővítményekként lett kialakítva. Lehetővé teszik, hogy a Hadoop-környezetben az összetevők teljes készlete közvetlenül az által kezelett adatokon működve működtessenek. Az Azure Storage, a Data Lake Storage Gen1 és a Gen2 különböző fájlrendszerek. A rendszerek az adatok és számítások tárolására vannak optimalizálva. További információ az Azure Storage használatának előnyeiről: [Az Azure Storage használata a HDInsightban.](hdinsight-hadoop-use-blob-storage.md) Lásd még: Use Data Lake Storage Gen1 with HDInsight (Az Data Lake Storage Gen1 használata [a HDInsightban)](hdinsight-hadoop-use-data-lake-storage-gen1.md)és Use Data Lake Storage Gen2 with HDInsight (Az Data Lake Storage Gen2 használata [a HDInsightban).](hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, vegye figyelembe a következő követelményeket:

* Egy Azure-beli HDInsight-fürt. Útmutatásért lásd: [Első lépések a Azure HDInsight.](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* A következő cikkek ismerete:
    * [Az Azure Storage használata a HDInsightban](hdinsight-hadoop-use-blob-storage.md)
    * [Az Data Lake Storage Gen1 használata a HDInsightban](hdinsight-hadoop-use-data-lake-storage-gen1.md)
    * [Az Data Lake Storage Gen2 használata a HDInsightban](hdinsight-hadoop-use-data-lake-storage-gen2.md)  

## <a name="upload-data-to-azure-storage"></a>Adatok feltöltése az Azure Storage-ba

### <a name="utilities"></a>Segédprogramok

A Microsoft az alábbi segédprogramokat biztosítja az Azure Storage-hoz:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |ââ" |ââ" |ââ" |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |ââ" |ââ" |ââ" |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |ââ" |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |ââ" | |ââ" |
| [Hadoop-parancs](#hadoop-command-line) |ââ" |ââ" |ââ" |

> [!NOTE]  
> A Hadoop-parancs csak a HDInsight-fürtön érhető el. A parancs csak a helyi fájlrendszerből engedélyezi az adatok betöltését az Azure Storage-ba.  

### <a name="hadoop-command-line"></a>Hadoop parancssor

A Hadoop parancssor csak akkor hasznos az adatok Azure Storage-blobban való tárolásához, ha az adatok már jelen vannak a fürt főcsomópontján.

A Hadoop-parancs csak akkor használható, ha SSH vagy [PuTTY](hdinsight-hadoop-linux-use-ssh-unix.md)használatával csatlakozik az főcsomóponthoz.

A csatlakozás után a következő szintaxissal tölthet fel egy fájlt a tárolóba.

```bash
hadoop fs -copyFromLocal <localFilePath> <storageFilePath>
```

Például: `hadoop fs -copyFromLocal data.txt /example/data/data.txt`

Mivel a HDInsight alapértelmezett fájlrendszere az Azure Storage-ban található, az /example/data/data.txt valójában az Azure Storage-ban található. A fájlra a következő ként is hivatkozhat:

`wasbs:///example/data/data.txt`

vagy

`wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt`

A fájlokkal is működik más Hadoop-parancsok listáját lásd: [https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)

> [!WARNING]  
> Apache HBase-fürtökön az adatok írásakor használt alapértelmezett blokkméret 256 KB. Bár ez jól működik HBase API-k vagy REST API-k használata esetén, a vagy a parancs használata a `hadoop` ~12 GB-nál nagyobb adatok írására hibát `hdfs dfs` ad vissza. További információ: Storage [exception for write on blob (Storage-kivétel blobon való íráshoz).](hdinsight-troubleshoot-hdfs.md#storage-exception-for-write-on-blob)

### <a name="graphical-clients"></a>Grafikus ügyfelek

Több alkalmazás is rendelkezésre áll, amelyek grafikus felületet biztosítanak az Azure Storage-hoz. Az alábbi táblázat néhány ilyen alkalmazást sorol fel:

| Ügyfél | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Microsoft Visual Studio Tools for HDInsight](hadoop/apache-hadoop-visual-studio-tools-get-started.md#explore-linked-resources) |ââ" |ââ" |ââ" |
| [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md) |ââ" |ââ" |ââ" |
| [`Cerulea`](https://www.cerebrata.com/products/cerulean/features/azure-storage) | | |ââ" |
| [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) | | |ââ" |
| [CloudBerry Explorer for Microsoft Azure](https://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx) | | |ââ" |
| [Cyberduck](https://cyberduck.io/) | |ââ" |ââ" |

## <a name="mount-azure-storage-as-local-drive"></a>Az Azure Storage csatlakoztatása helyi meghajtóként

Lásd: [Az Azure Storage csatlakoztatása helyi meghajtóként.](/archive/blogs/bigdatasupport/mount-azure-blob-storage-as-local-drive)

## <a name="upload-using-services"></a>Feltöltés szolgáltatásokkal

### <a name="azure-data-factory"></a>Azure Data Factory

Az Azure Data Factory szolgáltatás egy teljes körűen felügyelt szolgáltatás az adatok( tárolási, feldolgozási és mozgási szolgáltatások) zökkenőmentes, adaptálható és megbízható adat-előállítási folyamatokba való feldolgozásához.

|Tárolási típus|Dokumentáció|
|----|----|
|Azure Blob Storage|[Adatok másolása az Azure Blob Storage-be vagy onnan máshová az Azure Data Factoryvel](../data-factory/connector-azure-blob-storage.md)|
|1. generációs Azure Data Lake Storage|[Adatok másolása a Azure Data Lake Storage Gen1 a Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)|
|2. generációs Azure Data Lake Storage |[Adatok betöltése Azure Data Lake Storage Gen2 Azure Data Factory](../data-factory/load-azure-data-lake-storage-gen2.md)|

### <a name="apache-sqoop"></a>Apache Sqoop

A Sqoop egy olyan eszköz, amely a Hadoop és a relációs adatbázisok közötti adatátvitelre lett tervezve. Ezzel adatokat importálhat egy relációsadatbázis-kezelő rendszerből (RDBMS), például a SQL Server, a MySQL-ről vagy az Oracle-ről. Ezután a Hadoop elosztott fájlrendszerbe (HDFS). Alakítsa át az adatokat a Hadoopban a MapReduce vagy a Hive segítségével, majd exportálja vissza az adatokat egy RDBMS-be.

További információ: [A Sqoop használata a HDInsightban.](hadoop/hdinsight-use-sqoop.md)

### <a name="development-sdks"></a>Fejlesztési SDK-k

Az Azure Storage az Azure SDK-val is elérhető a következő programozási nyelvekről:

* .NET
* Java
* Node.js
* PHP
* Python
* Ruby

Az Azure SDK-k telepítésével kapcsolatos további információkért lásd: [Azure-letöltések](https://azure.microsoft.com/downloads/)

## <a name="next-steps"></a>Következő lépések

Most, hogy már tudja, hogyan lehet adatokat behozni a HDInsightba, olvassa el a következő cikkeket az elemzés elsajátítása érdekében:

* [Első lépések a Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Apache Hadoop-feladatok programozott elküldése](hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Az Apache Hive használata a HDInsightban](hadoop/hdinsight-use-hive.md)
