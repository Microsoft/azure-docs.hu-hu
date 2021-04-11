---
title: A Spark-alkalmazások függőségeinek kezelése az Azure HDInsight
description: Ez a cikk bemutatja, hogyan kezelheti a Spark-függőségeket a HDInsight Spark-fürtben a PySpark és a Scala alkalmazásokhoz.
author: yanancai
ms.author: yanacai
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: f4940da47b832c2b3c39ab2fa225a229d1d730bf
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062709"
---
# <a name="manage-spark-application-dependencies"></a>Spark-alkalmazások függőségeinek kezelése

Ebből a cikkből megtudhatja, hogyan kezelheti a HDInsight-on futó Spark-alkalmazások függőségeit. A PySpark a Spark-alkalmazás és a fürt hatóköre is kiterjed.

A rövid hivatkozások használatával ugorjon a szakaszra a felhasználói eset alapján:
* [Spark-feladatok jar-függőségeinek beállítása Jupyter Notebook használatával](#use-jupyter-notebook)
* [Spark-feladatok jar-függőségeinek beállítása a use Azure Toolkit for IntelliJ használatával](#use-azure-toolkit-for-intellij)
* [Jar-függőségek konfigurálása a Spark-fürthöz](#jar-libs-for-cluster)
* [Jar-függőségek biztonságos kezelése](#safely-manage-jar-dependencies)
* [A Spark-feladatok Python-csomagjainak beállítása Jupyter Notebook használatával](#use-jupyter-notebook-1)
* [Python-csomagok biztonságos kezelése a Spark-fürthöz](#python-packages-for-cluster)

## <a name="jar-libs-for-one-spark-job"></a>Jar könyvtárak egy Spark-feladatokhoz
### <a name="use-jupyter-notebook"></a>Jupyter Notebook használata
Ha egy Spark-munkamenet a Scala-ben Jupyter Notebook a Spark kernelen, a következő csomagokat állíthatja be:

* [Maven-tárház](https://search.maven.org/)vagy Közösség által készített csomagok a [Spark-csomagokban](https://spark-packages.org/).
* A fürt elsődleges tárolójában tárolt JAR-fájlok.

A Magic használatával úgy `%%configure` konfigurálhatja a jegyzetfüzetet, hogy külső csomagot használjon. Külső csomagokat használó jegyzetfüzetekben ügyeljen arra, hogy az `%%configure` első kód cellájában hívja meg a magict. Ez biztosítja, hogy a kernel a csomag használatára legyen konfigurálva a munkamenet elindítása előtt.

>
>[!IMPORTANT]  
>Ha elfelejti a rendszermag konfigurálását az első cellában, használja a `%%configure` `-f` paramétert, de a rendszer újraindítja a munkamenetet, és az összes folyamat el fog veszni.

**Minta a Maven adattárból vagy Spark-csomagokból származó csomagokhoz**

Miután megtalálta a csomagot a Maven adattárból, Gyűjtse össze a **GroupID**, a **ArtifactId** és a **verzió** értékeit. Összefűzi a három értéket kettősponttal elválasztva (**:**).

   A :::image type="content" source="./media/apache-spark-manage-dependencies/spark-package-schema.png " alt-text="csomag sémájának" border="true":::"Border =" true ":::

Győződjön meg arról, hogy az összegyűjtött értékek megfelelnek a fürtnek. Ebben az esetben a HDInsight 3,6 Spark-fürthöz készült Spark Cosmos DB Connector-csomagot használjuk a Scala 2,11 és a Spark 2,3 számára. Ha nem biztos abban, hogy a `scala.util.Properties.versionString` fürt Scala-verziójának lekéréséhez a Spark kernel kód cellájában futtassa a parancsot. Futtassa `sc.version` a parancsot a fürt Spark-verziójának beolvasásához.

```
%%configure { "conf": {"spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.3.3" }}
```

**Az elsődleges tárolóban tárolt tégelyek mintája**

Használja az [URI-sémát](../hdinsight-hadoop-linux-information.md#URI-and-scheme) a fürtök elsődleges tárolóján található jar-fájlokhoz. Ez az `wasb://` Azure Storage számára lenne `abfs://` Azure Data Lake Storage Gen2 vagy Azure Data Lake Storage Gen1 esetén `adl://` . Ha a biztonságos átvitel engedélyezve van az Azure Storage vagy a Data Lake Storage Gen2 számára, az URI a következő lesz: `wasbs://` vagy `abfss://` . Lásd: [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

A jar-elérési utak vesszővel tagolt listáját használhatja több jar-fájlhoz, a globs használata engedélyezett. A tégelyek az illesztőprogramban és a végrehajtó classpaths is szerepelnek.

```
%%configure { "conf": {"spark.jars": "wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/azure-cosmosdb-spark_2.3.0_2.11-1.3.3.jar" }}
```

A külső csomagok konfigurálása után az importálás kód cellában futtatva ellenőrizheti, hogy a csomagok helyesen vannak-e elhelyezve.

```scala
import com.microsoft.azure.cosmosdb.spark._
```

### <a name="use-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ használata
[Azure Toolkit for IntelliJ beépülő](./apache-spark-intellij-tool-plugin.md) modul felhasználói felületi élményt nyújt a Spark Scala-alkalmazás HDInsight-fürthöz való beküldéséhez. A szolgáltatás `Referenced Jars` `Referenced Files` a Spark-alkalmazás elküldésekor a jar könyvtárak elérési útjainak konfigurálására és tulajdonságaira szolgál. További információ a [HDInsight Azure Toolkit for IntelliJ beépülő moduljának használatáról](./apache-spark-intellij-tool-plugin.md#run-a-spark-scala-application-on-an-hdinsight-spark-cluster).

:::image type="content" source="./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png" alt-text="A Spark beküldése párbeszédpanel" border="true":::

## <a name="jar-libs-for-cluster"></a>Jar könyvtárak a fürthöz
Bizonyos esetekben előfordulhat, hogy a jar-függőségeket a fürt szintjén kell konfigurálni, hogy az egyes alkalmazások alapértelmezés szerint azonos függőségekkel is beállíthatók legyenek. A módszer a jar-elérési utak hozzáadása a Spark-illesztőprogramhoz és a végrehajtó osztály elérési útjához.

1. Az alábbi minta parancsfájl-műveletek futtatásával másolja a jar-fájlokat az elsődleges tárolóból `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` a fürt helyi fájljába `/usr/libs/sparklibs` . Erre a lépésre azért van szükség, mert a Linux `:` az osztályok elérési útjainak listáját használja, de a HDInsight csak olyan sémákat támogatnak, mint a `wasb://` . A távoli tárolási útvonal nem fog megfelelően működni, ha közvetlenül hozzáadja az osztály elérési útjához.

    ```bash
    sudo mkdir -p /usr/libs/sparklibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/sparklibs
    ```

2. Módosítsa a Spark szolgáltatás konfigurációját a Ambari-ből az osztály elérési útjának frissítéséhez. Ugrás a **Ambari > Spark > konfigurációk > egyéni Spark2 – alapértelmezett beállítások**. **Adja hozzá a tulajdonságot** az alábbiak szerint. `:`Ha több elérési úttal rendelkezik, az elérési utakat válassza el. A globs használata engedélyezett.

    ```
    spark.driver.extraClassPath=/usr/libs/sparklibs/*
    spark.executor.extraClassPath=/usr/libs/sparklibs/*
    ```

   A :::image type="content" source="./media/apache-spark-manage-dependencies/change-spark-default-config.png " alt-text="Spark alapértelmezett konfigurációjának módosítása" border="true":::QL config "Border =" true ":::

3. Mentse a módosított konfigurációkat, és indítsa újra az érintett szolgáltatásokat.

   Az érintett :::image type="content" source="./media/apache-spark-manage-dependencies/restart-impacted-services.png " alt-text="szolgáltatások újraindítása" border="true":::a "Border =" true ":::

A lépéseket [parancsfájl-műveletek](../hdinsight-hadoop-customize-cluster-linux.md)használatával automatizálhatja. A [kaptár egyéni kódtárainak hozzáadására](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh) szolgáló parancsfájl-művelet jó hivatkozás. A Spark szolgáltatás konfigurációjának módosításakor ügyeljen arra, hogy a Ambari API-kat használja közvetlenül a konfigurációs fájlok módosítása helyett. 

## <a name="safely-manage-jar-dependencies"></a>Jar-függőségek biztonságos kezelése
A HDInsight-fürt beépített jar-függőségekkel rendelkezik, és ezek a jar-verziók frissítései időről időre megtörténnek. Ha el szeretné kerülni a verziók ütközését a beépített tégelyek és a hivatkozott tégelyek között, érdemes megfontolnia [az alkalmazás függőségeinek árnyékolását](./safely-manage-jar-dependency.md).

## <a name="python-packages-for-one-spark-job"></a>Python-csomagok egy Spark-feladatokhoz
### <a name="use-jupyter-notebook"></a>Jupyter Notebook használata
A HDInsight Jupyter Notebook PySpark kernel nem támogatja a Python-csomagok telepítését közvetlenül a PyPi vagy az anaconda Package adattárból. Ha rendelkezik `.zip` , `.egg` vagy függőségekkel rendelkezik, `.py` és egy Spark-munkamenetre szeretne hivatkozni, kövesse az alábbi lépéseket:

1. Futtassa az alábbi minta parancsfájl-műveleteket a másoláshoz `.zip` , `.egg` illetve `.py` az elsődleges tárolóból `wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*` a fürt helyi fájlrendszerbe való `/usr/libs/pylibs` másolásához. Erre a lépésre azért van szükség, mert a Linux `:` a keresési útvonalak listáját választja, de a HDInsight csak olyan sémákat támogatnak, mint például a tárolási útvonalak `wasb://` . A távoli tároló elérési útja nem fog megfelelően működni a használatakor `sys.path.insert` .

    ```bash
    sudo mkdir -p /usr/libs/pylibs
    sudo hadoop fs -copyToLocal wasb://mycontainer@mystorageaccount.blob.core.windows.net/libs/*.* /usr/libs/pylibs
    ```

2. A jegyzetfüzetben futtassa az alábbi kódot egy PySpark kernelt tartalmazó kód cellában:

   ```python
   import sys
   sys.path.insert(0, "/usr/libs/pylibs/pypackage.zip")
   ```

3. A futtatásával `import` ellenőrizze, hogy a csomagok bekerültek-e sikeresen.  

## <a name="python-packages-for-cluster"></a>Python-csomagok a fürthöz
A Conda paranccsal parancsfájl-műveletek használatával is telepíthet Python-csomagokat az Anacondaből a fürtbe. A telepített csomagok a fürt szintjén vannak, és minden alkalmazásra érvényesek. 

A HDInsight Spark-fürt két beépített Python-telepítéssel rendelkezik, a anaconda Python 2,7 és a anaconda Python 3,5. Ha többet szeretne megtudni a szolgáltatások alapértelmezett Python-beállításairól, valamint arról, hogyan lehet biztonságosan telepíteni a külső Python-csomagokat a fürt megszakítása nélkül, tekintse meg a [fürthöz tartozó Python-függőségek biztonságos kezelésével](./apache-spark-python-package-installation.md)foglalkozó témakört.
