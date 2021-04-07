---
title: Azure N sorozatú AMD GPU illesztőprogram-telepítő Windows rendszerhez
description: AMD GPU-illesztőprogramok beállítása az Azure-ban Windows Servert vagy Windowst futtató N sorozatú virtuális gépekhez
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 62723a0fee6a3f696c517bc642fdac8cfa80a6b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557420"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Az AMD GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken

Az új Azure NVv4 sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat. Az [AMD GPU illesztőprogram-bővítmény](../extensions/hpccompute-amd-gpu-windows.md) egy NVv4-sorozatú virtuális gépen telepíti az AMD GPU-illesztőprogramokat. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [AMD GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-amd-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez.

Ha manuálisan telepíti az AMD GPU-illesztőprogramokat, ez a cikk a támogatott operációs rendszereket, illesztőprogramokat és telepítési és ellenőrzési lépéseket tartalmazza.

A NVv4 virtuális gépeken csak a Microsoft által közzétett GPU-illesztőprogramok támogatottak. Ne telepítse a GPU-illesztőprogramokat bármilyen más forrásból.

Az alapszintű specifikációk, a tárolási kapacitások és a lemezek részleteiért lásd: [GPU Windowsos virtuális gépek méretei](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json).



## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

| Operációs rendszer | Illesztő |
| -------- |------------- |
| Windows 10 Enterprise multi-session-Build 1909 <br/><br/>Windows 10 – Build 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (. exe) |

 > [!NOTE]
   >  Ha a Build 1903/1909-et használja, akkor előfordulhat, hogy az optimális teljesítmény érdekében frissítenie kell a következő csoportházirendet. Ezek a módosítások nem szükségesek más Windows-buildekhez.
   >  
   >  [Számítógép konfigurációja – >házirendek – >Windows-beállítások – >Felügyeleti sablonok->Windows-összetevők – >Távoli asztali szolgáltatások->távoli asztal munkamenet-gazdagép – >távoli munkamenet-környezet], állítsa be a házirendet [használja a WDDM Graphics Display Driver for Távoli asztal Connections] elemet a letiltáshoz.
   >  


## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódjon Távoli asztal az egyes NVv4-sorozatú virtuális gépekhez.

2. Ha el kell távolítania az illesztőprogram előző verzióját, majd le kell töltenie az AMD [CleanUp segédprogramot](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) , kérjük, ne használja az illesztőprogram előző verzióját tartalmazó segédprogramot.

3. Töltse le és telepítse a legújabb illesztőprogramot.

4. Indítsa újra a virtuális gépet.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Az illesztőprogram telepítését a Eszközkezelő ellenőrizheti. Az alábbi példa a Radeon ösztön MI25 kártya sikeres konfigurálását mutatja be egy Azure NVv4 virtuális gépen.
<br />

![Képernyőkép, amely a Radeon ösztön MI25 kártya sikeres konfigurálását mutatja be egy Azure NVv4 virtuális gépen.](./media/n-series-amd-driver-setup/device-manager.png)

A Dxdiag használatával ellenőrizheti a GPU megjelenítési tulajdonságait, beleértve a videó RAM-ját is. Az alábbi példa egy Azure NVv4 virtuális gépen található Radeon ösztön MI25 kártya 1/2-es partícióját mutatja be.
<br />
![Képernyőkép, amely egy Azure NVv4 virtuális gépen a Radeon ösztön MI25 kártya 1/2-os partícióját jeleníti meg.](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Ha a Windows 10 Build 1903-es vagy újabb verzióját futtatja, akkor a Dxdiag nem jelenít meg információt a "Display" (megjelenítés) lapon. Használja az összes információ mentése lehetőséget az alján, és a kimeneti fájl megjeleníti az AMD MI25 GPU-val kapcsolatos információkat.

![GPU-illesztőprogram tulajdonságai](./media/n-series-amd-driver-setup/dxdiag-details.png)
