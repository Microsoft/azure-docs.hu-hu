---
title: ND sorozat – Azure Virtual Machines
description: Az ND sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 5516e17a74fffd28f432df305dfa7f592644d4a7
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102611572"
---
# <a name="nd-series"></a>ND sorozat

Az ND sorozatú Virtual Machines az AI-hoz készült GPU-család, valamint a Deep learning számítási feladatainak egy új kiegészítése. Kiváló teljesítményt nyújtanak a képzéshez és a következtetésekhez. Az ND-példányokat az [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU-k és az Intel Xeon E5-2690 v4 (Broadwell) processzorok működtetik. Ezek a példányok kiváló teljesítményt biztosítanak az egyszeres pontosságú lebegőpontos műveletekhez, a Microsoft Cognitive Toolkit, TensorFlow, Cafe és más keretrendszereket használó AI-munkaterhelésekhez. Az ND sorozat emellett sokkal nagyobb GPU-memória méretet (24 GB) biztosít, ami lehetővé teszi, hogy a rendszer sokkal nagyobb neurális hálós modellekhez illeszkedjen. Az NC sorozathoz hasonlóan az ND sorozat egy olyan konfigurációt kínál, amely egy másodlagos, kis késleltetésű, nagy átviteli sebességű hálózattal rendelkezik a RDMA-on keresztül, és InfiniBand a kapcsolatot, így nagy léptékű, több GPU-ra kiterjedő képzési feladatokat is futtathat.

[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): nem támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
NVIDIA NVLink Interconnect: nem támogatott<br>

> [!IMPORTANT]
> Ennél a virtuálisgép-sorozatnál az előfizetéshez tartozó régiónként vCPU (mag) kvóta kezdetben 0 értékre van állítva. [VCPU-kvóta növelésének kérése](../azure-portal/supportability/resource-manager-core-quotas-request.md) a sorozathoz egy [elérhető régióban](https://azure.microsoft.com/regions/services/).
>
| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = egy P40 kártya.

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
