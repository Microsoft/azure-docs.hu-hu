---
title: Apache Sqoop Apache Hadoop-Azure HDInsight
description: Ismerje meg, hogyan használható az Apache Sqoop a HDInsight és a Azure SQL Database közötti Apache Hadoop importálására és exportálására.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 2d0d38dee15817e56c2784981365ea331b6a8459
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943143"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-azure-sql-database"></a>Az Apache Sqoop használatával importálhat és exportálhat adatApache Hadoop a HDInsight és a Azure SQL Database között

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan használható az Apache Sqoop az Azure HDInsight-beli Apache Hadoop-fürtök és a Azure SQL Database vagy Microsoft SQL Server közötti importálására és exportálására. A jelen dokumentumban szereplő lépések közvetlenül a `sqoop` Hadoop-fürt átjárócsomóponthoz származó parancsot használják. Az SSH használatával csatlakozhat a fő csomóponthoz, és futtathatja a dokumentumban található parancsokat. Ez a cikk az [Apache Sqoop és a Hadoop HDInsight-ben való használatának](./hdinsight-use-sqoop.md)folytatása.

## <a name="prerequisites"></a>Előfeltételek

* A [tesztkörnyezet üzembe](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) állításának befejezése az [Apache Sqoop és a Hadoop használatával a HDInsight-ben](./hdinsight-use-sqoop.md).

* Egy SSH-ügyfél. További információért lásd: [Csatlakozás a HDInsighthoz (Apache Hadoop) SSH-val](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Az Sqoop ismerete. További információ: [Sqoop felhasználói útmutató](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="set-up"></a>Beállítás

1. A fürthöz való kapcsolódáshoz használja az [SSH-parancsot](../hdinsight-hadoop-linux-use-ssh-unix.md) . Szerkessze az alábbi parancsot az CLUSTERNAME helyére a fürt nevével, majd írja be a következő parancsot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Az egyszerű használat érdekében állítsa be a változókat. Cserélje le a, a `PASSWORD` `MYSQLSERVER` és a `MYDATABASE` értéket a megfelelő értékekre, majd írja be az alábbi parancsokat:

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop-exportálás

A kaptárból az SQL-be.

1. Annak ellenőrzéséhez, hogy a Sqoop láthatják-e az adatbázisát, írja be az alábbi parancsot az Open SSH-kapcsolatban. Ez a parancs az adatbázisok listáját adja vissza.

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. A következő parancs megadásával tekintheti meg a megadott adatbázis tábláinak listáját:

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. Ha a struktúra `hivesampletable` táblából exportálni szeretné az adatait az `mobiledata` adatbázisában lévő táblázatba, írja be az alábbi parancsot az Open SSH-kapcsolatban:

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. Az adatok exportálásának ellenőrzéséhez használja az SSH-kapcsolatban az alábbi lekérdezéseket az exportált adatok megtekintéséhez:

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop importálása

Az SQL-ből az Azure Storage-ba.

1. Adja meg az alábbi parancsot az Open SSH-kapcsolatban az `mobiledata` SQL-táblázatból származó adatok importálásához a `wasbs:///tutorials/usesqoop/importeddata` HDInsight található könyvtárba. Az adatokban található mezőket tabulátor karakter választja el egymástól, a vonalakat pedig egy új sor karaktere állítja le.

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. Azt is megteheti, hogy egy kaptár táblát is megadhat:

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. Az importálás befejezése után írja be a következő parancsot az Open SSH-kapcsolatban, hogy kilistázza az új könyvtár adatait:

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. A [Beeline](./apache-hadoop-use-hive-beeline.md) használatával ellenőrizze, hogy a tábla létre lett-e hozva a kaptárban.

    1. Kapcsolódás

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. Hajtsa végre az egyes lekérdezéseket egy időben, és tekintse át a kimenetet:

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. Kilépés a Beeline-val `!exit` .

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – a Linux-alapú HDInsight az SQL-adatexportáláshoz használt Sqoop-összekötő nem támogatja a tömeges beszúrásokat.

* Kötegelt feldolgozás – a Linux-alapú HDInsight, amikor a `-batch` kapcsolót a lapkák végrehajtásakor használja, a Sqoop több beszúrást tesz lehetővé a beszúrási műveletek kötegelt feldolgozása helyett.

## <a name="important-considerations"></a>Fontos szempontok

* A HDInsight és a SQL Servernak ugyanazon az Azure-Virtual Network kell lennie.

    Példaként tekintse meg a [HDInsight összekapcsolása](./../connect-on-premises-network.md) a helyszíni hálózati dokumentumhoz című témakört.

    További információ a HDInsight Azure Virtual Network használatával történő használatáról: a [HDInsight kiterjesztése az azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md) dokumentummal. További információ az Azure Virtual Networkről: [Virtual Network áttekintő](../../virtual-network/virtual-networks-overview.md) dokumentum.

* Az SQL Servert úgy kell konfigurálni, hogy engedélyezze az SQL-hitelesítést. További információ: a [hitelesítési mód kiválasztása](/sql/relational-databases/security/choose-an-authentication-mode) dokumentum.

* Előfordulhat, hogy a távoli kapcsolatok fogadásához konfigurálnia kell SQL Server. További információ: az SQL Server adatbázismotor-dokumentumhoz [való csatlakozás hibáinak megoldása](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .

## <a name="next-steps"></a>Következő lépések

Most már megtanulta, hogyan használhatja a Sqoop-t. További információ:

* Az [Apache Oozie és a HDInsight használata](../hdinsight-use-oozie-linux-mac.md): Sqoop művelet használata Oozie-munkafolyamatokban.
* [Repülési késleltetési idő elemzése a HDInsight használatával](../interactive-query/interactive-query-tutorial-analyze-flight-data.md): interaktív lekérdezés használatával elemezheti a repülési késleltetési adataikat, majd a Sqoop használatával exportálhatja az Azure-beli adatbázisba.
* [Adatok feltöltése a HDInsight-be](../hdinsight-upload-data.md): további módszerek az adatok HDInsight/Azure Blob Storage-ba való feltöltéséhez.