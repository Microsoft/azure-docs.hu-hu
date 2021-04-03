---
title: Azure Stack Edge mini R-korlátok | Microsoft Docs
description: Leírja a rendszerkorlátokat és az Azure Stack Edge mini R számára ajánlott méreteket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 6f01d62d1d11f5ff90661482ffd2db112657eee5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466878"
---
# <a name="azure-stack-edge-mini-r-limits"></a>Azure Stack Edge mini R-korlátok


Vegye figyelembe ezeket a korlátokat az Azure Stack Edge mini R-megoldás üzembe helyezése és üzemeltetése során.

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge-szolgáltatás korlátai

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-mini-r-device-limits"></a>Azure Stack Edge mini R-eszközök korlátai

Az alábbi táblázat az Azure Stack Edge mini R-eszköz korlátozásait ismerteti.

| Leírás | Korlát|
|---|---:|
|Nem. fájl/eszköz | 100 000 000 <!--check with devs-->|
|Nem. megosztások száma tárolóban | 1|
|Maximum nem. a megosztási végpontok és a REST-végpontok eszközönként| 24 |
|Maximum nem. a többszintes Storage-fiókok száma eszközönként| 24|
|Megosztásba írt fájlok maximális mérete| 500 GB|
|Erőforráscsoportok maximális száma eszközönként| 800|

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure Storage-fiók mérete és az objektum méretére vonatkozó korlátok

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Következő lépések

- [Felkészülés az Azure Stack Edge üzembe helyezésére](azure-stack-edge-gpu-deploy-prep.md)
