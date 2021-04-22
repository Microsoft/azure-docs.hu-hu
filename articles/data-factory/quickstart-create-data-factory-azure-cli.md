---
title: 'Rövid útmutató: Azure Data Factory létrehozása az Azure CLI használatával'
description: Ez a rövid útmutató egy Azure Data Factory hoz létre, beleértve a csatolt szolgáltatást, az adatkészleteket és a folyamatot. A folyamat futtatásával fájlmásoló műveletet is futtathat.
author: linda33wj
ms.author: jingwang
ms.service: azure-cli
ms.topic: quickstart
ms.date: 03/24/2021
ms.custom:
- template-quickstart
- devx-track-azurecli
ms.openlocfilehash: b40407f4c4fb81bbf76bd0b552f3c9f2c827232a
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871513"
---
# <a name="quickstart-create-an-azure-data-factory-using-azure-cli"></a>Rövid útmutató: Azure Data Factory létrehozása az Azure CLI használatával

Ez a rövid útmutató azt ismerteti, hogyan használható az Azure CLI egy Azure Data Factory. Az adat-előállítóban létrehozott folyamat adatokat másol egy másik mappába egy Azure Blob Storage. További információ az adatok átalakításával kapcsolatban a Azure Data Factory adatok [átalakításával](transform-data.md)a Azure Data Factory.

Ha szeretné megismerni az Azure Data Factoryt, tekintse meg [Az Azure Data Factory bemutatását](introduction.md).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

[!INCLUDE [azure-cli-prepare-your-environment](../../includes/azure-cli-prepare-your-environment.md)]

> [!NOTE]
> Data Factory-példányok létrehozásához az Azure-ba történő bejelentkezéshez használt felhasználói fióknak a közreműködő vagy tulajdonos szerepkör tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie. További információ: [Azure-szerepkörök.](quickstart-create-data-factory-powershell.md#azure-roles)

## <a name="prepare-a-container-and-test-file"></a>Tároló előkészítése és tesztfájl

Ez a rövid útmutató egy Azure Storage-fiókot használ, amely tartalmaz egy fájlt tartalmazó tárolót.

1. A nevű erőforráscsoport létrehozásához `ADFQuickStartRG` használja az az group [create](/cli/azure/group#az_group_create) parancsot:

   ```azurecli
   az group create --name ADFQuickStartRG --location eastus
   ```

1. Hozzon létre egy tárfiókot [az az storage account create paranccsal:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage account create --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --location eastus
   ```

1. Hozzon létre egy nevű `adftutorial` tárolót [az az storage container create paranccsal:](/cli/azure/storage/container#az_storage_container_create)

   ```azurecli
   az storage container create --resource-group ADFQuickStartRG --name adftutorial \
       --account-name adfquickstartstorage --auth-mode key
   ```

1. A helyi könyvtárban hozzon létre egy nevű fájlt a `emp.txt` feltöltéshez. Ha az aktuális munkakönyvtárban Azure Cloud Shell, az aktuális munkakönyvtárat a `echo $PWD` Bash-paranccsal találhatja meg. Fájl létrehozásához használhat szabványos Bash-parancsokat, például `cat` a parancsot:

   ```console
   cat > emp.txt
   This is text.
   ```

   Mentse az új fájlt a **Ctrl+D** billentyűkombinációval.

1. Az új fájl Azure Storage-tárolóba való feltöltéshez használja [az az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) parancsot:

   ```azurecli
   az storage blob upload --account-name adfquickstartstorage --name input/emp.txt \
       --container-name adftutorial --file emp.txt --auth-mode key
   ```

   Ez a parancs egy nevű új mappába tölt fel `input` adatokat.

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

Azure-beli adat-előállító létrehozásához futtassa [az az datafactory factory create](/cli/azure/datafactory/factory#az_datafactory_factory_create) parancsot:

```azurecli
az datafactory factory create --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

> [!IMPORTANT]
> Cserélje `ADFTutorialFactory` le a helyére a globálisan egyedi adat-előállító nevét, például: ADFTutorialFactorySP1127.

A létrehozott adat-előállítót az [az datafactory factory show](/cli/azure/datafactory/factory#az_datafactory_factory_show) paranccsal láthatja:

```azurecli
az datafactory factory show --resource-group ADFQuickStartRG \
    --factory-name ADFTutorialFactory
```

## <a name="create-a-linked-service-and-datasets"></a>Csatolt szolgáltatás és adatkészletek létrehozása

Ezután hozzon létre egy összekapcsolt szolgáltatást és két adatkészletet.

1. Az [az storage account show-connection-string](/cli/azure/datafactory/factory#az_datafactory_factory_show) paranccsal szerezze be a tárfiók kapcsolati sztringet:

   ```azurecli
   az storage account show-connection-string --resource-group ADFQuickStartRG \
       --name adfquickstartstorage --key primary
   ```

1. A munkakönyvtárban hozzon létre egy JSON-fájlt ezzel a tartalommal, amely tartalmazza az előző lépésből származó saját kapcsolati sztringet. A fájlnak nevezze el a `AzureStorageLinkedService.json` következőt:

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

1. Hozzon létre egy nevű csatolt szolgáltatást `AzureStorageLinkedService` az [az datafactory linked-service create paranccsal:](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_create)

   ```azurecli
   az datafactory linked-service create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --linked-service-name AzureStorageLinkedService \
       --properties @AzureStorageLinkedService.json
   ```

1. A munkakönyvtárban hozzon létre egy nevű JSON-fájlt ezzel a `InputDataset.json` tartalommal:

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

1. Hozzon létre egy nevű bemeneti `InputDataset` adatkészletet [az az datafactory dataset create paranccsal:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name InputDataset --factory-name ADFQuickStartFactory \
       --properties @InputDataset.json
   ```

1. A munkakönyvtárban hozzon létre egy nevű JSON-fájlt ezzel a `OutputDataset.json` tartalommal:

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

1. Hozzon létre egy nevű kimeneti `OutputDataset` adatkészletet [az az datafactory dataset create paranccsal:](/cli/azure/datafactory/dataset#az_datafactory_dataset_create)

   ```azurecli
   az datafactory dataset create --resource-group ADFQuickStartRG \
       --dataset-name OutputDataset --factory-name ADFQuickStartFactory \
       --properties @OutputDataset.json
   ```

## <a name="create-and-run-the-pipeline"></a>A folyamat létrehozása és futtatása

Végül hozza létre és futtassa a folyamatot.

1. A munkakönyvtárban hozzon létre egy JSON-fájlt a következő `Adfv2QuickStartPipeline.json` tartalommal:

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

1. Hozzon létre egy nevű `Adfv2QuickStartPipeline` folyamatot az [az datafactory pipeline create paranccsal:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create)

   ```azurecli
   az datafactory pipeline create --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --name Adfv2QuickStartPipeline \
       --pipeline @Adfv2QuickStartPipeline.json
   ```

1. Futtassa a folyamatot az [az datafactory pipeline create-run paranccsal:](/cli/azure/datafactory/pipeline#az_datafactory_pipeline_create_run)

   ```azurecli
   az datafactory pipeline create-run --resource-group ADFQuickStartRG \
       --name Adfv2QuickStartPipeline --factory-name ADFTutorialFactory
   ```

   Ez a parancs futtatásazonosítót ad vissza. Másolja ki a következő parancsban való használathoz.

1. Az [az datafactory pipeline-run show](/cli/azure/datafactory/pipeline-run#az_datafactory_pipeline_run_show) paranccsal ellenőrizze, hogy a folyamat futtatása sikeres volt-e:

   ```azurecli
   az datafactory pipeline-run show --resource-group ADFQuickStartRG \
       --factory-name ADFTutorialFactory --run-id 00000000-0000-0000-0000-000000000000
   ```

Azt is ellenőrizheti, hogy a folyamat a várt módon futott-e az [Azure Portal.](https://portal.azure.com/) További információ: [Az üzembe helyezett erőforrások áttekintése.](quickstart-create-data-factory-powershell.md#review-deployed-resources)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A rövid útmutató összes erőforrása ugyanannak az erőforráscsoportnak a része. Az összes eltávolításához használja az [az group delete](/cli/azure/group#az_group_delete) parancsot:

```azurecli
az group delete --name ADFQuickStartRG
```

Ha ezt az erőforráscsoportot bármi máshoz használja, törölje az egyes erőforrásokat. Például a csatolt szolgáltatás eltávolításához használja az [az datafactory linked-service delete](/cli/azure/datafactory/linked-service#az_datafactory_linked_service_delete) parancsot.

Ebben a rövid útmutatóban a következő JSON-fájlokat hozta létre:

- AzureStorageLinkedService.jsbe
- InputDataset.jsbe
- OutputDataset.jsbe
- Adfv2QuickStartPipeline.jsbe

Törölje őket standard Bash-parancsokkal.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Factory folyamatai és tevékenységei](concepts-pipelines-activities.md)
- [Társított szolgáltatások az Azure Data Factoryben](concepts-linked-services.md)
- [Adathalmazok az Azure Data Factoryben](concepts-datasets-linked-services.md)
- [Adatok átalakítása az Azure Data Factoryben](transform-data.md)
