---
title: A Azure AD Domain Services segítségével engedélyezze a fájladatok SMB-ről való hozzáférését
description: Megtudhatja, hogyan engedélyezheti az identitásalapú hitelesítést az SMB protokollon keresztül a Azure Files a Azure Active Directory Domain Services. A tartományhoz csatlakozott Windows rendszerű virtuális gépek ezután Azure AD hitelesítő adatokkal férhetnek hozzá az Azure-fájlmegosztáshoz.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/03/2021
ms.author: rogarana
ms.subservice: files
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: df2bd1c12c86de43e2a5057813a743d822dbda33
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718347"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>A Azure Active Directory Domain Services engedélyezése a Azure Files

[Azure Files](storage-files-introduction.md)   kétféle tartományi szolgáltatáson keresztül támogatja az identitásalapú hitelesítést az SMB protokollon keresztül: a helyi Active Directory Domain Services (AD DS) és a Azure Active Directory Domain Services (Azure AD DS). Határozottan javasoljuk, hogy [](./storage-files-active-directory-overview.md#how-it-works) tekintse át a Hogyan működik? című szakaszt, és válassza ki a megfelelő tartományi szolgáltatást a hitelesítéshez. A beállítás a választott tartományi szolgáltatástól függően eltérő. Ez a cikk az Azure-fájlmegosztásokkal való Azure AD DS és konfigurálásával foglalkozik.

Ha még nem használja az Azure-fájlmegosztásokat, javasoljuk, hogy olvassa el a tervezési útmutatót, mielőtt elolvassa a következő cikkeket. [](storage-files-planning.md)

> [!NOTE]
> Azure Files csak RC4-HMAC-val Azure AD DS Kerberos-hitelesítést támogatja. Az AES Kerberos-titkosítás még nem támogatott.
> Azure Files támogatja az Azure AD-Azure AD DS való teljes szinkronizálással való hitelesítést. Ha engedélyezte a hatókörrel rendelkező szinkronizálást a Azure AD DS amely csak korlátozott számú identitást szinkronizál az Azure AD-ból, a hitelesítés és az engedélyezés nem támogatott.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt engedélyezi az Azure AD-t SMB-n keresztül az Azure-fájlmegosztások számára, győződjön meg arról, hogy megfelel az alábbi előfeltételeknek:

1.  **Válasszon ki vagy hozzon létre egy Azure AD-bérlőt.**

    Az SMB-n keresztüli Azure AD-hitelesítéshez használhat új vagy meglévő bérlőt. Az elérni kívánt bérlőnek és fájlmegosztásnak ugyanannak az előfizetésnek kell társítva lennie.

    Új Azure AD-bérlő létrehozásához hozzáadhat egy Azure AD-bérlőt és [egy Azure AD-előfizetést.](/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Ha rendelkezik meglévő Azure AD-bérlővel, de szeretne létrehozni egy új bérlőt az Azure-fájlmegosztásokkal való használathoz, tekintse meg a [következőt:](/rest/api/datacatalog/create-an-azure-active-directory-tenant)Create an Azure Active Directory tenant (Új bérlő létrehozása a Azure Active Directory számára).

1.  **Engedélyezze Azure AD Domain Services Azure AD-bérlőn.**

    Az Azure AD hitelesítő adatokkal történő hitelesítés támogatásához engedélyeznie kell a Azure AD Domain Services azure AD-bérlőhöz. Ha nem Ön az Azure AD-bérlő rendszergazdája, lépjen kapcsolatba a rendszergazdával, és kövesse az Enable Azure Active Directory Domain Services using the Azure Portal( ) lépéses [útmutatót.](../../active-directory-domain-services/tutorial-create-instance.md)

    Általában körülbelül 15 percet vesz igénybe, hogy egy Azure AD DS üzembe helyezés befejeződik. Mielőtt továbblépne, ellenőrizze, hogy a jelszó-kivonat szinkronizálása Azure AD DS futó állapotú-e, és hogy a jelszó-kivonat szinkronizálása fut-e.

1.  **Azure-beli virtuális gép tartományhoz való csatlakozása Azure AD DS.**

    Ha egy virtuális gépről Azure AD hitelesítő adatokkal fér hozzá egy fájlmegosztáshoz, a virtuális gépnek tartományhoz kell csatlakozva lennie a Azure AD DS. További információ a virtuális gépek tartományhoz való csatlakozásról: Csatlakozás Windows Server rendszerű virtuális [géphez felügyelt tartományhoz.](../../active-directory-domain-services/join-windows-vm.md)

    > [!NOTE]
    > Azure AD DS SMB-alapú hitelesítés Azure-fájlmegosztásokkal csak a Windows 7 vagy Windows Server 2008 R2 feletti operációsrendszer-verziókban futó Azure-beli virtuális gépeken támogatott.

1.  **Válasszon ki vagy hozzon létre egy Azure-fájlmegosztást.**

    Válasszon ki egy új vagy meglévő fájlmegosztást, amely az Azure AD-bérlővel azonos előfizetéshez van társítva. További információ az új fájlmegosztás létrehozásáról: [Fájlmegosztás létrehozása a Azure Files.](storage-how-to-create-file-share.md)
    Az optimális teljesítmény érdekében javasoljuk, hogy a fájlmegosztás ugyanabban a régióban legyen, mint a virtuális gép, amelyről a megosztást el tervezi elérni.

1.  **Ellenőrizze Azure Files kapcsolatát az Azure-fájlmegosztások tárfiókk kulccsal történő csatlakoztatásával.**

    Annak ellenőrzéséhez, hogy a virtuális gép és a fájlmegosztás megfelelően van-e konfigurálva, próbálja meg a fájlmegosztást a tárfiók kulcsával csatlakoztatni. További információ: [Azure-fájlmegosztás csatlakoztatása](storage-how-to-use-files-windows.md)és a megosztás elérése Windows rendszeren.

## <a name="regional-availability"></a>Regionális elérhetőség

Azure Files azure Azure AD DS [nyilvános, gov](https://azure.microsoft.com/global-infrastructure/locations/)és kínai régióban érhető el.

## <a name="overview-of-the-workflow"></a>A munkafolyamat áttekintése

Mielőtt engedélyezi a Azure AD DS SMB-hitelesítést az Azure-fájlmegosztások számára, ellenőrizze, hogy az Azure AD- és az Azure Storage-környezetek megfelelően vannak-e konfigurálva. Javasoljuk, hogy kövesse [](#prerequisites) végig az előfeltételeket, és győződjön meg arról, hogy az összes szükséges lépést megfelelte.

Ezután tegye a következőket, hogy hozzáférést biztosítson Azure Files erőforrásokhoz Az Azure AD hitelesítő adataival:

1. Engedélyezze Azure AD DS SMB-hitelesítést a tárfiókhoz, hogy regisztrálja a tárfiókot a társított Azure AD DS üzemelő példányban.
2. Rendeljen hozzáférési engedélyeket egy megosztáshoz egy Azure AD-identitáshoz (felhasználóhoz, csoporthoz vagy szolgáltatásnévhez).
3. Konfigurálja az SMB-re vonatkozó NTFS-engedélyeket a könyvtárakhoz és fájlokhoz.
4. Azure-fájlmegosztás csatlakoztatása tartományhoz csatlakozott virtuális gépről.

Az alábbi ábra azt a végpontok között lekért munkafolyamatot mutatja be, amely lehetővé teszi a Azure AD DS SMB-protokollon keresztüli hitelesítést Azure Files.

![Az Azure AD-t SMB-n keresztül bemutató ábra Azure Files munkafolyamathoz](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>A Azure AD DS hitelesítésének engedélyezése

Ha engedélyezni Azure AD DS SMB-alapú hitelesítést az Azure Files-hoz, a tárfiókok tulajdonságát az Azure Portal, Azure PowerShell vagy az Azure CLI használatával állíthatja be. Ennek a tulajdonságnak a beállítása implicit módon "tartományhoz csatlakozik" a tárfiókot a társított Azure AD DS telepítéshez. Azure AD DS SMB protokollon keresztüli hitelesítés ezután engedélyezve lesz a tárfiókban lévő összes új és meglévő fájlmegosztáshoz.

Ne feledje, hogy az SMB Azure AD DS keresztüli hitelesítést csak azután engedélyezheti, hogy sikeresen üzembe Azure AD DS azure aD-bérlőn. További információkért lásd az [előfeltételeket.](#prerequisites)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha engedélyezni Azure AD DS SMB-alapú hitelesítést a [Azure Portal,](https://portal.azure.com)kövesse az alábbi lépéseket:

1. A Azure Portal a meglévő tárfiókhoz, vagy hozzon létre [egy tárfiókot.](../common/storage-account-create.md)
1. A Beállítások **szakaszban** válassza a Konfiguráció **lehetőséget.**
1. A **fájlmegosztások identitásalapú hozzáférése** területen váltsa az Azure Active Directory **Domain Service (AAD DS)** kapcsolóját Engedélyezve **beállításra.**
1. Kattintson a **Mentés** gombra.

Az alábbi kép bemutatja, hogyan engedélyezheti a Azure AD DS SMB-hitelesítést a tárfiókhoz.

:::image type="content" source="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png" alt-text="Képernyőkép a tárfiók konfigurációs paneljével, az Azure Active Directory Doman-szolgáltatások engedélyezve vannak." lightbox="media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Az SMB-Azure AD DS az Azure PowerShell-val való hitelesítés engedélyezéséhez telepítse a legújabb Az modult (2.4-es vagy újabb) vagy az Az.Storage modult (1.5-ös vagy újabb). A PowerShell telepítésével kapcsolatos további információkért lásd: Install Azure PowerShell on Windows with PowerShellGet (Az alkalmazások telepítése Windows rendszeren [a PowerShellGet használatával).](/powershell/azure/install-Az-ps)

Új tárfiók létrehozásához hívja meg a [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)paramétert, majd állítsa az **EnableAzureActiveDirectoryDomainServicesForFile** paramétert **true (igaz) értékre.** A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használja, a funkció engedélyezésének paramétere **az EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

A szolgáltatás meglévő tárfiókok esetében való engedélyezéséhez használja a következő parancsot:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha engedélyezni szeretné az SMB-alapú Azure AD-hitelesítést az Azure CLI-val, telepítse a cli legújabb verzióját (2.0.70-es vagy újabb verzió). További információ az Azure CLI telepítéséről: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

Új tárfiók létrehozásához hívja meg az [az storage account create](/cli/azure/storage/account#az-storage-account-create), és állítsa a tulajdonságot true `--enable-files-aadds` **(igaz) értékre.** A következő példában ne felejtse el lecserélni a helyőrző értékeket a saját értékeire. (Ha az előző előzetes modult használja, a funkció engedélyezésének paramétere **a file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

A szolgáltatás meglévő tárfiókok esetében való engedélyezéséhez használja a következő parancsot:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```
---

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Sikeresen engedélyezte a Azure AD DS SMB-alapú hitelesítést, és hozzárendelt egy egyéni szerepkört, amely hozzáférést biztosít egy Azure-fájlmegosztáshoz egy Azure AD-identitással. Ha további felhasználóknak szeretne hozzáférést ad a [](#assign-access-permissions-to-an-identity) fájlmegosztáshoz, kövesse a Hozzáférési engedélyek hozzárendelése identitás használatára és NTFS-engedélyek konfigurálása SMB-kapcsolaton keresztül szakasz [utasításait.](#configure-ntfs-permissions-over-smb)

## <a name="next-steps"></a>Következő lépések

Az azure-Azure Files és az Azure AD SMB-n keresztüli használatával kapcsolatos további információkért tekintse meg a következő forrásokat:

- [Az Azure Files identitásalapú hitelesítési támogatásának áttekintése SMB-hozzáférés esetén](storage-files-active-directory-overview.md)
- [Gyakori kérdések](storage-files-faq.md)
