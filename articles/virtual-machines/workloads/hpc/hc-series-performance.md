---
title: Hibrid kapcsolat-sorozatú virtuális gép mérete teljesítmény - Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg a teljesítmény-ellenőrzési eredmények hibrid kapcsolat-sorozat virtuális gépeinek esetében az Azure-ban.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: f931bfa486384a0f38a4964c2ed7b2446b40bfec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66809857"
---
# <a name="hc-series-virtual-machine-sizes"></a>Hibrid kapcsolat-sorozatú virtuális gépek méretei

A hibrid kapcsolat-sorozat méretei több Teljesítménytesztek futtatása. Az alábbiakban néhány, a Teljesítménytesztelés, az eredmények.

| Számítási feladat                                        | HB                    |
|-------------------------------------------------|-----------------------|
| A STREAM háromrészes                                    | ~ 190 GB/s (Intel AVX beállítása MLC-512)  |
| Nagy teljesítményű Linpack (HPL)                  | ~ 3520 GigaFLOPS (Rpeak), ~ 2970 GigaFLOPS (Rmax) |
| RDMA-késés és sávszélesség                        | 1.80 mikroszekundum, 96.3 Gb/s   |
| A helyi NVMe SSD FIO                           | ~1.3 GB/s olvasási, ~ 900 MB/s-írások |  
| A 4 Azure prémium szintű SSD IOR (P30 által felügyelt lemezek RAID0) **  | ~ 780 MB/s olvas, ~ 780 MB/ír |

## <a name="infiniband-send-latency"></a>InfiniBand küldési késleltetés

Mellanox Perftest.

```azure-cli
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

|  #bytes         | #iterations     | t_min [mikroszekundumos]     | t_max[microsecond]     | t_typical [mikroszekundumos] | t_avg [mikroszekundumos]     | t_stdev [mikroszekundumos]   |
|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|-----------------|
| 2               | 1000            | 1.80            | 7.50            | 1.85            | 1.86            | 0.20            |
| 4               | 1000            | 1.79            | 6.06            | 1.83            | 1.84            | 0.20            |
| 8               | 1000            | 1.78            | 5.26            | 1.83            | 1.84            | 0.19            |
| 16              | 1000            | 1.79            | 6.21            | 1.83            | 1.84            | 0.22            |
| 32              | 1000            | 1.80            | 6.82            | 1.84            | 1.85            | 0.24            |
| 64              | 1000            | 1.85            | 5.47            | 1.88            | 1.86            | 0.12            |
| 128             | 1000            | 1.88            | 5.61            | 1.93            | 1.89            | 0.25            |
| 256             | 1000            | 2.24            | 6.39            | 2.28            | 2.02            | 0.18            |
| 512             | 1000            | 2.32            | 5.42            | 2.36            | 2.30            | 0.17            |
| 1024            | 1000            | 2.43            | 6.22            | 2.48            | 2.38            | 0.21            |
| 2048            | 1000            | 2.68            | 6.14            | 2.75            | 2.52            | 0.20            |
| 4096            | 1000            | 3.17            | 7.02            | 3.26            | 2.81            | 0.24            |

## <a name="osu-mpi-latency-test"></a>OSU MPI-késés teszt

OSU MPI-késés teszteléséhez v5.4.3.

```azure-cli
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

| #bytes  | Késés [mikroszekundumos] (MPICH 3.3 + CH4) | Késés [mikroszekundumos] (OpenMPI 4.0.0 verzióra.) | Késés [mikroszekundumos] (MVAPICH2 2.3.) |
|------|----------|----------|----------|
| 2    | 1.84     | 1.78     | 2.08     |
| 4    | 1.84     | 1.79     | 2.08     |
| 8    | 1.85     | 1.79     | 2.05     |
| 16   | 1.85     | 1.79     | 2.1      |
| 32   | 1.87     | 1.82     | 2.12     |
| 64   | 2        | 1.95     | 2.13     |
| 128  | 2.05     | 2        | 2.18     |
| 256  | 2.48     | 2.44     | 2.75     |
| 512  | 2.57     | 2.52     | 2.81     |
| 1024 | 2.76     | 2.71     | 2.97     |
| 2048 | 3.09     | 3.11     | 3.34     |
| 4096 | 3.72     | 3.91     | 4.44     |

## <a name="mpi-bandwidth"></a>MPI-sávszélesség

OSU MPI sávszélesség v5.4.3 teszteléséhez.

```azure-cli
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

| #Size   | Sávszélesség (MB/s) | Sávszélesség (Gb/s) |
|---------|------------------|------------------|
| 2       | 6.18             | 0.04944          |
| 4       | 13.27            | 0.10616          |
| 8       | 26.58            | 0.21264          |
| 16      | 53.51            | 0.42808          |
| 32      | 106.81           | 0.85448          |
| 64      | 211.24           | 1.68992          |
| 128     | 386.98           | 3.09584          |
| 256     | 756.32           | 6.05056          |
| 512     | 1434.2           | 11.4736          |
| 1024    | 2663.8           | 21.3104          |
| 2048    | 4396.99          | 35.17592         |
| 4096    | 6365.86          | 50.92688         |
| 8192    | 8137.9           | 65.1032          |
| 16384   | 9218.29          | 73.74632         |
| 32768   | 10564.61         | 84.51688         |
| 65536   | 11275.6          | 90.2048          |
| 131072  | 11633.7          | 93.0696          |
| 262144  | 11856.27         | 94.85016         |
| 524288  | 11962.69         | 95.70152         |
| 1048576 | 12025.43         | 96.20344         |
| 2097152 | 12038.7          | 96.3096          |
| 4194304 | 11290.92         | 90.32736         |
