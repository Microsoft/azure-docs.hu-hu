---
title: A MapReduce és a PowerShell használata a Apache Hadoop-Azure HDInsight
description: Megtudhatja, hogyan használhatja a PowerShellt a MapReduce-feladatok távoli futtatásához a HDInsight Apache Hadoop használatával.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 16c6c5e317591b70c3a1300453093fc715e213fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939678"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>MapReduce-feladatok futtatása a HDInsight Apache Hadoop a PowerShell használatával

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Ez a dokumentum egy példát mutat be a Azure PowerShell használatára egy MapReduce-feladatoknak a HDInsight-fürt Hadoop való futtatásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Apache Hadoop-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md).

* A PowerShell az [modul](/powershell/azure/) telepítve van.

## <a name="run-a-mapreduce-job"></a>MapReduce-feladatok futtatása

A Azure PowerShell olyan *parancsmagokat* biztosít, amelyek lehetővé teszik a MapReduce-feladatok távoli futtatását a HDInsight. Belsőleg a PowerShell REST-hívásokat tesz lehetővé a HDInsight-fürtön futó [webhcaten](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (korábbi nevén Templeton).

A következő parancsmagok használhatók a MapReduce-feladatok távoli HDInsight-fürtben való futtatásakor.

|Parancsmag | Leírás |
|---|---|
|Connect-AzAccount|Azure PowerShell hitelesíti az Azure-előfizetését.|
|New-AzHDInsightMapReduceJobDefinition|Új *feladatdefiníció* létrehozása a megadott MapReduce-adatok használatával.|
|Start-AzHDInsightJob|A HDInsight elküldi a feladatot, és elindítja a feladatot. A rendszer egy *feladatütemezés* visszaadását adja vissza.|
|Wait-AzHDInsightJob|A feladattípust használja a feladatok állapotának vizsgálatához. Megvárja, amíg a feladatok befejeződik, vagy túllépi a várakozási időt.|
|Get-AzHDInsightJobOutput|A feladatok kimenetének beolvasására szolgál.|

A következő lépések bemutatják, hogyan használhatja ezeket a parancsmagokat feladatok futtatására a HDInsight-fürtben.

1. Szerkesztő használatával mentse a következő kódot **mapreducejob.ps1ként**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Nyisson meg egy új **Azure PowerShell** parancssort. Módosítsa a könyvtárakat a **mapreducejob.ps1** fájl helyére, majd futtassa a következő parancsot a szkript futtatásához:

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    A parancsfájl futtatásakor a rendszer kéri a HDInsight-fürt és a fürt bejelentkezési nevét. Előfordulhat, hogy az Azure-előfizetésében is meg kell adnia a hitelesítést.

3. Amikor a feladatok befejeződik, a következő szöveghez hasonló kimenetet kap:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Ez a kimenet azt jelzi, hogy a feladatot sikerült befejezni.

    > [!NOTE]  
    > Ha a **ExitCode** értéke nem 0, lásd: [Hibaelhárítás](#troubleshooting).

    Ez a példa a letöltött fájlokat egy **output.txt** fájlba tárolja abban a könyvtárban, amelyből a parancsfájlt futtatja.

### <a name="view-output"></a>Kimenet megtekintése

Ha meg szeretné tekinteni a feladatok által előállított szavakat és számadatokat, nyissa meg a **output.txt** fájlt egy szövegszerkesztőben.

> [!NOTE]  
> A MapReduce-feladatok kimeneti fájljai nem változtathatók meg. Így ha újra futtatja ezt a mintát, módosítania kell a kimeneti fájl nevét.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a rendszer nem ad vissza információt a feladatok befejeződésekor, tekintse meg a feladattal kapcsolatos hibákat. Ha meg szeretné tekinteni a feladattal kapcsolatos hibákat, adja hozzá a következő parancsot a **mapreducejob.ps1** fájl végéhez. Ezután mentse a fájlt, és futtassa újra a parancsfájlt.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Ez a parancsmag a STDERR írt adatokat adja vissza.

## <a name="next-steps"></a>Következő lépések

Amint az látható, Azure PowerShell egyszerű módszert kínál a MapReduce-feladatok HDInsight-fürtön való futtatására, a feladat állapotának figyelésére, valamint a kimenet lekérésére. További információ a Hadoop a HDInsight-ben való használatával kapcsolatos egyéb módszerekről:

* [A MapReduce használata a HDInsight-Hadoop](hdinsight-use-mapreduce.md)
* [Apache Hive használata a HDInsight Apache Hadoop használatával](hdinsight-use-hive.md)