---
title: Adatok lekérdezése HDFS-kompatibilis Azure Storage-ból – Azure HDInsight
description: Ismerje meg, hogyan kérdezheti le az Azure Storage és a Azure Data Lake Storage adatait az elemzés eredményeinek tárolásához.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: cedc0ff1b3c2aa64f32445eabc800748a753981d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945432"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Az Azure Storage és az Azure HDInsight-fürtök együttes használata

Az [Azure Blob Storage](../storage/common/storage-introduction.md)-ban, [Azure Data Lake Storage Gen1ban](../data-lake-store/data-lake-store-overview.md)vagy [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)tárolhatja az adattárolást. Vagy ezen beállítások kombinációja. Ezek a tárolási beállítások lehetővé teszik a számításhoz használt HDInsight-fürtök biztonságos törlését felhasználói adatvesztés nélkül.

Apache Hadoop támogatja az alapértelmezett fájlrendszer fogalmát. Az alapértelmezett fájlrendszer egy alapértelmezett sémát és szolgáltatót is jelent. A relatív elérési utak feloldásához is használható. A HDInsight-fürt létrehozási folyamata során megadhat egy BLOB-tárolót az Azure Storage szolgáltatásban alapértelmezett fájlrendszerként. A HDInsight 3,6 esetében pedig az Azure Blob Storage-t vagy a Azure Data Lake Storage Gen1/Azure Data Lake Storage Gen2 is választhatja alapértelmezett fájlrendszerként, néhány kivétellel. A Data Lake Storage Gen1 az alapértelmezett és a csatolt tárolóként való használatának támogatásához tekintse meg a [HDInsight-fürt rendelkezésre állását](./hdinsight-hadoop-use-data-lake-storage-gen1.md#availability-for-hdinsight-clusters)ismertető témakört.

Ebből a cikkből megtudhatja, hogyan használható az Azure Storage a HDInsight-fürtökkel. 
* Ha szeretné megtudni, hogyan működik a Data Lake Storage Gen1 HDInsight-fürtökkel, tekintse meg [a Azure Data Lake Storage Gen1 használata Azure HDInsight-fürtökkel](./hdinsight-hadoop-use-data-lake-storage-gen1.md)című témakört.
* Ha szeretné megtudni, hogyan működik a Data Lake Storage Gen2 HDInsight-fürtökkel, tekintse meg [a Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](./hdinsight-hadoop-use-data-lake-storage-gen2.md)című témakört.
* További információ a HDInsight-fürtök létrehozásáról: [Apache Hadoop-fürtök létrehozása a HDInsight-ben](./hdinsight-hadoop-provision-linux-clusters.md).

> [!IMPORTANT]  
> A Storage-fiók típusa **BlobStorage** csak másodlagos tárolóként használhatók a HDInsight-fürtökhöz.

| Storage-fiók típusa | Támogatott szolgáltatások | Támogatott teljesítményszint |Nem támogatott teljesítményszint| Támogatott hozzáférési szintek |
|----------------------|--------------------|-----------------------------|---|------------------------|
| StorageV2 (általános célú v2)  | Blob     | Standard                    |Prémium| Gyors elérésű, ritka elérésű Archívum\*   |
| Tároló (általános célú v1)   | Blob     | Standard                    |Prémium| N/A                    |
| BlobStorage                    | Blob     | Standard                    |Prémium| Gyors elérésű, ritka elérésű Archívum\*   |

Nem javasoljuk, hogy az üzleti adattároláshoz használja az alapértelmezett BLOB-tárolót. Az alapértelmezett blobtárolót ajánlatos törölni minden egyes használat után. Az alapértelmezett tároló alkalmazás-és rendszernaplókat tartalmaz. A tároló törlése előtt gondoskodjon a naplók begyűjtéséről.

Egy blob-tároló megosztása, mivel a több fürt alapértelmezett fájlrendszere nem támogatott.

> [!NOTE]  
> Az archív hozzáférési szint egy olyan offline szint, amely több órás lekérési késéssel rendelkezik, és nem ajánlott a HDInsight-mel való használatra. További információ: az [archív hozzáférési szint](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

## <a name="access-files-from-within-cluster"></a>Fájlok elérése a fürtön belülről

Több módon is hozzáférhet a Data Lake Storage lévő fájlokhoz egy HDInsight-fürtről. Az URI-séma titkosítatlan hozzáférést biztosít (a *wasb:* előtaggal) és a TLS titkosított hozzáférését ( *wasbs*). Ajánlott a *wasbs* előtagot használnia, amikor lehetséges, még akkor is, amikor az Azure-ban ugyanabban a régióban lévő adatokat éri el.

* **A teljes név használatával**. Ezzel a módszerrel az elérni kívánt fájl teljes elérési útját megadja.

    ```
    wasb://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    wasbs://<containername>@<accountname>.blob.core.windows.net/<file.path>/
    ```

* **A rövidített elérésiút-formátum használatával**. Ezzel a módszerrel az elérési utat a fürt gyökerére cseréli le a következővel:

    ```
    wasb:///<file.path>/
    wasbs:///<file.path>/
    ```

* **A relatív elérési út használatával**. Ezzel a módszerrel csak az elérni kívánt fájl relatív elérési útját adja meg.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Adatelérési példák

A példák a fürt fő csomópontjának [SSH-kapcsolatain](./hdinsight-hadoop-linux-use-ssh-unix.md) alapulnak. A példák mindhárom URI-sémát használják. Cserélje `CONTAINERNAME` le `STORAGEACCOUNT` a és a értéket a megfelelő értékekre.

#### <a name="a-few-hdfs-commands"></a>Néhány hdfs parancs

1. Hozzon létre egy fájlt a helyi tárolóban.

    ```bash
    touch testFile.txt
    ```

1. Könyvtárak létrehozása a fürt tárterületén.

    ```bash
    hdfs dfs -mkdir wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -mkdir wasbs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Adatok másolása a helyi tárolóból a fürt tárolójába.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  wasbs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. A fürt tárterületén lévő könyvtár tartalmának listázása.

    ```bash
    hdfs dfs -ls wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/sampledata1/
    hdfs dfs -ls wasbs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

> [!NOTE]  
> Amikor a HDInsight eszközön kívüli blobokkal dolgozik, a legtöbb segédprogram nem ismeri fel a WASB formátumot, és ehelyett alapvető elérési út formátumot vár, például a következőt: `example/jars/hadoop-mapreduce-examples.jar`.

#### <a name="creating-a-hive-table"></a>Struktúra-tábla létrehozása

A szemléltető célokra három fájl helye látható. A tényleges végrehajtáshoz csak az egyik bejegyzést használja `LOCATION` .

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'wasbs://CONTAINERNAME@STORAGEACCOUNT.blob.core.windows.net/example/data/';
LOCATION 'wasbs:///example/data/';
LOCATION '/example/data/';
```

## <a name="access-files-from-outside-cluster"></a>Fájlok elérése a fürtön kívülről

A Microsoft az alábbi eszközöket biztosítja az Azure Storage-hoz való együttműködéshez:

| Eszköz | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="identify-storage-path-from-ambari"></a>Tároló elérési útjának azonosítása a Ambari

* A konfigurált alapértelmezett tároló teljes elérési útjának azonosításához keresse meg a következőt:

    **HDFS**  >  Adja meg a **konfigurációt** , majd írja be `fs.defaultFS` a szűrőt a beviteli mezőbe.

* Annak vizsgálatához, hogy a wasb-tároló másodlagos tárolóként van-e konfigurálva, keresse meg a következőt:

    **HDFS**  >  Adja meg a **konfigurációt** , majd írja be `blob.core.windows.net` a szűrőt a beviteli mezőbe.

Az elérési út Ambari REST API használatával történő beszerzéséhez tekintse meg [az alapértelmezett tároló lekérése](./hdinsight-hadoop-manage-ambari-rest-api.md#get-the-default-storage)című témakört.

## <a name="blob-containers"></a>Blobtárolók

A blobok használatához először hozzon létre egy [Azure Storage-fiókot](../storage/common/storage-account-create.md). Ennek a lépésnek a részeként meg kell adnia egy Azure-régiót, ahol a Storage-fiók létrejön. A fürtnek és a tárfióknak ugyanabban a régióban kell lennie. A Hive-metaadattár SQL Server adatbázisnak és az Apache Oozie metaadattár SQL Server adatbázisnak ugyanabban a régióban kell lennie.

Akárhol él, mindegyik létrehozott blob az Azure Storage-fiókban lévő tárolóhoz tartozik. Ez a tároló lehet a HDInsight-en kívül létrehozott meglévő blob is. Vagy lehet egy tároló, amely egy HDInsight-fürthöz lett létrehozva.

Az alapértelmezett Blob-tároló a fürtre jellemző információkat, például a feladatelőzményeket és a naplókat tárolja. Ne osszon meg alapértelmezett Blob tárolókat több HDInsight-fürttel. Ez a művelet a feladatok előzményeit is megsértheti. Azt javasoljuk, hogy minden fürthöz használjon egy másik tárolót. A megosztott adatokat az alapértelmezett Storage-fiók helyett az összes releváns fürthöz megadott társított Storage-fiókban helyezze el. A kapcsolt tárfiókok konfigurálásáról további információért lásd: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md). De újból felhasználhatja az alapértelmezett tárolókat az eredeti HDInsight fürt törlése után. A HBase-fürtök esetében ténylegesen megtarthatja a HBase tábla sémáját és az adataikat úgy, hogy létrehoz egy új HBase-fürtöt a törölt HBase-fürt által használt alapértelmezett blob-tároló használatával.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="use-additional-storage-accounts"></a>További tárfiókok használata

HDInsight-fürt létrehozásakor meg kell adnia azt az Azure Storage-fiókot, amelyet a fürthöz társítani kívánja. Emellett további Storage-fiókokat is hozzáadhat ugyanahhoz az Azure-előfizetéshez vagy különböző Azure-előfizetésekhez a létrehozási folyamat során vagy a fürt létrehozása után. Útmutatás további tárfiókok hozzáadásához: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

> [!WARNING]  
> A rendszer nem támogatja további tárfiókok használatát a HDInsight-fürtön kívül eső helyeken.

## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtanulta, hogyan használhat HDFS-kompatibilis Azure-tárolót a HDInsighttal. Ez a tároló lehetővé teszi az adaptív, hosszú távú, archivált adatgyűjtési megoldások kiépítését, és a HDInsight használatával feloldja a tárolt strukturált és strukturálatlan adatokon belüli információkat.

További információkért lásd:

* [Rövid útmutató: Apache Hadoop-fürt létrehozása](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Oktatóanyag: HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Adatok feltöltése a HDInsightba](hdinsight-upload-data.md)
* [Oktatóanyag: adatok kinyerése, átalakítása és betöltése az Azure HDInsight interaktív lekérdezés használatával](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Az Azure Storage közös hozzáférésű jogosultságkódok használata az adathozzáférés korlátozásához a HDInsightban](hdinsight-storage-sharedaccesssignature-permissions.md)