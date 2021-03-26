---
title: 'Gyors útmutató: RStudio Server & ML szolgáltatások az R-Azure HDInsight'
description: A gyors útmutatóban egy R-szkriptet hajt végre az Azure HDInsight egy ML Services-fürtön az RStudio-kiszolgáló használatával.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 8abd485bff885868d7865cf273cb161974106013
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104862833"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Gyors útmutató: R-szkript végrehajtása egy ML Services-fürtön az Azure HDInsight az RStudio-kiszolgáló használatával

Az Azure HDInsight ML-szolgáltatásai lehetővé teszik az R-parancsfájlok használatát Apache Spark és Apache Hadoop MapReduce elosztott számítások futtatásához. A ML-szolgáltatások azt vezérlik, hogy a rendszer hogyan hajtja végre a hívásokat a számítási környezet beállításával. A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való kapcsolódáshoz és az R-parancsfájlok futtatásához. A peremhálózati csomópontok esetében lehetősége van a RevoScaleR párhuzamosan elosztott funkcióinak futtatására a peremhálózati csomópont-kiszolgáló magjai között. Ezeket a fürtök csomópontjain is futtathatja a RevoScaleR Hadoop-leképezésével, és csökkentheti vagy Apache Spark a számítási környezeteket.

Ebből a rövid útmutatóból megtudhatja, hogyan futtathat egy R-szkriptet a RStudio-kiszolgálóval, amely bemutatja a Spark elosztott R-számításokhoz való használatát. A számítási kontextust úgy fogja meghatározni, hogy helyileg hajtja végre a számításokat egy peremhálózati csomóponton, majd újra elosztja a HDInsight-fürt csomópontjai között.

## <a name="prerequisite"></a>Előfeltétel

Egy ML Services-fürt a HDInsight-on. Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md) , majd a **ml-szolgáltatások** kiválasztása a **fürt típusához**.

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

A RStudio-kiszolgáló a fürt peremhálózati csomópontján fut. Nyissa meg a következő URL-címet, ahol a a `CLUSTERNAME` létrehozott ml Services-fürt neve:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Amikor először jelentkezik be, kétszer kell hitelesítenie. Az első hitelesítési kéréshez adja meg a fürt rendszergazdai felhasználónevét és jelszavát, az alapértelmezett érték: `admin` . A második hitelesítési kérésnél adja meg az SSH-felhasználónevet és a jelszót, az alapértelmezett érték: `sshuser` . A későbbi bejelentkezések csak az SSH hitelesítő adatokat igénylik.

A csatlakozást követően a képernyőnek a következőhöz hasonlóan kell kinéznie:

:::image type="content" source="./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png" alt-text="R Studio webkonzol – áttekintések" border="true":::

## <a name="use-a-compute-context"></a>Számítási környezet használata

1. A RStudio-kiszolgálóról a következő kód használatával tölthet be például az alapértelmezett tárolóba a HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Ez a lépés körülbelül 8 percet is igénybe vehet.

1. Hozzon létre adatinformációkat, és határozzon meg két adatforrást. Adja meg a következő kódot a RStudio:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Futtasson logisztikai regressziót az adatokon a **helyi** számítási környezet használatával. Adja meg a következő kódot a RStudio:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    A számításoknak körülbelül 7 percen belül kell elvégezniük. Az alábbi kódrészlethez hasonló sorokkal végződő kimenetnek kell megjelennie:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Futtassa ugyanazt a logisztikai regressziót a **Spark** kontextus használatával. A Spark környezet elosztja a feldolgozást a HDInsight-fürt összes munkavégző csomópontja között. Adja meg a következő kódot a RStudio:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    A számításoknak körülbelül 5 percen belül kell elvégezniük.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A gyors üzembe helyezés befejezése után érdemes lehet törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan futtathat egy R-szkriptet a Spark által elosztott R-számításokhoz használt RStudio-kiszolgálóval.  A következő cikkből megtudhatja, hogy milyen lehetőségek érhetők el annak megadásához, hogy a végrehajtás hogyan legyen párhuzamos a peremhálózati csomópont vagy a HDInsight-fürt magjai között.

> [!div class="nextstepaction"]
>[Számítási környezeti beállítások a HDInsight ML-szolgáltatásaihoz](./r-server-compute-contexts.md)

> [!NOTE]
> Ez az oldal a RStudio szoftver funkcióit ismerteti. Microsoft Azure HDInsight nem kapcsolódik a RStudio, Inc. szolgáltatáshoz.
