---
title: Rövid útmutató – Azure Key Vault létrehozása az Azure CLI használatával
description: Rövid útmutató, amely bemutatja, hogyan hozhat létre Azure Key Vault az Azure CLI használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 588bee2922ee44f3f89b5d252b5b4a6991d26b82
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815125"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Rövid útmutató: Kulcstartó létrehozása az Azure CLI használatával

Azure Key Vault egy felhőszolgáltatás, amely biztonságos tárolót [](../secrets/index.yml)biztosít a [kulcsok,](../keys/index.yml)titkos kulcsok és tanúsítványok [számára.](../certificates/index.yml) További információ a Key Vault: [About Azure Key Vault](overview.md); A kulcstartókban tárolható adatokkal kapcsolatos további információkért lásd: [About keys, secrets, and certificates](about-keys-secrets-certificates.md)(Tudnivalók a kulcsokról, titkos kulcsokról és tanúsítványokról).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault és törölte azt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](overview.md)
- A biztonsági [Azure Key Vault áttekintése](security-features.md)
- Tekintse meg az [Azure CLI az keyvault parancsának referenciáját](/cli/azure/keyvault)

