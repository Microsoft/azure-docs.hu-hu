---
title: HBv2 sorozatú VM-méret teljesítmény
description: A HBv2 sorozatú virtuálisgép-méretek teljesítmény-tesztelési eredményeinek megismerése az Azure-ban.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28e7066c166a4c61c2f6436e9bd126f712f89f78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670073"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2 sorozatú virtuális gépek méretei

Több teljesítményteszt is fut a [HBv2 sorozatú](../../hbv2-series.md) virtuális gépeken. A teljesítmény tesztelésének néhány eredménye a következő.


| Számítási feladat                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Hármas ADATFOLYAM                                    | 350 GB/s (21-23 GB/s/CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (RMAX, FP32)               |
| RDMA késés & sávszélesség                        | 1,2-s, 190 GB/s                                        |
| FIO helyi NVMe SSD-vel                           | 2,7 GB/s olvasás, 1,1 GB/s írás; 102k IOPS olvasások, 115 IOPS írások |
| IOR 8 * Azure prémium SSD (P40 Managed Disks, RAID0) * *  | 1,3 GB/s olvasás, 2,5 GB/írás; 101k IOPS olvasások, 105k IOPS írások |


## <a name="mpi-latency"></a>MPI-késés

A OSU-es teljesítményteszt-csomagból származó MPI-késési teszt fut. A minta szkriptek a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)találhatók.


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="MPI-késés az Azure HB-ban.":::


## <a name="mpi-bandwidth"></a>MPI-sávszélesség

A OSU-es teljesítményteszt-csomagból származó MPI sávszélesség-teszt fut. A minta szkriptek a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)találhatók.


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="MPI-sávszélesség az Azure HB-ban.":::


## <a name="mellanox-perftest"></a>Mellanox Perftest

A [Mellanox Perftest-csomag](https://community.mellanox.com/s/article/perftest-package) számos InfiniBand-teszttel rendelkezik, például a késés (ib_send_lat) és a sávszélesség (ib_send_bw). Az alábbi parancs egy példát mutat be. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Következő lépések

- Olvassa el a legújabb bejelentéseket és néhány nagy teljesítményű számítástechnikai (HPC) példát és eredményt az [Azure számítási technikai közösségének blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.