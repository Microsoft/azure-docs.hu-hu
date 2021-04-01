---
title: Gépi tanulás és AI a ONNX az Azure SQL Edge-ben
description: Az Azure SQL Edge gépi tanulása támogatja a nyílt neurális hálózati Exchange-(ONNX-) formátumú modelleket. A ONNX egy nyílt formátum, amellyel különböző gépi tanulási keretrendszerek és eszközök közötti adatcsere-modelleket lehet használni.
keywords: SQL Edge üzembe helyezése
services: sql-edge
ms.service: sql-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 05/19/2020
ms.openlocfilehash: 5dc3d44ac4396897fd43831d51ee628bb06048cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392061"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-edge"></a>Gépi tanulás és AI a ONNX az SQL Edge-ben

Az Azure SQL Edge gépi tanulása támogatja a [nyílt neurális hálózati Exchange-(ONNX-)](https://onnx.ai/) formátumú modelleket. A ONNX egy nyílt formátum, amellyel különböző [gépi tanulási keretrendszerek és eszközök](https://onnx.ai/supported-tools)közötti adatcsere-modelleket lehet használni.

## <a name="overview"></a>Áttekintés

Ahhoz, hogy a gépi tanulási modellek az Azure SQL Edge-ben is kikövetkeztetve legyenek, először be kell szereznie egy modellt. Ez lehet egy előre betanított modell vagy egy egyéni modell, amely az Ön által választott keretrendszerrel lett kiképezve. Az Azure SQL Edge támogatja a ONNX formátumot, és erre a formátumra kell konvertálnia a modellt. A modell pontossága nem befolyásolható, és ha már rendelkezik a ONNX-modellel, a modellt üzembe helyezheti az Azure SQL Edge-ben, és használhatja [a natív pontozást a prediktív T-SQL függvénnyel](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>ONNX-modellek beolvasása

Modell beszerzése ONNX formátumban:

- **Modell-építési szolgáltatások**: olyan szolgáltatások, mint például a Azure Machine learning és az [Custom Vision Service Azure](../cognitive-services/custom-vision-service/getting-started-build-a-classifier.md) [automatizált Machine learning szolgáltatása](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) , a betanított modell ONNX-formátumba történő közvetlen exportálásának támogatása.

- [**Meglévő modellek konvertálása és/vagy exportálása**](https://github.com/onnx/tutorials#converting-to-onnx-format): több betanítási keretrendszer (pl. [PyTorch](https://pytorch.org/docs/stable/onnx.html), láncolás és Caffe2) támogatja a natív exportálási funkciókat a ONNX, így a betanított modellt a ONNX formátum egy adott verziójára mentheti. A natív exportálást nem támogató keretrendszerek esetében léteznek különálló ONNX Converter telepíthető csomagok, amelyek lehetővé teszik különböző gépi tanulási keretrendszerekből betanított modellek átalakítását a ONNX formátumba.

     **Támogatott keretrendszerek**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit – Learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    A támogatott keretrendszerek és példák teljes listájáért lásd: [Konvertálás ONNX formátumra](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Korlátozások

Jelenleg nem minden ONNX modell támogatott az Azure SQL Edge-ben. A támogatás a **numerikus adattípusú** modellekre korlátozódik:

- [int és bigint](/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [valós és lebegőpontos](/sql/t-sql/data-types/float-and-real-transact-sql).
  
A más numerikus típusok a [Cast és a convert](/sql/t-sql/functions/cast-and-convert-transact-sql)paranccsal alakíthatók át támogatott típusokra.

A modell bemeneteit strukturálni kell, hogy a modell minden bemenete egy tábla egyetlen oszlopának felel meg. Ha például pandák dataframe használ a modell betanításához, akkor minden bemenetnek külön oszlopnak kell lennie a modellhez.

## <a name="next-steps"></a>Következő lépések

- [Az SQL Edge üzembe helyezése Azure Portal](deploy-portal.md)
- [ONNX-modell üzembe helyezése az Azure SQL Edge-ben ](deploy-onnx.md)