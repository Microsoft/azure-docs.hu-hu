---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95562506"
---
## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek kiosztása identitáshoz

A Azure Files-erőforrások identitás-alapú hitelesítéssel való eléréséhez az identitásnak (egy felhasználónak, csoportnak vagy egyszerű szolgáltatásnak) rendelkeznie kell a szükséges engedélyekkel a megosztás szintjén. Ez a folyamat hasonló a Windows-megosztási engedélyek megadásához, ahol megadhatja, hogy egy adott felhasználó milyen típusú hozzáférést osszon meg a fájlmegosztás számára. Az ebben a szakaszban található útmutatás azt mutatja be, hogyan lehet olvasási, írási vagy törlési engedélyeket rendelni egy fájlmegosztás identitásához. 

Három Azure-beli beépített szerepkört vezettünk be a felhasználók számára a megosztási szintű engedélyek megadásához:

- **Storage file-adat SMB-megosztás olvasója** az Azure Storage-fájlmegosztás SMB-kapcsolaton keresztüli olvasási hozzáférését teszi lehetővé.
- A **Storage file-adat SMB-megosztási közreműködői** lehetővé teszik az olvasási, írási és törlési hozzáférést az Azure Storage-fájlmegosztás SMB-en keresztül.
- A **Storage file-ADATsmb-megosztás emelt szintű közreműködője** lehetővé teszi az Azure Storage-FÁJLMEGOSZTÁS az SMB protokollon keresztül történő olvasását, írását, törlését és módosítását.

> [!IMPORTANT]
> Egy fájlmegosztás teljes körű felügyeleti ellenőrzése, beleértve a fájl tulajdonjogának átvételének lehetőségét, a Storage-fiók kulcsát kell használnia. Az Azure AD hitelesítő adatai nem támogatják a felügyeleti felügyeletet.

A Azure Portal, a PowerShell vagy az Azure CLI használatával hozzárendelheti a beépített szerepköröket egy felhasználó Azure AD-identitásához a megosztási szintű engedélyek megadásához. Vegye figyelembe, hogy a megosztási szint Azure-szerepkör-hozzárendelése hosszabb időt is igénybe vehet. 

> [!NOTE]
> Ne felejtse el [szinkronizálni AD DS hitelesítő adatait az Azure ad-be](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) , ha a helyszíni AD DS használatát tervezi a hitelesítéshez. Az AD DSról az Azure AD-re való jelszó-kivonatolási szinkronizálás nem kötelező. A megosztási szint engedély a helyszíni AD DS szinkronizált Azure AD-identitáshoz lesz biztosítva.

Az általános javaslat a megosztási szint engedély használata a magas szintű hozzáférés-kezeléshez a felhasználók és identitások egy csoportját képviselő AD-csoportra vonatkozóan, majd az NTFS-engedélyek kihasználása a címtár/fájl szintjén a részletes hozzáférés-vezérléshez. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Azure-szerepkör kiosztása AD-identitáshoz

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha Azure-szerepkört szeretne hozzárendelni egy Azure AD-identitáshoz a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg a fájlmegosztást, vagy [hozzon létre egy fájlmegosztást](../articles/storage/files/storage-how-to-create-file-share.md).
2. Válassza a **Access Control (iam)** lehetőséget.
3. Válassza **a szerepkör-hozzárendelés hozzáadása** elemet.
4. A **szerepkör-hozzárendelés hozzáadása** panelen válassza ki a megfelelő beépített szerepkört (tárolási fájl adatsmb-megosztási olvasó, tárolási fájl adat SMB-megosztás közreműködője) a **szerepkör** listából. Az alapértelmezett beállításhoz ne **rendeljen hozzá hozzáférést** : **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**. Válassza ki a cél Azure AD-identitást név vagy e-mail-cím alapján.
5. A szerepkör-hozzárendelési művelet befejezéséhez válassza a **Mentés** lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-minta bemutatja, hogyan rendelhet hozzá Azure-szerepköröket egy Azure AD-identitáshoz a bejelentkezési név alapján. Az Azure-szerepkörök PowerShell-lel való hozzárendelésével kapcsolatos további információkért lásd: [a hozzáférés kezelése a RBAC és a Azure PowerShell használatával](../articles/role-based-access-control/role-assignments-powershell.md).

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
A következő CLI 2,0-parancs bemutatja, hogyan rendelhet hozzá Azure-szerepköröket egy Azure AD-identitáshoz a bejelentkezési név alapján. Az Azure-szerepkörök Azure CLI-vel való hozzárendelésével kapcsolatos további információkért lásd: a [hozzáférés kezelése a RBAC és az Azure CLI használatával](../articles/role-based-access-control/role-assignments-cli.md). 

A következő minta parancsfájl futtatása előtt ne felejtse el helyettesíteni a helyőrző értékeket, beleértve a zárójeleket is a saját értékeivel.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-engedélyek konfigurálása SMB-kapcsolaton keresztül

A megosztási szintű engedélyek RBAC való hozzárendelését követően megfelelő NTFS-engedélyeket kell rendelnie a gyökér, a könyvtár vagy a fájl szintjén. Gondoljon arra, hogy a megosztási szintű engedélyek magas szintű forgalomirányító, amely meghatározza, hogy a felhasználó hozzáférhet-e a megosztáshoz. Míg az NTFS-engedélyek részletesebben határozzák meg, hogy a felhasználó milyen műveleteket végezhet el a címtárban vagy a fájl szintjén.

Azure Files a teljes NTFS alapszintű és speciális engedélyeket támogatja. Az Azure-fájlmegosztás könyvtárain és fájljain NTFS-engedélyeket tekinthet meg és konfigurálhat, ha csatlakoztatja a megosztást, majd a Windows fájlkezelővel vagy a Windows [icacls](/windows-server/administration/windows-commands/icacls) vagy a [set-ACL](/powershell/module/microsoft.powershell.security/set-acl) parancsot futtatja. 

Az NTFS rendszergazdai jogosultságokkal történő konfigurálásához a megosztást a tartományhoz csatlakoztatott virtuális gépről a Storage-fiók kulcsa használatával kell csatlakoztatnia. Kövesse a következő szakaszban található utasításokat egy Azure-fájlmegosztás parancssorból való csatlakoztatásához és ennek megfelelően az NTFS-engedélyek konfigurálásához.

A fájlmegosztás gyökérkönyvtárában a következő engedélyek támogatottak:

- Builtin\rendszergazda: (OI) (CI) (F)
- NT AUTHORITY\SYSTEM: (OI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (IO) (GR, GE)
- NT AUTHORITY\Authenticated-felhasználók: (OI) (CI) (M)
- NT AUTHORITY\SYSTEM: (F)
- LÉTREHOZÓ TULAJDONOS: (OI) (CI) (IO) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

Az Azure-fájlmegosztás csatlakoztatásához használja a Windows **net use** parancsot. Ne felejtse el lecserélni az alábbi példában szereplő helyőrző értékeket a saját értékeire. A fájlmegosztás csatlakoztatásával kapcsolatos további információkért lásd: [Azure-fájlmegosztás használata a Windowsban](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Ha a Azure Fileshoz való csatlakozással kapcsolatos problémákat tapasztal, tekintse meg [a hibaelhárítási eszközt, amelyet Azure Files csatlakoztatási hibákhoz tettünk közzé a Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/)rendszeren. Az 445-as port blokkolása esetén [útmutatást](../articles/storage/files/storage-files-faq.md#on-premises-access) is biztosítunk a forgatókönyvek megoldásához. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-engedélyek konfigurálása a Windows fájlkezelővel

A Windows fájlkezelővel teljes hozzáférést biztosíthat a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is.

1. Nyissa meg a Windows fájlkezelő alkalmazást, és kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok** lehetőséget.
2. Válassza a **Biztonság** fület.
3. Válassza a **Szerkesztés lehetőséget.** az engedélyek módosításához.
4. Módosíthatja a meglévő felhasználók engedélyeit, vagy kiválaszthatja a **Hozzáadás...** lehetőséget az új felhasználók engedélyeinek megadásához.
5. Az új felhasználók hozzáadására szolgáló kérdés ablakban adja meg azt a célként megadott felhasználónevet, amelynek engedélyt szeretne adni az **adja meg a kijelölendő objektumok nevét** mezőbe, **majd válassza a Névellenőrzés lehetőséget** a MEGcélzott felhasználó teljes UPN-nevének megkereséséhez.
7.    Válassza az **OK** lehetőséget.
8.    A **Biztonság** lapon válassza ki az összes olyan engedélyt, amely számára engedélyezni szeretné az új felhasználót.
9.    Kattintson az **Alkalmaz** gombra.

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-engedélyek konfigurálása icacls-val

A következő Windows-paranccsal teljes körű engedélyeket adhat meg a fájlmegosztás alatt lévő összes könyvtárhoz és fájlhoz, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában szereplő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Ha többet szeretne megtudni arról, hogyan használható a icacls az NTFS-engedélyek megadásához és a különböző típusú támogatott engedélyekhez, tekintse meg [az icacls parancssori útmutatóját](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről

A következő folyamat ellenőrzi, hogy a fájlmegosztás és a hozzáférési engedélyek megfelelően lettek-e beállítva, és hogy elérhető-e egy Azure-fájlmegosztás egy tartományhoz csatlakoztatott virtuális gépről. Vegye figyelembe, hogy a megosztási szint Azure-szerepkör-hozzárendelése hosszabb időt is igénybe vehet. 

Jelentkezzen be a virtuális gépre azzal az Azure AD-identitással, amelyhez engedélyeket adott meg, ahogy az az alábbi képen is látható. Ha engedélyezte a helyszíni AD DS hitelesítést a Azure Fileshoz, használja a AD DS hitelesítő adatait. Azure AD DS-hitelesítéshez jelentkezzen be az Azure AD hitelesítő adataival.

![Az Azure AD bejelentkezési képernyőjét bemutató képernyőkép a felhasználói hitelesítéshez](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Az Azure-fájlmegosztás csatlakoztatásához használja az alábbi parancsot. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. Mivel a hitelesítése megtörtént, nem kell megadnia a Storage-fiók kulcsát, a helyszíni AD DS hitelesítő adatokat vagy az Azure AD DS hitelesítő adatait. Az egyszeri bejelentkezés a helyszíni AD DS vagy az Azure AD DS szolgáltatással történő hitelesítésre is használható. Ha AD DS hitelesítő adatokkal történő csatlakoztatási problémákba ütközik, tekintse meg a [Windows-problémák Azure Files hibaelhárítása a Windowsban](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) című témakört.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```