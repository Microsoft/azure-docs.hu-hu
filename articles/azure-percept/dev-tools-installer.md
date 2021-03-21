---
title: Az Azure Percept dev Tools Pack telepítő áttekintése
description: További információ a fejlesztői eszközök csomag telepítőjének használatáról az Azure Percept való fejlett fejlesztés felgyorsításához
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 66498fabadc0784a4a4ab1c3762daaaa9a5738c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503215"
---
# <a name="dev-tools-pack-installer-overview"></a>A dev Tools Pack telepítő áttekintése

A dev Tools Pack telepítője egy egyablakos megoldás, amely az intelligens peremhálózati megoldás fejlesztéséhez szükséges összes eszközt telepíti és konfigurálja. Ha már telepítette az alább felsorolt szoftvercsomagok valamelyikét, a dev Tools Pack telepítője újratelepíti ezeket a csomagokat, hogy az eszközei konzisztensek legyenek a telepítő szoftver verziójával.

## <a name="mandatory-tools-installed"></a>Telepített kötelező eszközök

* [Visual Studio Code](https://code.visualstudio.com/)
* [Python 3,6 (Windows) vagy 3,5 (Linux)](https://www.python.org/)
* [Docker 19,03](https://www.docker.com/)
* [PIP3](https://pip.pypa.io/en/stable/user_guide/)
* [TensorFlow 1,13](https://www.tensorflow.org/)
* [Azure Machine Learning SDK 1,1](https://docs.microsoft.com/python/api/overview/azure/ml/)

## <a name="optional-tools-available-for-installation"></a>Választható elérhető eszközök a telepítéshez

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

- Az opcionális Cafe-telepítés meghiúsulhat, ha a Docker nem fut megfelelően a rendszeren. Ha a Cafe-t szeretné telepíteni, győződjön meg arról, hogy a Docker telepítve van és fut, mielőtt a fejlesztői eszközök csomag telepítője segítségével megpróbálkozott a Cafe-telepítéssel. 

- Nem kötelező a CUDA telepítése a nem kompatibilis rendszereken. Mielőtt megpróbálta telepíteni a [CUDA Toolkit 10.0.130](https://developer.nvidia.com/cuda-toolkit) a dev Tools Pack telepítőjének használatával, ellenőrizze a rendszer kompatibilitását.

## <a name="minimum-requirements"></a>Minimális követelmények

* Docker minimális követelményei:

    * Windows:
        * https://docs.docker.com/docker-for-windows/install/#system-requirements

        - Windows 10 64 bites: Pro, Enterprise vagy Education (Build 16299 vagy újabb).

             Windows 10 Home esetén lásd: a Docker Desktop telepítése Windows Home rendszerre.
           - A Hyper-V és a tárolók Windows-szolgáltatásait engedélyezni kell.
           - A következő hardverkövetelmények szükségesek ahhoz, hogy sikeresen futtassa a Hyper-V-t a Windows 10 rendszerű ügyfélen:

              - 64 bites processzor [második szintű címfordítással (léc)](https://en.wikipedia.org/wiki/Second_Level_Address_Translation)
              - 4 GB RAM-memória
              - A BIOS-beállításokban engedélyezni kell a BIOS-szintű hardveres virtualizálás támogatását. További információ: virtualizáció.

        > [!NOTE]
        > A Docker a Windows 10-es operációs rendszerhez készült Microsoft támogatási életciklusa alapján támogatja a Docker Desktop használatát Windows rendszeren. További információ: a Windows- [életciklusról szóló adatlap](https://support.microsoft.com/help/13853/windows-lifecycle-fact-sheet).

    * Mac:
        * https://docs.docker.com/docker-for-mac/install/#system-requirements
       
        A Docker Desktop sikeres telepítéséhez a Mac-nek a következő követelményeknek kell megfelelnie:
         
         - A **Mac-hardvernek 2010 vagy újabb, Intel processzorral ellátott modellnek kell lennie**, az Intel hardveres felügyeleti egység (MMU) virtualizálása, beleértve a kiterjesztett lapokat (EPT) és a korlátlan üzemmódot. A következő parancs egy terminálon való futtatásával ellenőrizze, hogy a számítógép rendelkezik-e ezzel a támogatással: ```sysctl kern.hv_support```

        Ha a Mac támogatja a hypervisor keretrendszert, a parancs kinyomtatja a parancsot ```kern.hv_support: 1``` .

         - **a macOS 10,14-es vagy újabb verziójúnak kell lennie**. Ez a Mojave, a Catalina vagy a Big Sur. Javasoljuk, hogy a macOS legújabb verziójára frissítsen.

        Ha a macOS 10,15-es verzióra való frissítése után problémákat tapasztal, telepítenie kell a Docker Desktop legújabb verzióját, hogy kompatibilis legyen a macOS ezen verziójával.

        - Legalább 4 GB RAM.
        - A 4.3.30 verzió előtti VirtualBox nem telepíthető, mert nem kompatibilis a Docker Desktop szolgáltatással.

        > [!NOTE]
        > A Docker támogatja a Docker Desktopot a macOS legújabb verzióiban. Ez a macOS jelenlegi kiadása, valamint az előző két kiadás. Mivel általánosan elérhető a macOS új fő verziói, a Docker nem támogatja a legrégebbi verziót, és támogatja a macOS legújabb verzióját (az előző két kiadáson felül). A Docker Desktop jelenleg a macOS Mojave, a macOS Catalina és a macOS Big Sur használatát támogatja.
        > 
        - A telepítő nem támogatott az Apple M1-ben.

## <a name="instructions"></a>Utasítások

1. Töltse le a fejlesztői eszközök csomag telepítőjét Windows, [Linux](https://go.microsoft.com/fwlink/?linkid=2132186)és [Mac](https://go.microsoft.com/fwlink/?linkid=2132296) [rendszerre](https://go.microsoft.com/fwlink/?linkid=2132187).

1. A platformtól függően a telepítő elindításának néhány különbsége lesz.

    1. Windows esetén:
    
        1. A telepítővarázsló megnyitásához kattintson a **dev-Tools-Pack-Installer** elemre.
        
    1. Mac rendszer esetén:
    
        1. A letöltés után helyezze át a Dev-Tools-Pack-Installer.app fájlt az alkalmazások mappába.
        
        1. A telepítővarázsló megnyitásához kattintson a **dev-Tools-Pack-Installer.app** elemre.
        
        1. Ha "azonosítatlan fejlesztő" biztonsági párbeszédpanelt kap:
        
            1. Lépjen a rendszerbeállítások – > biztonsági & adatvédelem – > általános elemre, és kattintson a "Dev-Tools-Pack-Installer.app" melletti "Megnyitás amúgy" gombra.
        
            1. Kattintson ismét az elektron ikonra a Dockon
        
            1. Kattintson a "Megnyitás" gombra a biztonság párbeszédpanelen.
    
    1. Linux esetén:
    
        1. Ha a böngésző kéri, kattintson a Save (Mentés) gombra a telepítő letöltésének befejezéséhez
        
        1. Végrehajtási engedélyek hozzáadása a **. appimage** fájlhoz 1. módszer (commandline):
            
            1. A Linux-terminál megnyitása
            
            1. Írja be a következőt a terminálon a letöltések mappába való ugráshoz
            
                1. CD ~/downloads/
                
            1. Írja be a következőt a terminálon, hogy a AppImage végrehajtható legyen
            
                1. chmod + x **dev-Tools-Pack-Installer. AppImage**
                
            1. A telepítő futtatásához írja be a következőt a terminálba
            
                1. ./Dev-Tools-Pack-Installer.AppImage
        
        1. Végrehajtási engedélyek hozzáadása a **. appimage** fájl 2. módszeréhez (UI):
        
            1. Kattintson a jobb gombbal a. appimage fájlra, és válassza a tulajdonságok lehetőséget.
            
            1. Megnyitási engedélyek lap
            
            1. Jelölje be a "fájl végrehajtásának engedélyezése programként" jelölőnégyzetet.
            
            1. A tulajdonságok bezárásához és a. appimage fájl megnyitásához

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

   1. Windows:
   
      1. Rendszertálca rejtett ikonjainak kibontása:
      
         1. A rendszertálca rejtett ikonjainak kibontása rejtett:

            :::image type="content" source="./media/dev-tools-installer/system-tray.png" alt-text="Tálcán.":::
         
         1. Ellenőrizze, hogy fut-e a Docker Desktop ikon:

            :::image type="content" source="./media/dev-tools-installer/docker-status-running.png" alt-text="Docker-állapot.":::
         
         1. Ha a fenti ikon nem jelenik meg a rendszertálcán, indítsa el a Docker Desktopot a Start menüből.
         
         1. Ha a Docker felszólítja az újraindításra, a rendszer lezárta a telepítőt, és újraindítja az újraindítást, és a Docker fut állapotban van. A rendszer a sikeresen telepített, harmadik féltől származó alkalmazásokat észleli, és nem telepíti automatikusan.

## <a name="next-steps"></a>Következő lépések

Tekintse meg az [Azure Percept speciális fejlesztői tárházát](https://github.com/microsoft/azure-percept-advanced-development) , amely az Azure Percept DK fejlett fejlesztésének első lépéseiben nyújt segítséget.