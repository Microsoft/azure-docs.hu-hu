---
title: GitHub Actions a CI/CD-hez
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre GitHub-műveletek munkafolyamatot modell betanításához Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: b21f53f8ec76257fc19e0e30cd025ecc46ad2188
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218281"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>GitHub-műveletek használata Azure Machine Learning

Ismerkedjen meg a [GitHub-műveletekkel](https://docs.github.com/en/actions) Azure Machine learning-modell betanításához. 

> [!NOTE]
> A Azure Machine Learning GitHub-műveletei a következőként vannak megadva, és a Microsoft nem támogatja teljesen. Ha egy adott művelettel kapcsolatos problémákat tapasztal, nyisson meg egy problémát a tárházban a művelethez. Ha például problémát tapasztal a pénzmosás-telepítés művelettel kapcsolatban, jelentse a problémát a tárházban [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) .

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy GitHub-fiók. Ha még nem rendelkezik ilyennel, regisztráljon [ingyenesen](https://github.com/join).  

## <a name="workflow-file-overview"></a>A munkafolyamat-fájl áttekintése

A munkafolyamatot egy YAML-(. YML) fájl határozza meg a `/.github/workflows/` tárház elérési útjában. Ez a definíció a munkafolyamatot alkotó különböző lépéseket és paramétereket tartalmazza.

A fájl négy szakaszt tartalmaz:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Adjon meg egy szolgáltatásnevet. <br /> 2. hozzon létre egy GitHub-titkot. |
|**Kapcsolódás** | 1. kapcsolódjon a Machine learning-munkaterülethez. <br /> 2. kapcsolódás számítási célhoz. |
|**Futtatás** | 1. betanítási Futtatás. |
|**Telepítés** | 1. a modell regisztrálása Azure Machine Learning beállításjegyzékben. 1. A modell üzembe helyezése. |

## <a name="create-repository"></a>Tárház létrehozása

Hozzon létre egy új tárházat az [ml Ops-ből a GitHub-műveletekkel és Azure Machine learning sablonnal](https://github.com/machine-learning-apps/ml-template-azure). 

1. Nyissa meg a [sablont](https://github.com/machine-learning-apps/ml-template-azure) a githubon. 
2. Válassza **a sablon használata** lehetőséget. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Válassza a sablon használata lehetőséget":::
3. Hozzon létre egy új tárházat a sablonból. Adja meg az adattár nevét `ml-learning` vagy az Ön által választott nevet. 


## <a name="generate-deployment-credentials"></a>Központi telepítési hitelesítő adatok előállítása

Az [Azure CLI](/cli/azure/)-ben létrehozhat egy [egyszerű szolgáltatást](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) az az [ad SP Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) parancs használatával. Futtassa ezt a parancsot [Azure Cloud Shell](https://shell.azure.com/) a Azure Portalban, vagy kattintson a **TRY IT (kipróbálás** ) gombra.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés-AZONOSÍTÓra, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum, amely a szerepkör-hozzárendelés hitelesítő adatait tartalmazza, amelyek hozzáférést biztosítanak a App Service alkalmazáshoz az alábbihoz hasonló módon. A JSON-objektum másolása később.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>A GitHub-titok konfigurálása

1. A [githubon](https://github.com/)tallózzon a tárházban, válassza a **beállítások > titkok > új titok hozzáadása** lehetőséget.

2. Illessze be a teljes JSON-kimenetet az Azure CLI-parancsból a titok érték mezőjébe. Adja meg a titkot a nevet `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Kapcsolódás a munkaterülethez

A Azure Machine Learning munkaterülethez való kapcsolódáshoz használja a [Azure Machine learning-munkaterület műveletet](https://github.com/marketplace/actions/azure-machine-learning-workspace) . 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Alapértelmezés szerint a művelet egy `workspace.json` fájlt vár. Ha a JSON-fájl neve eltérő, megadhatja a `parameters_file` bemeneti paraméterrel. Ha nincs fájl, a rendszer egy újat hoz létre az adattár nevével.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
A művelet a munkaterület Azure Resource Manager (ARM) tulajdonságait egy konfigurációs fájlba írja, amelyet az összes jövőbeli Azure Machine Learning GitHub-művelet kiválaszt. A fájl a következő helyre lesz mentve: `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Kapcsolódás számítási célhoz Azure Machine Learning

Az [Azure Machine learning számítási művelettel](https://github.com/Azure/aml-compute) csatlakozhat egy számítási célhoz Azure Machine learning.  Ha a számítási cél létezik, a művelet csatlakozni fog hozzá. Ellenkező esetben a művelet létrehoz egy új számítási célt. A [pénzmosás-számítási művelet](https://github.com/Azure/aml-compute) csak az Azure ml számítási fürtöt és az Azure Kubernetes szolgáltatást (ak) támogatja. 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Betanítási futtatás elküldése

A [Azure Machine learning betanítási művelettel](https://github.com/Azure/aml-run) elküldheti a ScriptRun, a becsléseket vagy a folyamatokat Azure Machine learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Modell regisztrálása a beállításjegyzékben

A modell [Azure Machine learning regisztrálása művelettel](https://github.com/Azure/aml-registermodel) regisztrálhat egy modellt a Azure Machine Learningba.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Modell üzembe helyezése Azure Machine Learning az ACI-ban

A [Azure Machine learning központi telepítés művelettel](https://github.com/Azure/aml-deploy) modellt telepíthet, és létrehoz egy végpontot a modellhez. Az Azure Kubernetes Service-ben való üzembe helyezéshez használhatja a Azure Machine Learning üzembe helyezését is. Tekintse meg [ezt a minta munkafolyamatot](https://github.com/Azure-Samples/mlops-enterprise-template) az Azure Kubernetes Service-ben üzembe helyezett modellhez.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Példa befejezése

A modell betanítása és üzembe helyezése Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha az erőforráscsoport és a tárház már nem szükséges, a telepített erőforrások tisztításához törölje az erőforráscsoportot és a GitHub-tárházat. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gépi tanulási folyamatokat hozhat létre és futtathat Azure Machine Learning SDK-val](./how-to-create-machine-learning-pipelines.md)