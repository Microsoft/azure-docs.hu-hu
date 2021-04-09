---
title: Képzések beállítása & számítási célok következtetése
titleSuffix: Azure Machine Learning
description: Számítási erőforrások (számítási célok) hozzáadása a munkaterülethez a gépi tanulási képzéshez és a következtetésekhez
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/02/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 9fa6a1758bc2e2a76291efc3bb239c5249a6e21e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149341"
---
# <a name="set-up-compute-targets-for-model-training-and-deployment"></a>Számítási célok beállítása a modell betanításához és üzembe helyezéséhez

Ismerje meg, hogyan csatolhat Azure számítási erőforrásokat a Azure Machine Learning-munkaterülethez.  Ezt követően a gépi tanulási feladatokban felhasználhatja ezeket az erőforrásokat a [számítási célok](concept-compute-target.md) betanítása és következtetése szerint.

Ebből a cikkből megtudhatja, hogyan állíthatja be a munkaterületet a számítási erőforrások használatára:

* A helyi számítógép
* Távoli virtuális gépek
* Azure HDInsight
* Azure Batch
* Azure Databricks
* Azure Data Lake Analytics
* Azure Container Instance

Azure Machine Learning által felügyelt számítási célok használatához lásd:


* [Azure Machine Learning számítási példány](how-to-create-manage-compute-instance.md)
* [Számítási fürt Azure Machine Learning](how-to-create-attach-compute-cluster.md)
* [Azure Kubernetes Service-fürt](how-to-create-attach-kubernetes.md)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

* Az [Azure CLI-bővítmény Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), [Azure Machine learning Python SDK](/python/api/overview/azure/ml/intro)-hoz vagy a [Azure Machine learning Visual Studio Code bővítményhez](tutorial-setup-vscode-extension.md).

## <a name="limitations"></a>Korlátozások

* Ne **hozzon létre több egyidejű mellékletet ugyanahhoz a számítási** feladatokhoz a munkaterületen. Például egy Azure Kubernetes Service-fürt csatolása egy munkaterülethez két különböző név használatával. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.

    Ha szeretné újracsatlakoztatni a számítási célt, például a TLS vagy más fürtkonfiguráció módosítása beállításnál, először el kell távolítania a meglévő mellékletet.

## <a name="whats-a-compute-target"></a>Mi a számítási cél?

A Azure Machine Learning segítségével különböző erőforrásokra és környezetekre is betaníthatja a modellt, együttesen [__számítási célok__](concept-azure-machine-learning-architecture.md#compute-targets)néven. A számítási cél lehet egy helyi gép vagy egy felhőalapú erőforrás, például egy Azure Machine Learning számítási, Azure-HDInsight vagy távoli virtuális gép.  Emellett számítási célokat is használhat a modell telepítéséhez a ["hol és hogyan kell üzembe helyezni a modelleket"](how-to-deploy-and-where.md)című cikkben leírtak szerint.


## <a name="local-computer"></a><a id="local"></a>Helyi számítógép

Ha a helyi számítógépet használja a **betanításhoz**, nem kell számítási célt létrehoznia.  Csak [küldje el a képzést](how-to-set-up-training-targets.md) a helyi gépről.

Ha a helyi számítógépet használja a **következtetéshez**, telepítve kell lennie a Docker-nek. Az üzemelő példány végrehajtásához használja a [LocalWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.local.localwebservice#deploy-configuration-port-none-) lehetőséget a webszolgáltatás által használt port definiálásához. Ezután használja a normál telepítési folyamatot a [modellek üzembe helyezése Azure Machine learning](how-to-deploy-and-where.md)használatával című témakörben leírtak szerint.

## <a name="remote-virtual-machines"></a><a id="vm"></a>Távoli virtuális gépek

A Azure Machine Learning támogatja az Azure-beli virtuális gépek csatlakoztatását is. A virtuális gépnek Azure-Data Science Virtual Machine (DSVM) kell lennie. Ez a virtuális gép egy előre konfigurált adatelemzési és AI-fejlesztési környezet az Azure-ban. A virtuális gép számos eszközt és keretrendszert kínál a teljes életciklusú gépi tanulás fejlesztéséhez. A DSVM és a Azure Machine Learning használatával kapcsolatos további információkért lásd: [fejlesztési környezet konfigurálása](./how-to-configure-environment.md#dsvm).

1. **Létrehozás**: hozzon létre egy DSVM, mielőtt a modellt betanítani. Az erőforrás létrehozásával kapcsolatban tekintse meg [a Linux (Ubuntu) Data Science Virtual Machine kiépítése](./data-science-virtual-machine/dsvm-ubuntu-intro.md)című témakört.

    > [!WARNING]
    > Azure Machine Learning csak az **Ubuntut** futtató virtuális gépeket támogatja. Amikor létrehoz egy virtuális gépet, vagy egy meglévő virtuális gépet választ, ki kell választania egy Ubuntut használó virtuális gépet.
    > 
    > A Azure Machine Learning azt is megköveteli, hogy a virtuális gép __nyilvános IP-címmel__ rendelkezzen.

1. **Csatolás**: Ha egy meglévő virtuális gépet számítási célként szeretne csatolni, meg kell adnia a virtuális gép erőforrás-azonosítóját, felhasználónevét és jelszavát. A virtuális gép erőforrás-azonosítója az előfizetés-azonosító, az erőforráscsoport neve és a virtuális gép neve alapján hozható létre a következő karakterlánc-formátum használatával: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Compute/virtualMachines/<vm_name>`

 
   ```python
   from azureml.core.compute import RemoteCompute, ComputeTarget

   # Create the compute config 
   compute_target_name = "attach-dsvm"
   
   attach_config = RemoteCompute.attach_configuration(resource_id='<resource_id>',
                                                   ssh_port=22,
                                                   username='<username>',
                                                   password="<password>")

   # Attach the compute
   compute = ComputeTarget.attach(ws, compute_target_name, attach_config)

   compute.wait_for_completion(show_output=True)
   ```

   A DSVM a munkaterülethez [Azure Machine learning Studio használatával](how-to-create-attach-compute-studio.md#attached-compute)is csatlakoztathatja.

    > [!WARNING]
    > Ne hozzon létre egyszerre több, egyidejű mellékletet ugyanahhoz a DSVM a munkaterületről. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt a DSVM számítási célhoz. A Docker és a Conda a DSVM lévő képzési környezet létrehozásához és konfigurálásához használható.

   ```python
   from azureml.core import ScriptRunConfig
   from azureml.core.environment import Environment
   from azureml.core.conda_dependencies import CondaDependencies
   
   # Create environment
   myenv = Environment(name="myenv")
   
   # Specify the conda dependencies
   myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'])
   
   # If no base image is explicitly specified the default CPU image "azureml.core.runconfig.DEFAULT_CPU_IMAGE" will be used
   # To use GPU in DSVM, you should specify the default GPU base Docker image or another GPU-enabled image:
   # myenv.docker.enabled = True
   # myenv.docker.base_image = azureml.core.runconfig.DEFAULT_GPU_IMAGE
   
   # Configure the run configuration with the Linux DSVM as the compute target and the environment defined above
   src = ScriptRunConfig(source_directory=".", script="train.py", compute_target=compute, environment=myenv) 
   ```

## <a name="azure-hdinsight"></a><a id="hdinsight"></a>Azure HDInsight 

Az Azure HDInsight egy népszerű platform a Big-adatelemzéshez. A platform Apache Spark biztosít, amely a modell betanítására használható.

1. **Létrehozás**: hozza létre a HDInsight-fürtöt, mielőtt felhasználja a modell betanításához. A Spark on HDInsight-fürt létrehozásával kapcsolatban lásd: [Spark-fürt létrehozása a HDInsight-ben](../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

    > [!WARNING]
    > Azure Machine Learning megköveteli, hogy a HDInsight-fürt __nyilvános IP-címmel__ rendelkezzen.

    A fürt létrehozásakor meg kell adnia egy SSH-felhasználónevet és-jelszót. Jegyezze fel ezeket az értékeket, mert szüksége lesz rájuk a HDInsight számítási célként való használatához.
    
    A fürt létrehozása után kapcsolódjon hozzá az állomásnév \<clustername> -SSH.azurehdinsight.net, ahol a a \<clustername> fürthöz megadott név. 

1. **Csatolás**: Ha egy HDInsight-fürtöt számítási célként kíván csatolni, meg kell adnia a HDInsight-fürt erőforrás-azonosítóját, felhasználónevét és jelszavát. A HDInsight-fürt erőforrás-azonosítója az előfizetés-azonosító, az erőforráscsoport neve és a HDInsight-fürt neve alapján hozható létre a következő karakterlánc-formátum használatával: `/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.HDInsight/clusters/<cluster_name>`

    ```python
   from azureml.core.compute import ComputeTarget, HDInsightCompute
   from azureml.exceptions import ComputeTargetException

   try:
    # if you want to connect using SSH key instead of username/password you can provide parameters private_key_file and private_key_passphrase

    attach_config = HDInsightCompute.attach_configuration(resource_id='<resource_id>',
                                                          ssh_port=22, 
                                                          username='<ssh-username>', 
                                                          password='<ssh-pwd>')
    hdi_compute = ComputeTarget.attach(workspace=ws, 
                                       name='myhdi', 
                                       attach_configuration=attach_config)

   except ComputeTargetException as e:
    print("Caught = {}".format(e.message))

   hdi_compute.wait_for_completion(show_output=True)
   ```

   A HDInsight-fürtöt [Azure Machine learning Studio használatával](how-to-create-attach-compute-studio.md#attached-compute)is csatlakoztathatja a munkaterülethez.

    > [!WARNING]
    > Ne hozzon létre egyszerre több, egyidejű mellékletet ugyanahhoz a HDInsight a munkaterületről. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.

1. **Konfigurálás**: hozzon létre egy futtatási konfigurációt a HDI számítási célhoz. 

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/hdi.py?name=run_hdi)]


Most, hogy csatlakoztatta a számítási és konfigurálta a futtatást, a következő lépés a [betanítási Futtatás elküldése](how-to-set-up-training-targets.md).

## <a name="azure-batch"></a><a id="azbatch"></a>Azure Batch 

Azure Batch a nagy léptékű párhuzamos és nagy teljesítményű számítástechnikai (HPC) alkalmazások felhőben történő futtatására szolgál. A AzureBatchStep Azure Machine Learning folyamatokban felhasználható feladatok Azure Batch készletbe való elküldéséhez.

Azure Batch számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

-    **Azure batch számítási név**: a munkaterületen belüli számításhoz használandó felhasználóbarát név
-    **Azure batch fiók neve**: a Azure batch fiók neve
-    **Erőforráscsoport**: az Azure batch fiókot tartalmazó erőforráscsoport.

A következő kód bemutatja, hogyan csatolhatja Azure Batch számítási célként:

```python
from azureml.core.compute import ComputeTarget, BatchCompute
from azureml.exceptions import ComputeTargetException

# Name to associate with new compute in workspace
batch_compute_name = 'mybatchcompute'

# Batch account details needed to attach as compute to workspace
batch_account_name = "<batch_account_name>"  # Name of the Batch account
# Name of the resource group which contains this account
batch_resource_group = "<batch_resource_group>"

try:
    # check if the compute is already attached
    batch_compute = BatchCompute(ws, batch_compute_name)
except ComputeTargetException:
    print('Attaching Batch compute...')
    provisioning_config = BatchCompute.attach_configuration(
        resource_group=batch_resource_group, account_name=batch_account_name)
    batch_compute = ComputeTarget.attach(
        ws, batch_compute_name, provisioning_config)
    batch_compute.wait_for_completion()
    print("Provisioning state:{}".format(batch_compute.provisioning_state))
    print("Provisioning errors:{}".format(batch_compute.provisioning_errors))

print("Using Batch compute:{}".format(batch_compute.cluster_resource_id))
```

> [!WARNING]
> Ne hozzon létre több egyidejű mellékletet ugyanahhoz a Azure Batch a munkaterületről. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.

### <a name="azure-databricks"></a><a id="databricks"></a>Azure Databricks

A Azure Databricks egy Apache Spark-alapú környezet az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Databricks munkaterületet. Munkaterület-erőforrás létrehozásához tekintse [meg a Spark-feladatok futtatása Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal) dokumentumon című témakört.

Azure Databricks számítási célként való csatolásához adja meg a következő információkat:

* __Databricks számítási név__: a számítási erőforráshoz hozzárendelni kívánt név.
* __Databricks-munkaterület neve__: az Azure Databricks munkaterület neve.
* __Databricks hozzáférési jogkivonat__: a Azure Databricks hitelesítéséhez használt hozzáférési jogkivonat. Hozzáférési jogkivonat létrehozásához tekintse meg a [hitelesítési](/azure/databricks/dev-tools/api/latest/authentication) dokumentumot.

Az alábbi kód azt mutatja be, hogyan csatolható Azure Databricks számítási célként a Azure Machine Learning SDK-val (__az Databricks-munkaterületnek ugyanabban az előfizetésben kell lennie, mint a pénzmosás-munkaterület__):

```python
import os
from azureml.core.compute import ComputeTarget, DatabricksCompute
from azureml.exceptions import ComputeTargetException

databricks_compute_name = os.environ.get(
    "AML_DATABRICKS_COMPUTE_NAME", "<databricks_compute_name>")
databricks_workspace_name = os.environ.get(
    "AML_DATABRICKS_WORKSPACE", "<databricks_workspace_name>")
databricks_resource_group = os.environ.get(
    "AML_DATABRICKS_RESOURCE_GROUP", "<databricks_resource_group>")
databricks_access_token = os.environ.get(
    "AML_DATABRICKS_ACCESS_TOKEN", "<databricks_access_token>")

try:
    databricks_compute = ComputeTarget(
        workspace=ws, name=databricks_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('databricks_compute_name {}'.format(databricks_compute_name))
    print('databricks_workspace_name {}'.format(databricks_workspace_name))
    print('databricks_access_token {}'.format(databricks_access_token))

    # Create attach config
    attach_config = DatabricksCompute.attach_configuration(resource_group=databricks_resource_group,
                                                           workspace_name=databricks_workspace_name,
                                                           access_token=databricks_access_token)
    databricks_compute = ComputeTarget.attach(
        ws,
        databricks_compute_name,
        attach_config
    )

    databricks_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát a notebookra](https://aka.ms/pl-databricks) .

> [!WARNING]
> Ne hozzon létre több egyidejű mellékletet ugyanahhoz a Azure Databricks a munkaterületről. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.

### <a name="azure-data-lake-analytics"></a><a id="adla"></a>Azure Data Lake Analytics

A Azure Data Lake Analytics egy big data elemzési platform az Azure-felhőben. A számítási célként Azure Machine Learning folyamattal is használható.

Használat előtt hozzon létre egy Azure Data Lake Analytics fiókot. Az erőforrás létrehozásához tekintse meg az [Ismerkedés a Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) dokumentummal című témakört.

Data Lake Analytics számítási célként való csatolásához a Azure Machine Learning SDK-t kell használnia, és meg kell adnia a következő információkat:

* __Számítási név__: a számítási erőforráshoz hozzárendelni kívánt név.
* __Erőforráscsoport__: az Data Lake Analytics fiókot tartalmazó erőforráscsoport.
* __Fiók neve__: a Data Lake Analytics fiók neve.

A következő kód bemutatja, hogyan csatolhatja Data Lake Analytics számítási célként:

```python
import os
from azureml.core.compute import ComputeTarget, AdlaCompute
from azureml.exceptions import ComputeTargetException


adla_compute_name = os.environ.get(
    "AML_ADLA_COMPUTE_NAME", "<adla_compute_name>")
adla_resource_group = os.environ.get(
    "AML_ADLA_RESOURCE_GROUP", "<adla_resource_group>")
adla_account_name = os.environ.get(
    "AML_ADLA_ACCOUNT_NAME", "<adla_account_name>")

try:
    adla_compute = ComputeTarget(workspace=ws, name=adla_compute_name)
    print('Compute target already exists')
except ComputeTargetException:
    print('compute not found')
    print('adla_compute_name {}'.format(adla_compute_name))
    print('adla_resource_id {}'.format(adla_resource_group))
    print('adla_account_name {}'.format(adla_account_name))
    # create attach config
    attach_config = AdlaCompute.attach_configuration(resource_group=adla_resource_group,
                                                     account_name=adla_account_name)
    # Attach ADLA
    adla_compute = ComputeTarget.attach(
        ws,
        adla_compute_name,
        attach_config
    )

    adla_compute.wait_for_completion(True)
```

Részletesebb példaként tekintse meg a GitHubon egy [példát a notebookra](https://aka.ms/pl-adla) .

> [!WARNING]
> Ne hozzon létre egyszerre több, egyidejű mellékletet ugyanahhoz a ADLA a munkaterületről. Minden új melléklet megtöri az előző meglévő melléklet (eke) t.

> [!TIP]
> Azure Machine Learning folyamatok csak az Data Lake Analytics-fiók alapértelmezett adattárában tárolt adatmennyiségek esetében használhatók. Ha a működéséhez szükséges adatmennyiség nem alapértelmezett tárolóban található, akkor az a használatával [`DataTransferStep`](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.data_transfer_step.datatransferstep) másolhatja az Adatmásolást a betanítás előtt.

## <a name="azure-container-instance"></a><a id="aci"></a>Azure Container-példány

A modell telepítésekor a Azure Container Instances (ACI) dinamikusan jön létre. Más módon nem hozhat létre vagy csatolhat ACI-t a munkaterülethez. További információ: [modell üzembe helyezése Azure Container instances](how-to-deploy-azure-container-instance.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az Azure Kubernetes Service (ak) számos konfigurációs lehetőséget biztosít a Azure Machine Learning használata esetén. További információ: [Az Azure Kubernetes szolgáltatás létrehozása és csatlakoztatása](how-to-create-attach-kubernetes.md).


## <a name="notebook-examples"></a>Jegyzetfüzet-példák

Tekintse meg ezeket a jegyzetfüzeteket a különböző számítási célokból származó képzésekre:
* [használati útmutató – azureml/képzés](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training)
* [oktatóanyagok/IMG-Classification-part1-Training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések

* A számítási erőforrás használatával [konfigurálhatja és elküldheti a betanítási futtatást](how-to-set-up-training-targets.md).
* [Oktatóanyag: a betanítási modell](tutorial-train-models-with-aml.md) felügyelt számítási célt használ a modellek betanításához.
* Ismerje meg, hogy miként lehet [hatékonyan hangolni a hiperparaméterek beállítása](how-to-tune-hyperparameters.md) a jobb modellek létrehozásához.
* A betanított modellel megtudhatja, [Hogyan és hol helyezheti üzembe a modelleket](how-to-deploy-and-where.md).
* [Azure Machine Learning használata az Azure Virtual Networks használatával](./how-to-network-security-overview.md)