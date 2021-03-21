---
title: Az Azure Percept DK frissítése USB-kapcsolaton keresztül
description: Ismerje meg, hogyan frissítheti az Azure Percept DK-t USB-kapcsolaton keresztül
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101662377"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Az Azure Percept DK frissítése USB-kapcsolaton keresztül

Ebből az útmutatóból megtudhatja, hogyan hajthat végre USB-frissítést az Azure Percept DK Carrier táblájában.

## <a name="prerequisites"></a>Előfeltételek
- Számítógép egy elérhető USB-C vagy USB-porttal.
- Az Azure Percept DK (dev Kit) Carrier Board és az USB-c kábel USB-c kábellel való ellátása. Ha a gazdaszámítógép USB-porttal rendelkezik, de nem USB-C portot használ, USB-C-t is használhat USB-kábelként (külön megvásárolható).
- Telepítse a [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) -t (rendszergazdai hozzáférés szükséges).
- Telepítse a NXP UUU eszközt. [Töltse le a legújabb kiadási](https://github.com/NXPmicro/mfgtools/releases) uuu.exe fájlt (Windows rendszeren) vagy a UUU-fájlt (Linux rendszeren) az eszközök lapon.
- [Telepítse a 7 – zip fájlt](https://www.7-zip.org/). Ezt a szoftvert a rendszer a nyers képfájl XZ tömörített fájlból való kinyerésére fogja használni. Töltse le és telepítse a megfelelő. exe fájlt.

## <a name="steps"></a>Lépések
1.  Töltse le a következő [három USB-frissítési fájlt](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101-UEFI-***&lt; Version Number &gt;***. Raw. XZ
    - emmc_full.txt
    - Fast-hab-fw. Raw
 
1. Bontsa ki a pe101-UEFI-***&lt; Version &gt; Number**_. Raw fájlt a tömörített pe101_ * _&lt; &gt;_-UEFI verziószám * *. Raw. XZ fájlból. Nem tudja, hogyan kell kinyerni? Töltse le és telepítse a 7 – zip fájlt, majd kattintson a jobb gombbal a **. XZ** lemezképfájlra, és válassza ki a 7 – zip &gt; kibontást itt.

1. Másolja a következő három fájlt a UUU eszközt tartalmazó mappába:
    - Kibontott pe101-UEFI-***&lt; Version Number &gt;***. RAW fájl (a 2. lépésből).
    - emmc_full.txt (az 1. lépésből).
    - Fast-hab-fw. Raw (az 1. lépésből).
 
1. Kapcsolja be a fejlesztői készletet.
1. [Kapcsolódás a fejlesztői csomaghoz SSH-kapcsolaton keresztül](./how-to-ssh-into-percept-dk.md)
1. Nyisson meg egy Windows-parancssort (indítsa el a &gt; cmd parancsot) vagy egy Linux-terminált, és navigáljon arra a mappára, ahol a frissítési fájlok vannak tárolva. A frissítés elindításához futtassa a következő parancsot:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
A parancsok futtatása után egy üzenet jelenik meg, amely "Várakozás az eszközre..." a parancssorban. Ez várható, és folytassa a következő lépéssel.
    
1. Csatlakoztassa a dev Kit Carrier táblát a gazdagéphez USB-kábellel. Mindig csatlakoztassa a szállítói USB-C portot a gazdagép USB-C vagy USB-portjának portjához (USB-C-ről USB-re – A kábel külön megvásárolható), attól függően, hogy mely portok érhetők el. 
 
1. Az SSH/Putty terminálon írja be a következő parancsokat a dev Kit USB-módba állításához, majd indítsa újra a fejlesztői készletet.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Előfordulhat, hogy a gazdaszámítógép felismeri az eszközt, a frissítési folyamat pedig automatikusan elindul. Az állapot megtekintéséhez váltson vissza a parancssorba. A folyamat akár tíz percet is igénybe vehet, és a sikeres frissítés után megjelenik egy üzenet, amely a "sikeres 1 hiba 0" hibát jelzi.
 
1. Miután a frissítés befejeződött, kapcsolja ki a Szállítmányozói táblát. Húzza ki az USB-kábelt a SZÁMÍTÓGÉPRŐL.  Csatlakoztassa az Azure Percept-jövőkép modult a Carrier táblához az USB-kábel használatával.

1. Kapcsolja vissza a szolgáltatót.

## <a name="next-steps"></a>Következő lépések

A fejlesztői csomag frissítése sikeresen megtörtént. Továbbra is folytathatja a fejlesztést és a műveletet a fejlesztői készlet.