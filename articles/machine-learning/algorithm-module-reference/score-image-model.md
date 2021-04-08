---
title: A pontszám rendszerkép-modell moduljának használata
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan készíthet előrejelzéseket egy betanított képmodell használatával a Azure Machine Learning pontszám rendszerkép-modell moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: fe57a9e8ce9b14f7d1346d819965576770afef3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93324892"
---
# <a name="score-image-model"></a>Képmodell pontozása

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal előrejelzések hozhatók elő a betanított képmodell használatával a bemeneti képadatokon.

## <a name="how-to-configure-score-image-model"></a>A pontszám rendszerkép-modell konfigurálása

1. Adja hozzá a **pontszám rendszerkép-modell** modult a folyamathoz.

2. Egy betanított képmodell és egy bemeneti képadatokat tartalmazó adatkészlet csatolása. 

    Az adattípusnak ImageDirectory típusúnak kell lennie. A rendszerkép könyvtárának beolvasásával kapcsolatos további információkért tekintse meg az [átalakítás rendszerkép-](convert-to-image-directory.md) modulba című témakört. A bemeneti adatkészlet sémájának általában egyeznie kell a modell betanításához használt adatok sémájával is.

3. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

Miután a [pontszám képmodell](score-image-model.md)használatával generálta a pontszámokat, a modell pontosságának kiértékeléséhez használt mérőszámok (teljesítmény) létrehozásához a modul és a pontozásos adatkészlet összekapcsolhatók a [modell kiértékeléséhez](evaluate-model.md). 

### <a name="publish-scores-as-a-web-service"></a>Pontszámok közzététele webszolgáltatásként

A pontozás gyakori használata a kimenet visszaküldése a prediktív webszolgáltatás részeként. További információkért tekintse meg [ezt az oktatóanyagot](../tutorial-designer-automobile-price-deploy.md) , amely bemutatja, hogyan helyezhet üzembe egy valós idejű végpontot a Azure Machine learning Designer egyik folyamata alapján.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .