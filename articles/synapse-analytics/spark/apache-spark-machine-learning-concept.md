---
title: Gépi tanulás az Apache Sparkkal
description: Ez a cikk az Azure szinapszis Analytics Apache Spark által elérhető gépi tanulási és adatelemzési képességek fogalmi áttekintését tartalmazza.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 11/13/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 0485f697b9360b0f2dfe94fdf07629978b5127c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98116925"
---
# <a name="machine-learning-with-apache-spark"></a>Gépi tanulás Apache Spark

Az Azure szinapszis Analytics szolgáltatásban Apache Spark lehetővé teszi a gépi tanulást a big data, így értékes információkhoz juthat a nagy mennyiségű strukturált, strukturálatlan és gyorsan változó adatról. 

Ez a szakasz a gépi tanulási munkafolyamatok áttekintését és oktatóanyagokat tartalmaz, többek között a felderítő adatelemzést, a funkciók tervezését, a modellek betanítását, a modell pontozását és a telepítést  

## <a name="synapse-runtime"></a>Szinapszis-futtatókörnyezet 
A szinapszis futtatókörnyezet az adatelemzéshez és a gépi tanuláshoz optimalizált, kurátori környezet. A szinapszis Runtime számos népszerű nyílt forráskódú kódtárat kínál, és alapértelmezés szerint a Azure Machine Learning SDK-ban épít. A szinapszis futtatókörnyezet számos külső kódtárat is tartalmaz, beleértve a PyTorch, a Scikit, a XGBoost és egyebeket.

Az elérhető kódtárak és a kapcsolódó verziók megismeréséhez tekintse meg a közzétett [Azure szinapszis Analytics futtatókörnyezetet](../spark/apache-spark-version-support.md).

## <a name="exploratory-data-analysis"></a>Feltáró adatelemzés
Az Azure szinapszis Analytics Apache Spark használata során számos beépített lehetőség áll rendelkezésre, amelyek segítségével megjelenítheti az adatait, beleértve a szinapszis jegyzetfüzet-diagramok beállításait, a népszerű nyílt forráskódú könyvtárakhoz való hozzáférést, például a Seaborn és a Matplotlib, valamint a szinapszis SQL és a Power BI integrálását.

Az adatvizualizációval és az adatelemzési lehetőségekkel kapcsolatos további információkért tekintse meg az [Azure szinapszis notebookok használatával történő adatmegjelenítést](../spark/apache-spark-data-visualization.md)ismertető cikket.

## <a name="feature-engineering"></a>Jellemzőkiemelés
Alapértelmezés szerint a szinapszis-futtatókörnyezet olyan kódtárakat tartalmaz, amelyeket általában a szolgáltatások fejlesztéséhez használnak. Nagyméretű adatkészletek esetében a Spark SQL, a MLlib és a koalas funkciót használhatja a funkciók mérnöki felépítéséhez. A kisebb adatkészletek esetében a külső gyártók (például a NumPy, a pandák és a Scikit) is hasznos módszereket biztosítanak ezekhez a forgatókönyvekhez.

## <a name="train-models"></a>Modellek betanítása
Több lehetőség is rendelkezésre áll, ha a gépi tanulási modellek Azure-beli Spark használatával történő betanítása az Azure szinapszis Analyticsben: Apache Spark MLlib, Azure Machine Learning és számos más nyílt forráskódú kódtár. 

További információ a gépi tanulási képességekről: [modellek betanítása az Azure szinapszis Analyticsben](../spark/apache-spark-machine-learning-training.md)című cikkből.

### <a name="sparkml-and-mllib"></a>SparkML és MLlib
A Spark memóriában elosztott számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban használt iterációs algoritmusok számára. ```spark.ml``` egységes, magas szintű API-kat biztosít, amelyek segítségével a felhasználók gépi tanulási folyamatokat hozhatnak létre és hangolnak be. További információ ```spark.ml``` a [Apache Spark ml programozási útmutatóban](https://spark.apache.org/docs/1.2.2/ml-guide.html)olvasható.

### <a name="azure-machine-learning-automated-ml"></a>Automatizált ML Azure Machine Learning
A [Azure Machine learning AUTOMATIZÁLT ml](../../machine-learning/concept-automated-ml.md) (automatizált gépi tanulás) segít automatizálni a gépi tanulási modellek fejlesztésének folyamatát. Lehetővé teszi az adatszakértők, elemzők és fejlesztők számára, hogy a modell minőségének fenntartása mellett nagy mennyiségű, hatékonyságú és termelékenységű ML-modellt építsenek. A Azure Machine Learning automatizált ML SDK-t futtató összetevők közvetlenül a szinapszis futtatókörnyezetbe vannak építve.

### <a name="open-source-libraries"></a>Nyílt forráskódú kódtárak
Az Azure szinapszis Analytics minden Apache Spark készlete előre betöltött és népszerű gépi tanulási kódtárakat tartalmaz.  Az alapértelmezés szerint a kapcsolódó Machine learning-kódtárak közé tartoznak a következők:

- [Scikit – Ismerje meg](https://scikit-learn.org/stable/index.html) az egyik legnépszerűbb, egycsomópontos gépi tanulási kódtárat a klasszikus ml-algoritmusokhoz. Scikit – Ismerje meg a felügyelt és a nem felügyelt tanulási algoritmusok többségét, és az adatbányászat és az adatelemzés céljából is használható.
  
- A [XGBoost](https://xgboost.readthedocs.io/en/latest/) egy népszerű gépi tanulási könyvtár, amely optimalizált algoritmusokat tartalmaz a döntési fák és a véletlenszerű erdők képzéséhez. 
  
- [PyTorch](https://pytorch.org/)  &  A [Tensorflow](https://www.tensorflow.org/) hatékony Python Deep learning-kódtárak. Az Azure szinapszis Analytics Apache Spark készletén belül ezeket a kódtárakat a készletben lévő végrehajtók számának a nulla értékre állításával hozhatja létre. Annak ellenére, hogy Apache Spark ebben a konfigurációban nem működik, ez egy egyszerű és költséghatékony módszer az egylapos modellek létrehozásához.

## <a name="track-model-development"></a>Modellfejlesztés nyomon követése
A [MLFlow](https://www.mlflow.org/) egy nyílt forráskódú kódtár a gépi tanulási kísérletek életciklusának kezeléséhez. A MLFlow Tracking a MLflow egy olyan összetevője, amely naplózza és nyomon követi a képzések futtatási metrikáit és a modelleket. Ha többet szeretne megtudni arról, hogyan használhatja a MLFlow követését az Azure szinapszis Analytics és a Azure Machine Learning használatával, tekintse meg ezt az oktatóanyagot a [MLFlow használatáról](../../machine-learning/how-to-use-mlflow.md).

## <a name="model-scoring"></a>Modell pontozása
A modell pontozása vagy következtetése az a fázis, ahol a modell az előrejelzések készítésére szolgál. A SparkML vagy MLLib használatával történő modellek esetében kihasználhatja a natív Spark-metódusokat, hogy közvetlenül egy Spark-DataFrame végezzen következtetni. Más nyílt forráskódú kódtárak és modellek esetében létrehozhat egy Spark UDF-t is a nagyméretű adatkészletek kiskálázásához. Kisebb adatkészletek esetében a könyvtár által biztosított natív modellen alapuló következtetési módszereket is használhatja.

## <a name="register-and-serve-models"></a>Modellek regisztrálása és kiszolgálása
A modell regisztrálása lehetővé teszi a munkaterületen lévő modellekhez tartozó metaadatok tárolását, verzióját és nyomon követését. A modell képzésének befejezése után a modell regisztrálható a [Azure Machine learning modell beállításjegyzékében](../../machine-learning/concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere). A regisztrálást követően a ONNX-modellekkel a dedikált SQL-készletekben tárolt [adat](../machine-learning/tutorial-sql-pool-model-scoring-wizard.md) is bővíthető.

## <a name="next-steps"></a>Következő lépések
Az Azure szinapszis Analyticsben a gépi tanulás használatának megkezdéséhez tekintse meg az alábbi oktatóanyagokat:
- [Az adatelemzés az Azure szinapszis notebookokkal](../spark/apache-spark-data-visualization-tutorial.md)

- [Gépi tanulási modell betanítása automatizált ML-vel](../spark/apache-spark-azure-machine-learning-tutorial.md)

- [Gépi tanulási modell betanítása Apache Spark MLlib](../spark/apache-spark-machine-learning-mllib-notebook.md)
