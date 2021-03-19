---
title: Az Azure-fájlmegosztás elérésének szabályozása – helyszíni AD DS hitelesítés
description: Megtudhatja, hogyan rendelhet hozzá engedélyeket a Storage-fiókot képviselő Active Directory Domain Services identitáshoz. Ez lehetővé teszi az identitás-alapú hitelesítéssel való hozzáférés szabályozását.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91716036"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Második rész: megosztási szintű engedélyek kiosztása identitáshoz

A cikk elkezdése előtt győződjön meg arról, hogy végrehajtotta az előző cikket, és [engedélyezze AD DS hitelesítését a fiókjához](storage-files-identity-ad-ds-enable.md).

Miután engedélyezte a Active Directory Domain Services (AD DS) hitelesítést a Storage-fiókjában, konfigurálnia kell a megosztási szintű engedélyeket ahhoz, hogy hozzáférjen a fájlmegosztás eléréséhez. Az Azure file share-erőforrásokhoz való hozzáféréshez használt identitásnak a AD DS és az Azure AD-ben található hibrid identitásnak kell lennie. Tegyük fel például, hogy rendelkezik egy felhasználóval a AD DSban, user1@onprem.contoso.com és szinkronizálva van az Azure ad-vel user1@contoso.com Azure ad Connect Sync használatával. Ahhoz, hogy a felhasználó hozzáférhessen Azure Fileshoz, a megosztási szintű engedélyeket hozzá kell rendelnie a következőhöz: user1@contoso.com . Ugyanez a fogalom a csoportokra és az egyszerű szolgáltatásokra is vonatkozik. Emiatt szinkronizálnia kell a felhasználókat és a csoportokat a AD DS az Azure AD-be Azure AD Connect Sync használatával. 

A megosztási szintű engedélyeket ahhoz az Azure AD-identitáshoz kell rendelni, amely ugyanazt a felhasználót vagy csoportot jelöli a AD DS az Azure-fájlmegosztás AD DS hitelesítésének támogatásához. Az olyan identitások hitelesítése és engedélyezése, amelyek csak az Azure AD-ben léteznek, például az Azure által felügyelt identitások (rendszercsomagok-EK), AD DS hitelesítéssel nem támogatottak. Ez a cikk bemutatja, hogyan rendelhet megosztási szintű engedélyeket egy fájlmegosztás identitásához.


## <a name="share-level-permissions"></a>Megosztási szintű engedélyek

Általánosságban azt javasoljuk, hogy a magas szintű hozzáférés-kezeléshez használt megosztási szintű engedélyek használatával egy olyan Azure AD-csoportot használjon, amely a felhasználók és az identitások egy csoportját jelöli, majd kihasználja a Windows ACL-eket a részletes hozzáférés-vezérléshez a címtár/fájl szintjén 

Három Azure beépített szerepkört biztosít a felhasználók számára a megosztási szintű engedélyek megadásához:

- **Storage file-adat SMB-megosztás olvasója** az Azure Storage-fájlmegosztás SMB-kapcsolaton keresztüli olvasási hozzáférését teszi lehetővé.
- A **Storage file-adat SMB-megosztási közreműködői** lehetővé teszik az olvasási, írási és törlési hozzáférést az Azure Storage-fájlmegosztás SMB-en keresztül.
- A **Storage file-adatsmb-megosztás emelt szintű közreműködője** lehetővé teszi az Azure Storage-FÁJLMEGOSZTÁS az SMB protokollon keresztül történő olvasását, írását, törlését és módosítását.

> [!IMPORTANT]
> Egy fájlmegosztás teljes körű felügyeleti ellenőrzése, beleértve a fájl tulajdonjogának átvételének lehetőségét, a Storage-fiók kulcsát kell használnia. Az Azure AD hitelesítő adatai nem támogatják a felügyeleti felügyeletet.

A Azure Portal, a Azure PowerShell vagy az Azure CLI használatával hozzárendelheti a beépített szerepköröket egy felhasználó Azure AD-identitásához a megosztási szintű engedélyek megadásához.

## <a name="assign-an-azure-role"></a>Azure-szerepkör kiosztása

### <a name="azure-portal"></a>Azure Portal

Ha Azure-szerepkört szeretne hozzárendelni egy Azure AD-identitáshoz a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg a fájlmegosztást, vagy [hozzon létre egy fájlmegosztást](storage-how-to-create-file-share.md).
1. Válassza a **Access Control (iam)** lehetőséget.
1. Válassza **a szerepkör-hozzárendelés hozzáadása** elemet.
1. A **szerepkör-hozzárendelés hozzáadása** panelen válassza ki a megfelelő beépített szerepkört (tárolási fájl adatsmb-megosztási olvasó, tárolási fájl adat SMB-megosztás közreműködője) a **szerepkör** listából. Az alapértelmezett beállításhoz ne **rendeljen hozzá hozzáférést** : **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**. Válassza ki a cél Azure AD-identitást név vagy e-mail-cím alapján. **A kiválasztott Azure AD-identitásnak hibrid identitásnak kell lennie, és csak Felhőbeli identitás lehet.** Ez azt jelenti, hogy ugyanazt az identitást is képviseli AD DSban.
1. A szerepkör-hozzárendelési művelet befejezéséhez válassza a **Mentés** lehetőséget.

### <a name="powershell"></a>PowerShell

A következő PowerShell-minta bemutatja, hogyan rendelhet hozzá Azure-szerepköröket egy Azure AD-identitáshoz a bejelentkezési név alapján. Az Azure-szerepkörök PowerShell-lel való hozzárendelésével kapcsolatos további információkért lásd: Azure-beli [szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure PowerShell modul használatával](../../role-based-access-control/role-assignments-powershell.md).

Az alábbi minta parancsfájl futtatása előtt cserélje le a helyőrző értékeket, beleértve a zárójeleket is, az értékekkel együtt.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>parancssori felület
  
A következő CLI 2,0 parancs egy Azure-szerepkört rendel hozzá egy Azure AD-identitáshoz a bejelentkezési név alapján. Az Azure-szerepkörök Azure CLI-vel való hozzárendelésével kapcsolatos további információkért lásd: Azure-beli [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md). 

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megosztott szintű engedélyeket rendelt hozzá, konfigurálnia kell a címtár-és a fájl szintű engedélyeket. Folytassa a következő cikkel.

[Harmadik rész: a könyvtár-és a fájl szintű engedélyek konfigurálása SMB protokollon keresztül](storage-files-identity-ad-ds-configure-permissions.md)
