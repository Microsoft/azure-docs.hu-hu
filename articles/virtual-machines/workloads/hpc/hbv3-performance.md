---
title: HBv3 sorozatú VM-méret teljesítmény
description: A HBv3 sorozatú virtuálisgép-méretek teljesítmény-tesztelési eredményeinek megismerése az Azure-ban.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: c92c30da2b8e78715df46341dc4bc502ed5e34ce
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472847"
---
# <a name="hbv3-series-virtual-machine-performance"></a>HBv3 sorozatú virtuális gépek teljesítménye

A HBv3 virtuális gépek korai hozzáférésű felhasználói a következő teljesítménybeli számadatokat várhatják a gyakori HPC-teljesítménymutatók esetén

| Számítási feladat                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| Hármas ADATFOLYAM                                    | 330-350 GB/s (~ 82-86 GB/s/NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) 120-Core VM-mérethez               |
| RDMA késés & sávszélesség                        | 1,2-másodpercenkénti (1 bájtos), 192 GB/s (egyirányú)                                        |
| FIO a helyi NVMe SSD-k (RAID0)                  | 7 GB/s olvasás, 3 GB/s írás; 186k IOPS olvasások, 201k IOPS írások |

## <a name="process-pinning"></a>Folyamat-rögzítés

A folyamat rögzítése jól működik a HBv3 sorozatú virtuális gépeken, mert a mögöttes szilíciumot elérhetővé tesszük a vendég virtuális gép számára. Erősen ajánlott az optimális teljesítmény és konzisztencia érdekében feldolgozni a rögzítést.

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
- Olvassa el a legújabb bejelentéseket és néhány nagy teljesítményű számítástechnikai (HPC) példát és eredményt az [Azure számítási technikai közösségének blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti nézetét lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.