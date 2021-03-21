---
title: Modellek üzembe helyezése számítási példányokhoz
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan helyezheti üzembe a Azure Machine Learning modelleket webszolgáltatásként számítási példányok használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: mnark
author: MrudulaN
ms.reviewer: larryfr
ms.date: 03/05/2020
ms.openlocfilehash: d8b1c2a5384e479e39d169d368554f16c300a33e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954546"
---
# <a name="deploy-a-model-to-azure-machine-learning-compute-instances"></a>Modell üzembe helyezése Azure Machine Learning számítási példányok számára



Ismerje meg, hogyan helyezhet üzembe egy modellt webszolgáltatásként a Azure Machine Learning számítási példányon Azure Machine Learning használatával. Használjon számítási példányokat, ha a következő feltételek egyike igaz:

- Gyorsan üzembe kell helyeznie és ellenőriznie kell a modellt.
- A fejlesztés alatt álló modellt tesztel.

> [!TIP]
> Egy modell üzembe helyezése egy számítási példányon lévő Jupyter Notebook, egy virtuális gépen lévő webszolgáltatás esetében egy _helyi telepítés_. Ebben az esetben a "helyi" számítógép a számítási példány. További információ a központi telepítésekről: [modellek üzembe helyezése a Azure Machine Learningsal](how-to-deploy-and-where.md).

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning munkaterületen futó számítási példány. További információ: [telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md).

## <a name="deploy-to-the-compute-instances"></a>Üzembe helyezés a számítási példányokon

A számítási példány tartalmaz egy példaként szolgáló jegyzetfüzetet, amely bemutatja a helyi központi telepítéseket. A következő lépésekkel töltse be a jegyzetfüzetet, és telepítse a modellt webszolgáltatásként a virtuális gépen:

1. [Azure Machine learning studióból](https://ml.azure.com)válassza a "jegyzetfüzetek" lehetőséget, majd válassza a How-to-use-azureml/Deployment/Deploy-to-local/Register-Model-Deploy-local. ipynb elemet a "minta notebookok" területen. A jegyzetfüzet klónozása a felhasználói mappába.

1. Keresse meg az 1. lépésben klónozott jegyzetfüzetet, válasszon ki vagy hozzon létre egy számítási példányt a jegyzetfüzet futtatásához.

    ![Képernyőkép a futó helyi szolgáltatásról a notebookon](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service.png)


1. A jegyzetfüzet megjeleníti azt az URL-címet és portot, amelyen a szolgáltatás fut. Például: `https://localhost:6789`. A portot tartalmazó cellát is futtathatja a `print('Local service port: {}'.format(local_service.port))` port megjelenítéséhez.

    ![Képernyőkép a futó helyi szolgáltatás portról](./media/how-to-deploy-local-container-notebook-vm/deploy-local-service-port.png)

1. A szolgáltatás számítási példányból való teszteléséhez használja az `https://localhost:<local_service.port>` URL-címet. Távoli ügyfélről történő teszteléshez szerezze be a számítási példányon futó szolgáltatás nyilvános URL-címét. A nyilvános URL-cím a következő képlet használatával határozható meg: 
    * Jegyzetfüzet virtuális gép: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.notebooks.azureml.net/score` . 
    * Számítási példány: `https://<vm_name>-<local_service_port>.<azure_region_of_workspace>.instances.azureml.net/score` . 

    Példa: 
    * Jegyzetfüzet virtuális gép: `https://vm-name-6789.northcentralus.notebooks.azureml.net/score` 
    * Számítási példány: `https://vm-name-6789.northcentralus.instances.azureml.net/score`

## <a name="test-the-service"></a>A szolgáltatás tesztelése

Mintaadatok küldéséhez a futó szolgáltatáshoz használja a következő kódot. Cserélje le a értéket az `service_url` előző lépés URL-címére:

> [!NOTE]
> Ha a számítási példányon végzett központi telepítésre végez hitelesítést, a hitelesítés Azure Active Directory használatával történik. A példában szereplő hívás a `interactive_auth.get_authentication_header()` HRE használatával hitelesíti a szolgáltatást, és egy olyan fejlécet ad vissza, amely a számítási példányon a szolgáltatásban történő hitelesítéshez használható. További információ: [Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](how-to-setup-authentication.md#interactive-authentication).
>
> Ha az Azure Kubernetes Service-ben vagy a Azure Container Instances-ban üzemelő példányra végzi a hitelesítést, a rendszer egy másik hitelesítési módszert használ. További információért lásd: a [webszolgáltatásként üzembe helyezett Azure-beli gépi modellek hitelesítésének konfigurálása](how-to-authenticate-web-service.md).

```python
import requests
import json
from azureml.core.authentication import InteractiveLoginAuthentication

# Get a token to authenticate to the compute instance from remote
interactive_auth = InteractiveLoginAuthentication()
auth_header = interactive_auth.get_authentication_header()

# Create and submit a request using the auth header
headers = auth_header
# Add content type header
headers.update({'Content-Type':'application/json'})

# Sample data to send to the service
test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

# Replace with the URL for your compute instance, as determined from the previous section
service_url = "https://vm-name-6789.northcentralus.notebooks.azureml.net/score"
# for a compute instance, the url would be https://vm-name-6789.northcentralus.instances.azureml.net/score
resp = requests.post(service_url, test_sample, headers=headers)
print("prediction:", resp.text)
```

## <a name="next-steps"></a>Következő lépések

* [Modell üzembe helyezése egyéni Docker-rendszerkép használatával](how-to-deploy-custom-docker-image.md)
* [Üzembe helyezés hibaelhárítása](how-to-troubleshoot-deployment.md)
* [TLS használata webszolgáltatás védelméhez az Azure Machine Learning szolgáltatás segítségével](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett ML-modell felhasználása](how-to-consume-web-service.md)
* [A Azure Machine Learning modellek monitorozása a Application Insights](how-to-enable-app-insights.md)
* [Adatok gyűjtése a termelési modellekhez](how-to-enable-data-collection.md)
