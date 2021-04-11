---
title: Az Azure Percept dev Tools Pack telepítő áttekintése
description: További információ a fejlesztői eszközök csomag telepítőjének használatáról az Azure Percept való fejlett fejlesztés felgyorsításához
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: template-how-to
ms.openlocfilehash: f81f7922431f85cfc2a98261a128ba66d23a984f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608595"
---
# <a name="dev-tools-pack-installer-overview"></a>A dev Tools Pack telepítő áttekintése

A dev Tools Pack telepítője egy egyablakos megoldás, amely a fejlett intelligens peremhálózati megoldás fejlesztéséhez szükséges összes eszközt telepíti és konfigurálja.

## <a name="mandatory-tools"></a>Kötelező eszközök

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 vagy újabb](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](/python/api/overview/azure/ml/)

## <a name="optional-tools"></a>Választható eszközök

* [NVIDIA DEEPSTREAM SDK 5](https://developer.nvidia.com/deepstream-sdk) (eszközkészlet az NVIDIA-gyorsító megoldások fejlesztéséhez)
* [Intel OpenVino toolkit 2020,2](https://docs.openvinotoolkit.org/) (eszközkészlet az Intel-gyorssegédekhez készült megoldások fejlesztéséhez)
* [Lobe.ai](https://lobe.ai/)  
* [Streamlit](https://www.streamlit.io/)
* [Pytorch 1.4.0 (Windows) vagy 1.2.0 (Linux)](https://pytorch.org/)
* [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
* [Láncolás 5,2](https://chainer.org/)
* [Caffe](https://caffe.berkeleyvision.org/)
* [A CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit)
* [Microsoft Cognitive Toolkit 2.5.1](https://www.microsoft.com/research/product/cognitive-toolkit/?lang=fr_ca)

## <a name="known-issues"></a>Ismert problémák

- Az opcionális Cafe-telepítés meghiúsulhat, ha a Docker nem fut megfelelően. Ha a Cafe telepítését szeretné elvégezni, győződjön meg arról, hogy a Docker telepítve van és fut, mielőtt megkísérelte a Cafe telepítését a dev Tools Pack telepítőn keresztül.

- Nem kötelező a CUDA telepítése a nem kompatibilis rendszereken. Mielőtt megpróbálta telepíteni a [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) a dev Tools Pack telepítőjének használatával, ellenőrizze a rendszer kompatibilitását.

## <a name="docker-minimum-requirements"></a>Docker minimális követelményei

### <a name="windows"></a>Windows

- Windows 10 64 bites: Pro, Enterprise vagy Education (Build 16299 vagy újabb).

- A Hyper-V és a tárolók Windows-szolgáltatásait engedélyezni kell. A Hyper-V Windows 10 rendszeren való sikeres futtatásához a következő hardver-előfeltételek szükségesek:

    - 64 bites processzor [második szintű címfordítással (léc)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
    - 4 GB RAM-memória
    - A BIOS-beállításokban engedélyezni kell a BIOS-szintű hardveres virtualizálás támogatását. További információ: virtualizáció.

> [!NOTE]
> A Docker a Windows 10-es operációs rendszerhez készült Microsoft támogatási életciklusa alapján támogatja a Docker Desktop használatát Windows rendszeren. További információ: a Windows- [életciklusról szóló adatlap](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

További információ a [Docker Desktop Windows rendszeren történő telepítéséről](https://docs.docker.com/docker-for-windows/install/#install-docker-desktop-on-windows).

### <a name="mac"></a>Mac

- A Mac 2010-es vagy újabb modellnek kell lennie a következő attribútumokkal:
    - Intel processzor
    - Az Intel hardveres támogatása a memória-kezelési egység (MMU) virtualizálása, beleértve a kiterjesztett lapokat (EPT) és a nem korlátozott üzemmódot. A következő parancs egy terminálon való futtatásával ellenőrizze, hogy a számítógép rendelkezik-e ezzel a támogatással: ```sysctl kern.hv_support``` . Ha a Mac támogatja a hypervisor keretrendszert, a parancs kinyomtatja a parancsot ```kern.hv_support: 1``` .

- macOS 10,14 vagy újabb verzió (Mojave, Catalina vagy Big Sur). Javasoljuk, hogy a macOS legújabb verziójára frissítsen. Ha a macOS 10,15-es verzióra való frissítése után problémákat tapasztal, telepítenie kell a Docker Desktop legújabb verzióját, hogy kompatibilis legyen a macOS ezen verziójával.

- Legalább 4 GB RAM.

- Ne telepítse a VirtualBox-t a 4.3.30 verzió előtt – nem kompatibilis a Docker Desktop szolgáltatással.

- A telepítő nem támogatott az Apple M1-ben.

További információ a [Docker Desktop Mac számítógépen való telepítéséről](https://docs.docker.com/docker-for-mac/install/#system-requirements).

## <a name="launch-the-installer"></a>Indítsa el a telepítőt

Töltse le a fejlesztői eszközök csomag telepítőjét Windows, [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)vagy [Mac](https://go.microsoft.com/fwlink/?linkid=2132296) [rendszerre](https://go.microsoft.com/fwlink/?linkid=2132187). Indítsa el a telepítőt a platformnak megfelelően az alább leírtak szerint.

### <a name="windows"></a>Windows

1. A telepítővarázsló megnyitásához kattintson a **dev-Tools-Pack-Installer** elemre.

### <a name="mac"></a>Mac

1. A letöltés után helyezze át a **dev-Tools-Pack-Installer.app** fájlt az **alkalmazások** mappába.

1. A telepítővarázsló megnyitásához kattintson a **dev-Tools-Pack-Installer.app** elemre.

1. Ha a "nem azonosított fejlesztő" biztonsági párbeszédpanelt kapja:

    1. Lépjen a **Rendszerbeállítások**  ->  **biztonsági & adatvédelem**  ->  **általános** elemre, majd kattintson az **dev-Tools-Pack-Installer.app** lehetőség melletti **Megnyitás** gombra.
    1. Kattintson az elektron ikonra.
    1. A biztonság párbeszédpanelen kattintson a **Megnyitás** gombra.

### <a name="linux"></a>Linux

1. Ha a böngésző kéri, kattintson a **Save (Mentés** ) gombra a telepítő letöltésének befejezéséhez.

1. Végrehajtási engedélyek hozzáadása a **. appimage** fájlhoz:

    1. Nyisson meg egy Linux-terminált.

    1. Adja meg a következőt a terminálon a **letöltések** mappába való ugráshoz:

        ```bash
        cd ~/Downloads/
        ```

    1. A AppImage végrehajtható fájljának elkészítése:

        ```bash
        chmod +x Dev-Tools-Pack-Installer.AppImage
        ```

    1. Futtassa a telepítőt:

        ```bash
        ./Dev-Tools-Pack-Installer.AppImage
        ```

1. Végrehajtási engedélyek hozzáadása a **. appimage** fájlhoz:

    1. Kattintson a jobb gombbal a. appimage fájlra, és válassza a **Tulajdonságok** lehetőséget.
    1. Nyissa meg az **engedélyek** lapot.
    1. Jelölje be a következő jelölőnégyzetet a **fájl futtatásának engedélyezése programként**.
    1. A **Tulajdonságok** bezárásához és a **. appimage** fájl megnyitásához.

## <a name="run-the-installer"></a>A telepítő futtatása

1. A **dev Tools Pack telepítőjének telepítése** lapon kattintson a **licenc megtekintése** elemre a telepítőben található egyes szoftvercsomagok licencszerződésének megtekintéséhez. Ha elfogadja a licencszerződések feltételeit, jelölje be a jelölőnégyzetet, és kattintson a **tovább** gombra.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-license-agreements.png" alt-text="Licencszerződés képernyője a telepítőben.":::

1. A Microsoft adatvédelmi nyilatkozatának áttekintéséhez kattintson az **adatvédelmi nyilatkozatra** . Ha elfogadja az adatvédelmi nyilatkozat feltételeit, és diagnosztikai adatokat szeretne küldeni a Microsoftnak, válassza az **Igen** lehetőséget, és kattintson a **tovább** gombra. Ellenkező esetben válassza a **nem** lehetőséget, majd kattintson a **tovább** gombra.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-privacy-statement.png" alt-text="Adatvédelmi nyilatkozati szerződés képernyője a telepítőben.":::

1. Az **összetevők konfigurálása** lapon válassza ki a telepíteni kívánt választható eszközöket (a kötelező eszközöket alapértelmezés szerint telepíti a rendszer).

    1. Ha együttműködik az Azure Percept DK részét képező Azure Percept audio SoM szolgáltatással, akkor ügyeljen arra, hogy telepítse az Intel OpenVino Toolkit és a Miniconda3.

    1. A telepítés folytatásához kattintson a **telepítés** gombra.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-configure-components.png" alt-text="A telepítő képernyője a rendelkezésre álló szoftvercsomagok megjelenítéséhez.":::

1. Az összes kiválasztott összetevő sikeres telepítése után a varázsló továbblép a telepítővarázsló **befejezése** lapra. A telepítőből való kilépéshez kattintson a **Befejezés** gombra.

    :::image type="content" source="./media/dev-tools-installer/dev-tools-finish.png" alt-text="A telepítő befejezési képernyője.":::

## <a name="docker-status-check"></a>Docker-állapot-ellenőrzési

Ha a telepítő értesíti arról, hogy a Docker Desktop jó futású állapotban van, tekintse meg a következő lépéseket:

### <a name="windows"></a>Windows

1. A rendszertálca rejtett ikonjainak kibontása.

    :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Tálcán.":::

1. Ellenőrizze **, hogy fut**-e a Docker Desktop ikonja.

    :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker-állapot.":::

1. Ha a fenti ikon nem jelenik meg a rendszertálcán, indítsa el a Docker Desktopot a Start menüből.

1. Ha a Docker felszólítja az újraindításra, a rendszer lezárta a telepítőt, és újraindítja az újraindítást, és a Docker fut állapotban van. A rendszer a sikeresen telepített, harmadik féltől származó alkalmazásokat észleli, és nem telepíti automatikusan.

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [Azure Percept speciális fejlesztői tárházát](https://github.com/microsoft/azure-percept-advanced-development) , amely az Azure Percept DK fejlett fejlesztésének első lépéseiben nyújt segítséget.
