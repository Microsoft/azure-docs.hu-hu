---
title: Kapcsolódás az Azure Percept DK-hoz a soros használatával
description: Megtudhatja, hogyan állíthat be soros csatlakozást az Azure Percept DK-hoz a PuTTY és egy USB – TTL soros kábellel
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 93b8ab0ce53202402e86b059abe3c600590d549e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101662324"
---
# <a name="connect-to-your-azure-percept-dk-over-serial"></a>Kapcsolódás az Azure Percept DK-hoz a soros használatával

Kövesse az alábbi lépéseket az Azure Percept DK soros kapcsolatának [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)használatával történő beállításához.

> [!WARNING]
> Ne **próbálja meg** a fejlesztői készlet a soros kapcsolaton keresztül csatlakozni, kivéve a szélsőséges meghibásodási esetekben (például az eszköz befalazott). A szállítói fedélzeten a soros kábel csatlakoztatása nagyon nehéz, és megszakítja Wi-Fi antenna kábeleit.

## <a name="prerequisites"></a>Előfeltételek

- [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)
- Gazdagép SZÁMÍTÓGÉPe
- Azure Percept DK
- [USB – TTL soros kábel](https://www.adafruit.com/product/954)

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/usb-serial-cable.png" alt-text="USB – TTL soros kábel.":::

## <a name="initiate-the-serial-connection"></a>A soros kapcsolat kezdeményezése

1. Ha a szállítmányozó egy 80/20-as vasúthoz csatlakozik, távolítsa el a vasúti hálózatról a hexadecimális kulcs használatával (a fejlesztői készlet üdvözlő kártyán található).

1. Távolítsa el a szállítói fedélzeti ház alján található csavart, és bontsa ki az alaplapot.

    > [!WARNING]
    > Az alaplap eltávolítása megszakítja Wi-Fi antenna kábeleit. Ne **folytassa a** soros kapcsolattal, kivéve, ha az utolsó megoldás az eszköz helyreállítására.

1. Távolítsa el a hűtőbordát.

1. Távolítsa el az áthidaló táblát a GPIO-PIN-kódokból.

    > [!TIP]
    > Jegyezze fel az áthidaló tábla tájolását a eltávolítása előtt. Például rajzoljon egy nyilat, vagy csatoljon egy matricát az áthidaló táblához, amely az áramkörre mutat. Az áthidaló tábla nincs bekötve, és a szállítói tábla újraösszeállításakor véletlenül visszafelé is kapcsolódhat.

1. Csatlakoztassa az [USB-t a TTL soros kábelhez](https://www.adafruit.com/product/954) az alaplapon az alábbi ábrán látható GPIO-PIN-kódokhoz. Vegye figyelembe, hogy a piros drót nincs csatlakoztatva.

    - Csatlakoztassa a fekete kábelt (GND) a 6. PIN-kódhoz.
    - Csatlakoztassa a fehér kábelt (RX) a 8. PIN-kódhoz.
    - Csatlakoztassa a zöld kábelt (TX) a 10. PIN-kód eléréséhez.

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/serial-connection-carrier-board.png" alt-text="Szállítói tábla soros PIN-kódjának kapcsolatai.":::

1. Kapcsolja be a fejlesztői készlet, és csatlakoztassa a soros kábel USB-oldalát a számítógéphez.

1. A Windows rendszerben válassza a **Start**  ->  **Windows Update beállítások**  ->  **nézet választható frissítések**  ->  **illesztőprogram-frissítések** lehetőséget. A listában keresse meg az USB-frissítés sorozatszámát, jelölje be a mellette található jelölőnégyzetet, majd kattintson a **letöltés és telepítés** gombra.  

1. Ezután nyissa meg a Windows Eszközkezelő (**Start**  ->  **Eszközkezelő**). Nyissa meg a **portok** **menüpontot**, és kattintson az **USB-ről UART** -ra a tulajdonságok megnyitásához. Vegye figyelembe, hogy az eszköz melyik COM-porthoz csatlakozik.

1. Kattintson a **Portbeállítások** fülre. Győződjön meg arról, hogy a **bitek másodpercenkénti** értéke 115200.

1. Nyissa meg a PuTTY eszközt. Adja meg a következőt, majd kattintson a **Megnyitás** gombra a fejlesztői készlet való kapcsolódáshoz a soros használatával:

    1. Soros vonal: COM [port #]
    1. Sebesség: 115200
    1. Kapcsolat típusa: soros

    :::image type="content" source="./media/how-to-connect-to-percept-dk-over-serial/putty-serial-session.png" alt-text="A PuTTY munkamenet-ablaka kijelölt soros paraméterekkel.":::

## <a name="next-steps"></a>Következő lépések

Ha egy nem rendszerindító eszközt szeretne a soros [USB-ről TTL soros kábellel](https://www.adafruit.com/product/954)frissíteni, tekintse meg az USB-frissítési útmutatót a nem szabványos helyzetekben.

[comment]: # (Adja meg az USB-frissítési útmutató hivatkozását, ha elérhető.)