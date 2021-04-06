---
title: Azure Data Box korlátok | Microsoft Docs
description: A rendszerkorlátokat és a Microsoft Azure Data Box összetevők és kapcsolatok ajánlott méreteit ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/05/2021
ms.author: alkohli
ms.openlocfilehash: 97d8da86565db73aa9a3866f39f793aaf0905470
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97900161"
---
# <a name="azure-data-box-limits"></a>Azure Data Box korlátok

Vegye figyelembe ezeket a korlátokat a Microsoft Azure Data Box üzembe helyezése és üzemeltetése során. A következő táblázat ismerteti a Data Box korlátozásait.

## <a name="data-box-service-limits"></a>Data Box szolgáltatási korlátok

[!INCLUDE [data-box-service-limits](../../includes/data-box-service-limits.md)]

## <a name="data-box-limits"></a>Data Box korlátok

- Data Box legfeljebb 500 000 000 fájlt tárolhat az importáláshoz és az exportáláshoz.
- A Data Box legfeljebb 512 tárolót vagy megosztást támogat a felhőben. A felhasználói megosztáson belüli legfelső szintű könyvtárak tárolók vagy Azure-fájlmegosztás formájában válnak elérhetővé a felhőben. 
- Data Box a használati kapacitás 80 TB-nál kevesebb lehet, mert ReFS a metaadatokat.
- Data Box egyszerre legfeljebb 10 ügyfélkapcsolatot támogat egy NFS-megosztáson.

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

[!INCLUDE [data-box-storage-limits](../../includes/data-box-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései


### <a name="for-import-order"></a>Importálási sorrend

Az importálási rendelésekre vonatkozó Data Box a következők:

[!INCLUDE [data-box-data-upload-caveats](../../includes/data-box-data-upload-caveats.md)]

### <a name="for-export-order"></a>Exportálási sorrend

Az exportálási sorrendre vonatkozó Data Box a következők:

- Data Box egy Windows-alapú eszköz, és nem támogatja a kis-és nagybetűket megkülönböztető fájlneveket. Előfordulhat például, hogy az Azure-ban két különböző fájl található, amelyek neve csak a házban különbözik. Ne használja a Data Box az ilyen fájlok exportálására, mert a fájlok felül lesznek írva az eszközön.
- Ha ismétlődő címkékkel rendelkezik a bemeneti fájlokban vagy az ugyanarra az adatokra hivatkozó címkékből, akkor a Data Box exportálás kihagyhatja vagy felülírhatja a fájlokat. A Azure Portal által megjelenített fájlok és adatok mérete eltérő lehet az eszközön lévő adatok tényleges méretétől. 
- Data Box az adatexportálást a Windows-alapú rendszerbe SMB protokollon keresztül, és a fájlok és mappák SMB-korlátozásai korlátozzák. A nem támogatott nevekkel rendelkező fájlok és mappák exportálása nem történt meg.
- Létezik egy 1:1-es leképezés az előtagról a tárolóra.
- A fájlnév maximális mérete 1024 karakter. Az ezt a hosszúságot meghaladó fájlnevek nem lesznek exportálva.
- A rendszer az *XML-* fájlban duplikált előtagokat exportál (a rendelés létrehozása során feltöltve). A duplikált előtagok figyelmen kívül lesznek hagyva.
- A blobok és a tárolók nevei megkülönböztetik a kis-és nagybetűket. Ha a burkolat nem egyezik, a blob és/vagy tároló nem található.
 

## <a name="azure-storage-account-size-limits"></a>Azure Storage-fiók méretének korlátai

[!INCLUDE [data-box-storage-account-size-limits](../../includes/data-box-storage-account-size-limits.md)]

## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

[!INCLUDE [data-box-object-size-limits](../../includes/data-box-object-size-limits.md)]

## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Az Azure Block blob, az oldal blobja és a fájl elnevezési konvenciói

[!INCLUDE [data-box-naming-conventions](../../includes/data-box-naming-conventions.md)]
