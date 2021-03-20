---
title: Kapcsolódás Microsoft Store ügyfélhez – Azure
description: Kapcsolódás a Windows rendszerű virtuális asztalhoz az Microsoft Store-ügyfél használatával.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9bab2a11ff9e7907621087e5027929e3e42eaf29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91744794"
---
# <a name="connect-with-the-microsoft-store-client"></a>Kapcsolódás a Microsoft Store-ügyfélhez

>A következőkre vonatkozik: Windows 10.

Windows rendszerű virtuális asztali erőforrásokat is elérhet az eszközökön a Windows 10 rendszerben.

## <a name="install-the-microsoft-store-client"></a>Az Microsoft Store-ügyfél telepítése

Telepítheti az aktuális felhasználóhoz tartozó ügyfelet, amely nem követeli meg a rendszergazdai jogosultságokat. Azt is megteheti, hogy a rendszergazda telepítheti és konfigurálhatja az ügyfelet, hogy az eszköz összes felhasználója hozzáférhessen.

A telepítést követően a-ügyfél a Start menüből indítható el Távoli asztal keresésével.

Első lépésként [töltse le és telepítse az ügyfelet a Microsoft Storeból](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Előfizetés munkaterületre

Fizessen elő a rendszergazda által biztosított munkaterületre, hogy lekérje a számítógépen elérhető felügyelt erőforrások listáját.

Előfizetés munkaterületre:

1. A kapcsolatkezelő képernyőn koppintson a **+ Hozzáadás** elemre, majd koppintson a **munkaterületek** elemre.
2. Adja meg a munkaterület URL-címét a rendszergazda által megadott munkaterület URL-cím mezőbe. A munkaterület URL-címe lehet URL-cím vagy e-mail-cím.
   
   - Ha munkaterület URL-címét használja, használja azt az URL-címet, amelyet a rendszergazda adott Önnek.
   - Ha a Windows rendszerű virtuális asztalról csatlakozik, használja a következő URL-címek egyikét attól függően, hogy a szolgáltatás melyik verzióját használja:
       - Windowsos virtuális asztal (klasszikus): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Windowsos virtuális asztal: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Koppintson az **előfizetés** elemre.
4. Ha a rendszer kéri, adja meg a hitelesítő adatait.
5. A feliratkozás után a munkaterületek megjelennek a kapcsolatok központban.

A munkaterületek hozzáadhatók, módosíthatók vagy eltávolíthatók a rendszergazda által végrehajtott módosítások alapján.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Microsoft Store-ügyfél használatáról, tekintse meg [a Microsoft Store-ügyfél használatának első lépéseit](/windows-server/remote/remote-desktop-services/clients/windows/)ismertető témakört.