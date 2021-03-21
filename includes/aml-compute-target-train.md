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
ms.openlocfilehash: 081eea7842652884363603bc5ba742ba48539f85
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503614"
---
**A számítási célok az egyik betanítási feladatokból a következőre használhatók.** Ha például egy távoli virtuális gépet csatlakoztat a munkaterülethez, újra felhasználhatja azt több feladathoz is. Gépi tanulási folyamatokhoz használja az egyes számítási célkitűzések megfelelő [folyamatának lépéseit](/python/api/azureml-pipeline-steps/azureml.pipeline.steps) .

A legtöbb feladathoz a következő források bármelyikét használhatja egy képzési számítási célra. Nem minden erőforrás használható az automatikus gépi tanuláshoz, a gépi tanulási folyamatokhoz vagy a tervezőhöz.

|Képzési &nbsp; célok|[Automatizált gépi tanulás](../articles/machine-learning/concept-automated-ml.md) | [Machine Learning-folyamatok](../articles/machine-learning/concept-ml-pipelines.md) | [Azure Machine Learning Designer](../articles/machine-learning/concept-designer.md)
|----|:----:|:----:|:----:|
|[Helyi számítógép](../articles/machine-learning/how-to-attach-compute-targets.md#local)| Yes | &nbsp; | &nbsp; |
|[Számítási fürt Azure Machine Learning](../articles/machine-learning/how-to-create-attach-compute-cluster.md)| Igen | Igen | Yes |
|[Azure Machine Learning számítási példány](../articles/machine-learning/how-to-create-manage-compute-instance.md) | Igen (az SDK-n keresztül)  | Yes |  |
|[Távoli virtuális gép](../articles/machine-learning/how-to-attach-compute-targets.md#vm) | Igen  | Yes | &nbsp; |
|[Azure- &nbsp; Databricks](../articles/machine-learning/how-to-attach-compute-targets.md#databricks)| Igen (csak SDK helyi mód) | Yes | &nbsp; |
|[Azure Data Lake Analytics](../articles/machine-learning/how-to-attach-compute-targets.md#adla) | &nbsp; | Yes | &nbsp; |
|[Azure HDInsight](../articles/machine-learning/how-to-attach-compute-targets.md#hdinsight) | &nbsp; | Yes | &nbsp; |
|[Azure Batch](../articles/machine-learning/how-to-attach-compute-targets.md#azbatch) | &nbsp; | Yes | &nbsp; |
