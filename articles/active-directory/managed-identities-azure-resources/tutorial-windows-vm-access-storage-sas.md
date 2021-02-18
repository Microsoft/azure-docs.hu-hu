---
title: 'Oktatóanyag: `:` felügyelt identitás használata az Azure Storage sas-hitelesítő adatokkal való eléréséhez – Azure ad'
description: Egy oktatóanyag, amely bemutatja, hogyan használható a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás az Azure Storage eléréséhez, a Storage-fiók elérési kulcsa helyett SAS hitelesítő adatok használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45b4a6f7915f931e2eff24b56b178957a039e1ff
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096586"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage-via-a-sas-credential"></a>Oktatóanyag: a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használata az Azure Storage SAS-hitelesítő adatokkal való eléréséhez

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ebből az oktatóanyagból megtudhatja, hogyan használhatja a rendszerhez rendelt identitást egy Windowsos virtuális géphez (VM) a Storage Shared Access Signature (SAS) hitelesítő adatainak beszerzéséhez. Kifejezetten [szolgáltatási SAS-hitelesítő adatok](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures) lekérésére. 

A Service SAS lehetővé teszi, hogy a fiók hozzáférési kulcsa nélkül korlátozott ideig és egy adott szolgáltatásban (a mi esetünkben a blob szolgáltatásban) engedélyezze a hozzáférést a Storage-fiók objektumaihoz. A SAS-hitelesítő adatokat a szokásos módon használhatja a tárolási műveletek során, például a Storage SDK használata esetén. Ebben az oktatóanyagban egy blob feltöltését és letöltését mutatjuk be az Azure Storage PowerShell használatával. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Hozzáférés engedélyezése virtuális gép számára a tárfiók a Resource Managerben lévő SAS-adataihoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a SAS-adatok lekérése a Resource Managerből annak használatával 

## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások ismerete. Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Egy Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A "tulajdonos" engedély a megfelelő hatókörben (előfizetése vagy erőforráscsoport) a szükséges erőforrás-létrehozási és szerepkör-felügyeleti lépések végrehajtásához. Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg az Azure [-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md)című témakört
- Szükség van egy Windows rendszerű virtuális gépre is, amelyhez engedélyezve van a rendszerhez rendelt felügyelt identitások.
  - Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse a [virtuális gép létrehozása rendszer által hozzárendelt identitással](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) című cikket.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik tárfiókkal, most létrehoz egyet. Kihagyhatja ezt a lépést, és megadhatja a virtuális gép rendszer által hozzárendelt felügyelt identitási hozzáférését egy meglévő Storage-fiók SAS hitelesítő adataihoz. 

1. Kattintson az Azure Portal bal felső sarkában található **+/Új szolgáltatás létrehozása** gombra.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre, amit követően megjelenik egy új „Tárfiók létrehozása” panel.
3. Nevezze el a tárfiókot, amelyet később fog használni.  
4. Az **üzembe helyezési modellt** és a **fiók típusát** "Resource Manager" és "általános célú" értékre kell beállítani. 
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.
6. Kattintson a **Létrehozás** lehetőségre.

    ![Új tárfiók létrehozása](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-in-the-storage-account"></a>Blobtároló létrehozása a tárfiókban

Később feltöltünk egy fájlt az új tárfiókba, majd letöltjük abból. Mivel a fájlok tárolásához blobtároló szükséges, létre kell hoznunk egyet, amelyben tárolhatjuk a fájlt.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **Tárolók** hivatkozásra bal oldalon található „Blob szolgáltatás” területen.
3. Kattintson a **+ Tároló** gombra a lap tetején, amit követően becsúszik az „Új tároló” panel.
4. Nevezze el a tárolót, válasszon ki egy hozzáférési szintet, majd kattintson az **OK** gombra. A megadott névre az oktatóanyag későbbi részében lesz majd szükség. 

    ![Storage-tároló létrehozása](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-a-storage-sas"></a>Hozzáférés engedélyezése virtuális gép rendszer által hozzárendelt felügyelt identitása számára a tároló SAS-adatainak használatához 

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítést.  A felügyelt identitással azonban lekérhet egy Storage SAS-t a Resource Managerből, majd a SAS használatával elérheti a tárolót.  Ebben a lépésben hozzáférést biztosít a virtuális gép rendszer által hozzárendelt felügyelt identitása számára a tárfiók SAS-adataihoz.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.   
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre a lap tetején egy új szerepkör-hozzárendelés hozzáadásához a virtuális géphez
4. A lap jobb oldalán a **Szerepkör** értékeként adja meg a „Tárfiók-közreműködő” értéket.  
5. A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a „Virtuális gép” értéket.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a „Minden erőforráscsoport” értékre.  
7. Végül a **Kiválasztás** mezőben válassza ki a Windows rendszerű virtuális gépet a legördülő menüben, majd kattintson a **Mentés** gombra. 

    ![Helyettesítő képszöveg](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával 

Az oktatóanyag hátralévő részében a virtuális gépről fogunk dolgozni.

Ebben a részben az Azure Resource Manager PowerShell-parancsmagokat kell használnia.  Ha nincs telepítve, a folytatás előtt [töltse le a legújabb verziót](/powershell/azure/).

1. Az Azure Portalon lépjen a **Virtuális gépek** lapra, keresse meg a Windows rendszerű virtuális gépet, majd kattintson az **Áttekintés** lap tetején található **Csatlakozás** gombra.
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **Távoli asztali kapcsolatt** a virtuális géppel.
4. Nyissa meg a PowerShellt a távoli munkamenetben, és a Invoke-WebRequest használatával szerezzen be egy Azure Resource Manager tokent a helyi felügyelt identitásból az Azure-erőforrások végpontja számára.

    ```powershell
       $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
    ```
    
    > [!NOTE]
    > A „resource” paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    
    Ezután nyerje ki a „Content” elemet, amelyet egy JavaScript Object Notation (JSON) formátumú sztringként tárol a $response objektum. 
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```
    Ezután nyerje ki a hozzáférési jogkivonatot a válaszból.
    
    ```powershell
    $ArmToken = $content.access_token
    ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS-hitelesítő adatok lekérése az Azure Resource Managerből tárolóhívások indításához 

Most a PowerShell segítségével hívja meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonat használatával a Storage SAS hitelesítő adatainak létrehozásához. A SAS hitelesítő adataival meghívhatjuk a tárolási műveleteket.

Ehhez a kéréshez az alábbi HTTP-kérésparamétereket használja majd a SAS-hitelesítő adatok létrehozásához:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite. Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezek a paraméterek a SAS-hitelesítő adatokra vonatkozó kérés POST-törzsében találhatók. A SAS-hitelesítő adatok létrehozására vonatkozó paraméterekkel kapcsolatos további információkat [a szolgáltatási SAS REST referenciában](/rest/api/storagerp/storageaccounts/listservicesas) találja.

Először alakítsa át a paramétereket a JSON-be, majd hívja `listServiceSas` meg a tárolási végpontot az SAS hitelesítő adatainak létrehozásához:

```powershell
$params = @{canonicalizedResource="/blob/<STORAGE-ACCOUNT-NAME>/<CONTAINER-NAME>";signedResource="c";signedPermission="rcw";signedProtocol="https";signedExpiry="2017-09-23T00:00:00Z"}
$jsonParams = $params | ConvertTo-Json
```

```powershell
$sasResponse = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE-ACCOUNT-NAME>/listServiceSas/?api-version=2017-06-01 -Method POST -Body $jsonParams -Headers @{Authorization="Bearer $ArmToken"}
```
> [!NOTE] 
> Az URL megkülönbözteti a kis- és nagybetűket, ezért ellenőrizze, hogy pontosan ugyanúgy írja be a kis- és nagybetűket, mint az erőforráscsoport elnevezésekor, beleértve a „resourceGroups” kifejezés nagy „G” betűjét is. 

Most kinyerhetjük a SAS hitelesítő adatait a válaszból:

```powershell
$sasContent = $sasResponse.Content | ConvertFrom-Json
$sasCred = $sasContent.serviceSasToken
```

Ha megvizsgálja az SAS-beli cred-t, a következőhöz hasonló lesz:

```powershell
PS C:\> $sasCred
sv=2015-04-05&sr=c&spr=https&se=2017-09-23T00%3A00%3A00Z&sp=rcw&sig=JVhIWG48nmxqhTIuN0uiFBppdzhwHdehdYan1W%2F4O0E%3D
```

Ezután létrehozunk egy „test.txt” nevű fájlt. Ezután használja az SAS hitelesítő adatait a parancsmaggal való hitelesítéshez `New-AzStorageContent` , töltse fel a fájlt a blob-tárolóba, majd töltse le a fájlt.

```bash
echo "This is a test text file." > test.txt
```

Először az Azure Storage-parancsmagokat telepítse az `Install-Module Azure.Storage` használatával. Ezután töltse fel a most létrehozott blobot a `Set-AzStorageBlobContent` PowerShell-parancsmaggal:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <STORAGE-ACCOUNT-NAME> -SasToken $sasCred
Set-AzStorageBlobContent -File test.txt -Container <CONTAINER-NAME> -Blob testblob -Context $ctx
```

Válasz:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

Le is töltheti a most feltöltött blobot a `Get-AzStorageBlobContent` PowerShell-parancsmaggal:

```powershell
Get-AzStorageBlobContent -Blob testblob -Container <CONTAINER-NAME> -Destination test2.txt -Context $ctx
```

Válasz:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 56
ContentType       : application/octet-stream
LastModified      : 9/21/2017 6:14:25 PM +00:00
SnapshotTime      :
ContinuationToken :
Context           : Microsoft.WindowsAzure.Commands.Storage.AzureStorageContext
Name              : testblob
```

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan használhatja a Windows rendszerű virtuális gépekhez rendelt felügyelt identitást az Azure Storage SAS-hitelesítő adatokkal való eléréséhez.  További információ az Azure Storage SAS-hitelesítéséről:

> [!div class="nextstepaction"]
>[Közös hozzáférésű jogosultságkódok (SAS) használata](../../storage/common/storage-sas-overview.md)