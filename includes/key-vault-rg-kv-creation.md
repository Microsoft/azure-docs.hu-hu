---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6d6c4a019e9b0b9bd4ed52990fa8b59e939026f1
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "106072960"
---
Ez a rövid útmutató egy előre létrehozott Azure Key vaultot használ. Kulcstartó létrehozásához kövesse az [Azure CLI](../articles/key-vault/general/quick-create-cli.md)gyors üzembe helyezésének lépéseit, [Azure PowerShell](../articles/key-vault/general/quick-create-powershell.md)a gyors üzembe helyezést, vagy [Azure Portal](../articles/key-vault/general/quick-create-portal.md)a gyors üzembe helyezést. 

Azt is megteheti, hogy egyszerűen futtatja az Azure CLI-t vagy Azure PowerShell-parancsokat.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName myResourceGroup -Location eastus
```
---