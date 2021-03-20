---
title: 'Oktatóanyag: `:` felügyelt identitás használata az Azure Storage eléréséhez hozzáférési kulcs – Linux – Azure ad'
description: Egy oktatóanyag, amely végigvezeti egy linuxos virtuálisgép-rendszer által hozzárendelt felügyelt identitás használatának folyamatán az Azure Storage elérési kulcson keresztüli eléréséhez.
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
ms.date: 03/04/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: e99a199fcc5f43f3710fe2e2fcfe55b7e624987b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91317463"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage-via-access-key"></a>Oktatóanyag: Linux VM-beli, rendszer által hozzárendelt felügyelt identitás használata az Azure Storage eléréséhez hozzáférési kulccsal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan kérheti le a tárfiókok hozzáférési kulcsait egy Linux rendszerű virtuális gép (VM) rendszer által hozzárendelt felügyelt identitásának használatával. A tárelérési kulcsokat a szokásos módon használhatja a tárolási műveletek során, például a Storage SDK használata esetén. Ebben az oktatóanyagban blobokat töltünk fel és le az Azure CLI használatával. Az alábbiakat fogja elsajátítani:

> [!div class="checklist"]
> * Hozzáférés biztosítása a VM számára a tárfiók Resource Managerben található hozzáférési kulcsaihoz 
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd a tárelérési kulcsok lekérése a Resource Managerből a használatával  

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

## <a name="grant-your-vms-system-assigned-managed-identity-access-to-use-storage-account-access-keys"></a>Hozzáférés biztosítása a VM rendszer által hozzárendelt felügyelt identitása számára a tárfiók hozzáférési kulcsainak használatához

Ebben a lépésben hozzáférést biztosít a VM rendszer által hozzárendelt felügyelt identitása számára a tárfiók kulcsaihoz.   

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre a lap tetején egy új szerepkör-hozzárendelés hozzáadásához a virtuális géphez
4. A lap jobb oldalán a **Szerepkör** értékeként adja meg a „Storage Account Key Operator Service Role” (Tárfiókkulcs-kezelési szolgáltatás szerepkör) értéket. 
5. A következő legördülő menüben a **Hozzáférés hozzárendelése** beállítás számára válassza ki a „Virtuális gép” értéket.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a „Minden erőforráscsoport” értékre.  
7. Végül a **Kiválasztás** mezőben válassza ki a Linux rendszerű virtuális gépet a legördülő menüben, majd kattintson a **Mentés** gombra. 

    ![Helyettesítő képszöveg](./media/msi-tutorial-linux-vm-access-storage/msi-storage-role.png)

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
    > A következő válaszban az access_token elemet a helytakarékosság miatt rövidítve jelenik meg.
    
    ```bash
    {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://management.azure.com",
    "token_type":"Bearer"} 
     ```
    
## <a name="get-storage-account-access-keys-from-azure-resource-manager-to-make-storage-calls"></a>A tárfiók hozzáférési kulcsainak a lekérése az Azure Resource Managerből tárolóhívások indításához  

Most a CURL használatával hívjuk meg a Resource Managert az előző szakaszban lekért hozzáférési jogkivonattal a tárelérési kulcs lekéréséhez. Amint megkaptuk a tárelérési kulcsot, meghívhatjuk a tárfeltöltési/-letöltési műveleteket. A `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` és `<STORAGE ACCOUNT NAME>` paraméterek értékét mindenképp helyettesítse be a saját értékeivel. Az `<ACCESS TOKEN>` értéket cserélje le a korábban lekért hozzáférési jogkivonattal:

```bash 
curl https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Storage/storageAccounts/<STORAGE ACCOUNT NAME>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Az előző URL-cím szövege megkülönbözteti a kis- és nagybetűket, ezért az erőforráscsoportok esetében is különböztesse meg ezeket a megfelelő hivatkozás érdekében. Ezenkívül fontos észben tartani, hogy ez egy POST és nem egy GET kérés, és a -d paraméterrel átadhat egy értéket a maximális hossz korlátozására (lehet NULL értékű is).  

A CURL-válasz visszaadja a kulcsok listáját:  

```bash 
{"keys":[{"keyName":"key1","permissions":"Full","value":"iqDPNt..."},{"keyName":"key2","permissions":"Full","value":"U+uI0B..."}]} 
```
Hozzon létre egy példablobfájlt, amelyet feltölthet a Blob Storage-tárolóba. Linux virtuális gépen azt a következő paranccsal teheti meg. 

```bash
echo "This is a test file." > test.txt
```

Ezután hitelesítsen az `az storage` CLI-paranccsal a tárelérési kulcs használatával, és töltse fel a fájlt a blobtárolóba. Ehhez a lépéshez [telepítenie kell az Azure CLI legújabb verzióját](/cli/azure/install-azure-cli) a virtuális gépen, ha eddig még nem tette volna meg.
 

```azurecli
az storage blob upload -c <CONTAINER NAME> -n test.txt -f test.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Válasz: 

```JSON
Finished[#############################################################]  100.0000%
{
  "etag": "\"0x8D4F9929765C139\"",
  "lastModified": "2017-09-12T03:58:56+00:00"
}
```

Emellett a fájlt letöltheti az Azure CLI használatával is. A hitelesítéshez használja a tárelérési kulcsot. 

Kérés: 

```azurecli
az storage blob download -c <CONTAINER NAME> -n test.txt -f test-download.txt --account-name <STORAGE ACCOUNT NAME> --account-key <STORAGE ACCOUNT KEY>
```

Válasz: 

```JSON
{
  "content": null,
  "metadata": {},
  "name": "test.txt",
  "properties": {
    "appendBlobCommittedBlockCount": null,
    "blobType": "BlockBlob",
    "contentLength": 21,
    "contentRange": "bytes 0-20/21",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentMd5": "LSghAvpnElYyfUdn7CO8aw==",
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
    "etag": "\"0x8D5067F30D0C283\"",
    "lastModified": "2017-09-28T14:42:49+00:00",
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

Az oktatóanyag bemutatta, hogyan használhat Linux VM-beli, rendszer által hozzárendelt felügyelt identitást az Azure Storage hozzáférési kulcsokkal való eléréséhez.  További információ az Azure Storage tárelérési kulcsairól:

> [!div class="nextstepaction"]
>[A tárelérési kulcsok kezelése](../../storage/common/storage-account-create.md)
