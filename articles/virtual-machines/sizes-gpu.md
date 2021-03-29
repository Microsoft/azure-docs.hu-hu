---
title: Azure VM-méretek – GPU | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző GPU-optimalizált méreteket. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 09d62ea5f2db77c14e8faff44de7fb3ce759c6fe
ms.sourcegitcommit: dae6b628a8d57540263a1f2f1cdb10721ed1470d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "105709735"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU-optimalizált virtuális gépek méretei

A GPU-ra optimalizált virtuálisgép-méretek olyan speciális virtuális gépek, amelyek egyetlen, több vagy tört GPU-val érhetők el. Ezek a méretek nagy számítási igényű, grafikus igényű és vizualizációs munkaterhelésekhez készültek. Ez a cikk a GPU-k, a vCPU, az adatlemezek és a hálózati adapterek számáról és típusáról nyújt információt. A rendszer a tároló átviteli sebességét és a hálózati sávszélességet is tartalmazza a csoportosítás minden egyes méretéhez.

- A [NCv3 sorozat](ncv3-series.md) és az [NC T4_v3 sorozatú](nct4-v3-series.md) méretek nagy számítási igényű GPU-gyorsított alkalmazásokhoz vannak optimalizálva. Ilyenek például a CUDA és a OpenCL-alapú alkalmazások, a szimulációk, az AI és a Deep learning. Az NC T4 v3 sorozat az NVIDIA Tesla T4 GPU-val és az AMD EPYC2 Rome processzorral kapcsolatos következtetésekre összpontosít. A NCv3 sorozat az NVIDIA Tesla V100 GPU-val rendelkező nagy teljesítményű számítástechnikai és AI-munkaterhelésekre összpontosít.

- A [NDv2-sorozat](ndv2-series.md) mérete a kibővíthető és kibővíthető Deep learning-alkalmazásokra koncentrál. A NDv2 sorozat az NVIDIA volta V100 és az Intel Xeon Platinum 8168 (Skylake) processzort használja.

- Az [NV-sorozat](nv-series.md) és a [NVv3-sorozat](nvv3-series.md) méretei a távoli vizualizációk, streaming-, játék-, kódolási és VDI-forgatókönyvekhez lettek optimalizálva, olyan keretrendszerek használatával, mint például az OpenGL és a DirectX. Ezeket a virtuális gépeket az NVIDIA Tesla M60 GPU támogatja.

- [NVv4 sorozat](nvv4-series.md) A virtuális gépek és a távoli vizualizációk számára optimalizált és tervezett virtuálisgép-méretek. A particionált GPU-k esetében a NVv4 a kisebb GPU-erőforrásokat igénylő számítási feladatok számára biztosít megfelelő méretet. Ezeket a virtuális gépeket az AMD Radeon ösztön MI25 GPU támogatja. A NVv4 virtuális gépek jelenleg csak a Windows vendég operációs rendszert támogatják.

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA vagy az AMD GPU-illesztőprogramokat.

- Az NVIDIA GPU-k által támogatott virtuális gépek esetén az [NVIDIA GPU illesztőprogram-bővítmény](./extensions/hpccompute-gpu-windows.md) telepíti a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](./extensions/hpccompute-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](./extensions/overview.md).

   Azt is megteheti, hogy manuálisan telepíti az NVIDIA GPU-illesztőprogramokat. Lásd: [NVIDIA GPU-illesztőprogramok telepítése a Windows rendszerű n sorozatú virtuális gépeken](./windows/n-series-driver-setup.md) , illetve az [NVIDIA GPU-illesztőprogramok telepítése az n sorozatú virtuális gépeken Linux rendszerű](./linux/n-series-driver-setup.md) , támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések esetén.

- Az AMD GPU-k által támogatott virtuális gépek esetén az [AMD GPU illesztőprogram-bővítmény](./extensions/hpccompute-amd-gpu-windows.md) telepíti a megfelelő AMD-illesztőprogramokat. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](./extensions/overview.md).

   Azt is megteheti, hogy manuálisan telepíti az AMD GPU-illesztőprogramokat. Lásd: az [AMD GPU-illesztőprogramok telepítése az N sorozatú, Windows rendszerű virtuális gépeken](./windows/n-series-amd-driver-setup.md) támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések esetén.

## <a name="deployment-considerations"></a>Telepítési szempontok

- Az N sorozatú virtuális gépek elérhetőségét lásd: [régiónként elérhető termékek](https://azure.microsoft.com/regions/services/).

- Az N sorozatú virtuális gépek csak a Resource Manager-alapú üzemi modellben helyezhetők üzembe.

- Az N sorozatú virtuális gépek különböznek a lemezük által támogatott Azure Storage-típustól. Az NC-és NV-alapú virtuális gépek csak a standard Disk Storage (HDD) által támogatott VM-lemezeket támogatják. Minden más GPU virtuális gép támogatja a standard Disk Storage és a prémium Disk Storage (SSD) által támogatott virtuálisgép-lemezeket.

- Ha több N sorozatú virtuális gépet szeretne üzembe helyezni, vegye fontolóra az utólagos elszámolású előfizetést vagy más vásárlási lehetőséget. Amennyiben [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) használ, csak korlátozott számú számítási magot használhat az Azure-ban.

- Előfordulhat, hogy az Azure-előfizetésében meg kell emelni a magok kvótáját (régiónként), és meg kell emelni az NC, a NCv2, a NCv3, az ND, a NDv2, az NV vagy a NVv2 magok külön kvótáját. A kvóta növeléséhez [Nyisson meg egy online ügyfélszolgálati kérést](../azure-portal/supportability/how-to-create-azure-support-request.md) díjmentesen. Az alapértelmezett korlátok az előfizetési kategóriától függően változhatnak.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
