---
title: Modellek üzembe helyezése élesben – csoportos adatelemzési folyamat
description: Modellek üzembe helyezése az éles környezetben, amely lehetővé teszi, hogy aktív szerepet játsszon az üzleti döntések meghozatalában.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 89ea1e991df46b4e4d23305b6118980b80c2f917
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321194"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Modellek üzembe helyezése éles környezetben az üzleti döntések meghozatala érdekében

Az éles környezetbe állítás lehetővé teszi, hogy egy modell aktív szerepet játsszon a vállalaton belül. Az üzembe helyezett modellből származó előrejelzések üzleti döntések meghozatalára is használhatók.

## <a name="production-platforms"></a>Üzemi platformok

A modellek éles környezetben való üzembe helyezéséhez különböző megközelítések és platformok tartoznak. Íme néhány lehetőség:

- [A modellek üzembe helyezésének helye Azure Machine Learning](../how-to-deploy-and-where.md)
- [Modell üzembe helyezése az SQL-Serverben](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Microsoft Machine Learning-kiszolgáló](/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Az üzembe helyezés előtt az egyiknek biztosítania kell, hogy a modell pontozásának késése elég alacsony legyen az éles környezetben való használathoz.
>

>[!NOTE]
>A Azure Machine Learning Studio használatával történő üzembe helyezéssel kapcsolatban lásd: [Azure Machine learning webszolgáltatás üzembe helyezése](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B tesztelés

Ha több modell is éles üzemben van, [a/B tesztelést](https://en.wikipedia.org/wiki/A/B_testing) is használhatja a modell teljesítményének összehasonlítására. 
 
## <a name="next-steps"></a>Következő lépések

Az **adott forgatókönyvek** folyamatának összes lépését bemutató útmutatókat is megadja. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példában szereplő forgatókönyvek](walkthroughs.md) című cikkben. Bemutatják, hogyan kombinálhatók a felhő, a helyszíni eszközök és a szolgáltatások egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához.