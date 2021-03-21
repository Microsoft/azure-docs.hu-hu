---
title: A DevOps használata adatfeldolgozási folyamathoz
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alkalmazhat DevOps-eljárásokat egy adatfeldolgozási folyamat létrehozásához Azure Data Factory és Azure Databricks használatával történő adatelőkészítéshez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python, data4ml
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: e8a8b952d917db3a7eefd2e0371d41287c5be944
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612473"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>A DevOps használata adatfeldolgozási folyamathoz

A legtöbb esetben az adatfeldolgozási megoldás a parancsfájlok, a szolgáltatások meghívásai és az összes tevékenységet összehangoló folyamat. Ebből a cikkből megtudhatja, hogyan alkalmazhatja a DevOps gyakorlatokat egy olyan közös adatfeldolgozási folyamat fejlesztési életciklusára, amely előkészíti a gépi tanulási modell betanításához szükséges információkat. A folyamat a következő Azure-szolgáltatások használatával készült:

* __Azure Data Factory__: beolvassa a nyers adatokat, és összehangolja az adatokat.
* __Azure Databricks__: egy Python-jegyzetfüzetet futtat, amely átalakítja az adatfájlokat.
* __Azure-folyamatok__: automatizálja a folyamatos integrációt és a fejlesztési folyamatot.

## <a name="data-ingestion-pipeline-workflow"></a>Adatfeldolgozási folyamat munkafolyamata

Az adatfeldolgozási folyamat a következő munkafolyamatot valósítja meg:

1. A rendszer beolvassa a nyers adatAzure Data Factory (ADF) folyamatát.
1. Az ADF-folyamat elküldi az adatokat egy Azure Databricks-fürtnek, amely egy Python-jegyzetfüzetet futtat az adat átalakításához.
1. Az adattároló egy blob-tárolóba kerül, ahol a Azure Machine Learning a modell betanítására is használható.

![adatfeldolgozási folyamat munkafolyamata](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Folyamatos integráció és kézbesítés áttekintése

A sok szoftveres megoldáshoz hasonlóan egy csapat (például adatmérnökök) dolgozik rajta. Közösen használják az Azure-erőforrásokat, például a Azure Data Factory, a Azure Databricks és az Azure Storage-fiókokat. Ezen erőforrások gyűjteménye fejlesztési környezet. Az adatmérnökök ugyanahhoz a forráskód-alaphoz járulnak hozzá.

A folyamatos integráció és a továbbítási rendszer automatizálja a megoldás létrehozásának, tesztelésének és megvalósításának folyamatát. A folyamatos integrációs (CI) folyamat a következő feladatokat hajtja végre:

* A kód összeállítása
* Ellenőrzi a kód minőségi teszteléseit
* Egység tesztek futtatása
* Olyan összetevőket hoz létre, mint a tesztelt kód és a Azure Resource Manager sablonok

A folyamatos kézbesítés (CD) folyamata az összetevőket az alsóbb rétegbeli környezetekben telepíti.

![vel adatfeldolgozási diagram](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Ez a cikk bemutatja, hogyan automatizálható a CI-és CD-folyamatok az [Azure](https://azure.microsoft.com/services/devops/pipelines/)-folyamatokkal.

## <a name="source-control-management"></a>Verziókövetés kezelése

A verziókövetés felügyeletére a módosítások nyomon követéséhez és a csapattagok közötti együttműködés engedélyezéséhez van szükség.
A kód például egy Azure DevOps-, GitHub-vagy GitLab-tárházban tárolódik. Az együttműködési munkafolyamat egy elágazó modellen alapul. Például: [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Python notebook-forráskód

Az adatmérnökök a Python notebook forráskódját helyileg, egy IDE (például a [Visual Studio Code](https://code.visualstudio.com)) vagy közvetlenül a Databricks munkaterületen működnek. A kód módosításainak befejezése után a rendszer egyesíti őket a tárházba az elágazási szabályzat után.

> [!TIP] 
> Azt javasoljuk, hogy Jupyter Notebook formátumban tárolja a kódot a `.py` fájlokban `.ipynb` . Javítja a kód olvashatóságát, és lehetővé teszi az automatikus kódok minőségének ellenőrzését a CI-folyamat során.

### <a name="azure-data-factory-source-code"></a>Forráskód Azure Data Factory

Azure Data Factory folyamatok forráskódja Azure Data Factory munkaterület által generált JSON-fájlok gyűjteménye. Az adatmérnökök általában a Azure Data Factory munkaterületen lévő vizuális tervezővel működnek, nem pedig közvetlenül a forráskód fájljaival. 

A munkaterület forrás-ellenőrzési adattár használatára való konfigurálásával kapcsolatban lásd: [Szerző az Azure Repos git-integrációval](../data-factory/source-control.md#author-with-azure-repos-git-integration).   

## <a name="continuous-integration-ci"></a>Folyamatos integráció (CI)

A folyamatos integrációs folyamat végső célja, hogy összegyűjtse a közös csapat munkáját a forráskódból, és előkészítse az üzembe helyezést az alsóbb rétegbeli környezetekben. A forráskód-felügyelethez hasonlóan ez a folyamat különbözik a Python-jegyzetfüzetek és a Azure Data Factory folyamatok esetében. 

### <a name="python-notebook-ci"></a>Python notebook CI

A Python-jegyzetfüzetek CI-folyamata megkapja az együttműködési ág kódját (például ***Master** _ vagy _ *_fejlesztése_* *), és a következő tevékenységeket végzi el:
* Kód
* Egységtesztelés
* A kód mentése összetevőként

A következő kódrészlet bemutatja, hogyan hajthatja végre ezeket a lépéseket egy Azure DevOps ***YAML*** -folyamatban:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

A folyamat a [flake8](https://pypi.org/project/flake8/) használatával hajtja végre a Python-kód felépítését. Futtatja a forráskódban meghatározott mértékegység-teszteket, és közzéteszi a kiértékelési és tesztelési eredményeket, hogy elérhetők legyenek az Azure-folyamat végrehajtási képernyőjén:

![elszámolási egység tesztek](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Ha a kivezetés és az egység tesztelése sikeres, a folyamat a forráskódot átmásolja az összetevő-tárházba, amelyet a következő telepítési lépések használni fognak.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Az adatfeldolgozási folyamatok esetében a CI folyamat egy Azure Data Factoryi folyamat szűk keresztmetszete. Nincs folyamatos integráció. A Azure Data Factory telepíthető összetevője Azure Resource Manager-sablonok gyűjteménye. A sablonok előállításának egyetlen módja, ha a Azure Data Factory munkaterület ***Közzététel*** gombjára kattint.

1. Az adatmérnökök egyesítik a forráskódot a szolgáltatási ágakból az együttműködési ágra, például ***Master** _ vagy _ *_fejlesztése_* *. 
1. A megadott engedélyekkel rendelkező személy a ***Közzététel*** gombra kattintva hozza Azure Resource Manager sablonokat az együttműködési ág forráskódjában. 
1. A munkaterület ellenőrzi a folyamatokat (úgy gondolja, hogy az kihelyezés és az egység tesztelése), Azure Resource Manager sablonokat hoz létre (például az építésből), és a generált sablonokat egy technikai ***adf_publish*** ág számára menti, amely ugyanabban a tárban található (a közzétételi összetevőktől függetlenül). Ezt az ágat a Azure Data Factory munkaterület automatikusan létrehozta. 

A folyamattal kapcsolatos további információkért lásd: [folyamatos integráció és kézbesítés Azure Data Factoryban](../data-factory/continuous-integration-deployment.md).

Fontos annak biztosítása, hogy a generált Azure Resource Manager sablonok a környezet agnosztikusok legyenek. Ez azt jelenti, hogy minden olyan érték, amely a környezetek között eltérő lehet, parametrized. Azure Data Factory elég okos ahhoz, hogy az értékek többségét elérhetővé tegye a paraméterként. A következő sablonban például a Azure Machine Learning munkaterület kapcsolódási tulajdonságai paraméterekként vannak kitéve:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Azonban előfordulhat, hogy a Azure Data Factory munkaterület által nem kezelt egyéni tulajdonságokat szeretné kitenni. Ebben a cikkben a Azure Data Factory folyamat egy Python-jegyzetfüzetet hív meg az adatfeldolgozás során. A jegyzetfüzet egy bemeneti adatfájl nevét tartalmazó paramétert fogad el.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Ez a név különbözik a ***dev** _, a _*_QA_*_, a _*_ellenőrzését_*_ és a _*_Prod_*_ környezetekben. A több tevékenységgel rendelkező összetett folyamatokban több egyéni tulajdonság is lehet. Célszerű az összes értéket összegyűjteni egy helyen, és a folyamat _ * változók * *_változóként_ definiálni:

![A képernyőképen egy PrepareData nevű jegyzetfüzet és egy M L művelet-végrehajtási folyamat fut, amelynek neve: M L végrehajtási folyamat felül, az alábbi változók lapon, az új változók hozzáadásával, névvel, típussal és alapértelmezett értékkel.](media/how-to-cicd-data-ingestion/adf-variables.png)

A folyamat tevékenységei a folyamat változóit a tényleges használat közben is hivatkozhatják:

![A képernyőképen egy PrepareData nevű jegyzetfüzet és egy M L végrehajtási folyamat fut, amelynek neve: M L végrehajtási folyamat, az alább kiválasztott beállítások lapon.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

A Azure Data Factory munkaterület alapértelmezés szerint ***nem*** tesz elérhetővé Azure Resource Manager sablon paraméterként a folyamat változóit. A munkaterület az [alapértelmezett paraméterezés-sablont](../data-factory/continuous-integration-deployment.md#default-parameterization-template) használja, amely azt diktálja, hogy a folyamat mely tulajdonságai legyenek elérhetők Azure Resource Manager sablon paramétereinek. Ha a listához szeretné hozzáadni a folyamat változóit, frissítse az `"Microsoft.DataFactory/factories/pipelines"` [alapértelmezett paraméterezés-sablon](../data-factory/continuous-integration-deployment.md#default-parameterization-template) szakaszát az alábbi kódrészlettel, és helyezze el az eredmény JSON-fájlját a forrás mappa gyökerébe:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Ennek hatására a Azure Data Factory munkaterület felveszi a változókat a paraméterek listára, amikor a ***Közzététel*** gombra kattint:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

A JSON-fájl értékei a folyamat definíciójában megadott alapértelmezett értékek. A rendszer a Azure Resource Manager sablon központi telepítésekor várhatóan felülbírálja a célként megadott környezeti értékeket.

## <a name="continuous-delivery-cd"></a>Folyamatos teljesítés (CD)

A folyamatos kézbesítési folyamat az összetevőket veszi át, és telepíti őket az első célként megadott környezetbe. Gondoskodik arról, hogy a megoldás a tesztek futtatásával működjön. Ha a művelet sikeres, az továbbra is a következő környezetbe kerül. 

A CD Azure-folyamat a környezeteket képviselő több szakaszból áll. Mindegyik szakasz a következő lépéseket végrehajtó [központi telepítéseket](/azure/devops/pipelines/process/deployment-jobs) és [feladatokat](/azure/devops/pipelines/process/phases?tabs=yaml) tartalmazza:

* Python-jegyzetfüzet üzembe helyezése Azure Databricks munkaterületen
* Azure Data Factory folyamat üzembe helyezése 
* A folyamat futtatása
* Az adatfeldolgozás eredményének ellenőrzéséhez

A folyamat fázisai olyan [jóváhagyásokkal](/azure/devops/pipelines/process/approvals?tabs=check-pass) és [kapukkal](/azure/devops/pipelines/release/approvals/gates) konfigurálhatók, amelyek további szabályozást biztosítanak a telepítési folyamatnak a környezetek láncán való kialakulásához.

### <a name="deploy-a-python-notebook"></a>Python-jegyzetfüzet üzembe helyezése

Az alábbi kódrészlet egy olyan Azure-alapú folyamat- [telepítést](/azure/devops/pipelines/process/deployment-jobs) határoz meg, amely egy Python-jegyzetfüzetet másol egy Databricks-fürtre:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

A CI által előállított összetevők automatikusan átkerülnek a központi telepítési ügynökre, és elérhetők a `$(Pipeline.Workspace)` mappában. Ebben az esetben a telepítési feladat a Python-jegyzetfüzetet tartalmazó összetevőre hivatkozik `di-notebooks` . Ez [az](/azure/devops/pipelines/process/deployment-jobs) üzemelő példány a [Databricks Azure DevOps bővítmény](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) használatával másolja a notebook-fájlokat a Databricks-munkaterületre.

A `Deploy_to_QA` szakasz az `devops-ds-qa-vg` Azure DevOps projektben definiált változóra mutató hivatkozást tartalmaz. Az ebben a szakaszban szereplő lépések a változókat az adott változócsoport (például és) változóit jelentik `$(DATABRICKS_URL)` `$(DATABRICKS_TOKEN)` . Az elképzelés az, hogy a következő szakasz (például `Deploy_to_UAT` ) ugyanazokat a változókat fogja használni, mint a saját ellenőrzését-hatókörű változó csoportjában.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Azure Data Factory folyamat üzembe helyezése

A Azure Data Factory telepíthető összetevője Azure Resource Manager sablon. Az ***Azure erőforráscsoport-telepítési*** feladattal lesz üzembe helyezve, mivel az alábbi kódrészletben látható:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Az adatfájlnév paraméter értéke a `$(DATA_FILE_NAME)` minőségbiztosítási fázis változó csoportjában definiált változóból származik. Hasonlóképpen, a ***ARMTemplateForFactory.json*** definiált összes paraméter felülbírálható. Ha nem, akkor a rendszer az alapértelmezett értékeket használja.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>A folyamat futtatása és az adatfeldolgozás eredményének ellenőrzéséhez

A következő lépés annak ellenőrzése, hogy a telepített megoldás működik-e. A következő feladatdefiníció egy [PowerShell-parancsfájlt](https://github.com/microsoft/DataOps/tree/master/adf/utils) futtató Azure Data Factory folyamatot futtat, és egy Python-jegyzetfüzetet hajt végre egy Azure Databricks-fürtön. A jegyzetfüzet ellenőrzi, hogy az adat megfelelően lett-e betöltve, és ellenőrzi az eredményül kapott adatfájl `$(bin_FILE_NAME)` nevét.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

A feladat utolsó feladata ellenőrzi a jegyzetfüzet végrehajtásának eredményét. Ha hibát ad vissza, akkor a folyamat végrehajtásának állapotát nem sikerült értékre állítja.

## <a name="putting-pieces-together"></a>Összeállítás

A teljes CI/CD Azure-folyamat a következő szakaszokból áll:
* CI
* Üzembe helyezés a QA-ben
    * Üzembe helyezés a Databricks és az ADF üzembe helyezése
    * Integrációs teszt

Számos ***üzembe helyezési** fázist tartalmaz, amely megegyezik a megcélzott környezetek számával. Minden _ *_üzembe helyezés_** szakasz két üzemelő üzemelő [példányt](/azure/devops/pipelines/process/deployment-jobs) tartalmaz, amelyek párhuzamosan futnak, valamint egy olyan [feladatot](/azure/devops/pipelines/process/phases?tabs=yaml) , amely az üzembe helyezést követően fut a megoldás teszteléséhez a környezetben.

A folyamat egy minta implementációját a következő ***YAML*** -kódrészletben kell összeállítani:

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Következő lépések

* [Verziókövetés az Azure Data Factoryben](../data-factory/source-control.md)
* [Folyamatos integráció és teljesítés az Azure Data Factoryben](../data-factory/continuous-integration-deployment.md)
* [Azure Databricks DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)