---
title: Azure Storage Explorer használata a Azure Data Lake Storage Gen2
description: A Azure Storage Explorer segítségével kezelheti a könyvtárakat és a fájl-és címtár-hozzáférés-vezérlési listákat (ACL) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5ccef241a37e63467b681d5fd12c65072cb92e58
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626463"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a> kezelése az Azure Storage Explorerrel az Azure Data Lake Storage Gen2-ben

Ez a cikk bemutatja, hogyan hozhat létre és kezelhet olyan könyvtárakat, fájlokat és hozzáférés-vezérlési listákat (ACL-eket) a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával, amelyeken engedélyezve van a hierarchikus NÉVTÉR (HNS).

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](../common/storage-account-create.md) útmutatást követve hozzon létre egyet.

- Azure Storage Explorer telepítve a helyi számítógépen. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Bejelentkezés Storage Explorer

A Storage Explorer első indításakor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. Míg Storage Explorer számos módszert biztosít a Storage-fiókokhoz való kapcsolódáshoz, a hozzáférés-vezérlési listák kezelése jelenleg csak egyetlen módon támogatott.

|Feladat|Cél|
|---|---|
|Azure-fiók hozzáadása | Átirányítja a szervezet bejelentkezési lapjára az Azure-ba való hitelesítéshez. Jelenleg ez az egyetlen támogatott hitelesítési módszer, ha az ACL-eket szeretné kezelni és beállítani.|
|Kapcsolati sztring vagy közös hozzáférésű jogosultságkód URI azonosítójának használata | Egy tároló vagy tárfiók közvetlen elérésére szolgál egy SAS-token vagy egy közös kapcsolati sztring segítségével. |
|Tárfióknév és -kulcs használata| Csatlakozzon az Azure Storage-hoz a tárfiók nevével és kulcsával.|

Válassza **Az Azure-fiók hozzáadása** lehetőséget, és kattintson **a bejelentkezés..**. elemre. A képernyőn megjelenő utasításokat követve jelentkezzen be az Azure-fiókjába.

![A Microsoft Azure Storage Explorer megjelenítő képernyőkép, amely kiemeli az Azure-fiók hozzáadása lehetőséget és a bejelentkezés gombot.](media/storage-quickstart-blobs-storage-explorer/connect.png)

A csatlakozás befejeztével az Azure Storage Explorer betöltést követően megjeleníti az **Explorer** lapot. Ez a nézet betekintést nyújt az összes Azure Storage-fiókba, valamint a [Azurite-tároló emulátoron](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos db](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) fiókokon vagy [Azure stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) környezeteken keresztül konfigurált helyi tárhelyre.

![Microsoft Azure Storage Explorer – Csatlakozás ablak](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Tároló létrehozása

A tárolók könyvtárakat és fájlokat tárolnak. Ha létre szeretne hozni egyet, bontsa ki a folytatás lépésben létrehozott Storage-fiókot. Válassza a **blob-tárolók** lehetőséget, kattintson a jobb gombbal, majd válassza a **blob-tároló létrehozása** lehetőséget. Adja meg a tároló nevét. A tárolók elnevezésére vonatkozó szabályok és korlátozások listáját lásd: [Container létrehozása](storage-quickstart-blobs-dotnet.md#create-a-container) szakasz. Ha elkészült, nyomja le az **ENTER** billentyűt a tároló létrehozásához. Miután a tároló sikeresen létrejött, a kiválasztott Storage-fiók **blob containers** mappájában jelenik meg.

![Microsoft Azure Storage Explorer-tároló létrehozása](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Könyvtár létrehozása

Könyvtár létrehozásához válassza ki a folytatás lépésben létrehozott tárolót. A tároló menüszalagon kattintson az **új mappa** gombra. Adja meg a címtár nevét. Ha elkészült, nyomja le az **ENTER** billentyűt a könyvtár létrehozásához. Miután a címtár sikeresen létrejött, megjelenik a szerkesztő ablakban.

![Microsoft Azure Storage Explorer – könyvtár létrehozása](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blobok feltöltése a könyvtárba

A címtár menüszalagján kattintson a **feltöltés** gombra. Ez a művelet lehetővé teszi egy mappa vagy fájl feltöltését.

Válassza ki a feltölteni kívánt fájlokat vagy mappát.

![Microsoft Azure Storage Explorer – Blob feltöltése](media/data-lake-storage-explorer/upload-file.png)

Ha az **OK** gombra kattint, a kiválasztott fájlokat a rendszer sorba állítja a feltöltéshez, majd egyesével feltölti azokat. A feltöltés befejeztével az eredmények megjelennek a **Tevékenységek** ablakban.

## <a name="view-blobs-in-a-directory"></a>Blobok megtekintése egy könyvtárban

A **Azure Storage Explorer** alkalmazásban válasszon ki egy könyvtárat egy Storage-fiókban. A fő ablaktábla a kiválasztott könyvtárban lévő Blobok listáját jeleníti meg.

![Microsoft Azure Storage Explorer – Blobok listázása egy könyvtárban](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blobok letöltése

A fájlok **Azure Storage Explorer** használatával történő letöltéséhez jelölje ki a fájlt, és válassza a **Letöltés** lehetőséget a menüszalagon. Megnyílik egy fájl párbeszédpanel, amelyen megadhat egy fájlnevet. A **Mentés** gombra kattintva elindíthatja a fájl letöltését a helyi helyre.

<a id="managing-access"></a>

## <a name="manage-acls"></a>ACL-ek kezelése

Kattintson a jobb gombbal a tárolóra, egy könyvtárra vagy egy fájlra, majd kattintson a **Access Control-lista kezelése** lehetőségre.  A következő képernyőképen a menü látható, amikor a jobb gombbal egy könyvtárra kattint.

> [!div class="mx-imgBorder"]
> ![Kattintson a jobb gombbal egy könyvtárra Azure Storage Explorer](./media/data-lake-storage-explorer/manage-access-control-list-option.png)

A **hozzáférés kezelése** párbeszédpanel lehetővé teszi a tulajdonosi és a tulajdonosi csoport engedélyeinek kezelését. Azt is lehetővé teszi, hogy új felhasználókat és csoportokat vegyen fel a hozzáférés-vezérlési listára, ahol az engedélyeket kezelheti.

> [!div class="mx-imgBorder"]
> ![Hozzáférés kezelése párbeszédpanel](./media/data-lake-storage-explorer/manage-access-dialog-box.png)

Ha új felhasználót vagy csoportot szeretne hozzáadni a hozzáférés-vezérlési listához, kattintson a **Hozzáadás** gombra. Ezután adja meg a listához hozzáadni kívánt Azure Active Directory-(HRE-) bejegyzést, majd válassza a **Hozzáadás** lehetőséget.  A felhasználó vagy csoport ekkor megjelenik a **felhasználók és csoportok:** mezőben, így megkezdheti az engedélyeik kezelését.

> [!NOTE]
> Ajánlott eljárás, és javasoljuk, hogy hozzon létre egy biztonsági csoportot a HRE-ben, és tartsa meg az engedélyeket a csoporton az egyes felhasználók helyett. A javaslat részletes ismertetését, valamint az egyéb ajánlott eljárásokat lásd: [hozzáférés-vezérlési modell Azure Data Lake Storage Gen2ban](data-lake-storage-access-control-model.md).

A jelölőnégyzet vezérlőelemekkel megadhatja a hozzáférést és az alapértelmezett ACL-eket. További információ az ilyen típusú ACL-ek közötti különbségről: [ACL-típusok](data-lake-storage-access-control.md#types-of-acls).

<a id="apply-acls-recursively"></a>

## <a name="apply-acls-recursively"></a>ACL-ek rekurzív alkalmazása

Az ACL-bejegyzéseket rekurzív módon alkalmazhatja a szülő könyvtár meglévő alárendelt elemeire anélkül, hogy ezeket a módosításokat külön kell elvégeznie az egyes alárendelt elemek esetében.

Az ACL-bejegyzések rekurzív alkalmazásához kattintson a jobb gombbal a tárolóra vagy a könyvtárra, majd kattintson a **terjesztési Access Control listák** elemre.  A következő képernyőképen a menü látható, amikor a jobb gombbal egy könyvtárra kattint.

> [!div class="mx-imgBorder"]
> ![Kattintson a jobb gombbal egy könyvtárra, és válassza a hozzáférés-vezérlés propagálása beállítást.](./media/data-lake-storage-explorer/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg Data Lake Storage Gen2 hozzáférés-vezérlési listájával.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlés a 2. generációs Azure Data Lake Storage-ben](./data-lake-storage-access-control.md)
