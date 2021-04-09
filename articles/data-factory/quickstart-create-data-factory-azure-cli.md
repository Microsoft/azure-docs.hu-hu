---
title: 'Gyors útmutató: Azure Data Factory létrehozása az Azure CLI használatával'
description: Ez a rövid útmutató létrehoz egy Azure Data Factory, beleértve a társított szolgáltatásokat, az adatkészleteket és a folyamatokat. A folyamat futtatásával elvégezheti a fájlmásolás műveletet.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: 9af5f276e49e9eb2756dc544db353c75c99bc5a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105938062"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Gyors útmutató: Azure Data Factory létrehozása az Azure CLI használatával

Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy Azure Data Factory létrehozásához. Az adatfeldolgozóban létrehozott folyamat az Azure-Blob Storage egy mappájából egy másikba másolja az adatait. Az adatok Azure Data Factory használatával történő átalakításával kapcsolatos információkért lásd: [adatok átalakítása Azure Data Factoryban](transform-data.md).

Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a közreműködő vagy tulajdonos szerepkör tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie. További információ: Azure- [szerepkörök](quickstart-create-data-factory-powershell.md#azure-roles).

## <a name="prepare-a-container-and-test-file"></a>Tároló előkészítése és a fájl tesztelése

Ez a rövid útmutató egy Azure Storage-fiókot használ, amely tartalmaz egy fájlt tartalmazó tárolót.

1. Egy nevű erőforráscsoport létrehozásához `ADFQuickStartRG` használja az az [Group Create](/cli/azure/group#az_group_create) parancsot:

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Hozzon létre egy Storage-fiókot az az [Storage Account Create](/cli/azure/storage/container#az_storage_container_create) paranccsal:

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Hozzon létre egy nevű tárolót `adftutorial` az az [Storage Container Create](/cli/azure/storage/container#az_storage_container_create) parancs használatával:

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. A helyi könyvtárban hozzon létre egy nevű fájlt a `emp.txt` feltöltéshez. Ha Azure Cloud Shell dolgozik, az aktuális munkakönyvtárat a bash parancs használatával találja meg `echo $PWD` . Egy fájl létrehozásához használhatja a standard bash-parancsokat, például a következőt `cat` :

   ```console
   cat > emp.txt
   This is text.
   ```

   Az új fájl mentéséhez használja a **CTRL + D** billentyűkombinációt.

1. Az új fájl Azure Storage-tárolóba való feltöltéséhez használja az az [Storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) parancsot:

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Ez a parancs feltölt egy nevű új mappába `input` .

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Azure-beli adatelőállító létrehozásához futtassa az az [DataFactory Factory Create](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_create) parancsot:

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Cserélje le `ADFTutorialFactory` egy globálisan egyedi adatfeldolgozó-névre, például ADFTutorialFactorySP1127.

Az az [DataFactory Factory show](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) paranccsal megtekintheti a létrehozott adatelőállítót:

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Társított szolgáltatás és adatkészletek létrehozása

Ezután hozzon létre egy társított szolgáltatást és két adatkészletet.

1. Szerezze be a Storage-fiókhoz tartozó kapcsolati karakterláncot az az [Storage Account show-kapcsolat-string](/cli/azure/ext/datafactory/datafactory/factory#ext_datafactory_az_datafactory_factory_show) parancs használatával:

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. A munkakönyvtárában hozzon létre egy JSON-fájlt ezzel a tartalommal, amely tartalmazza az előző lépésben létrehozott saját kapcsolati karakterláncot. Nevezze el a fájlt `AzureStorageLinkedService.json` :

   ```json
   {
       "type":"AzureStorage",
           "typeProperties":{
           "connectionString":{
           "type": "SecureString",
           "value":"DefaultEndpointsProtocol=https;AccountName=adfquickstartstorage;AccountKey=K9F4Xk/EhYrMBIR98rtgJ0HRSIDU4eWQILLh2iXo05Xnr145+syIKNczQfORkQ3QIOZAd/eSDsvED19dAwW/tw==;EndpointSuffix=core.windows.net"
           }
       }
   }
   ```

1. Hozzon létre egy nevű társított szolgáltatást `AzureStorageLinkedService` az az [DataFactory linked-Service Create](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_create) parancs használatával:

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. A munkakönyvtárában hozzon létre egy JSON-fájlt a következő névvel `InputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "input",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Hozzon létre egy nevű bemeneti adatkészletet `InputDataset` az az [DataFactory adatkészlet Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) paranccsal:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. A munkakönyvtárában hozzon létre egy JSON-fájlt a következő névvel `OutputDataset.json` :

   ```json
   {
       "type": 
           "AzureBlob",
           "linkedServiceName": {
               "type":"LinkedServiceReference",
               "referenceName":"AzureStorageLinkedService"
               },
           "annotations": [],
           "type": "Binary",
           "typeProperties": {
               "location": {
                   "type": "AzureBlobStorageLocation",
                   "fileName": "emp.txt",
                   "folderPath": "output",
                   "container": "adftutorial"
           }
       }
   }
   ```

1. Hozzon létre egy nevű kimeneti adatkészletet `OutputDataset` az az [DataFactory adatkészlet Create](/cli/azure/ext/datafactory/datafactory/dataset#ext_datafactory_az_datafactory_dataset_create) paranccsal:

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>A folyamat létrehozása és futtatása

Végül hozza létre és futtassa a folyamatot.

1. A munkakönyvtárában hozzon létre egy JSON-fájlt a következő névvel `Adfv2QuickStartPipeline.json` :

   ```json
   {
       "name": "Adfv2QuickStartPipeline",
       "properties": {
           "activities": [
               {
                   "name": "CopyFromBlobToBlob",
                   "type": "Copy",
                   "dependsOn": [],
                   "policy": {
                       "timeout": "7.00:00:00",
                       "retry": 0,
                       "retryIntervalInSeconds": 30,
                       "secureOutput": false,
                       "secureInput": false
                   },
                   "userProperties": [],
                   "typeProperties": {
                          "source": {
                           "type": "BinarySource",
                           "storeSettings": {
                               "type": "AzureBlobStorageReadSettings",
                               "recursive": true
                           }
                       },
                       "sink": {
                           "type": "BinarySink",
                           "storeSettings": {
                               "type": "AzureBlobStorageWriteSettings"
                           }
                       },
                       "enableStaging": false
                   },
                   "inputs": [
                       {
                           "referenceName": "InputDataset",
                           "type": "DatasetReference"
                       }
                   ],
                   "outputs": [
                       {
                           "referenceName": "OutputDataset",
                           "type": "DatasetReference"
                       }
                   ]
               }
           ],
           "annotations": []
       }
   }
   ```

1. Hozzon létre egy nevű folyamatot `Adfv2QuickStartPipeline` az az [DataFactory pipeline Create](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create) parancs használatával:

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Futtassa a folyamatot az az [DataFactory-folyamat létrehozás-Futtatás](/cli/azure/ext/datafactory/datafactory/pipeline#ext_datafactory_az_datafactory_pipeline_create_run) parancs használatával:

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Ez a parancs egy futtatási azonosítót ad vissza. Másolja a következő parancsban való használatra.

1. Ellenőrizze, hogy a folyamat futása sikeres volt-e az az [DataFactory pipeline-Run show](/cli/azure/ext/datafactory/datafactory/pipeline-run#ext_datafactory_az_datafactory_pipeline_run_show) parancs használatával:

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Azt is ellenőrizheti, hogy a folyamat a várt módon futott-e a [Azure Portal](https://portal.azure.com/)használatával. További információ: [üzembe helyezett erőforrások áttekintése](quickstart-create-data-factory-powershell.md#review-deployed-resources).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az ebben a rövid útmutatóban szereplő összes erőforrás ugyanahhoz az erőforráscsoporthoz tartozik. Az összes eltávolításához használja az az [Group delete](/cli/azure/group#az_group_delete) parancsot:

```azurecli
az group delete --name ADFQuickStartRG
```

Ha ezt az erőforráscsoportot bármilyen más számára használja, az egyes erőforrásokat törölje. Ha például el szeretné távolítani a társított szolgáltatást, használja az az [DataFactory linked-Service delete](/cli/azure/ext/datafactory/datafactory/linked-service#ext_datafactory_az_datafactory_linked_service_delete) parancsot.

Ebben a rövid útmutatóban a következő JSON-fájlokat hozta létre:

- AzureStorageLinkedService.jsbekapcsolva
- InputDataset.jsbekapcsolva
- OutputDataset.jsbekapcsolva
- Adfv2QuickStartPipeline.jsbekapcsolva

A standard bash-parancsok használatával törölje őket.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory folyamatai és tevékenységei](concepts-pipelines-activities.md)
- [Társított szolgáltatások az Azure Data Factoryben](concepts-linked-services.md)
- [Adathalmazok az Azure Data Factoryben](concepts-datasets-linked-services.md)
- [Adatok átalakítása az Azure Data Factoryben](transform-data.md)
