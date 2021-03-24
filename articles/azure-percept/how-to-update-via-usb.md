---
title: Az Azure Percept DK frissítése USB-kapcsolaton keresztül
description: Ismerje meg, hogyan frissítheti az Azure Percept DK-t USB-kapcsolaton keresztül
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104887750"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Az Azure Percept DK frissítése USB-kapcsolaton keresztül

Bár a over-the-Air (OTA) frissítések használata a legjobb módszer a fejlesztői csomag operációs rendszerének és a belső vezérlőprogram naprakészen tartására, vannak olyan forgatókönyvek, ahol a fejlesztői csomag USB-kapcsolaton keresztül történő frissítése (vagy "villogás") szükséges:

- A kapcsolódási vagy egyéb technikai problémák miatt az OTA-frissítés nem lehetséges
- Az eszközt vissza kell állítani a gyári állapotra.

Ez az útmutató bemutatja, hogyan frissítheti a fejlesztői csomag operációs rendszerét és belső vezérlőprogramja USB-kapcsolaton keresztül.

> [!WARNING]
> A fejlesztői csomag USB-kapcsolaton keresztüli frissítése törli az eszközön lévő összes meglévő adatát, beleértve az AI-modelleket és a tárolókat is.
>
> Kövesse az összes utasítást a sorrendben. A lépések kihagyása használhatatlan állapotba helyezheti a fejlesztői készletet.

## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Percept DK
- Egy Windows, Linux vagy OS X rendszerű gazdagép, amely Wi-Fi képességgel és elérhető USB-C vagy USB-porttal rendelkezik
- USB-C – USB-kábel (opcionális, külön megvásárolható)
- Az [Azure PERCEPT DK telepítési felületén](./quickstart-percept-dk-set-up.md) létrehozott ssh-bejelentkezés

## <a name="download-software-tools-and-update-files"></a>Szoftvereszközök letöltése és fájlok frissítése

1. [NXP UUU eszköz](https://github.com/NXPmicro/mfgtools/releases). Töltse le a **legújabb kiadási** uuu.exe fájlt (Windows rendszeren) vagy a UUU-fájlt (Linux rendszeren) az **eszközök** lapon.

1. [7 – zip](https://www.7-zip.org/). Ezt a szoftvert a rendszer a nyers képfájl XZ tömörített fájlból való kinyerésére fogja használni. Töltse le és telepítse a megfelelő. exe fájlt.

1. [Töltse le a frissítési fájlokat](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Győződjön meg arról, hogy mindhárom Build-összetevő megtalálható:
    - Azure-Percept-DK-*&lt; Version Number &gt;*. Raw. XZ
    - Fast-hab-fw. Raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>A környezet kialakítása

1. Hozzon létre egy mappát vagy könyvtárat a gazdaszámítógépen olyan helyen, amely könnyen elérhető a parancssorból.

1. Másolja a UUU eszközt (**uuu.exe** vagy **UUU**) az új mappába.

1. Bontsa ki az **Azure-Percept-DK-*&lt; Version Number &gt;*. Raw** fájlt a tömörített fájlból. ehhez kattintson a jobb gombbal az **Azure-Percept-DK-*&lt; Version Number &gt;*. Raw. XZ** elemre, és válassza ki a **7 – zip** - &gt; **kivonatot itt**.

1. Helyezze át a kinyert **Azure-Percept-DK-*&lt; Version Number &gt;*. Raw** fájlt, a **Fast-hab-fw. Raw** fájlt, és **emmc_full.txt** a UUU eszközt tartalmazó mappába.

## <a name="update-your-device"></a>Az eszköz frissítése

1. [SSH-t a fejlesztői csomagba](./how-to-ssh-into-percept-dk.md).

1. Ezután nyisson meg egy Windows-parancssort (**indítsa el** a  >  **cmd** eszközt) vagy egy Linux-terminált, és navigáljon arra a mappára, ahol a frissítési fájlokat és a UUU eszközt tárolja. Adja meg a következő parancsot a parancssorban vagy a terminálban, hogy felkészítse a számítógépet a Flash eszköz fogadására:

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Válassza le az Azure Percept-jövőkép eszközt a szállítói tábla USB-C portjáról.

1. Csatlakoztassa a mellékelt USB-C kábelt a szállítói kártya USB-C portjához és a gazdagép USB-C portjához. Ha a számítógép csak USB-porttal rendelkezik, csatlakoztassa az USB-C-t USB-kábelhez (külön megvásárolható) a Szállítmányozói táblához és a gazdagéphez.

1. Az SSH-ügyfél parancssorába írja be a következő parancsokat:

    1. Az eszköz beállítása USB-frissítési módra:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Indítsa újra az eszközt. Ekkor megkezdődik a frissítés telepítése.

        ```bash
        sudo reboot -f
        ```

1. Váltson vissza a másik parancssorba vagy terminálra. A frissítés befejezésekor a következő üzenet jelenik meg ```Success 1    Failure 0``` :

    > [!NOTE]
    > A frissítés után a rendszer visszaállítja az eszközt a gyári beállításokra, és elveszti a Wi-Fi kapcsolatát és az SSH-bejelentkezést.

1. Miután a frissítés befejeződött, kapcsolja ki a Szállítmányozói táblát. Húzza ki az USB-kábelt a SZÁMÍTÓGÉPRŐL.  

## <a name="next-steps"></a>Következő lépések

Az eszköz újrakonfigurálásához az [Azure PERCEPT DK telepítési felületén](./quickstart-percept-dk-set-up.md) keresztül dolgozhat.