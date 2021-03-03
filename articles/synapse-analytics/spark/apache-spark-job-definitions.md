---
title: 'Oktatóanyag: Apache Spark feladatdefiníció létrehozása a szinapszis Studióban'
description: Oktatóanyag – az Azure szinapszis Analytics használatával Spark-feladatdefiníciók hozhatók létre, és beküldhetik azokat egy Apache Sparkba az Azure szinapszis Analytics-készletben.
author: Jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 10/16/2020
ms.openlocfilehash: 3f1e3fd360197310a89a67d43053649d904aeb18
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677609"
---
# <a name="tutorial-create-apache-spark-job-definition-in-synapse-studio"></a>Oktatóanyag: Apache Spark feladatdefiníció létrehozása a szinapszis Studióban

Ez az oktatóanyag azt mutatja be, hogyan használható az Azure szinapszis Studio Apache Spark feladatdefiníció létrehozásához, majd küldje el őket egy kiszolgáló nélküli Apache Spark készletbe.

Ez az oktatóanyag a következő feladatokat mutatja be:
> [!div class="checklist"]
>
> - Apache Spark feladatdefiníció létrehozása a PySpark (Python)
> - Apache Spark feladatdefiníció létrehozása a Sparkhoz (Scala)
> - Apache Spark feladatdefiníció létrehozása a .NET Sparkhoz (C#/F #)
> - Apache Spark feladatdefiníció beküldése batch-feladatokként
> - Apache Spark feladatdefiníció hozzáadása a folyamathoz

## <a name="prerequisites"></a>Előfeltételek

Mielőtt nekilát az oktatóanyagnak, ellenőrizze, hogy megfelel-e a következő feltételeknek:

* Egy Azure szinapszis Analytics-munkaterület. Útmutatásért lásd: [Azure szinapszis Analytics-munkaterület létrehozása](../../machine-learning/how-to-manage-workspace.md).
* Kiszolgáló nélküli Apache Spark készlet.
* Egy ADLS Gen2 Storage-fiók. A használni kívánt ADLS Gen2 fájlrendszer **Storage blob-Adattulajdonosának** kell lennie. Ha nem, manuálisan kell hozzáadnia az engedélyt.
* Ha nem szeretné használni a munkaterület alapértelmezett tárolóját, kapcsolja össze a szükséges ADLS Gen2 Storage-fiókot a szinapszis Studióban. 

## <a name="create-an-apache-spark-job-definition-for-pyspark-python"></a>Apache Spark feladatdefiníció létrehozása a PySpark (Python)

Ebben a szakaszban a PySpark (Python) Apache Spark feladatainak definícióját hozza létre.

1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).

2. A Apache Spark-feladatdefiníciók [létrehozásához](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Python) nyissa meg a mintaképeket **python.zipához**, majd bontsa ki a tömörített csomagot, és bontsa ki a **WordCount.py** és **shakespeare.txt** fájlokat. 

     ![Példák a fájlokra](./media/apache-spark-job-definitions/sample-files.png)

3. Válassza ki az **adatokhoz**  ->  **csatolt**  ->  **Azure Data Lake Storage Gen2**, és töltse fel a **WordCount.py** , és **shakespeare.txt** a ADLS Gen2 fájlrendszerbe. 

     ![Python-fájl feltöltése](./media/apache-spark-job-definitions/upload-python-file.png)

4. Válassza ki a hub **fejlesztése** lehetőséget, válassza a "+" ikont, és válassza a **Spark-feladattípus** lehetőséget egy új Spark-feladatdefiníció létrehozásához. 

     ![új definíció létrehozása a Pythonhoz](./media/apache-spark-job-definitions/create-new-definition.png)

5. Válassza a **PySpark (Python)** elemet a Apache Spark Job definition Main ablak nyelv legördülő listájában.

     ![Python kiválasztása](./media/apache-spark-job-definitions/select-python.png)

6. Adja meg Apache Spark-feladatdefiníció adatait. 

     |  Tulajdonság   | Leírás   |  
     | ----- | ----- |  
     |Feladatdefiníció neve| Adja meg a Apache Spark-feladatdefiníció nevét. Ez a név bármikor frissíthető, amíg közzé nem teszi. <br> Minta `job definition sample`|
     |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy fájlt a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. <br> Minta `abfss://…/path/to/wordcount.py`|
     |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok. <br> Minta: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Megjegyzés: a minta-feladatdefiníció két argumentuma szóközzel van elválasztva.*|
     |Hivatkozási fájlok| A fő definíciós fájlban való hivatkozáshoz használt további fájlok. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. |
     |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Apache Spark-készletbe.|
     |Spark-verzió| A Apache Spark-készletet futtató Apache Spark verziója.|
     |Végrehajtók| A feladathoz megadott Apache Spark készletben megadható végrehajtók száma.|
     |Végrehajtó mérete| A feladathoz megadott Apache Spark készletben megadott végrehajtók számára használandó magok és memória száma.|  
     |Illesztőprogram mérete| A feladathoz megadott Apache Spark készletben megadott illesztőprogramhoz használandó magok és memória száma.|

     ![A Spark-feladatdefiníció értékének beállítása a Pythonhoz](./media/apache-spark-job-definitions/create-py-definition.png)

7. Válassza a **Közzététel** lehetőséget a Apache Spark feladatdefiníció mentéséhez.

     ![a-konfiguráció közzétételének meghatározása](./media/apache-spark-job-definitions/publish-py-definition.png)

## <a name="create-an-apache-spark-job-definition-for-apache-sparkscala"></a>Apache Spark (Scala) Apache Spark feladatdefiníció létrehozása

Ebben a szakaszban létrehoz egy Apache Spark Apache Spark (Scala) feladatdefiníció.

 1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).

 2. A Apache Spark-feladatdefiníciók [létrehozásához](https://github.com/Azure-Samples/Synapse/tree/master/Spark/Scala) nyissa meg a mintaképeket **scala.zipához**, majd bontsa ki a tömörített csomagot, és bontsa ki a **WordCount. jar** és **shakespeare.txt** fájlokat. 
 
     ![Sample Files Scala](./media/apache-spark-job-definitions/sample-files-scala.png)

 3. Válassza ki az **adatokhoz**  ->  **csatolt**  ->  **Azure Data Lake Storage Gen2**, és töltse fel a **WordCount. jar** fájlt, és **shakespeare.txt** a ADLS Gen2 fájlrendszerbe.
 
     ![a Scala-struktúra előkészítése](./media/apache-spark-job-definitions/prepare-scala-structure.png)

 4. Válassza ki a hub **fejlesztése** lehetőséget, válassza a "+" ikont, és válassza a **Spark-feladattípus** lehetőséget egy új Spark-feladatdefiníció létrehozásához. (A minta képe megegyezik a **Apache Spark Job Definition (Python) PySpark létrehozásához szükséges** 4. lépéssel.)

 5. Válassza ki a **Spark (Scala)** elemet a Apache Spark Job definition Main ablak nyelv legördülő listájában.

     ![a Scala kiválasztása](./media/apache-spark-job-definitions/select-scala.png)

 6. Adja meg Apache Spark-feladatdefiníció adatait. A mintaadatok másolására is lehetőség van.

     |  Tulajdonság   | Leírás   |  
     | ----- | ----- |  
     |Feladatdefiníció neve| Adja meg a Apache Spark-feladatdefiníció nevét. Ez a név bármikor frissíthető, amíg közzé nem teszi. <br> Minta `scala`|
     |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon ki egy JAR-fájlt a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. <br> Minta `abfss://…/path/to/wordcount.jar`|
     |Fő osztály neve| A fő definíciós fájlban lévő teljes azonosító vagy fő osztály. <br> Minta `WordCount`|
     |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok. <br> Minta: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Megjegyzés: a minta-feladatdefiníció két argumentuma szóközzel van elválasztva.* |
     |Hivatkozási fájlok| A fő definíciós fájlban való hivatkozáshoz használt további fájlok. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
     |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Apache Spark-készletbe.|
     |Spark-verzió| A Apache Spark-készletet futtató Apache Spark verziója.|
     |Végrehajtók| A feladathoz megadott Apache Spark készletben megadható végrehajtók száma.|  
     |Végrehajtó mérete| A feladathoz megadott Apache Spark készletben megadott végrehajtók számára használandó magok és memória száma.|
     |Illesztőprogram mérete| A feladathoz megadott Apache Spark készletben megadott illesztőprogramhoz használandó magok és memória száma.|

     ![A Spark-feladatdefiníció értékének beállítása a Scala számára](./media/apache-spark-job-definitions/create-scala-definition.png)

 7. Válassza a **Közzététel** lehetőséget a Apache Spark feladatdefiníció mentéséhez.

      ![a Scala definíciójának közzététele](./media/apache-spark-job-definitions/publish-scala-definition.png)

## <a name="create-an-apache-spark-job-definition-for-net-sparkcf"></a>Apache Spark feladatdefiníció létrehozása a .NET Sparkhoz (C#/F #)

Ebben a szakaszban létre fog hozni egy Apache Spark feladatdefiníció a .NET Sparkhoz (C#/F #).
 1. Nyissa meg az [Azure szinapszis Studio alkalmazást](https://web.azuresynapse.net/).

 2. A Apache Spark-feladatdefiníciók [létrehozásához](https://github.com/Azure-Samples/Synapse/tree/master/Spark/DotNET) nyissa meg a mintaképeket **dotnet.zipához**, majd bontsa ki a tömörített csomagot, és bontsa ki a **wordcount.zip** és **shakespeare.txt** fájlokat. 

     ![DotNet-minta](./media/apache-spark-job-definitions/sample-dotnet.png)

 3. Válassza ki az **adatokhoz**  ->  **csatolt**  ->  **Azure Data Lake Storage Gen2**, és töltse fel **wordcount.zip** és **shakespeare.txt** a ADLS Gen2 fájlrendszerbe.
 
     ![DotNet-struktúra előkészítése](./media/apache-spark-job-definitions/prepare-dotnet-structure.png)

 4. Válassza ki a hub **fejlesztése** lehetőséget, válassza a "+" ikont, és válassza a **Spark-feladattípus** lehetőséget egy új Spark-feladatdefiníció létrehozásához. (A minta képe megegyezik a **Apache Spark Job Definition (Python) PySpark létrehozásához szükséges** 4. lépéssel.)

 5. Válassza a **.net Spark (C#/f #)** lehetőséget a Apache Spark Job definition főablakában található nyelv legördülő listából.

     ![a DotNet kiválasztása](./media/apache-spark-job-definitions/select-dotnet.png)

 6. Adja meg Apache Spark-feladatdefiníció adatait. A mintaadatok másolására is lehetőség van.
    
     |  Tulajdonság   | Leírás   |  
     | ----- | ----- |  
     |Feladatdefiníció neve| Adja meg a Apache Spark-feladatdefiníció nevét. Ez a név bármikor frissíthető, amíg közzé nem teszi. <br> Minta `dotnet`|
     |Fő definíciós fájl| A feladatokhoz használt fő fájl. Válasszon egy ZIP-fájlt, amely tartalmazza a .NET for Apache Spark alkalmazást (azaz a fő végrehajtható fájlt, a felhasználó által definiált függvényeket tartalmazó DLL-eket és az egyéb szükséges fájlokat) a tárolóból. A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba. <br> Minta `abfss://…/path/to/wordcount.zip`|
     |Fő végrehajtható fájl| A fő végrehajtható fájl a fő definíciós ZIP-fájlban. <br> Minta `WordCount`|
     |Parancssori argumentumok| A feladatokhoz nem kötelező argumentumok. <br> Minta: `abfss://…/path/to/shakespeare.txt``abfss://…/path/to/result` <br> *Megjegyzés: a minta-feladatdefiníció két argumentuma szóközzel van elválasztva.* |
     |Hivatkozási fájlok| További fájlok szükségesek a munkavégző csomópontok számára a .NET Apache Spark alkalmazáshoz való végrehajtásához, amely nem szerepel a fő definíciós ZIP-fájlban (azaz függő tégelyekben, a felhasználó által definiált függvény dll-jei és más konfigurációs fájlokban). A fájl **feltöltése** lehetőség kiválasztásával feltöltheti a fájlt egy Storage-fiókba.|
     |Spark-készlet| A rendszer elküldi a feladatot a kiválasztott Apache Spark-készletbe.|
     |Spark-verzió| A Apache Spark-készletet futtató Apache Spark verziója.|
     |Végrehajtók| A feladathoz megadott Apache Spark készletben megadható végrehajtók száma.|  
     |Végrehajtó mérete| A feladathoz megadott Apache Spark készletben megadott végrehajtók számára használandó magok és memória száma.|
     |Illesztőprogram mérete| A feladathoz megadott Apache Spark készletben megadott illesztőprogramhoz használandó magok és memória száma.|

     ![A Spark-feladatdefiníció értékének beállítása a DotNet-hez](./media/apache-spark-job-definitions/create-dotnet-definition.png)

 7. Válassza a **Közzététel** lehetőséget a Apache Spark feladatdefiníció mentéséhez.

      ![DotNet-definíció közzététele](./media/apache-spark-job-definitions/publish-dotnet-definition.png)

## <a name="submit-an-apache-spark-job-definition-as-a-batch-job"></a>Apache Spark feladatdefiníció beküldése batch-feladatokként

Apache Spark feladatdefiníció létrehozása után elküldheti azt egy Apache Spark készletbe. Győződjön meg arról, hogy a ADLS Gen2 fájlrendszer **Storage blob-Adattulajdonosa** , amellyel dolgozni szeretne. Ha nem, manuálisan kell hozzáadnia az engedélyt.

### <a name="scenario-1-submit-apache-spark-job-definition"></a>1. forgatókönyv: Apache Spark feladatdefiníció elküldése
 1. A kiválasztásával nyisson meg egy Apache Spark Job definition-ablakot.

      ![Az elküldéshez nyissa meg a Spark-feladatdefiníció ](./media/apache-spark-job-definitions/open-spark-definition.png)

 2. Kattintson a **Submit (Küldés** ) gombra a projektnek a kiválasztott Apache Spark készletbe való beküldéséhez. Kiválaszthatja a **Spark monitoring URL** fület, ahol megtekintheti a Apache Spark alkalmazás LogQuery.

    ![A Spark-feladatdefiníció elküldéséhez válassza a Küldés gombot.](./media/apache-spark-job-definitions/submit-spark-definition.png)

    ![A Spark beküldése párbeszédpanel](./media/apache-spark-job-definitions/submit-definition-result.png)

### <a name="scenario-2-view-apache-spark-job-running-progress"></a>2. forgatókönyv: folyamatban lévő Apache Spark-feladatok megtekintése

 1. Válassza a **figyelés**, majd a **Apache Spark alkalmazások** lehetőséget. Megtalálhatja az elküldött Apache Spark alkalmazást.

     ![Spark-alkalmazás megtekintése](./media/apache-spark-job-definitions/view-spark-application.png)

 2. Ezután válasszon ki egy Apache Spark alkalmazást, a **SparkJobDefinition** -feladatok ablak jelenik meg. Itt megtekintheti a feladatok végrehajtásának folyamatát.
     
     ![Spark-alkalmazás LogQuery megtekintése](./media/apache-spark-job-definitions/view-job-log-query.png)

### <a name="scenario-3-check-output-file"></a>3. forgatókönyv: kimeneti fájl keresése

 1. Válassza **az**  ->  **adatcsatolt**  ->  **Azure Data Lake Storage Gen2** (hozhaobdbj) elemet, nyissa meg a korábban létrehozott **Result** mappát, lépjen az eredmény mappájába, és ellenőrizze, hogy létrejött-e a kimenet.

     ![Kimeneti fájl megtekintése](./media/apache-spark-job-definitions/view-output-file.png)

## <a name="add-an-apache-spark-job-definition-into-pipeline"></a>Apache Spark feladatdefiníció hozzáadása a folyamathoz

Ebben a szakaszban egy Apache Spark feladatdefiníció hozzáadása a folyamathoz.

 1. Nyisson meg egy meglévő Apache Spark feladatdefiníció.

 2. Válassza ki Apache Spark feladatdefiníció jobb felső sarkában található ikont, válassza a **meglévő folyamat** vagy az **új folyamat** elemet. További információért tekintse meg a folyamat lapját.

     ![Hozzáadás a pipeline1](./media/apache-spark-job-definitions/add-to-pipeline01.png)

     ![Hozzáadás a pipeline2](./media/apache-spark-job-definitions/add-to-pipeline02.png)

## <a name="next-steps"></a>Következő lépések

Ezután az Azure szinapszis Studio használatával létrehozhat Power BI adatkészleteket, és kezelheti a Power BI adatokat. Ha további információt szeretne, folytassa a [Power bi munkaterület összekapcsolásával egy szinapszis-munkaterülettel](../quickstart-power-bi.md) . 

