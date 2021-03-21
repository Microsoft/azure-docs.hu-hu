---
title: PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a várólista adatainak eléréséhez
titleSuffix: Azure Storage
description: A PowerShell támogatja az Azure AD-beli hitelesítő adatokkal való bejelentkezést az Azure Queue Storage-adatokra vonatkozó parancsok futtatásához. A munkamenethez hozzáférési token van megadva, és a hívási műveletek engedélyezésére szolgál. Az engedélyek az Azure AD rendszerbiztonsági tag számára hozzárendelt Azure-szerepkörtől függenek.
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 61bcf7abca2860078bd89da070309a0057360f0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370223"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-queue-data"></a>PowerShell-parancsok futtatása Azure AD-beli hitelesítő adatokkal a várólista adatainak eléréséhez

Az Azure Storage olyan bővítményeket biztosít a PowerShellhez, amelyek lehetővé teszik a Azure Active Directory-(Azure AD-) hitelesítő adatokkal való bejelentkezést és parancsfájlok futtatását. Ha Azure AD-beli hitelesítő adatokkal jelentkezik be a PowerShellbe, egy OAuth 2,0 hozzáférési tokent ad vissza. A PowerShell automatikusan használja ezt a tokent, hogy engedélyezze a további adatműveleteket Queue Storage. A támogatott műveletek esetében már nem kell átadnia egy fiók kulcsát vagy SAS-jogkivonatát a paranccsal.

Engedélyeket rendelhet az Azure AD rendszerbiztonsági tag számára az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával az üzenetsor-adathoz. Az Azure Storage-beli Azure-szerepkörökkel kapcsolatos további információkért lásd: [hozzáférési jogosultságok kezelése az Azure Storage-adatokhoz az Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

## <a name="supported-operations"></a>Támogatott műveletek

Az Azure Storage-bővítmények az üzenetsor-adatokon végrehajtott műveletek esetében támogatottak. A felhívható műveletek az Azure AD rendszerbiztonsági tag által a PowerShellbe való bejelentkezéshez megadott engedélyektől függenek. A várólisták engedélyei az Azure RBAC keresztül rendelhetők hozzá. Ha például hozzá lett rendelve a **várólista-Adatolvasó** szerepkörhöz, futtathat parancsfájl-parancsokat, amelyek egy várólistáról olvasnak be egy adatforrást. Ha hozzá lett rendelve a **várólista-adatközreműködői** szerepkörhöz, futtathat parancsfájl-parancsokat, amelyek egy várólistát vagy az általa tárolt adat olvasását, írását vagy törlését írják le, írhatják vagy törölhetik.

További információ az egyes Azure Storage-műveletekhez szükséges engedélyekről a várólistán: [tárolási műveletek hívása OAuth-jogkivonatokkal](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).

> [!IMPORTANT]
> Ha egy Storage-fiók zárolva van egy Azure Resource Manager **írásvédett** zárolással, a [kulcsok listázása](/rest/api/storagerp/storageaccounts/listkeys) művelet nem engedélyezett ehhez a Storage-fiókhoz. A **kulcsok listázása** post művelet, és az összes post művelet meg lesz akadályozva, ha **írásvédett** zárolás van konfigurálva a fiókhoz. Emiatt, ha a fiók **írásvédett** zárolással van zárolva, a fiók kulcsaival nem rendelkező felhasználóknak az Azure ad hitelesítő adatait kell használniuk a várólista adatainak eléréséhez. A PowerShellben adja meg a `-UseConnectedAccount` paramétert, és hozzon létre egy **AzureStorageContext** -OBJEKTUMOT az Azure ad-beli hitelesítő adataival.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>PowerShell-parancsok meghívása az Azure AD hitelesítő adataival

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha a Azure PowerShell használatával szeretne bejelentkezni, és az Azure Storage-ban az Azure Storage-ban további műveleteket hajt végre, hozzon létre egy tárolási környezetet a Storage-fiókra való hivatkozáshoz, és adja meg a `-UseConnectedAccount` paramétert.

Az alábbi példa azt szemlélteti, hogyan hozható létre üzenetsor egy új Storage-fiókban Azure PowerShell Azure AD-beli hitelesítő adataival. Ne felejtse el lecserélni a helyőrző értékeket a saját értékeire a szögletes zárójelekben:

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

1. A várólista létrehozása előtt rendelje hozzá a [tárolási üzenetsor adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) szerepkörét. Annak ellenére, hogy Ön a fiók tulajdonosa, explicit engedélyekkel kell rendelkeznie az adatműveletek elvégzéséhez a Storage-fiókon. Az Azure-szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [a Azure Portal használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adatokhoz való hozzáféréshez](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Az Azure-beli szerepkör-hozzárendelések eltartása néhány percet is igénybe vehet.

1. Hozzon létre egy várólistát a [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue)hívásával. Mivel ez a hívás az előző lépésekben létrehozott környezetet használja, a várólista az Azure AD-beli hitelesítő adataival jön létre.

    ```powershell
    $queueName = "sample-queue"
    New-AzStorageQueue -Name $queueName -Context $ctx
    ```

## <a name="next-steps"></a>Következő lépések

- [A PowerShell használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adateléréshez](../common/storage-auth-aad-rbac-powershell.md)
- [Hozzáférés engedélyezése a blob-és üzenetsor-szolgáltatásokhoz az Azure-erőforrások felügyelt identitásával](../common/storage-auth-aad-msi.md)
