---
title: Az Azure-ba berendezés használatával történő adatátvitel | Microsoft Docs
description: Megtudhatja, hogyan választhatja ki a megfelelő berendezést az Azure-ba történő helyszíni adatátvitelhez a Data Box Edge, Azure File Sync és a StorSimple 8000 sorozat között.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 1345486e6bda7501a862612652b722b0075e190f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791164"
---
# <a name="compare-storsimple-with-azure-file-sync-and-data-box-edge-data-transfer-options"></a>A StorSimple összehasonlítása az Azure File Synckel és a Data Box Edge adatátviteli lehetőségeivel 

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]
 
Ez a dokumentum áttekintést nyújt az Azure-ba történő helyszíni adatátvitel lehetőségeiről, összehasonlítva a következővel: Data Box Edge és Azure File Sync StorSimple 8000 sorozat.

- **[Data Box Edge](../databox-online/azure-stack-edge-overview.md)** – Data Box Edge egy helyszíni hálózati eszköz, amely adatokat mozgat az Azure-ba és onnan ki, és AI-kompatibilis Peremhálózati számításokkal rendelkezik az adatok feltöltés közbeni előzetes feldolgozásához. A Data Box Gateway az eszköz virtuális verziója, amely ugyanazokkal az adatátviteli képességekkel rendelkezik.
- **[Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)** – Azure File Sync segítségével központosíthatja a szervezet fájlmegosztását az Azure Files-ban, miközben a helyszíni fájlkiszolgálók rugalmasságát, teljesítményét és kompatibilitását is biztosítja. Az Azure File Sync a Windows Servert az Azure-fájlmegosztás gyors gyorsítótárává alakítja át. A szolgáltatások általános Azure File Sync 2018-ban már bejelentettük.
- **[StorSimple](./storsimple-overview.md)** – A StorSimple egy hibrid eszköz, amely az Azure Storage-ral való szoros integráció révén egyetlen megoldáson segít a vállalatoknak egyesíteni tárolási infrastruktúrájukat elsődleges tárolásra, adatvédelemre, archiválásra és vészhelyreállításra. A StorSimple termék életciklusa itt [található:](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series).

## <a name="comparison-summary"></a>Összehasonlítás összegzése

|                           |StorSimple 8000   |Azure File Sync   |Data Box Edge           |
|---------------------------|----------------------------------------|-------------------------------|-----------------------------------------|
|**Áttekintés**     |Rétegzett hibrid tárolás és archiválás|Általános fájlkiszolgáló-tároló felhőbeli rétegezéssel és több telephelyes szinkronizálással.  |Tárolási megoldás az adatok elő feldolgozásához és hálózaton keresztül az Azure-ba való elküldéhez.        |
|**Forgatókönyvek**    |Fájlkiszolgáló, archiválás, biztonsági mentési cél |Fájlkiszolgáló, archiválás (több hely)   |Adatátvitel, adatok előzetes feldolgozása, beleértve a gépi tanulási következtetést, az IoT-t és az archiválást    |
|**Peremhálózati számítás** |Nem érhető el |Nem érhető el |Támogatja a tárolók futtatását Azure IoT Edge    |
|**Form factor (Űrlaptényező)**  |Fizikai eszköz   |A Windows Serveren telepített ügynök |Fizikai eszköz   |
|**Hardver**     |A Microsofttól a szolgáltatás részeként biztosított fizikai eszköz | Az ügyfél által megadott |A Microsofttól a szolgáltatás részeként biztosított fizikai eszköz  |
|**Adatformátum**  |Egyéni formátum   |Fájlok         |Blobok vagy fájlok    |
|**Protokolltámogatás** |iSCSI          |SMB, NFS    | SMB vagy NFS      |
|**Díjszabás**      |[StorSimple](https://azure.microsoft.com/pricing/details/storsimple/) |[Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/)  |[Data Box Edge](https://azure.microsoft.com/pricing/details/storage/databox/edge/)  |

## <a name="next-steps"></a>Következő lépések

- Az [Edge Azure Data Box és](../databox-online/azure-stack-edge-overview.md) a [Azure Data Box Gateway](../databox-gateway/data-box-gateway-overview.md)
- További információ a [Azure File Sync](../storage/file-sync/file-sync-deployment-guide.md)