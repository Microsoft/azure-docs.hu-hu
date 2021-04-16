---
title: Csatolt szolgáltatás létrehozása a Synapse és a Azure Machine Learning (előzetes verzió) használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan kapcsolható Azure Synapse és Azure Machine Learning az egységes adatrendezéshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.reviewer: nibaccam
ms.date: 03/08/2021
ms.custom: how-to, devx-track-python, data4ml, synapse-azureml
ms.openlocfilehash: 23184eee67013e39400446db5f744dd0ddb7bc50
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575735"
---
# <a name="link-azure-synapse-analytics-and-azure-machine-learning-workspaces-preview"></a>Munkaterületek Azure Synapse Analytics és Azure Machine Learning (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan hozhat létre olyan csatolt szolgáltatást, amely összekapcsolja a Azure Synapse Analytics [és](/azure/synapse-analytics/overview-what-is) a [Azure Machine Learning munkaterülettel.](concept-workspace.md)

Ha az Azure Machine Learning-munkaterülete össze van kapcsolva az Azure Synapse-munkaterülettel, csatlakoztathat egy Apache Spark-készletet dedikált számítási kapacitásként a nagy léptékű adatrendezéshez, vagy modellbeképezést is folytathat ugyanabba a Python-jegyzetfüzetbe.

Az ML-munkaterületet és a Synapse-munkaterületet a [Python SDK](#link-sdk) vagy a [Azure Machine Learning stúdió.](#link-studio)

Munkaterületeket is összekapcsolhat, és egy Synapse Spark-készletet csatolhat egyetlen Azure Resource Manager [(ARM) sablonnal.](https://github.com/Azure/azure-quickstart-templates/blob/master/101-machine-learning-linkedservice-create/azuredeploy.json)

>[!IMPORTANT]
> A Azure Machine Learning és Azure Synapse integráció nyilvános előzetes verzióban érhető el. A csomagból bemutatott funkciók kísérleti előzetes verziójú funkciók, és `azureml-synapse` bármikor változhatnak. [](/python/api/overview/azure/ml/#stable-vs-experimental)

## <a name="prerequisites"></a>Előfeltételek

* [Hozzon létre egy Azure Machine Learning munkaterületet.](how-to-manage-workspace.md?tabs=python)

* [Synapse-munkaterület létrehozása a Azure Portal.](/azure/synapse-analytics/quickstart-create-workspace)

* [Hozzon Apache Spark készletet Azure Portal, webes eszközök vagy Synapse Studio](/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio)

* Az [Azure Machine Learning Python SDK telepítése](/python/api/overview/azure/ml/intro)

* Hozzáférés az [Azure Machine Learning stúdió.](https://ml.azure.com/)

<a name="link-sdk"></a>
## <a name="link-workspaces-with-the-python-sdk"></a>Munkaterületek összekapcsolása a Python SDK-val

> [!IMPORTANT]
> Ahhoz, hogy sikeresen hivatkozni tudja a Synapse-munkaterületre, tulajdonosi szerepkört kell biztosítani Önnek a Synapse-munkaterülethez.  Ellenőrizze a hozzáférését a [Azure Portal.](https://ms.portal.azure.com/)
>
> Ha Ön nem **tulajdonos,** és csak **közreműködője** a Synapse-munkaterületnek, csak a meglévő csatolt szolgáltatásokat használhatja. Tekintse meg, hogyan [lehet lekért és használni egy meglévő csatolt szolgáltatást.](how-to-data-prep-synapse-spark-pool.md#get-an-existing-linked-service)

A következő kód a és [`LinkedService`](/python/api/azureml-core/azureml.core.linked_service.linkedservice) [`SynapseWorkspaceLinkedServiceConfiguration`](/python/api/azureml-core/azureml.core.linked_service.synapseworkspacelinkedserviceconfiguration) osztályt alkalmazza a következőkre:

* Csatolja a Machine Learning-munkaterületet `ws` a Azure Synapse munkaterülethez.
* A Synapse-munkaterület regisztrálása Azure Machine Learning szolgáltatásként.

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
> Minden csatolt szolgáltatáshoz létrejön egy felügyelt `system_assigned_identity_principal_id` identitás (). Ennek a felügyelt identitásnak a **Synapse-munkaterület Synapse** Apache Spark-rendszergazdai szerepkörét kell biztosítani, mielőtt elindítja a Synapse-munkamenetet. [Rendelje hozzá a Synapse Apache Spark rendszergazdai szerepkört](../synapse-analytics/security/how-to-manage-synapse-rbac-role-assignments.md)a felügyelt identitáshoz a Synapse Studio.
>
> Egy adott `system_assigned_identity_principal_id` összekapcsolt szolgáltatáshoz a használatával találhatja meg `LinkedService.get('<your-mlworkspace-name>', '<linked-service-name>')` a következőt: .

### <a name="manage-linked-services"></a>Csatolt szolgáltatások kezelése

Az összes megtekintése gépi tanulási munkaterülethez társított társított szolgáltatásokat.

```python
LinkedService.list(ws)
```

A munkaterületek leválasztása a `unregister()` metódussal

``` python
linked_service.unregister()
```

<a name="link-studio"></a>
## <a name="link-workspaces-via-studio"></a>Munkaterületek összekapcsolása a Studióban

Csatolja a Machine Learning-munkaterületet és a Synapse-munkaterületet Azure Machine Learning stúdió következő lépésekkel: 

1. Jelentkezzen be a [Azure Machine Learning stúdió.](https://ml.azure.com/)
1. A **bal oldali panel** Kezelés szakaszában válassza a Linked Services (Csatolt szolgáltatások) lehetőséget. 
1. Válassza **az Integráció hozzáadása lehetőséget.**
1. A Munkaterület **csatolása űrlapon** töltse ki a mezőket

    |Mező| Leírás    
    |---|---
    |Név| Adja meg a csatolt szolgáltatás nevét. Ez a név lesz használva az adott hivatkozott szolgáltatásra való hivatkozáshoz.
    |Előfizetés neve | Válassza ki a Machine Learning-munkaterülethez társított előfizetés nevét. 
    |Synapse-munkaterület | Válassza ki azt a Synapse-munkaterületet, amelyhez hivatkozni szeretne.
    
1. Kattintson **a Tovább** gombra a **Spark-készletek kiválasztása (nem kötelező) űrlap megnyitásához.** Ezen az űrlapon kiválaszthatja, hogy melyik Synapse Spark-készletet csatolja a munkaterülethez

1. Kattintson **a Tovább** gombra az Áttekintés űrlap **megnyitásához** és a kijelölések ellenőrzéshez.
1. A **csatolt szolgáltatás** létrehozásának befejezéséhez válassza a Létrehozás lehetőséget.

## <a name="next-steps"></a>Következő lépések

* [Synapse Spark-készletek csatolása az adatok előkészítéséhez Azure Synapse (előzetes verzió) gombra.](how-to-data-prep-synapse-spark-pool.md)
* [Az Apache Spark használata a gépi tanulási folyamatban Azure Synapse (előzetes verzió)](how-to-use-synapsesparkstep.md)
* [Betanít egy modellt.](how-to-set-up-training-targets.md)
