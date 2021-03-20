---
title: Azure Data Box Gateway korlátok | Microsoft Docs
description: A rendszerkorlátokat és a Microsoft Azure Data Box Gateway ajánlott méreteit ismerteti.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 15b01f92fe0d39d099c10c7c086790a4dbb91379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582105"
---
# <a name="azure-data-box-gateway-limits"></a>Azure Data Box Gateway korlátok

Vegye figyelembe ezeket a korlátokat a Microsoft Azure Data Box Gateway megoldás üzembe helyezése és üzemeltetése során.

## <a name="data-box-gateway-service-limits"></a>Data Box Gateway szolgáltatási korlátok

[!INCLUDE [data-box-gateway-service-limits](../../includes/data-box-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway eszköz korlátai

Az alábbi táblázat a Data Box Gateway eszköz korlátozásait ismerteti.

| Description | Érték |
|---|---|
|Nem. fájl/eszköz |100 000 000 <br> Minden hozzáadott 25 000 000-fájlhoz (a 100 000 000-es maximális korlátnál) 2 TB lemezterületet, 8 GB RAM-ot és 4 mag CPU-t kell hozzáadnia. |
|Nem. a megosztások eszközönként |24 |
|Nem. Az Azure Storage-tárolók által tárolt megosztások |1 |
|Megosztásba írt fájlok maximális mérete|2 TB-os virtuális eszköz esetén a maximális fájlméret 500 GB. <br> A maximális fájlméret az előző arányban az adatlemez méretével nő, amíg az érték nem éri el a maximális 5 TB-ot. |

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

[!INCLUDE [data-box-gateway-storage-limits](../../includes/data-box-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

[!INCLUDE [data-box-gateway-storage-data-upload-caveats](../../includes/data-box-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure Storage-fiók mérete és az objektum méretére vonatkozó korlátok

[!INCLUDE [data-box-gateway-storage-acct-limits](../../includes/data-box-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

[!INCLUDE [data-box-gateway-storage-object-limits](../../includes/data-box-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Következő lépések

- [Az Azure Data Box Gateway üzembe helyezésének előkészítése](data-box-gateway-deploy-prep.md)
