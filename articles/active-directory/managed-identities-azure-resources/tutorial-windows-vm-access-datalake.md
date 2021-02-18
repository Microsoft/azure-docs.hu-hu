---
title: Oktatóanyag `:` felügyelt identitás használata Azure Data Lake Store-Windows-Azure ad eléréséhez
description: Ez az oktatóanyag bemutatja, hogyan használhat Windows VM-beli, rendszer által hozzárendelt felügyelt identitást az Azure Data Lake Storage eléréséhez.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093818"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Oktatóanyag: Hozzáférés az Azure Data Lake Storage-hoz egy Windows VM-beli, rendszer által hozzárendelt felügyelt identitással

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az oktatóanyag bemutatja, hogyan használhat rendszer által hozzárendelt felügyelt identitást egy Windows rendszerű virtuális gépen (VM) egy Azure Data Lake Storage eléréséhez. A felügyeltszolgáltatás-identitások kezelését automatikusan az Azure végzi, és lehetővé teszi a hitelesítést az Azure AD-hitelesítést támogató szolgáltatásokban anélkül, hogy be kellene szúrnia a hitelesítő adatokat a kódba. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Hozzáférés engedélyezése a virtuális gép számára egy Azure Data Lake Store-hoz
> * Hozzáférési jogkivonat lekérése a VM identitásával, majd hozzáférés egy Azure Data Lake Store-hoz a jogkivonat segítségével

## <a name="prerequisites"></a>Előfeltételek

- A felügyelt identitások ismerete. Ha még nem ismeri az Azure-erőforrások felügyelt identitására vonatkozó funkciót, tekintse meg ezt az [áttekintést](overview.md). 
- Egy Azure-fiókkal, [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A "tulajdonos" engedély a megfelelő hatókörben (előfizetése vagy erőforráscsoport) a szükséges erőforrás-létrehozási és szerepkör-felügyeleti lépések végrehajtásához. Ha segítségre van szüksége a szerepkör-hozzárendeléssel kapcsolatban, tekintse meg az Azure [-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../../role-based-access-control/role-assignments-portal.md)című témakört
- Szükség van egy Windows rendszerű virtuális gépre is, amelyhez engedélyezve van a rendszerhez rendelt felügyelt identitások.
  - Ha létre kell hoznia egy virtuális gépet ehhez az oktatóanyaghoz, kövesse a [virtuális gép létrehozása rendszer által hozzárendelt identitással](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) című cikket.




## <a name="enable"></a>Engedélyezés

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Hozzáférés biztosítása

Most hozzáférést adhat a virtuális gépnek az Azure Data Lake Store-on lévő fájlokhoz és mappákhoz.  Ehhez a lépéshez meglévő Data Lake Store-t is használhat, de újat is létrehozhat.  Ha új Data Lake Store-t szeretne létrehozni az Azure Portalon, kövesse ennek az [Azure Data Lake Store rövid útmutatónak](../../data-lake-store/data-lake-store-get-started-portal.md) a lépéseit. Az [Azure Data Lake Store dokumentációjában](../../data-lake-store/data-lake-store-overview.md) olyan rövid útmutatók is vannak, amelyek az Azure CLI-t és az Azure PowerShellt használják.

A Data Lake Storage-ban hozzon létre egy új mappát, és adjon engedélyt a VM rendszer által hozzárendelt identitásának a mappában lévő fájlok olvasásához, írásához és futtatásához:

1. Az Azure Portalon kattintson a bal oldali navigációs sávban lévő **Data Lake Store** elemre.
2. Kattintson az oktatóanyaghoz használni kívánt Data Lake Store-ra.
3. Kattintson a parancssávon az **Adatkezelő** lehetőségre.
4. A Data Lake Store gyökérmappája van kiválasztva.  Kattintson a **Hozzáférés** elemre a parancssávon.
5. Kattintson a **Hozzáadás** parancsra.  A **Kiválasztás** mezőbe írja be a virtuális gép nevét (például **DevTestVM**).  Kattintson a virtuális gép kiválasztásához a keresési eredmények közül, majd kattintson a **Kiválasztás** lehetőségre.
6. Kattintson az **Engedélyek kiválasztása** elemre.  Válassza ki az **Olvasás** és a **Végrehajtás** lehetőséget, adja hozzá **ehhez a mappához**, és adja hozzá **csak hozzáférési engedélyként**.  Kattintson az **OK** gombra.  Ezzel elvileg sikeresen hozzáadta az engedélyt.
7. Zárja be a **Hozzáférés** panelt.
8. Ebben az oktatóanyagban egy új mappát hozunk létre.  Kattintson a parancssáv **Új mappa** elemére, és adjon nevet az új mappának (például **TestFolder**).  Kattintson az **OK** gombra.
9. Kattintson a létrehozott mappára, majd kattintson a **Hozzáférés** gombra a parancssávon.
10. Az 5. lépéshez hasonlóan kattintson a **Hozzáadás** lehetőségre, a **Kiválasztás** mezőbe írja be a virtuális gép nevét, válassza ki, és kattintson a **Kiválasztás** elemre.
11. A 6. lépéshez hasonlóan kattintson az **Engedélyek kiválasztása** lehetőségre, válassza az **Olvasás**, **Írás** és **Végrehajtás** elemet, adja hozzá **ehhez a mappához**, és adja hozzá **hozzáférési és alapértelmezett engedély bejegyzéseként**.  Kattintson az **OK** gombra.  Ezzel elvileg sikeresen hozzáadta az engedélyt.

A VM rendszer által hozzárendelt felügyelt identitása mostantól az összes műveletet el tudja végezni a létrehozott mappában lévő fájlokon.  További információkat a Data Lake Store hozzáférésének kezeléséről [a hozzáférés-vezérlés Data Lake Store-ban való használatával kapcsolatos](../../data-lake-store/data-lake-store-access-control.md) cikkben talál.

## <a name="access-data"></a>Adatok elérése

Az Azure Data Lake Storage natív támogatást nyújt az Azure AD-hitelesítésnek, így közvetlenül is elfogadhatja az Azure-erőforrások felügyelt identitásai használatával beszerzett hozzáférési jogkivonatokat.  A Data Lake Store-fájlrendszer hitelesítéséhez az Azure AD által kiadott hozzáférési jogkivonatot kell küldenie a Data Lake Store-fájlrendszer végpontjának egy engedélyeztetési fejlécben, „Bearer <HOZZÁFÉRÉSI_JOGKIVONAT_ÉRTÉKE>” formátumban.  Az Azure AD-hitelesítés Data Lake Store általi támogatásáról további információt a [Data Lake Store-ral és az Azure Active Directoryval való hitelesítést](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md) ismertető cikkben talál.

> [!NOTE]
> A Data Lake Storage-fájlrendszer ügyféloldali SDK-i még nem támogatják az Azure-erőforrások felügyelt identitásait.  Ez az oktatóanyag frissülni fog, mihelyt az SDK általi támogatás kibővül.

Ebben az oktatóanyagban a Data Lake Store-fájlrendszer REST API-jában fog hitelesíteni a PowerShell használatával REST-kérések elindításához. Ha a VM rendszer által hozzárendelt felügyelt identitását hitelesítéshez szeretné használni, a virtuális gépről kell indítania a kéréseket.

1. A portálon lépjen a **Virtuális gépek** lapra, lépjen a Windows VM-hez, és az **Áttekintés** területen kattintson a **Csatlakozás** elemre.
2. A **Felhasználónév** és a **Jelszó** mezőbe azt a felhasználónevet és jelszót írja be, amelyet a Windows VM létrehozásakor adott meg. 
3. Most, hogy létrehozott egy **távoli asztali kapcsolatot** a virtuális géppel, nyissa meg a **PowerShellt** a távoli munkamenetben. 
4. A Powershell `Invoke-WebRequest` parancsával küldjön kérést az Azure-erőforrások helyi felügyeltidentitási végpontjára, hogy lekérjen egy hozzáférési jogkivonatot az Azure Data Lake Storage-hoz.  Data Lake Store erőforrás-azonosítója `https://datalake.azure.net/` .  A Data Lake az erőforrás-azonosítót pontosan egyezteti, így a záró perjel nem hagyható el.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Konvertálja a választ JSON-objektumból PowerShell-objektummá. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Nyerje ki a hozzáférési jogkivonatot a válaszból.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. A PowerShell „Invoke-WebRequest” parancsával intézzen egy kérést a Data Lake Store REST-végpontjához a gyökérmappában lévő mappák listázása érdekében.  Így egyszerűen ellenőrizhető, hogy minden megfelelően van-e konfigurálva.  Fontos, hogy az engedélyeztetési fejléc „Bearer” sztringjében a „B” betű nagy legyen.  A Data Lake Store nevét az Azure Portalon, a Data Lake Store panel **Áttekintés** szakaszában ellenőrizheti.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   A sikeres válasz így néz ki:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Most megpróbálhat feltölteni egy fájlt a Data Lake Store-ba.  Először hozza létre a feltölteni kívánt fájlt.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. A PowerShell `Invoke-WebRequest` parancsával intézzen egy kérést a Data Lake Store REST-végpontjához, hogy feltöltse a fájlt a korábban létrehozott mappába.  Ez a kérés két lépésből áll.  Az első lépésben elindít egy kérést, és a rendszer átirányítja oda, ahová a fájlt fel kell tölteni.  A második lépésben ténylegesen feltölti a fájlt.  Ne feledje megfelelően beállítani a mappa és a fájl nevét, ha az oktatóanyagtól eltérő értékeket használt. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   A `$HdfsRedirectResponse` értékének a következő válaszhoz hasonlóan kell kinéznie:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   A feltöltés befejezéséhez küldjön egy kérést az átirányítási végponthoz:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   A sikeres válasz nagyjából így néz ki:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

A Data Lake Store-fájlrendszer más API-jaival fájlokhoz végezhet hozzáfűzést, fájlokat tölthet le, és egyéb műveleteket is elvégezhet.


## <a name="disable"></a>Letiltás

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Következő lépések

Az oktatóanyag bemutatta, hogyan használhat rendszer által hozzárendelt felügyelt identitást egy Windows rendszerű virtuális gépen egy Azure Data Lake Storage eléréséhez. További információ az Azure Data Lake Store-ról:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)