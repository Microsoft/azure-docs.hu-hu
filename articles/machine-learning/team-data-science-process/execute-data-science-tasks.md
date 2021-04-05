---
title: Adatelemzési feladatok végrehajtása – csoportos adatelemzési folyamat
description: Egy adatelemző projekt nyomon követhető, vezérelt és együttműködő módon végezhető el az adatelemzési projektek végrehajtásában.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: e180ecbf5c68dbd9c179244083a641ac6ed42de0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100371957"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Adatelemzési feladatok végrehajtása: feltárás, modellezés és üzembe helyezés

A tipikus adatelemzési feladatok közé tartozik az adatok feltárása, modellezése és üzembe helyezése. Ez a cikk azokat a feladatokat ismerteti, amelyek számos olyan általános adatelemzési feladatot hajtanak végre, mint például az interaktív adatok feltárása, az adatok elemzése, a jelentéskészítés és a modell létrehozása. A modellek éles környezetben történő üzembe helyezésének lehetőségei a következők lehetnek:

- [Azure Machine Learning](../index.yml)
- [SQL-Server ML-szolgáltatásokkal](/sql/advanced-analytics/r/r-services)
- [Microsoft Machine Learning-kiszolgáló](/machine-learning-server/what-is-machine-learning-server)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> feltárás 

Az adattudós különböző módokon végezheti el a feltárást és jelentéskészítést: a Pythonhoz elérhető kódtárak és csomagok (például matplotlib) vagy az R (ggplot vagy Lattice) használatával. Az adatszakértők számára az ilyen kódok testreszabhatók, hogy illeszkedjenek az adatfeltárás igényeihez bizonyos forgatókönyvek esetében. A strukturált adat kezelésére vonatkozó igények különböznek a strukturálatlan adatmennyiségek, például a szöveg vagy a képek esetében. 

Az olyan termékek, mint a Azure Machine Learning [speciális adatelőkészítést](../how-to-create-register-datasets.md) biztosítanak az adathuzavona és-feltáráshoz, beleértve a szolgáltatások létrehozását is. A felhasználónak meg kell határoznia az igényeiknek legmegfelelőbb eszközöket, kódtárakat és csomagokat. 

A fázis végén elkészített termék egy adatfeltárási jelentés. A jelentésnek átfogó képet kell adnia a modellezéshez használt adatmennyiségről és annak felméréséről, hogy az adott információ alkalmas-e a modellezési lépés folytatására. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> modellezés

A különböző nyelveken számos eszközkészletet és csomagot kell betanítani a modellekhez. Az adatszakértőknek minden eddiginél szívesebben kell használniuk, ha a pontossággal és késéssel kapcsolatos teljesítménnyel kapcsolatos megfontolások teljesülnek a releváns üzleti használati esetekben és a termelési forgatókönyvekben.

### <a name="model-management"></a>Modellkezelés
Több modell létrehozása után általában a modellek regisztrálásához és kezeléséhez szükséges rendszernek kell lennie. Általában parancsfájlok vagy API-k, valamint háttér-adatbázis vagy verziószámozási rendszer kombinációja szükséges. A következő felügyeleti feladatokhoz néhány lehetőség közül választhat:

1. [Azure Machine Learning – modell-kezelési szolgáltatás](../index.yml)
2. [ModelDB az MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL-kiszolgáló modell-felügyeleti rendszerként](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)
4. [Microsoft Machine Learning-kiszolgáló](/sql/advanced-analytics/r/r-server-standalone)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> üzembe helyezés

Az éles környezetbe állítás lehetővé teszi, hogy egy modell aktív szerepet játsszon a vállalaton belül. Az üzembe helyezett modellből származó előrejelzések üzleti döntések meghozatalára is használhatók.

### <a name="production-platforms"></a>Üzemi platformok
A modellek éles környezetben való üzembe helyezéséhez különböző megközelítések és platformok tartoznak. Íme néhány lehetőség:


- [Modell üzembe helyezése Azure Machine Learning](../how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning-kiszolgáló](/sql/advanced-analytics/r/r-server-standalone)

> [!NOTE]
> Az üzembe helyezés előtt az egyiknek biztosítania kell, hogy a modell pontozásának késése elég alacsony legyen az éles környezetben való használathoz.
>
>

További példákat talál az útmutatókban, amelyek a folyamat összes lépését mutatják be **adott forgatókönyvek** esetén. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Bemutatják, hogyan kombinálhatók a felhő, a helyszíni eszközök és a szolgáltatások egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához.

> [!NOTE]
> A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>A/B tesztelés
Ha több modell van éles környezetben, hasznos lehet a [/B teszt](https://en.wikipedia.org/wiki/A/B_testing) végrehajtása a modellek teljesítményének összehasonlításához. 

 
## <a name="next-steps"></a>Következő lépések

Az adatelemzési [projektek előrehaladásának követésével](track-progress.md) megtudhatja, hogy az adattudós hogyan követheti nyomon az adatelemzési projekt állapotát.

A [Model művelet és a CI/CD](ci-cd-flask.md) azt mutatja be, hogyan hajtható végre a CI/CD a kifejlesztett modellekkel.
