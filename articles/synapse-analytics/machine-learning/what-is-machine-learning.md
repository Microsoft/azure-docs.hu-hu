---
title: Machine Learning az Azure szinapszis Analyticsben
description: Az Azure szinapszis Analytics gépi tanulási képességeinek áttekintése.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 68b113de63cfefde805c1c46e9303829c4eb33a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222139"
---
# <a name="machine-learning-capabilities-in-azure-synapse-analytics"></a>Az Azure Synapse Analytics gépi tanulási képességei

Az Azure szinapszis Analytics különböző gépi tanulási funkciókat kínál. Ez a cikk áttekintést nyújt arról, hogyan alkalmazhat Machine Learning az Azure szinapszis környezetében.

Ez az Áttekintés az adatelemzési folyamat perspektívájában a gépi tanulással kapcsolatos különböző képességeket ismerteti.

Lehet, hogy ismeri a tipikus adatelemzési folyamat megjelenését. Ez egy jól ismert folyamat, amelyet a legtöbb gépi tanulási projekt követ.

A folyamat magas szinten a következő lépéseket tartalmazza:
* (Üzleti ismeretek)
* Adatgyűjtés és adatértelmezés
* Modellezés
* Modell üzembe helyezése és pontozása

Ez a cikk az adatelemzési folyamat perspektívájában ismerteti az Azure szinapszis gépi tanulási képességeit különböző analitikai motorokban. Az adatelemzési folyamat minden egyes lépéseként az Azure szinapszis olyan képességeit összegzi, amelyek segíthetnek az összefoglalásban.

## <a name="azure-synapse-machine-learning-capabilities"></a>Azure szinapszis Machine learning-képességek

### <a name="data-acquisition-and-understanding"></a>Adatgyűjtés és adatértelmezés

A legtöbb gépi tanulási projekt jól kialakított lépéseket tesz elérhetővé, és az egyik lépés az, hogy hozzáférjen és megértse az adatmennyiséget.

#### <a name="data-source-and-pipelines"></a>Adatforrás és folyamatok

A [Azure Data Factorynak](../../data-factory/introduction.md)köszönhetően az Azure szinapszis natívan integrált része, amely az adatfeldolgozási és adatelőkészítési folyamatok számára is hatékony eszközkészlettel rendelkezik. Ez lehetővé teszi az adatfolyamatok egyszerű kiépítését, amelyekkel a gépi tanulás számára felhasználható formátumhoz férhet hozzá és alakíthatja át azokat. További információ a Szinapszisban található [adatfolyamatokról](../../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) . 

#### <a name="data-preparation-and-explorationvisualization"></a>Az adatelőkészítés és a feltárás/vizualizáció

A gépi tanulási folyamat fontos része az, hogy megértse az adatelemzést és vizualizációkat.

Az adattárolás helyétől függően a szinapszis különféle eszközöket kínál az elemzéshez és a gépi tanuláshoz való felkészüléshez és előkészítéséhez. Az adatfeltárás első lépéseinek egyik leggyorsabb módja Apache Spark vagy kiszolgáló nélküli SQL-készletek használata közvetlenül a Microsoft adatkezelési szolgáltatásában.

* A [Apache Spark for Azure szinapszis](../spark/apache-spark-overview.md) olyan képességeket kínál, amelyek nagy mennyiségű adatátalakítást, előkészítést és feltárást biztosítanak. Ezek a Spark-készletek olyan eszközöket kínálnak, mint a PySpark/Python, a Scala és a .NET az adatfeldolgozáshoz. A hatékony vizualizációs kódtárak használatával az adatfeltárási élmény jobban megismerheti az adatelemzést. [Ismerje meg, hogyan derítheti fel és jelenítheti meg a szinapszis adatait a Spark használatával](../get-started-analyze-spark.md).

* A [kiszolgáló nélküli SQL-készletek](../sql/on-demand-workspace-overview.md) lehetővé teszik az adatelemzést közvetlenül a TSQL használatával. A kiszolgáló nélküli SQL-készletek néhány beépített vizualizációt is kínálnak a szinapszis Studióban. [További információ az adatelemzés kiszolgáló nélküli SQL-készletekkel való megismeréséről](../get-started-analyze-sql-on-demand.md).

### <a name="modeling"></a>Modellezés

Az Azure Szinapszisban a gépi tanulási modelleket a Apache Spark készleteken végezheti el, például a PySpark/Python, a Scala vagy a .NET eszközökkel.

#### <a name="train-models-on-spark-pools-with-mllib"></a>Modellek betanítása a Spark-készleteken a MLlib

A gépi tanulási modelleket különböző algoritmusok és könyvtárak segítségével lehet betanítani. A [Spark MLlib](http://spark.apache.org/docs/latest/ml-guide.html) méretezhető gépi tanulási algoritmusokat kínál, amelyek segítenek a legtöbb klasszikus gépi tanulási probléma megoldásában. [Ez az oktatóanyag](../spark/apache-spark-machine-learning-mllib-notebook.md) bemutatja, hogyan taníthat modelleket a MLlib használatával a szinapszisban.

A MLlib mellett az olyan népszerű kódtárak is használhatók, mint a [Scikit-Learn](https://scikit-learn.org/stable/) modellek. A könyvtárak a szinapszis Spark-készleteken való telepítésével kapcsolatos részletekért lásd: az [Azure szinapszis Analytics Apache Spark könyvtárainak kezelése](../spark/apache-spark-azure-portal-add-libraries.md) .

#### <a name="train-models-with-azure-machine-learning-automated-ml"></a>Modellek betanítása Azure Machine Learning automatizált ML-vel

A gépi tanulási modellek betanításának egy másik módja, amely nem igényli a gépi tanuláshoz szükséges jóval korábbi ismereteket, az automatikus ML-t használja. Az [automatikus ml](../../machine-learning/concept-automated-ml.md) egy olyan szolgáltatás, amely automatikusan betanítja a gépi tanulási modellek készletét, és lehetővé teszi, hogy a felhasználó kiválassza a legjobb modellt adott mérőszámok alapján. Az Azure szinapszis-jegyzetfüzetek Azure Machine Learningával való zökkenőmentes integrációnak köszönhetően a felhasználók könnyedén használhatják az automatikus ML-t a Szinapszisban a továbbító Azure Active Directory hitelesítéssel.  Ez azt jelenti, hogy csak a Azure Machine Learning munkaterületre kell mutatnia, és nem kell megadnia a hitelesítő adatokat. Itt talál egy [AUTOMATIZÁLT ml-oktatóanyagot](../spark/apache-spark-azure-machine-learning-tutorial.md) , amely leírja, hogyan kell betanítani a modelleket a szinapszis Spark-készleteken található Azure Machine learning automatizált ml használatával.

### <a name="model-deployment-and-scoring"></a>Modell üzembe helyezése és pontozása

Az Azure Szinapszisban vagy az Azure szinapszison kívül már betanított modellek könnyen használhatók a Batch-pontozáshoz. Jelenleg a Szinapszisban kétféle módon futtatható a Batch-pontozási szolgáltatás.

* A szinapszis SQL-készletekben a [TSQL prediktív függvény](../sql-data-warehouse/sql-data-warehouse-predict.md) használatával a jóslatokat a megfelelő módon futtathatja. Ez a hatékony és skálázható funkció lehetővé teszi, hogy az adattárházból kifelé irányuló összes adat áthelyezése nélkül gazdagítsa az adatait. A [szinapszis Studióban egy új, interaktív gépi tanulási modellt](./tutorial-sql-pool-model-scoring-wizard.md) ismertetünk, amelyen üzembe HELYEZHET egy ONNX modellt a Azure Machine learning modell beállításjegyzékében a szinapszis SQL-készletekben a Batch-pontozáshoz az előrejelzés használatával.

* Az Azure Szinapszisban a gépi tanulási modellek egy másik lehetősége az Azure szinapszis Apache Spark készletének kihasználása. A modellek betanításához használt könyvtáraktól függően a Batch-pontozási futtatásához használhat programkódot.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Synapse Analytics első lépései](../get-started.md)
* [Munkaterület létrehozása](../get-started-create-workspace.md)
* [Gyors útmutató: új Azure Machine Learning társított szolgáltatás létrehozása a Szinapszisban](quickstart-integrate-azure-machine-learning.md)
* [Oktatóanyag: gépi tanulási modell pontozása varázsló – dedikált SQL-készlet](tutorial-sql-pool-model-scoring-wizard.md)