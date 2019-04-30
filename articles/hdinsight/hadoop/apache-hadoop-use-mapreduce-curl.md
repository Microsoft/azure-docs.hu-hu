---
title: MapReduce használata és az Apache hadooppal a HDInsight - Azure Curl
description: Ismerje meg, hogyan lehet távolról futtatni a Curl használatával HDInsight az Apache Hadoop MapReduce-feladatok.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: e4968310459097fc6a00f7c453846fe61726c3d5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129263"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Az Apache Hadoop MapReduce feladatok futtatása HDInsight REST használatával

Megtudhatja, hogyan használható az Apache Hive-WebHCat REST API egy HDInsight-fürtön az Apache hadoop MapReduce-feladatok futtatásához. A curl használatával mutatja be, hogyan használhatja a HDInsight MapReduce-feladatok futtatása a nyers HTTP-kérések használatával szolgál.

> [!NOTE]  
> Ha már ismeri a Linux-alapú Hadoop-kiszolgálókat használ, de még nem ismeri a HDInsight, tekintse meg a [mit kell tudni a HDInsight Linux-alapú Apache Hadoop](../hdinsight-hadoop-linux-information.md) dokumentumot.


## <a id="prereq"></a>Előfeltételek

* A Hadoop HDInsight-fürtön
* Windows PowerShell vagy [Curl](https://curl.haxx.se/) és [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>A MapReduce-feladat futtatása

> [!NOTE]  
> Használatakor a Curl vagy más REST kommunikációt használ a Webhcattel, hitelesítenie kell a kéréseket a HDInsight-fürt rendszergazdai felhasználónév és jelszó megadásával. A fürt nevét az URI-t, a kérések a kiszolgálóhoz küldéséhez használt részeként kell használnia.
>
> A REST API használatával védett [alapszintű hitelesítés](https://en.wikipedia.org/wiki/Basic_access_authentication). Kérések mindig készítsen, győződjön meg arról, hogy a hitelesítő adatait biztonságos módon küldje el a kiszolgáló a HTTPS-en keresztül.

1. Állítsa be a fürtre való bejelentkezéshez, a jelen dokumentum a parancsfájlok által használt, használja a következő parancsok egyikét:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. A fürt nevének megadásához használja a következő parancsok egyikét:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Hasonló a következő JSON-választ kap:

        {"status":"ok","version":"v1"}

    Ezen parancs paraméterei a következők:

   * **-u**: Azt jelzi, hogy a felhasználónév és a kérés hitelesítésére használt jelszó
   * **-G**: Azt jelzi, hogy ez a művelet egy GET kéréssel

   Az URI-t, az elején **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, összes kérelem esetében azonos.

4. MapReduce feladatok elküldéséhez használja a következő parancsot:

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Az URI-t (vagy a mapreduce/jar) végén közli, hogy a kérelem első eleme egy MapReduce-feladatot egy jar-fájlt egy osztály WebHCat. Ezen parancs paraméterei a következők:

   * **-d**: `-G` nem használják, ezért a kérelem az alapértelmezett a POST metódust. `-d` Megadja a küldött adatok értékeket a kérelemmel.
     * **user.name**: A parancsot futtató felhasználó
     * **jar**: A jar-fájlt, amely tartalmazza a futtatni kívánt osztály helye
     * **class**: A MapReduce logikát tartalmazó osztály
     * **arg**: Az átadott argumentum is, a MapReduce-feladatot. Ebben az esetben, a bemeneti szövegfájl és a használt a kimeneti könyvtár

   Ezzel a paranccsal ellenőrizheti az állapotot, a feladat használható Feladatazonosítót kell visszaadnia:

       job_1415651640909_0026

5. A feladat állapotának ellenőrzéséhez használja a következő parancsot:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Ha a feladat befejeződött, a visszaküldött állapot `SUCCEEDED`.

   > [!NOTE]  
   > Ezt a kérelmet a Curl egy JSON-dokumentumok, a feladattal kapcsolatos információkat ad vissza. Csak az állapot értékét a vizualizációhoz Jq szolgál.

6. Ha a feladat állapota megváltozott, hogy `SUCCEEDED`, a feladat eredményeinek kérheti le az Azure Blob storage-ból. A `statusdir` lekérdezése átadott paraméter tartalmazza a kimeneti fájl helye. Ebben a példában a hely a `/example/curl`. Ez a cím tárolja a fürt alapértelmezett tárolója, a feladat kimenetei `/example/curl`.

A listában, és ezeket a fájlokat le használatával a [Azure CLI-vel](https://docs.microsoft.com/cli/azure/install-azure-cli). Blobok az Azure parancssori felületen való használatáról további információkért lásd: a [az Azure CLI használatával az Azure Storage](../../storage/common/storage-azure-cli.md#create-and-manage-blobs) dokumentumot.

## <a id="nextsteps"></a>Következő lépések

HDInsight MapReduce-feladatok általános tájékoztatást:

* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)
* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)

A REST-felület, amely használatban van ebben a cikkben kapcsolatos további információkért lásd: a [WebHCat referencia](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
