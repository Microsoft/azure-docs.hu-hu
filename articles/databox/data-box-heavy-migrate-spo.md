---
title: A fájlmegosztás tartalmának a SharePoint Online-ba való áthelyezéséhez Azure Data Box Heavy használata
description: Ebből az oktatóanyagból megtudhatja, hogyan telepítheti át a fájlmegosztás tartalmát az online megosztási pontra a Azure Data Box Heavy használatával
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: how-to
ms.date: 07/18/2019
ms.author: alkohli
ms.openlocfilehash: b00a944b69b8492d4aa00258154bf00f1c66ad33
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92127114"
---
# <a name="use-the-azure-data-box-heavy-to-migrate-your-file-share-content-to-sharepoint-online"></a>A fájlmegosztás tartalmának migrálása a SharePoint Online-ba a Azure Data Box Heavy használatával

Az Azure Data Box Heavy és a SharePoint áttelepítési eszköz (SPMT) használatával egyszerűen áttelepítheti a fájlmegosztás tartalmát a SharePoint Online-ba és a OneDrive-be. A Data Box Heavy használatával eltávolíthatja a nagy kiterjedésű hálózati (WAN) kapcsolat függőségét az adatok átviteléhez.

A Microsoft Azure Data Box egy olyan szolgáltatás, amely lehetővé teszi egy eszköz megrendelését a Microsoft Azure Portal. Ezután a kiszolgálókról az eszközre másolhat terabájt adatmennyiséget. A Microsoft felé történő szállítás után az adatai az Azure-ba lesznek másolva. Az átvinni kívánt adatok méretétől függően a következő lehetőség közül választhat:

- [Data Box Disk](./data-box-disk-overview.md) a 35 TB-os felhasználható kapacitással, kis-közepes adatkészletek esetén.
- [Data Box](./data-box-overview.md) a 80 TB-os felhasználható kapacitást a közepes és a nagy adatkészletek esetében.
- [Data Box Heavy](./data-box-heavy-overview.md) a 770 TB-os felhasználható kapacitással a nagyméretű adatkészletek esetében.

Ez a cikk részletesen ismerteti, hogyan lehet az Data Box Heavy használatával áttelepíteni a fájlmegosztás tartalmát a SharePoint Online-ba.

## <a name="requirements-and-costs"></a>Követelmények és költségek

### <a name="for-data-box-heavy"></a>A Data Box Heavyhez

- A Data Box Heavy csak Nagyvállalati Szerződés (EA), Cloud Solution Provider (CSP) vagy Azure szponzorálási ajánlatok esetén érhető el. Ha az előfizetése nem tartozik a fenti típusok egyikéhez sem, forduljon a Microsoft ügyfélszolgálatahoz, és frissítse az előfizetését, vagy tekintse meg az [Azure-előfizetés díjszabását](https://azure.microsoft.com/pricing/).
- A Data Box Heavy használatára díjat számítunk fel. Ügyeljen rá, hogy áttekintse a [Data Box Heavy díjszabását](https://azure.microsoft.com/pricing/details/databox/heavy/).


### <a name="for-sharepoint-online"></a>A SharePoint Online-hoz

- Tekintse át a [SharePoint áttelepítési eszköz (SPMT) minimális követelményeit](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

## <a name="workflow-overview"></a>A munkafolyamat áttekintése

Ehhez a munkafolyamathoz a Azure Data Box Heavyon és a SharePoint Online-on is végre kell hajtania a szükséges lépéseket.
A következő lépések kapcsolódnak a Azure Data Box Heavyhoz.

1. Megrendelés Azure Data Box Heavy.
2. Az eszköz fogadása és beállítása.
3. Másolja a helyszíni fájlmegosztás adatait az eszközön lévő Azure Files mappájába.
4. A másolás befejezése után küldje vissza az eszközt az utasításoknak megfelelően.
5. Várjon, amíg az adatok teljesen feltöltve lettek az Azure-ba.

A következő lépések kapcsolódnak a SharePoint Online-hoz.

6. Hozzon létre egy virtuális gépet a Azure Portalban, és csatlakoztassa az Azure-fájlmegosztást.
7. Telepítse a SPMT eszközt az Azure-beli virtuális gépre.
8. Futtassa a SPMT eszközt az Azure-fájlmegosztás használatával *forrásként*.
9. Fejezze be az eszköz utolsó lépéseit.
10. Ellenőrizze és erősítse meg az adatait.

## <a name="use-data-box-heavy-to-copy-data"></a>Az Adatmásolás Data Box Heavy használata

Az alábbi lépéseket követve másolhatja az adatait a Data Box Heavyba.

1. [Rendeljen Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Miután megkapta a Data Box Heavy, [állítsa be a Data Box Heavy](data-box-heavy-deploy-set-up.md). Az eszközön lévő csomópontokat is csatlakoztathatja és konfigurálhatja.
3. [Adatmásolás Azure Data Box Heavyba](data-box-heavy-deploy-copy-data.md). A másolás során ügyeljen rá, hogy:

    - Az adatmásoláshoz csak a Data Box Heavy *StorageAccountName_AzFile* mappáját használja. Ennek az az oka, hogy az adott Azure-fájlmegosztás nem blokkolja a blobokat vagy a blobokat.
    - Fájlok másolása *StorageAccountName_AzFile* mappában található mappába. A *StorageAccountName_AzFile* mappában található almappa egy fájlmegosztást hoz létre. A közvetlenül a *StorageAccountName_AzFile* mappába másolt fájlok sikertelenek lesznek, és a rendszer blokk blobként feltölti őket. Ezt a fájlmegosztást fogja csatlakoztatni a virtuális gépen a következő lépésben.
    - Másolja az adatait a Data Box Heavy mindkét csomópontjára.
3. [Szállításra való előkészítés](data-box-heavy-deploy-picked-up.md#prepare-to-ship) futtatása az eszközön. A szállítás sikeres előkészítése biztosítja a fájlok sikeres feltöltését az Azure-ba.
4. [Az eszköz visszaadása](data-box-heavy-deploy-picked-up.md#ship-data-box-heavy-back).
5. [Ellenőrizze az adatok feltöltését az Azure-](data-box-heavy-deploy-picked-up.md#verify-data-upload-to-azure)ba.

## <a name="use-spmt-to-migrate-data"></a>Az SPMT használata az adatáttelepítéshez

Miután megkapta az Azure-beli Adatmásolás jóváhagyását, folytassa az adatok átmásolását a SharePoint Online-ba.

A legjobb teljesítmény és kapcsolat érdekében javasoljuk, hogy hozzon létre egy Azure-beli virtuális gépet (VM).

1. Jelentkezzen be a Azure Portalba, majd [hozzon létre egy virtuális gépet](../virtual-machines/windows/quick-create-portal.md).
2. [Csatlakoztassa az Azure-fájlmegosztást a virtuális géphez](../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share-with-file-explorer).
3. [Töltse le a SharePoint áttelepítési eszközt](https://spmtreleasescus.blob.core.windows.net/install/default.htm) , és telepítse az Azure-beli virtuális gépre.
4. Indítsa el a SharePoint áttelepítési eszközét. Kattintson a **Bejelentkezés** elemre, és adja meg a munkahelyi vagy iskolai fiókjához tartozó felhasználónevet és jelszót.
5. Ha a rendszer kéri, **hogy hol található az adatai?**, válassza a **fájlmegosztás** lehetőséget. Adja meg az Azure-fájlmegosztás elérési útját, ahol az adatai találhatók.
6. A többi kérést a szokásos módon követheti, beleértve a célhelyet is. További információért látogasson el [a SharePoint áttelepítési eszköz használatára](/sharepointmigration/how-to-use-the-sharepoint-migration-tool).

> [!IMPORTANT]
> - Az adatok SharePoint Online-ba való betöltésének gyorsasága számos tényezőt érint, függetlenül attól, hogy az adatok már az Azure-ban vannak-e. Ezen tényezők megismerése segít megtervezni és maximalizálni az áttelepítés hatékonyságát.  További információért látogasson el a [SharePoint Online-ba és a OneDrive áttelepítési sebességre](/sharepointmigration/sharepoint-online-and-onedrive-migration-speed).
> - A SharePoint Online-ba való Migrálás során fennáll a veszélye, hogy a fájlokra vonatkozóan meglévő engedélyeket veszít. Előfordulhat, hogy bizonyos metaadatokat is elveszít, például a által *létrehozott* és *módosított dátummal*.

## <a name="next-steps"></a>Következő lépések

[Rendeljen Data Box Heavy](./data-box-heavy-deploy-ordered.md)