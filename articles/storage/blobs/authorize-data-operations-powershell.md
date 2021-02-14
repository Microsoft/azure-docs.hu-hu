---
title: PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a Blobok adatainak eléréséhez
titleSuffix: Azure Storage
description: A PowerShell támogatja az Azure AD-beli hitelesítő adatokkal való bejelentkezést, hogy parancsokat futtasson az Azure Storage-beli blob-adatokon. A munkamenethez hozzáférési token van megadva, és a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági tag számára hozzárendelt Azure-szerepkörtől függenek.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.openlocfilehash: 45fa5cf4c76577cb5e8ba9bf482f4aab7301e3c6
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391490"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-data"></a>PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a Blobok adatainak eléréséhez

Az Azure Storage olyan bővítményeket biztosít a PowerShellhez, amelyek lehetővé teszik a Azure Active Directory-(Azure AD-) hitelesítő adatokkal való bejelentkezést és parancsfájlok futtatását. Ha Azure AD-beli hitelesítő adatokkal jelentkezik be a PowerShellbe, egy OAuth 2,0 hozzáférési tokent ad vissza. A PowerShell automatikusan használja ezt a tokent, hogy engedélyezze a további adatműveleteket a blob Storage-ban. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Engedélyeket rendelhet a blob-adathoz az Azure AD rendszerbiztonsági tag számára az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával. Az Azure Storage-beli Azure-szerepkörökkel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz az Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Támogatott műveletek

Az Azure Storage-bővítmények a blob-adatokon végrehajtott műveletek esetében támogatottak. A felhívható műveletek az Azure AD rendszerbiztonsági tag által a PowerShellbe való bejelentkezéshez megadott engedélyektől függenek. Az Azure Storage-tárolók engedélyei az Azure RBAC keresztül rendelhetők hozzá. Ha például hozzá lett rendelve a **blob-Adatolvasó** szerepkörhöz, futtathat parancsfájl-parancsokat, amelyek egy tárolóból olvasnak be egy adatforrást. Ha a **blob-adatközreműködői** szerepkört hozzárendelte, akkor futtathat parancsfájl-parancsokat, amelyek egy tárolót vagy az általuk tartalmazott adattartalmakat olvassák, írhatják vagy törölhetik.

A tárolók egyes Azure Storage-műveleteihez szükséges engedélyekkel kapcsolatos további információkért lásd: [tárolási műveletek hívása OAuth-tokenekkel](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

> [!IMPORTANT]
> Ha egy Storage-fiók zárolva van egy Azure Resource Manager **írásvédett** zárolással, a [kulcsok listázása](/rest/api/storagerp/storageaccounts/listkeys) művelet nem engedélyezett ehhez a Storage-fiókhoz. A **kulcsok listázása** post művelet, és az összes post művelet meg lesz akadályozva, ha **írásvédett** zárolás van konfigurálva a fiókhoz. Emiatt, ha a fiók **írásvédett** zárolással van zárolva, a fiók kulcsaival nem rendelkező felhasználóknak az Azure ad hitelesítő adatait kell használniuk a Blobok adatainak eléréséhez. A PowerShellben adja meg a `-UseConnectedAccount` paramétert, és hozzon létre egy **AzureStorageContext** -OBJEKTUMOT az Azure ad-beli hitelesítő adataival.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-parancsok meghívása az Azure AD hitelesítő adataival

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a Azure PowerShell használatával szeretne bejelentkezni, és az Azure Storage-ban az Azure Storage-ban további műveleteket hajt végre, hozzon létre egy tárolási környezetet a Storage-fiókra való hivatkozáshoz, és adja meg a `-UseConnectedAccount` paramétert.

Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót egy új Storage-fiókban Azure PowerShell az Azure AD-beli hitelesítő adataival. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

1. Jelentkezzen be az Azure-fiókjába a [Kapcsolódás-AzAccount](/powershell/module/az.accounts/connect-azaccount) paranccsal:

    ```powershell
    Connect-AzAccount
    ```

    További információ az Azure-ba történő bejelentkezésről a PowerShell használatával: [bejelentkezés Azure PowerShellsal](/powershell/azure/authenticate-azureps).

1. Hozzon létre egy Azure-erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)hívásával. 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Hozzon létre egy Storage-fiókot a [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)hívásával.

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Szerezze be a Storage-fiók környezetét, amely az új Storage-fiókot adja meg a [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)hívásával. Storage-fiók esetén a hitelesítő adatok ismételt átadása helyett hivatkozhat a környezetre. Az `-UseConnectedAccount` Azure ad-beli hitelesítő adataival a következő adatműveletek meghívásához használja a paramétert:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. A tároló létrehozása előtt rendelje hozzá a [Storage blob adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört saját magának. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Hozzon létre egy tárolót a [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer)hívásával. Mivel ez a hívás az előző lépésekben létrehozott környezetet használja, a tároló az Azure AD-beli hitelesítő adataival jön létre.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Következő lépések

- [A PowerShell használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adateléréshez](../common/storage-auth-aad-rbac-powershell.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](../common/storage-auth-aad-msi.md)