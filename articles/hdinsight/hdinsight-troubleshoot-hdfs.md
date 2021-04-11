---
title: Az Azure HDInsight HDFS hibáinak megoldása
description: Választ kaphat a HDFS és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: c725e7b4e7af33181cb616d2bce2ae19521a5e7d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871877"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Az Apache Hadoop HDFS hibaelhárítása az Azure HDInsighttal

A Hadoop elosztott fájlrendszer (HDFS) használata során a legfontosabb problémák és megoldások megismerése. A parancsok teljes listáját a [HDFS parancsok útmutatójában](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) és a [fájlrendszer rendszerhéj-útmutatójában](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)találja.

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Hogyan hozzáférni a helyi HDFS egy fürtön belülről?

### <a name="issue"></a>Probléma

A helyi HDFS a parancssorból és az alkalmazás kódjából a HDInsight-fürtön belüli Azure Blob Storage vagy Azure Data Lake Storage használatával érheti el.

### <a name="resolution-steps"></a>A megoldás lépései

1. A parancssorban a szó szerint használja `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` a következő parancsot:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. A forráskódból használja az URI- `hdfs://mycluster/` t a szó szoros értelmében, ahogy az alábbi példában is látható:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Futtassa a lefordított. jar-fájlt (például egy nevű fájlt `java-unit-tests-1.0.jar` ) a HDInsight-fürtön a következő paranccsal:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Tárolási kivétel a blobon való íráshoz

### <a name="issue"></a>Probléma

Ha a (z) vagy a (z) `hadoop` `hdfs dfs` parancsot a HBase-fürtön lévő ~ 12 GB vagy nagyobb méretű fájlok írására használja, a következő hibaüzenet jelenhet meg:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Ok

Az Azure Storage-ba való íráskor a HDInsight-fürtök HBase alapértelmezett értéke 256 KB. Habár HBase API-k vagy REST API-k esetében is működik, hibaüzenetet eredményez a `hadoop` vagy a `hdfs dfs` parancssori segédeszközök használatakor.

### <a name="resolution"></a>Feloldás

`fs.azure.write.request.size`Nagyobb blokk méretének megadásához használja a következőt:. Ezt a módosítást használhatja használati alapon a (z `-D` ) paraméter használatával. A következő parancs egy példa erre a paraméterre a `hadoop` parancs használatával:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Az `fs.azure.write.request.size` Apache Ambari használatával globálisan is növelheti az értékét. Az alábbi lépéseket követve módosíthatja a Ambari webes felhasználói felületének értékét:

1. A böngészőben nyissa meg a Ambari webes felhasználói felületét a fürthöz. Az URL-cím a (z `https://CLUSTERNAME.azurehdinsight.net` ), ahol a a `CLUSTERNAME` fürt neve. Ha a rendszer kéri, adja meg a fürthöz tartozó rendszergazdai nevet és jelszót.
2. A képernyő bal oldalán válassza a **HDFS** lehetőséget, majd válassza a **konfigurációk** fület.
3. A **szűrő...** mezőbe írja be a értéket `fs.azure.write.request.size` .
4. Módosítsa a 262144 (256 KB) értéket az új értékre. Például 4194304 (4 MB).

    :::image type="content" source="./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png" alt-text="Az érték Ambari webes felhasználói felületén keresztüli módosításának képe" border="false":::

A Ambari használatával kapcsolatos további információkért lásd: [HDInsight-fürtök kezelése az Apache Ambari webes felhasználói felületén](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

A [`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du) parancs megjeleníti az adott könyvtárban található fájlok és könyvtárak méretét, vagy egy fájl hosszát, ha ez csak egy fájl.

A `-s` beállítás megjeleníti a fájlokban megjelenített hosszok összesített összegzését.  
A `-h` beállítás formázza a fájlméretet.

Példa:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>RM

Az [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) parancs az argumentumként megadott fájlokat törli.

Példa:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]