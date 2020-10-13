---
title: Munkaterület-adatexportálás vagy-törlés
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan exportálhatja vagy törölheti a munkaterületet a Azure Machine Learning Studio, CLI, SDK és hitelesített REST API-k használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e847f62c2ae3d1d68c39685a38b67e1d0ada8c2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91251014"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Machine Learning szolgáltatás-munkaterület adatainak exportálása vagy törlése



Azure Machine Learning a munkaterület adatait a portál grafikus felületének vagy a Python SDK használatával exportálhatja vagy törölheti. Ez a cikk mindkét lehetőséget ismerteti.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>A munkaterület adatainak szabályozása

Az Azure Machine Learning által tárolt termékbeli adatkészletek exportálásra és törlésre is használhatók. Azure Machine Learning Studio, CLI és SDK használatával exportálhat és törölhet. A telemetria-adatok az Azure adatvédelmi portálján érhetők el. 

Azure Machine Learning a személyes adatok a futtatási előzmények dokumentumaiban felhasználói adatokból állnak. 

## <a name="delete-high-level-resources-using-the-portal"></a>Magas szintű erőforrások törlése a portál használatával

Munkaterületek létrehozásakor az Azure számos erőforrást hoz létre az erőforráscsoport alatt:

- Maga a munkaterület
- Egy tárfiókot
- Egy tároló-beállításjegyzék
- Application-adatáttekintési példány
- Key Vault

Ezeket az erőforrásokat törölheti, ha kiválasztja őket a listából, és kiválasztja a **Törlés** lehetőséget. 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Képernyőkép a portálról, a törlés ikon kiemelve":::

A futtatási előzményeket tartalmazó dokumentumok, amelyek személyes felhasználói adatokat is tartalmazhatnak, a blob Storage-ban található Storage-fiókban tárolódnak a-ben `/azureml` . Az adatok a portálról tölthetők le és törölhetők.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Képernyőkép a portálról, a törlés ikon kiemelve":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>Gépi tanulási erőforrások exportálása és törlése Azure Machine Learning Studio használatával

A Azure Machine Learning Studio egységes áttekintést nyújt a gépi tanulási erőforrásokról, például jegyzetfüzetekről, adatkészletekről, modellekről és kísérletekről. Azure Machine Learning Studio az adatok és kísérletek rekordjának megőrzését hangsúlyozza. A számítási erőforrások, például a folyamatok és a számítási erőforrások a böngésző használatával törölhetők. Ezekhez az erőforrásokhoz navigáljon a szóban forgó erőforráshoz, és válassza a **Törlés**lehetőséget. 

Az adatkészletek nem regisztrálhatók, és a kísérletek archiválása is lehetséges, de ezek a műveletek nem törlik az adatokat. Az adatkészletek és az adatfuttatások teljes eltávolításához a tárolási szinten törölni kell az adatokat. A tárolási szint törlését a portálon végezheti el, az előzőekben leírtak szerint.

A Studio használatával letölthetők a kísérleti futtatásokból származó tananyagok. Válassza ki a **kísérletet** és a **futtatást** , amelyben érdekli. Válassza a **kimenet + naplók** lehetőséget, és navigáljon a letölteni kívánt összetevőkhöz. Válassza a **...** és a **Letöltés**lehetőséget.

A regisztrált modell letöltéséhez navigáljon a kívánt **modellre** , és válassza a **Letöltés**lehetőséget. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Képernyőkép a portálról, a törlés ikon kiemelve":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Erőforrások exportálása és törlése a Python SDK használatával

Egy adott Futtatás kimeneteit a következők használatával töltheti le: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

A következő gépi tanulási erőforrások törölhetők a Python SDK használatával: 

| Típus | Függvény hívása | Jegyzetek | 
| --- | --- | --- |
| `Workspace` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete-delete-dependent-resources-false--no-wait-false-) | `delete-dependent-resources`A törlés kaszkádolt használatával |
| `Model` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | | 
| `ComputeTarget` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truedelete--) | |
| `WebService` | [`delete`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py&preserve-view=true) | | 

