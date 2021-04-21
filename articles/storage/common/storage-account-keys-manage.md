---
title: A fiók hozzáférési kulcsainak kezelése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan lehet megtekinteni, kezelni és forgatni a tárfiók hozzáférési kulcsait.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791210"
---
# <a name="manage-storage-account-access-keys"></a>Tárfiók hozzáférési kulcsának kezelése

Amikor létrehoz egy tárfiókot, az Azure létrehoz két 512 bites tárfiók-hozzáférési kulcsot. Ezekkel a kulcsokkal engedélyezheti a tárfiókban található adatokhoz való hozzáférést megosztott kulcsos hitelesítéssel.

A Microsoft azt javasolja, Azure Key Vault használja a kulcsokat a hozzáférési kulcsok kezeléséhez, és rendszeresen váltsa át és újragenerálja a kulcsokat. A Azure Key Vault megkönnyíti a kulcsok az alkalmazások megszakítása nélküli váltogatottítását. A kulcsokat manuálisan is elforgathatja.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Fiók hozzáférési kulcsának megtekintése

A fiók hozzáférési kulcsait megtekintheti és másolhatja a Azure Portal, a PowerShell vagy az Azure CLI használatával. A Azure Portal egy kapcsolati sztringet is biztosít a tárfiókhoz, amelyről másolhat.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A tárfiók hozzáférési kulcsának vagy kapcsolati sztringének megtekintése és másolása a Azure Portal:

1. Lépjen a tárfiókra a [Azure Portal.](https://portal.azure.com)
1. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
1. Keresse meg **a Key (Kulcs)** értéket a **key1 (1. kulcs)** alatt, majd kattintson a Copy (Másolás) gombra a fiókkulcs másoláshoz. 
1. A teljes kapcsolati sztringet is átmásolhatja. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Képernyőkép a hozzáférési kulcsok megtekintéséről a Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A fiók hozzáférési kulcsait a PowerShell használatával a [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) paranccsal lehet lekérni.

Az alábbi példa az első kulcsot olvassa be. A második kulcs lekérése helyett használja `Value[1]` a következőt: `Value[0]` . Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A fiókelérési kulcsok Azure CLI-beli listához hívja az [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

A két kulcs bármelyikét használhatja az Azure Storage eléréséhez, de általában jó gyakorlat az első kulcs használata, és a második kulcs használata a kulcsok rotozása során.

Egy fiók hozzáférési kulcsának megtekintéséhez vagy olvasásához a felhasználónak szolgáltatás-rendszergazdának kell lennie, vagy olyan Azure-szerepkört kell hozzárendelnie, amely tartalmazza a **Microsoft.Storage/storageAccounts/listkeys/action műveletet.** Néhány beépített Azure-szerepkör, amely tartalmazza ezt a **műveletet,** a **Tulajdonos,** a Közreműködő és a **Tárfiókkulcs-kezelői szolgáltatás szerepkör.** További információ a szolgáltatás-rendszergazdai szerepkörről: Klasszikus előfizetés-rendszergazdai [szerepkörök, Azure-szerepkörök és Azure AD-szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Az Azure Storage beépített szerepköreiről az  Azure RBAC beépített Azure-szerepkörei című cikk Tárolás szakaszában található [részletes információ.](../../role-based-access-control/built-in-roles.md#storage)

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>A Azure Key Vault a hozzáférési kulcsok kezeléséhez

A Microsoft a Azure Key Vault használatát javasolja a hozzáférési kulcsok kezeléséhez és váltogatása érdekében. Az alkalmazás biztonságosan hozzáférhet a kulcsokhoz a Key Vault, így elkerülheti a tárolást az alkalmazáskóddal. A kulcskezeléshez használt Key Vault a következő cikkekben talál további információt:

- [Tárfiókkulcsok kezelése a Azure Key Vault És a PowerShell használatával](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Tárfiókkulcsok kezelése a Azure Key Vault és az Azure CLI használatával](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Hozzáférési kulcsok manuális elforgatása

A Microsoft azt javasolja, hogy a tárfiók biztonságának biztosítása érdekében rendszeresen váltsa át a hozzáférési kulcsokat. Ha lehetséges, a Azure Key Vault használhatja a hozzáférési kulcsok kezeléséhez. Ha nem használ Key Vault, manuálisan kell elforgatnia a kulcsokat.

Két hozzáférési kulcs van hozzárendelve, hogy a kulcsokat el tudjaforgatni. A két kulcs biztosítja, hogy az alkalmazás a folyamat során fenntartsa a hozzáférést az Azure Storage-hoz.

> [!WARNING]
> A hozzáférési kulcsok újragenerálása hatással lehet a tárfiókkulcstól függő alkalmazásokra vagy Azure-szolgáltatásokra. A tárfiók eléréséhez fiókkulcsot használó ügyfeleket frissíteni kell az új kulcs használatára, beleértve a médiaszolgáltatásokat, a felhőt, az asztali és mobilalkalmazásokat, valamint az Azure Storage grafikus felhasználói felületi alkalmazásait, például a [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

# <a name="portal"></a>[Portál](#tab/azure-portal)

A tárfiók hozzáférési kulcsait a következő Azure Portal:

1. Frissítse az alkalmazás kódban a kapcsolati sztringeket úgy, hogy a tárfiók másodlagos hozzáférési kulcsát hivatkozza.
1. Lépjen a tárfiókra a [Azure Portal.](https://portal.azure.com)
1. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet.
1. A tárfiók elsődleges hozzáférési kulcsának újrageneráláshoz válassza az elsődleges hozzáférési kulcs melletti Újragenerálás gombot. 
1. Frissítse a kapcsolati sztringeket a kódban, hogy az új elsődleges tárhozzáférési kulcsra hivatkozzanak.
1. Hasonló módon állítsa elő újra a másodlagos hozzáférési kulcsot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A tárfiók hozzáférési kulcsának váltogatása a PowerShell használatával:

1. Frissítse az alkalmazás kódban a kapcsolati sztringeket úgy, hogy a tárfiók másodlagos hozzáférési kulcsát hivatkozza.
1. Hívja meg [a New-AzStorageAccountKey parancsot](/powershell/module/az.storage/new-azstorageaccountkey) az elsődleges hozzáférési kulcs újrageneráltatására az alábbi példában látható módon:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Frissítse a kapcsolati sztringeket a kódban, hogy az új elsődleges tárhozzáférési kulcsra hivatkozzanak.
1. Hasonló módon állítsa elő újra a másodlagos hozzáférési kulcsot. A másodlagos kulcs újragenerálhoz használja `key2` a kulcsnevet a `key1` helyett.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A tárfiók hozzáférési kulcsának váltogatása az Azure CLI használatával:

1. Frissítse az alkalmazás kódban a kapcsolati sztringeket úgy, hogy a tárfiók másodlagos hozzáférési kulcsát hivatkozza.
1. Hívja meg [az az storage account keys renew parancsot](/cli/azure/storage/account/keys#az_storage_account_keys_renew) az elsődleges hozzáférési kulcs újrageneráláshoz, az alábbi példában látható módon:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Frissítse a kapcsolati sztringeket a kódban, hogy az új elsődleges tárhozzáférési kulcsra hivatkozzanak.
1. Hasonló módon állítsa elő újra a másodlagos hozzáférési kulcsot. A másodlagos kulcs újragenerálhoz használja `key2` a kulcsnevet a `key1` helyett.

---

> [!NOTE]
> A Microsoft azt javasolja, hogy egyszerre csak az egyik kulcsot használja az összes alkalmazásban. Ha egyes helyeken az 1. kulcsot, másokban pedig a 2. kulcsot használja, nem lesz képes a kulcsok váltogatása anélkül, hogy az alkalmazás elveszítené a hozzáférést.

A fiók hozzáférési kulcsának váltogatása érdekében a felhasználónak szolgáltatás-rendszergazdának kell lennie, vagy olyan Azure-szerepkört kell hozzárendelnie, amely tartalmazza a **Microsoft.Storage/storageAccounts/regeneratekey/action szolgáltatásokat.** Néhány beépített Azure-szerepkör, amely tartalmazza ezt a **műveletet,** a **Tulajdonos,** a Közreműködő és a **Tárfiókkulcs-kezelői szolgáltatás szerepkör.** További információ a szolgáltatás-rendszergazdai szerepkörről: [Klasszikus előfizetés-rendszergazdai szerepkörök, Azure-szerepkörök és Azure AD-szerepkörök.](../../role-based-access-control/rbac-and-directory-admin-roles.md) Az Azure Storage beépített Azure-szerepköreiről az  Azure beépített szerepkörei az [Azure RBAC-hez](../../role-based-access-control/built-in-roles.md#storage)című cikk Storage szakaszában található részletes információ.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Storage-fiók áttekintése](storage-account-overview.md)
- [Tárfiók létrehozása](storage-account-create.md)
