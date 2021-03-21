---
title: NCv3 sorozat – Azure Virtual Machines
description: A NCv3 sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: c28ed042156865c52607ce031507c696f28b4e7c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102617301"
---
# <a name="ncv3-series"></a>NCv3 sorozat

Az NCv3 sorozatú virtuális gépeket NVIDIA Tesla V100 GPU-k működtetik. Ezek a GPU-k 1,5 x-t biztosítanak a NCv2 sorozat számítási teljesítményéhez. Az ügyfelek igénybe vehetik a frissített GPU-ket a hagyományos HPC-számítási feladatokhoz, mint például a tározó modellezése, a DNS-szekvencia, a protein-elemzés, a Monte Carlo-szimulációk és egyebek. Az NC24rs v3 konfiguráció alacsony késésű, nagy átviteli sebességű hálózati adaptert biztosít a szorosan összekapcsolt párhuzamos számítási feladatokhoz. A GPU-k mellett az Intel Xeon E5-2690 v4 (Broadwell) processzorok is a NCv3 sorozatú virtuális gépeket használják.

[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): nem támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
NVIDIA NVLink Interconnect: nem támogatott<br>

> [!IMPORTANT]
> Ennél a virtuálisgép-sorozatnál az előfizetéshez tartozó vCPU (Core) kvóta kezdetben 0 értékre van állítva minden régióban. [VCPU-kvóta növelésének kérése](../azure-portal/supportability/resource-manager-core-quotas-request.md) a sorozathoz egy [elérhető régióban](https://azure.microsoft.com/regions/services/). Ezek az SKU-elemek nem érhetők el próbaverziós vagy Visual Studio-előfizetői Azure-előfizetésekhez. Előfordulhat, hogy az előfizetési szint nem támogatja ezen SKU-elemek kijelölését és telepítését. 
>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = egy V100 kártya.

*RDMA-kompatibilis

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](./extensions/hpccompute-gpu-windows.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](./extensions/hpccompute-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](./extensions/overview.md).

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, tekintse meg a következőt: [n-sorozat GPU-illesztőprogram beállítása Windows](./windows/n-series-driver-setup.md) vagy [N sorozatú GPU-illesztőhöz Linux](./linux/n-series-driver-setup.md) rendszeren támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
