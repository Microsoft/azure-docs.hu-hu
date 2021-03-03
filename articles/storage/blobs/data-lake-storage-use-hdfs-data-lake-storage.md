---
title: A HDFS CLI használata Azure Data Lake Storage Gen2
description: A Azure Data Lake Storage Gen2hoz használja a Hadoop elosztott fájlrendszer (HDFS) CLI-t. Hozzon létre egy tárolót, szerezze be a fájlok vagy könyvtárak listáját, és így tovább.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/06/2018
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: artek
ms.openlocfilehash: b73e79c2f7bbd37b640d3e83d98be68d85ed3b5e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735369"
---
# <a name="using-the-hdfs-cli-with-data-lake-storage-gen2"></a>A HDFS CLI használata Data Lake Storage Gen2

A Storage-fiókban lévő adatait úgy érheti el és kezelheti, ha a [Hadoop elosztott fájlrendszer (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)egy parancssori felülettel rendelkezik. Ez a cikk néhány példát mutat be, amely segítséget nyújt az első lépésekhez.

A HDInsight hozzáférést biztosít a számítási csomópontokhoz helyileg csatlakoztatott elosztott tárolóhoz. Ezt a tárolót a HDFS és a Hadoop által támogatott egyéb fájlrendszerek használatával közvetlenül kommunikáló rendszerhéj segítségével érheti el.

A HDFS CLI-vel kapcsolatos további információkért tekintse meg a [hivatalos dokumentációt](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) és a [HDFS engedélyeit ismertető útmutatót](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) .

>[!NOTE]
>Ha a HDInsight helyett a Azure Databricks használja, és parancssori felületen szeretné használni az adatait, az Databricks CLI használatával használhatja a Databricks fájlrendszert. Lásd: [DATABRICKS CLI](/azure/databricks/dev-tools/cli/).

## <a name="use-the-hdfs-cli-with-an-hdinsight-hadoop-cluster-on-linux"></a>A HDFS CLI használata HDInsight Hadoop-fürttel Linux rendszeren

Először is hozzon létre [távoli hozzáférést a szolgáltatásokhoz](../../hdinsight/hdinsight-hadoop-linux-information.md#remote-access-to-services). Ha az [SSH](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md) -t választja, a minta PowerShell-kód a következőképpen néz ki:

```powershell
#Connect to the cluster via SSH.
ssh sshuser@clustername-ssh.azurehdinsight.net
#Execute basic HDFS commands. Display the hierarchy.
hdfs dfs -ls /
#Create a sample directory.
hdfs dfs -mkdir /samplefolder
```
A (z) Azure Portalban található HDInsight-fürt panelének "SSH + fürt bejelentkezési" szakaszában a következő: a kapcsolatok karakterlánca. A fürt létrehozásakor a rendszer SSH hitelesítő adatokat adott meg.

>[!IMPORTANT]
>A HDInsight-fürt számlázása egy fürt létrehozása után kezdődik, és a fürt törlésekor leáll. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban. A fürtök törlésével kapcsolatos további információkért tekintse [meg a című cikket a következő témakörben](../../hdinsight/hdinsight-delete-cluster.md):. A Data Lake Storage Gen2 engedélyezve lévő Storage-fiókban tárolt adatkészletek azonban még a HDInsight-fürt törlése után is megmaradnak.

## <a name="create-a-container"></a>Tároló létrehozása

`hdfs dfs -D "fs.azure.createRemoteFileSystemDuringInitialization=true" -ls abfs://<container-name>@<storage-account-name>.dfs.core.windows.net/`

* Cserélje le a `<container-name>` helyőrzőt arra a névre, amelyet meg szeretne adni a tárolónak.

* Cserélje le a `<storage-account-name>` helyőrzőt a Storage-fiók nevére.

## <a name="get-a-list-of-files-or-directories"></a>Fájlok vagy könyvtárak listájának beolvasása

`hdfs dfs -ls <path>`

Cserélje le a `<path>` helyőrzőt a tároló vagy a tároló mappa URI azonosítójának helyére.

Például: `hdfs dfs -ls abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name`

## <a name="create-a-directory"></a>Könyvtár létrehozása

`hdfs dfs -mkdir [-p] <path>`

Cserélje le a `<path>` helyőrzőt a gyökér tároló nevére vagy egy, a tárolón belüli mappára.

Például: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

## <a name="delete-a-file-or-directory"></a>Fájl vagy könyvtár törlése

`hdfs dfs -rm <path>`

Cserélje le a `<path>` helyőrzőt a törölni kívánt fájl vagy mappa URI-fájljára.

Például: `hdfs dfs -rmdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/my-directory-name/my-file-name`

## <a name="display-the-access-control-lists-acls-of-files-and-directories"></a>Fájlok és könyvtárak Access Control listáinak (ACL-jei) megjelenítése

`hdfs dfs -getfacl [-R] <path>`

Példa:

`hdfs dfs -getfacl -R /dir`

Lásd: [getfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl)

## <a name="set-acls-of-files-and-directories"></a>Fájlok és könyvtárak ACL-ek beállítása

`hdfs dfs -setfacl [-R] [-b|-k -m|-x <acl_spec> <path>]|[--set <acl_spec> <path>]`

Példa:

`hdfs dfs -setfacl -m user:hadoop:rw- /file`

Lásd: [setfacl](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl)

## <a name="change-the-owner-of-files"></a>A fájlok tulajdonosának módosítása

`hdfs dfs -chown [-R] <new_owner>:<users_group> <URI>`

Lásd: [chown](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown)

## <a name="change-group-association-of-files"></a>Fájlok csoport társításának módosítása

`hdfs dfs -chgrp [-R] <group> <URI>`

Lásd: [chgrp](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp)

## <a name="change-the-permissions-of-files"></a>A fájlok engedélyeinek módosítása

`hdfs dfs -chmod [-R] <mode> <URI>`

Lásd: [chmod](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod)

A parancsok teljes listáját megtekintheti a [Apache Hadoop 2.4.1 fájlrendszer rendszerhéj-útmutatójának](https://hadoop.apache.org/docs/r2.4.1/hadoop-project-dist/hadoop-common/FileSystemShell.html) webhelyén.

## <a name="next-steps"></a>Következő lépések

* [Azure Data Lake Storage Gen2 képes fiók használata Azure Databricks](./data-lake-storage-use-databricks-spark.md)

* [Tudnivalók a fájlok és könyvtárak hozzáférés-vezérlési listájáról](./data-lake-storage-access-control.md)