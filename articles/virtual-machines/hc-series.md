---
title: HC-sorozat – Azure Virtual Machines
description: A HC-sorozatú virtuális gépek specifikációi.
author: ju-shim
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: daca619affa0f5f177ccddbc08f26e73bd5086e3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439986"
---
# <a name="hc-series"></a>HC-sorozat

A HC sorozatú virtuális gépek a sűrű számítások által vezérelt alkalmazások számára vannak optimalizálva, mint például az implicit, véges elemek elemzése, a molekuláris dinamika és a számítási kémia. A HC-alapú virtuális gépek a 44 Intel Xeon Platinum 8168 processzor-magot, a CPU-mag 8 GB RAM-ot, a feleznie pedig nem rendelkeznek. Az Intel Xeon Platinum platform támogatja az Intel olyan szoftverek gazdag ökoszisztémáját, mint az Intel Math kernel Library és a speciális vektoros feldolgozási képességek, például a AVX-512.

A HC sorozatú virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezek a virtuális gépek az optimalizált és konzisztens RDMA teljesítmény érdekében nem blokkoló FAT-fában vannak csatlakoztatva. Ezek a virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakozású átvitelt (a DCT a standard RC és UD átvitelek esetében). Ezek a funkciók javítják az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és a használata ajánlott.

[ACU](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>

<br>

| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Maximális Ethernet-Vnic |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Mind | 700 | 4 | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

- További információ a [virtuális gépek konfigurálásáról, a](./workloads/hpc/configure.md) [InfiniBand engedélyezéséről](./workloads/hpc/enable-infiniband.md), az [MPI beállításáról](./workloads/hpc/setup-mpi.md)és az Azure-hoz készült HPC-alkalmazások optimalizálásáról a [HPC-munkaterhelések](./workloads/hpc/overview.md)esetében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magas szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
