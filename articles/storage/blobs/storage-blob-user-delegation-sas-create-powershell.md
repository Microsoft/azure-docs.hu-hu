---
title: Felhasználói delegálási SAS létrehozása egy tárolóhoz vagy blobhoz a PowerShell használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan hozhat létre felhasználói delegálási SAS-t Azure Active Directory hitelesítő adatokkal a PowerShell használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714527"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Felhasználói delegálási SAS létrehozása tárolóhoz vagy blobhoz a PowerShell használatával

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Ez a cikk bemutatja, hogyan használhatók a Azure Active Directory-(Azure AD-) hitelesítő adatok egy felhasználói delegálási SAS létrehozásához egy tárolóhoz vagy blobhoz Azure PowerShell használatával.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>A PowerShell-modul telepítése

Felhasználói delegálási SAS PowerShell-lel történő létrehozásához telepítse az az. Storage modul 1.10.0 vagy újabb verzióját. Kövesse az alábbi lépéseket a modul legújabb verziójának telepítéséhez:

1. Távolítsa el a Azure PowerShell összes korábbi telepítését:

    - Távolítsa el a Azure PowerShell korábbi telepítését a Windows rendszerből a **Beállítások** területen található **alkalmazások & szolgáltatások** beállítással.
    - Távolítsa el az összes **Azure** -modult `%Program Files%\WindowsPowerShell\Modules` .

1. Győződjön meg arról, hogy a PowerShellGet legújabb verziója van telepítve. Nyisson meg egy Windows PowerShell-ablakot, és futtassa a következő parancsot a legújabb verzió telepítéséhez:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. A PowerShellGet telepítése után zárjuk be és nyissa meg újra a PowerShell ablakot.

1. Telepítse a Azure PowerShell legújabb verzióját:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Győződjön meg arról, hogy telepítette a Azure PowerShell 3.2.0-es vagy újabb verzióját. Futtassa az alábbi parancsot az Azure Storage PowerShell-modul legújabb verziójának telepítéséhez:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Zárjuk be és nyissa meg újra a PowerShell ablakot.

A következő parancs futtatásával ellenőrizhető, hogy az az. Storage modul melyik verzióját telepíti:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

További információ a Azure PowerShell telepítéséről: [Azure PowerShell telepítése a PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Bejelentkezés Azure PowerShell az Azure AD-vel

Hívja meg a AzAccount parancsot, és jelentkezzen be az Azure AD [-](/powershell/module/az.accounts/connect-azaccount) fiókjával:

```powershell
Connect-AzAccount
```

A PowerShell-lel való bejelentkezéssel kapcsolatos további információkért lásd: [bejelentkezés Azure PowerShellsal](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Engedélyek kiosztása az Azure RBAC

Ha Azure PowerShellból szeretne felhasználói delegálási SAS-t létrehozni, a PowerShellbe való bejelentkezéshez használt Azure AD-fióknak olyan szerepkört kell hozzárendelni, amely tartalmazza a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. Ez az engedély lehetővé teszi, hogy az Azure AD-fiók a *felhasználói delegálási kulcsot* kérje. A felhasználói delegálási kulcs a felhasználói delegálási SAS aláírására szolgál. A **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet biztosító szerepkört a Storage-fiók, az erőforráscsoport vagy az előfizetés szintjén kell hozzárendelni. További információ az Azure RBAC-engedélyekről felhasználói delegálási SAS létrehozásához: **engedélyek kiosztása az Azure RBAC** című rész a [felhasználói delegálási sas létrehozása](/rest/api/storageservices/create-user-delegation-sas)című részben.

Ha nem rendelkezik megfelelő engedélyekkel az Azure-szerepkörök Azure AD-rendszerbiztonsági tag számára való hozzárendeléséhez, akkor előfordulhat, hogy a fiók tulajdonosának vagy a rendszergazdának kell megkérnie a szükséges engedélyek hozzárendelését.

A következő példa a **Storage blob-adatközreműködői** szerepkört rendeli hozzá, amely magában foglalja a **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet. A szerepkör hatóköre a Storage-fiók szintjén van.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

A **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** műveletet tartalmazó beépített szerepkörökkel kapcsolatos további információkért lásd: [Azure beépített szerepkörök](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Az Azure AD hitelesítő adataival biztonságossá teheti az SAS-t

Ha Azure PowerShell használatával hoz létre felhasználói delegálási SAS-t, a rendszer implicit módon létrehozza az SAS aláírásához használt felhasználói delegálási kulcsot. A rendszer az SAS számára megadott kezdési időt és lejárati időt is használja a felhasználói delegálási kulcs kezdési és lejárati idejeként. 

Mivel a felhasználói delegálási kulcs érvényességi időtartama a kezdő dátumtól számított 7 nap, a kezdési időponttól számított 7 napon belül meg kell adnia a lejárati időt. Az SAS érvénytelen a felhasználói delegálási kulcs lejárata után, így a 7 napnál hosszabb lejárati idővel rendelkező SAS-t a rendszer továbbra is csak 7 napig érvényes.

Ha Azure PowerShell használatával szeretne létrehozni egy felhasználói delegálási SAS-t egy tárolóhoz vagy blobhoz, először hozzon létre egy új Azure Storage környezeti objektumot, és határozza meg a `-UseConnectedAccount` paramétert. A `-UseConnectedAccount` paraméter azt adja meg, hogy a parancs létrehozza a környezeti objektumot abban az Azure ad-fiókban, amellyel bejelentkezett.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Felhasználói delegálási SAS létrehozása tárolóhoz

Ha egy tárolóhoz felhasználói delegálási SAS-tokent szeretne visszaadni, hívja meg a [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) parancsot, amely a korábban létrehozott Azure Storage környezeti objektumba kerül.

A következő példa egy felhasználó delegálási SAS-jogkivonatát adja vissza egy tárolóhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

A felhasználói delegálási SAS-jogkivonat a következőhöz hasonló lesz:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Felhasználói delegálási SAS létrehozása blobhoz

Ha egy blobhoz felhasználói delegálási SAS-tokent szeretne visszaadni, hívja meg a [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) parancsot, amely a korábban létrehozott Azure Storage környezeti objektumba kerül.

A következő szintaxis a blobhoz tartozó felhasználói delegálási SAS-t adja vissza. A példa a `-FullUri` paramétert adja meg, amely visszaadja a blob URI azonosítót a HOZZÁFŰZÖTT sas-jogkivonattal. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

A felhasználói delegálás SAS URI-ja a következőhöz hasonló lesz:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> A felhasználói delegálási SAS nem támogatja az engedélyek megadását egy tárolt hozzáférési házirenddel.

## <a name="revoke-a-user-delegation-sas"></a>Felhasználói delegálási SAS visszavonása

Ha Azure PowerShell szeretne visszavonni egy felhasználói delegálási SAS-t, hívja meg a **Visszavonás-AzStorageAccountUserDelegationKeys** parancsot. Ez a parancs visszavonja a megadott Storage-fiókhoz társított összes felhasználói delegálási kulcsot. A kulcsokhoz társított megosztott hozzáférési aláírások érvénytelenítve lettek.

Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> A felhasználói delegálási kulcsot és az Azure-szerepkörök hozzárendeléseit is gyorsítótárazza az Azure Storage, így a visszavonás folyamata és a meglévő felhasználói delegálási SAS érvénytelenné válik.

## <a name="next-steps"></a>Következő lépések

- [Felhasználói delegálási SAS létrehozása (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Felhasználói delegálási kulcs műveletének beolvasása](/rest/api/storageservices/get-user-delegation-key)
