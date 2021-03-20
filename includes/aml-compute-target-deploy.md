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
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97505120"
---
A modell üzemeltetéséhez használt számítási cél a telepített végpont költségeit és rendelkezésre állását is befolyásolja. A táblázat segítségével kiválaszthatja a megfelelő számítási célt.

| Számítási cél | Alkalmazási cél | GPU-támogatás | FPGA-támogatás | Description |
| ----- | ----- | ----- | ----- | ----- |
| [Helyi &nbsp; webszolgáltatás &nbsp;](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Tesztelés/hibakeresés | &nbsp; | &nbsp; | Korlátozott teszteléshez és hibaelhárításhoz használható. A hardveres gyorsítás a helyi rendszer könyvtárainak használatából függ.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Valós idejű következtetés |  [Igen](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webszolgáltatás üzembe helyezése) | [Igen](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Nagy léptékű üzembe helyezések esetén használható. Gyors válaszidőt és automatikus skálázást biztosít a telepített szolgáltatás számára. A fürt automatikus skálázása nem támogatott a Azure Machine Learning SDK-n keresztül. Az AK-fürt csomópontjainak módosításához használja a Azure Portalban az AK-fürt felhasználói felületét. <br/><br/> A tervező támogatja. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Tesztelés vagy fejlesztés | &nbsp;  | &nbsp; | Alacsony léptékű CPU-alapú számítási feladatokhoz használható, amelyek kevesebb mint 48 GB RAM memóriát igényelnek. <br/><br/> A tervező támogatja. |
| [Az Azure Machine Learning számítási fürtjei](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Batch- &nbsp; következtetés | [Igen](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (gépi tanulási folyamat) | &nbsp;  | Kötegelt pontozás futtatása kiszolgáló nélküli számítási feladatokhoz. A a normál és az alacsony prioritású virtuális gépeket támogatja. A valós idejű következtetések nem támogatottak.|

> [!NOTE]
> Bár a számítási célok, például a helyi, Azure Machine Learning számítások, és a Azure Machine Learning számítási fürtök támogatják a GPU-t a betanításhoz és kísérletezéshez, GPU-t használ, _Ha webszolgáltatásként való üzembe helyezés esetén_ csak az AK-ban támogatott.
>
> _Ha a Machine learning-folyamattal való pontozást_ csak Azure Machine learning számításokban támogatja, GPU-t használ a következtetéshez.
> 
> A fürt SKU kiválasztásakor először fel kell skálázást, majd ki kell bővíteni. Kezdje egy olyan géppel, amely a modell által igényelt RAM 150%-a, az eredmény profilja és a szükséges teljesítménnyel rendelkező gép keresése. Miután megismerte, növelje a gépek számát, hogy illeszkedjen az egyidejű következtetéshez.

> [!NOTE]
> * A Container instances csak az 1 GB-nál kisebb méretű kisméretű modellek esetében alkalmas.
> * Használjon egycsomópontos AK-fürtöket nagyobb modellek fejlesztéséhez és teszteléséhez.