---
title: Azure Stack Edge Pro R-korlátok | Microsoft Docs
description: Leírja a rendszerkorlátokat és az Azure Stack Edge Pro R számára ajánlott méreteket.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dfff3bdd716c54a6c83dbc9fec63c794c1fba85b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466765"
---
# <a name="azure-stack-edge-pro-r-limits"></a>Azure Stack Edge Pro R-korlátok

Vegye figyelembe ezeket a korlátokat az Azure Stack Edge Pro R megoldás üzembe helyezése és üzemeltetése során.

## <a name="azure-stack-edge-pro-r-service-limits"></a>Azure Stack Edge Pro R szolgáltatási korlátai

[!INCLUDE [azure-stack-edge-gateway-service-limits](../../includes/azure-stack-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-pro-r-device-limits"></a>Azure Stack Edge Pro R-eszközök korlátai

Az alábbi táblázat az Azure Stack Edge Pro R-eszköz korlátozásait ismerteti.

| Leírás | Érték |
|---|---|
|Nem. fájl/eszköz |100 000 000 |
|Nem. megosztások száma tárolóban |1 |
|Maximum nem. a megosztási végpontok és a REST-végpontok eszközönként| 24 |
|Maximum nem. a többszintes Storage-fiókok száma eszközönként| 24|
|Megosztásba írt fájlok maximális mérete| 5 TB |
|Erőforráscsoportok maximális száma eszközönként| 800 |

## <a name="azure-storage-limits"></a>Azure Storage-korlátok

[!INCLUDE [azure-stack-edge-gateway-storage-limits](../../includes/azure-stack-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Az adatfeltöltés kikötései

[!INCLUDE [azure-stack-edge-gateway-storage-data-upload-caveats](../../includes/azure-stack-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Az Azure Storage-fiók mérete és az objektum méretére vonatkozó korlátok

[!INCLUDE [azure-stack-edge-gateway-storage-acct-limits](../../includes/azure-stack-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure-objektum méretének korlátai

[!INCLUDE [azure-stack-edge-gateway-storage-object-limits](../../includes/azure-stack-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Következő lépések

- [Felkészülés a Azure Stack Edge Pro R üzembe helyezésére](azure-stack-edge-pro-r-deploy-prep.md)
