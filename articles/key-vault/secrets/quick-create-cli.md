---
title: Rövid útmutató – Titkos adat beállítása és lekérése a Azure Key Vault
description: Rövid útmutató, amely bemutatja a titkos kulcsok beállítását és lekérését az Azure Key Vaultból az Azure CLI használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc5fa0eeabc437f16ca64c34b4bb72e62c0c3b21
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815017"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból az Azure CLI használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre a Azure Key Vault Azure CLI használatával. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információt a Key Vault áttekintésében [talál.](../general/overview.md) Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Miután végzett ezzel, titkos kulcsot fog tárolni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Titkos kód a tárolóhoz való hozzáadásához csak néhány további lépést kell végrehajtania. Ezt a jelszót egy alkalmazás használhatja. A jelszó neve **ExamplePassword** lesz, és a **hVFkk965BuUv** értékét fogja tárolni.

Az alábbi Azure CLI [az keyvault secret set](/cli/azure/keyvault/secret#az_keyvault_secret_set) paranccsal hozzon létre egy **ExamplePassword** nevű titkos kulcsot az Key Vault-ben, amely a **hVFkk965BuUv értéket fogja tárolni:**

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

## <a name="retrieve-a-secret-from-key-vault"></a>Titkos adat lekérése a Key Vault

Az Azure Key Vaulthoz hozzáadott jelszóra ezután az URI használatával hivatkozhat. A **"https://<-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword"** használatával lekért aktuális verzió.

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Most létrehozott egy Key Vaultot, tárolt egy titkos kulcsot, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault és tárolt benne egy titkos adatokat. Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Megtudhatja, hogyan [tárolható többsoros titkos kulcsok Key Vault](multiline-secrets.md)
- Tekintse meg az [Azure CLI az keyvault parancsának referenciáját](/cli/azure/keyvault)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)
