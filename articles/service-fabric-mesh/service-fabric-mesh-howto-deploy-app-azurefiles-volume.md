---
title: Azure Files alapú kötet használata Service Fabric Mesh-alkalmazásban
description: Megtudhatja, hogyan tárolhatja az állapotot egy Azure Service Fabric Mesh alkalmazásban, ha egy Azure Files-alapú kötetet csatlakoztat egy szolgáltatáson belül az Azure CLI használatával.
author: georgewallace
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: gwallace
ms.custom: mvc, devcenter , devx-track-azurecli
ms.openlocfilehash: 40d10568e13ad455bc5178821da80e89f4132e93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625837"
---
# <a name="mount-an-azure-files-based-volume-in-a-service-fabric-mesh-application"></a>Azure Files-alapú kötet csatlakoztatása egy Service Fabric Mesh-alkalmazásban 

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez a cikk azt ismerteti, hogyan csatlakoztathat egy Azure Files-alapú kötetet egy Service Fabric Mesh-alkalmazás szolgáltatásában.  A Azure Files kötet-illesztőprogram egy olyan Docker-kötet-illesztőprogram, amellyel Azure Files-megosztást csatlakoztathat egy tárolóhoz, amelyet a szolgáltatás állapotának megőrzése érdekében használ. A kötetek általános célú fájlmegosztást biztosítanak, és lehetővé teszik a fájlok olvasását/írását a normál lemez I/O-fájl API-jai használatával.  Ha többet szeretne megtudni az alkalmazásadatok tárolásához szükséges kötetekről és lehetőségekről, olvassa el a [tárolás állapotát](service-fabric-mesh-storing-state.md).

Ha kötetet szeretne csatlakoztatni egy szolgáltatásban, hozzon létre egy kötet-erőforrást a Service Fabric Mesh alkalmazásban, majd hivatkozzon a kötetre a szolgáltatásban.  A mennyiségi erőforrás deklarálása és a szolgáltatási erőforrásban való hivatkozása a [YAML-alapú erőforrás-fájlokban](#declare-a-volume-resource-and-update-the-service-resource-yaml) vagy a [JSON-alapú telepítési sablonban](#declare-a-volume-resource-and-update-the-service-resource-json)végezhető el. A kötet csatlakoztatása előtt először hozzon létre egy Azure Storage-fiókot és egy [fájlmegosztást a Azure Filesban](../storage/files/storage-how-to-create-file-share.md).

## <a name="prerequisites"></a>Előfeltételek
> [!NOTE]
> **Ismert probléma az üzembe helyezéssel a Windows RS5 fejlesztői gépen:** A PowerShell-parancsmaggal New-SmbGlobalMapping a RS5 Windows rendszerű gépeken, amelyek meggátolják a Azurefile-kötetek csatlakoztatását. Az alábbi példa olyan hibát észlel, amely akkor fordul elő, ha a AzureFile-alapú kötetet a helyi fejlesztési gépre csatlakoztatja.
```
Error event: SourceId='System.Hosting', Property='CodePackageActivation:counterService:EntryPoint:131884291000691067'.
There was an error during CodePackage activation.System.Fabric.FabricException (-2147017731)
Failed to start Container. ContainerName=sf-2-63fc668f-362d-4220-873d-85abaaacc83e_6d6879cf-dd43-4092-887d-17d23ed9cc78, ApplicationId=SingleInstance_0_App2, ApplicationName=fabric:/counterApp. DockerRequest returned StatusCode=InternalServerError with ResponseBody={"message":"error while mounting volume '': mount failed"}
```
A probléma megkerülő megoldása 1) futtassa az alábbi parancsot a PowerShell-rendszergazdaként, és 2.) indítsa újra a gépet.
```powershell
PS C:\WINDOWS\system32> Mofcomp c:\windows\system32\wbem\smbwmiv2.mof
```

A cikk végrehajtásához használhatja az Azure CLI Azure Cloud Shell vagy helyi telepítését is. 

Ha az Azure CLI-t helyileg szeretné használni a cikkben, győződjön meg arról, hogy `az --version` legalább a értéket adja vissza `azure-cli (2.0.43)` .  Az alábbi [utasításokat](service-fabric-mesh-howto-setup-cli.md)követve telepítse (vagy frissítse) az Azure Service FABRIC Mesh CLI bővítmény modulját.

Az Azure-ba való bejelentkezéshez és az előfizetés beállításához:

```azurecli
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-storage-account-and-file-share-optional"></a>Storage-fiók és-fájlmegosztás létrehozása (nem kötelező)
Azure Files kötet csatlakoztatásához Storage-fiók és fájlmegosztás szükséges.  Használhat meglévő Azure Storage-fiókot és-fájlmegosztást, vagy erőforrásokat is létrehozhat:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az storage account create --name myStorageAccount --resource-group myResourceGroup --location eastus --sku Standard_LRS --kind StorageV2

$current_env_conn_string=$(az storage account show-connection-string -n myStorageAccount -g myResourceGroup --query 'connectionString' -o tsv)

az storage share create --name myshare --quota 2048 --connection-string $current_env_conn_string
```

## <a name="get-the-storage-account-name-and-key-and-the-file-share-name"></a>Szerezze be a Storage-fiók nevét és kulcsát, valamint a fájlmegosztás nevét
A Storage-fiók neve, a Storage-fiók kulcsa, valamint a fájlmegosztás neve a `<storageAccountName>` `<storageAccountKey>` következő részekben, és a fájlokra hivatkozik `<fileShareName>` . 

Sorolja fel a Storage-fiókokat, és szerezze be a Storage-fiók nevét a használni kívánt fájlmegosztás használatával:
```azurecli-interactive
az storage account list
```

A fájlmegosztás nevének beolvasása:
```azurecli-interactive
az storage share list --account-name <storageAccountName>
```

A Storage-fiók kulcsának beszerzése ("key1"):
```azurecli-interactive
az storage account keys list --account-name <storageAccountName> --query "[?keyName=='key1'].value"
```

Ezeket az értékeket a [Azure Portal](https://portal.azure.com)is megtalálhatja:
* `<storageAccountName>` – A **Storage-fiókok** területen a fájlmegosztás létrehozásához használt Storage-fiók neve.
* `<storageAccountKey>` – Válassza ki a Storage-fiókot a **Storage-fiókok** területen, majd válassza a **hozzáférési kulcsok** lehetőséget, és használja a **key1** alatti értéket.
* `<fileShareName>` – Válassza ki a Storage-fiókot a  **Storage-fiókok** területen, majd válassza a **fájlok** lehetőséget. A használandó név a létrehozott fájlmegosztás neve.

## <a name="declare-a-volume-resource-and-update-the-service-resource-json"></a>Mennyiségi erőforrás deklarálása és a szolgáltatási erőforrás (JSON) frissítése

Paraméterek hozzáadása az `<fileShareName>` `<storageAccountName>` `<storageAccountKey>` előző lépésben megtalált, és értékekhez. 

Hozzon létre egy kötet-erőforrást az alkalmazás-erőforrás társaként. Adja meg a nevet és a szolgáltatót ("SFAzureFile") a Azure Files-alapú kötet használatára). A alkalmazásban `azureFileParameters` határozza meg az `<fileShareName>` `<storageAccountName>` `<storageAccountKey>` előző lépésben megtalált, és értékek paramétereit.

Ha a kötetet a szolgáltatásban szeretné csatlakoztatni, vegyen fel egy `volumeRefs` `codePackages` elemet a szolgáltatás eleméhez.  `name` a kötet erőforrás-azonosítója (vagy a kötet erőforrásának telepítési sablon paramétere), valamint a Volume. YAML fájlban deklarált kötet neve.  `destinationPath` a a helyi könyvtár, amelyhez a kötet csatlakoztatva lesz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "defaultValue": "EastUS",
      "type": "String",
      "metadata": {
        "description": "Location of the resources."
      }
    },
    "fileShareName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure Files file share that provides the volume for the container."
      }
    },
    "storageAccountName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Azure storage account that contains the file share."
      }
    },
    "storageAccountKey": {
      "type": "securestring",
      "metadata": {
        "description": "Access key for the Azure storage account that contains the file share."
      }
    },
    "stateFolderName": {
      "type": "string",
      "defaultValue": "TestVolumeData",
      "metadata": {
        "description": "Folder in which to store the state. Provide an empty value to create a unique folder for each container to store the state. A non-empty value will retain the state across deployments, however if more than one applications are using the same folder, the counter may update more frequently."
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "VolumeTest",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/VolumeTestNetwork",
        "Microsoft.ServiceFabricMesh/volumes/testVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "VolumeTestService",
            "properties": {
              "description": "VolumeTestService description.",
              "osType": "Windows",
              "codePackages": [
                {
                  "name": "VolumeTestService",
                  "image": "volumetestservice:dev",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]",
                      "destinationPath": "C:\\app\\data"
                    }
                  ],
                  "environmentVariables": [
                    {
                      "name": "ASPNETCORE_URLS",
                      "value": "http://+:20003"
                    },
                    {
                      "name": "STATE_FOLDER_NAME",
                      "value": "[parameters('stateFolderName')]"
                    }
                  ],
                  ...
                }
              ],
              ...
            }
          }
        ],
        "description": "VolumeTest description."
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "testVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for the test application.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
    ...
  ]
}
```

## <a name="declare-a-volume-resource-and-update-the-service-resource-yaml"></a>Mennyiségi erőforrás deklarálása és a szolgáltatási erőforrás (YAML) frissítése

Vegyen fel egy új *Volume. YAML* fájlt az alkalmazásához tartozó alkalmazás- *erőforrások* könyvtárba.  Adja meg a nevet és a szolgáltatót ("SFAzureFile") a Azure Files-alapú kötet használatára). `<fileShareName>``<storageAccountName>`a, és `<storageAccountKey>` az egy előző lépésben megtalált értékek.

```yaml
volume:
  schemaVersion: 1.0.0-preview2
  name: testVolume
  properties:
    description: Azure Files storage volume for counter App.
    provider: SFAzureFile
    azureFileParameters: 
        shareName: <fileShareName>
        accountName: <storageAccountName>
        accountKey: <storageAccountKey>
```

Frissítse a Service *. YAML* fájlt a szolgáltatás *erőforrásainak* könyvtárában a kötet a szolgáltatásban való csatlakoztatásához.  Adja hozzá az `volumeRefs` elemet a `codePackages` elemhez.  `name` a kötet erőforrás-azonosítója (vagy a kötet erőforrásának telepítési sablon paramétere), valamint a Volume. YAML fájlban deklarált kötet neve.  `destinationPath` a a helyi könyvtár, amelyhez a kötet csatlakoztatva lesz.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: VolumeTest
  properties:
    services:
      - name: VolumeTestService
        properties:
          description: VolumeTestService description.
          osType: Windows
          codePackages:
            - name: VolumeTestService
              image: volumetestservice:dev
              volumeRefs:
                - name: "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'testVolume')]"
                  destinationPath: C:\app\data
              endpoints:
                - name: VolumeTestServiceListener
                  port: 20003
              environmentVariables:
                - name: ASPNETCORE_URLS
                  value: http://+:20003
                - name: STATE_FOLDER_NAME
                  value: TestVolumeData
              resources:
                requests:
                  cpu: 0.5
                  memoryInGB: 1
          replicaCount: 1
          networkRefs:
            - name: VolumeTestNetwork
```

## <a name="next-steps"></a>Következő lépések

- Tekintse meg a Azure Files mennyiségi minta alkalmazást a [githubon](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- A Service Fabric-erőforrásmodellel kapcsolatos további tudnivalókért lásd a [Service Fabric Mesh-erőforrásmodellt](service-fabric-mesh-service-fabric-resources.md) bemutató cikket.
- A Service Fabric Meshsel kapcsolatos további információkért olvassa el a [Service Fabric Mesh áttekintésével](service-fabric-mesh-overview.md) foglalkozó cikket.
