---
title: Apache Hive használata a HDInsight – Azure PowerShell használatával
description: Az Apache Hadoop Hive-lekérdezések futtatása a HDInsight a PowerShell használatával.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
origin.date: 04/23/2018
ms.date: 04/15/2019
ms.author: v-yiso
ms.openlocfilehash: 108a3e7d899eef4ca78ae7507bf4852b861e74d5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095523"
---
# <a name="run-apache-hive-queries-using-powershell"></a>PowerShell-lel, az Apache Hive-lekérdezések futtatása
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Ez a dokumentum azt szemlélteti, az Azure PowerShell használata az Azure-erőforráscsoport módban az az Apache Hadoop Hive-lekérdezések futtatása HDInsight-fürtön.

> [!NOTE]  
> Ez a dokumentum nem biztosít a hiveql a példákban használt mire részletes leírását. Az ebben a példában használt HiveQL kapcsolatos tudnivalókat lásd: [Apache Hive használata a HDInsight az Apache Hadoop](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Egy Linux-alapú Apache Hadoop a HDInsight-fürt verziója 3.4-es vagy nagyobb.

  > [!IMPORTANT]
  > A Linux az egyetlen operációs rendszer, amely a HDInsight 3.4-es vagy újabb verziói esetében használható. További tudnivalókért lásd: [A HDInsight elavulása Windows rendszeren](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Egy ügyfél az Azure PowerShell használatával.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

## <a name="run-a-hive-query"></a>Hive-lekérdezések futtatása

Az Azure PowerShell biztosít *parancsmagok* , amelyekkel távolról futtatni a HDInsight Hive-lekérdezések. Belsőleg, a parancsmagok hívásokat REST [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) a HDInsight-fürtön.

A következő parancsmagok használhatók, ha egy távoli HDInsight-fürtben Hive-lekérdezések futtatása:

* `Connect-AzAccount`: Azure PowerShell-lel hitelesíti az Azure-előfizetéshez.
* `New-AzHDInsightHiveJobDefinition`: Létrehoz egy *feladat definíciójának* a megadott hiveql használatával.
* `Start-AzHDInsightJob`: A feladatdefiníció HDInsight küld, és elindítja a feladatot. A *feladat* objektumot ad vissza.
* `Wait-AzHDInsightJob`: A feladat állapotának ellenőrzéséhez használja a feladatobjektum. Arra vár, amíg a feladat befejeződik, vagy túllépi a várakozási idő.
* `Get-AzHDInsightJobOutput`: A feladat kimenetének lekéréséhez használja.
* `Invoke-AzHDInsightHiveJob`: HiveQL utasítások futtatásához használt. Ez a parancsmag blokkolja a lekérdezés befejeződött, majd az eredményeket adja vissza.
* `Use-AzHDInsightCluster`: A használandó aktuális fürt beállítja a `Invoke-AzHDInsightHiveJob` parancsot.

A következő lépések bemutatják, hogyan használja ezeket a parancsmagokat futtathat feladatokat a HDInsight-fürt:

1. Egy szerkesztővel, mentse a következő kódot, `hivejob.ps1`.

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    #HiveQL
    #Note: set hive.execution.engine=tez; is not required for
    #      Linux-based HDInsight
    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

    #Create an HDInsight Hive job definition
    $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

    #Submit the job to the cluster
    Write-Host "Start the Hive job..." -ForegroundColor Green

    $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

    #Wait for the Hive job to complete
    Write-Host "Wait for the job to complete..." -ForegroundColor Green
    Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

    # Print the output
    Write-Host "Display the standard output..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $hiveJob.JobId `
        -HttpCredential $creds
    ```

2. Nyisson meg egy új **Azure PowerShell-lel** parancssort. Módosítsa a könyvtárat a helyét a `hivejob.ps1` fájlt, majd futtassa a parancsfájlt a következő paranccsal:

        .\hivejob.ps1

    A parancsfájl futtatásakor kéri, adja meg a fürt neve és a HTTPS-/ fürt rendszergazdai hitelesítő adataival. Előfordulhat, hogy is kérni, jelentkezzen be az Azure-előfizetéshez.

3. A feladat befejezése után, akkor hasonló információt ad vissza a következő szöveget:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Ahogy korábban említettük, `Invoke-Hive` segítségével futtassa a lekérdezést, és várja meg a választ. Az Invoke-Hive kipróbálásához használja a következő szkriptet:

    ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount -EnvironmentName AzureChinaCloud
    }

    #Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    $creds=Get-Credential -Message "Enter the login for the cluster"

    # Set the cluster to use
    Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds

    $queryString = "set hive.execution.engine=tez;" +
                "DROP TABLE log4jLogs;" +
                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/';" +
                "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
    Invoke-AzureRmHDInsightHiveJob `
        -StatusFolder "statusout" `
        -Query $queryString
    ```

    A kimenet hasonlít az alábbi szöveget:

        2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
        2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
        2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id

   > [!NOTE]
   > Hosszabb HiveQL lekérdezések esetén használhatja az Azure PowerShell **itt-karakterláncok** parancsmag vagy a HiveQL parancsfájlok. Az alábbi kódrészlet bemutatja, hogyan használhatja a `Invoke-Hive` parancsmag futtatása egy olyan HiveQL-parancsfájlt. A HiveQL-parancsfájlt kell tölthető fel a wasb: / /.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > További információ **itt-karakterláncok**, lásd: <a href="https://technet.microsoft.com/library/ee692792.aspx" target="_blank">használó Windows PowerShell itt-karakterláncok</a>.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha semmilyen adatot nem ad vissza, ha a feladat befejeződik, tekintse meg a hibanaplókat. A feladat hibaadatok megtekintéséhez adja hozzá a következő végéhez a `hivejob.ps1` fájlt, és mentse, majd futtassa újra.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag a feladat feldolgozása közben STDERR írt adatokat adja vissza.

## <a name="summary"></a>Összegzés

Látható, az Azure PowerShell Hive-lekérdezések futtatása HDInsight-fürtben, a feladat állapotának figyelése és lekérése a kimeneti egyszerű módszert biztosít.

## <a name="next-steps"></a>További lépések

Általános információk a HDInsight Hive-ról:

* [Apache Hive használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-hive.md)

Egyéb módjaival kapcsolatos további információk a HDInsight Hadoop-keretrendszerrel használhatja:

* [Az Apache Pig használata a HDInsight Apache Hadoop-keretrendszerrel](hdinsight-use-pig.md)
* [A HDInsight az Apache Hadoop MapReduce használata](hdinsight-use-mapreduce.md)
