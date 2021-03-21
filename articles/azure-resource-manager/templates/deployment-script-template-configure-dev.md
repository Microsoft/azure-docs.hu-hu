---
title: Fejlesztői környezet konfigurálása telepítési parancsfájlok számára a sablonokban | Microsoft Docs
description: A Azure Resource Manager-sablonokban (ARM-sablonok) lévő telepítési parancsfájlok fejlesztői környezetének konfigurálása.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102200937"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Fejlesztési környezet konfigurálása az ARM-sablonok üzembe helyezési parancsfájljaihoz

Megtudhatja, hogyan hozhat létre fejlesztési környezetet az ARM-sablonok üzembe helyezési parancsfájljainak telepítéséhez és teszteléséhez telepítési parancsfájlokkal. Létrehozhat egy [Azure Container-példányt](../../container-instances/container-instances-overview.md) , vagy használhatja a [Docker](https://docs.docker.com/get-docker/)-t is. Ebben a cikkben mindkét lehetőség szerepel.

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-powershell-container"></a>Azure PowerShell tároló

Ha nem rendelkezik Azure PowerShell telepítési parancsfájllal, a következő tartalom használatával hozhat létre *hello.ps1* -fájlt:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI-tároló

Az Azure CLI-tárolók rendszerképéhez a következő tartalommal hozhat létre *Hello.sh* -fájlt:

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> Az Azure CLI üzembe helyezési parancsfájl futtatásakor a nevű környezeti változó `AZ_SCRIPTS_OUTPUT_PATH` tárolja a parancsfájl kimeneti fájljának helyét. A környezeti változó nem érhető el a fejlesztői környezet tárolójában. További információ az Azure CLI-kimenetek használatáról: [kimenetek használata CLI-parancsfájlból](deployment-script-template.md#work-with-outputs-from-cli-script).

## <a name="use-azure-powershell-container-instance"></a>Azure PowerShell Container-példány használata

A parancsfájlok a számítógépen való létrehozásához létre kell hoznia egy Storage-fiókot, és csatlakoztatnia kell a Storage-fiókot a Container-példányhoz. Így feltöltheti a parancsfájlt a Storage-fiókba, és futtathatja a szkriptet a Container példányon.

> [!NOTE]
> A parancsfájl teszteléséhez létrehozott Storage-fiók nem ugyanaz a Storage-fiók, amelyet a telepítési parancsfájl-szolgáltatás a parancsfájl végrehajtásához használ. Az üzembe helyezési parancsfájl szolgáltatás minden végrehajtáskor egy egyedi nevet hoz létre fájlmegosztásként.

### <a name="create-an-azure-powershell-container-instance"></a>Azure PowerShell tároló példányának létrehozása

A következő Azure Resource Manager sablon (ARM-sablon) létrehoz egy tároló példányt és egy fájlmegosztást, majd csatlakoztatja a fájlmegosztást a tároló lemezképéhez.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

A csatlakoztatási útvonal alapértelmezett értéke a következő: `/mnt/azscripts/azscriptinput` . Ez a tároló példányának elérési útja, amely a fájlmegosztást csatlakoztatja.

A sablonban megadott alapértelmezett **MCR.microsoft.com/azuredeploymentscripts-PowerShell:Az5.2**. Tekintse meg az összes [támogatott Azure PowerShell-verzió](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list)listáját.

A sablon 1 800 másodperc után felfüggeszti a tároló példányát. 30 perccel azelőtt, hogy a Container-példány véges állapotba kerül, és a munkamenet véget ér.

A sablon üzembe helyezése:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Az üzembe helyezési parancsfájl feltöltése

Töltse fel a telepítési szkriptet a Storage-fiókba. Példa egy PowerShell-szkriptre:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

A fájlt a Azure Portal vagy az Azure CLI használatával is feltöltheti.

### <a name="test-the-deployment-script"></a>Az üzembe helyezési parancsfájl tesztelése

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amelyben üzembe helyezte a tároló-példányt és a Storage-fiókot.
2. Nyissa meg a tároló csoportot. Az alapértelmezett tároló csoport neve a *CG*-vel hozzáfűzött projekt neve. A tároló példánya **futó** állapotban van.
3. Az erőforrás menüben válassza a **tárolók** lehetőséget. A tároló-példány neve a projekt neve a *tárolóval* hozzáfűzve.

    ![Képernyőfelvétel: a telepítési parancsfájl összekapcsolási példánya a Azure Portal.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Válassza a **kapcsolat**, majd a **kapcsolat** lehetőséget. Ha nem tud csatlakozni a Container-példányhoz, indítsa újra a tároló csoportot, és próbálkozzon újra.
5. A konzol ablaktábláján futtassa a következő parancsokat:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    A kimenet **Hello John Dole**.

    ![Képernyőkép az üzembe helyezési parancsfájlhoz kapcsolódó tároló-példány tesztelési kimenetéről a konzolon.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Azure CLI-tároló példányának használata

A parancsfájlok a számítógépen való létrehozásához hozzon létre egy Storage-fiókot, és csatlakoztassa a Storage-fiókot a Container-példányhoz. Ezután feltöltheti a parancsfájlt a Storage-fiókba, és futtathatja a szkriptet a Container példányon.

> [!NOTE]
> A parancsfájl teszteléséhez létrehozott Storage-fiók nem ugyanaz a Storage-fiók, amelyet a telepítési parancsfájl-szolgáltatás a parancsfájl végrehajtásához használ. Az üzembe helyezési parancsfájl szolgáltatás minden végrehajtáskor egy egyedi nevet hoz létre fájlmegosztásként.

### <a name="create-an-azure-cli-container-instance"></a>Azure CLI-tároló példányának létrehozása

A következő ARM-sablon létrehoz egy tároló-példányt és egy fájlmegosztást, majd csatlakoztatja a fájlmegosztást a tároló lemezképéhez:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

A csatlakoztatási útvonal alapértelmezett értéke a következő: `/mnt/azscripts/azscriptinput` . Ez a tároló példányának elérési útja, amely a fájlmegosztást csatlakoztatja.

A sablonban megadott alapértelmezett **MCR.microsoft.com/Azure-CLI:2.9.1**. Tekintse meg a [támogatott Azure CLI-verziók](https://mcr.microsoft.com/v2/azure-cli/tags/list)listáját.

> [!IMPORTANT]
> Az üzembehelyezési parancsfájl a Microsoft Container Registry (MCR) által elérhető CLI-rendszerképeket használja. Egy hónapot vesz igénybe egy telepítési parancsfájl CLI-rendszerképének igazolásához. Ne használja a 30 napon belül kiadott CLI-verziókat. A képek kiadási dátumait az [Azure CLI kibocsátási megjegyzései](/cli/azure/release-notes-azure-cli)című témakörben találja. Ha nem támogatott verziót használ, a hibaüzenet felsorolja a támogatott verziókat.

A sablon 1 800 másodperc után felfüggeszti a tároló példányát. 30 perccel azelőtt, hogy a tároló példánya terminál állapotba kerül, és a munkamenet véget ér.

A sablon üzembe helyezése:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Az üzembe helyezési parancsfájl feltöltése

Töltse fel a telepítési szkriptet a Storage-fiókba. A következő egy PowerShell-példa:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

A fájlt a Azure Portal vagy az Azure CLI használatával is feltöltheti.

### <a name="test-the-deployment-script"></a>Az üzembe helyezési parancsfájl tesztelése

1. A Azure Portal nyissa meg azt az erőforráscsoportot, amelyben üzembe helyezte a tároló-példányt és a Storage-fiókot.
1. Nyissa meg a tároló csoportot. Az alapértelmezett tároló csoport neve a *CG*-vel hozzáfűzött projekt neve. A tároló példánya a **futó** állapotban jelenik meg.
1. Az erőforrás menüben válassza a **tárolók** lehetőséget. A tároló-példány neve a projekt neve a *tárolóval* hozzáfűzve.

    ![üzembe helyezési parancsfájl összekapcsolási tárolójának példánya](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Válassza a **kapcsolat**, majd a **kapcsolat** lehetőséget. Ha nem tud csatlakozni a Container-példányhoz, indítsa újra a tároló csoportot, és próbálkozzon újra.
1. A konzol ablaktábláján futtassa a következő parancsokat:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    A kimenet **Hello John Dole**.

    ![üzembehelyezési parancsfájl-tároló példányának tesztelése](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>A Docker használata

Egy előre konfigurált Docker-tároló rendszerképet is használhat a telepítési parancsfájl fejlesztési környezete számára. A Docker telepítéséhez lásd: [Docker beolvasása](https://docs.docker.com/get-docker/).
A fájlmegosztást úgy is be kell állítania, hogy csatlakoztassa a könyvtárat, amely tartalmazza az üzembe helyezési parancsfájlokat a Docker-tárolóba.

1. Kérje le a telepítési parancsfájl tárolójának rendszerképét a helyi számítógépre:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    A példa a PowerShell-4.3.0 verzióját használja.

    CLI-rendszerkép lekérése egy MCR:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Ez a példa a CLI-2.0.80 verzióját használja. Az üzembe helyezési parancsfájl az [itt](https://hub.docker.com/_/microsoft-azure-cli)található alapértelmezett CLI-tárolók rendszerképeit használja.

1. Futtassa helyileg a Docker-rendszerképet.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Cserélje le a **&lt; gazdagép illesztőprogramjának betűjelét>** és az **&lt; állomásnév nevét>** egy meglévő mappát a megosztott meghajtón. Leképezi a mappát a tároló _/Data_ mappájába. Például a _D:\docker_ leképezéséhez:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **– Ez azt jelenti, hogy** a tároló rendszerképét életben tartja.

    Egy CLI-példa:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Az alábbi képernyőfelvételen egy PowerShell-szkript futtatását láthatja, mivel a megosztott meghajtón *helloworld.ps1* -fájl található.

    ![Resource Manager-sablon üzembe helyezési parancsfájl Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

A parancsfájl sikeres tesztelése után a sablonban használható üzembe helyezési parancsfájlként.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan használhatja a telepítési parancsfájlokat. Útmutató az üzembe helyezési parancsfájlhoz:

> [!div class="nextstepaction"]
> [Oktatóanyag: üzembe helyezési parancsfájlok használata ARM-sablonokban](./template-tutorial-deployment-script.md)
