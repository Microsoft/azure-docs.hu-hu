---
title: A Media szolgáltatás számára fenntartott egységek (MRUs) parancssori felületének méretezése
description: Ez a témakör bemutatja, hogyan méretezheti a CLI-t a médiafájlok feldolgozásának méretezésére Azure Media Services használatával.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870160"
---
# <a name="how-to-scale-media-reserved-units"></a>A Media szolgáltatás számára fenntartott egységek méretezése

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk bemutatja, hogyan méretezhető a Media szolgáltatás számára fenntartott egységek (MRSs) a gyorsabb kódolás érdekében.

> [!WARNING]
> Ez a parancs már nem fog Media Services működni az API 2020-05-01-es verziójával vagy újabb verzióval létrehozott fiókokhoz. Ezekhez a fiókokhoz a Media szolgáltatás számára fenntartott egységek már nem szükségesek, mivel a rendszer a terhelés alapján automatikusan fel-és leskálázást végez. Ha nem látja a MRUs kezelését a Azure Portal, akkor olyan fiókot használ, amely az 2020-05-01 API-val vagy később lett létrehozva.

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
