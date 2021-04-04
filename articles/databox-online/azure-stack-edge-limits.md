---
title: Azure Stack Edge Pro-korlátok | Microsoft Docs
description: Ismerje meg a korlátokat és az ajánlott méreteket a Azure Stack Edge Pro üzembe helyezése és üzemeltetése során, beleértve a szolgáltatási korlátokat, az eszközök korlátait és a tárolási korlátokat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: f1bb5662b48765c08ec58d1f7fa9d341de4937bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91992753"
---
# <a name="azure-stack-edge-pro-limits"></a>Az Azure Stack Edge Pro korlátai

Ezeket a korlátokat a Microsoft Azure Stack Edge Pro-megoldás üzembe helyezése és üzemeltetése során érdemes figyelembe venni. 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge-szolgáltatás korlátai

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge-eszközök korlátai

Az alábbi táblázat az Azure Stack Edge Pro-eszköz korlátozásait ismerteti. 

Az alábbi táblázat az Azure Stack Edge-eszközre vonatkozó korlátozásokat ismerteti.

| Leírás | Érték |
|---|---|
|Nem. fájl/eszköz |100 000 000 |
|Nem. megosztások száma tárolóban |1 |
|Maximum nem. a megosztási végpontok és a REST-végpontok eszközönként| 24 |
|Maximum nem. a többszintes Storage-fiókok száma eszközönként| 24|
|Megosztásba írt fájlok maximális mérete| 5 TB |
|Erőforráscsoportok maximális száma eszközönként| 800 |

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure Storage-fiók mérete és az objektum méretére vonatkozó korlátok

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Következő lépések

- [Felkészülés a Azure Stack Edge Pro üzembe helyezésére](azure-stack-edge-deploy-prep.md)
