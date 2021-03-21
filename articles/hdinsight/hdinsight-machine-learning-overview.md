---
title: Gépi tanulás – áttekintés – Azure HDInsight
description: Az Azure HDInsight lévő fürtök big data gépi tanulási lehetőségeinek áttekintése.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 18be3fa8851c2dbaab622c770d02f4db252a231c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945262"
---
# <a name="machine-learning-on-hdinsight"></a>Gépi tanulás a HDInsight-on

A HDInsight lehetővé teszi a gépi tanulást a big dataokkal, így értékes információkhoz juthat a strukturált, strukturálatlan és gyorsan mozgó adatok nagy mennyiségű (petabájt vagy akár több exabájt) adatairól. A HDInsight több gépi tanulási lehetőség is létezik: a SparkML és a Apache Spark MLlib, az R, a Apache Hive és a Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML és MLlib

A [HDInsight Spark](spark/apache-spark-overview.md) egy [Apache Spark](https://spark.apache.org/), egy egységes, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszer, amely támogatja a memóriabeli feldolgozást a Big Data Analitika fellendítéséhez. A Spark-feldolgozó motor a sebességre, a könnyű használatra és a kifinomult elemzésekre épül. A Spark memóriában elosztott számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban használt iterációs algoritmusok számára. Két skálázható gépi tanulási kódtár használható a következő elosztott környezethez: MLlib és SparkML. A MLlib a RDD-re épülő eredeti API-t tartalmazza. A SparkML egy újabb csomag, amely egy magasabb szintű API-t biztosít, amely a DataFrames-re épül a ML-folyamatok létrehozásához. A SparkML még nem támogatja a MLlib összes funkcióját, de a MLlib a Spark szabványos Machine learning-könyvtáraként váltja fel.

A Apache Spark Microsoft Machine Learning könyvtára [MMLSpark](https://github.com/Azure/mmlspark). Ez a könyvtár úgy lett kialakítva, hogy a Sparknál termelékenyebb adatszakértőket hozzon létre, növelje a kísérletezés mértékét, és kihasználja az élvonalbeli gépi tanulási technikákat, beleértve a mély tanulást, nagyon nagy adatkészleteken. A MMLSpark méretezhető ML-modellek, például indexelési karakterláncok, a gépi tanulási algoritmusok által várt elrendezésre, valamint a szolgáltatás-vektorok összeállítására szolgáló réteget biztosít a SparkML alacsony szintű API-jai felett. A MMLSpark könyvtár leegyszerűsíti ezeket és egyéb gyakori feladatokat a PySpark-modellek létrehozásához.

## <a name="r"></a>R

Az [R](https://www.r-project.org/) jelenleg a legnépszerűbb statisztikai programozási nyelv a világon. Ez egy nyílt forráskódú adatvizualizációs eszköz, amelynek közössége több mint 2 500 000 felhasználóval rendelkezik, és egyre növekszik. A virágzó felhasználói bázisával és a több mint 8 000 közreműködő csomaggal az R valószínűleg számos olyan vállalat számára valószínű választás, akiknek gépi tanulásra van szükségük. Létrehozhat egy HDInsight-fürtöt ML-szolgáltatásokkal, amelyek készen állnak a nagy adatkészletekkel és modellekkel való használatra. Ez a képesség olyan jól ismert R-felületet biztosít az adatszakértők és a statisztikusok számára, amely igény szerint méretezhető a HDInsight keresztül, a fürt beállítása és karbantartása nélkül.

![Az R Serverrel való előrejelzés betanítása](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

A fürt peremhálózati csomópontja kényelmes helyet biztosít a fürthöz való kapcsolódáshoz és az R-parancsfájlok futtatásához.  Az R-szkripteket a fürt csomópontjain is futtathatja, ha a skálázás Hadoop-térképével csökkenti vagy Spark számítási környezeteket használ.

A Spark-nal rendelkező HDInsight ML-szolgáltatásaival a Spark számítási környezettel integrálással képzéseket biztosíthat a fürtök csomópontjain. Az R-szkripteket közvetlenül a peremhálózati csomóponton is futtathatja, ha az összes rendelkezésre álló magot szükség szerint párhuzamosan használja. Másik lehetőségként futtathatja a kódot a peremhálózati csomópontból a fürt összes csomópontján elosztott feldolgozás elindításához. A Sparktal rendelkező HDInsight ML-szolgáltatások a nyílt forráskódú R-csomagok tetszés funkcióit is lehetővé teszik, ha szükséges.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning és Apache Hive

A Azure Machine Learning eszközöket biztosít a prediktív elemzések modellezéséhez, valamint egy teljes körűen felügyelt szolgáltatáshoz, amellyel a prediktív modelleket használatra kész webszolgáltatásként helyezheti üzembe. A Azure Machine Learning egy teljes körű prediktív elemzési megoldás a felhőben, amely a prediktív modellek létrehozásához, teszteléséhez, működővé tenni és kezeléséhez használható. Válasszon egy nagyméretű algoritmus-függvénytárból, használjon webalapú stúdiót modellek létrehozásához, és egyszerűen üzembe helyezheti a modellt webszolgáltatásként.

![Microsoft Azure Machine learning – áttekintés](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

HDInsight Hadoop-fürtben található adatszolgáltatások létrehozása a [kaptár-lekérdezések](../machine-learning/team-data-science-process/create-features-hive.md)használatával. A szolgáltatások *mérnöki* célja, hogy a tanulási algoritmusok által létrehozott nyers adatokból származó funkciók létrehozásával növelje a tanulási algoritmusok prediktív erejét. HiveQL-lekérdezéseket futtathat Azure Machine Learning Studio (klasszikus), és a kaptárban feldolgozott és a blob Storage-ban tárolt adatokhoz férhet hozzá az [adatok importálása modul](../machine-learning/classic/import-data.md)használatával.

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

A [Deep learning](https://www.microsoft.com/en-us/research/group/dltc/) a gépi tanulás olyan ága, amely neurális hálózatokat használ, az emberi agy biológiai folyamatai által inspirálva. Számos kutató ígéretes megközelítésként látja el a mesterséges intelligenciát. A Deep learning példái közé tartoznak a beszélő nyelvi fordítók, a képfelismerési rendszerek és a gépi indoklások.

A Microsoft az ingyenes, könnyen használható, nyílt forráskódú [Microsoft Cognitive Toolkitt](https://www.microsoft.com/en-us/cognitive-toolkit/)fejlesztette ki, hogy elősegítse a munkát a mélyreható tanulásban. Ezt az eszközkészletet számos Microsoft-termék, a vállalatok világszerte használják, és a nagy léptékű tanulás, valamint a legújabb algoritmusok és technikák érdeklik.

## <a name="see-also"></a>Lásd még

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Filmkészítési javaslatok készítése az Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive és Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive és Azure Machine Learning végpontok közötti](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Gépi tanulás a Apache Spark on HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Deep learning-erőforrások

* [Microsoft Cognitive Toolkit Deep learning-modell használata Azure HDInsight Spark-fürttel](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Mély tanulási és AI-keretrendszerek a Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
