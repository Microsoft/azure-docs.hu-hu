---
title: HBv3 sorozatú VM-méretek teljesítmény és méretezhetőség
description: Ismerje meg a HBv3-sorozatú virtuálisgép-méretek teljesítményét és méretezhetőségét az Azure-ban.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: bf64cfc8ad00fc7f761019ed2fa66089434a96ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604770"
---
# <a name="hbv3-series-virtual-machine-performance"></a>HBv3 sorozatú virtuális gépek teljesítménye

A gyakori HPC-referenciaértékeket használó teljesítménybeli elvárások a következők:

| Számítási feladat                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Hármas ADATFOLYAM                                    | 330-350 GB/s (~ 82-86 GB/s/NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) 120-Core VM-mérethez               |
| RDMA késés & sávszélesség                        | 1,2-másodpercenkénti (1 bájtos), 192 GB/s (egyirányú)                                        |
| FIO a helyi NVMe SSD-k (RAID0)                  | 7 GB/s olvasás, 3 GB/s írás; 186k IOPS olvasások, 201k IOPS írások |

## <a name="process-pinning"></a>Folyamat-rögzítés

A [folyamat](compiling-scaling-applications.md#process-pinning) rögzítése jól működik a HBv3 sorozatú virtuális gépeken, mert a mögöttes szilíciumot elérhetővé tesszük a vendég virtuális gép számára. Erősen ajánlott az optimális teljesítmény és konzisztencia érdekében feldolgozni a rögzítést.

## <a name="mpi-latency"></a>MPI-késés

Az OSU-es teljesítményteszt-csomag MPI-késési tesztje az alábbiak szerint hajtható végre. A minta szkriptek a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)találhatók.

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI-sávszélesség
Az OSU-es teljesítményteszt-csomag MPI-sávszélesség-tesztje az alábbiak szerint hajtható végre. A minta szkriptek a [githubon](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)találhatók.
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox Perftest
A [Mellanox Perftest-csomag](https://community.mellanox.com/s/article/perftest-package) számos InfiniBand-teszttel rendelkezik, például a késés (ib_send_lat) és a sávszélesség (ib_send_bw). Az alábbi parancs egy példát mutat be.
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Következő lépések
- További információ az [MPI-alkalmazások méretezéséről](compiling-scaling-applications.md).
- Tekintse át a HPC-alkalmazások teljesítmény-és méretezhetőségi eredményeit a HBv3 virtuális gépeken a [TechCommunity cikkben](https://techcommunity.microsoft.com/t5/azure-compute/hpc-performance-and-scalability-results-with-azure-hbv3-vms/bc-p/2235843).
- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
