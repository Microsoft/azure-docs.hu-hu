---
title: A Media szolgáltatás számára fenntartott egységek (MRUs) parancssori felületének méretezése
description: Ez a témakör bemutatja, hogyan méretezheti a CLI-t a médiafájlok feldolgozásának méretezésére Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/30/2020
ms.author: inhenkel
ms.openlocfilehash: a07c4a20b854e09daf3b320b8c99757ca99b2578
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213810"
---
# <a name="how-to-scale-media-reserved-units"></a>A Media szolgáltatás számára fenntartott egységek méretezése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan méretezhető a Media szolgáltatás számára fenntartott egységek (MRSs) a gyorsabb kódolás érdekében.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services fiókot](./create-account-howto.md).

A [Media szolgáltatás számára fenntartott egységek](concept-media-reserved-units.md)ismertetése.

## <a name="scale-media-reserved-units-with-cli"></a>A Media szolgáltatás számára fenntartott egységek méretezése a parancssori felülettel

Futtassa a következő parancsot: `mru`.

A következő az [AMS Account MRU](/cli/azure/ams/account/mru) parancs beállítja a Media szolgáltatás számára fenntartott egységeket a "amsaccount" fiókban a **Count** és a **Type** paraméterek használatával.

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>Számlázás

A számlázás a fiókban a Media szolgáltatás számára fenntartott egységek száma alapján történik. Ez attól függetlenül történik, hogy van-e a fiókjában futó feladat. Részletes magyarázatért tekintse meg a [Media Services díjszabási](https://azure.microsoft.com/pricing/details/media-services/) oldalának gyakori kérdések című szakaszát.   

## <a name="next-step"></a>Következő lépés

[Videók elemzése](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>Lásd még

* [Kvóták és korlátok](limits-quotas-constraints.md)
