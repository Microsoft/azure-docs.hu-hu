---
title: U-SQL-parancsfájlok kiterjesztése R-Azure Data Lake Analytics
description: Megtudhatja, hogyan futtathat R-kódokat az U-SQL-szkriptekben Azure Data Lake Analytics használatával. R-kód beágyazása vagy hivatkozás a fájlokból.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/20/2017
ms.openlocfilehash: ab28ba1bb6185f5f0ab90df99f0abea7895b1bf4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218000"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>U-SQL-parancsfájlok kiterjesztése R-kóddal Azure Data Lake Analytics

Az alábbi példa az R-kód üzembe helyezésének alapvető lépéseit szemlélteti:

* A `REFERENCE ASSEMBLY` U-SQL-parancsfájl R-bővítményeinek engedélyezéséhez használja az utasítást.
* A `REDUCE` művelettel particionálhatja a bemeneti adatokat egy kulcson.
* Az U-SQL R-bővítményei közé tartozik egy beépített szűkítő ( `Extension.R.Reducer` ), amely az r-kódot futtatja a szűkítőhöz rendelt minden egyes csúcsponton.
* A dedikált névvel ellátott adatkeretek használata az `inputFromUSQL` `outputToUSQL` U-SQL és az R közötti adatok továbbításához. a bemeneti és a kimeneti DataFrame-azonosító neve rögzített (azaz a felhasználók nem változtathatják meg a bemeneti és kimeneti DataFrame-azonosítók előre megadott neveit).

## <a name="embedding-r-code-in-the-u-sql-script"></a>R-kód beágyazása a U-SQL-parancsfájlba

A U-SQL-szkriptet a (z) parancs paraméterének használatával lehet bemutatni `Extension.R.Reducer` . Deklarálhatja például az R-szkriptet karakterlánc-változóként, és paraméterként átadhatja a Szűkítőnek.

```usql
REFERENCE ASSEMBLY [ExtR];

DECLARE @myRScript = @"
inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
#do not return readonly columns and make sure that the column names are the same in usql and r cripts,
outputToUSQL=data.frame(summary(lm.fit)$coefficients)
colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
outputToUSQL
";

@RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, ReturnType:"dataframe");
```

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Az R-kód megtartása egy külön fájlban, és hivatkozása a U-SQL-parancsfájlra

Az alábbi példa egy összetettebb használatot mutat be. Ebben az esetben az R-kód a U-SQL-szkriptként üzemelő ERŐFORRÁSként van telepítve.

Az R-kód mentése különálló fájlként.

```usql
load("my_model_LM_Iris.rda")
outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
```

Egy U-SQL-parancsfájllal telepítse az R-szkriptet az üzembe helyezési erőforrás-utasítással.

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
DECLARE @PartitionCount int = 10;
@InputData =
    EXTRACT
        SepalLength double,
        SepalWidth double,
        PetalLength double,
        PetalWidth double,
        Species string
    FROM @IrisData
    USING Extractors.Csv();
@ExtendedData =
    SELECT
        Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
        SepalLength,
        SepalWidth,
        PetalLength,
        PetalWidth
    FROM @InputData;
// Predict Species
@RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, fit double, lwr double, upr double
    READONLY Par
    USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
    OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();
```

## <a name="how-r-integrates-with-u-sql"></a>Az R integrálása az U-SQL-sel

### <a name="datatypes"></a>Adattípusok

* A U-SQL-ből származó karakterlánc-és numerikus oszlopok az R DataFrame és U-SQL [támogatott típusok:,,, `double` `string` `bool` `integer` , `byte` ] között lesznek konvertálva.
* Az `Factor` adattípus nem támogatott az U-SQL-ben.
* `byte[]` Base64 kódolású szerializálva kell lennie `string` .
* Az u-SQL-karakterláncok az R-kódban szereplő tényezőkre konvertálhatók, ha U-SQL-t hoz létre az R input dataframe, vagy beállítja a csökkentő paramétert `stringsAsFactors: true` .

### <a name="schemas"></a>Sémák

* Az U-SQL-adatkészletek nem rendelkezhetnek ismétlődő oszlopnevek.
* Az U-SQL-adatkészletek oszlopainak karakterláncoknak kell lenniük.
* Az oszlopnevek a U-SQL és az R parancsfájlok esetében azonosnak kell lenniük.
* A írásvédett oszlop nem lehet a kimeneti dataframe része. Mivel az írásvédett oszlopok automatikusan bekerülnek a U-SQL-táblába, ha az a UDO kimeneti sémájának része.

### <a name="functional-limitations"></a>Funkcionális korlátozások

* Az R-motor nem hozható létre kétszer ugyanabban a folyamatban.
* Jelenleg az U-SQL nem támogatja a Udo-t a redukáló Udo használatával generált particionált modellek használatával. A felhasználók a particionált modelleket erőforrásként deklarálják, és az R-szkriptben használhatják őket (lásd: mintakód `ExtR_PredictUsingLMRawStringReducer.usql` )

### <a name="r-versions"></a>R-verziók

Csak az R 3.2.2 támogatott.

### <a name="standard-r-modules"></a>Standard R-modulok

```usql
base
boot
Class
Cluster
codetools
compiler
datasets
doParallel
doRSR
foreach
foreign
Graphics
grDevices
grid
iterators
KernSmooth
lattice
MASS
Matrix
Methods
mgcv
nlme
Nnet
Parallel
pkgXMLBuilder
RevoIOQ
revoIpe
RevoMods
RevoPemaR
RevoRpeConnector
RevoRsrConnector
RevoScaleR
RevoTreeView
RevoUtils
RevoUtilsMath
Rpart
RUnit
spatial
splines
Stats
stats4
survival
Tcltk
Tools
translations
utils
XML
```

### <a name="input-and-output-size-limitations"></a>Bemeneti és kimeneti méretre vonatkozó korlátozások

Minden csúcspont korlátozott mennyiségű memóriát rendel hozzá. Mivel a bemeneti és a kimeneti DataFrames léteznie kell az R-kód memóriájában, a bemenet és a kimenet teljes mérete nem haladhatja meg a 500 MB-ot.

### <a name="sample-code"></a>Mintakód

Az U-SQL Advanced Analytics Extensions telepítése után több mintakód is elérhető a Data Lake Store-fiókjában. A további mintakód elérési útja a következő: `<your_account_address>/usqlext/samples/R` .

## <a name="deploying-custom-r-modules-with-u-sql"></a>Egyéni R-modulok üzembe helyezése U-SQL-vel

Először hozzon létre egy egyéni R-modult és zip-fájlt, majd töltse fel a tömörített R egyéni modult az ADL áruházba. A példában a rendszer feltölti magittr_1.5.zip az alapértelmezett ADLS-fiók gyökerébe az általunk használt ADLA-fiókhoz. Miután feltöltötte a modult az ADL Store-ba, állapítsa meg, hogy használja az erőforrás üzembe helyezése lehetőséget, hogy elérhető legyen a U-SQL-parancsfájlban, és hívja meg `install.packages` a telepítését.

```usql
REFERENCE ASSEMBLY [ExtR];
DEPLOY RESOURCE @"/magrittr_1.5.zip";
DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";
// R script to run
DECLARE @myRScript = @"
# install the magrittr package,
install.packages('magrittr_1.5.zip', repos = NULL),
# load the magrittr package,
require(magrittr),
# demonstrate use of the magrittr package,
2 %>% sqrt
";
@InputData =
EXTRACT SepalLength double,
SepalWidth double,
PetalLength double,
PetalWidth double,
Species string
FROM @IrisData
USING Extractors.Csv();
@ExtendedData =
SELECT 0 AS Par,
*
FROM @InputData;
@RScriptOutput = REDUCE @ExtendedData ON Par
PRODUCE Par, RowId int, ROutput string
READONLY Par
USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");
OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();
```

## <a name="next-steps"></a>Következő lépések

* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* [U-SQL-parancsfájlok fejlesztése a Data Lake Tools for Visual Studio használatával](data-lake-analytics-data-lake-tools-get-started.md)
* [A U-SQL Window functions használata Azure Data Lake Analytics feladatokhoz](./data-lake-analytics-u-sql-get-started.md)