---
title: Analitika a HDInsight Sparkban a PySpark, a Scala-Team adatelemzési folyamattal
description: Példák a csoportos adatelemzési folyamatra, amely végigvezeti a PySpark és a Scala egy Azure HDInsight Sparkon való használatával.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1662af6cd3499fdf851d4e1bd8a0db48da7635b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320129"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark-adatelemzési útmutató a PySpark és a Scala használatával az Azure-ban

Ezek a forgatókönyvek a PySpark és a Scala használatával egy Azure Spark-fürtön végzik a prediktív elemzést. Ezek követik a csoportos adatelemzési folyamat lépéseit. A csoportos adatelemzési folyamat áttekintését lásd: [adatelemzési folyamat](overview.md). A Spark on HDInsight áttekintését lásd: a [Spark on HDInsight bemutatása](../../hdinsight/spark/apache-spark-overview.md).

A csoportos adatelemzési folyamatokat végrehajtó további adatelemzési útmutatókat az általuk használt **platform** szerint csoportosítjuk. A példák részletezés tekintse meg [a csoportos adatelemzési folyamat végrehajtásához szükséges útmutatókat](walkthroughs.md) .

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>A taxi tippek előrejelzése az Azure Spark PySpark használatával

A New York-i taxi-adatforgalom használatával a [Spark on Azure HDInsight](spark-overview.md) útmutatóban megjósolható, hogy a tipp fizetős-e, és hogy a várt mennyiségek milyen tartományban vannak. Ez a példa a csapat adatelemzési folyamatát használja egy [Azure HDInsight Spark-fürtöt](https://azure.microsoft.com/services/hdinsight/) használó forgatókönyvben a nyilvánosan elérhető NYC taxi Trip és fare adatkészletből származó mérnöki adatok tárolására, feltárására és szolgáltatására. Ez az áttekintő témakör egy HDInsight Spark-fürtöt és Jupyter PySpark-jegyzetfüzeteket használ. Ezek a jegyzetfüzetek megmutatják, hogyan vizsgálhatja meg az adatait, és hogyan hozhat létre és használhat modelleket. A speciális adatfeltárási és-modellezési jegyzetfüzet azt mutatja be, hogyan lehet a több érvényesítést, a Hyper-paraméter-eltakarítást és a modell kiértékelését.

### <a name="data-exploration-and-modeling-with-spark"></a>Adatelemzés és-modellezés a Sparktal 
Fedezze fel az adatkészletet, és hozza létre, szerzi és értékelje ki a gépi tanulási modelleket a Spark MLlib Toolkit című témakörben található [bináris besorolási és regressziós modellek létrehozásával](spark-data-exploration-modeling.md) .

### <a name="model-consumption"></a>Modell felhasználása
A jelen témakörben létrehozott besorolási és regressziós modellek kiértékelésével kapcsolatban lásd a [Spark által készített gépi tanulási modellek pontszámát és értékelését](spark-model-consumption.md)ismertető témakört.

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Több ellenőrzés és hiperparaméter elsöprő
Tekintse meg a [fejlett adatfeltárást és-modellezést a sparktal](spark-advanced-data-exploration-modeling.md) , hogy a modelleket hogyan lehet betanítani a több ellenőrzési és a Hyper-paraméteres söpörte használatával.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Taxi tippek előrejelzése a Scala használatával az Azure Sparkban

A [Scala és a Spark on Azure bemutató használata](scala-walkthrough.md) azt jelzi, hogy a tipp fizetős-e, és a várhatóan fizetendő összegek köre. Azt mutatja be, hogyan használható a Scala a felügyelt gépi tanulási feladatokhoz a Spark Machine learning Library (MLlib) és a SparkML-csomagok használatával egy Azure HDInsight Spark-fürtön. Végigvezeti az [adatelemzési folyamatot](./index.yml)alkotó feladatokon: az adatok betöltését és feltárását, a vizualizációt, a szolgáltatások mérnöki használatát, a modellezést és a modell felhasználását. A modellek a következők: logisztikai és lineáris regresszió, véletlenszerű erdők és átmenetes kiemeléses fák.


## <a name="next-steps"></a>Következő lépések

A csoportos adatelemzési folyamat áttekintését lásd: a [csoportos adatelemzési folyamat áttekintése](overview.md).

A csoportos adatelemzési folyamat életciklusának megvitatására a [csoportos adatelemzési folyamat életciklusa](lifecycle.md)című témakörben olvashat. Ez az életciklus az elejétől a végéig terjedő lépéseket ismerteti, amelyeket a projektek általában a végrehajtásuk után követnek.