---
title: Azure N-Series NVIDIA GPU illesztőprogram-telepítő Windows rendszerhez
description: NVIDIA GPU-illesztőprogramok beállítása az Azure-ban Windows Servert vagy Windowst futtató N sorozatú virtuális gépekhez
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: vikancha
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 05487db1d0522634eda606dcb2ba22876fc341db
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557437"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-windows"></a>NVIDIA GPU-illesztőprogramok telepítése a Windows rendszerű N sorozatú virtuális gépeken 

Az NVIDIA GPU-k által támogatott Azure N-sorozatú virtuális gépek GPU-képességeinek kihasználásához az NVIDIA GPU-illesztőprogramokat kell telepítenie. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-windows.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez.

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, ez a cikk a támogatott operációs rendszereket, illesztőprogramokat és telepítési és ellenőrzési lépéseket tartalmazza. A Linux rendszerű [virtuális gépekhez](../linux/n-series-driver-setup.md)a manuális illesztőprogram beállítási információi is elérhetők.

Az alapszintű specifikációk, a tárolási kapacitások és a lemezek részleteiért lásd: [GPU Windowsos virtuális gépek méretei](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json). 

[!INCLUDE [virtual-machines-n-series-windows-support](../../../includes/virtual-machines-n-series-windows-support.md)]

## <a name="driver-installation"></a>Illesztőprogram telepítése

1. Kapcsolódjon Távoli asztal az egyes N sorozatú virtuális gépekhez.

2. Töltse le, bontsa ki és telepítse a Windows operációs rendszerhez támogatott illesztőprogramot.

Miután a GRID-illesztőprogram telepítése egy virtuális gépre megtörténik, újraindítás szükséges. A CUDA-illesztőprogram telepítése után a rendszer nem igényel újraindítást.

## <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Vegye figyelembe, hogy az NVIDIA Vezérlőpult csak a GRID-illesztőprogram telepítésével érhető el. Ha telepítette a CUDA-illesztőprogramokat, az NVIDIA Vezérlőpult nem lesz látható.

Az illesztőprogram telepítését a Eszközkezelő ellenőrizheti. Az alábbi példa a Tesla K80 kártya sikeres konfigurálását mutatja be egy Azure NC virtuális gépen.

![GPU-illesztőprogram tulajdonságai](./media/n-series-driver-setup/GPU_driver_properties.png)

A GPU-eszköz állapotának lekéréséhez futtassa az illesztőprogrammal telepített [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot.

1. Nyisson meg egy parancssort, és váltson a **C:\Program Files\NVIDIA Corporation\NVSMI** könyvtárba.

2. Futtassa az `nvidia-smi` parancsot. Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet jelenik meg. A **GPU-util** **0%-ot** mutat, ha jelenleg nem fut GPU-munkaterhelés a virtuális gépen. Az illesztőprogram verziószáma és a GPU adatai eltérhetnek a megjelenített adatoktól.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)  

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

A RDMA hálózati kapcsolat engedélyezhető a RDMA-kompatibilis N sorozatú virtuális gépeken, például az azonos rendelkezésre állási csoportokban vagy a virtuálisgép-méretezési csoportok egyetlen elhelyezési csoportjában telepített NC24r. A HpcVmDrivers bővítményt hozzá kell adni a RDMA-kapcsolatot engedélyező Windows hálózati eszközillesztők telepítéséhez. Ha hozzá szeretné adni a virtuálisgép-bővítményt egy RDMA-kompatibilis N sorozatú virtuális géphez, használja a Azure Resource Manager [Azure PowerShell](/powershell/azure/) parancsmagokat.

A legújabb 1,1 HpcVMDrivers bővítmény telepítése egy myVM nevű meglévő RDMA-kompatibilis virtuális gépen az USA nyugati régiójában:
  ```powershell
  Set-AzVMExtension -ResourceGroupName "myResourceGroup" -Location "westus" -VMName "myVM" -ExtensionName "HpcVmDrivers" -Publisher "Microsoft.HpcCompute" -Type "HpcVmDrivers" -TypeHandlerVersion "1.1"
  ```
  További információ: [virtuálisgép-bővítmények és-szolgáltatások a Windows rendszerhez](../extensions/features-windows.md).

A RDMA-hálózat támogatja a [Microsoft MPI](/message-passing-interface/microsoft-mpi) vagy az Intel MPI 5. x verzióban futó alkalmazások üzenet-átadási felületének (MPI) forgalmát. 


## <a name="next-steps"></a>Következő lépések

* Az NVIDIA Tesla GPU-k számára készült GPU-gyorsított alkalmazásokat fejlesztő fejlesztők is letölthetik és telepíthetik a legújabb [CUDA-eszközkészletet](https://developer.nvidia.com/cuda-downloads). További információ: a [CUDA telepítési útmutatója](https://docs.nvidia.com/cuda/cuda-installation-guide-microsoft-windows/index.html#axzz4ZcwJvqYi).
