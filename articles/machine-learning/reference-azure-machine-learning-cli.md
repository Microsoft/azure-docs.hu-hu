---
title: Telepítés & CLI Azure Machine Learning használatával
description: Megtudhatja, hogyan használhatja az Azure CLI ML-bővítményt erőforrások &, például a munkaterület, az adattárolók, az adatkészletek, a folyamatok, a modellek és az üzemelő példányok kezeléséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: jordane
author: jpe316
ms.date: 04/02/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 8e912e141ab769f3121fcccdecb9c3952920572f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516212"
---
# <a name="install--use-the-cli-extension-for-azure-machine-learning"></a>Telepítse & cli-bővítményt a Azure Machine Learning


A Azure Machine Learning CLI az Azure CLI egy platformfüggetlen parancssori felülete, az [Azure-platform](/cli/azure/)bővítménye. Ez a bővítmény parancsokat biztosít a Azure Machine Learning. Lehetővé teszi a gépi tanulási tevékenységek automatizálását. Az alábbi lista néhány példaműveletet mutat be a CLI-bővítményhez:

+ Kísérletek futtatása gépi tanulási modellek létrehozásához

+ Gépi tanulási modellek regisztrálása ügyfélhasználathoz

+ A gépi tanulási modellek csomagolása, üzembe helyezése és nyomon követése

A CLI nem helyettesíti a Azure Machine Learning SDK-t. Ez egy kiegészítő eszköz, amely úgy van optimalizálva, hogy jól paraméterezett feladatokat kezeljen, amelyek jól illeszkednek az automatizáláshoz.

## <a name="prerequisites"></a>Előfeltételek

* A CLI-hez Azure-előfizetéssel kell lennie. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy fizetős](https://aka.ms/AMLFree) verzióját.

* Az ebben a dokumentumban használt CLI-parancsoknak a helyi **környezetből** való használatára az [Azure CLI-nek kell szüksége.](/cli/azure/install-azure-cli)

    Ha a Azure Cloud Shell [használja,](https://azure.microsoft.com/features/cloud-shell/)a CLI a böngészőn keresztül érhető el, és a felhőben van.

## <a name="full-reference-docs"></a>Teljes referencia-dokumentumok

Keresse meg [az Azure CLI azure-cli-ml](/cli/azure/ml/)bővítményének teljes referencia-dokumentumokját.

## <a name="connect-the-cli-to-your-azure-subscription"></a>A CLI csatlakoztatása az Azure-előfizetéshez

> [!IMPORTANT]
> Ha a következőt használja Azure Cloud Shell, kihagyhatja ezt a szakaszt. A Cloud Shell automatikusan hitelesíti Az Azure-előfizetésbe bejelentkező fiókkal.

Az Azure-előfizetésben többféleképpen is hitelesítheti magát a CLI-ről. A legfontosabb az interaktív hitelesítés böngésző használatával. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell az utasításokat a parancssorban. Az utasítások az engedélyezési kód [https://aka.ms/devicelogin](https://aka.ms/devicelogin) tallózását és beírását foglalják magukban.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

A hitelesítés egyéb módjaiért lásd: [Bejelentkezés az Azure CLI-val.](/cli/azure/authenticate-azure-cli)

## <a name="install-the-extension"></a>A bővítmény telepítése

A bővítmény automatikusan telepítve lesz, amikor először próbál meg olyan parancsot használni, amely a következővel kezdődik: `az ml` .

## <a name="update-the-extension"></a>A bővítmény frissítése

A parancssori felületi Machine Learning frissítéséhez használja a következő parancsot:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="remove-the-extension"></a>A bővítmény eltávolítása

A CLI-bővítmény eltávolításához használja a következő parancsot:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Erőforrás-kezelés

Az alábbi parancsok bemutatják, hogyan használható a parancssori felület a Azure Machine Learning.

+ Ha még nem rendelkezik erőforráscsoporttal, hozzon létre egy erőforráscsoportot:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Hozzon létre egy Azure Machine Learning munkaterületet:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    További információ: [az ml workspace create.](/cli/azure/ext/azure-cli-ml/ml/workspace#ext-azure-cli-ml-az-ml-workspace-create)

+ Csatoljon egy munkaterület-konfigurációt egy mappához a CLI környezetfüggőségének engedélyezéséhez.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Ez a parancs létrehoz egy alkönyvtárat, amely példa `.azureml` runconfig- és Conda-környezeti fájlokat tartalmaz. Emellett tartalmaz egy fájlt is, amely a `config.json` munkaterülettel való Azure Machine Learning használható.

    További információ: [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder#ext-azure-cli-ml-az-ml-folder-attach).

+ Azure Blob-tároló csatolása adattárként.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    További információ: [az ml datastore attach-blob.](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-attach-blob)

+ Fájlok feltöltése egy adattárba.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    További információ: [az ml datastore upload.](/cli/azure/ext/azure-cli-ml/ml/datastore#ext-azure-cli-ml-az-ml-datastore-upload)

+ AKS-fürt csatolása számítási célként.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    További információ: [az ml computetarget attach aks](/cli/azure/ext/azure-cli-ml/ml/computetarget/attach#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

### <a name="compute-clusters"></a>Számítási fürtök

+ Hozzon létre egy új felügyelt számítási fürtöt.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```



+ Új felügyelt számítási fürt létrehozása felügyelt identitással

  + Felhasználó által hozzárendelt felügyelt identitás

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  + Rendszer által hozzárendelt felügyelt identitás

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
+ Felügyelt identitás hozzáadása meglévő fürthöz:

    + Felhasználó által hozzárendelt felügyelt identitás
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    + Rendszer által hozzárendelt felügyelt identitás

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

További információ: [az ml computetarget create amlcompute](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

<a id="computeinstance"></a>

### <a name="compute-instance"></a>Számítási példány
Számítási példányok kezelése.  Az alábbi példákban a számítási példány neve **cpu**

+ Hozzon létre egy új számítástelepítést.

    ```azurecli-interactive
    az ml computetarget create computeinstance -n cpu -s "STANDARD_D3_V2" -v
    ```

    További információ: [az ml computetarget create computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-computeinstance).

+ Számítási példányok leállítása.

    ```azurecli-interactive
    az ml computetarget computeinstance stop -n cpu -v
    ```

    További információ: [az ml computetarget computeinstance stop.](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-stop)

+ Indítsunk el egy számítástelepítést.

    ```azurecli-interactive
    az ml computetarget computeinstance start -n cpu -v
    ```

    További információ: [az ml computetarget computeinstance start.](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-start)

+ Indítsa újra a computeinstance-t.

    ```azurecli-interactive
    az ml computetarget computeinstance restart -n cpu -v
    ```

    További információ: [az ml computetarget computeinstance restart](/cli/azure/ext/azure-cli-ml/ml/computetarget/computeinstance#ext-azure-cli-ml-az-ml-computetarget-computeinstance-restart).

+ Számítási példány törlése.

    ```azurecli-interactive
    az ml computetarget delete -n cpu -v
    ```

    További információ: [az ml computetarget delete computeinstance](/cli/azure/ext/azure-cli-ml/ml/computetarget#ext-azure-cli-ml-az-ml-computetarget-delete).


## <a name="run-experiments"></a><a id="experiments"></a>Kísérletek futtatása

* Indítsa el a kísérlet futtatását. A parancs használata esetén adja meg a runconfig fájl nevét (a .runconfig előtti szöveget, ha a fájlrendszert nézi) a \* -c paraméterrel.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > A `az ml folder attach` parancs létrehoz egy `.azureml` alkönyvtárat, amely két példa runconfig fájlt tartalmaz. 
    >
    > Ha olyan Python-szkriptet használ, amely programozott módon hoz létre egy futtatási konfigurációs objektumot, a [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) használatával mentheti runconfig fájlként.
    >
    > A teljes runconfig séma ebben a [JSON-fájlban található.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json) A séma az egyes objektumok kulcsán `description` keresztül saját maga dokumentálja. Emellett a lehetséges értékekhez enumok is vannak, a végén pedig egy sablonrészlet.

    További információ: [az ml run submit-script.](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-script)

* Kísérletek listájának megtekintése:

    ```azurecli-interactive
    az ml experiment list
    ```

    További információ: [az ml experiment list.](/cli/azure/ext/azure-cli-ml/ml/experiment#ext-azure-cli-ml-az-ml-experiment-list)

### <a name="hyperdrive-run"></a>HyperDrive-futtatás

A HyperDrive és az Azure CLI segítségével paraméter-finomhangolási futtatásokat hajthat végre. Először hozzon létre egy HyperDrive konfigurációs fájlt a következő formátumban. A [hiperparaméterek](how-to-tune-hyperparameters.md) finomhangolási paramétereivel kapcsolatos részletekért lásd: Hiperparaméterek hangolása a modellhez.

```yml
# hdconfig.yml
sampling: 
    type: random # Supported options: Random, Grid, Bayesian
    parameter_space: # specify a name|expression|values tuple for each parameter.
    - name: --penalty # The name of a script parameter to generate values for.
      expression: choice # supported options: choice, randint, uniform, quniform, loguniform, qloguniform, normal, qnormal, lognormal, qlognormal
      values: [0.5, 1, 1.5] # The list of values, the number of values is dependent on the expression specified.
policy: 
    type: BanditPolicy # Supported options: BanditPolicy, MedianStoppingPolicy, TruncationSelectionPolicy, NoTerminationPolicy
    evaluation_interval: 1 # Policy properties are policy specific. See the above link for policy specific parameter details.
    slack_factor: 0.2
primary_metric_name: Accuracy # The metric used when evaluating the policy
primary_metric_goal: Maximize # Maximize|Minimize
max_total_runs: 8 # The maximum number of runs to generate
max_concurrent_runs: 2 # The number of runs that can run concurrently.
max_duration_minutes: 100 # The maximum length of time to run the experiment before cancelling.
```

Adja hozzá ezt a fájlt a futtatás konfigurációs fájljaival együtt. Ezután küldje el a HyperDrive-futtatásokat a következővel:
```azurecli
az ml run submit-hyperdrive -e <experiment> -c <runconfig> --hyperdrive-configuration-name <hdconfig> my_train.py
```

Jegyezze *fel a* runconfig és a paramétertér argumentumok szakaszát a HyperDrive konfigurációban.  Ezek tartalmazzák a betanító szkriptnek átadott parancssori argumentumokat. A runconfig értéke minden iterációban ugyanaz marad, míg a HyperDrive-konfiguráció tartománya iterálva van. Ne adja meg ugyanazt az argumentumot mindkét fájlban.

## <a name="dataset-management"></a>Adatkészlet-kezelés

A következő parancsok bemutatják, hogyan dolgozhat adatkészletekkel a Azure Machine Learning:

+ Adatkészlet regisztrálása:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    Az adatkészlet meghatározásához használt JSON-fájl formátumával kapcsolatos információkért használja a következőt: `az ml dataset register --show-template` .

    További információ: [az ml dataset register.](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-register)

+ List all datasets in a workspace (Egy munkaterület összes adatát listába sorolja):

    ```azurecli-interactive
    az ml dataset list
    ```

    További információ: [az ml dataset list](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-list).

+ Adatkészlet részleteinek lekérte:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    További információ: [az ml dataset show](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-show).

+ Adatkészlet regisztrációjának a regisztrációja:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    További információ: [az ml dataset unregister](/cli/azure/ext/azure-cli-ml/ml/dataset#ext-azure-cli-ml-az-ml-dataset-archive).

## <a name="environment-management"></a>A környezet kezelése

A következő parancsok bemutatják, hogyan hozhat létre, regisztrálhat és listálhat Azure Machine Learning [a](how-to-configure-environment.md) munkaterülethez:

+ Hozzon létre állványozási fájlokat egy környezethez:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    További információ: [az ml environment scaffold](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-scaffold).

+ Környezet regisztrálása:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    További információ: [az ml environment register.](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-register)

+ Regisztrált környezetek felsorolása:

    ```azurecli-interactive
    az ml environment list
    ```

    További információ: [az ml environment list.](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-list)

+ Regisztrált környezet letöltése:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    További információ: [az ml environment download](/cli/azure/ext/azure-cli-ml/ml/environment#ext-azure-cli-ml-az-ml-environment-download).

### <a name="environment-configuration-schema"></a>Környezetkonfigurációs séma

Ha a parancsot használta, az létrehoz egy sablonfájlt, amely módosítható, és egyéni környezeti konfigurációk létrehozásához használható `az ml environment scaffold` a `azureml_environment.json` PARANCSSORi felülettel. A legfelső szintű objektum lazán leképezi a [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) osztályt a Python SDK-ban. 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

Az alábbi táblázat a JSON-fájl összes legfelső szintű mezőjét, típusát és leírását részletezi. Ha egy objektumtípus egy osztályhoz kapcsolódik a Python SDK-ból, lazán 1:1 egyezés van az egyes JSON-mezők és a nyilvános változók neve között a Python-osztályban. Bizonyos esetekben a mező leképezheti egy konstruktorarátor argumentumát egy osztályváltozó helyett. A mező például `environmentVariables` a osztály `environment_variables` változóját [`Environment`](/python/api/azureml-core/azureml.core.environment%28class%29) leképezi.

| JSON-mező | Típus | Leírás |
|---|---|---|
| `name` | `string` | A környezet neve. Ne kezdje el a nevet **a Microsoft vagy** az **AzureML használatával.** |
| `version` | `string` | A környezet verziója. |
| `environmentVariables` | `{string: string}` | A környezeti változók nevének és értékeinek kivonattérképe. |
| `python` | [`PythonSection`](/python/api/azureml-core/azureml.core.environment.pythonsection)A hat határozza meg a cél számítási erőforráson használható Python-környezetet és -értelmezőt. |
| `docker` | [`DockerSection`](/python/api/azureml-core/azureml.core.environment.dockersection) | A környezet specifikációihoz készült Docker-rendszerkép testreszabásához szükséges beállításokat határozza meg. |
| `spark` | [`SparkSection`](/python/api/azureml-core/azureml.core.environment.sparksection) | A szakasz a Spark beállításait konfigurálja. Csak akkor használatos, ha a keretrendszer PySparkra van beállítva. |
| `databricks` | [`DatabricksSection`](/python/api/azureml-core/azureml.core.databricks.databrickssection) | Konfigurálja a Databricks-kódtár függőségeit. |
| `inferencingStackVersion` | `string` | Megadja a lemezképhez hozzáadott következtetési verem verzióját. A következtetési verem hozzáadásának elkerüléséhez hagyja meg ezt a `null` mezőt. Érvényes érték: "latest". |

## <a name="ml-pipeline-management"></a>Gépi tanulási folyamatok kezelése

Az alábbi parancsok bemutatják, hogyan dolgozhat gépi tanulási folyamatokkal:

+ Gépi tanulási folyamat létrehozása:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    További információ: [az ml pipeline create.](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create)

    További információ a folyamat YAML-fájlról: Gépi tanulási folyamatok [definiálás a YAML-ban.](reference-pipeline-yaml.md)

+ Folyamat futtatása:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    További információ: [az ml run submit-pipeline.](/cli/azure/ext/azure-cli-ml/ml/run#ext-azure-cli-ml-az-ml-run-submit-pipeline)

    További információ a folyamat YAML-fájlról: Gépi tanulási folyamatok [definiálás a YAML-ban.](reference-pipeline-yaml.md)

+ Folyamat ütemezése:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    További információ: [az ml pipeline create-schedule.](/cli/azure/ext/azure-cli-ml/ml/pipeline#ext-azure-cli-ml-az-ml-pipeline-create-schedule)

    A folyamat ütemezési YAML-fájllal kapcsolatos további információkért lásd: Gépi tanulási folyamatok [meghatározása a YAML-ben.](reference-pipeline-yaml.md#schedules)

## <a name="model-registration-profiling-deployment"></a>Modellregisztráció, profilkészítés, üzembe helyezés

A következő parancsok bemutatják, hogyan regisztrálhat egy betanított modellt, majd hogyan helyezheti üzembe éles szolgáltatásként:

+ Modell regisztrálása a következő Azure Machine Learning:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    További információ: [az ml model register.](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-register)

+ **NEM KÖTELEZŐ** Profilt kell használnia a modellről, hogy optimális processzor- és memóriaértékeket kap az üzembe helyezéshez.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    További információ: [az ml model profile.](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-profile)

+ A modell üzembe helyezése az AKS-be
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    A dedíciós konfigurációs fájlsémára vonatkozó további információkért lásd: [Következtetési konfigurációs séma.](#inferenceconfig)
    
    Az üzembe helyezés konfigurációs fájlsémára vonatkozó további információkért lásd: Üzembe [helyezési konfigurációs séma.](#deploymentconfig)

    További információ: [az ml model deploy](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Következtetési konfigurációs séma

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Üzembe helyezési konfigurációs séma

### <a name="local-deployment-configuration-schema"></a>Helyi üzembe helyezés konfigurációs sémája

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Az Azure Container Instance üzembe helyezési konfigurációs sémája 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service konfigurációs sémája

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Következő lépések

* [A parancssori felületi bővítmény Machine Learning parancsreferenciája.](/cli/azure/ext/azure-cli-ml/ml)

* [Gépi tanulási modellek betanítása és üzembe helyezése az Azure Pipelines használatával](/azure/devops/pipelines/targets/azure-machine-learning)
