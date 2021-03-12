---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102623331"
---
* Új munkaterület létrehozásakor automatikusan létrehozhatók a munkaterület által igényelt szolgáltatások, vagy meglévő szolgáltatások is használhatók. Ha a __meglévő szolgáltatásokat más Azure-előfizetésből__ kívánja használni, mint a munkaterületet, regisztrálnia kell a Azure Machine learning névteret a szolgáltatásokat tartalmazó előfizetésben. Ha például létrehoz egy olyan munkaterületet az előfizetésben, amely a B előfizetéshez tartozó Storage-fiókot használ, akkor a Azure Machine Learning névteret regisztrálni kell a B előfizetésben, mielőtt a Storage-fiókot a munkaterülethez használhassa.

    A Azure Machine Learning erőforrás-szolgáltatója a __Microsoft. MachineLearningServices__. Az [Azure Resource Providers és types](../articles/azure-resource-manager/management/resource-providers-and-types.md)  című cikkből megtudhatja, hogyan tekintheti meg, hogy regisztrálva van-e, és hogyan regisztrálhat.

    > [!IMPORTANT]
    > Ez csak a munkaterület létrehozása során biztosított erőforrásokra vonatkozik; Azure Storage-fiókok, Azure Container register, Azure Key Vault és Application Insights.
