---
title: 'Gyors útmutató: Azure Data Factory létrehozása a Python használatával'
description: Létrehozhat egy Azure-beli adat-előállítót az adatok egy Azure Blob Storage-beli helyről egy másik helyre történő másolásához.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.devlang: python
ms.topic: quickstart
ms.date: 01/22/2018
ms.custom: seo-python-october2019, tracking-python
ms.openlocfilehash: 3a40ff7fbf021833ff382ad2288eb263378c96c2
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2020
ms.locfileid: "85512596"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Gyors útmutató: adatfeldolgozó és-folyamat létrehozása a Python használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuális verzió](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ebben a rövid útmutatóban egy adatelőállítót hoz létre a Python használatával. Az adatfeldolgozó folyamata az Azure Blob Storage egyik mappájából egy másikba másolja az adatait.

A Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi adatvezérelt munkafolyamatok létrehozását az adatáthelyezés és az adatátalakítások előkészítéséhez és automatizálásához. A Azure Data Factory használatával adatvezérelt munkafolyamatokat hozhat létre és ütemezhet, folyamatok néven.

A folyamatok különböző adattárakból származó adatok betöltésére képesek. A folyamatok számítási szolgáltatások, például Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning használatával dolgozzák fel vagy alakítják át az adatfeldolgozást. A folyamatok kimeneti adatokat tesznek közzé az adattárakban, például a Azure SQL Data Warehouse for Business Intelligence (BI) alkalmazásaiban.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Egy Azure Storage-fiók](../storage/common/storage-account-create.md).

* [Azure Storage Explorer](https://storageexplorer.com/) (nem kötelező).

* [Egy alkalmazás a Azure Active Directoryban](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Jegyezze fel a következő értékeket a későbbi lépésekben való használathoz: **alkalmazás azonosítója**, **hitelesítési kulcs**és **bérlő azonosítója**. Rendelje hozzá az alkalmazást a **közreműködő** szerepkörhöz az ugyanebben a cikkben található utasításokat követve.

## <a name="create-and-upload-an-input-file"></a>Bemeneti fájl létrehozása és feltöltése

1. Indítsa el a Jegyzettömböt. Másolja be az alábbi szöveget, és mentse egy **input.txt** nevű fájlként a lemezen.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  Az [Azure Storage Explorerrel](https://storageexplorer.com/) vagy egy hozzá hasonló eszközzel hozza létre az **adfv2tutorial** tárolót, és a tárolóban az **input** mappát. Ezután töltse fel az **input.txt** fájlt az **input** mappába.

## <a name="install-the-python-package"></a>Python-csomag telepítése

1. Nyisson meg egy terminált vagy parancssort rendszergazdai jogosultságokkal. 
2. Először telepítse a Python-csomagot az Azure felügyeleti erőforrásokhoz:

    ```python
    pip install azure-mgmt-resource
    ```
3. A Python-csomag a Data Factoryhoz történő telepítéséhez futtassa az alábbi parancsot:

    ```python
    pip install azure-mgmt-datafactory
    ```

    A [PYTHON SDK for Data Factory](https://github.com/Azure/azure-sdk-for-python) támogatja a Python 2,7, 3,3, 3,4, 3,5, 3,6 és 3,7.

## <a name="create-a-data-factory-client"></a>Adat-előállító ügyfél létrehozása

1. Hozzon létre egy **datafactory.py** nevű fájlt. Adja hozzá az alábbi utasításokat, hogy a névterekre mutató hivatkozásokat tudjon felvenni.

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Adja hozzá az alábbi függvényeket az adatok megjelenítéséhez.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy DataPipelineManagementClient osztályú példányt. Ezzel az objektummal adat-előállítót, társított szolgáltatást, adatkészleteket és folyamatot hozhat létre. Ezenfelül ez az objektum a folyamat futása részleteinek monitorozására is használható. Állítsa be a **subscription_id** változót az Azure-előfizetés azonosítójaként. Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<Specify your Azure Subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = 'ADFTutorialResourceGroup'

        # The data factory name. It must be globally unique.
        df_name = '<Specify a name for the data factory. It must be globally unique>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ServicePrincipalCredentials(client_id='<Active Directory application/client ID>', secret='<client secret>', tenant='<Active Directory tenant ID>')
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'eastus'}
        df_params = {'location':'eastus'}
    ```

## <a name="create-a-data-factory"></a>Data factory létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza az **adat-előállítót**. Ha az erőforráscsoportja már létezik, tegye megjegyzésbe az első `create_or_update` utasítást.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Társított szolgáltatás létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehoz egy **Azure Storage-beli társított szolgáltatást**.

Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a rövid útmutatóban csak egy Azure Storage-beli társított szolgáltatást kell létrehoznia a másolás forrásaként és fogadó adattárként. A példában ennek a neve: AzureStorageLinkedService. A `<storageaccountname>` és a `<storageaccountkey>` kifejezés helyére írja be Azure Storage-fiókja nevét, illetve kulcsát.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>')

    ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Adatkészletek létrehozása

Ebben a részben két adatkészletet hoz létre: egyet a forráshoz és egyet a fogadóhoz.

### <a name="create-a-dataset-for-source-azure-blob"></a>Adatkészlet létrehozása a forrás Azure Blobhoz

Adja hozzá a következő kódot a Main metódushoz, amely létrehoz egy Azure Blob-adatkészletet. Az Azure Blob-adatkészlet tulajdonságaival kapcsolatos információkért tekintse meg az [Azure Blob-összekötőt](connector-azure-blob-storage.md#dataset-properties) ismertető cikket.

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path= 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob= AzureBlobDataset(linked_service_name=ds_ls, folder_path=blob_path, file_name = blob_filename)
    ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Adatkészlet létrehozása a fogadó Azure Blobhoz

Adja hozzá a következő kódot a Main metódushoz, amely létrehoz egy Azure Blob-adatkészletet. Az Azure Blob-adatkészlet tulajdonságaival kapcsolatos információkért tekintse meg az [Azure Blob-összekötőt](connector-azure-blob-storage.md#dataset-properties) ismertető cikket.

Meghatároz egy adatkészletet, amely a forrásadatokat jelöli az Azure Blobban. Ez a blob-adatkészlet az előző lépésben létrehozott Azure Storage-beli társított szolgáltatásra vonatkozik.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Folyamat létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely létrehozza a **másolási tevékenységet tartalmazó folyamatot**.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása

Adja hozzá a következő kódot a **Main** metódushoz, amely **elindítja a folyamat futását**.

```python
    #Create a pipeline run.
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>Folyamat futásának monitorozása

A folyamat futtatásának monitorozásához adja hozzá a következő kódot a **Main** metódushoz:

```python
    #Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Most adja meg az alábbi utasítást, hogy a rendszer meghívja a **main** metódust, amikor a program fut:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Teljes szkript

Itt látható a teljes Python-kód:

```python
from azure.common.credentials import ServicePrincipalCredentials
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time


def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)
    print("\n")


def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n")


def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(
            activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(
            activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(
            activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<your Azure subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<Azure resource group name>'

    # The data factory name. It must be globally unique.
    df_name = '<Your data factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ServicePrincipalCredentials(
        client_id='<Active Directory client ID>', secret='<client secret>', tenant='<tenant ID>')
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location': 'eastus'}
    df_params = {'location': 'eastus'}

    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='eastus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService'

    # Specify the name and key of your Azure Storage account
    storage_string = SecureString(
        value='DefaultEndpointsProtocol=https;AccountName=<storage account name>;AccountKey=<storage account key>')

    ls_azure_storage = AzureStorageLinkedService(
        connection_string=storage_string)
    ls = adf_client.linked_services.create_or_update(
        rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = 'adfv2tutorial/input'
    blob_filename = 'input.txt'
    ds_azure_blob = AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = 'adfv2tutorial/output'
    dsOut_azure_blob = AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath)
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>A kód futtatása

Állítsa össze és indítsa el az alkalmazást, majd ellenőrizze a folyamat-végrehajtást.

A konzol megjeleníti az adat-előállító, a társított szolgáltatás, az adatkészletek, a folyamat, valamint a folyamat futása létrehozási állapotát. Várjon, amíg megjelennek a másolási tevékenység futásának részletei, beleértve az olvasott és írt adatok méretét. Ezután például az [Azure Storage Explorerhez](https://azure.microsoft.com/features/storage-explorer/) hasonló eszközök használatával ellenőrizheti, hogy a blobok a változókban megadottak szerint át lettek-e másolva az outputBlobPath helyre az inputBlobPath helyről.

Itt látható a minta kimenete:

```json
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az adat-előállító törléséhez adja hozzá az alábbi kódot a programhoz:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Következő lépések

A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. A Data Factory más forgatókönyvekben való használatát ismertető további információkért tekintse meg az [oktatóanyagokat](tutorial-copy-data-dot-net.md).
