---
title: Key Vault beállítása a PowerShell használatával
description: Key Vault beállítása virtuális géppel a PowerShell használatával.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557488"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Key Vault beállítása virtuális gépekhez Azure PowerShell használatával

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Azure Resource Manager veremben a titkok/tanúsítványok a Key Vault erőforrás-szolgáltatója által biztosított erőforrásokként vannak modellezve. További információ a Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Ahhoz, hogy a Key Vault Azure Resource Manager virtuális gépekkel is használhatók legyenek, a Key Vault **EnabledForDeployment** tulajdonságát True értékre kell állítani. Ezt megteheti különböző ügyfeleken.
> 2. A Key Vault a virtuális géppel megegyező előfizetésben és helyen kell létrehozni.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Key Vault beállítása a PowerShell használatával
Ha a PowerShell használatával szeretne kulcstartót létrehozni, olvassa el [a titkos kód beállítása és lekérése Azure Key Vault a PowerShell használatával](../../key-vault/secrets/quick-create-powershell.md)című témakört.

Új kulcstartók esetén ezt a PowerShell-parancsmagot használhatja:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

A meglévő kulcstartók esetében ezt a PowerShell-parancsmagot használhatja:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>Key Vault beállítása a CLI használatával
Key Vault parancssori felületen (CLI) való létrehozásával kapcsolatban lásd: [Key Vault kezelése a CLI használatával](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

A CLI esetében létre kell hoznia a kulcstartót, mielőtt hozzárendeli a központi telepítési szabályzatot. Ehhez futtassa az alábbi parancsot:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Ezután az Key Vault a sablonok központi telepítésének engedélyezéséhez futtassa a következő parancsot:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Sablonok használata a Key Vault beállításához
Sablon használatakor a `enabledForDeployment` tulajdonságot a Key Vault erőforráshoz kell beállítania `true` .

```config
{
  "type": "Microsoft.KeyVault/vaults",
  "name": "ContosoKeyVault",
  "apiVersion": "2015-06-01",
  "location": "<location-of-key-vault>",
  "properties": {
    "enabledForDeployment": "true",
    ....
    ....
  }
}
```

A Key Vault sablonok használatával történő létrehozásakor konfigurálható egyéb beállításokért lásd: [kulcstartó létrehozása](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
