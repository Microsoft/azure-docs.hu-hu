---
title: Azure Storage elérése a Windows rendszerű virtuális gépekhez rendelt felügyelt identitás használatával | Microsoft Docs
description: Az oktatóanyag azt ismerteti, hogyan férhet hozzá az Azure Storage-hoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással.
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
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1cc69b3cfdac307edf6dfe999a5d538c2cb811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89263178"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Oktatóanyag: Hozzáférés az Azure Storage-hoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ez az oktatóanyag bemutatja, hogyan férhet hozzá az Azure Storage-hoz egy Windows rendszerű virtuális gép (VM) rendszer által hozzárendelt felügyelt identitásával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Blobtároló létrehozása egy tárfiókban
> * Hozzáférés engedélyezése Windows VM-beli, rendszer által hozzárendelt felügyelt identitás számára egy tárfiókhoz
> * Hozzáférés kérése, majd az Azure Storage meghívása a hozzáféréssel

> [!NOTE]
> Az Azure Storage Azure Active Directory-hitelesítése nyilvános előzetes verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>Engedélyezés

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Hozzáférés biztosítása


### <a name="create-storage-account"></a>Storage-fiók létrehozása

Ebben a szakaszban egy új tárfiókot fog létrehozni.

1. Kattintson az Azure Portal bal felső sarkában található **+ Erőforrás létrehozása** gombra.
2. Kattintson a **Storage**, majd a **Tárfiók – blob, fájl, tábla, üzenetsor** lehetőségre.
3. A **Név** mezőben adja meg a tárfiók nevét.
4. A **Telepítési modell** mezőben a **Resource Manager**, a **Fiók típusa** mezőben a **Storage (általános célú v1)** beállítást kell megadni.
5. Ellenőrizze, hogy az **Előfizetés** és az **Erőforráscsoport** mező értéke egyezik-e az előző lépésben a virtuális gép létrehozása során megadottakkal.
6. Kattintson a **Létrehozás** lehetőségre.

    ![Új tárfiók létrehozása](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Blobtároló létrehozása és egy fájl feltöltése a tárfiókba

A fájlok tárolásához blobtároló szükséges, ezért létre kell hoznia egyet, amelyben a fájlt tárolhatja. Ezután fel fog tölteni egy fájlt az új tárfiókon lévő blobtárolóba.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. A **blob szolgáltatás** alatt kattintson a **tárolók** elemre.
3. Kattintson a **+ Tároló** gombra a lap tetején.
4. Az **Új tároló** területen írja be a tároló nevét, és a **Nyilvános hozzáférés szintje** területen tartsa meg az alapértelmezett értéket.

    ![Storage-tároló létrehozása](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Egy tetszőleges szövegszerkesztővel hozzon létre egy *hello world.txt* nevű fájlt a helyi gépen. Nyissa meg a fájlt, adja hozzá a „Hello, world! :)” szöveget (idézőjel nélkül), majd mentse el.
6. A tároló nevére, majd a **Feltöltés** lehetőségre kattintva töltse fel a fájlt az újonnan létrehozott tárolóba.
7. A **Blob feltöltése** panel **Fájlok** területén kattintson a mappa ikonra, keresse meg a **hello_world.txt** fájlt a helyi gépen, válassza ki a fájlt, majd kattintson a **Feltöltés** gombra.
    ![Szövegfájl feltöltése](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>Hozzáférés biztosítása

Ez a szakasz bemutatja, hogyan biztosítható a virtuális gép hozzáférése egy Azure Storage-tárolóhoz. A VM rendszer által hozzárendelt felügyelt identitásával lekérheti az Azure-tárolóblob adatait.

1. Lépjen vissza az újonnan létrehozott tárfiókra.
2. Kattintson a **Hozzáférés-vezérlés (IAM)** hivatkozásra a bal oldali panelen.
3. Kattintson a **+ szerepkör-hozzárendelés hozzáadása** lehetőségre a lap tetején egy új szerepkör-hozzárendelés hozzáadásához a virtuális géphez.
4. A **szerepkör** alatt a legördülő listából válassza a **Storage blob-Adatolvasó** lehetőséget.
5. A következő legördülő menüben, a **Hozzáférés hozzárendelése** területen válassza ki a **Virtuális gép** elemet.
6. Ezután ellenőrizze, hogy a megfelelő előfizetés szerepel-e az **Előfizetés** legördülő menüben, majd állítsa az **Erőforráscsoport** értékét a **Minden erőforráscsoport** értékre.
7. A **Kiválasztás** mezőben válassza ki a virtuális gépet, majd kattintson a **Mentés** gombra.

    ![Engedélyek hozzárendelése](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>Adatok elérése 

Az Azure Storage natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja a felügyelt identitás használatával beszerzett hozzáférési jogkivonatokat. Ez az Azure Storage és az Azure AD integrációjának része, és eltér attól a megoldástól, amikor a kapcsolati sztringen adja meg a hitelesítő adatokat.

Az alábbi .NET-kód példa az Azure Storage-hoz való kapcsolódás hozzáférési jogkivonat használatával történő megnyitására, majd a korábban létrehozott fájl tartalmának olvasására mutat. Ennek a kódnak kell futnia a virtuális gépen a VM felügyelt identitásához tartozó végpont eléréséhez. A hozzáférési jogkivonat metódusának használatához a .NET-keretrendszer 4,6-es vagy újabb verziója szükséges. Cserélje le az `<URI to blob file>` értékét a megfelelőre. Az érték lekéréséhez keresse meg a létrehozott és a blobtárolóba feltöltött fájlt, és másolja ki az **Áttekintés** oldalon lévő **Tulajdonságok** területen szereplő **URL** értéket.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");

            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);

            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }
    }
}
```

A válasz tartalmazza a fájl tartalmát:

`Hello world! :)`


## <a name="disable"></a>Letiltás

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan gondoskodhat róla, hogy egy Windows rendszerű virtuális gép rendszer által hozzárendelt identitása hozzá tudjon férni az Azure Storage-hoz.  További információ az Azure Storage-ról:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)