---
title: A HC sorozatú virtuális gépek méretének teljesítménye
description: Ismerje meg a HC-sorozatú virtuálisgép-méretek teljesítmény-tesztelési eredményeit az Azure-ban.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0d164c19da1ed2cbf6930a92686b35690fb0afb5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666924"
---
# <a name="hc-series-virtual-machine-sizes"></a>HC sorozatú virtuális gépek méretei

Több teljesítményteszt is fut a HC-sorozat méretében. A teljesítmény tesztelésének néhány eredménye a következő.

| Számítási feladat                                        | HB                    |
|-------------------------------------------------|-----------------------|
| Hármas ADATFOLYAM                                    | 190 GB/s (Intel MLC AVX-512)  |
| High-Performance Linpack (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (RMAX) |
| RDMA késés & sávszélesség                        | 1,05-s, 96,8 GB/s   |
| FIO helyi NVMe SSD-vel                           | 1,3 GB/s olvasás, 900 MB/s írás |  
| IOR 4 Azure-prémium SSDon (P30 Managed Disks, RAID0) * *  | 780 MB/s olvasás, 780 MB/írás |

## <a name="mpi-latency"></a>MPI-késés

A OSU-es teljesítményteszt-csomagból származó MPI-késési teszt fut. A minta parancsfájlok a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) találhatók

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="MPI-késés az Azure HC-ben.":::

## <a name="mpi-bandwidth"></a>MPI-sávszélesség

A OSU-es teljesítményteszt-csomagból származó MPI sávszélesség-teszt fut. A minta parancsfájlok a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh) találhatók

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="MPI-sávszélesség az Azure HC-ben.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

A [Mellanox Perftest-csomag](https://community.mellanox.com/s/article/perftest-package) számos InfiniBand-teszttel rendelkezik, például a késés (ib_send_lat) és a sávszélesség (ib_send_bw). Az alábbi parancs egy példát mutat be.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Következő lépések

- Olvassa el a legújabb bejelentéseket és néhány nagy teljesítményű számítástechnikai (HPC) példát és eredményt az [Azure számítási technikai közösségének blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
