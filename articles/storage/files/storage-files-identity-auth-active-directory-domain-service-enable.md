---
title: A Azure AD Domain Services használata az SMB-en keresztüli fájlok elérésének engedélyezéséhez
description: Megtudhatja, hogyan engedélyezheti az identitás-alapú hitelesítést a Server Message Block (SMB) protokollon keresztül Azure Files a Azure Active Directory Domain Services használatával. A tartományhoz csatlakoztatott Windows-alapú virtuális gépek (VM-EK) az Azure AD-beli hitelesítő adatok használatával érhetik el az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2d9f7eccae6b87923b52119ded90ced5e4206d7b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85510388"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Active Directory Domain Services hitelesítés engedélyezése Azure Files

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Az Azure-fájlmegosztás SMB protokollon keresztüli Azure AD-hitelesítésének áttekintését lásd: [az SMB protokollon keresztüli Azure Active Directory hitelesítés áttekintése Azure Fileshoz](storage-files-active-directory-overview.md). Ebből a cikkből megtudhatja, hogyan engedélyezheti a (z) Azure Active Directory Domain Services (Azure AD DS) hitelesítését Azure Fileson.

> [!NOTE]
> Azure Files támogatja a Kerberos-hitelesítést az Azure AD DS és az RC4-HMAC titkosítás révén. Az AES Kerberos-titkosítás még nem támogatott.
> A Azure Files az Azure AD-vel való teljes szinkronizálással támogatja az Azure AD DS hitelesítését. Ha engedélyezte a hatókörön belüli szinkronizálást az Azure AD DSban, amely csak korlátozott számú identitást szinkronizál az Azure AD-ből, a hitelesítés és az engedélyezés nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt engedélyezte az Azure AD-t az Azure-fájlmegosztás SMB-en keresztül, győződjön meg arról, hogy végrehajtotta a következő előfeltételeket:

1.  **Válasszon ki vagy hozzon létre egy Azure AD-bérlőt.**

    Az Azure AD-hitelesítéshez új vagy meglévő bérlőt használhat az SMB protokollon keresztül. A bérlőt és az elérni kívánt fájlmegosztást ugyanahhoz az előfizetéshez kell társítani.

    Új Azure AD-bérlő létrehozásához [hozzáadhat egy Azure ad-bérlőt és egy Azure ad-előfizetést](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Ha rendelkezik meglévő Azure AD-Bérlővel, de szeretne létrehozni egy új bérlőt az Azure-fájlmegosztás használatához, tekintse meg a [Azure Active Directory bérlő létrehozása](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)című témakört.

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

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az Azure AD DS Azure Files hitelesítés az [összes Azure-beli nyilvános régióban](https://azure.microsoft.com/global-infrastructure/locations/)elérhető.

## <a name="overview-of-the-workflow"></a>A munkafolyamat áttekintése

Mielőtt engedélyezi az Azure AD DS az Azure-fájlmegosztás SMB-alapú hitelesítését, ellenőrizze, hogy az Azure AD és az Azure Storage-környezetek megfelelően vannak-e konfigurálva. Javasoljuk, hogy járjon el az [Előfeltételek](#prerequisites) között, és győződjön meg arról, hogy végrehajtotta az összes szükséges lépést.

Ezután tegye a következőket az Azure AD-beli hitelesítő adatokkal Azure Files erőforrásaihoz való hozzáférés biztosításához:

1. Engedélyezze az Azure AD DS hitelesítést az SMB protokollon keresztül a Storage-fiók számára a Storage-fiók regisztrálásához a társított Azure AD DS-telepítéssel.
2. Megosztás hozzáférési engedélyeinek kiosztása egy Azure AD-identitáshoz (felhasználó, csoport vagy egyszerű szolgáltatásnév).
3. NTFS-engedélyek konfigurálása az SMB protokollal a címtárakhoz és a fájlokhoz.
4. Azure-fájlmegosztás csatlakoztatása tartományhoz csatlakoztatott virtuális gépről.

Az alábbi ábra a teljes munkafolyamatot mutatja be, amely lehetővé teszi az Azure AD DS hitelesítés használatát az SMB-en keresztül a Azure Files számára.

![Az Azure AD-t az Azure Files-munkafolyamathoz SMB-t megjelenítő diagram](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. az Azure AD DS-hitelesítés engedélyezése a fiókhoz

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure Fileshoz, a Azure Portal, a Azure PowerShell vagy az Azure CLI használatával beállíthat egy tulajdonságot a Storage-fiókokban. Ha a tulajdonságot implicit módon állítja be, a "tartomány csatlakozik" a Storage-fiókhoz a társított Azure AD DS-telepítéssel. Az Azure AD DS az SMB protokollon keresztüli hitelesítés a Storage-fiókban lévő összes új és meglévő fájlmegosztás esetében engedélyezve lesz.

Ne feledje, hogy az Azure AD DS-hitelesítést csak akkor engedélyezheti az SMB-en keresztül, ha sikeresen telepítette az Azure AD DSt az Azure AD-bérlőbe. További információ: [Előfeltételek](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a [Azure Portal](https://portal.azure.com)használatával, kövesse az alábbi lépéseket:

1. A Azure Portal nyissa meg a meglévő Storage-fiókot, vagy [hozzon létre egy Storage-fiókot](../common/storage-account-create.md).
1. A **Beállítások** szakaszban válassza a **Konfigurálás**lehetőséget.
1. A **fájlmegosztás identitás-alapú hozzáférése** területen kapcsolja be a **Azure Active Directory tartományi szolgáltatás (HRE DS)** kapcsolóját, hogy **engedélyezve**legyen.
1. Kattintson a **Mentés** gombra.

Az alábbi képen bemutatjuk, hogyan engedélyezhető az Azure AD DS hitelesítés az SMB protokollon keresztül a Storage-fiókhoz.

![Az Azure AD DS hitelesítés engedélyezése az SMB protokollon keresztül a Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Ha engedélyezni szeretné az Azure AD DS hitelesítést az SMB protokollon keresztül a Azure PowerShell, telepítse a legújabbat az az Module (2,4 vagy újabb) vagy az az. Storage modul (1,5 vagy újabb). A PowerShell telepítésével kapcsolatos további információkért lásd: [Azure PowerShell telepítése Windows rendszerre a PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps)használatával.

Új Storage-fiók létrehozásához hívja a [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), majd állítsa a **EnableAzureActiveDirectoryDomainServicesForFile** paramétert **true (igaz**) értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használta, a szolgáltatás engedélyezésének paramétere a következő: **EnableAzureFilesAadIntegrationForSMB**.)

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


### <a name="azure-cli"></a>Azure CLI

Ha engedélyezni szeretné az Azure AD-hitelesítést az SMB protokollon keresztül az Azure CLI-vel, telepítse a CLI legújabb verzióját (2.0.70 vagy újabb verzió). Az Azure CLI telepítésével kapcsolatos további információkért lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Hozzon létre egy új Storage-fiókot az [az Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)paranccsal, és állítsa a `--enable-files-aadds` tulajdonságot **true (igaz**) értékre. A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használta, a szolgáltatás engedélyezésének paramétere a **file-HRE**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Ha engedélyezni szeretné a funkciót a meglévő Storage-fiókokon, használja a következő parancsot:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Sikeresen engedélyezte az Azure AD DS hitelesítést az SMB protokollon keresztül, és hozzárendelt egy egyéni szerepkört, amely hozzáférést biztosít egy Azure-fájlmegosztás Azure AD-identitással való eléréséhez. Ha további felhasználóknak szeretne hozzáférést adni a fájlmegosztás eléréséhez, kövesse a [hozzáférési engedélyek hozzárendelése](#2-assign-access-permissions-to-an-identity) az identitás használatához és az NTFS- [engedélyek SMB-szakaszokon keresztüli konfigurálásához](#3-configure-ntfs-permissions-over-smb)című témakör utasításait.

## <a name="next-steps"></a>További lépések

A Azure Files és az Azure AD SMB használatával történő használatáról további információt az alábbi forrásokban talál:

- [Az SMB-hozzáférésre vonatkozó Azure Files identitás-alapú hitelesítés támogatásának áttekintése](storage-files-active-directory-overview.md)
- [Gyakori kérdések](storage-files-faq.md)
