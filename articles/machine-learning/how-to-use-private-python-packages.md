---
title: Privát Python-csomagok használata
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan dolgozhat biztonságosan a Azure Machine Learning-környezetekben található privát Python-csomagokkal.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 5cc1025528959f8c81aa80703e3fc26d59c5266f
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618100"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Privát Python-csomagok használata Azure Machine Learning


Ebből a cikkből megtudhatja, hogyan használhatók biztonságosan a privát Python-csomagok Azure Machine Learningon belül. A privát Python-csomagokhoz a következő esetekben használhat példákat:

 * Olyan privát csomagot fejlesztett ki, amelyet nyilvánosan nem szeretne megosztani.
 * A vállalati tűzfalon belül tárolt csomagok válogatott tárházát kívánja használni.

Az ajánlott módszer attól függ, hogy van-e néhány csomag egyetlen Azure Machine Learning-munkaterülethez, vagy a csomagok teljes tárháza a szervezeten belüli összes munkaterülethez.

A privát csomagokat a [környezeti](/python/api/azureml-core/azureml.core.environment.environment) osztály használja. A környezeten belül deklarálhatja, hogy mely Python-csomagokat szeretné használni, beleértve a magánjellegűket is. Az általános Azure Machine Learning környezetének megismeréséhez tekintse meg a [környezetek használatát](how-to-use-environments.md)ismertető témakört. 

## <a name="prerequisites"></a>Előfeltételek

 * A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/install)
 * [Azure Machine learning munkaterület](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Kis számú csomagot használhat a fejlesztéshez és a teszteléshez

Ha kis mennyiségű privát csomagot használ egy adott munkaterülethez, használja a statikus [`Environment.add_private_pip_wheel()`](/python/api/azureml-core/azureml.core.environment.environment#add-private-pip-wheel-workspace--file-path--exist-ok-false-) módszert. Ezzel a módszerrel gyorsan hozzáadhat egy privát csomagot a munkaterülethez, és kiválóan alkalmas fejlesztési és tesztelési célokra.

Mutasson a fájl elérési útja argumentumra egy helyi kerék fájlra, és futtassa a ```add_private_pip_wheel``` parancsot. A parancs egy URL-címet ad vissza, amellyel nyomon követheti a csomag helyét a munkaterületen belül. Rögzítse a tárolási URL-címet, és adja át a `add_pip_package()` metódusnak.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Belsőleg a Azure Machine Learning szolgáltatás a biztonságos SAS URL-cím alapján cseréli le az URL-címet, így a Wheel-fájl privát és biztonságos marad.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Csomagok tárházának használata az Azure DevOps-hírcsatornából

Ha aktívan fejleszt Python-csomagokat a Machine learning-alkalmazáshoz, az Azure DevOps-tárházban tárolhatja őket, és hírcsatornáként közzéteheti őket. Ez a módszer lehetővé teszi, hogy integrálja a DevOps munkafolyamatot a csomagok létrehozásához a Azure Machine Learning-munkaterület. A Python-hírcsatornák Azure DevOps való beállításának megismeréséhez olvassa el [a Python-csomagok használatának első lépései az Azure-](/azure/devops/artifacts/quickstarts/python-packages) összetevőkben című témakört.

Ez a módszer személyes hozzáférési tokent használ a tárházon végzett hitelesítéshez. Ugyanez a módszer a jogkivonat-alapú hitelesítéssel, például a privát GitHub-adattárakkal rendelkező más adattárakra is érvényes. 

 1. [Hozzon létre egy személyes hozzáférési jogkivonatot (Pat)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?tabs=preview-page#create-a-pat) az Azure DevOps-példányhoz. Állítsa be a jogkivonat hatókörét __csomagolási > olvasásra__. 

 2. Adja hozzá az Azure DevOps URL-címét és a PAT as munkaterület-tulajdonságokat a [Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-) metódus használatával.

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Hozzon létre egy Azure Machine Learning-környezetet, és adjon hozzá Python-csomagokat a hírcsatornából.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

A környezet most már készen áll a futtatásra vagy a webszolgáltatás-végpontok üzembe helyezésére. A környezet létrehozásakor Azure Machine Learning szolgáltatás a PAT használatával hitelesíti a hírcsatornát a megfelelő alap URL-címmel.

## <a name="use-a-repository-of-packages-from-private-storage"></a>Csomagok tárházának használata a privát tárolóból

Az Azure Storage-fiókból csomagokat használhat a szervezet tűzfalán belül. A Storage-fiókban tárolhatók a csomagok vagy a nyilvánosan elérhető csomagok belső tükrözései.

A magánhálózati tároló beállításához lásd: [Azure Machine learning munkaterület biztonságossá tétele és a kapcsolódó erőforrások](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints). A [VNet mögé kell helyeznie a Azure Container Registry (ACR)](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr)is.

> [!IMPORTANT]
> Ezt a lépést kell végrehajtania ahhoz, hogy a privát csomag adattárával be tudja tanítani vagy üzembe lehessen helyezni a modelleket.

A konfigurációk befejezése után hivatkozhat a Azure Machine Learning környezeti definícióban található csomagokra az Azure Blob Storage teljes URL-címével.

## <a name="next-steps"></a>Következő lépések

 * További információ a [Azure Machine learning vállalati biztonságáról](concept-enterprise-security.md)