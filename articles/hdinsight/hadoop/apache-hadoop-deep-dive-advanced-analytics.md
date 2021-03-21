---
title: Deep Dive – fejlett Analitika – Azure HDInsight
description: Ismerje meg, hogy a fejlett elemzési algoritmusok hogyan használják a big data Azure HDInsight való feldolgozását.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 4b57eddafbf9a5615dc42e9a3c5a49c5f90781e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946663"
---
# <a name="deep-dive---advanced-analytics"></a>Deep Dive – fejlett Analitika

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Mi a HDInsight fejlett elemzési funkciója?

A HDInsight lehetővé teszi értékes információk beszerzését nagy mennyiségű strukturált, strukturálatlan és gyorsan mozgó adatból. A speciális elemzések kiválóan méretezhető architektúrák, statisztikai és gépi tanulási modellek, valamint intelligens irányítópultok használatával biztosítanak értelmes elemzéseket. A gépi tanulás vagy a *prediktív elemzés* az adatokban található kapcsolatok azonosítására és megismerésére szolgáló algoritmusokat használ az előrejelzések készítéséhez és a döntések elsajátításához.

## <a name="advanced-analytics-process"></a>Fejlett elemzési folyamat

![Fejlett elemzési folyamat](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Miután azonosította az üzleti problémát, és megkezdte az adatok gyűjtését és feldolgozását, létre kell hoznia egy modellt, amely az előre jelezni kívánt kérdést jelöli. A modell egy vagy több gépi tanulási algoritmust használ az üzleti igényeknek leginkább megfelelő előrejelzési típus elvégzéséhez.  Az adatok többségét a modell betanítására kell használni, a többit pedig tesztelésre vagy kiértékelésre használják.

A modell létrehozását, betöltését, tesztelését és értékelését követően a következő lépés a modell üzembe helyezése, hogy megkezdje a válaszok megadását a kérdéseire. Az utolsó lépés a modell teljesítményének figyelése és szükség szerinti finomhangolása.

## <a name="common-types-of-algorithms"></a>Az algoritmusok gyakori típusai

A speciális elemzési megoldások gépi tanulási algoritmusokat biztosítanak. Itt látható az algoritmusok és a kapcsolódó gyakori üzleti használati esetek kategóriáinak összefoglalása.

![Machine Learning kategória összefoglalói](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

A legjobban illeszkedő algoritmus (ok) kiválasztásával együtt kell megfontolnia, hogy szükséges-e az adatképzésben. A gépi tanulási algoritmusok a következőképpen vannak kategorizálva:

* Felügyelt – az algoritmust a címkével ellátott adathalmazon kell tanítani, mielőtt az eredményeket megadhatja
* A részben felügyelt algoritmusok további célokból is kihasználhatók az oktató által végzett interaktív lekérdezéseken keresztül, amelyek a képzés kezdeti szakaszában nem voltak elérhetők.
* Nem felügyelt algoritmus – nem szükséges betanítási adat
* Megerősítés – az algoritmus a szoftver-ügynökök használatával határozza meg az ideális viselkedést egy adott kontextuson belül (gyakran a robotika esetében használatos)

| Algoritmus kategóriája| Használat | Tanulás típusa | Algoritmusok |
| --- | --- | --- | -- |
| Osztályozás | Személyek vagy dolgok csoportokba való besorolása | Felügyelt | Döntési fák, logisztikai regresszió, neurális hálózatok |
| Fürtözés | Példák halmazának elosztása homogén csoportokba | Unsupervised | K – fürtözés |
| Mintázat észlelése | Az adatkezelés gyakori hozzárendeléseinek azonosítása | Unsupervised | Társítási szabályok |
| Regresszió | Numerikus eredmények előrejelzése | Felügyelt | Lineáris regresszió, neurális hálózatok |
| Megerősítés | A robotok optimális viselkedésének meghatározása | Megerősítés | Monte Carlo-szimulációk, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Gépi tanulás a HDInsight-on

A HDInsight számos gépi tanulási lehetőséggel rendelkezik a fejlett elemzési munkafolyamatokhoz:

* Machine Learning és Apache Spark
* R és ML szolgáltatások
* Azure Machine Learning és Apache Hive
* Apache Spark és mély tanulás

### <a name="machine-learning-and-apache-spark"></a>Machine Learning és Apache Spark

A [HDInsight Spark](../spark/apache-spark-overview.md) egy [Apache Spark](https://spark.apache.org/), egy egységes, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszer, amely memórián belüli feldolgozást használ a Big adatelemzéshez. A Spark-feldolgozó motor a sebességre, a könnyű használatra és a kifinomult elemzésekre épül. A Spark memóriában elosztott számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban használt iterációs algoritmusok számára.

Három skálázható gépi tanulási kódtár van, amelyek a következő elosztott környezethez biztosítanak algoritmikus modellezési képességeket:

* A [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) -MLlib a Spark RDD-ra épülő eredeti API-t tartalmazza.
* A [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) -SparkML egy újabb csomag, amely egy magasabb szintű API-t biztosít a Spark DataFrames, amely ml-folyamatok létrehozását teszi lehetővé.
* [**MMLSpark**](https://github.com/Azure/mmlspark)  – a (z) Apache Spark Microsoft Machine learning könyvtára (MMLSpark) úgy lett kialakítva, hogy az adatszakértőket hatékonyabbá tegye a Sparkban, hogy növelje a kísérletezés mértékét, és kihasználja az élvonalbeli gépi tanulási technikákat, beleértve a mély tanulást a nagy adathalmazokon. A MMLSpark könyvtár leegyszerűsíti az általános modellezési feladatokat a PySpark-modellek létrehozásához.

### <a name="r-and-ml-services"></a>R és ML szolgáltatások

A HDInsight részeként létrehozhat egy HDInsight-fürtöt ml- [szolgáltatásokkal](../r-server/r-server-overview.md) , amelyek készen állnak a nagy adatkészletekkel és modellekkel való használatra. Ez az új képesség adatszakértőket és statisztikusokat biztosít olyan jól ismert R felülettel, amely igény szerint méretezhető az HDInsight-on keresztül, a fürt beállítása és karbantartása nélkül.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning és Apache Hive

A [Azure Machine learning Studio (klasszikus)](https://studio.azureml.net/) eszközöket biztosít a prediktív elemzések modellezéséhez, valamint egy teljes körűen felügyelt szolgáltatáshoz, amellyel a prediktív modelleket használatra kész webszolgáltatásként helyezheti üzembe. A Azure Machine Learning eszközöket biztosít a felhőben a teljes prediktív elemzési megoldások létrehozásához, teszteléséhez, működővé tenni és a prediktív modellek kezeléséhez. Válasszon egy nagyméretű algoritmus-függvénytárból, használjon webalapú stúdiót modellek létrehozásához, és egyszerűen üzembe helyezheti a modellt webszolgáltatásként.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark és mély tanulás

A [Deep learning](https://www.microsoft.com/research/group/dltc/) a gépi tanulás olyan ága, amely az emberi agy biológiai folyamatai által inspirált *mély neurális hálózatokat* (DNN) használ. Számos kutató a mesterséges intelligencia ígéretes megközelítésének tekinti a mélyreható tanulást. Néhány példa a mély tanulásra: beszélt nyelvi fordítók, képfelismerési rendszerek és gépi indoklás. A Microsoft az ingyenes, könnyen használható, nyílt forráskódú [Microsoft Cognitive Toolkitt](https://www.microsoft.com/en-us/cognitive-toolkit/)fejlesztette ki, hogy elősegítse a munkát a mélyreható tanulásban. Az eszközkészlet széles körben használatos számos Microsoft-termék, a világszerte működő vállalatok számára, hogy nagy mennyiségű, nagy léptékű tanulást és a legújabb algoritmusokat és technikákat érdeklik.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Forgatókönyv – képek a városi fejlesztési minták azonosításához

Tekintsük át a fejlett analitikai gépi tanulási folyamat példáját a HDInsight használatával.

Ebben a forgatókönyvben láthatja, hogy a Microsoft Cognitive Toolkit (CNTK) által létrehozott DNN hogyan használható az Azure Blob Storage-fiókban tárolt nagy képgyűjtemények pontozására egy HDInsight Spark-fürtön a PySpark használatával. Ezt a megközelítést egy közös DNN-használati esetre, a légi rendszerképek besorolására alkalmazza, és felhasználható a közelmúltbeli mintázatok azonosítására a városi fejlesztésekben.  Egy előre betanított rendszerkép-besorolási modellt fog használni. A modell előre betanítva van a [CIFAR-10 adatkészleten](https://www.cs.toronto.edu/~kriz/cifar.html) , és 10 000 visszatartott lemezképre lett alkalmazva.

Ebben a fejlett elemzési forgatókönyvben három kulcsfontosságú feladat van:

1. Hozzon létre egy Azure HDInsight Hadoop fürtöt egy Apache Spark 2.1.0-eloszlással.
2. Egyéni parancsfájl futtatásával Microsoft Cognitive Toolkit telepíthet egy Azure HDInsight Spark-fürt összes csomópontjára.
3. A Spark Python API (PySpark) használatával feltölthet egy előre összeállított Jupyter Notebookt a HDInsight Spark-fürthöz, amely egy betanított Microsoft Cognitive Toolkit mély tanulási modellt alkalmaz egy Azure Blob Storage-fiókban lévő fájlokra.

Ez a példa a CIFAR-10 képkészletet használja, amelyet Alex Krizhevsky, vino Siro és Geoffrey Hinton lefordított és elosztott. A CIFAR-10 adatkészlet 60 000 32 × 32 színes képet tartalmaz, amelyek a 10 egymást kölcsönösen kizáró osztályhoz tartozóak:

![Machine Learning képek](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Az adatkészlettel kapcsolatos további információkért tekintse meg az Alex Krizhevsky [learning több rétegét a Tiny images szolgáltatásban](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf).

Az adatkészlet 50 000 rendszerképekből és 10 000-es rendszerképekből álló tesztelési csoportba lett particionálva. Az első készlet egy húsz rétegű, nagy teljesítményű (ResNet) modell betanítására szolgál a Microsoft Cognitive Toolkit használatával a Cognitive Toolkit GitHub-tárházból származó [oktatóanyagot](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) követve. A fennmaradó 10 000-es kép a modell pontosságának tesztelésére szolgál. Ebben az esetben az elosztott számítástechnika játékba kerül: az előzetes feldolgozás és a képek pontozásának feladata nagyon párhuzamosítható. A mentett betanított modellel együtt a következőket használtuk:

* PySpark a lemezképek és a betanított modell terjesztését a fürt munkavégző csomópontjaira.
* Python a HDInsight Spark-fürt minden csomópontján lévő lemezképek előzetes feldolgozásához.
* Cognitive Toolkit a modell betöltéséhez és az előre feldolgozott lemezképek kiértékeléséhez az egyes csomópontokon.
* A Jupyter a PySpark-szkript futtatásához, az eredmények összesítéséhez és a [Matplotlib](https://matplotlib.org/) használatával jeleníti meg a modell teljesítményét.

Az 10 000-es lemezképek teljes előfeldolgozása/pontozása kevesebb mint egy percet vesz igénybe egy 4 munkavégző csomóponttal rendelkező fürtön. A modell pontosan megjósolja a ~ 9 100 (91%) címkéit képek. A zűrzavaros mátrix a leggyakoribb besorolási hibákat mutatja be. A mátrix például azt mutatja, hogy a kutyák macskákként való címkézése és fordítva gyakrabban történik, mint a többi címke pároknál.

![Machine Learning eredmények diagramja](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Próbálja ki!

A megoldás teljes körű megvalósításához kövesse [az oktatóanyagot](../spark/apache-spark-microsoft-cognitive-toolkit.md) : hozzon létre egy HDInsight Spark-fürtöt, telepítse a Cognitive Toolkitt, és futtassa a 10 000-es SZÁMú CIFAR-rendszerképekből álló Jupyter notebook.

## <a name="next-steps"></a>Következő lépések

Apache Hive és Azure Machine Learning

* [Apache Hive és Azure Machine Learning végpontok közötti](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Azure HDInsight Hadoop-fürt használata 1 TB-os adatkészleten](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark és MLLib

* [Gépi tanulás a Apache Spark on HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark a Machine Learning használatával: Apache Spark használata a HDInsight az építési hőmérséklet elemzéséhez a HVAC-adatok használatával](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: a HDInsight Apache Spark használata az élelmiszer-ellenőrzés eredményeinek előrejelzéséhez](../spark/apache-spark-machine-learning-mllib-ipython.md)

Mély tanulás, Cognitive Toolkit és egyebek

* [Adatelemzési Azure-beli virtuális gép](../../machine-learning/data-science-virtual-machine/overview.md)
* [A H2O.ai bemutatása az Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
