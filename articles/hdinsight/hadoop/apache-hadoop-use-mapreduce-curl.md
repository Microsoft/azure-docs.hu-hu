---
title: A MapReduce és a curl használata Apache Hadoop HDInsight-Azure-ban
description: Ismerje meg, hogyan futtathat MapReduce-feladatokat Apache Hadoop a HDInsight a curl használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939691"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>MapReduce-feladatok futtatása a HDInsight Apache Hadoop a REST használatával

Megtudhatja, hogyan futtathat MapReduce-feladatokat a HDInsight-fürtön lévő Apache Hadoopon a Apache Hive Webhcaten REST API használatával. A curl használatával megtudhatja, hogyan kezelheti a HDInsight a MapReduce-feladatok futtatására szolgáló nyers HTTP-kérelmek használatával.

> [!NOTE]  
> Ha már ismeri a Linux-alapú Hadoop-kiszolgálókat, de most ismerkedik a HDInsight-mel, tekintse [meg a HDInsight-dokumentum Linux-alapú Apache Hadoop megismeréséhez szükséges tudnivalókat](../hdinsight-hadoop-linux-information.md) .

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md).

Vagy:
  * Windows PowerShell vagy,
  * [Curl](https://curl.haxx.se/) a [jQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>MapReduce-feladatok futtatása

> [!NOTE]  
> Ha curl vagy bármilyen más REST-kommunikációt használ a Webhcaten-mel, a kérelmeket hitelesítenie kell a HDInsight-fürt rendszergazdai felhasználónevének és jelszavának megadásával. A fürt nevét a kérések kiszolgálónak való küldéséhez használt URI részeként kell használni.
>
> A REST API [alapvető hozzáférési hitelesítéssel](https://en.wikipedia.org/wiki/Basic_access_authentication)van védve. A kéréseket mindig HTTPS használatával kell elvégeznie, hogy a hitelesítő adatai biztonságosan legyenek elküldve a kiszolgálónak.

### <a name="curl"></a>Curl

1. Az egyszerű használat érdekében állítsa be az alábbi változókat. Ez a példa egy Windows-környezeten alapul, és szükség szerint módosítja a környezetét.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. Egy parancssorból a következő paranccsal ellenőrizze, hogy tud-e kapcsolódni a HDInsight-fürthöz:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Ezen parancs paraméterei a következők:

   * **-u**: megadja a kérelem hitelesítéséhez használt felhasználónevet és jelszót
   * **-G**: azt jelzi, hogy ez a művelet egy Get kérelem

   Az URI kezdetének `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` értéke az összes kérelem esetében azonos.

    A következő JSON-hoz hasonló válasz érkezik:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce-feladatok elküldéséhez használja a következő parancsot. Szükség szerint módosítsa a **jQ** elérési útját.

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Az URI (/MapReduce/jar) vége azt jelzi, hogy a kérelem Webhcaten elindít egy MapReduce-feladatot egy jar-fájlból. Ezen parancs paraméterei a következők:

   * **-d**: `-G` nincs használatban, ezért a kérelem alapértelmezett értéke a post metódus. `-d` Megadja a kéréssel elküldhető adatértékeket.
     * **User.name**: a parancsot futtató felhasználó
     * **jar**: a futtatni kívánt osztályt tartalmazó jar-fájl helye
     * **osztály**: a MapReduce logikát tartalmazó osztály
     * **ARG**: a MapReduce-feladatoknak átadandó argumentumok. Ebben az esetben a bemeneti szövegfájl és a kimenethez használt könyvtár

    A parancsnak egy olyan AZONOSÍTÓJÚ feladatot kell visszaadnia, amely a következő feladatok állapotának vizsgálatára használható: `job_1415651640909_0026` .

1. A feladatok állapotának megtekintéséhez használja a következő parancsot. A értékét cserélje le az `JOBID` előző lépésben visszaadott **tényleges** értékre. Szükség szerint módosítsa a **jQ** helyét.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Az egyszerű használat érdekében állítsa be az alábbi változókat. Cserélje le a `CLUSTERNAME` nevet a tényleges fürt nevére. Hajtsa végre a parancsot, és ha a rendszer kéri, adja meg a fürt bejelentkezési jelszavát.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. A következő parancs használatával ellenőrizheti, hogy tud-e csatlakozni a HDInsight-fürthöz:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    A következő JSON-hoz hasonló válasz érkezik:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. MapReduce-feladatok elküldéséhez használja a következő parancsot:

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

    Az URI (/MapReduce/jar) vége azt jelzi, hogy a kérelem Webhcaten elindít egy MapReduce-feladatot egy jar-fájlból. Ezen parancs paraméterei a következők:

    * **User.name**: a parancsot futtató felhasználó
    * **jar**: a futtatni kívánt osztályt tartalmazó jar-fájl helye
    * **osztály**: a MapReduce logikát tartalmazó osztály
    * **ARG**: a MapReduce-feladatoknak átadandó argumentumok. Ebben az esetben a bemeneti szövegfájl és a kimenethez használt könyvtár

   A parancsnak egy olyan AZONOSÍTÓJÚ feladatot kell visszaadnia, amely a következő feladatok állapotának vizsgálatára használható: `job_1415651640909_0026` .

1. A feladatok állapotának megtekintéséhez használja a következő parancsot:

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

### <a name="both-methods"></a>Mindkét módszer

1. Ha a feladatok befejeződik, a visszaadott állapot a következő: `SUCCEEDED` .

1. Ha a feladatokhoz tartozó állapot módosult `SUCCEEDED` , az Azure Blob Storage-ból kérheti le a feladatok eredményét. A `statusdir` lekérdezéssel átadott paraméter tartalmazza a kimeneti fájl helyét. Ebben a példában a hely a következő: `/example/curl` . Ez a címe tárolja a feladatok kimenetét a fürtök alapértelmezett tárolójában a következő helyen: `/example/curl` .

Ezeket a fájlokat az [Azure CLI](/cli/azure/install-azure-cli)használatával listázhatja és letöltheti. Az Azure CLI Azure Blob Storage-hoz való használatáról további információt a rövid útmutató [: Blobok létrehozása, letöltése és listázása az Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md)használatával című témakörben talál.

## <a name="next-steps"></a>Következő lépések

További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [A MapReduce használata a HDInsight Apache Hadoop használatával](hdinsight-use-mapreduce.md)
* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)

A cikkben használt REST-felülettel kapcsolatos további információkért tekintse meg a Webhcaten- [referenciát](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
