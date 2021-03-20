---
title: 'Oktatóanyag: `:` felügyelt identitás használata az Azure Storage – Linux – Azure ad eléréséhez'
description: Az oktatóanyag azt ismerteti, hogyan férhet hozzá az Azure Storage-hoz egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c7612188043be070ead92c88838b567b22787d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98131270"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Oktatóanyag: Hozzáférés az Azure Storage-hoz egy Linux VM-beli, rendszer által hozzárendelt felügyelt identitással 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá az Azure Storage-hoz egy Linux rendszerű virtuális gép (VM) rendszer által hozzárendelt felügyelt identitásával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Tárfiók létrehozása
> * Blobtároló létrehozása egy tárfiókban
> * Hozzáférés engedélyezése a Linux rendszerű virtuális gép felügyelt identitása számára egy Azure Storage-tárolóhoz
> * Hozzáférési jogkivonat lekérése, majd azzal az Azure Storage meghívása

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

A jelen oktatóanyagban a parancssori példaszkriptek futtatása kétféleképpen végezhető el:

- Az [Azure Cloud Shell](~/articles/cloud-shell/overview.md) használatával az Azure Portalon vagy a minden egyes kódblokk jobb felső sarkában megtalálható **Kipróbálom** gombbal.
- Ha inkább a helyi parancssori konzolt kívánja használni, [telepítse a CLI 2.0 legújabb verzióját](/cli/azure/install-azure-cli) (2.0.23 vagy újabb).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása 

Ebben a szakaszban egy új tárfiókot fog létrehozni. 

1. Kattintson az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** gombra.
2. Kattintson a **Storage**, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőségre.
3. A **Név** mezőben adja meg a tárfiók nevét.  
4. A **Telepítési modell** mezőben a **Resource Manager**, a **Fiók típusa** mezőben a **Storage (általános célú v1)** beállítást kell megadni. 
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.
6. Kattintson a **Létrehozás** lehetőségre.

    ![Új tárfiók létrehozása](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Blobtároló létrehozása és egy fájl feltöltése a tárfiókba

A fájlok tárolásához blobtároló szükséges, ezért létre kell hoznia egyet, amelyben a fájlt tárolhatja. Ezután fel fog tölteni egy fájlt az új tárfiókon lévő blobtárolóba.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. A **blob szolgáltatás** alatt kattintson a **tárolók** elemre.
3. Kattintson a **+ Tároló** gombra a lap tetején.
4. Az **Új tároló** területen írja be a tároló nevét, és a **Nyilvános hozzáférés szintje** területen tartsa meg az alapértelmezett értéket.

    ![Storage-tároló létrehozása](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Egy tetszőleges szövegszerkesztővel hozzon létre egy *hello world.txt* nevű fájlt a helyi gépen.  Nyissa meg a fájlt, adja hozzá a „Hello, world! :)” szöveget (idézőjel nélkül), majd mentse el. 

6. A tároló nevére, majd a **Feltöltés** lehetőségre kattintva töltse fel a fájlt az újonnan létrehozott tárolóba.
7. A **Blob feltöltése** panel **Fájlok** területén kattintson a mappa ikonra, keresse meg a **hello_world.txt** fájlt a helyi gépen, válassza ki a fájlt, majd kattintson a **Feltöltés** gombra.

    ![Szövegfájl feltöltése](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Hozzáférés engedélyezése a virtuális gép számára egy Azure Storage-tárolóhoz 

A VM felügyelt identitásával lekérheti az Azure-tárolóblob adatait.

>[!NOTE]
> További információ a tárolók engedélyeinek megadásához használható különböző szerepkörökről: a [blobok és a várólisták hozzáférésének engedélyezése a Azure Active Directory használatával](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)

1. Lépjen vissza az újonnan létrehozott tárfiókra.  
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.  
3. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre a lap tetején egy új szerepkör-hozzárendelés hozzáadásához a virtuális géphez.
4. A **szerepkör** alatt a legördülő listából válassza a **Storage blob-Adatolvasó** lehetőséget. 
5. A következő legördülő menüben, a **Hozzáférés hozzárendelése** területen válassza ki a **Virtuális gép** elemet.  
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a **Minden erőforráscsoport** értékre.  
7. A **Kiválasztás** mezőben válassza ki a virtuális gépet, majd kattintson a **Mentés** gombra.

    ![Engedélyek hozzárendelése](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Hozzáférési jogkivonat lekérése, majd azzal az Azure Storage meghívása

Az Azure Storage natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja a felügyelt identitás használatával beszerzett hozzáférési jogkivonatokat. Ez az Azure Storage és az Azure AD integrációjának része, és eltér attól a megoldástól, amikor a kapcsolati sztringen adja meg a hitelesítő adatokat.

Az alábbi lépések elvégzéséhez a korábban létrehozott virtuális gépről kell dolgoznia, az ahhoz való csatlakozáshoz pedig egy SSH-ügyfélre van szüksége. Windows használata esetén használhatja a [Linux Windows alrendszerében](/windows/wsl/about) elérhető SSH-ügyfelet. Amennyiben segítségre van szüksége az SSH-ügyfél kulcsának konfigurálásához, [Az SSH-kulcsok és a Windows együttes használata az Azure-ban](~/articles/virtual-machines/linux/ssh-from-windows.md) vagy [Nyilvános és titkos SSH-kulcspár létrehozása és használata az Azure-ban Linux rendszerű virtuális gépekhez](~/articles/virtual-machines/linux/mac-create-ssh-keys.md) című cikkekben talál további információt.

1. Az Azure Portalon lépjen a **Virtuális gépek** területre, keresse meg a Linux rendszerű virtuális gépét, majd az **Áttekintés** lapon kattintson a **Csatlakozás** gombra. Másolja ki sztringet a virtuális géphez való csatlakozáshoz.
2. **Kapcsolódjon** a virtuális géphez az Ön által választott SSH-ügyféllel. 
3. A terminálablakban a CURL használatával küldjön kérést a helyi felügyeltidentitás-végpontra egy hozzáférési jogkivonat lekérésére az Azure Resource Managerhez.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. A hozzáférési jogkivonat segítségével most már elérheti az Azure Storage-et, például a korábban a tárolóba feltöltött mintafájl tartalmának olvasásához. Cserélje le a `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` és `<FILE NAME>` értékeit a korábban megadott értékekre, az `<ACCESS TOKEN>` értékét pedig az előző lépésben visszaadott jogkivonatra.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   A válasz tartalmazza a fájl tartalmát:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan gondoskodhat róla, hogy egy Linux rendszerű virtuális gép rendszer által hozzárendelt felügyelt identitása hozzá tudjon férni az Azure Storage-hoz.  További információ az Azure Storage-ról:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)
