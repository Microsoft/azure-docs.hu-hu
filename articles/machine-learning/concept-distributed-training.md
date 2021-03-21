---
title: Mi az elosztott képzés?
titleSuffix: Azure Machine Learning
description: Megtudhatja, milyen típusú elosztott képzési Azure Machine Learning támogat, valamint az elosztott képzéshez elérhető nyílt forráskódú keretrendszer-integrációkat.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f87175500fcf5bdbcf9a5c2f499f6bab96b37b63
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498965"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Elosztott képzés Azure Machine Learning

Ebben a cikkben megismerheti az elosztott képzéseket, és azt, hogy a Azure Machine Learning hogyan támogatja az IT-modelleket. 

Az elosztott képzésben a modell betanításához szükséges számítási feladatok felosztása és megosztása több, feldolgozó csomópontnak nevezett mini-processzor között történik. Ezek a feldolgozói csomópontok párhuzamosan működnek a modell betanításának felgyorsításához. Az elosztott képzések hagyományos ML-modellekhez használhatók, de jobban alkalmazkodnak a számítási és időigényes feladatokhoz, például a mély [tanulás](concept-deep-learning-vs-machine-learning.md) a mély neurális hálózatok betanításához. 

## <a name="deep-learning-and-distributed-training"></a>Mélyreható tanulás és elosztott képzés 

Az elosztott képzésnek két fő típusa van: az [adatpárhuzamosság](#data-parallelism) és a [modell párhuzamossága](#model-parallelism). A részletes tanulási modelleken a [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro) támogatja a népszerű keretrendszerek, a PyTorch és a TensorFlow integrációját. Mindkét keretrendszer adatpárhuzamosságot alkalmaz az elosztott képzések esetében, és a számítási sebesség optimalizálása érdekében [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) is képes kihasználni. 

* [Elosztott betanítás a PyTorch használatával](how-to-train-pytorch.md#distributed-training)

* [Elosztott képzés a TensorFlow](how-to-train-tensorflow.md#distributed-training)

Az elosztott képzést nem igénylő ML-modelleknél tekintse meg a modellek [betanítása a Azure Machine learning](concept-train-machine-learning-model.md#python-sdk) segítségével című témakört, amely a Python SDK használatával különböző modelleket tanít.

## <a name="data-parallelism"></a>Adatpárhuzamosság

Az adatpárhuzamosság a legegyszerűbben a két elosztott képzési módszer megvalósítása, és a legtöbb felhasználási esethez elegendő.

Ebben a megközelítésben az adatok felosztva vannak osztva, ahol a partíciók száma megegyezik az elérhető csomópontok teljes számával a számítási fürtben. A modellt a rendszer átmásolja a munkavégző csomópontok mindegyikében, és mindegyik feldolgozó a saját részhalmazán működik. Ne feledje, hogy minden csomópontnak rendelkeznie kell a betanított modell támogatásához szükséges kapacitással, azaz a modellnek teljes mértékben el kell férnie az egyes csomópontokon. A következő ábra vizuálisan mutatja be ezt a megközelítést.

![Adatpárhuzamosság – koncepció – diagram](./media/concept-distributed-training/distributed-training.svg)

Mindegyik csomópont egymástól függetlenül kiszámítja a betanítási minták és a címkézett kimenetek közötti hibákat. Az egyes csomópontok pedig a hibák alapján frissítik a modelljét, és a többi csomóponton lévő összes módosítást tájékoztatni kell a megfelelő modelljeik frissítéséhez. Ez azt jelenti, hogy a feldolgozó csomópontoknak szinkronizálnia kell a modell paramétereit vagy színátmeneteit a Batch kiszámításának végén, így biztosítva, hogy egységes modellt tanítanak. 

## <a name="model-parallelism"></a>Modell párhuzamossága

A modell párhuzamossága, más néven hálózati párhuzamosság, a modell különböző részekre oszlik, amelyek párhuzamosan futhatnak különböző csomópontokon, és mindegyik ugyanazon az adatain fut le. Ennek a módszernek a méretezhetősége az algoritmus párhuzamos mértékétől függ, és összetettebb az adatpárhuzamosságok megvalósításához. 

A modell párhuzamossága esetében a feldolgozó csomópontoknak csak egyszer kell szinkronizálnia a megosztott paramétereket, általában egyszer az egyes továbbítási vagy visszamenőleges terjesztési lépésekhez. Emellett a nagyobb modellek nem érintik a problémát, mivel az egyes csomópontok a modell egy alszakaszán működnek ugyanazon a betanítási adaton.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [használhatók a számítási célok a modell betanításához](how-to-set-up-training-targets.md) a Python SDK-val.
* Technikai példákért tekintse meg a [hivatkozási architektúra forgatókönyvét](/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Ml modellek betanítása a TensorFlow](how-to-train-tensorflow.md).
* [Ml modellek betanítása a PyTorch](how-to-train-pytorch.md).