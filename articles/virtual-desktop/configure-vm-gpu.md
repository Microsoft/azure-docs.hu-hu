---
title: A GPU konfigurálása a Windows rendszerű virtuális asztali környezethez – Azure
description: A GPU-gyorsított renderelés és a kódolás engedélyezése a Windows Virtual Desktopban.
author: gundarev
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: f95b9c1615cc58d9cc0589bad98c7315e571686e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709463"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Windows Virtual Desktop grafikusprocesszor-gyorsításának (GPU-gyorsításának) konfigurálása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/configure-vm-gpu-2019.md).

A Windows virtuális asztal támogatja a GPU-gyorsított renderelést és kódolást az alkalmazások teljesítményének és méretezhetőségének növelése érdekében. A GPU-gyorsítás különösen fontos a nagy grafikai igényű alkalmazások esetében.

Az ebben a cikkben található utasítások alapján hozzon létre egy GPU-ra optimalizált Azure-beli virtuális gépet, adja hozzá a gazdagéphez, és konfigurálja úgy, hogy GPU-gyorsítást használjon a rendereléshez és a kódoláshoz. Ez a cikk azt feltételezi, hogy már van konfigurálva a Windows rendszerű virtuális asztali bérlő.

## <a name="select-an-appropriate-gpu-optimized-azure-virtual-machine-size"></a>A megfelelő GPU-ra optimalizált Azure-beli virtuális gépek méretének kiválasztása

Válasszon egy Azure-beli [NV-sorozat](../virtual-machines/nv-series.md), [NVv3-sorozat](../virtual-machines/nvv3-series.md)vagy [NVv4-sorozatú](../virtual-machines/nvv4-series.md) VM-méretet. Ezek az alkalmazások és az asztali virtualizálás számára vannak kialakítva, és lehetővé teszik a legtöbb alkalmazás és a Windows felhasználói felületének a GPU-gyorsítását. A gazdagép-készlet megfelelő választása számos tényezőtől függ, többek között az alkalmazás számítási feladataitól, a felhasználói élmény kívánt minőségétől és a költséghatékonyságtól. Általánosságban elmondható, hogy a nagyobb és nagyobb kapacitású GPU-k jobb felhasználói élményt nyújtanak egy adott felhasználói sűrűségben, míg a kisebb és a többtényezős GPU-méretek részletesebben szabályozzák a költségeket és a minőséget.

>[!NOTE]
>Az Azure NC-, NCv2-, NCv3-, ND-és NDv2-sorozatú virtuális gépek általában nem megfelelőek a Windows rendszerű virtuális asztali munkamenet-gazdagépek számára. Ezek a virtuális gépek speciális, nagy teljesítményű számítási és gépi tanulási eszközökhöz vannak szabva, például NVIDIA CUDA-mel. Nem támogatják a GPU-gyorsítást a legtöbb alkalmazáshoz vagy a Windows felhasználói felületéhez.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Címkészlet létrehozása, a virtuális gép üzembe helyezése és az alkalmazás csoportjának konfigurálása

Hozzon létre egy új címkészletet a kiválasztott méretű virtuális géppel. Útmutatásért lásd [: oktatóanyag: állomáslista létrehozása a Azure Portal](./create-host-pools-azure-marketplace.md).

A Windows virtuális asztal támogatja a GPU-gyorsított renderelést és kódolást a következő operációs rendszereken:

* Windows 10 1511-es vagy újabb verzió
* Windows Server 2016 vagy újabb

Az új címkészlet létrehozásakor is konfigurálnia kell egy alkalmazás-csoportot, vagy az alapértelmezett asztali alkalmazást ("asztali alkalmazáscsoport") kell használnia. Útmutatásért lásd [: oktatóanyag: alkalmazás-csoportok kezelése a Windows rendszerű virtuális asztali gépeken](./manage-app-groups.md).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Támogatott grafikus illesztőprogramok telepítése a virtuális gépen

Ahhoz, hogy kihasználhassa az Azure N sorozatú virtuális gépek GPU-képességeit a Windows Virtual Desktopban, telepítenie kell a megfelelő grafikus illesztőprogramokat. Illesztőprogramok telepítéséhez kövesse a [támogatott operációs rendszerek és illesztőprogramok](../virtual-machines/sizes-gpu.md#supported-operating-systems-and-drivers) utasításait. Csak az Azure által terjesztett illesztőprogramok támogatottak.

* Az Azure NV-sorozat-vagy NVv3-sorozatú virtuális gépek esetében csak az NVIDIA GRID-illesztőprogramok, és nem az NVIDIA CUDA-illesztőprogramok támogatják a GPU-gyorsítást a legtöbb alkalmazáshoz és a Windows felhasználói felületéhez. Ha úgy dönt, hogy manuálisan telepíti az illesztőprogramokat, ügyeljen rá, hogy telepítse a GRID-illesztőprogramokat. Ha az Azure virtuálisgép-bővítmény használatával telepíti az illesztőprogramokat, a rendszer automatikusan telepíti a GRID-illesztőprogramokat ezekhez a virtuálisgép-méretekhez.
* Az Azure NVv4 sorozatú virtuális gépek esetében telepítse az Azure által biztosított AMD-illesztőprogramokat. Ezeket automatikusan telepítheti az Azure virtuálisgép-bővítmény használatával, vagy manuálisan is telepítheti őket.

Az illesztőprogram telepítése után szükség van egy virtuális gép újraindítására. A fenti utasításokban található ellenőrzési lépések segítségével ellenőrizze, hogy a grafikus illesztőprogramok telepítése sikeres volt-e.

## <a name="configure-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazások renderelésének konfigurálása

Alapértelmezés szerint a többmunkamenetes konfigurációkban futó alkalmazások és asztali számítógépek a PROCESSZORral jelennek meg, és nem használják fel a rendelkezésre álló GPU-ket a rendereléshez. Csoportházirend konfigurálása a munkamenet-gazdagép számára a GPU-gyorsított renderelés engedélyezéséhez:

1. Kapcsolódjon a virtuális gép asztalához helyi rendszergazdai jogosultságokkal rendelkező fiókkal.
2. Nyissa meg a Start menüt, és írja be a "gpedit. msc" parancsot a Csoportházirend-szerkesztő megnyitásához.
3. Navigáljon a fában a **Számítógép konfigurációja**  >  **Felügyeleti sablonok**  >  **Windows-összetevők**  >  **Távoli asztali szolgáltatások**  >  **Távoli asztal munkamenet-gazdagép**  >  **távoli munkamenet-környezete**.
4. Válassza a házirend a **hardver grafikus adapterek használata minden távoli asztali szolgáltatások munkamenethez** lehetőséget, és állítsa be ezt a házirendet **úgy, hogy engedélyezze** a GPU renderelést a távoli munkamenetben.

## <a name="configure-gpu-accelerated-frame-encoding"></a>GPU-gyorsított keret kódolásának konfigurálása

Távoli asztal kódolja az alkalmazások és az asztali számítógépek által megjelenített összes grafikát (akár GPU-val, akár CPU-val) a Távoli asztal ügyfeleknek való továbbításhoz. Ha a képernyő egy részét gyakran frissítik, a képernyő ezen része egy videó kodekkel (H. 264/AVC) van kódolva. Alapértelmezés szerint a Távoli asztal nem használ elérhető GPU-ket ehhez a kódoláshoz. Konfigurálja Csoportházirend a munkamenet-gazdagép számára a GPU-gyorsított keret kódolásának engedélyezéséhez. Folytassa a fenti lépéseket:

>[!NOTE]
>A GPU-gyorsított keret kódolása nem érhető el a NVv4 sorozatú virtuális gépeken.

1. Válassza a házirend a **H. 264/AVC hardveres kódolás beállítása Távoli asztal kapcsolatok számára** lehetőséget, és állítsa be ezt a házirendet **úgy,** hogy engedélyezze az AVC/H. 264 hardveres kódolást a távoli munkamenetben.

    >[!NOTE]
    >A Windows Server 2016-ben állítsa be a beállítást, hogy a rendszer mindig az **AVC hardveres kódolást** **próbálja** meg használni.

2. Most, hogy a csoportházirendek szerkesztése megtörtént, kényszerítse a csoportházirend frissítését. Nyissa meg a parancssort és írja be a következőt:

    ```cmd
    gpupdate.exe /force
    ```

3. Jelentkezzen ki a Távoli asztal-munkamenetből.

## <a name="configure-fullscreen-video-encoding"></a>Teljes képernyős videó kódolásának konfigurálása

Ha gyakran használ olyan alkalmazásokat, amelyek nagy képarányú tartalmat hoznak létre, például 3D-modellezést, CAD/CAM-és video-alkalmazásokat, akkor dönthet úgy, hogy egy teljes képernyős kódolást engedélyez egy távoli munkamenetben. A teljes képernyős videó profil magasabb szintű képarányt és jobb felhasználói élményt biztosít az ilyen alkalmazások számára a hálózati sávszélesség, valamint a munkamenet-állomás és az ügyfél erőforrásainak rovására. A teljes képernyős videó kódolásához javasolt a GPU-gyorsított keret kódolása. Konfigurálja Csoportházirend a munkamenet-gazdagép számára a teljes képernyős videó kódolásának engedélyezéséhez. Folytassa a fenti lépéseket:

1. Válassza ki a szabályzatot **prioritásként h. 264/avc 444 grafikus módban távoli asztal kapcsolatokhoz** , és állítsa be ezt a házirendet úgy, hogy az **engedélyezze** a h. 264/AVC 444 kodek kényszerítését a távoli munkamenetben.
2. Most, hogy a csoportházirendek szerkesztése megtörtént, kényszerítse a csoportházirend frissítését. Nyissa meg a parancssort és írja be a következőt:

    ```cmd
    gpupdate.exe /force
    ```

3. Jelentkezzen ki a Távoli asztal-munkamenetből.
## <a name="verify-gpu-accelerated-app-rendering"></a>GPU-gyorsított alkalmazások megjelenítésének ellenőrzése

Annak ellenőrzéséhez, hogy az alkalmazások a GPU-t használják a rendereléshez, próbálkozzon a következők bármelyikével:

* Az NVIDIA GPU-val rendelkező Azure-beli virtuális gépek esetében használja az `nvidia-smi` [illesztőprogram-telepítés ellenőrzése](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation) a GPU-használat ellenőrzéséhez az alkalmazások futtatásakor című témakörben leírtak szerint.
* A támogatott operációsrendszer-verziók esetében a Feladatkezelő segítségével keresse meg a GPU-kihasználtságot. Válassza ki a GPU-t a "teljesítmény" lapon annak megtekintéséhez, hogy az alkalmazások használják-e a GPU-t.

## <a name="verify-gpu-accelerated-frame-encoding"></a>GPU-gyorsított keret kódolásának ellenőrzése

Annak ellenőrzése, hogy a Távoli asztal GPU-gyorsított kódolást használ-e:

1. Kapcsolódjon a virtuális gép asztalához a Windows Virtual Desktop ügyfélprogram használatával.
2. Indítsa el a Eseménynapló, és nyissa meg a következő csomópontot: az **alkalmazások és a szolgáltatások naplózzák** a  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operational**
3. Annak megállapításához, hogy a GPU-gyorsított kódolás használatban van-e, keresse meg a 170-es AZONOSÍTÓJÚ eseményt. Ha a "AVC hardveres kódoló engedélyezve: 1" látható, akkor a rendszer GPU-kódolást használ.

## <a name="verify-fullscreen-video-encoding"></a>Teljes képernyős videó kódolásának ellenőrzése

Annak ellenőrzése, hogy Távoli asztal a teljes képernyős videó kódolását használja-e:

1. Kapcsolódjon a virtuális gép asztalához a Windows Virtual Desktop ügyfélprogram használatával.
2. Indítsa el a Eseménynapló, és nyissa meg a következő csomópontot: az **alkalmazások és a szolgáltatások naplózzák** a  >  **Microsoft**  >  **Windows**  >  **RemoteDesktopServices-RdpCoreCDV**  >  **Operational**
3. Annak megállapításához, hogy a teljes videó kódolása használatban van-e, keresse meg a 162-es AZONOSÍTÓJÚ eseményt. Ha a "AVC elérhető: 1 kezdeti profil: 2048" jelenik meg, akkor az AVC 444 használatos.

## <a name="next-steps"></a>Következő lépések

Ezeknek az utasításoknak a GPU-gyorsítással együtt kell működniük egy adott munkamenet-gazdagépen (egy virtuális gépen). Néhány további megfontolandó szempont a GPU-gyorsítás nagyobb gazdagép-készleten való engedélyezéséhez:

* Vegye fontolóra egy virtuálisgép-bővítmény használatát, amely leegyszerűsíti az illesztőprogramok telepítését és a frissítéseket a több virtuális [gépen](../virtual-machines/extensions/overview.md) keresztül. Használja az NVIDIA [GPU illesztőprogram-bővítményt](../virtual-machines/extensions/hpccompute-gpu-windows.md) az NVIDIA GPU-val rendelkező virtuális gépekhez, és használja az [AMD GPU illesztőprogram-BŐVÍTMÉNYt](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md) az AMD GPU-val rendelkező virtuális gépekhez.
* Vegye fontolóra Active Directory Csoportházirend használatát a csoportházirend konfigurálásának leegyszerűsítéséhez több virtuális gép között. További információ a Csoportházirend telepítéséről a Active Directory tartományban: [csoportházirend objektumok használata](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731212(v=ws.11)).