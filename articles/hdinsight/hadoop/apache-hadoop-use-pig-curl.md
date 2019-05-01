---
title: Az Apache Hadoop a Pig használata a HDInsight - Azure REST
description: Útmutató a REST használata a Pig Latin-feladatok futtatása az Azure HDInsight egy Apache Hadoop-fürtöt.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: hrasheed
ms.openlocfilehash: 690a4bf08c7bfd6ccc039fdd04a3dda26b5a9301
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717860"
---
# <a name="run-apache-pig-jobs-with-apache-hadoop-on-hdinsight-by-using-rest"></a>Az Apache Pig feladatok futtatása az Apache Hadoop HDInsight REST használatával

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Ismerje meg, hogyan futtathat Apache Pig Latin feladatokat REST-kérelmeket, így egy Azure HDInsight-fürtön. A curl használatával mutatja be, hogyan használhatja a WebHCat REST API-t használó HDInsight szolgál.

> [!NOTE]  
> Ha már ismeri a Linux-alapú Apache Hadoop-kiszolgálókat használ, de nem ismeri a HDInsight, [Linux-alapú HDInsight-tippek](../hdinsight-hadoop-linux-information.md).

## <a id="prereq"></a>Előfeltételek

* Egy Azure HDInsight (Hadoop on HDInsight)-fürt (Linux-alapú vagy Windows-alapú)

  > [!IMPORTANT]  
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [A curl](https://curl.haxx.se/)

* [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Az Apache Pig-feladatok futtatása a Curl használatával


> [!NOTE]
> A REST API védelméről [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication). Mindig kapcsolatos kéréseit: Győződjön meg arról, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgáló HTTP Secure (HTTPS) használatával.
>
> Ebben a szakaszban a parancsok használatakor cserélje le a `USERNAME` a fürtön, és cserélje le a felhasználóval `PASSWORD` a felhasználói fiókhoz tartozó jelszót. Cserélje le a `CLUSTERNAME` elemet a fürt nevére.
>


1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    A következő JSON-választ kell kapnia:

        {"status":"ok","version":"v1"}

    Ezen parancs paraméterei a következők:

   * **-u**: A felhasználónév és a kérés hitelesítésére használt jelszó
   * **-G**: Azt jelzi, hogy a kérelem a GET-kérés

     Az URL-cím elején **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, összes kérelem esetében azonos. Az elérési út **/status**, azt jelzi, hogy a kérelem visszaállíthatja az állapotot, a WebHCat (más néven templeton eszközön keresztül végzett) a kiszolgálón.

2. A fürthöz a Pig Latin feladatok elküldéséhez használja a következő kódot:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d execute="LOGS=LOAD+'/example/data/sample.log';LEVELS=foreach+LOGS+generate+REGEX_EXTRACT($0,'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)',1)+as+LOGLEVEL;FILTEREDLEVELS=FILTER+LEVELS+by+LOGLEVEL+is+not+null;GROUPEDLEVELS=GROUP+FILTEREDLEVELS+by+LOGLEVEL;FREQUENCIES=foreach+GROUPEDLEVELS+generate+group+as+LOGLEVEL,COUNT(FILTEREDLEVELS.LOGLEVEL)+as+count;RESULT=order+FREQUENCIES+by+COUNT+desc;DUMP+RESULT;" -d statusdir="/example/pigcurl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/pig
    ```

    Ezen parancs paraméterei a következők:

   * **-d**: Mivel `-G` nem használ, akkor a kérelem az alapértelmezett a POST metódust. `-d` Megadja a küldött adatok értékeket a kérelemmel.

   * **user.name**: A parancsot futtató felhasználó
   * **hajtsa végre**: A Pig Latin utasításokkal futtathat
   * **statusdir**: A könyvtár írt Ez a feladat állapota

     > [!NOTE]  
     > Figyelje meg, hogy a tárolóhelyek a Pig Latin utasításokkal helyébe a `+` . amikor a curl használatával használt karakter.

     Ez a parancs használható, például a feladat állapota Feladatazonosítót kell visszaadnia:

       {"id":"job_1415651640909_0026"}

3. A feladat állapotának ellenőrzéséhez használja a következő parancsot

     ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

     Cserélje le `JOBID` adja vissza az előző lépésben értékkel. Ha például a visszaadott érték volt `{"id":"job_1415651640909_0026"}`, majd `JOBID` van `job_1415651640909_0026`.

    Ha a feladat befejeződött, az állapot az **sikeres**.

    > [!NOTE]  
    > Ezt a kérelmet a Curl egy JavaScript Object Notation (JSON) dokumentumot a feladattal kapcsolatos információkat ad vissza, és csak az állapotérték beolvasásához használt jq.

## <a id="results"></a>Eredmények megtekintése

Ha a feladat állapota megváltozott, hogy **sikeres**, kérheti le a feladat eredményét. A `statusdir` lekérdezése átadott paraméter tartalmazza a helyet, a kimeneti fájl; ebben az esetben `/example/pigcurl`.

HDInsight használhatja Azure Storage vagy az Azure Data Lake Storage alapértelmezett adattárként. Attól függően, melyiket használja az adatok lekérése különböző módja van. További információkért tekintse meg a tárterület szakaszában a [Linux-alapú HDInsight-információk](../hdinsight-hadoop-linux-information.md#hdfs-azure-storage-and-data-lake-storage) dokumentumot.

## <a id="summary"></a>Summary (Összefoglalás)

Ahogyan ebben a dokumentumban, is használhatja a nyers HTTP-kérelmek futtatásához, figyelheti és Pig-feladatok eredményeinek megtekintéséhez a HDInsight-fürtön.

A cikk ezt használja a REST-felület kapcsolatos további információkért lásd: a [WebHCat referencia](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).

## <a id="nextsteps"></a>Következő lépések

A HDInsight Pig általános tájékoztatást:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
