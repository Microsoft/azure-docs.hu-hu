---
title: Azure-fájlmegosztás kezelése Azure Storage Explorer használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan felügyelheti az Azure Files szolgáltatást az Azure Storage Explorerrel.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629784"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure Storage Explorerrel
Ez az útmutató az [Azure-fájlmegosztások](storage-files-introduction.md) Azure Storage Explorerrel történő használatának alapvető lépéseit mutatja be. Az Azure-fájlmegosztások nem különböznek más fájlmegosztásoktól, a tárolásuk azonban a felhőben történik, és az Azure platform nyújt számukra támogatást. Az Azure-fájlmegosztások támogatják az iparági szabvány SMB protokollt, és lehetővé teszik a több gép, alkalmazás és példány közötti fájlmegosztást. 

Az Azure Storage Explorer a Windows, macOS és Linux esetén egyaránt elérhető népszerű ügyféleszköz. A Storage Explorer segítségével felügyelheti az Azure-fájlmegosztásokat és más tárolási erőforrásokat.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek
A rövid útmutatóhoz a Storage Explorer telepítése szükséges. A letöltéséhez és telepítéséhez keresse fel az [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) webhelyét.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
A Storage Explorer nem használható új erőforrások létrehozására. A bemutatóhoz hozzon létre egy tárfiókot az [Azure Portalon](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>A Storage Explorer csatlakoztatása Azure-erőforrásokhoz
A Storage Explorer első indításakor megjelenik a **Microsoft Azure Storage Explorer – Csatlakozás** ablak. A Storage Explorer számos különféle módot kínál a tárfiókokhoz való csatlakozáshoz: 

- **Bejelentkezés az Azure-fiókon keresztül**: Bejelentkezhet a vállalata vagy Microsoft-fiókja felhasználói hitelesítő adataival. 
- **Csatlakozás adott tárfiókhoz kapcsolati sztring vagy SAS-jogkivonat használatával**: A kapcsolati sztring egy speciális sztring, amely tartalmazza a tárfiók nevét és a tárfiók kulcsát/SAS-jogkivonatát. A jogkivonattal a Storage Explorer közvetlenül a tárfiókhoz fér hozzá (ahelyett, hogy egyszerűen az Azure-fiókban lévő összes tárfiókot látná). További információ a kapcsolati sztringekról: [Az Azure Storage kapcsolati sztringek konfigurálása](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Csatlakozás adott tárfiókhoz a tárfiók nevének és kulcsának használatával**: A tárfiók nevével és kulcsával csatlakozhat az Azure Storage-hoz.

Ebben a rövid útmutatóban az Azure-fiókjával jelentkezzen be. Válassza az **Azure-fiók hozzáadása**, majd a **Bejelentkezés** elemet. Az utasításokat követve jelentkezzen be Azure-fiókjába.

![A Microsoft Azure Storage Explorer – Csatlakozás ablak képernyőképe](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Fájlmegosztás létrehozása
Az első Azure-fájlmegosztás létrehozása a `storageacct<random number>` tárfiókban:

1. Bontsa ki a létrehozott tárfiókot.
2. Kattintson a jobb gombbal a **Fájlmegosztások** elemre, majd válassza a **Fájlmegosztás létrehozása** lehetőséget.  
    ![A fájlmegosztások mappa és a környezet helyi menüjének képernyőképe](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Adja a *myshare* nevet a fájlmegosztásnak, majd nyomja le az Enter billentyűt.

A fájlmegosztások neve csak kisbetűket, számokat és kötőjeleket tartalmazhat (de nem kezdődhet kötőjellel). A fájlmegosztás és a fájlok elnevezésével kapcsolatos részletes információkért lásd: [megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Ha a fájlmegosztás létrejött, a jobb oldali panelen megnyílik a fájlmegosztás lapja. 

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure Storage Explorerrel is használhatja az Azure-fájlmegosztást. Az Azure Storage Explorer használata azért előnyösebb a fájlmegosztás SMB-vel való csatlakoztatásánál, mert az Azure Storage Explorerrel kezdeményezett kérések mindegyike a Fájl REST API-t használja. A Fájl REST API segítségével létrehozhat, módosíthat és törölhet fájlokat és könyvtárakat az SMB-hozzáféréssel nem rendelkező ügyfeleken is.

### <a name="create-a-directory"></a>Könyvtár létrehozása
Egy könyvtár hozzáadása hierarchikus struktúrát biztosít a fájlmegosztás felügyeletéhez. A könyvtárban több szintet is létrehozhat. Az alkönyvtárak létrehozása előtt azonban mindig győződjön meg arról, hogy a szülőkönyvtárak léteznek. A myDirectory/mySubDirectory elérési út esetében például előbb a *myDirectory* könyvtárat kell létrehozni. Ezután hozhatja létre a *mySubDirectory* alkönyvtárat. 

1. A fájlmegosztás lapjának felső menüjében kattintson az **Új mappa** gombra. Megnyílik az **Új könyvtár létrehozása** panel.
    ![Képernyőkép a menü Új mappa gombjáról](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. A könyvtár neveként adja meg a *myDirectory* nevet, majd kattintson az **OK** gombra. 

A *myDirectory* könyvtár megjelenik a *myshare* fájlmegosztás lapján.

### <a name="upload-a-file"></a>Fájl feltöltése 
Feltölthet fájlokat a helyi gépről a fájlmegosztás új könyvtárába. Feltölthet teljes mappát vagy akár egyetlen fájlt is.

1. A felső menüben válassza a **Feltöltés** elemet. Ez lehetővé teszi egy mappa vagy fájl feltöltését.
2. Válassza a **Fájl feltöltése** lehetőséget, majd jelöljön ki egy feltölteni kívánt fájlt a helyi gépen.
3. A **Feltöltés könyvtárba** mezőbe írja be a *myDirectory* nevet, majd kattintson a **Feltöltés** gombra. 

Amikor végzett, a fájl megjelenik a *myDirectory* panelen lévő listában.

### <a name="download-a-file"></a>Fájl letöltése
Ha le szeretné tölteni a fájlmegosztásból valamelyik fájl másolatát, kattintson a jobb gombbal a fájlra, majd válassza a **Letöltés** lehetőséget. Válassza ki, hová szeretné menteni a fájlt a helyi gépen, majd válassza a **Mentés** lehetőséget.

A letöltés állapota az ablak alján található **Tevékenységek** panelen látható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A Storage Explorer nem használható erőforrások eltávolítására. A jelen gyors útmutató fölöslegessé vált elemeinek törléséhez az [Azure Portalt](https://portal.azure.com/) használhatja. 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Mi az az Azure Files?](storage-files-introduction.md)