---
title: A Key Vault beállítása
description: Key Vault beállítása virtuális géppel való használatra.
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: d5e4bb1d3f62685c90e95eebf5f8f07720a23b82
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086993"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Key Vault beállítása virtuális gépekhez Azure Resource Manager

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
