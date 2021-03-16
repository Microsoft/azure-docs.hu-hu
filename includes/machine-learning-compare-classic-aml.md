---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: ff64a0948402ff152e45bd4702d986f51b9547aa
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563189"
---
Az alábbi táblázat összefoglalja ML Studio (klasszikus) és Azure Machine Learning közötti fő különbségeket.

| Szolgáltatás | ML Studio (klasszikus) | Azure Machine Learning |
|---| --- | --- |
| Húzási interfész | Klasszikus felhasználói élmény | Frissített tapasztalat – [Azure Machine learning Designer](../articles/machine-learning/concept-designer.md)| 
| SDK-kód | Nem támogatott | Teljes mértékben integrált [Azure Machine learning Python](/python/api/overview/azure/ml/) és [R](https://github.com/Azure/azureml-sdk-for-r) SDK-k |
| Experiment | Skálázható (10 GB-os betanítási adatkorlát) | Méretezés számítási céllal |
| Számítási célok betanítása | Tulajdonosi számítási cél, csak CPU-támogatás | Testreszabható [képzési számítási célok](../articles/machine-learning/concept-compute-target.md#train)széles köre. GPU-val és CPU-támogatással rendelkezik | 
| Üzembe helyezési számítási célok | Tulajdonosi webszolgáltatás formátuma, nem testreszabható | Testreszabható [üzembe helyezési számítási célok](../articles/machine-learning/concept-compute-target.md#deploy)széles köre. GPU-val és CPU-támogatással rendelkezik |
| ML-folyamat | Nem támogatott | Rugalmas, moduláris [folyamatok](../articles/machine-learning/concept-ml-pipelines.md) készítése a munkafolyamatok automatizálására |
| MLOps | Alapszintű modell kezelése és üzembe helyezése; Csak CPU-telepítések | Entitások verziószámozása (modell, adattárolás, munkafolyamatok), munkafolyamat-automatizálás, integráció a vel-eszközökkel, processzor-és GPU-környezetek, [valamint egyebek](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Modell formátuma | Saját formátum, Studio (klasszikus) csak | Több támogatott formátum a betanítási feladattípustól függően |
| Automatizált modellek betanítása és hiperparaméter finomhangolása |  Nem támogatott | [Támogatott](../articles/machine-learning/concept-automated-ml.md). Kód – az első és a kód nélküli beállítások. | 
| Adateltolódás észlelése | Nem támogatott | [Támogatott](../articles/machine-learning/how-to-monitor-datasets.md) |
| Adatcímkéző projektek | Nem támogatott | [Támogatott](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Szerepköralapú hozzáférés-vezérlés (RBAC) | Csak közreműködői és tulajdonosi szerepkör | [Rugalmas szerepkör-definíció és RBAC-vezérlés](../articles/machine-learning/how-to-assign-roles.md) |
| AI-gyűjtemény | Támogatott ( [https://gallery.azure.ai/](https://gallery.azure.ai/) ) | Nem támogatott <br><br> Ismerje meg a [PYTHON SDK-jegyzetfüzeteket](https://github.com/Azure/MachineLearningNotebooks). |