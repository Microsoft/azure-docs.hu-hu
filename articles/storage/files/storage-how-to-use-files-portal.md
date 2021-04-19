---
title: Rövid útmutató az Azure-fájlmegosztásoknak az Azure Portallal történő felügyeletéhez
description: Tekintse meg, hogyan hozhat létre és kezelhet Azure-fájlmegosztásokat a Azure Portal. Hozzon létre egy tárfiókot, hozzon létre egy Azure-fájlmegosztást, és használja az Azure-fájlmegosztást.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d7867a4bd60bb179c18fff49691072d683660f7e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717845"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Rövid útmutató: Azure-fájlmegosztások létrehozása és felügyelete az Azure Portallal 
Az [Azure Files](storage-files-introduction.md) a Microsoft könnyen használható felhőalapú fájlrendszere. Az Azure-fájlmegosztások Windows, Linux és macOS rendszeren csatlakoztathatók. Ez az útmutató az Azure-fájlmegosztások [Azure Portallal](https://portal.azure.com/) való használatának alapvető lépéseit mutatja be.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Tárfiók létrehozása
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Azure-fájlmegosztás létrehozása
Azure-fájlmegosztás létrehozása:

1. Válassza ki a tárfiókot az irányítópulton.
1. A tárfiók oldalának **Szolgáltatások** szakaszában válassza a **Fájlok** elemet.
    
    ![A tárfiók adattárolási szakaszának képernyőképe; fájlmegosztások kiválasztása.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. A Fájlszolgáltatás oldal tetején található **menüben** kattintson a **Fájlmegosztás elemre.** Lenyílik az **Új fájlmegosztás** oldal.
1. A **Név mezőbe** írja be a *myshare* nevet, írjon be egy idézőjelet, és hagyja kijelölve a **Tranzakció** optimalizálva a **szinteket.**
1. Az **Azure-fájlmegosztás** létrehozásához válassza a Létrehozás lehetőséget.

A fájlmegosztások neve kisbetűket, számokat és kötőjeleket tartalmazhat, de nem kezdődhet kötőjellel. A fájlmegosztások és fájlok elnevezésére vonatkozó információkért lásd: [Naming and Referencing Shares, Directories, Files, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Megosztások, könyvtárak, fájlok és metaadatok elnevezése és hivatkozása).

## <a name="use-your-azure-file-share"></a>Az Azure-fájlmegosztás használata
Azure Files az Azure-fájlmegosztás fájljainak és mappáinak három módszerét biztosítja: az iparági szabvány [Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)protokollt, a hálózati fájlrendszer (NFS) protokollt (előzetes verzió) és a [Fájl REST protokollt.](/rest/api/storageservices/file-service-rest-api) 

A fájlmegosztás SMB-vel való csatlakoztatásához tekintse meg a használt operációs rendszernek megfelelő dokumentumot az alábbiak közül:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Azure-fájlmegosztás használata az Azure Portalról
Az Azure Portalon keresztül intézett összes kérelem a fájl REST API-jával történik, amely lehetővé teszi a fájlok és könyvtárak létrehozását, módosítását és törlését SMB-hozzáférés nélküli ügyfeleken. A Fájl REST protokollal közvetlenül is dolgozhat (vagyis saját maga is dolgozhat a REST HTTP-hívásokon), de a fájl REST protokoll használatának leggyakoribb módja (a Azure Portal használata is) az [Azure PowerShell](storage-how-to-use-files-powershell.md)modul, az [Azure CLI](storage-how-to-use-files-cli.md)vagy egy Azure Storage SDK használata, amelyek megfelelő burkolóval szolgálnak a File REST protokoll körül a választott szkriptelési/programozási nyelven. 

Az Azure Files legtöbb felhasználója az SMB protokollon keresztül szeretné használni az Azure-fájlmegosztást, mivel így használhatja a használni kívánt meglévő alkalmazásokat és eszközöket, de több oka is van annak, hogy előnyösebb a File REST API használata az SMB helyett, például:

- Útközben kell elvégeznie egy gyors módosítást az Azure-fájlmegosztáson, például egy SMB-hozzáférés nélküli laptopról, táblagépről vagy mobileszközről.
- Olyan ügyfélről kell szkriptet vagy alkalmazást futtatnia, amely nem tud SMB-megosztást csatlakoztatni, például a helyszíni ügyfelekről, amelyekhez nincs feloldva a 445-ös port.
- Ki szeretné használni a kiszolgáló nélküli erőforrások, például az [Azure Functions](../../azure-functions/functions-overview.md) előnyeit. 

Az alábbi példák azt mutatják be, hogyan használhatja az Azure Portalt az Azure-fájlmegosztás Fájl REST protokollal való módosítására. 

Most, hogy létrehozott egy Azure-fájlmegosztást, csatlakoztathatja azt az SMB szolgáltatással [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) vagy [macOS](storage-how-to-use-files-mac.md) rendszeren. Másik megoldásként az Azure Portalon is használhatja az Azure-fájlmegosztást. 

#### <a name="create-a-directory"></a>Könyvtár létrehozása
Ha egy új *myDirectory* nevű könyvtárat szeretne létrehozni az Azure-fájlmegosztás gyökérmappájában:

1. A **Fájlszolgáltatás** oldalon válassza ki a **myshare** fájlmegosztást. Megnyílik a fájlmegosztás oldala.
1. Az oldal tetején lévő menüben kattintson a **+ Könyvtár hozzáadása** elemre. Lenyílik az **Új könyvtár** oldal.
1. Írja be a *myDirectory* nevet, majd kattintson az **OK**.gombra.

#### <a name="upload-a-file"></a>Fájl feltöltése 
A fájlok feltöltésének bemutatása érdekében először létre kell hoznia vagy ki kell választania a feltölteni kívánt fájlt. Ezt bármilyen módon megteheti. Miután kiválasztotta a feltölteni kívánt fájlt:

1. Kattintson a **myDirectory** könyvtárra. Megnyílik a **myDirectory** panel.
1. A felső menüben válassza a **Feltöltés** elemet. Megnyílik a **Fájlok feltöltése** panel.  
    
    ![A fájlok feltöltése panel képernyőképe](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Kattintson a mappa ikonra a helyi fájlok megkeresésére szolgáló ablak megnyitásához. 
1. Válasszon egy fájlt, majd kattintson a **Megnyitás** gombra. 
1. A **Fájlok feltöltése** oldalon ellenőrizze a fájl nevét, majd kattintson a **Feltöltés** elemre.
1. Amikor végzett, a fájlnak meg kell jelennie a **myDirectory** oldalon lévő listában.

#### <a name="download-a-file"></a>Fájl letöltése
A fájlra a jobb gombbal kattintva letöltheti a feltöltött fájl másolatát. Miután a letöltés gombra kattintott, a pontos élmény az operációs rendszertől és a használt böngészőtől függ.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Mi az az Azure Files?](storage-files-introduction.md)