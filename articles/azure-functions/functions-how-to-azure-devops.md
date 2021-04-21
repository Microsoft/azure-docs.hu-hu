---
title: Függvényalkalmazás kódszámának folyamatos frissítése az Azure DevOps használatával
description: Megtudhatja, hogyan állíthat be olyan Azure DevOps-folyamatot, amely Azure Functions.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
ms.openlocfilehash: a99e313a0c3fe9093137d4acaa64e789ef5e10e3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762208"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Folyamatos teljesítés az Azure DevOps használatával

A függvényt automatikusan üzembe helyezheti egy Azure Functions az [Azure Pipelines használatával.](/azure/devops/pipelines/)

A folyamat meghatározására két lehetőség közül választhat:

- **YAML-fájl:** A folyamatot egy YAML-fájl írja le. A fájlnak lehet egy build lépések szakasza és egy kiadási szakasza. A YAML-fájlnak és az alkalmazásnak ugyanabban az adattában kell lennie.
- **Sablon:** A sablonok használatra kész feladatok, amelyek az alkalmazást buildlik vagy telepítik.

## <a name="yaml-based-pipeline"></a>YAML-alapú folyamat

YAML-alapú folyamat létrehozásához először hozza létre az alkalmazást, majd telepítse az alkalmazást.

### <a name="build-your-app"></a>Az alkalmazás létrehozása

Az alkalmazás Azure Pipelinesban való buildének módja az alkalmazás programozási nyelvétől függ. Minden nyelv rendelkezik egy adott build lépésekkel, amelyek létrehoznak egy üzembe helyezési összetevőt. A függvényalkalmazás az Azure-ban való üzembe helyezéséhez egy üzembe helyezési összetevőt használ.

# <a name="c"></a>[C\#](#tab/csharp)

A következő minta használatával yaml-fájlt hozhat létre .NET-alkalmazás létrehozásához:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az alábbi minta segítségével yaml-fájlt hozhat létre JavaScript-alkalmazás létrehozásához:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

Az alábbi minták egyikével YAML-fájlt hozhat létre egy adott Python-verzióhoz készült alkalmazás létrehozásához. A Python csak a Linuxon futó függvényalkalmazások esetében támogatott.

**3.7-es verzió**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**3.6-os verzió**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Az alábbi minta segítségével létrehozhat egy YAML-fájlt egy PowerShell-alkalmazás becsomagolához. A PowerShell csak Windows rendszerű Azure Functions.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Az üzemeltetési operációs rendszertől függően az alábbi YAML-minták egyikét kell tartalmaznia a YAML-fájlban.

#### <a name="windows-function-app"></a>Windows-függvényalkalmazás

A windowsos függvényalkalmazások üzembe helyezéséhez a következő kódrészletet használhatja:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux-függvényalkalmazás

A Linux-függvényalkalmazás üzembe helyezéséhez a következő kódrészletet használhatja:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Sablonalapú folyamat

Az Azure DevOps-sablonok előre definiált feladatcsoportok, amelyek egy alkalmazást hoznak létre vagy helyeznek üzembe.

### <a name="build-your-app"></a>Az alkalmazás létrehozása

Az alkalmazás Azure Pipelinesban való buildének módja az alkalmazás programozási nyelvétől függ. Minden nyelvhez külön összeállítási lépések vannak, amelyek egy üzembe helyezési összetevőt hoznak létre. Az üzembe helyezési összetevővel frissítheti a függvényalkalmazást az Azure-ban.

Ha beépített buildsablonokat használ, új build-folyamat létrehozásakor válassza a Use the **classic editor** to create a pipeline by using designer templates (Folyamat létrehozása tervezősablonokkal a klasszikus szerkesztővel) lehetőséget.

![Az Azure Pipelines klasszikus szerkesztőjének kiválasztása](media/functions-how-to-azure-devops/classic-editor.png)

A kód forrásának konfigurálása után keresse meg a Azure Functions buildsablonokat. Válassza ki az alkalmazás nyelvének megfelelő sablont.

![Válasszon ki egy Azure Functions-sablont](media/functions-how-to-azure-devops/build-templates.png)

Bizonyos esetekben a build-összetevők egy adott mappastruktúrával is rendelkezik. Előfordulhat, hogy be kell választania a **Prepend root folder name to archive paths (Gyökérmappa** neve az archiváláshoz archivált elérési utakhoz) jelölőnégyzetet.

![A gyökérmappa nevének előtagként való be- és be-](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-alkalmazások

Ha a JavaScript-alkalmazás natív Windows-moduloktól függ, frissítenie kell az ügynökkészlet verzióját **Üzemeltetett VS2017 verzióra.**

![Az ügynökkészlet verziójának frissítése](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Az alkalmazás üzembe helyezése

Új kiadási folyamat létrehozásakor keresse meg a Azure Functions kiadási sablont.

![Keresse meg a Azure Functions kiadási sablont](media/functions-how-to-azure-devops/release-template.png)

Az üzembe helyezés az üzembe helyezési tárolóhelyen nem támogatott a kiadási sablonban.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Build folyamat létrehozása az Azure CLI használatával

Build folyamat Azure-ban való létrehozásához használja a `az functionapp devops-pipeline create` [parancsot.](/cli/azure/functionapp/devops-pipeline#az_functionapp_devops_pipeline_create) A build folyamatának létrehozása az adattában végrehajtott kódváltozások buildlését és kiadását jelenti. A parancs létrehoz egy új YAML-fájlt, amely meghatározza a build- és kiadási folyamatot, majd véglegesít az adattáraban. A parancs előfeltételei a kód helyétől függnek.

- Ha a kód a GitHubon található:

    - Az előfizetéséhez **írási** engedéllyel kell rendelkeznie.

    - Önnek kell a projekt rendszergazdájának lennie az Azure DevOpsban.

    - Rendelkeznie kell a megfelelő engedélyekkel rendelkező személyes GitHub hozzáférési jogkivonat (PAT) létrehozásához szükséges engedélyekkel. További információ: [A GitHub PAT engedélykövetelményei.](/azure/devops/pipelines/repos/github#repository-permissions-for-personal-access-token-pat-authentication)

    - Az automatikusan létrehozott YAML-fájl véglegesítéshez rendelkeznie kell a GitHub-adattár fő ágában való véglegesítéshez szükséges engedélyekkel.

- Ha a kód az Azure Reposban van:

    - Az előfizetéséhez **írási** engedéllyel kell rendelkeznie.

    - Önnek kell a projekt rendszergazdájának lennie az Azure DevOpsban.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [Azure Functions áttekintését.](functions-overview.md)
- Tekintse át az [Azure DevOps áttekintését.](/azure/devops/pipelines/)