---
title: Az Azure import/export szolgáltatásra vonatkozó követelmények | Microsoft Docs
description: Ismerje meg az Azure import/export szolgáltatás szoftver-és hardver-követelményeit.
author: alkohli
services: storage
ms.service: storage
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 0bfc09a372584a25c23060cef33d1f698e6d5ff3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712606"
---
# <a name="azure-importexport-system-requirements"></a>Az Azure Import/Export rendszerkövetelményei

Ez a cikk az Azure import/export szolgáltatással kapcsolatos fontos követelményeket ismerteti. Javasoljuk, hogy az importálási/exportálási szolgáltatás használata előtt figyelmesen tekintse át az információkat, majd a művelet során szükség szerint tekintse át újra.

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A merevlemezek a WAImportExport eszközzel történő előkészítéséhez a **BitLocker meghajtótitkosítást támogató következő 64 bites operációs rendszer** támogatott.


|Platform |Verzió |
|---------|---------|
|Windows     | Windows 7 Enterprise, Windows 7 Ultimate <br> Windows 8 Pro, Windows 8 Enterprise, Windows 8,1 Pro, Windows 8,1 Enterprise <br> Windows 10        |
|Windows Server     |Windows Server 2008 R2 <br> Windows Server 2012, Windows Server 2012 R2         |

## <a name="other-required-software-for-windows-client"></a>Egyéb szükséges szoftverek a Windows-ügyfélhez

|Platform |Verzió |
|---------|---------|
|.NET-keretrendszer    | 4.5.1       |
| BitLocker        |  _          |


## <a name="supported-storage-accounts"></a>Támogatott tárfiókok

Az Azure import/export szolgáltatás a következő típusú Storage-fiókokat támogatja:

- Standard általános célú v2 Storage-fiókok (a legtöbb esetben ajánlott)
- Blob Storage-fiókok
- Általános célú v1 Storage-fiókok (klasszikus vagy Azure Resource Manager üzemelő példányok)

> [!IMPORTANT]
> A hálózati fájlrendszer (NFS) 3,0 protokoll támogatása az Azure Blob Storage-ban nem támogatott az Azure import/export szolgáltatásban.

A Storage-fiókokkal kapcsolatos további információkért lásd: az [Azure Storage-fiókok áttekintése](../storage/common/storage-account-overview.md).

Az egyes feladatokhoz csak egy Storage-fiókból vagy azokból lehet adatátvitelt alkalmazni. Ez azt jelenti, hogy egyetlen importálási/exportálási feladattípus nem terjedhet több Storage-fiókra. További információ az új Storage-fiókok létrehozásáról: [Storage-fiók létrehozása](../storage/common/storage-account-create.md).

> [!IMPORTANT]
> Azon tárolási fiókok esetében, amelyeken engedélyezve van a [Virtual Network szolgáltatás-végpontok](../virtual-network/virtual-network-service-endpoints-overview.md) szolgáltatás, a **megbízható Microsoft-szolgáltatások engedélyezése...** beállítással [engedélyezheti az importálási/exportálási](../storage/common/storage-network-security.md) szolgáltatást az Azure-ba irányuló adatok importálásához/exportálásához.

## <a name="supported-storage-types"></a>Támogatott tárolótípusok

Az Azure import/export szolgáltatás támogatja az alábbi tárolási típusok listáját.


|Feladat  |Tárolási szolgáltatás |Támogatott  |Nem támogatott  |
|---------|---------|---------|---------|
|Importálás     |  Azure Blob Storage <br><br> Azure File Storage       | A blobok és a Blobok által támogatott lapok letiltása <br><br> Támogatott fájlok          |
|Exportálás     |   Azure Blob Storage       | Blobok, blobok és hozzáfűző Blobok blokkolása támogatott         | Azure Files nem támogatott


## <a name="supported-hardware"></a>Támogatott hardverek

Az Azure import/export szolgáltatáshoz az adatmásoláshoz támogatott lemezekre van szükség.

### <a name="supported-disks"></a>Támogatott lemezek

Az importálási/exportálási szolgáltatással az alábbi lemezek listája támogatott.


|Lemeztípus  |Méret  |Támogatott |
|---------|---------|---------|
|SSD    |   2,5 "      |SATA III          |
|HDD     |  2,5 "<br>3,5 "       |SATA II, SATA III         |

A következő típusú lemezek nem támogatottak:

- USBs.
- Külső HDD beépített USB-adapterrel.
- Külső HDD házán belüli lemezek.

Egy adott importálási/exportálási feladatokhoz a következőket teheti:

- Legfeljebb 10 HDD/SSD.
- Bármilyen méretű HDD/SSD-kombináció.

A nagy számú meghajtó több feladaton is elosztható, és a létrehozható feladatok száma nincs korlátozva. Az importálási feladatokhoz csak a meghajtón lévő első adatmennyiség lesz feldolgozva. Az adatkötetet NTFS fájlrendszerrel kell formázni.

Merevlemezek előkészítésekor és az adatok WAImportExport eszközzel történő másolásakor külső USB-adaptereket is használhat. A legtöbbet a polcon lévő USB 3,0 vagy újabb rendszerű adaptereknek működniük kell.

## <a name="next-steps"></a>Következő lépések

* [Transfer data with the AzCopy Command-Line Utility (Adatátvitel az AzCopy parancssori segédprogrammal)](../storage/common/storage-use-azcopy-v10.md)