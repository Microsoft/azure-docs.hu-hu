---
title: NDv2 sorozat
description: A NDv2 sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 7a417def30fcb3a2b429151b9d4c9ba4152220a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669167"
---
# <a name="updated-ndv2-series"></a>Frissített NDv2 sorozat

A NDv2 sorozatú virtuális gép a GPU-család új, a legigényesebb GPU-gyorsított mesterséges intelligenciához, a gépi tanuláshoz, a szimulációs és a HPC számítási feladatokhoz készült.

A NDv2 8 NVIDIA Tesla V100 NVLINK csatlakoztatott GPU-val rendelkezik, amelyek mindegyike 32 GB GPU memóriával rendelkezik. Az egyes NDv2 virtuális gépekhez 40 nem hiperszálas Intel Xeon Platinum 8168 (Skylake) mag és 672 GiB a rendszermemória.

A NDv2-példányok kiváló teljesítményt nyújtanak a HPC-és AI-munkaterhelésekhez, amelyek a CUDA GPU-val optimalizált számítási kerneleket, valamint a GPU-gyorsítást támogató számos mesterséges intelligencia, ML és analitikai eszközt támogatják, például a TensorFlow, a Pytorch, a Cafe, a RAPIDS és más keretrendszerek.

Kritikus fontosságú, hogy a NDv2 mind a számítási szempontból intenzív felskálázás (8 GPU-s virtuális gép kihasználása), mind a Kibővítés (több virtuális gép a közös munkával együtt) munkaterhelések használatával készült. A NDv2 sorozat mostantól a 100-Gigabit InfiniBand EDR háttér-hálózatkezelést támogatja, hasonlóan a HB-sorozathoz elérhető HPC virtuális gépekhez, így lehetővé teszi a nagy teljesítményű fürtözést párhuzamos forgatókönyvek esetén, beleértve az AI és a ML elosztott képzését is. Ez a háttérrendszer támogatja az összes jelentősebb InfiniBand-protokollt, beleértve az NVIDIA NCCL2-könyvtárainak használatát is, ami lehetővé teszi a GPU-k zökkenőmentes fürtözését.

> [!IMPORTANT]
> Ha [engedélyezi a InfiniBand](./workloads/hpc/enable-infiniband.md) a ND40rs_v2 virtuális gépen, használja a 4.7-1.0.0.1 Mellanox OFED-illesztőprogramot.
>
> A GPU-memória megnövekedett mérete miatt az új ND40rs_v2 virtuális gépen a [2. generációs virtuális gépek](./generation-2.md) és a Piactéri rendszerképek használata szükséges. 
>
> Megjegyzés: a GPU memóriával rendelkező ND40s_v2 a már nem érhető el az előzetes verzióhoz, és a frissített ND40rs_v2 felülírt.

<br>

[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
InfiniBand: támogatott<br>
NVIDIA NVLink Interconnect: támogatott<br>
<br>

| Méret | vCPU | Memória: GiB | Temp Storage (SSD): GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati sávszélesség | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](./extensions/hpccompute-gpu-linux.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](./extensions/overview.md).

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, tekintse [meg a következőt: N-sorozat GPU-illesztőprogram beállítása Linux rendszerhez](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
