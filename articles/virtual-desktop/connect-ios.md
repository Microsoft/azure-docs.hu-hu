---
title: Kapcsolódás a Windows rendszerű virtuális asztalhoz iOS-Azure-ból
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz az iOS-ügyfél használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 02/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 87bb2cc53ce056552e8f44aac4ade96e603a8787
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230041"
---
# <a name="connect-to-windows-virtual-desktop-with-the-ios-client"></a>Kapcsolódás a Windows rendszerű virtuális asztalhoz az iOS-ügyféllel

> A következőkre vonatkozik: iOS 13,0 vagy újabb. IPhone-, iPad-és iPod Touch-kompatibilis.

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/connect-ios-2019.md).

A Windows rendszerű virtuális asztali erőforrásokat a letölthető ügyféllel is elérheti iOS-eszközéről. Ebből az útmutatóból megtudhatja, hogyan állíthatja be az iOS-ügyfelet.

## <a name="install-the-ios-client"></a>Az iOS-ügyfél telepítése

Első lépésként [töltse le](https://aka.ms/rdios) és telepítse az ügyfelet iOS-eszközén.

## <a name="subscribe-to-a-feed"></a>Előfizetés hírcsatornára

Fizessen elő a rendszergazda által biztosított hírcsatornára az iOS-eszközön elérhető felügyelt erőforrások listájának lekéréséhez.

Előfizetés a hírcsatornára:

1. A Csatlakozáskezelő területen koppintson a **+** elemre, majd koppintson a **munkaterület hozzáadása** lehetőségre.
2. Írja be a hírcsatorna URL-címét a **hírcsatorna URL-címe** mezőbe. A hírcsatorna URL-címe lehet URL vagy e-mail-cím.
   - Ha URL-címet használ, használja a rendszergazdát. Általában az URL-cím <https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery> .
   - E-mail-cím használatához adja meg az e-mail-címét. Ez azt jelzi, hogy az ügyfél megkeresi az e-mail-címéhez tartozó URL-címet, ha a rendszergazda konfigurálta a kiszolgálót.
   - A US Gov-portálon keresztüli kapcsolódáshoz használja a következőt: <https://rdweb.wvd.azure.us/api/arm/feeddiscovery> .
3. Koppintson a **tovább** gombra.
4. Ha a rendszer kéri, adja meg a hitelesítő adatait.
   - A **Felhasználónév mezőben** adja meg a felhasználónevet, amely jogosult az erőforrások elérésére.
   - A **Password (jelszó**) mezőben adja meg a felhasználónévhez tartozó jelszót.
   - Az is előfordulhat, hogy további tényezőket kell megadnia, ha a rendszergazda úgy konfigurálta a hitelesítést.
5. Koppintson a **Mentés** gombra.

Ezután a Csatlakozáskezelő megjeleníti a távoli erőforrásokat.

Miután előfizetett egy hírcsatornára, a hírcsatorna tartalma automatikusan frissülni fog. A rendszergazda által végrehajtott módosítások alapján hozzáadhatók, módosíthatók vagy eltávolíthatók az erőforrások.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az iOS-ügyfél használatáról, tekintse meg az iOS-ügyfél dokumentációjának használatának [első lépéseit](/windows-server/remote/remote-desktop-services/clients/remote-desktop-ios/) .
