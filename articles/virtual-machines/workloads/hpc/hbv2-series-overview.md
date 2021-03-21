---
title: HBv2 sorozatú VM – áttekintés – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HBv2-sorozatú virtuális gépek méretét.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 59dd953b2116bc1ec7bd0a581cc181df64fbf49e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721160"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 sorozatú virtuális gépek – áttekintés 

 
A nagy teljesítményű számítási (HPC-) alkalmazások teljesítményének maximalizálása az AMD-EPYC az átgondolt megközelítéssel, a memória helyi és a feldolgozásával kapcsolatos eljárás szükséges. Az alábbiakban felvázoljuk az AMD EPYC architektúráját és az Azure-ban a HPC-alkalmazások megvalósítását. A **pNUMA** kifejezést fogjuk használni egy fizikai NUMA-tartományra, és a **vNUMA** egy virtualizált NUMA-tartományra kell hivatkoznia. 

Fizikailag a [HBv2 sorozatú](../../hbv2-series.md) kiszolgáló 2 * 64-core EPYC 7742 processzor, összesen 128 fizikai maggal. Ezek a 128-es magok 32 pNUMA tartományokra oszlanak (16 szoftvercsatorna), amelyek mindegyike 4 maggal rendelkezik, és az AMD az **alapvető összetett** (vagy **CCX**). Minden CCX saját L3-gyorsítótárral rendelkezik, ami azt szemlélteti, hogy az operációs rendszer egy pNUMA/vNUMA határt fog látni. Négy szomszédos CCXs-megosztás fér hozzá két, fizikai DRAM-csatornához. 

Ha biztosítani szeretné, hogy az Azure hypervisor a virtuális gép beavatkozása nélkül működjön, akkor a 0 és 16 közötti fizikai pNUMA-tartományokat (azaz az egyes CPU-szoftvercsatornák első CCX) fenntartjuk. Az összes fennmaradó 30 pNUMA-tartomány hozzá van rendelve a virtuális géphez, amely vNUMA válik. Így a virtuális gép a következőket fogja látni:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` magok száma virtuális gépenként 

Maga a virtuális gép nem ismeri, hogy a pNUMA 0 és 16 van fenntartva. Ez a 0-29-as számú vNUMA-t, a vNUMA 0-14-et pedig a vSocket 0 vNUMA, a vNUMA 15-29 pedig a vSocket 1. A következő szakaszban megtudhatja, hogyan futtathatnak MPI-alkalmazásokat ezen az aszimmetrikus NUMA-elrendezésen. 

A folyamat rögzítése a HBv2 sorozatú virtuális gépeken fog működni, mivel a mögöttes szilíciumot elérhetővé tesszük a vendég virtuális gép számára. Erősen ajánlott az optimális teljesítmény és konzisztencia érdekében feldolgozni a rögzítést. 


## <a name="hardware-specifications"></a>Hardverspecifikációk 

| Hardver specifikációi          | HBv2 sorozatú virtuális gép                   | 
|----------------------------------|----------------------------------|
| Cores                            | 120 (SMT letiltva)               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU-gyakoriság (nem AVX)          | ~ 3,1 GHz (egyetlen és minden mag)    | 
| Memória                           | 4 GB/mag (480 GB összesen)         | 
| Helyi lemez                       | 960 GB NVMe (blokk), 480 GB SSD (lapozófájl) | 
| InfiniBand                       | 200 GB/s EDR Mellanox ConnectX-6 | 
| Network (Hálózat)                          | 50 GB/s Ethernet (40 GB/s használható) Azure második Gen SmartNIC | 


## <a name="software-specifications"></a>Szoftverek specifikációi 

| Szoftverek specifikációi     | HBv2 sorozatú virtuális gép                                            | 
|-----------------------------|-----------------------------------------------------------|
| MPI-feladatok maximális mérete            | 36000 mag (300 virtuális gép egyetlen virtuálisgép-méretezési készletben, singlePlacementGroup = true) |
| MPI-támogatás                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPI  |
| További keretrendszerek       | UCX, libfabric, PGAS |
| Azure Storage-támogatás       | Standard és prémium szintű lemezek (legfeljebb 8 lemez) |
| Operációs rendszer támogatása a CSATOLÓJÁHOZ nincs SR RDMA   | CentOS/RHEL 7.6 +, Ubuntu 16.04 +, SLES 12 SP4 +, WinServer 2016 +  |
| Orchestrator-támogatás        | CycleCloud, batch, AK; [fürt konfigurációs beállításai](../../sizes-hpc.md#cluster-configuration-options)  |

> [!NOTE] 
> A Windows Server 2012 R2 nem támogatott a HBv2 és más, több mint 64 (virtuális vagy fizikai) magokkal rendelkező virtuális gépeken. További információt [itt](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) talál.

## <a name="next-steps"></a>Következő lépések

- További információ az [AMD EPYC architektúráról](https://bit.ly/2Epv3kC) és a [többplatformos architektúráról](https://bit.ly/2GpQIMb). További részletekért tekintse meg a [HPC hangolási útmutató az AMD EPYC processzorokhoz](https://bit.ly/2T3AWZ9)című témakört.
- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.