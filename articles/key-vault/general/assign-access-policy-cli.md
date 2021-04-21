---
title: Hozzáférés-Azure Key Vault szabályzat hozzárendelése (CLI)
description: Hogyan használhatja az Azure CLI-t egy Key Vault hozzáférési szabályzat hozzárendelésére egy rendszerbiztonsági taghoz vagy alkalmazásidentitáshoz.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 349d7453962a736c9f15bb7d31d5a44098f463a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791948"
---
# <a name="assign-a-key-vault-access-policy"></a>Hozzáférési szabályzat Key Vault hozzárendelése

A Key Vault házirend határozza meg, hogy egy adott rendszerbiztonsági tag, nevezetesen egy felhasználó, egy alkalmazás vagy [](../keys/index.yml)egy felhasználói csoport különböző műveleteket hajthat-e végre Key Vault titkos kulcsokon, [](../secrets/index.yml)kulcsokon és [tanúsítványokon.](../certificates/index.yml) A hozzáférési szabályzatokat a következő [Azure Portal,](assign-access-policy-portal.md)az Azure CLI (ez a cikk) vagy [a](assign-access-policy-powershell.md)Azure PowerShell.

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

A csoportok Azure CLI-Azure Active Directory való létrehozásával kapcsolatos további információkért lásd: [az ad group create](/cli/azure/ad/group#az_ad_group_create) és az az [ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Az Azure CLI konfigurálása és bejelentkezés

1. Az Azure CLI-parancsok helyi futtatásához telepítse az [Azure CLI-t.](/cli/azure/install-azure-cli)
 
    A parancsok közvetlenül a felhőben való futtatásához használja a [Azure Cloud Shell.](../../cloud-shell/overview.md)

1. Csak helyi CLI esetén: jelentkezzen be az Azure-ba a `az login` használatával:

    ```bash
    az login
    ```

    A `az login` parancs megnyit egy böngészőablakot, amely szükség esetén gyűjti a hitelesítő adatokat.

## <a name="acquire-the-object-id"></a>Az objektumazonosító lekérte

Határozza meg annak az alkalmazásnak, csoportnak vagy felhasználónak az objektumazonosítóját, amelyhez hozzá szeretné rendelni a hozzáférési szabályzatot:

- Alkalmazások és egyéb szolgáltatásnév: a szolgáltatásnév lekéréséhez használja [az az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) parancsot. Vizsgálja meg a parancs kimenetét annak a rendszerbiztonsági tagnak az objektumazonosítójának meghatározásához, amelyhez hozzá szeretné rendelni a hozzáférési szabályzatot.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Csoportok: használja az [az ad group list parancsot,](/cli/azure/ad/group#az_ad_group_list) és szűrje az eredményeket a `--display-name` paraméterrel:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Felhasználók: használja az [az ad user show parancsot,](/cli/azure/ad/user#az_ad_user_show) és a paraméterben továbbadva a felhasználó e-mail-címét: `--id`

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>A hozzáférési szabályzat hozzárendelése
    
Az [az keyvault set-policy paranccsal](/cli/azure/keyvault#az_keyvault_set_policy) rendelje hozzá a kívánt engedélyeket:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Cserélje `<object-id>` le a helyére a rendszerbiztonsági tag objektumazonosítóját.

Csak a , a és a típust kell tartalmaznia, amikor engedélyeket rendel az `--secret-permissions` `--key-permissions` adott `--certificate-permissions` típusokhoz. A , és megengedett értékei `<secret-permissions>` `<key-permissions>` az az `<certificate-permissions>` [keyvault set-policy dokumentációban vannak megadva.](/cli/azure/keyvault#az_keyvault_set_policy)

## <a name="next-steps"></a>Következő lépések

- [Azure Key Vault biztonság: Identitás- és hozzáférés-kezelés](security-overview.md#identity-management)
- [Kulcstartó biztonságossá tere.](security-overview.md)
- [Azure Key Vault fejlesztői útmutató](developers-guide.md)