---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 112c30fdd242c20f11c43f42ba54e3717e074bbb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101706035"
---
Itt látható a Data Box eszköz támogatott tárolási fiókjainak és tárolási típusainak listája. A Storage-fiókok összes típusával kapcsolatos összes képesség teljes listájáért lásd: [Storage-fiókok típusai](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Importálási megrendelések esetén a következő táblázat a támogatott Storage-fiókokat mutatja be.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blob** _ |_ *Azure-fájlok** |**Megjegyzések**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | Y | Y | Y |
| Általános célú v1 standard  | Y | Y | Y | A gyakori és a ritka elérés is támogatott.|
| Általános célú v1 Premium  |  | Y| | |
| Általános célú v2 standard  | Y | Y | Y | A gyakori és a ritka elérés is támogatott.|
| Általános célú v2 prémium  |  |Y | | |
| Prémium szintű Azure-FileStorage |  |  | Y |  |  
| BLOB Storage standard |Y | | |A gyakori és a ritka elérés is támogatott. |

\**– Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.*

Az exportálási megrendelések esetében a következő táblázat a támogatott Storage-fiókokat mutatja be.

| **Storage-fiók/támogatott tárolási típusok** | **Blokkblob** |**Oldal blob** _ |_ *Azure-fájlok** |**Támogatott hozzáférési szintek**|
| --- | --- | -- | -- | -- |
| Klasszikus standard | Y | Y | Y | |
| Általános célú v1 standard  | Y | Y | Y | Gyors elérésű, ritka|
| Általános célú v1 Premium  |  | Y| | |
| Általános célú v2 standard  | Y | Y | Y | Gyors elérésű, ritka|
| Általános célú v2 prémium  |  |Y | | |
| Prémium szintű Azure-FileStorage |  |  | Y |  |
| BLOB Storage standard |Y | | |Gyors elérésű, ritka |
| A blob Storage Premium letiltása |Y | | |Gyors elérésű, ritka |
| Oldal blob Storage Premium | |Y | | |

> [!IMPORTANT]
> - Az általános célú fiókok esetében a Data Box nem támogatja az importálási rendelésekhez tartozó üzenetsor-, tábla-és lemez-tárolási típusokat. Az exportálási megrendelések esetében Data Box nem támogatja az üzenetsor-kezelést, a táblát, a lemezt és a Azure Data Lake 2. generációs tárolási típusokat az általános célú fiókokhoz.
> - A Data Box nem támogatja a Blobok hozzáfűzését a Blob Storagehoz és a Blob Storage fiókok blokkolásához.
> - A hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage-ban Data Box nem támogatott.
> - Az oldal blobokra feltöltött adatmennyiségnek 512 bájtnak kell lennie, például VHD-k.
> - Legfeljebb 80 TB lehet exportálni.
> - A rendszer nem exportálja a fájl előzményeit és a blob-pillanatképeket.