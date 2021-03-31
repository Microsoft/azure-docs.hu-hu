---
title: A fiók hozzáférési kulcsainak kezelése
titleSuffix: Azure Storage
description: Megtudhatja, hogyan tekintheti meg, kezelheti és forgathatja el a Storage-fiók hozzáférési kulcsait.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e5ea94fea00771b64634d6c28a7879fabb195f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89069659"
---
# <a name="manage-storage-account-access-keys"></a>A Storage-fiók hozzáférési kulcsainak kezelése

A Storage-fiók létrehozásakor az Azure 2 512 bites Storage-fiókhoz tartozó hozzáférési kulcsokat hoz létre. Ezek a kulcsok a Storage-fiókban lévő adathozzáférés engedélyezésére használhatók a megosztott kulcsos hitelesítésen keresztül.

A Microsoft azt javasolja, hogy a Azure Key Vault segítségével kezelje a hozzáférési kulcsokat, és hogy rendszeresen elforgatja és újragenerálja a kulcsokat. A Azure Key Vault használata megkönnyíti a kulcsok elforgatását az alkalmazásokkal való megszakítás nélkül. Manuálisan is elforgathatja a kulcsokat.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Fiók hozzáférési kulcsainak megtekintése

A fiókhoz való hozzáférési kulcsokat a Azure Portal, a PowerShell vagy az Azure CLI használatával tekintheti meg és másolhatja. A Azure Portal is biztosít a Storage-fiókhoz a másolható kapcsolatok karakterláncát.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Storage-fiók hozzáférési kulcsainak vagy kapcsolati karakterláncának megtekintése és másolása a Azure Portalből:

1. Navigáljon a Storage-fiókjához a [Azure Portal](https://portal.azure.com).
1. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet. Megjelennek a fiókhoz tartozó hozzáférési kulcsok, valamint az egyes kulcsokhoz tartozó kapcsolati sztringek.
1. Keresse meg a **kulcs** értékét a **key1** alatt, és kattintson a **Másolás** gombra a fiók kulcsának másolásához.
1. Másik lehetőségként átmásolhatja a teljes kapcsolatok sztringjét. Keresse meg a **Kapcsolati sztring** értéket a **key1** területen, és kattintson a **Másolás** gombra a kapcsolati sztring másolásához.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="A Azure Portal elérési kulcsainak megtekintését bemutató képernyőkép":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A fiók hozzáférési kulcsainak PowerShell-lel való lekéréséhez hívja meg a [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) parancsot.

A következő példa az első kulcsot kéri le. A második kulcs lekéréséhez használja a helyett a következőt: `Value[1]` `Value[0]` . Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A fiók hozzáférési kulcsainak az Azure CLI-vel való listázásához hívja meg az az [Storage Account Keys List](/cli/azure/storage/account/keys#az-storage-account-keys-list) parancsot az alábbi példában látható módon. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

A két kulcs közül bármelyiket használhatja az Azure Storage eléréséhez, de általánosságban ajánlott az első kulcs használata, és a második kulcs használatának fenntartása a kulcsok elforgatásakor.

A fiók hozzáférési kulcsainak megtekintéséhez vagy olvasásához a felhasználónak vagy szolgáltatás-rendszergazdának kell lennie, vagy hozzá kell rendelnie egy olyan Azure-szerepkört, amely tartalmazza a **Microsoft. Storage/storageAccounts/listkeys műveletének beolvasása/műveletet**. Néhány Azure-beli beépített szerepkör, amely tartalmazza ezt a műveletet, a **tulajdonos**, a **közreműködő** és a **Storage-fiók kulcs-kezelő szolgáltatásának szerepköre** . A szolgáltatás-rendszergazdai szerepkörrel kapcsolatos további információkért lásd a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és az Azure ad-szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md). Az Azure Storage beépített szerepköreivel kapcsolatos részletes információkért tekintse meg az Azure [RBAC beépített Azure-beli beépített szerepköreinek](../../role-based-access-control/built-in-roles.md#storage) **tárolási** szakaszát.

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>A Azure Key Vault használata a hozzáférési kulcsok kezeléséhez

A Microsoft azt javasolja, hogy a Azure Key Vault segítségével kezelje és forgassa el a hozzáférési kulcsokat. Az alkalmazás képes biztonságosan hozzáférni a kulcsaihoz Key Vaultban, így elkerülhető az alkalmazás kódjának tárolása. A kulcskezelő Key Vault használatáról a következő cikkekben talál további információt:

- [A Storage-fiók kulcsainak kezelése a Azure Key Vault és a PowerShell használatával](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [A Storage-fiók kulcsainak kezelése a Azure Key Vault és az Azure CLI használatával](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Hozzáférési kulcsok manuális elforgatása

A Microsoft azt javasolja, hogy rendszeres időközönként elforgatni a hozzáférési kulcsokat a Storage-fiók biztonságának megőrzése érdekében. Ha lehetséges, használja a Azure Key Vault a hozzáférési kulcsok kezeléséhez. Ha nem Key Vault használ, manuálisan kell elforgatnia a kulcsokat.

Két hozzáférési kulcs van hozzárendelve, hogy el lehessen forgatni a kulcsokat. A két kulcs biztosítja, hogy az alkalmazás a folyamat során fenntartsa az Azure Storage-hoz való hozzáférést.

> [!WARNING]
> A hozzáférési kulcsok újragenerálása hatással lehet bármely olyan alkalmazásra vagy Azure-szolgáltatásra, amely a Storage-fiók kulcsával függ. Minden olyan ügyfelet, amely a fiók kulcsát használja a Storage-fiók eléréséhez, frissíteni kell az új kulcs használatára, beleértve a Media Servicest, a felhőt, az asztali és a mobil alkalmazásokat, valamint az Azure Storage-hoz készült grafikus felhasználói felületi alkalmazásokat, például a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Portál](#tab/azure-portal)

A Storage-fiók hozzáférési kulcsainak elforgatásához a Azure Portalban:

1. Frissítse az alkalmazás kódjában található kapcsolati karakterláncokat a Storage-fiók másodlagos elérési kulcsára való hivatkozáshoz.
1. Navigáljon a Storage-fiókjához a [Azure Portal](https://portal.azure.com).
1. A **Beállítások** területen válassza a **Hozzáférési kulcsok** elemet.
1. A Storage-fiók elsődleges elérési kulcsának újralétrehozásához válassza az **újragenerált** gombot az elsődleges elérési kulcs mellett.
1. Frissítse a kapcsolati sztringeket a kódban, hogy az új elsődleges tárhozzáférési kulcsra hivatkozzanak.
1. Hasonló módon állítsa elő újra a másodlagos hozzáférési kulcsot.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A Storage-fiók hozzáférési kulcsainak elforgatása a PowerShell-lel:

1. Frissítse az alkalmazás kódjában található kapcsolati karakterláncokat a Storage-fiók másodlagos elérési kulcsára való hivatkozáshoz.
1. Hívja meg a [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) parancsot az elsődleges hozzáférési kulcs újralétrehozásához, ahogy az az alábbi példában is látható:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Frissítse a kapcsolati sztringeket a kódban, hogy az új elsődleges tárhozzáférési kulcsra hivatkozzanak.
1. Hasonló módon állítsa elő újra a másodlagos hozzáférési kulcsot. A másodlagos kulcs újbóli létrehozásához használja `key2` a nevet a helyett `key1` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A Storage-fiók hozzáférési kulcsainak elforgatása az Azure CLI-vel:

1. Frissítse az alkalmazás kódjában található kapcsolati karakterláncokat a Storage-fiók másodlagos elérési kulcsára való hivatkozáshoz.
1. Az elsődleges elérési kulcs újragenerálása érdekében hívja meg az az [Storage Account Keys renew](/cli/azure/storage/account/keys#az-storage-account-keys-renew) parancsot az alábbi példában látható módon:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Frissítse a kapcsolati sztringeket a kódban, hogy az új elsődleges tárhozzáférési kulcsra hivatkozzanak.
1. Hasonló módon állítsa elő újra a másodlagos hozzáférési kulcsot. A másodlagos kulcs újbóli létrehozásához használja `key2` a nevet a helyett `key1` .

---

> [!NOTE]
> A Microsoft azt javasolja, hogy egyszerre csak az egyik kulcsot használja az összes alkalmazásban. Ha egyes helyeken és a 2. kulcsban az 1. kulcsot használja másokban, nem fogja tudni elforgatni a kulcsokat anélkül, hogy egy alkalmazás elveszíti a hozzáférést.

A fiók hozzáférési kulcsainak elforgatásához a felhasználónak vagy szolgáltatás-rendszergazdának kell lennie, vagy hozzá kell rendelnie egy olyan Azure-szerepkört, amely tartalmazza a **Microsoft. Storage/storageAccounts/regeneratekey/műveletet**. Néhány Azure-beli beépített szerepkör, amely tartalmazza ezt a műveletet, a **tulajdonos**, a **közreműködő** és a **Storage-fiók kulcs-kezelő szolgáltatásának szerepköre** . A szolgáltatás-rendszergazdai szerepkörrel kapcsolatos további információkért lásd a [klasszikus előfizetés-rendszergazdai szerepköröket, az Azure-szerepköröket és az Azure ad-szerepköröket](../../role-based-access-control/rbac-and-directory-admin-roles.md). Az Azure Storage-hoz készült Azure beépített szerepköreivel kapcsolatos részletes információkért tekintse meg az Azure [-beli beépített Azure RBAC-szerepkörök](../../role-based-access-control/built-in-roles.md#storage) **tárolási** szakaszát.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Storage-fiók áttekintése](storage-account-overview.md)
- [Tárfiók létrehozása](storage-account-create.md)
