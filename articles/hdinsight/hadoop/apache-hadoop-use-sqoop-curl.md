---
title: A curl használata az Azure HDInsight Apache Sqoop használatával történő adatexportáláshoz
description: Útmutató az Apache Sqoop-feladatok távoli elküldéséhez az Azure HDInsight-ben a curl használatával.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 4de42bf30824fd71228aa27cc478a54ec3741da9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928369"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Apache Sqoop-feladatok futtatása a HDInsight-ben a curl segítségével

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Ismerje meg, hogyan futtathat Apache Sqoop-feladatokat a curl használatával egy Apache Hadoop-fürtön a HDInsight-ben. Ez a cikk bemutatja, hogyan exportálhatók az adatok az Azure Storage-ból, és hogyan importálhatók egy SQL Server adatbázisba a curl használatával. Ez a cikk az [Apache Sqoop és a Hadoop HDInsight-ben való használatának](./hdinsight-use-sqoop.md)folytatása.

A curl segítségével bemutatjuk, hogyan használhatja a HDInsight a Sqoop-feladatok eredményeinek futtatására, figyelésére és beolvasására szolgáló nyers HTTP-kérelmek használatával. Ez a HDInsight-fürt által biztosított Webhcaten REST API (korábbi nevén Templeton) használatával működik.

## <a name="prerequisites"></a>Előfeltételek

* A [tesztkörnyezet üzembe](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) állításának befejezése az [Apache Sqoop és a Hadoop használatával a HDInsight-ben](./hdinsight-use-sqoop.md).

* A Azure SQL Database lekérdezésére szolgáló ügyfél. Használjon [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) vagy [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md)-ot.

* [Curl](https://curl.haxx.se/). A curl egy eszköz, amellyel a vagy a HDInsight-fürtbe lehet továbbítani az adatok átvitelét.

* [jQ](https://stedolan.github.io/jq/). A jQ segédprogram a REST-kérelmekből visszaadott JSON-adatok feldolgozására szolgál.

* Az Sqoop ismerete. További információ: [Sqoop felhasználói útmutató](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Apache Sqoop-feladatok elküldése a curl használatával

A curl használatával adatok exportálhatók az Azure Storage-ból SQL Server az Apache Sqoop-feladatokkal.

> [!NOTE]  
> Amikor a Curl vagy más REST kommunikációt használ a WebHCattel, hitelesítenie kell a kéréseket a HDInsight fürt rendszergazdája felhasználónevének és jelszavának megadásával. A fürtnevet a kérések kiszolgálóhoz küldéséhez használt egységes erőforrás-azonosító (URI) részeként is használnia kell.

Az ebben a szakaszban szereplő parancsokra cserélje le a `USERNAME` felhasználót a fürtre történő hitelesítésre, majd cserélje le a parancsot a `PASSWORD` felhasználói fiók jelszavára. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.

A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication) gondoskodik. Mindig biztonságos HTTP-n (HTTPS-en) keresztül kell kéréseket végeznie, hogy a hitelesítő adatait biztonságos módon küldje a kiszolgálóhoz.

1. Az egyszerű használat érdekében állítsa be az alábbi változókat. Ez a példa egy Windows-környezeten alapul, és szükség szerint módosítja a környezetét.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    A következőhöz hasonló választ kell kapnia:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Sqoop-feladatok elküldéséhez használja a következőt:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Ezen parancs paraméterei a következők:

   * **-d** – mivel `-G` nincs használatban, a kérelem alapértelmezett értéke a post metódus. `-d` Megadja a kéréssel elküldhető adatértékeket.

       * **User.name** – a parancsot futtató felhasználó.

       * **parancs** – a végrehajtandó Sqoop-utasítás.

       * **statusdir** – az a könyvtár, amelyre a feladatokhoz tartozó állapot kerül.

     Ez a parancs egy olyan AZONOSÍTÓJÚ feladatot ad vissza, amely a feladatok állapotának vizsgálatára használható.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. A feladatok állapotának megtekintéséhez használja a következő parancsot. Cserélje le az `JOBID` értéket az előző lépésben visszaadott értékre. Ha például a visszatérési érték volt `{"id":"job_1415651640909_0026"}` , akkor a következő `JOBID` lesz: `job_1415651640909_0026` . Szükség szerint módosítsa a helyet `jq` .

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Ha a feladatok befejeződtek, az állapot **sikeres** lesz.

   > [!NOTE]  
   > Ez a curl-kérelem egy JavaScript Object Notation (JSON) dokumentumot ad vissza, amely a feladattal kapcsolatos információkkal szolgál. a jQ csak az állapot értékének beolvasására szolgál.

1. Ha a művelet állapota **sikeresen** módosult, az Azure Blob Storage-ból kérheti le a feladatok eredményeit. A `statusdir` lekérdezéssel átadott paraméter tartalmazza a kimeneti fájl helyét; ebben az esetben: `wasb:///example/data/sqoop/curl` . Ez a címe a `example/data/sqoop/curl` HDInsight-fürt által használt alapértelmezett tároló könyvtárában tárolja a feladatok kimenetét.

    A stderr és az stdout Blobok eléréséhez használhatja a Azure Portal.

1. Az adatok exportálásának ellenőrzéséhez használja az SQL-ügyfél következő lekérdezéseit az exportált adatok megtekintéséhez:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Korlátozások

* Tömeges exportálás – a Linux-alapú HDInsight a Sqoop-összekötő, amellyel az adatexportálás Microsoft SQL Server vagy Azure SQL Database jelenleg nem támogatja a tömeges beszúrásokat.
* Kötegelt feldolgozás – a Linux-alapú HDInsight, amikor a `-batch` kapcsolót a lapkák végrehajtásakor használja, a Sqoop több beszúrást hajt végre a beszúrási műveletek kötegelt feldolgozása helyett.

## <a name="summary"></a>Összefoglalás

Ahogy az a dokumentumban is látható, használhat egy nyers HTTP-kérést a HDInsight-fürtön futó Sqoop-feladatok eredményeinek futtatásához, figyeléséhez és megtekintéséhez.

A jelen cikkben használt REST-felülettel kapcsolatos további információkért tekintse meg az <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Apache Sqoop REST API útmutatót</a>.

## <a name="next-steps"></a>Következő lépések

[Apache Sqoop használata Apache Hadoop HDInsight](hdinsight-use-sqoop.md)

A curlot érintő egyéb HDInsight-cikkek esetén:

* [Apache Hadoop-fürtök létrehozása az Azure REST API használatával](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Apache Hive-lekérdezések futtatása a HDInsight Apache Hadoop a REST használatával](apache-hadoop-use-hive-curl.md)
* [MapReduce-feladatok futtatása a HDInsight Apache Hadoop a REST használatával](apache-hadoop-use-mapreduce-curl.md)
