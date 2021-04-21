---
title: Felhasználódelegálásos SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre felhasználódelegálásos SAS-Azure Active Directory hitelesítő adatokkal az Azure CLI használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ec434b9b6da3b3b80a3afddbb432ddeece2b389
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788546"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli"></a>Felhasználódelegálás SAS létrehozása tárolóhoz vagy blobhoz az Azure CLI használatával

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatja Azure Active Directory (Azure AD) hitelesítő adatait egy felhasználódelegálásos SAS létrehozásához egy tárolóhoz vagy blobhoz az Azure CLI használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Telepítse az Azure CLI legújabb verzióját

Ahhoz, hogy az Azure CLI használatával sas-eket biztosítsunk az Azure AD hitelesítő adataival, először győződjön meg arról, hogy az Azure CLI legújabb verziója van telepítve. További információ az Azure CLI telepítéséről: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

Ha felhasználódelegálásos SAS-t hoz létre az Azure CLI használatával, győződjön meg arról, hogy telepítette a 2.0.78-as vagy újabb verziót. A telepített verzió ellenőrzéshez használja az `az --version` parancsot.

## <a name="sign-in-with-azure-ad-credentials"></a>Bejelentkezés Azure AD-beli hitelesítő adatokkal

Jelentkezzen be az Azure CLI-be az Azure AD hitelesítő adataival. További információkért lásd: [Bejelentkezés az Azure CLI-vel](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-azure-rbac"></a>Engedélyek hozzárendelése az Azure RBAC használatával

Ha felhasználódelegációs SAS-t hoz létre az Azure PowerShell-ból, az Azure CLI-be való bejelentkezéshez használt Azure AD-fiókhoz hozzá kell rendelni egy szerepkört, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. Ez az engedély lehetővé teszi, hogy az Azure AD-fiók lekérte *a felhasználódelegálás kulcsát.* A felhasználódelegálás SAS-ének aláírásra a felhasználódelegálás kulcsa használható. A **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet biztosító szerepkört a tárfiók, az erőforráscsoport vagy az előfizetés szintjén kell hozzárendelni.

Ha nem rendelkezik megfelelő engedélyekkel ahhoz, hogy Azure-szerepköröket rendeljen egy Azure AD-rendszerbiztonsági taghoz, előfordulhat, hogy meg kell kérnie a fióktulajdonost vagy a rendszergazdát, hogy rendelje hozzá a szükséges engedélyeket.

Az alábbi példa hozzárendeli a **Storage-blobadatok** közreműködője szerepkört, amely tartalmazza a **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A szerepkör hatóköre a tárfiók szintjén van megszava.

Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőértékeket a saját értékeire:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

A **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** művelettel kapcsolatos további információkért lásd: [Beépített Azure-szerepkörök.](../../role-based-access-control/built-in-roles.md)

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>SAS biztonságossá tere Azure AD-beli hitelesítő adatokkal

Amikor felhasználódelegálás sas-t hoz létre az Azure CLI-ben, a rendszer implicit módon létrehozza az SAS aláíráshoz használt felhasználódelegálási kulcsot. A rendszer az SAS-hez megadott kezdési és lejárati időt is használja a felhasználódelegálás kulcsának kezdési és lejárati idejeként.

Mivel a felhasználódelegálás kulcsának érvényességének maximális időköze 7 nap a kezdési dátumtól számított 7 nap, olyan lejárati időt kell megadnia az SAS-hez, amely a kezdési időponttól számított 7 napon belül van. Az SAS érvénytelen a felhasználódelegálás kulcsának lejárata után, így a 7 napnál hosszabb lejárati idővel még csak 7 napig lesz érvényes.

Felhasználódelegálás SAS létrehozásakor a és a `--auth-mode login` `--as-user parameters` szükséges. Adja *meg a* bejelentkezést a paraméterhez, hogy az Azure Storage-ba lekért kérelmeket az Azure AD hitelesítő `--auth-mode` adataival engedélyezze. Adja meg a paramétert annak jelzésére, hogy a visszaadott `--as-user` SAS-nek felhasználódelegálásos SAS-nek kell lennie.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Felhasználódelegálás SAS létrehozása tárolóhoz

Ha felhasználódelegálás SAS-t hoz létre egy tárolóhoz az Azure CLI használatával, hívja meg [az az storage container generate-sas](/cli/azure/storage/container#az_storage_container_generate_sas) parancsot.

A tárolók felhasználói delegálás SAS-hez támogatott engedélyei a következők: Hozzáadás, Létrehozás, Törlés, Lista, Olvasás és Írás. Az engedélyek egyesével vagy kombinálhatók. További információ ezekről az engedélyekről: [FelhasználódelegálásRA vonatkozó SAS létrehozása.](/rest/api/storageservices/create-user-delegation-sas)

Az alábbi példa egy felhasználódelegálás SAS-jogkivonatát adja vissza egy tárolóhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

A visszaadott felhasználódelegálás SAS-jogkivonata a következőre hasonlít:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Felhasználódelegálás SAS létrehozása blobhoz

Ha az Azure CLI használatával felhasználói delegálás [SAS-t](/cli/azure/storage/blob#az_storage_blob_generate_sas) hoz létre egy blobhoz, hívja meg az az storage blob generate-sas parancsot.

Blobon a felhasználódelegálás SAS-hez támogatott engedélyei a következők: Hozzáadás, Létrehozás, Törlés, Olvasás és Írás. Az engedélyek egyesével vagy kombinálhatók. További információ ezekről az engedélyekről: [Felhasználódelegálásos SAS létrehozása.](/rest/api/storageservices/create-user-delegation-sas)

Az alábbi szintaxis egy felhasználódelegálásos SAS-t ad vissza egy blobhoz. A példa megadja a paramétert, amely a hozzáfűzett SAS-jogkivonattal együtt adja vissza a `--full-uri` blob URI-ját. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user \
    --full-uri
```

A visszaadott felhasználódelegálás SAS URI-ja a következőre hasonlít:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> A felhasználódelegált SAS nem támogatja az engedélyek tárolt hozzáférési szabályzattal történő definiálását.

## <a name="revoke-a-user-delegation-sas"></a>Felhasználódelegálás SAS visszavonása

Ha vissza kell vonnia egy felhasználódelegálás SAS-t az Azure CLI-ről, hívja meg [az az storage account revoke-delegation-keys](/cli/azure/storage/account#az_storage_account_revoke_delegation_keys) parancsot. Ez a parancs visszavonja a megadott tárfiókhoz társított összes felhasználódelegálási kulcsot. A kulcsokhoz társított közös hozzáférésű jogosultságok érvénytelenné válik.

Ne felejtse el lecserélni a szögletes zárójelben lévő helyőrzőértékeket a saját értékeire:

```azurecli-interactive
az storage account revoke-delegation-keys \
    --name <storage-account> \
    --resource-group <resource-group>
```

> [!IMPORTANT]
> Az Azure Storage a felhasználódelegálás kulcsát és az Azure-beli szerepkör-hozzárendeléseket is gyorsítótárazza, így késés lehet a visszavonási folyamat kezdeményezése és egy meglévő felhasználódelegált SAS érvénytelenné válása között.

## <a name="next-steps"></a>Következő lépések

- [Felhasználódelegálás SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Felhasználódelegálás kulcsának lekért művelete](/rest/api/storageservices/get-user-delegation-key)
