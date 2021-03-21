---
title: A Azure AD Domain Services használata az SMB-en keresztüli fájlok elérésének engedélyezéséhez
description: Megtudhatja, hogyan engedélyezheti az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül Azure Files a Azure Active Directory Domain Services használatával. A tartományhoz csatlakoztatott Windows-alapú virtuális gépek (VM-EK) az Azure AD-beli hitelesítő adatok használatával érhetik el az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: 3abca397186572cabb4f7ae99edae8688ea4d9a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499509"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Active Directory Domain Services hitelesítés engedélyezése Azure Files

[Azure Files](storage-files-introduction.md)   támogatja a kiszolgálói üzenetblokk (SMB) szolgáltatáson keresztüli identitás-alapú hitelesítést a következő két típusú tartományi szolgáltatáson keresztül: helyszíni Active Directory Domain Services (AD DS) és Azure Active Directory Domain Services (Azure AD DS). Javasoljuk, hogy tekintse át a [működéséről szóló szakaszt](./storage-files-active-directory-overview.md#how-it-works) , és válassza ki a megfelelő tartományi szolgáltatást a hitelesítéshez. A beállítás a választott tartományi szolgáltatástól függően eltérő. Ebből a cikkből megtudhatja, hogyan engedélyezheti és konfigurálhatja az Azure-AD DS az Azure-fájlmegosztás segítségével történő hitelesítéshez.

Ha még nem ismeri az Azure-fájlmegosztás újdonságait, javasoljuk, hogy olvassa el a [tervezési útmutatót](storage-files-planning.md) , mielőtt elolvassa a következő cikksorozatot.

> [!NOTE]
> Azure Files támogatja a Kerberos-hitelesítést az Azure AD DS csak az RC4-HMAC-mel. Az AES Kerberos-titkosítás még nem támogatott.
> A Azure Files az Azure AD-vel való teljes szinkronizálással támogatja az Azure AD DS hitelesítését. Ha engedélyezte a hatókörön belüli szinkronizálást az Azure AD DSban, amely csak korlátozott számú identitást szinkronizál az Azure AD-ből, a hitelesítés és az engedélyezés nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt engedélyezte az Azure AD-t az Azure-fájlmegosztás SMB-en keresztül, győződjön meg arról, hogy végrehajtotta a következő előfeltételeket:

1.  **Válasszon ki vagy hozzon létre egy Azure AD-bérlőt.**

    Az Azure AD-hitelesítéshez új vagy meglévő bérlőt használhat az SMB protokollon keresztül. A bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani.

    Új Azure AD-bérlő létrehozásához [hozzáadhat egy Azure ad-bérlőt és egy Azure ad-előfizetést](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Ha rendelkezik meglévő Azure AD-Bérlővel, de szeretne létrehozni egy új bérlőt az Azure-fájlmegosztás használatához, tekintse meg a [Azure Active Directory bérlő létrehozása](/rest/api/datacatalog/create-an-azure-active-directory-tenant)című témakört.

1.  **Engedélyezze Azure AD Domain Services az Azure AD-bérlőn.**

    Az Azure AD-beli hitelesítő adatokkal való hitelesítés támogatásához engedélyeznie kell Azure AD Domain Services az Azure AD-bérlő számára. Ha nem az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse a lépésenkénti útmutatót, amely [lehetővé teszi Azure Active Directory Domain Services használatát a Azure Portal használatával](../../active-directory-domain-services/tutorial-create-instance.md).

    Általában körülbelül 15 percet vesz igénybe, hogy az Azure AD DS üzembe helyezése befejeződjön. A következő lépés végrehajtása előtt ellenőrizze, hogy **fut**-e az Azure AD DS állapota, és hogy engedélyezve van-e a jelszó kivonatának szinkronizálása.

1.  **Tartomány – Azure-beli virtuális gép csatlakoztatása az Azure AD DShoz.**

    Ha egy virtuális gépről Azure AD-beli hitelesítő adatokkal kívánja elérni a fájlmegosztást, a virtuális gépnek tartományhoz kell tartoznia az Azure AD DS-hez. A virtuális gépek tartományhoz való csatlakoztatásával kapcsolatos további információkért lásd: [Windows Server rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Az Azure-AD DS az SMB-en keresztüli hitelesítés az Azure-fájlmegosztás esetében csak a Windows 7 vagy Windows Server 2008 R2 rendszerű operációs rendszeren futó Azure-beli virtuális gépeken támogatott.

1.  **Válasszon ki vagy hozzon létre egy Azure-fájlmegosztást.**

    Válasszon ki egy új vagy meglévő fájlmegosztást, amely ugyanahhoz az előfizetéshez tartozik, mint az Azure AD-bérlő. További információ az új fájlmegosztás létrehozásáról: [fájlmegosztás létrehozása Azure Filesban](storage-how-to-create-file-share.md).
    Az optimális teljesítmény érdekében javasoljuk, hogy a fájlmegosztás ugyanabban a régióban legyen, mint a virtuális gép, amelyről a megosztást szeretné elérni.

1.  **Ellenőrizze Azure Files kapcsolatot az Azure-fájlmegosztás a Storage-fiók kulcsa használatával történő csatlakoztatásával.**

    Annak ellenőrzéséhez, hogy a virtuális gép és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg csatlakoztatni a fájlmegosztást a Storage-fiók kulcsa alapján. További információ: [Azure-fájlmegosztás csatlakoztatása és a megosztás elérése a Windowsban](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Regionális elérhetőség

Az Azure AD DS Azure Files hitelesítés az [összes Azure Public, gov és China régióban](https://azure.microsoft.com/global-infrastructure/locations/)elérhető.

## <a name="overview-of-the-workflow"></a>A munkafolyamat áttekintése

Mielőtt engedélyezi az Azure AD DS az Azure-fájlmegosztás SMB-alapú hitelesítését, ellenőrizze, hogy az Azure AD és az Azure Storage-környezetek megfelelően vannak-e konfigurálva. Javasoljuk, hogy járjon el az [Előfeltételek](#prerequisites) között, és győződjön meg arról, hogy végrehajtotta az összes szükséges lépést.

Ezután tegye a következőket az Azure AD-beli hitelesítő adatokkal Azure Files erőforrásaihoz való hozzáférés biztosításához:

1. Engedélyezze az Azure AD DS hitelesítést az SMB protokollon keresztül a Storage-fiók számára a Storage-fiók regisztrálásához a társított Azure AD DS-telepítéssel.
2. Megosztás hozzáférési engedélyeinek kiosztása egy Azure AD-identitáshoz (felhasználó, csoport vagy egyszerű szolgáltatásnév).
3. NTFS-engedélyek konfigurálása az SMB protokollal a címtárakhoz és a fájlokhoz.
4. Azure-fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről.

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD DS hitelesítés használatát az SMB-en keresztül a Azure Files számára.

![Az Azure AD-t az Azure Files-munkafolyamathoz SMB-t megjelenítő diagram](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Azure AD DS-hitelesítés engedélyezése a fiókhoz

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure Fileshoz, a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával beállíthat egy tulajdonságot a Storage-fiókokban. Ha a tulajdonságot implicit módon állítja be, a "tartomány csatlakozik" a Storage-fiókhoz a társított Azure AD DS-telepítéssel. Az Azure AD DS az SMB protokollon keresztüli hitelesítés a Storage-fiókban lévő összes új és meglévő fájlmegosztás esetében engedélyezve lesz.

Ne feledje, hogy az Azure AD DS-hitelesítést csak akkor engedélyezheti az SMB-en keresztül, ha sikeresen telepítette az Azure AD DSt az Azure AD-bérlőbe. További információ: [Előfeltételek](#prerequisites).

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a meglévő Storage-fiókot, vagy [hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
1. A **Beállítások** szakaszban válassza a **Konfigurálás** lehetőséget.
1. A **fájlmegosztás identitás-alapú hozzáférése** területen kapcsolja be a **Azure Active Directory tartományi szolgáltatás (HRE DS)** kapcsolóját, hogy **engedélyezve** legyen.
1. Kattintson a **Mentés** gombra.

Az alábbi képen bemutatjuk, hogyan engedélyezhető az Azure AD DS hitelesítés az SMB protokollon keresztül a Storage-fiókhoz.

![Az Azure AD DS hitelesítés engedélyezése az SMB protokollon keresztül a Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure PowerShell, telepítse a legújabbat az az Module (2,4 vagy újabb) vagy az az. Storage modul (1,5 vagy újabb). A PowerShell telepítésével kapcsolatos további információkért lásd: [Azure PowerShell telepítése Windows rendszerre a PowerShellGet](/powershell/azure/install-Az-ps)használatával.

Új Storage-fiók létrehozásához hívja a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount), majd állítsa a **EnableAzureActiveDirectoryDomainServicesForFile** paramétert **true (igaz**) értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előnézeti modult használta, a szolgáltatás engedélyezésének paramétere a **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Ha engedélyezni szeretné a funkciót a meglévő Storage-fiókokon, használja a következő parancsot:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha engedélyezni szeretné az Azure AD-hitelesítést az SMB protokollon keresztül az Azure CLI-vel, telepítse a CLI legújabb verzióját (2.0.70 vagy újabb verzió). Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Hozzon létre egy új Storage-fiókot az [az Storage Account Create](/cli/azure/storage/account#az-storage-account-create)paranccsal, és állítsa a `--enable-files-aadds` tulajdonságot **true (igaz**) értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használta, a szolgáltatás engedélyezésének paramétere a **file-HRE**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Ha engedélyezni szeretné a funkciót a meglévő Storage-fiókokon, használja a következő parancsot:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Sikeresen engedélyezte az Azure AD DS hitelesítést az SMB protokollon keresztül, és hozzárendelt egy egyéni szerepkört, amely hozzáférést biztosít egy Azure-fájlmegosztás Azure AD-identitással való eléréséhez. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztás eléréséhez, kövesse a [hozzáférési engedélyek hozzárendelése](#assign-access-permissions-to-an-identity) az identitás használatához és az NTFS- [engedélyek SMB-szakaszokon keresztüli konfigurálásához](#configure-ntfs-permissions-over-smb)című témakör utasításait.

## <a name="next-steps"></a>Következő lépések

A Azure Files és az Azure AD SMB használatával történő használatáról további információt az alábbi forrásokban talál:

- [Az Azure Files identitásalapú hitelesítési támogatásának áttekintése SMB-hozzáférés esetén](storage-files-active-directory-overview.md)
- [Gyakori kérdések](storage-files-faq.md)
