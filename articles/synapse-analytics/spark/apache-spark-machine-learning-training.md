---
title: Gépi tanulási modellek betanítása Apache Spark
description: Az Azure szinapszis Analytics Apache Spark használata a gépi tanulási modellek betanításához
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 09/13/2020
ms.openlocfilehash: 56b9a98eb72b375aacfeb7cb147997028d3d9ba7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116806"
---
# <a name="train-machine-learning-models"></a>Gépi tanulási modellek betanítása
Az Azure szinapszis Analytics szolgáltatásban Apache Spark lehetővé teszi a gépi tanulást a big data, így értékes információkhoz juthat a nagy mennyiségű strukturált, strukturálatlan és gyorsan változó adatról. Több lehetőség is rendelkezésre áll, ha a gépi tanulási modellek Azure-beli Spark használatával történő betanítása az Azure szinapszis Analyticsben: Apache Spark MLlib, Azure Machine Learning és számos más nyílt forráskódú kódtár. 

## <a name="apache-sparkml-and-mllib"></a>Apache SparkML és MLlib
Az Azure szinapszis Analytics Apache Spark a Microsoft egyike a Felhőbeli Apache Spark megvalósításának. Egységes, nyílt forráskódú, párhuzamos adatfeldolgozási keretrendszert biztosít a memóriában történő feldolgozáshoz a big data Analitika növelése érdekében. A Spark-feldolgozó motor a sebességre, a könnyű használatra és a kifinomult elemzésekre épül. A Spark memóriában elosztott számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban használt iterációs algoritmusok számára. 

Két skálázható gépi tanulási kódtár használható a következő elosztott környezethez: MLlib és SparkML. A MLlib a RDD-re épülő eredeti API-t tartalmazza. A SparkML egy újabb csomag, amely egy magasabb szintű API-t biztosít, amely a DataFrames-re épül a ML-folyamatok létrehozásához. A SparkML még nem támogatja a MLlib összes funkcióját, de a MLlib a Spark szabványos Machine learning-könyvtáraként váltja fel.

> [!NOTE]
> 
> A SparkML-modellek létrehozásával kapcsolatos további információkért tekintse meg ezt az [oktatóanyagot](../spark/apache-spark-azure-machine-learning-tutorial.md).

## <a name="popular-libraries"></a>Népszerű kódtárak
Az Azure szinapszis Analytics minden Apache Spark készlete előre betöltött és népszerű gépi tanulási kódtárakat tartalmaz. Ezek a kódtárak újrafelhasználható programkódot biztosítanak, amelyet a programokba vagy projektekben érdemes felvenni. Az alapértelmezés szerint a kapcsolódó Machine learning-kódtárak közé tartoznak a következők:
- [Scikit – Ismerje meg](https://scikit-learn.org/stable/index.html) az egyik legnépszerűbb, egycsomópontos gépi tanulási kódtárat a klasszikus ml-algoritmusokhoz. Scikit – Ismerje meg a felügyelt és a nem felügyelt tanulási algoritmusok többségét, és az adatbányászat és az adatelemzés céljából is használható.
  
- A [XGBoost](https://xgboost.readthedocs.io/en/latest/) egy népszerű gépi tanulási könyvtár, amely optimalizált algoritmusokat tartalmaz a döntési fák és a véletlenszerű erdők képzéséhez. 
  
- [PyTorch](https://pytorch.org/)  &  A [Tensorflow](https://www.tensorflow.org/) hatékony Python Deep learning-kódtárak. Az Azure szinapszis Analytics Apache Spark készletén belül ezeket a kódtárakat a készletben lévő végrehajtók számának a nulla értékre állításával hozhatja létre. Annak ellenére, hogy Apache Spark ebben a konfigurációban nem működik, ez egy egyszerű és költséghatékony módszer az egylapos modellek létrehozásához.

Az elérhető kódtárak és a kapcsolódó verziók megismeréséhez tekintse meg a közzétett [Azure szinapszis Analytics futtatókörnyezetet](../spark/apache-spark-version-support.md).

## <a name="mmlspark"></a>MMLSpark
A Apache Spark Microsoft Machine Learning könyvtára [MMLSpark](https://github.com/Azure/mmlspark). Ez a könyvtár úgy lett kialakítva, hogy a Sparknál termelékenyebb adatszakértőket hozzon létre, növelje a kísérletezés mértékét, és kihasználja a legmodernebb gépi tanulási technikákat, beleértve a mély tanulást a nagyméretű adatkészleteken. 

A MMLSpark méretezhető ML-modellek, például indexelési karakterláncok, a gépi tanulási algoritmusok által várt elrendezésre, valamint a szolgáltatás-vektorok összeállítására szolgáló réteget biztosít a SparkML alacsony szintű API-jai felett. A MMLSpark könyvtár leegyszerűsíti ezeket és egyéb gyakori feladatokat a PySpark-modellek létrehozásához.

## <a name="automated-ml-in-azure-machine-learning"></a>Automatizált ML Azure Machine Learning 
A Azure Machine Learning egy felhőalapú környezet, amely lehetővé teszi a gépi tanulási modellek betanítását, üzembe helyezését, automatizálását, kezelését és nyomon követését. Az automatikus ML Azure Machine Learning fogadja az oktatási és konfigurációs beállításokat, és automatikusan megismétli a különböző funkciók normalizálása/szabványosítási módszereinek, modelljeinek és hiperparaméter beállításainak kombinációját, hogy a legjobb modellre jusson. 

Ha az Azure szinapszis Analyticsben az automatikus ML-t használja, kihasználhatja a különböző szolgáltatások közötti mély integrációt, hogy egyszerűbbé váljon a hitelesítés & a modell betanítása. 

> [!NOTE]
> 
> A Azure Machine Learning automatizált ML-kísérletek létrehozásával kapcsolatos további információkért tekintse meg ezt az [oktatóanyagot](./spark/../apache-spark-azure-machine-learning-tutorial.md).

## <a name="azure-cognitive-services"></a>Azure Cognitive Services
Az [Azure Cognitive Services](../../cognitive-services/what-are-cognitive-services.md) gépi tanulási funkciókat biztosít az olyan általános problémák megoldásához, mint például a szöveg elemzése az érzelmi hangulathoz, illetve a képek elemzése az objektumok vagy arcok felismeréséhez. Ezen szolgáltatások használatához nincs szükség különleges gépi tanulási vagy adatelemzési ismeretekre. A kognitív szolgáltatás egy gépi tanulási megoldás részét képező alkatrészt vagy az összes összetevőt biztosítja: az adatmennyiséget, az algoritmust és a betanított modellt. Ezeknek a szolgáltatásoknak a célja, hogy általános ismereteket kérjenek az adatairól anélkül, hogy a gépi tanulással vagy az adatelemzéssel kellene foglalkoznia. Ezeket az előre betanított Cognitive Services automatikusan kihasználhatja az Azure szinapszis Analytics szolgáltatáson belül.

## <a name="next-steps"></a>Következő lépések
Ez a cikk áttekintést nyújt a gépi tanulási modellek Apache Spark-készletekben az Azure szinapszis Analyticsben való tanításának különböző lehetőségeiről. A modellek betanításával kapcsolatos további információkért tekintse meg az alábbi oktatóanyagot:

- Automatizált ML-kísérletek futtatása a Azure Machine Learning és az Azure szinapszis Analytics használatával: [AUTOMATIZÁLT ml-oktatóanyag](../spark/apache-spark-azure-machine-learning-tutorial.md) 
- SparkML-kísérletek futtatása: [Apache SparkML oktatóanyag](../spark/apache-spark-machine-learning-mllib-notebook.md)
- Az alapértelmezett könyvtárak megtekintése: az [Azure szinapszis Analytics futtatókörnyezete](../spark/apache-spark-version-support.md)