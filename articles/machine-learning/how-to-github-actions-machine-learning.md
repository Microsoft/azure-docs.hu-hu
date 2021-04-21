---
title: GitHub Actions a CI/CD-hez
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre GitHub Actions munkafolyamatot egy modell betanítása Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 6505523aa367eaf202ece81a4253429e864e169a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780370"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>A GitHub Actions használata Azure Machine Learning

Első lépések a [GitHub Actions](https://docs.github.com/en/actions) modellek betanításában a Azure Machine Learning. 

> [!NOTE]
> GitHub Actions a Azure Machine Learning az adatokat a rendszer adott ként biztosítja, és a Microsoft nem támogatja teljes mértékben. Ha egy adott művelet során problémákba ütközik, nyisson meg egy problémát a művelet adattárában. Ha például problémába ütközik az aml-deploy művelet során, jelentse a problémát az [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) objektumban.

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Egy GitHub-fiók. Ha még nincs ilyen szolgáltatása, regisztráljon [ingyenesen.](https://github.com/join)  

## <a name="workflow-file-overview"></a>Munkafolyamat-fájl áttekintése

A munkafolyamatokat az adattár elérési útján található YAML- (.yml-) fájl `/.github/workflows/` határozza meg. Ez a definíció a munkafolyamatot felhozó különböző lépéseket és paramétereket tartalmazza.

A fájl négy szakaszból áll:

|Section  |Feladatok  |
|---------|---------|
|**Hitelesítés** | 1. Szolgáltatásnév meghatározása. <br /> 2. Hozzon létre egy titkos GitHub-et. |
|**Kapcsolódás** | 1. Csatlakozás a Machine Learning-munkaterülethez. <br /> 2. Csatlakozás számítási célhoz. |
|**Futtatás** | 1. Betanítás futtatása. |
|**Telepítés** | 1. A modell regisztrálása Azure Machine Learning beállításjegyzékben. 1. A modell üzembe helyezése. |

## <a name="create-repository"></a>Adattár létrehozása

Hozzon létre egy új adattárat az ML-műveletekből az GitHub Actions [és Azure Machine Learning sablonnal.](https://github.com/machine-learning-apps/ml-template-azure) 

1. Nyissa meg [a sablont](https://github.com/machine-learning-apps/ml-template-azure) a GitHubon. 
2. Válassza **a Sablon használata lehetőséget.** 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Válassza a sablon használata lehetőséget":::
3. Hozzon létre egy új adattárat a sablonból. Állítsa az adattár nevét a vagy a választott `ml-learning` névre. 


## <a name="generate-deployment-credentials"></a>Üzembe helyezési hitelesítő adatok létrehozása

Szolgáltatásnév az [](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [az ad sp create-for-rbac paranccsal](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) hozható létre az [Azure CLI-n.](/cli/azure/) Futtassa ezt a [Azure Cloud Shell](https://shell.azure.com/) a Azure Portal vagy a **Kipróbálom gombra kattintva.**

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

A fenti példában cserélje le a helyőrzőket az előfizetés azonosítójára, az erőforráscsoport nevére és az alkalmazás nevére. A kimenet egy JSON-objektum a szerepkör-hozzárendelési hitelesítő adatokkal, amelyek hozzáférést biztosítanak App Service alkalmazáshoz az alábbihoz hasonlóan. Másolja ezt a JSON-objektumot későbbire.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>A GitHub titkos kód konfigurálása

1. A [GitHubon](https://github.com/)keresse meg az adattárat, és válassza a Settings > Secrets > Add a new secret (Új titkos kód **hozzáadása) lehetőséget.**

2. Illessze be az Azure CLI-parancs teljes JSON-kimenetét a titkos gombra. Adja a titkos nak a `AZURE_CREDENTIALS` nevet.

## <a name="connect-to-the-workspace"></a>Csatlakozás a munkaterülethez

Az Azure Machine Learning-munkaterület [művelettel csatlakozhat](https://github.com/marketplace/actions/azure-machine-learning-workspace) a Azure Machine Learning munkaterületéhez. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Alapértelmezés szerint a művelet egy fájlt `workspace.json` vár. Ha a JSON-fájl neve más, megadhatja a bemeneti `parameters_file` paraméterrel. Ha nincs fájl, a rendszer létrehoz egy újat az adattár nevével.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
A művelet a munkaterület Azure Resource Manager (ARM) tulajdonságait egy konfigurációs fájlba írja, amelyet az összes későbbi Azure Machine Learning GitHub Actions. A fájl a következőbe lesz `GITHUB_WORKSPACE/aml_arm_config.json` mentve: . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Csatlakozás számítási célhoz a Azure Machine Learning

Az [Azure Machine Learning Compute művelet használatával](https://github.com/Azure/aml-compute) csatlakozhat egy számítási célhoz a Azure Machine Learning.  Ha a számítási cél létezik, a művelet csatlakozni fog hozzá. Ellenkező esetben a művelet létrehoz egy új számítási célt. Az [AML Compute művelet](https://github.com/Azure/aml-compute) csak az Azure ML számítási fürtöt és az Azure Kubernetes Service (AKS) támogatja. 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Betanítási futtatás elküldése

A [Azure Machine Learning Betanítás művelet használatával](https://github.com/Azure/aml-run) küldjön el egy ScriptRun, egy estimator vagy egy folyamat Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Modell regisztrálása a beállításjegyzékben

A modell [Azure Machine Learning regisztrálása művelet használatával](https://github.com/Azure/aml-registermodel) regisztrálhat egy modellt a Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Modell üzembe helyezése az ACI Azure Machine Learning ban

A [Azure Machine Learning Üzembe helyezés művelet](https://github.com/Azure/aml-deploy) használatával üzembe helyezhet egy modellt, és létrehozhat egy végpontot a modellhez. A Deploy (Üzembe helyezés) Azure Machine Learning is használhatja a Azure Kubernetes Service. Tekintse meg ezt a [minta-munkafolyamatot](https://github.com/Azure-Samples/mlops-enterprise-template) egy olyan modellhez, amely a Azure Kubernetes Service.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Teljes példa

Betanítja a modellt, és üzembe Azure Machine Learning. 

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

Ha már nincs szüksége az erőforráscsoportra és az adattárra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport és a GitHub-adattár törlésével. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gépi tanulási folyamatok létrehozása és futtatása Azure Machine Learning SDK-val](./how-to-create-machine-learning-pipelines.md)