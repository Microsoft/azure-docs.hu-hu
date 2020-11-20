---
title: ML-folyamat elindítása új adatértékekhez
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan indíthat el egy Azure Machine Learning folyamat futtatását Azure Logic Apps segítségével, hogy válaszoljon az új adataira.
services: machine-learning
author: NilsPohlmann
ms.author: nilsp
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.date: 02/07/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4
ms.openlocfilehash: 20d44fd3150f9da31e9c242017e597d4f46e4d5d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963929"
---
# <a name="trigger-a-run-of-a-machine-learning-pipeline-from-a-logic-app"></a>Machine Learning folyamat futtatásának elindítása logikai alkalmazásból

Aktiválja az Azure Machine Learning folyamat futtatását, amikor új adatmennyiség jelenik meg. Előfordulhat például, hogy a folyamatot úgy szeretné elindítani, hogy az új modellt betanítsa, amikor az új adatértékek megjelennek a blob Storage-fiókban. Az trigger beállítása [Azure Logic Appssal](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

* A közzétett Machine Learning folyamat REST-végpontja. [A folyamat létrehozása és közzététele](how-to-create-your-first-pipeline.md). Ezután keresse meg a PublishedPipeline REST-végpontját a folyamat AZONOSÍTÓjának használatával:
    
     ```
    # You can find the pipeline ID in Azure Machine Learning studio
    
    published_pipeline = PublishedPipeline.get(ws, id="<pipeline-id-here>")
    published_pipeline.endpoint 
    ```
* Az [Azure Blob Storage](../storage/blobs/storage-blobs-overview.md) tárolja az adatait.
* A munkaterületen lévő [adattár](how-to-access-data.md) , amely a blob Storage-fiók adatait tartalmazza.

## <a name="create-a-logic-app"></a>Logikai alkalmazás létrehozása

Most hozzon létre egy [Azure Logic app](../logic-apps/logic-apps-overview.md) -példányt. Ha szeretné, [használjon integrációs szolgáltatási környezetet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) , és [állítson be egy ügyfél által felügyelt kulcsot](../logic-apps/customer-managed-keys-integration-service-environment.md) a logikai alkalmazás általi használatra.

A logikai alkalmazás üzembe helyezése után a következő lépésekkel konfigurálhat egy triggert a folyamathoz:

1. [Hozzon létre egy rendszerhez rendelt felügyelt identitást](../logic-apps/create-managed-service-identity.md) , amely hozzáférést biztosít az alkalmazásnak a Azure Machine learning-munkaterülethoz.

1. Navigáljon a Logic app Designer nézethez, és válassza ki az üres logikai alkalmazás sablonját. 
    > [!div class="mx-imgBorder"]
    > ![Üres sablon](media/how-to-trigger-published-pipeline/blank-template.png)

1. A tervezőben keresse meg a **blob** kifejezést. Válassza ki a **blob hozzáadása vagy módosítása (csak tulajdonságok)** triggert, és adja hozzá ezt az triggert a logikai alkalmazáshoz.
    > [!div class="mx-imgBorder"]
    > ![Trigger hozzáadása](media/how-to-trigger-published-pipeline/add-trigger.png)

1. Adja meg a blob-hozzáadások vagy-módosítások figyeléséhez használni kívánt blob Storage-fiók kapcsolódási adatait. Válassza ki a figyelni kívánt tárolót. 
 
    Válassza ki az **időintervallumot** és a **gyakoriságot** az Önnek munkát igénylő frissítések lekérdezéséhez.  

    > [!NOTE]
    > Ez az trigger figyeli a kiválasztott tárolót, de nem figyeli az almappákat.

1. Olyan HTTP-művelet hozzáadása, amely akkor fut le, amikor új vagy módosított blobot észlel. Válassza az **+ új lépés** elemet, majd keresse meg és válassza ki a http-műveletet.

  > [!div class="mx-imgBorder"]
  > ![HTTP-művelet keresése](media/how-to-trigger-published-pipeline/search-http.png)

  A művelet konfigurálásához használja a következő beállításokat:

  | Beállítás | Érték | 
  |---|---|
  | HTTP-művelet | POST |
  | URI |az [előfeltételként](#prerequisites) megtalált közzétett folyamat végpontja |
  | Hitelesítési módszer | Felügyelt identitás |

1. Állítsa be úgy az ütemtervet, hogy beállítsa bármely [DataPath PipelineParameters](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) értékét:

    ```json
    "DataPathAssignments": { 
         "input_datapath": { 
                            "DataStoreName": "<datastore-name>", 
                            "RelativePath": "@triggerBody()?['Name']" 
    } 
    }, 
    "ExperimentName": "MyRestPipeline", 
    "ParameterAssignments": { 
    "input_string": "sample_string3" 
    },
    ```

    `DataStoreName` [Előfeltételként](#prerequisites)adja hozzá a munkaterülethez hozzáadott feltételt.
     
    > [!div class="mx-imgBorder"]
    > ![HTTP-beállítások](media/how-to-trigger-published-pipeline/http-settings.png)

1. Válassza a **Mentés** lehetőséget, és most már készen áll az ütemtervre.

> [!IMPORTANT]
> Ha Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) használ a folyamathoz való hozzáférés kezelésére, [állítsa be a folyamathoz tartozó forgatókönyv (képzés vagy pontozás) engedélyeit](how-to-assign-roles.md#common-scenarios).

## <a name="next-steps"></a>Következő lépések

További információ:

> [!div class="nextstepaction"]
> [Azure Machine Learning folyamatok használata a Batch pontozáshoz](tutorial-pipeline-batch-scoring-classification.md)

* További információ a [folyamatokról](concept-ml-pipelines.md)
* További információ a [Azure Machine learning Jupyter való feltárásáról](samples-notebooks.md)

