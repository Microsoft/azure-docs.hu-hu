---
title: Rövid útmutató – Tanúsítványok & megtekintése Azure Key Vault Azure CLI használatával
description: Rövid útmutató, amely bemutatja, hogyan állíthat be és Azure Key Vault az Azure CLI használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: dc3abe43c2cdf4d0ee58ac9aed570a2c507b0d7f
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815269"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Rövid útmutató: Tanúsítvány beállítása és lekérése a Azure Key Vault Azure CLI használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre a Azure Key Vault Azure CLI használatával. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információt a Key Vault áttekintésében [talál.](../general/overview.md) Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Miután ezt befejezte, egy tanúsítványt fog tárolni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Tanúsítvány hozzáadása a Key Vault

Ha tanúsítványt szeretne hozzáadni a tárolóhoz, csak néhány további lépést kell tennie. Ezt a tanúsítványt egy alkalmazás is tudná használni. 

Írja be az alábbi parancsokat egy **exampleCertificate** nevű alapértelmezett szabályzattal létrehozott önaírt tanúsítvány létrehozásához:

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Most már hivatkozhat erre a tanúsítványra, amelyet a Azure Key Vault URI-ját használva. A **"https://<-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate"** használatával lekérte az aktuális verziót. 

A korábban tárolt tanúsítvány megtekintése:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

Létrehozott egy új tanúsítványt Key Vault tárolt egy tanúsítványt, és lekérte azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, folytassa az alábbi cikkekkel.

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Lásd az [Azure CLI az keyvault parancsának referenciáját](/cli/azure/keyvault)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)
