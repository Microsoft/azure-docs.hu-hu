---
title: 'ML Studio (klasszikus): Migrálás Azure Machine Learning – felhasználható folyamat végpontjai'
description: A folyamat-végpontok integrálása Azure Machine Learning-beli ügyfélalkalmazások alkalmazásával.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565243"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Folyamat-végpontok felhasználása ügyfélalkalmazások számára

Ebből a cikkből megtudhatja, hogyan integrálhat ügyfélalkalmazások Azure Machine Learning-végpontokkal. Az alkalmazás kódjának írásával kapcsolatos további információkért lásd: [Azure Machine learning-végpont felhasználása](../how-to-consume-web-service.md).

Ez a cikk a Studio (klasszikus) Azure Machine Learning áttelepítési sorozat részét képezi. A Azure Machine Learningre való áttelepítéssel kapcsolatos további információkért tekintse meg [az áttelepítési áttekintést ismertető cikket](migrate-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy Azure Machine Learning-munkaterület. [Hozzon létre egy Azure Machine learning munkaterületet](../how-to-manage-workspace.md#create-a-workspace).
- Egy [Azure Machine learning valós idejű végpont vagy folyamat végpontja](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Valós idejű végpont felhasználása 

Ha **valós idejű végpontként** telepítette a modellt, megtalálhatja a REST-végpontját, valamint a C#, Python és R nyelven elérhető, előre generált használati kódokat:

1. Nyissa meg a Azure Machine Learning studiót ([ml.Azure.com](https://ml.azure.com)).
1. Lépjen a **végpontok** lapra.
1. Válassza ki a valós idejű végpontot.
1. Válassza **a** felhasználás lehetőséget.

> [!NOTE]
> A végponti specifikációt a **részletek** lapon is megtalálhatja. Használja a hencegés definícióját a végpont sémájának megismeréséhez. A hencegés meghatározásával kapcsolatos további információkért lásd a kivágási [hivatalos dokumentációját](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Folyamat-végpont felhasználása

Kétféle módon lehet használni egy folyamat végpontját:

- REST API hívások
- Integráció a Azure Data Factory

### <a name="use-rest-api-calls"></a>REST API-hívások használata

Hívja meg a REST-végpontot az ügyfélalkalmazás alapján. A végpontra vonatkozó részletezés használatával megismerheti a sémáját:

1. Nyissa meg a Azure Machine Learning studiót ([ml.Azure.com](https://ml.azure.com)).
1. Lépjen a **végpontok** lapra.
1. Válassza a **folyamat-végpontok** lehetőséget.
1. Válassza ki a folyamat végpontját.
1. A **folyamat végpontjának áttekintés** ablaktábláján válassza ki a hivatkozást a **Rest-végpont dokumentációjában**.

### <a name="use-azure-data-factory"></a>Az Azure Data Factory használata

A Azure Machine Learning folyamat egy Azure Data Factory folyamat lépéseként hívható meg. További információ: [Azure Machine learning folyamatok végrehajtása Azure Data Factoryban](../../data-factory/transform-data-machine-learning-service.md).


## <a name="next-steps"></a>Következő lépések

Ebből a cikkből megtudhatta, hogyan keresheti meg a folyamat végpontjai sémáját és mintáját. További információ a végpontok ügyfélalkalmazás általi használatáról: [Azure Machine learning-végpont felhasználása](../how-to-consume-web-service.md).

Tekintse meg a Azure Machine Learning áttelepítési sorozat cikkeinek további cikkeit: 
1. [Áttelepítési áttekintés](migrate-overview.md).
1. [Adatkészlet Átmigrálása](migrate-register-dataset.md).
1. [Építse újra a Studio (klasszikus) betanítási folyamatát](migrate-rebuild-experiment.md).
1. [Építse újra a Studio (klasszikus) webszolgáltatást](migrate-rebuild-web-service.md).
1. **Azure Machine learning webszolgáltatások integrálása az ügyfélalkalmazások alkalmazásával**.
1. [Telepítse át az R-szkriptet](migrate-execute-r-script.md).