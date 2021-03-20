---
title: 'Oktatóanyag: az Azure Storage elérése SAS-hitelesítő adatokkal – Linux – Azure AD'
description: Oktatóanyag, amely bemutatja, hogyan használhatja a Linux virtuális gép rendszer által hozzárendelt felügyelt identitását az Azure Storage elérésére a tárelérési kulcs helyett SAS-hitelesítő adatok használatával.
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
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3edc63a1532bb6889fc490e400dbb57e7bce10d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360411"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-identity-to-access-azure-storage-via-a-sas-credential"></a>Oktatóanyag: Az Azure Storage elérése SAS-hitelesítő adatok használatával Linux VM-beli rendszer által hozzárendelt identitással

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan használható egy rendszer által hozzárendelt felügyelt identitás Linux rendszerű virtuális gépen (VM) a közös hozzáférésű jogosultságkódú (SAS-) hitelesítő adatok lekérésére. Kifejezetten [szolgáltatási SAS-hitelesítő adatok](../../storage/common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-shared-access-signatures) lekérésére. 

> [!NOTE]
> Az oktatóanyagban létrehozott SAS-kulcs nem lesz korlátozva/kötve a virtuális géphez.  

A szolgáltatási SAS segítségével korlátozott hozzáférési lehetőséget biztosíthat a tárfiókjában található objektumokhoz, korlátozott időre és egy adott szolgáltatáshoz (esetünkben egy Blob szolgáltatáshoz) anélkül, hogy ki kellene adnia a tárfiók hozzáférési kulcsát. A SAS-hitelesítő adatokat a szokásos módon használhatja a tárolási műveletek során, például a Storage SDK használata esetén. Ebben az oktatóanyagban a blobok az Azure Storage CLI használatával való fel- és letöltését mutatjuk be. Az alábbiakat fogja elsajátítani:


> [!div class="checklist"]
> * Tárfiók létrehozása
> * Blobtároló létrehozása a tárfiókban
> * Hozzáférés engedélyezése virtuális gép számára a tárfiók a Resource Managerben lévő SAS-adataihoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a SAS-adatok lekérése a Resource Managerből annak használatával 

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ha még nem rendelkezik tárfiókkal, most létrehoz egyet.  Ki is hagyhatja ezt a lépést, és a VM rendszer által hozzárendelt felügyelt identitása számára biztosíthat hozzáférést egy meglévő tárfiók kulcsaihoz. 

1. Kattintson az Azure Portal bal felső sarkában található **+/Új szolgáltatás létrehozása** gombra.
2. Kattintson a **Tárolás**, majd a **Tárfiók** elemre, amit követően megjelenik egy új „Tárfiók létrehozása” panel.
3. Adjon meg egy nevet a tárfiók számára a **Név** mezőben, amelyet később használ majd.  
4. Az **Üzemi modell** mezőben a „Resource Manager”, a **Fióktípus** mezőben az „Általános célú” értéket kell megadni. 
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

Az Azure Storage nem támogatja natív módon az Azure AD-hitelesítést.  A VM rendszer által hozzárendelt felügyelt identitása használatával azonban lekérheti egy tároló SAS-adatait a Resource Managerből, majd a SAS-adatokkal elérheti a tárolót.  Ebben a lépésben hozzáférést biztosít a virtuális gép rendszer által hozzárendelt felügyelt identitása számára a tárfiók SAS-adataihoz.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre a lap tetején egy új szerepkör-hozzárendelés hozzáadásához a virtuális géphez
4. A lap jobb oldalán a **Szerepkör** értékeként adja meg a „Tárfiók-közreműködő” értéket. 
5. A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a „Virtuális gép” értéket.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a „Minden erőforráscsoport” értékre.  
7. Végül a **Kiválasztás** mezőben válassza ki a Linux rendszerű virtuális gépet a legördülő menüben, majd kattintson a **Mentés** gombra.  

    ![Helyettesítő képszöveg](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role-sas.png)

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-azure-resource-manager"></a>Hozzáférési jogkivonat lekérése a VM identitásával, majd az Azure Resource Manager meghívása a használatával

Az oktatóanyag további részében a korábban létrehozott virtuális gépről dolgozunk.

A lépések elvégzéséhez szüksége lesz egy SSH-ügyfélre. Windows használata esetén használhatja a [Linux Windows alrendszerében](/windows/wsl/install-win10) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](../../virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. Az Azure Portalon lépjen a **Virtuális gépek** felületre, keresse meg a Linux virtuális gépet, majd az **Áttekintés** lap tetején kattintson a **Csatlakozás** gombra. Másolja ki sztringet a virtuális géphez való csatlakozáshoz. 
2. Csatlakozzon a virtuális géphez az SSH-ügyfél használatával.  
3. Ezután meg kell adnia majd a **Linux VM** létrehozásakor hozzáadott **Jelszót**. Ezzel sikeresen be kell tudnia jelentkezni.  
4. A CURL használatával kérjen le egy hozzáférési jogkivonatot az Azure Resource Manager számára.  

    A hozzáférési jogkivonatra vonatkozó CURL-kérelmet és -választ alább láthatja:
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true    
    ```
    
    > [!NOTE]
    > Az előző kérésben a „resource” (erőforrás) paraméter értékének pontosan egyeznie kell az Azure AD által várt értékkel. Az Azure Resource Manager erőforrás-azonosítójának használatakor a záró perjelet is szerepeltetni kell az URI-ban.
    > A következő válaszban az access_token elemet a rövidség kedvéért lerövidíti.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```

## <a name="get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls"></a>SAS-hitelesítő adatok lekérése az Azure Resource Managerből tárolóhívások indításához

Most a CURL használatával hívja meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonattal egy tároló SAS-hitelesítő adatainak létrehozásához. Amint rendelkezésre állnak a SAS-hitelesítő adatok, meg lehet hívni a tárfeltöltési/-letöltési műveleteket.

Ehhez a kéréshez az alábbi HTTP-kérésparamétereket használja majd a SAS-hitelesítő adatok létrehozásához:

```JSON
{
    "canonicalizedResource":"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>",
    "signedResource":"c",              // The kind of resource accessible with the SAS, in this case a container (c).
    "signedPermission":"rcw",          // Permissions for this SAS, in this case (r)ead, (c)reate, and (w)rite.  Order is important.
    "signedProtocol":"https",          // Require the SAS be used on https protocol.
    "signedExpiry":"<EXPIRATION TIME>" // UTC expiration time for SAS in ISO 8601 format, for example 2017-09-22T00:06:00Z.
}
```

Ezek a paraméterek a SAS-hitelesítő adatokra vonatkozó kérés POST-törzsében találhatók. A SAS-hitelesítő adatok létrehozására vonatkozó paraméterekkel kapcsolatos további információkat [a szolgáltatási SAS REST referenciában](/rest/api/storagerp/storageaccounts/listservicesas) találja.

A SAS-hitelesítő adatok lekéréséhez használja a következő CURL-kérést. A `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, `<STORAGE ACCOUNT NAME>`, `<CONTAINER NAME>` és `<EXPIRATION TIME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. Az `<ACCESS TOKEN>` értéket cserélje le a korábban lekért hozzáférési jogkivonattal:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listServiceSas/?api-version=2017-06-01 -X POST -d "{\"canonicalizedResource\":\"/blob/<STORAGE ACCOUNT NAME>/<CONTAINER NAME>\",\"signedResource\":\"c\",\"signedPermission\":\"rcw\",\"signedProtocol\":\"https\",\"signedExpiry\":\"<EXPIRATION TIME>\"}" -H "Authorization: Bearer <ACCESS TOKEN>"
```

> [!NOTE]
> Az előző URL-cím szövege megkülönbözteti a kis- és nagybetűket, ezért az erőforráscsoportok esetében is különböztesse meg ezeket a megfelelő hivatkozás érdekében. Ezenkívül fontos észben tartani, hogy ez egy POST és nem egy GET kérés.

A CURL-válasz adja vissza a SAS-hitelesítő adatot:  

```bash 
{"serviceSasToken":"sv=2015-04-05&sr=c&spr=https&st=2017-09-22T00%3A10%3A00Z&se=2017-09-22T02%3A00%3A00Z&sp=rcw&sig=QcVwljccgWcNMbe9roAJbD8J5oEkYoq%2F0cUPlgriBn0%3D"} 
```

Hozzon létre egy példablobfájlt, amelyet feltölthet a Blob Storage-tárolóba. Linux virtuális gépen azt a következő paranccsal teheti meg. 

```bash
echo "This is a test file." > test.txt
```

Ezután hitelesítsen az `az storage` CLI-paranccsal a SAS-hitelesítő adatok használatával, és töltse fel a fájlt a blobtárolóba. Ehhez a lépéshez [telepítenie kell az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) a virtuális gépen, ha eddig még nem tette volna meg.

```azurecli
 az storage blob upload --container-name 
                        --file 
                        --name
                        --account-name 
                        --sas-token
```

Válasz: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-21T03:58:56+00:00"
}
```

Emellett a fájlt letöltheti az Azure CLI használatával is. A hitelesítéshez a SAS-hitelesítő adatokat használja. 

Kérés: 

```azurecli
az storage blob download --container-name
                         --file 
                         --name 
                         --account-name
                         --sas-token
```

Válasz: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "testblob",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 16,
    "contentRange": "bytes 0-15/16",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "Aryr///Rb+D8JQ8IytleDA==",
      "contentType": "text/plain"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D4F9929765C139\"",
    "lastModified": "2017-09-21T03:58:56+00:00",
    "lease": {
      "duration": null,
      "state": "available",
      "status": "unlocked"
    },
    "pageBlobSequenceNumber": null,
    "serverEncrypted": false
  },
  "snapshot": null
}
```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan használhat Linux VM-beli, rendszer által hozzárendelt felügyelt identitást az Azure Storage SAS-hitelesítő adatok használatával való eléréséhez.  További információ az Azure Storage SAS-hitelesítéséről:

> [!div class="nextstepaction"]
>[Közös hozzáférésű jogosultságkódok (SAS) használata](../../storage/common/storage-sas-overview.md)
