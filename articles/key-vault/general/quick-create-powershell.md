---
title: Rövid útmutató – Azure Key Vault létrehozása Azure PowerShell
description: Rövid útmutató, amely bemutatja, hogyan hozhat létre Azure Key Vault Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: cc6d9ca2621a56242d7472a088e55651f5502c9c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814801"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Rövid útmutató: Kulcstartó létrehozása a PowerShell használatával

Azure Key Vault egy felhőszolgáltatás, amely biztonságos tárolót [](../secrets/index.yml)biztosít a [kulcsok,](../keys/index.yml)titkos kulcsok és tanúsítványok [számára.](../certificates/index.yml) További információ a Key Vault: [About Azure Key Vault](overview.md); A kulcstartóban tárolható adatokkal kapcsolatos további információkért lásd: [About keys, secrets, and certificates](about-keys-secrets-certificates.md)(Tudnivalók a kulcsokról, titkos kulcsokról és tanúsítványokról).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ebben a rövid útmutatóban egy kulcstartót hoz létre a következő [Azure PowerShell:](/powershell/azure/). Ha a PowerShell helyi telepítését és használatát választja, az oktatóanyaghoz Azure PowerShell modul 1.0.0-s vagy újabb verziójára lesz szükség. A `$PSVersionTable.PSVersion` verzió megkereshez írja be a következőt: . Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Login-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault a Azure PowerShell. Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](overview.md)
- Lásd a Azure PowerShell Key Vault [parancsmagok referenciáit](/powershell/module/az.keyvault/)
- Tekintse át [a Azure Key Vault biztonsági áttekintését](security-features.md)

