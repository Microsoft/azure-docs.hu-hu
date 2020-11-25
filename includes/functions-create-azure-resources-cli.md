---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 29a31948c5bfc9b5fd3a31f39144d186dbfe4885
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025484"
---
## <a name="create-supporting-azure-resources-for-your-function"></a>A függvényhez kapcsolódó Azure-erőforrások létrehozása

Mielőtt üzembe helyezi a függvény kódját az Azure-ban, három erőforrást kell létrehoznia:

- Egy erőforráscsoport, amely a kapcsolódó erőforrások logikai tárolója.
- Egy Storage-fiók, amely fenntartja az állapotot és a projektekkel kapcsolatos egyéb információkat.
- Egy Function alkalmazás, amely biztosítja a környezetet a függvény kódjának végrehajtásához. A Function alkalmazás a helyi function projekthez kapcsolódik, és lehetővé teszi, hogy logikai egységként csoportosítsa a függvényeket az erőforrások egyszerűbb felügyelete, üzembe helyezése és megosztása érdekében.

Ezen elemek létrehozásához használja a következő parancsokat. Az Azure CLI és a PowerShell is támogatott.

1. Ha még nem tette meg, jelentkezzen be az Azure-ba:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Az az [login](/cli/azure/reference-index#az_login) parancs aláírja az Azure-fiókját.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    A [AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmag bejelentkezik az Azure-fiókjába.

    ---

1. Hozzon létre egy nevű erőforráscsoportot `AzureFunctionsQuickstart-rg` a `westeurope` régióban:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Az az [Group Create](/cli/azure/group#az_group_create) parancs létrehoz egy erőforráscsoportot. Az erőforráscsoport és az erőforrások általában az Ön közelében lévő régiókban hozhatók létre, a parancs által visszaadott elérhető régió használatával `az account list-locations` .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    A [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) parancs létrehoz egy erőforráscsoportot. A [Get-AzLocation](/powershell/module/az.resources/get-azlocation) parancsmag által visszaadott elérhető régió használatával általában létrehozhatja az erőforráscsoportot és az erőforrásokat egy közeli régióban.

    ---

1. Hozzon létre egy általános célú Storage-fiókot az erőforráscsoport és a régió területén:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Az az [Storage Account Create](/cli/azure/storage/account#az_storage_account_create) parancs létrehozza a Storage-fiókot. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    A [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) parancsmag létrehozza a Storage-fiókot.

    ---

    Az előző példában cserélje le a `<STORAGE_NAME>` nevet, amely megfelel az Ön számára, és egyedi az Azure Storage-ban. A névnek három – 24 karakterből kell állnia, és csak kisbetűket tartalmazhat. `Standard_LRS` a [függvények által támogatott](../articles/azure-functions/storage-considerations.md#storage-account-requirements)általános célú fiókot határozza meg.
