---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98225137"
---
Itt láthatók a Storage-fiókba másolt adatmennyiség korlátai. Győződjön meg arról, hogy a feltöltött adatok megfelelnek a határértékeknek. A korlátozásokkal kapcsolatos legfrissebb információk: a [blob Storage skálázhatósági és teljesítményi céljai](../articles/storage/blobs/scalability-targets.md) , valamint a [méretezhetőség és a teljesítmény-Azure Files](../articles/storage/files/storage-files-scale-targets.md).

| Az Azure Storage-fiókba másolt adatmennyiség                      | Alapértelmezett korlát          |
|---------------------------------------------------------------------|------------------------|
| BLOB és oldal blobjának letiltása                                            | A maximális korlát megegyezik az Azure- [előfizetéshez definiált tárolási korláttal](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) , és az összes forrásból, köztük a Data boxból származó adatokkal is rendelkezik.   |
| Azure Files                                                          | Data Box támogatja a nagyméretű fájlmegosztást (100 TiB), ha engedélyezve van a Data Box rendelés létrehozása előtt. <br> Ha nincs engedélyezve a megrendelés létrehozása előtt, a maximális fájlmegosztás mérete 5 TiB. <br> A Data Box támogatja az Azure Premium-fájlmegosztás használatát, amely összesen 100 TiB-t tesz lehetővé a Storage-fiók összes megosztásához. <br> A maximálisan felhasználható kapacitás valamivel kisebb, mert a naplókat és a naplókat a naplók használják. A másolási napló és a napló számára legalább 100 GiB van fenntartva. További információ: [Azure Data Box naplók naplózása, Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md). <br> A *StorageAccount_AzureFiles* alatti összes mappának ezt a korlátot kell követnie. <br> További információ: [nagyméretű fájlmegosztás engedélyezése és létrehozása](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
