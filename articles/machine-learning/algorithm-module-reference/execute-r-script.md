---
title: 'R-szkript végrehajtása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja az R szkript végrehajtása modult a Azure Machine Learning Designerben egyéni R-kód futtatásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/17/2020
ms.openlocfilehash: bdd7fd8e19bf2de6d0b3c6b2edd4515771fae237
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98119013"
---
# <a name="execute-r-script-module"></a>R-parancsfájl végrehajtása modul

Ez a cikk azt ismerteti, hogyan használható az r-parancsfájl végrehajtása a Azure Machine Learning Designer-folyamat R-kódjának futtatásához.

Az R használatával a meglévő modulok által nem támogatott feladatokat végezhet, például: 
- Egyéni adatátalakítások létrehozása
- Saját mérőszámok használata az előrejelzések kiértékeléséhez
- Modellek létrehozása a tervezőben nem önálló modulként megvalósított algoritmusok használatával

## <a name="r-version-support"></a>R-verziók támogatása

A Azure Machine Learning Designer a CRAN (átfogó R Archive Network) R-eloszlását használja. A jelenleg használt verzió a CRAN 3.5.1-es verziója.

## <a name="supported-r-packages"></a>Támogatott R-csomagok

Az R-környezet több mint 100 csomaggal van előtelepítve. A teljes listát lásd: [előre telepített R-csomagok](#preinstalled-r-packages)szakasza.

A telepített csomagok megtekintéséhez a következő kódot is hozzáadhatja bármelyik Execute R script modulhoz.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Ha a folyamat több olyan végrehajtási R parancsfájl-modult tartalmaz, amelyek olyan csomagokat igényelnek, amelyek nem szerepelnek az előre telepített listában, telepítse a csomagokat az egyes modulokban. 

## <a name="installing-r-packages"></a>R-csomagok telepítése
További R-csomagok telepítéséhez használja a `install.packages()` metódust. A csomagok minden végrehajtási R script modulhoz telepítve vannak. Nem osztják meg egymás között az R-szkriptek többi modulját.

> [!NOTE]
> Az R-csomagot **nem** ajánlott a parancsfájl-csomagból telepíteni. Javasoljuk, hogy telepítse a csomagokat közvetlenül a parancsfájl-szerkesztőbe.
> A csomagok telepítésekor a CRAN-tárházat kell megadni, például: `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

> [!WARNING]
> A Excute R script modul nem támogatja olyan csomagok telepítését, amelyek natív fordítást igényelnek, például a csomaghoz, amelyhez a `qdap` Java és a csomag szükséges, `drc` amelyekhez C++ szükséges. Ennek az az oka, hogy ez a modul egy olyan előre telepített környezetben fut, amely nem rendszergazdai jogosultsággal rendelkezik.
> Ne telepítsen olyan csomagokat, amelyek előre összeállítottak Windows rendszeren, mivel a Designer modulok Ubuntu rendszeren futnak. Annak megállapításához, hogy egy csomag előre épül-e a Windows rendszerre, megkeresheti a [Cran](https://cran.r-project.org/) -t, és kereshet a csomagban, letöltheti az operációs rendszerének megfelelő bináris fájlt, és megtekintheti a **beépített:** részt a **Leírás** fájlban. Az alábbi példa az :::image type="content" source="media/module/r-package-description.png" alt-text="R-csomag leírását" lightbox="media/module/r-package-page.png"::: szemlélteti.

Ez a példa bemutatja az állatkert telepítését:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
 > A csomag telepítése előtt ellenőrizze, hogy már létezik-e, így nem kell megismételni a telepítést. Az ismételt telepítések miatt a webszolgáltatás-kérelmek időtúllépést okozhatnak.     

## <a name="access-to-registered-dataset"></a>Hozzáférés a regisztrált adatkészlethez

A következő mintakód a munkaterületén [regisztrált adatkészletekhez](../how-to-create-register-datasets.md) való hozzáféréshez használható:

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="uploading-files"></a>Fájlok feltöltése
Az R-szkript végrehajtása modul támogatja a fájlok feltöltését a Azure Machine Learning R SDK használatával.

Az alábbi példa bemutatja, hogyan tölthet fel egy képfájlt az R-szkript végrehajtása során:
```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

A folyamat futásának befejezése után a rendszerképet a modul jobb oldali paneljén tekintheti meg.

> [!div class="mx-imgBorder"]
> ![Feltöltött rendszerkép előnézete](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Az R-szkript végrehajtásának konfigurálása

Az R-szkript végrehajtása modul kiindulási pontként tartalmazza a mintakód kódot.

![R-modul bemenetei diagramja](media/module/execute-r-script.png)

A tervezőben tárolt adatkészletek automatikusan egy R-adatkeretre lesznek konvertálva, ha ezzel a modullal vannak feltöltve.

1.  Adja hozzá az **R-szkript végrehajtása** modult a folyamathoz.  

1. Csatlakoztasson minden olyan bemenetet, amelyhez a szkriptnek szüksége van. A bemenetek nem kötelezőek, és tartalmazhatnak adatokat és további R-kódokat is.

    * **DataSet1 elemet**: az első bemenetre hivatkozik `dataframe1` . A bemeneti adatkészletet CSV-, TSV-vagy ARFF-fájlként kell formázni. Vagy összekapcsolhat egy Azure Machine Learning adatkészletet.

    * **Dataset2**: a második bemenetre hivatkozik `dataframe2` . Ezt az adatkészletet CSV-, TSV-vagy ARFF-fájlként, vagy Azure Machine Learning adatkészletként kell formázni.

    * **Parancsfájl-csomag**: a harmadik bemenet elfogadja a. zip fájlokat. A tömörített fájlok több fájlt és több fájltípust is tartalmazhatnak.

1. Az **r-szkript** szövegmezőbe írja be vagy illessze be az érvényes R-szkriptet.

    > [!NOTE]
    > A szkript írásakor körültekintően járjon el. Győződjön meg arról, hogy nincsenek szintaktikai hibák, például nem deklarált változók vagy nem importált modulok vagy függvények használata. A cikk végén külön figyelmet kell fordítani az előre telepített csomagok listájára. A nem felsorolt csomagok használatához telepítse őket a parancsfájlba. Például: `install.packages("zoo&quot;,repos = &quot;http://cran.us.r-project.org")`.
    
    Az első lépésekhez az R- **szkript** szövegmezője előre fel van töltve a mintakód használatával, amely szerkeszthető vagy lecserélhető.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    A belépési pont függvénynek rendelkeznie kell a bemeneti argumentumokkal `Param<dataframe1>` `Param<dataframe2>` , és akkor is, ha ezek az argumentumok nem használatosak a függvényben.

    > [!NOTE]
    > A végrehajtási R-parancsfájlnak átadott adatok a következőre hivatkoznak: `dataframe1` és `dataframe2` , amely eltér a Azure Machine learning designertől (a tervezői hivatkozástól `dataset1` `dataset2` ). Győződjön meg arról, hogy a bemeneti adatok a parancsfájlban megfelelően vannak-e hivatkozva.  
 
    > [!NOTE]
    > Előfordulhat, hogy a meglévő R-kódnak kisebb módosításokat kell futtatnia a tervezői folyamatokban. Például a CSV formátumban megadott bemeneti adatokat explicit módon át kell alakítani egy adatkészletbe, mielőtt használni lehetne a kódban. Az R nyelvben használt adatok és oszlopok különböző módokon különböznek a tervezőben használt adatok és oszlopok típusaitól.

1. Ha a szkript mérete meghaladja a 16 KB-ot, a **parancsfájl** -létrehozási port használatával elkerülhető, hogy a *commandline érték meghaladja a 16597 karakteres korlátot*. 
    
    1. A szkriptet és más egyéni erőforrásokat csomagolja egy zip-fájlba.
    1. Töltse fel a zip-fájlt **fájl adatkészletként** a studióba. 
    1. Húzza az adatkészlet modult a Designer authoring oldal bal oldali modul paneljének *adatkészletek* listájából. 
    1. Az adatkészlet moduljának csatlakoztatása az **R-parancsfájl végrehajtása** modul **parancsfájl-köteg** portjához.
    
    Az alábbi mintakód a szkriptet használja a parancsfájl-csomagban:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  **Véletlenszerű vetőmag** esetén adjon meg egy értéket, amelyet az R-környezetben a véletlenszerű mag értékeként kíván használni. Ez a paraméter egyenértékű az R-kódban való meghívással `set.seed(value)` .  

1. A folyamat elküldése.  

## <a name="results"></a>Results (Eredmények)

Az R-parancsfájl-modulok végrehajtása több kimenetet is képes visszaadni, de R-adatkeretként kell megadni őket. A tervező automatikusan átalakítja az adatkereteket az adatkészletekbe más modulokkal való kompatibilitás érdekében.

Az R standard üzeneteit és hibáit a rendszer visszaadja a modul naplójába.

Ha az eredményeket az R-szkriptben kell kinyomtatnia, a kinyomtatott eredményeket a modul jobb oldali paneljének **kimenet + naplók** lapján találja **70_driver_log** .

## <a name="sample-scripts"></a>Mintaparancsfájlok

A folyamatokat többféleképpen is kiterjesztheti egyéni R-parancsfájlok használatával. Ez a szakasz a gyakori feladatokhoz tartalmaz mintakód-kódot.


### <a name="add-an-r-script-as-an-input"></a>R-szkript hozzáadása bemenetként

Az R-szkript végrehajtása modul bemenetként támogatja a tetszőleges R-parancsfájlokat. A használatához a. zip fájl részeként fel kell töltenie őket a munkaterületre.

1. Ha R-kódot tartalmazó. zip-fájlt szeretne feltölteni a munkaterületre, ugorjon az **adatkészletek** eszköz oldalára. Válassza az **adatkészlet létrehozása** elemet, majd válassza a **helyi fájlból** és a **fájl** adatkészletének típusa lehetőséget.  

1. Ellenőrizze, hogy a tömörített fájl megjelenik-e az **adatkészletekben** a bal oldali modul fájának **adatkészletek** kategóriája alatt.

1.  Kapcsolja össze az adatkészletet a **parancsfájl-csomag** bemeneti portjával.

1. A. zip fájlban lévő összes fájl elérhető a folyamat futási ideje alatt. 

    Ha a parancsfájl-köteg fájlja tartalmaz egy címtár-struktúrát, a rendszer megőrzi a struktúrát. Azonban módosítania kell a kódot, hogy a könyvtár **./script csomagot** adja meg az elérési útnak.

### <a name="process-data"></a>Adatok feldolgozása

Az alábbi példa bemutatja, hogyan méretezhető és normalizálható a bemeneti adatok:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Zip-fájl olvasása bemenetként

Ez a minta azt mutatja be, hogyan használhatók az adatkészletek egy. zip-fájlban bemenetként az R-parancsfájl végrehajtása modulba.

1. Hozzon létre egy CSV formátumú adatfájlt, és nevezze el **mydatafile.csv**.
1. Hozzon létre egy. zip-fájlt, és adja hozzá a CSV-fájlt az archívumhoz.
1. Töltse fel a tömörített fájlt a Azure Machine Learning munkaterületre. 
1. Az eredményül kapott adatkészlet csatlakoztatása az R- **szkript végrehajtásához** tartozó modul **ScriptBundle** -bemenetéhez.
1. Használja a következő kódot a CSV-adatoknak a tömörített fájlból való olvasásához.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Sorok replikálása

Ez a minta bemutatja, hogyan replikálhat pozitív rekordokat egy adatkészletben a minta kiegyensúlyozásához:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-objektumok átadása az R-parancsfájl-modulok végrehajtása között

Az r-objektumokat a belső szerializálási mechanizmus használatával lehet átadni az R szkript végrehajtása modul példányai között. Ez a példa feltételezi, hogy át kívánja helyezni az R-objektumot `A` két végrehajtási r parancsfájl-modul között.

1. Adja hozzá az első **végrehajtási R szkript** modult a folyamathoz. Ezután írja be a következő kódot az **R-szkript** szövegmezőbe, és hozzon létre egy szerializált objektumot `A` oszlopként a modul kimeneti adattáblájában:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Az egész típusra történő explicit konverzió történik, mivel a szerializálási függvény az R formátumban jeleníti meg az adatokat `Raw` , amelyet a tervező nem támogat.

1. Vegyen fel egy második példányt az **R szkript végrehajtása** modulhoz, és kapcsolja az előző modul kimeneti portjához.

1. Írja be a következő kódot az **R-szkript** szövegmezőbe az objektum `A` bemeneti adatok táblából való kinyeréséhez. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="preinstalled-r-packages"></a>Előre telepített R-csomagok

Jelenleg a következő előre telepített R-csomagok érhetők el:

| Csomag      | Verzió    | 
|--------------|------------| 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0 – 1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3 – 22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| kalap jel        | 6.0 – 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| osztály        | 7.3 – 15     | 
| cli          | 1.1.0      | 
| Cliper        | 0.6.0      | 
| cluster      | 2.0.7 – 1    | 
| codetools    | 0.2 – 16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| adathalmazok     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0,8 – 71     | 
| FS           | 1.3.1      | 
| gdata        | 2.18.0     | 
| generikus     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| Grafikák     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9 – 9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0,20 – 38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3 – 51.4   | 
| Mátrix       | 1.2 – 17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8 – 28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8 – 1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| PS           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quanteda     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1 – 15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44 – 1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| tools        | 3.5.1      | 
| tseries      | 0,10 – 47    | 
| TTR          | 0.23 – 4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0,11 – 2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .