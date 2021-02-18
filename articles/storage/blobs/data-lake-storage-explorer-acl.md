---
title: 'Storage Explorer: ACL-ek beállítása Azure Data Lake Storage Gen2'
description: A Azure Storage Explorer segítségével kezelheti a hozzáférés-vezérlési listákat (ACL-eket) olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus névtér (HNS).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654344"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Az ACL-ek kezelése Azure Storage Explorer használatával Azure Data Lake Storage Gen2

Ez a cikk bemutatja, hogyan használhatók a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) a hozzáférés-vezérlési listák (ACL-ek) kezeléséhez olyan Storage-fiókokban, amelyeken engedélyezve van a hierarchikus NÉVTÉR (HNS).

A Storage Explorer használatával megtekintheti, majd frissítheti a könyvtárak és fájlok ACL-jeit. Az ACL öröklése már elérhető az új alárendelt elemekhez, amelyeket a rendszer a szülő címtárban hozott létre. Az ACL-beállításokat azonban rekurzív módon is alkalmazhatja a szülő könyvtár meglévő alárendelt elemeire anélkül, hogy ezeket a módosításokat külön kell elvégeznie az egyes alárendelt elemek esetében. 

Ebből a cikkből megtudhatja, hogyan módosíthatja a fájl vagy könyvtár hozzáférés-vezérlési listáját, valamint azt, hogyan alkalmazhat ACL-beállításokat a alárendelt címtárakba.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

- Olyan Storage-fiók, amelyen engedélyezve van a hierarchikus névtér (HNS). Az [alábbi](../common/storage-account-create.md) útmutatást követve hozzon létre egyet.

- Azure Storage Explorer telepítve a helyi számítógépen. Az Azure Storage Explorer Windows, Macintosh vagy Linux rendszerre való letöltése: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> A Storage Explorer a blob (blob) & Data Lake Storage Gen2 (DFS) [végpontok](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) használatát is lehetővé teszi a Azure Data Lake Storage Gen2 használatakor. Ha a Azure Data Lake Storage Gen2hoz való hozzáférés privát végpontok használatával van konfigurálva, győződjön meg arról, hogy a Storage-fiókhoz két privát végpont jön létre: egyet a cél alerőforrással, `blob` a másikat pedig a cél alerőforrással `dfs` .

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

## <a name="manage-an-acl"></a>ACL kezelése

Kattintson a jobb gombbal a tárolóra, egy könyvtárra vagy egy fájlra, majd kattintson a **Access Control-lista kezelése** lehetőségre.  A következő képernyőképen a menü látható, amikor a jobb gombbal egy könyvtárra kattint.

> [!div class="mx-imgBorder"]
> ![Kattintson a jobb gombbal egy könyvtárra Azure Storage Explorer](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

A **hozzáférés kezelése** párbeszédpanel lehetővé teszi a tulajdonosi és a tulajdonosi csoport engedélyeinek kezelését. Azt is lehetővé teszi, hogy új felhasználókat és csoportokat vegyen fel a hozzáférés-vezérlési listára, ahol az engedélyeket kezelheti.

> [!div class="mx-imgBorder"]
> ![Hozzáférés kezelése párbeszédpanel](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Ha új felhasználót vagy csoportot szeretne hozzáadni a hozzáférés-vezérlési listához, kattintson a **Hozzáadás** gombra. Ezután adja meg a listához hozzáadni kívánt Azure Active Directory (Azure AD) bejegyzést, majd válassza a **Hozzáadás** lehetőséget.  A felhasználó vagy csoport ekkor megjelenik a **felhasználók és csoportok:** mezőben, így megkezdheti az engedélyeik kezelését.

> [!NOTE]
> Ajánlott eljárás, és javasoljuk, hogy hozzon létre egy biztonsági csoportot az Azure AD-ben, és ne az egyes felhasználók, hanem a csoport engedélyeit. A javaslat részletes ismertetését, valamint az egyéb ajánlott eljárásokat lásd: [hozzáférés-vezérlési modell Azure Data Lake Storage Gen2ban](data-lake-storage-explorer-acl.md).

A jelölőnégyzet vezérlőelemekkel megadhatja a hozzáférést és az alapértelmezett ACL-eket. További információ az ilyen típusú ACL-ek közötti különbségről: [ACL-típusok](data-lake-storage-access-control.md#types-of-acls).

## <a name="apply-acls-recursively"></a>ACL-ek rekurzív alkalmazása

Az ACL-bejegyzéseket rekurzív módon alkalmazhatja a szülő könyvtár meglévő alárendelt elemeire anélkül, hogy ezeket a módosításokat külön kell elvégeznie az egyes alárendelt elemek esetében.

Az ACL-bejegyzések rekurzív alkalmazásához kattintson a jobb gombbal a tárolóra vagy a könyvtárra, majd kattintson a **terjesztési Access Control listák** elemre.  A következő képernyőképen a menü látható, amikor a jobb gombbal egy könyvtárra kattint.

> [!div class="mx-imgBorder"]
> ![Kattintson a jobb gombbal egy könyvtárra, és válassza a hozzáférés-vezérlés propagálása beállítást.](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Következő lépések

További információ a Data Lake Storage Gen2 engedélyezési modellről.

> [!div class="nextstepaction"]
> [Hozzáférés-vezérlési modell Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
