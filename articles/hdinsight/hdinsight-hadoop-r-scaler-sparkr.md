---
title: A skálázhatóság és a Spark használata az Azure HDInsight
description: A skálázhatóság és a Sparker használata az adatkezeléshez és a modellek fejlesztéséhez az Azure HDInsight ML-szolgáltatásaival
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/26/2019
ms.openlocfilehash: 95fcca289b0776cc19464b13eb7d243ca4f8d5ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945506"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>A Scaleer és a Spark kombinálása a HDInsight-ben

Ebből a dokumentumból megtudhatja, hogyan jósolhatja meg a repülési beérkezés késéseit egy **skálázhatósági** logisztikai regressziós modell használatával. A példa repülési késést és időjárási adatátvitelt használ a **sparker** használatával.

Habár mindkét csomag a Apache Hadoop Spark-végrehajtó motorján fut, a memóriában tárolt adatok megosztása blokkolja őket, mivel Mindegyikhez saját Spark-munkamenet szükséges. Amíg ez a probléma nem kerül a ML Server következő verziójában, a megkerülő megoldás a nem átfedő Spark-munkamenetek fenntartása, valamint az adatcsere közbenső fájlokon keresztül. Ebben az útmutatóban látható, hogy ezek a követelmények egyszerűen elérhetők.

Ezt a példát eredetileg a 2016-as számú előadásban osztották meg Mario inchios és Burd. Ebből a beszélgetésből megtudhatja, hogyan hozhat létre [egy skálázható adatelemzési platformot az R](https://channel9.msdn.com/blogs/Cloud-and-Enterprise-Premium/Building-A-Scalable-Data-Science-Platform-with-R-and-Hadoop)használatával.

A kód eredetileg az Azure-beli HDInsight-fürtön, a Sparkban futó ML Server lett írva. A Sparker és a méretező használatának koncepciója azonban egy parancsfájlban a helyszíni környezetek kontextusában is érvényes.

A jelen dokumentumban ismertetett lépések feltételezik, hogy az R-nek van egy közbenső szintű ismerete, és a ML Server [skálázhatósági](/machine-learning-server/r/concept-what-is-revoscaler) könyvtára. Ezt a forgatókönyvet a [sparker](https://spark.apache.org/docs/2.1.0/sparkr.html) -ben vezették be.

## <a name="the-airline-and-weather-datasets"></a>A légitársaság és az időjárási adatkészletek

A repülési adatok az [Egyesült Államok kormányzati levéltárában](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236)érhetők el. A [AirOnTimeCSV.zip](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip)zip-ként is elérhető.

Az időjárási adatok zip-fájlként tölthetők le nyers formában, a [nemzeti óceáni és a légköri adminisztrációs tárházban](https://www.ncdc.noaa.gov/orders/qclcd/). Ebben a példában a május 2007 – december 2012. Az óránkénti adatfájlok és `YYYYMMMstation.txt` a fájlok használata az egyes zip-fájlokon belül.

## <a name="setting-up-the-spark-environment"></a>A Spark-környezet beállítása

A Spark-környezet beállításához használja a következő kódot:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Ezután adja hozzá `Spark_Home` az R-csomagok keresési elérési útját. Ha hozzáadja azt a keresési útvonalhoz, a Sparker használatát és a Sparker-munkamenet inicializálását is lehetővé teszi:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Az időjárási adatfeldolgozás előkészítése

Az időjárási adattípusok előkészítéséhez a modellezéshez szükséges oszlopokat a következőhöz kell elkészíteni: 

- Láthatóság
- "DryBulbCelsius"
- "DewPointCelsius"
- "RelativeHumidity"
- Szélsebesség
- Magasságmérő

Ezután vegyen fel egy, a meteorológiai állomáshoz társított reptéri kódot, és alakítsa át a méréseket helyi idő szerint UTC értékre.

Először hozzon létre egy fájlt, amely az meteorológiai állomás (WBAN) információit egy repülőtéri kódra képezi le. A következő kód beolvassa az óránkénti nyers időjárási adatfájlokat, részhalmazokat a szükséges oszlopokra, egyesíti az meteorológiai állomás-hozzárendelési fájlt, beállítja a mérések dátumának időpontját az UTC értékre, majd kiírja a fájl új verzióját:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>A légitársaság és az időjárási adatgyűjtés importálása a Spark DataFramesba

Most a Sparker [READ. DF ()](https://spark.apache.org/docs/latest/api/R/read.df.html) függvény használatával importálja az időjárási és a légiközlekedési adatait a Spark DataFrames. Ez a függvény, mint sok más Spark-módszer, lustán fut, ami azt jelenti, hogy a rendszer a végrehajtásra várólistára helyezi, de nem hajtja végre, amíg szükséges.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Adattisztítás és-átalakítás

A következő lépés az oszlopok átnevezéséhez importált légiforgalmi Adattisztítás. Csak megtartjuk a szükséges változókat, és az ütemezett távozási időpontot a legközelebbi órára kell bontani, hogy engedélyezzük a legutóbbi időjárási adatvesztéssel való összevonást:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Most hasonló műveleteket végzünk az időjárási adatokon:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Az időjárási és a légiforgalmi szolgáltatáshoz való csatlakozás

Most a Sparker [JOIN ()](https://spark.apache.org/docs/latest/api/R/join.html) függvényt használjuk a légitársaság és az időjárási adat bal oldali külső csatlakoztatására a AirportID és a DateTime alapján. A külső illesztés lehetővé teszi, hogy az összes légitársasági adatrekordot megőrizze, még akkor is, ha nincs megfelelő időjárási adat. A csatlakozást követően eltávolítunk néhány redundáns oszlopot, és átnevezjük a megtartott oszlopokat az illesztés által bevezetett bejövő DataFrame-előtag eltávolításához.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Hasonló módon az időjárási és a légiközlekedési adatszolgáltatáshoz is csatlakozik az érkezési AirportID és a DateTime alapján:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Eredmények mentése CSV-fájlként az Exchange-hez a skálázással

Ez befejezi a Sparker-vel való csatlakozást. A "joinedDF5" végső Spark DataFrame származó adatokat egy CSV-fájlba mentjük a méretezéshez, majd lezárjuk a Sparker-munkamenetet. Explicit módon elmondjuk, hogy a Sparker az eredményül kapott CSV-t 80 különálló partíción mentse, hogy megfelelő párhuzamosságot biztosítson a skálázhatósági feldolgozásban:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importálás a XDF a méretező használatával

A beillesztett légitársaságok CSV-fájlját és az időjárási adatmennyiséget a modellezéshez a méretezési szöveg adatforrásán keresztül használhatja. Először importáljuk a XDF, mivel ez hatékonyabb, ha több műveletet futtat az adatkészleten:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Az Adatelosztás és a tesztelés

A rxDataStep használatával a 2012-es adatok kioszthatók a tesztelésre, és megtartjuk a többit a képzéshez:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Logisztikai regressziós modell betanítása és tesztelése

Most már készen áll a modell létrehozására. Ha szeretné megtekinteni, hogy az időjárási információk milyen hatással vannak a késésre az érkezés időpontjában, a skálázhatóság logisztikai regressziós rutinját használjuk. Azt a modellt használjuk, amely szerint a 15 percnél nagyobb megérkezési késleltetést az indulási és a megérkezési repülőterek időjárása befolyásolja:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Most lássuk, hogyan végzi el a tesztelési célú adatelemzéseket a ROC és a AUC megkeresésével.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Pontozás máshol

A modellt egy másik platformon lévő adatpontozásra is használhatja. Mentse egy RDS-fájlba, majd vigye át és importálja az RDS-et egy cél pontozási környezetbe, például Microsoft SQL Server R Servicesbe. Fontos meggyőződni arról, hogy az adatértékek pontszáma megegyezik a modell felépítésének szintjével. Ez a megfeleltetés a modellezési adatokhoz a skálázási `rxCreateColInfo()` függvényen keresztül, majd az oszlop adatainak a bemeneti adatforrásba való alkalmazásával érhető el. A következő példában a vizsgálati adatkészlet néhány sorát mentjük, és kinyerjük és használjuk az oszlop adatait ebből a mintából az előrejelzési parancsfájlban:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Összefoglalás

Ebben a cikkben bemutatjuk, hogyan lehet kombinálni a Sparker használatát az adatkezeléshez a Hadoop Spark modell-fejlesztéséhez. Ehhez a forgatókönyvhöz külön Spark-munkameneteket kell fenntartania, egyszerre csak egy munkamenetet kell futtatnia, és az adatcserét CSV-fájlok használatával kell megadnia. Habár egyértelmű, ennek a folyamatnak még egyszerűbbnek kell lennie egy közelgő ML-es szolgáltatási kiadásban, amikor a Sparker és a skálázás megoszthat egy Spark-munkamenetet, és megoszthatja a Spark DataFrames.

## <a name="next-steps-and-more-information"></a>Következő lépések és további információk

- A Apache Spark ML Server használatáról további információt az [első lépéseket ismertető útmutatóban](/machine-learning-server/r/how-to-revoscaler-spark)talál.

- A HDInsight ML-szolgáltatásaival kapcsolatos információkért lásd: a [HDINSIGHT ml szolgáltatásainak áttekintése](r-server/r-server-overview.md).

A Sparker használatáról további információt a következő témakörben talál:

- [Apache sparker-dokumentum](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [A sparker áttekintése](/azure/databricks/spark/latest/sparkr/overview)
