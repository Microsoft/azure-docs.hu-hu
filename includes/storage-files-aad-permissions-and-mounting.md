---
title: fájl belefoglalása
description: fájl belefoglalása
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 200bf290543747cf9abab6113b8013e2eec852a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513037"
---
## <a name="assign-access-permissions-to-an-identity"></a>Hozzáférési engedélyek hozzárendelése egy identitáshoz

Az Azure Files hitelesítéssel rendelkező erőforrások eléréséhez az identitásnak (felhasználónak, csoportnak vagy szolgáltatásnévnek) rendelkeznie kell a szükséges engedélyekkel a megosztás szintjén. Ez a folyamat hasonló a Windows-megosztási engedélyek megadásához, ahol megadhatja, hogy egy adott felhasználó milyen típusú hozzáféréssel rendelkezik egy fájlmegosztáshoz. Az ebben a szakaszban található útmutatás bemutatja, hogyan rendelhet olvasási, írási vagy törlési engedélyeket egy fájlmegosztáshoz egy identitáshoz. 

Három beépített Azure-szerepkört vezettünk be a megosztásszintű engedélyek felhasználók számára való megadásához:

- **A Storage-fájladatok SMB-megosztási olvasója** olvasási hozzáférést biztosít az Azure Storage-fájlmegosztások számára SMB-ről.
- **A Storage-fájladatok SMB-megosztási közreműködője** olvasási, írási és törlési hozzáférést biztosít az Azure Storage-fájlmegosztások számára az SMB-n keresztül.
- **A Storage-fájladatok SMB-megosztásának** emelt szintű közreműködője lehetővé teszi az AZURE Storage-fájlmegosztások NTFS-engedélyeinek olvasását, írását, törlését és módosítását SMB-kapcsolaton keresztül.

> [!IMPORTANT]
> A fájlmegosztások teljes körű felügyeleti vezérlése, beleértve a fájl tulajdonjogának képességét is, a tárfiókkulcsot kell használnia. Az Azure AD hitelesítő adatai nem támogatják a felügyeleti vezérlést.

A beépített szerepköröket a Azure Portal, a PowerShell vagy az Azure CLI használatával rendelheti hozzá egy felhasználó Azure AD-identitásához megosztási szintű engedélyek megadásához. Vegye figyelembe, hogy a megosztási szint Azure-beli szerepkör-hozzárendelése némi időt is eltelhet. 

> [!NOTE]
> Ne felejtse el [szinkronizálni AD DS hitelesítő](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) adatait az Azure AD-be, ha a helyszíni AD DS használni a hitelesítéshez. A jelszó kivonatának szinkronizálása AD DS Azure AD-be nem kötelező. A helyszíni virtuális hálózatról szinkronizált Azure AD-identitáshoz megosztási szintű engedély AD DS.

Az általános javaslat az, hogy a felhasználók és identitások egy csoportját képviselő AD-csoport magas szintű hozzáférés-kezeléséhez megosztási szintű engedélyeket használjon, majd használja az NTFS-engedélyeket a címtár-/fájlszintű részletes hozzáférés-vezérléshez. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Azure-szerepkör hozzárendelése AD-identitáshoz

# <a name="portal"></a>[Portál](#tab/azure-portal)
Ha Azure-szerepkört szeretne hozzárendelni egy Azure AD-identitáshoz a Azure Portal [használatával,](https://portal.azure.com)kövesse az alábbi lépéseket:

1. A Azure Portal a fájlmegosztáshoz, vagy [a Fájlmegosztás létrehozása gombra.](../articles/storage/files/storage-how-to-create-file-share.md)
2. Válassza **Access Control (IAM) lehetőséget.**
3. Válassza **a Szerepkör-hozzárendelés hozzáadása lehetőséget**
4. A **Szerepkör-hozzárendelés hozzáadása** panelen válassza ki a megfelelő beépített szerepkört (Storage-fájladatok SMB-megosztásának olvasója, Tárolófájladatok SMB-megosztásának közreműködője) a Szerepkör **listából.** A **Hozzáférés hozzárendelése beállítást** hagyja meg az alapértelmezett beállításnál: Azure **AD-felhasználó, -csoport vagy -szolgáltatásnév.** Válassza ki a cél Azure AD-identitást név vagy e-mail-cím alapján.
5. A **szerepkör-hozzárendelési** művelet befejezéséhez válassza a Mentés lehetőséget.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az alábbi PowerShell-minta bemutatja, hogyan rendelhet hozzá Azure-szerepkört egy Azure AD-identitáshoz a bejelentkezési név alapján. További információ az Azure-szerepkörök PowerShell-alapú hozzárendelésről: Hozzáférés kezelése [AZ RBAC](../articles/role-based-access-control/role-assignments-powershell.md)és a Azure PowerShell.

Az alábbi parancsfájlminta futtatása előtt ne felejtse el lecserélni a helyőrző értékeket, beleértve a szögletes zárójeleket is a saját értékeire.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
  
A következő CLI 2.0-parancs bemutatja, hogyan rendelhet hozzá Azure-szerepkört egy Azure AD-identitáshoz a bejelentkezési név alapján. További információ az Azure-szerepkörök Azure CLI-hez való hozzárendelésről: [Hozzáférés kezelése az RBAC](../articles/role-based-access-control/role-assignments-cli.md)és az Azure CLI használatával. 

Az alábbi parancsfájlminta futtatása előtt ne felejtse el lecserélni a helyőrző értékeket, beleértve a szögletes zárójeleket is a saját értékeire.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>NTFS-engedélyek konfigurálása SMB-kapcsolaton keresztül

Miután rbac-val oszt ki megosztási szintű engedélyeket, megfelelő NTFS-engedélyeket kell hozzárendelnie a gyökér, a könyvtár vagy a fájl szintjén. A megosztási szintű engedélyeket olyan magas szintű forgalomirányítóként kell felhasználhatja, amely meghatározza, hogy egy felhasználó hozzáférhet-e a megosztáshoz. Míg az NTFS-engedélyek részletesebben határozzák meg, hogy a felhasználó milyen műveleteket tud tenni a könyvtár vagy a fájl szintjén.

Azure Files az NTFS alapszintű és speciális engedélyeinek teljes készletét támogatja. Az Azure-fájlmegosztások könyvtáraira és fájljaira vonatkozó NTFS-engedélyeket a megosztás csatlakoztatásával, majd a Windows Fájlkezelő vagy a Windows [icacls](/windows-server/administration/windows-commands/icacls) vagy [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) parancs futtatásával lehet megtekinteni és konfigurálni. 

Ha superuser engedélyekkel rendelkező NTFS-t konfigurál, csatlakoztatnia kell a megosztást a tartományhoz csatlakozott virtuális gép tárfiókkulcsával. A következő szakaszban található utasításokat követve csatlakoztathatja az Azure-fájlmegosztást a parancssorból, és ennek megfelelően konfigurálhatja az NTFS-engedélyeket.

A fájlmegosztások gyökérkönyvtárában a következő engedélykészletek támogatottak:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Hitelesített felhasználók:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- LÉTREHOZÓ TULAJDONOSA:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Fájlmegosztás csatlakoztatása a parancssorból

Az **Azure-fájlmegosztás csatlakoztatására** használja a Windows net use parancsot. Ne felejtse el lecserélni a következő példában lévő helyőrző értékeket a saját értékeire. További információ a fájlmegosztások csatlakoztatásával kapcsolatban: [Azure-fájlmegosztás használata Windows rendszeren.](../articles/storage/files/storage-how-to-use-files-windows.md) 

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

Ha problémákat tapasztal a Azure Files kapcsolatban, tekintse meg a windowsos csatlakoztatási hibákhoz [Azure Files hibaelhárító eszközt.](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/) Emellett útmutatást is [nyújtunk a](../articles/storage/files/storage-files-faq.md#on-premises-access) 445-ös port blokkolt forgatókönyvei megoldáshoz. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>NTFS-engedélyek konfigurálása Windows Fájlkezelő

A Windows Fájlkezelő teljes körű engedélyt adhat a fájlmegosztás összes könyvtárának és fájljának, beleértve a gyökérkönyvtárat is.

1. Nyissa meg a Windows Fájlkezelő, kattintson a jobb gombbal a fájlra/könyvtárra, és válassza a **Tulajdonságok lehetőséget.**
2. Válassza a **Biztonság** lapot.
3. Válassza **a Szerkesztés lehetőséget.** az engedélyek módosítása.
4. Módosíthatja a meglévő felhasználók engedélyét, vagy a **Hozzáadás...** lehetőséget választva új felhasználóknak adhat engedélyeket.
5. Az új felhasználók hozzáadására vonatkozó parancssori ablakban adja meg azt a célfelhasználónevet, amely számára engedélyeket kíván megadni az **Enter the object names to select** (Adja meg a kijelölni kívánt objektumok nevét) mezőben, majd válassza a Check Names **(Nevek** ellenőrzése) lehetőséget a célfelhasználó teljes UPN-nevének megkeresésében.
7.    Válassza az **OK** lehetőséget.
8.    A Biztonság **lapon** válassza ki az összes olyan engedélyt, amely az új felhasználónak ad hozzáférést.
9.    Kattintson az **Alkalmaz** gombra.

### <a name="configure-ntfs-permissions-with-icacls"></a>NTFS-engedélyek konfigurálása icacl-ekkel

A következő Windows-paranccsal teljes körű engedélyeket adhat a fájlmegosztás összes könyvtárához és fájlához, beleértve a gyökérkönyvtárat is. Ne felejtse el lecserélni a példában lévő helyőrző értékeket a saját értékeire.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Az icacl-ekkel NTFS-engedélyek beállításával és a különböző támogatott engedélytípusokkal kapcsolatos további információkért tekintse meg az [icacl-hez](/windows-server/administration/windows-commands/icacls)használható parancssori referenciát.

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Fájlmegosztás csatlakoztatása tartományhoz csatlakozott virtuális gépről

A következő folyamat ellenőrzi, hogy a fájlmegosztás és a hozzáférési engedélyek megfelelően vannak-e beállítva, és hogy hozzáférhet-e egy Azure-fájlmegosztáshoz egy tartományhoz csatlakozott virtuális gépről. Vegye figyelembe, hogy a megosztási szint Azure-beli szerepkör-hozzárendelése némi időt is eltelhet. 

Jelentkezzen be a virtuális gépre azzal az Azure AD-identitással, amelyhez engedélyeket adott, ahogy az alábbi képen látható. Ha engedélyezte a helyszíni AD DS hitelesítést a Azure Files, használja AD DS hitelesítő adatait. A Azure AD DS az Azure AD hitelesítő adataival jelentkezzen be.

![Képernyőkép az Azure AD bejelentkezési képernyőről a felhasználói hitelesítéshez](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

A következő paranccsal csatlakoztatja az Azure-fájlmegosztást. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. A hitelesítés után nem kell megadnia a tárfiókkulcsot, a helyszíni AD DS hitelesítő adatait vagy a Azure AD DS hitelesítő adatait. Az egyszeri bejelentkezés helyszíni vagy helyszíni hitelesítéshez AD DS vagy Azure AD DS. Ha problémákat tapasztal az AD DS hitelesítő adataival való csatlakoztatás során, tekintse meg a [Windows Azure Files hibáinak elhárításával kapcsolatos](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) témakört.

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
