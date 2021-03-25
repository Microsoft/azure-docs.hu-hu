---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 09/17/2020
ms.openlocfilehash: 7c89527218e34bea0819a1e2446441216cde45ae
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104443"
---
**A számítási célok az egyik betanítási feladatokból a következőre használhatók.** Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is. Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) .

A legtöbb feladathoz a következő források bármelyikét használhatja egy képzési számítási célra. Nem minden erőforrás használható az automatikus gépi tanuláshoz, a gépi tanulási folyamatokhoz vagy a tervezőhöz.

|Képzési &nbsp; célok|[Automatizált gépi tanulás](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning-folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Igen | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Igen | Igen | Igen |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Igen (az SDK-n keresztül)  | Igen |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Igen  | Igen | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Igen (csak SDK helyi mód) | Igen | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Igen | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Igen | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Igen | &nbsp; |

> [!TIP]
> A számítási példány 120 GB-OS operációsrendszer-lemezzel rendelkezik. Ha elfogyott a lemezterület, törölje a megfelelő területet a számítási példány leállítása/újraindítása előtt.
