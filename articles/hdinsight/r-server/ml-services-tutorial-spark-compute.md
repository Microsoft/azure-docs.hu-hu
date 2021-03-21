---
title: 'Oktatóanyag: az R használata Spark számítási környezetben az Azure HDInsight'
description: Oktatóanyag – az R és a Spark első lépései egy Azure HDInsight Machine Learning Services-fürtön.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: bd6015529fb521e3b157e46ee808aea43e993dee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935672"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Oktatóanyag: az R használata Spark számítási környezetben az Azure HDInsight

Ez az oktatóanyag egy Azure HDInsight Machine Learning Services-fürtön futó Apache Spark R függvények használatának lépésenkénti bevezetését ismerteti.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A mintaadatok letöltése a helyi tárterületre
> * Az adatbázis másolása az alapértelmezett tárolóba
> * Adatkészlet beállítása
> * Adatforrások létrehozása
> * Számítási környezet létrehozása a Sparkhoz
> * Lineáris modell igazítása
> * Összetett XDF-fájlok használata
> * XDF konvertálása CSV-re

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure HDInsight Machine Learning Services-fürt. A Azure Portal és a **fürt típusa** beállításnál válassza a **ml szolgáltatások** lehetőséget [a Apache Hadoop-fürtök létrehozásához](../hdinsight-hadoop-create-linux-clusters-portal.md) .

## <a name="connect-to-rstudio-server"></a>Csatlakozás az RStudio Serverhez

A RStudio-kiszolgáló a fürt peremhálózati csomópontján fut. Lépjen a következő webhelyre (ahol az URL-cím *CLUSTERNAME* a létrehozott HDInsight Machine learning Services-fürt neve):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Amikor először jelentkezik be, a hitelesítés kétszer történik meg. Az első hitelesítési kérésnél adja meg a fürt rendszergazdai felhasználónevét és jelszavát (az alapértelmezett érték a *rendszergazda*). A második hitelesítési kérésnél adja meg az SSH-felhasználónevet és a jelszót (az alapértelmezett érték a *sshuser*). A későbbi bejelentkezések csak az SSH hitelesítő adatokat igénylik.

## <a name="download-the-sample-data-to-local-storage"></a>A mintaadatok letöltése a helyi tárterületre

A *légitársaság 2012-alapú adatkészlete* 12 vesszővel elválasztott fájlból áll, amelyek az USA-ban a 2012-es évre vonatkozó összes kereskedelmi járatra vonatkozóan beérkezési és indulási adatokat tartalmaznak. Ez az adatkészlet nagyméretű, több mint 6 000 000 megfigyeléssel.

1. Inicializáljon néhány környezeti változót. A RStudio-kiszolgáló konzolon adja meg a következő kódot:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. A jobb oldali ablaktáblában válassza a **környezet** lapot. A változók az **értékek** területen jelennek meg.

    ![HDInsight R Studio webkonzol](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Hozzon létre egy helyi könyvtárat, és töltse le a mintaadatok közül. A RStudio mezőbe írja be a következő kódot:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    A letöltést körülbelül 9,5 perc múlva kell végrehajtani.

## <a name="copy-the-data-to-default-storage"></a>Az adatbázis másolása az alapértelmezett tárolóba

A Hadoop elosztott fájlrendszer (HDFS) helye a `airDataDir` változóval van megadva. A RStudio mezőbe írja be a következő kódot:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

A lépést körülbelül 10 másodperc múlva kell végrehajtani.

## <a name="set-up-a-dataset"></a>Adatkészlet beállítása

1. Hozzon létre egy olyan fájlrendszer-objektumot, amely az alapértelmezett értékeket használja. A RStudio mezőbe írja be a következő kódot:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Mivel az eredeti CSV-fájlok inkább nem megfelelő változók névvel rendelkeznek, egy *colInfo* -listát kell megadnia, hogy könnyebben kezelhető legyen. A RStudio mezőbe írja be a következő kódot:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Adatforrások létrehozása

Spark számítási környezetben a következő függvények használatával hozhat létre adatforrásokat:

|Függvény | Leírás |
|---------|-------------|
|`RxTextData` | Vesszővel tagolt szöveges adatforrás. |
|`RxXdfData` | A XDF adatfájljának formátuma. A RevoScaleR-ben a XDF fájlformátum úgy módosul, hogy a Hadoop egyetlen fájl helyett egy összetett fájlban tárolja az adathalmazokat. |
|`RxHiveData` | Létrehoz egy kaptár-adatforrás objektumot.|
|`RxParquetData` | Egy Parquet adatforrás-objektumot hoz létre.|
|`RxOrcData` | Egy ork adatforrás-objektumot hoz létre.|

Hozzon létre egy [RxTextData](/machine-learning-server/r-reference/revoscaler/rxtextdata) OBJEKTUMOT a HDFS-be másolt fájlok használatával. A RStudio mezőbe írja be a következő kódot:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Számítási környezet létrehozása a Sparkhoz

Az adatok betöltéséhez és elemzések futtatásához a munkavégző csomópontokon a parancsfájlban állítsa be a számítási környezetet a [RxSpark](/machine-learning-server/r-reference/revoscaler/rxspark). Ebben a kontextusban az R functions automatikusan elosztja a munkaterhelést az összes munkavégző csomóponton, és nincs beépített követelmény a feladatok vagy a várólista kezeléséhez. A Spark számítási környezet a `RxSpark` vagy a használatával jön `rxSparkConnect()` létre a Spark számítási környezet létrehozásához, és a használatával `rxSparkDisconnect()` visszatérhet a helyi számítási környezetbe. A RStudio mezőbe írja be a következő kódot:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Lineáris modell igazítása

1. Az adatforrást használó lineáris modellhez a [rxLinMod](/machine-learning-server/r-reference/revoscaler/rxlinmod) függvényt használhatja `airDS` . A RStudio mezőbe írja be a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Ezt a lépést 2 – 3 percen belül kell végrehajtani.

1. Tekintse meg az eredményeket. A RStudio mezőbe írja be a következő kódot:

    ```R
    summary(delayArr)
    ```

    A következő eredményeknek kell megjelennie:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Az eredmények azt jelzik, hogy a megadott könyvtárban lévő összes CSV-fájl használatával feldolgozta az összes adatmennyiséget (6 000 000). Mivel a megadott `cube = TRUE` értékkel rendelkezik, a hét minden napjára (és nem az elfogásra) vonatkozó becsült együtthatóval rendelkezik.

## <a name="use-composite-xdf-files"></a>Összetett XDF-fájlok használata

Ahogy láttuk, a CSV-fájlokat közvetlenül is elemezheti az R használatával a Hadoop. Az elemzést azonban gyorsabban elvégezheti, ha az adattárolást hatékonyabb formában tárolja. Az R XDF fájlformátuma hatékony, de a HDFS némileg módosult, így az egyes fájlok egyetlen HDFS-blokkon belül maradnak. (A HDFS blokk mérete eltér a telepítéstől a telepítésig, de általában 64 MB vagy 128 MB.) 

Ha a [rxImport](/machine-learning-server/r-reference/revoscaler/rximport) használatával összetett XDF-fájlokat hoz létre, akkor olyan adatforrást kell megadnia, mint például az in, `RxTextData` `AirDS` és egy `RxXdfData` fájlrendszerrel rendelkező adatforrást, amely a Hadoop argumentumként egy HDFS-fájlrendszerre van beállítva. Ezután használhatja az objektumot a `RxXdfData` következő R-elemzések adatargumentumaként.

1. Definiáljon egy `RxXdfData` objektumot. A RStudio mezőbe írja be a következő kódot:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Állítsa be az 250000-as blokkos méretet, és adja meg, hogy az összes adattal elolvasva. A RStudio mezőbe írja be a következő kódot:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importálja az adatkészletet a használatával `rxImport` . A RStudio mezőbe írja be a következő kódot:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Ennek a lépésnek néhány percen belül el kell végeznie.

1. Becsülje újra ugyanazt a lineáris modellt az új, gyorsabb adatforrás használatával. A RStudio mezőbe írja be a következő kódot:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    A lépésnek egy percnél rövidebbnek kell lennie.

1. Tekintse meg az eredményeket. Az eredményeknek meg kell egyezniük a CSV-fájlokkal. A RStudio mezőbe írja be a következő kódot:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF konvertálása CSV-re

### <a name="in-a-spark-context"></a>Spark-környezetben

Ha a CSV-fájlokat XDF fájlformátumba konvertálta az elemzések futtatásakor, de most szeretné visszaalakítani az adatait a CSV-be, ezt a [rxDataStep](/machine-learning-server/r-reference/revoscaler/rxdatastep)használatával teheti meg.

CSV-fájlok mappájának létrehozásához először hozzon létre egy `RxTextData` objektumot a könyvtár nevével a fájl argumentumként. Ez az objektum azt a mappát jelöli, amelyben létre kívánja hozni a CSV-fájlokat. Ez a könyvtár a futtatásakor jön létre `rxDataStep` . Ezután mutasson erre az `RxTextData` objektumra a következő `outFile` argumentumában: `rxDataStep` . Minden létrehozott CSV-fájl neve a könyvtárnév alapján történik, és egy számot követ.

Tegyük fel, hogy egy CSV-fájl mappáját szeretné kiírni a HDFS az `airDataXdf` összetett XDF, miután elvégezte a logisztikai regressziót és előrejelzést, hogy az új CSV-fájlok tartalmazzák az előre jelzett értékeket és a maradványokat. A RStudio mezőbe írja be a következő kódot:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ennek a lépésnek a végrehajtása körülbelül 2,5 percet vesz igénybe.

A `rxDataStep` bemeneti összetett XDF fájl minden XDFD fájljának kiírása egy CSV-fájlba. Ez az alapértelmezett viselkedés a CSV-fájlok összetett XDF-fájlokból HDFS való írásához, ha a számítási környezet beállítása: `RxSpark` .

### <a name="in-a-local-context"></a>Helyi kontextusban

Azt is megteheti, hogy az elemzések elvégzése után visszaválthat a számítási környezetbe, hogy `local` kihasználja a két argumentum előnyeit, így `RxTextData` valamivel jobban szabályozhatja a CSV-fájlok HDFS való kiírásakor: `createFileSet` és `rowsPerOutFile` . Ha a `createFileSet` értékre van állítva `TRUE` , a CSV-fájlok mappáját a megadott könyvtárba írja a rendszer. Ha `createFileSet` a értékre `FALSE` van állítva, a rendszer egyetlen CSV-fájlt ír. Megadhatja a második argumentumot `rowsPerOutFile` egy egész számra, hogy jelezze, hány sort kell írnia az egyes CSV-fájlokba, ha `createFileSet` van `TRUE` .

A RStudio mezőbe írja be a következő kódot:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ez a lépés körülbelül 10 percen belül befejeződik.

Számítási környezet használatakor `RxSpark` `createFileSet` az alapértelmezett érték a, `TRUE` és `rowsPerOutFile` nincs hatással. Ezért, ha egyetlen CSV-fájlt szeretne létrehozni, vagy egy fájl sorainak számát szeretné testreszabni, végezze el a `rxDataStep` `local` számítási környezetet (az adatok továbbra is HDFS).

## <a name="final-steps"></a>Utolsó lépések

1. Törölje az adatvesztést. A RStudio mezőbe írja be a következő kódot:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Állítsa le a távoli Spark alkalmazást. A RStudio mezőbe írja be a következő kódot:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Zárja be az R-munkamenetet. A RStudio mezőbe írja be a következő kódot:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes lehet törölni a fürtöt. A HDInsight az adatait az Azure Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. A HDInsight-fürtökért is fizetnie kell, még akkor is, ha nincs használatban. Mivel a fürt díjai több időt vesznek igénybe, mint a tárterületre vonatkozó díjak, gazdasági szempontból érdemes törölni a fürtöket, amikor nincsenek használatban.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az R functions szolgáltatást egy HDInsight Machine Learning Services-fürtön futó Apache Spark. További információért tekintse át a következő cikkeket:

* [Számítási környezeti beállítások Azure HDInsight Machine Learning Services-fürthöz](r-server-compute-contexts.md)
* [R functions for Spark on Hadoop](/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)