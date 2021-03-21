---
title: Gyors útmutató – Azure Key Vault tanúsítványok & nézetének beállítása az Azure CLI-vel
description: Gyors útmutató, amely bemutatja, hogyan lehet tanúsítványokat beállítani és lekérni Azure Key Vault az Azure CLI használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 56e51d74358bcda96a6859a481e53710a6f78ec3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072421"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-cli"></a>Gyors útmutató: tanúsítvány beállítása és lekérése Azure Key Vault az Azure CLI használatával

Ebben a rövid útmutatóban egy kulcstartót hoz létre Azure Key Vault az Azure CLI-vel. Az Azure Key Vault egy felhőszolgáltatás, amely biztonságos titkoskulcs-tárolóként működik. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. További információ a Key Vaultről: [Áttekintés](../general/overview.md). Az Azure CLI az Azure-erőforrások létrehozására és kezelésére szolgál, parancsok és szkriptek alkalmazásával. Ha ezt elvégezte, egy tanúsítványt fog tárolni.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Ehhez a rövid útmutatóhoz az Azure CLI 2.0.4 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>Tanúsítvány hozzáadása a Key Vaulthoz

Ha tanúsítványt szeretne hozzáadni a tárolóhoz, mindössze néhány további lépést kell elvégeznie. Ezt a tanúsítványt egy alkalmazás használhatja. 

Írja be az alábbi parancsokat, és hozzon létre egy önaláírt tanúsítványt a **ExampleCertificate** nevű alapértelmezett házirenddel:

```azurecli
az keyvault certificate create --vault-name "<your-unique-keyvault-name>" -n ExampleCertificate -p "$(az keyvault certificate get-default-policy)"
```

Most már hivatkozhat erre a tanúsítványra, amelyet a Azure Key Vaulthoz adott hozzá az URI használatával. Az aktuális verzió beszerzéséhez használja a **"https://<a-Unique-kulcstartó-name>. Vault.Azure.net/Certificates/ExampleCertificate"** lehetőséget. 

A korábban tárolt tanúsítvány megtekintése:

```azurecli

az keyvault certificate show --name "ExampleCertificate" --vault-name "<your-unique-keyvault-name>"
```

Most létrehozott egy Key Vault, tárolt egy tanúsítványt, és beolvasta azt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és tárolt benne egy tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse meg az Azure CLI-re vonatkozó hivatkozást az kulcstartó [parancsainál](/cli/azure/keyvault)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
