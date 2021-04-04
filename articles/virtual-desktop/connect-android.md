---
title: Kapcsolódás Windowsos virtuális asztalhoz Androidról – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz az Android-ügyfél használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 03/25/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ebf91f68e45148d9a609ff671ffa4683bd74c82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89226114"
---
# <a name="connect-to-windows-virtual-desktop-with-the-android-client"></a>Kapcsolódás a Windows rendszerű virtuális asztalhoz az Android-ügyféllel

> A következőkre vonatkozik: Android 4,1 és újabb verziók, Chromebook a ChromeOS 53 és újabb verziókkal.

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/connect-android-2019.md).

A Windows rendszerű virtuális asztali erőforrásokat elérhetővé teheti az Android-eszközről a letölthető ügyféllel. Az Android-ügyfelet a Google Play Áruház támogató Chromebook-eszközökön is használhatja. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az Android-ügyfelet.

## <a name="install-the-android-client"></a>Az Android-ügyfél telepítése

Első lépésként [töltse le](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) és telepítse az ügyfelet az Android-eszközön.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a rendszergazda által biztosított hírcsatornára, hogy lekérje az Android-eszközén elérhető felügyelt erőforrások listáját.

Előfizetés a hírcsatornára:

1. A csatlakoztatási központban koppintson a **+** elemre, majd koppintson a **távoli erőforrás-hírcsatorna** lehetőségre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címe** mezőbe. A hírcsatorna URL-címe lehet URL vagy e-mail-cím.
   - Ha URL-címet használ, használja a rendszergazdát, aki a szokásos módon adta meg <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - E-mail-cím használatához adja meg az e-mail-címét. Az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda úgy konfigurálta a kiszolgálót.
   - A US Gov-portálon keresztüli kapcsolódáshoz használja a következőt: <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Koppintson a **tovább** gombra.
4. Ha a rendszer kéri, adja meg a hitelesítő adatait.
   - A **Felhasználónév mezőben** adja meg a felhasználónevet, amely jogosult az erőforrások elérésére.
   - A **Password (jelszó**) mezőben adja meg a felhasználónévhez tartozó jelszót.
   - Az is előfordulhat, hogy további tényezőket kell megadnia, ha a rendszergazda úgy konfigurálta a hitelesítést.

A feliratkozást követően a kapcsolatok középpontjában meg kell jelennie a távoli erőforrásoknak.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Android-ügyfél használatáról, tekintse meg [az Android-ügyfél használatának első lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-android/)ismertető témakört.
