---
title: A HB sorozatú virtuális gépek méretének teljesítménye
description: Az Azure-beli HB-sorozatú virtuálisgép-méretek teljesítmény-tesztelési eredményeinek megismerése.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/09/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1f2d24279d3e74774da05eba42c0e916370d4f1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101672151"
---
# <a name="hb-series-virtual-machine-sizes"></a>HB sorozatú virtuális gépek méretei

Több teljesítményteszt is fut a HB sorozatos méretekben. A teljesítmény tesztelésének néhány eredménye a következő.

| Számítási feladat                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Hármas ADATFOLYAM                                    | 260 GB/s (32-33 GB/s/CCX)  |
| High-Performance Linpack (HPL)                  | 1 000 GigaFLOPS (Rpeak), 860 GigaFLOPS (RMAX) |
| RDMA késés & sávszélesség                        | 1,27-s, 99,1 GB/s   |
| FIO helyi NVMe SSD-vel                           | 1,7 GB/s olvasás, 1,0 GB/s írások      |  
| IOR 4 * Azure prémium SSD (P30 Managed Disks, RAID0) * *  | 725 MB/s olvasás, 780 MB/írás   |


## <a name="mpi-latency"></a>MPI-késés

A OSU-es teljesítményteszt-csomagból származó MPI-késési teszt fut. A minta parancsfájlok a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) találhatók

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hb.png" alt-text="MPI-késés az Azure HB-ban.":::

## <a name="mpi-bandwidth"></a>MPI-sávszélesség

A OSU-es teljesítményteszt-csomagból származó MPI sávszélesség-teszt fut. A minta parancsfájlok a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) találhatók

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hb.png" alt-text="MPI-sávszélesség az Azure HB-ban.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

A [Mellanox Perftest-csomag](https://community.mellanox.com/s/article/perftest-package) számos InfiniBand-teszttel rendelkezik, például a késés (ib_send_lat) és a sávszélesség (ib_send_bw). Az alábbi parancs egy példát mutat be.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Következő lépések

- Olvassa el a legújabb bejelentéseket és néhány nagy teljesítményű számítástechnikai (HPC) példát és eredményt az [Azure számítási technikai közösségének blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
