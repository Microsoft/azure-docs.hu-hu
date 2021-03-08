---
title: Társított szolgáltatás létrehozása szinapszis és Azure Machine Learning munkaterületekkel (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kapcsolhatja össze az Azure Szinapszist és Azure Machine Learning munkaterületeket egy egységes adatkezelési huzavona-élményhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 8941a7f7a27f6ffe58cda3f0bf2c6833ec226783
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456235"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Az Azure szinapszis Analytics és Azure Machine Learning munkaterületek (előzetes verzió) összekapcsolása

Ebből a cikkből megtudhatja, hogyan hozhat létre egy társított szolgáltatást, amely összeköti az [Azure szinapszis Analytics](/synapse-analytics/overview-what-is.md) -munkaterületet és [Azure Machine learning munkaterületet](concept-workspace.md).

Ha az Azure szinapszis-munkaterülethez társított Azure Machine Learning-munkaterülettel rendelkezik, a skálán egy Apache Spark-készletet is csatolhat az adatok huzavona, és az azonos jegyzetfüzetből végezheti el a modell betanítását.

A ML-munkaterületet és a szinapszis-munkaterületet a [PYTHON SDK](#link-sdk) -val vagy a [Azure Machine learning Studióval](#link-studio)is összekapcsolhatja.

Emellett munkaterületeket is csatolhat, és egy szinapszis Spark-készletet is csatolhat egyetlen [Azure Resource Manager (ARM) sablonnal](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json).

>[!IMPORTANT]
> A Azure Machine Learning és az Azure szinapszis-integráció nyilvános előzetes verzióban érhető el. A csomagban bemutatott funkciók `azureml-synapse` [kísérleti](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py#stable-vs-experimental) előzetes funkciók, és bármikor megváltozhatnak.

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Azure Machine learning munkaterületet](how-to-manage-workspace.md?tabs=python).

* [Hozzon létre egy szinapszis-munkaterületet Azure Portal](/synapse-analytics/quickstart-create-workspace.md).

* [Apache Spark-készlet létrehozása a Azure Portal, a web Tools vagy a szinapszis Studio használatával](/synapse-analytics/quickstart-create-apache-spark-pool-portal.md)

* A [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) telepítése

* Hozzáférés a [Azure Machine learning studióhoz](https://ml.azure.com/).

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Munkaterületek összekapcsolása a Python SDK-val

> [!IMPORTANT]
> A szinapszis munkaterülethez való sikeres hivatkozáshoz a szinapszis munkaterület **tulajdonosi** szerepkörét kell megadnia. Győződjön meg arról, hogy a [Azure Portal](https://ms.portal.azure.com/)a hozzáférését.
>
> Ha Ön nem **tulajdonosa** , és csak a szinapszis munkaterülethez **járul hozzá** , akkor csak a meglévő társított szolgáltatásokat használhatja. Tekintse meg a [meglévő társított szolgáltatás](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)felolvasása és használata című témakört.

A következő kód a [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice?preserve-view=true&view=azure-ml-py) és osztályokat alkalmazza a alkalmazásra [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration?preserve-view=true&view=azure-ml-py) ,

* Kapcsolja össze a Machine learning-munkaterületet az Azure szinapszis-munkaterülettel `ws` .
* A szinapszis-munkaterület regisztrálása a Azure Machine Learning társított szolgáltatásként.

``` python
import datetime  
from azureml.core import Workspace, LinkedService, SynapseWorkspaceLinkedServiceConfiguration

# Azure Machine Learning workspace
ws = Workspace.from_config()

#link configuration 
synapse_link_config = SynapseWorkspaceLinkedServiceConfiguration(
    subscription_id=ws.subscription_id,
    resource_group= 'your resource group',
    name='mySynapseWorkspaceName')

# Link workspaces and register Synapse workspace in Azure Machine Learning
linked_service = LinkedService.register(workspace = ws,              
                                            name = 'synapselink1',    
                                            linked_service_config = synapse_link_config)
```

> [!IMPORTANT] 
> Felügyelt identitás `system_assigned_identity_principal_id` jön létre az egyes társított szolgáltatásokhoz. Ezt a felügyelt identitást meg kell adni a szinapszis munkaterülethez tartozó **szinapszis Apache Spark rendszergazdai** szerepkörnek, mielőtt elkezdené a szinapszis-munkamenetet. [Rendelje hozzá a szinapszis Apache Spark rendszergazdai szerepkört a kezelt identitáshoz a szinapszis Studióban](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md).
>
> Egy adott társított szolgáltatás megkereséséhez használja a következőt: `system_assigned_identity_principal_id` `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` .

### <a name="manage-linked-services"></a>Társított szolgáltatások kezelése

A Machine learning-munkaterülethez kapcsolódó összes társított szolgáltatás megtekintése.

```python
LinkedService.list(ws)
```

A munkaterületek leválasztásához használja a `unregister()` metódust.

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Munkaterületek összekapcsolása a Studio használatával

Kapcsolja össze a Machine learning-munkaterületet és a szinapszis-munkaterületet a Azure Machine Learning Studio használatával a következő lépésekkel: 

1. Jelentkezzen be a [Azure Machine learning studióba](https://ml.azure.com/).
1. A bal oldali ablaktábla **kezelés** szakaszában válassza a **társított szolgáltatások** elemet.
1. Válassza az **integráció hozzáadása** elemet.
1. A **hivatkozás munkaterület** űrlapon töltse ki a mezőket 
    Mező| Leírás    
    ---|---
    Név| Adja meg a társított szolgáltatás nevét. Ezt a nevet fogja használni az adott társított szolgáltatásra való hivatkozáshoz.
    Előfizetés neve | Válassza ki a Machine learning-munkaterülethez társított előfizetés nevét. 
    Szinapszis-munkaterület | Válassza ki azt a szinapszis-munkaterületet, amelyhez hivatkozni kíván.
1. Kattintson a **tovább** gombra a **Spark-készletek kiválasztása (opcionális)** űrlap megnyitásához. Ezen az űrlapon kiválaszthatja, hogy melyik szinapszis Spark-készletet szeretné csatolni a munkaterülethez

1. A **tovább** gombra kattintva nyissa meg a **felülvizsgálati** űrlapot, és jelölje ki a kívánt beállításokat.
1. Válassza a **Létrehozás** lehetőséget a társított szolgáltatás-létrehozási folyamat befejezéséhez.

## <a name="next-steps"></a>Következő lépések

* [Szinapszis Spark-készletek csatlakoztatása adatelőkészítéshez az Azure szinapszis (előzetes verzió)](how-to-data-prep-synapse-spark-pool.md)szolgáltatással.
* [A gépi tanulási folyamat Apache Spark használata az Azure szinapszis (előzetes verzió) használatával](how-to-use-synapsesparkstep.md)
* [Modell betanítása](how-to-set-up-training-targets.md).
