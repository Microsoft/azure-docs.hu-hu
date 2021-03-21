---
title: Nagy teljesítményű számítástechnika az InfiniBand-ben engedélyezett H-és N-sorozatú virtuális gépek – Azure Virtual Machines
description: Ismerje meg a HPC használatára optimalizált H-sorozatú és N sorozatú virtuális gépek InfiniBand funkcióit és képességeit.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 03/18/2021
ms.reviewer: cynthn
ms.openlocfilehash: 65b37a8c07e083f5e9809812e2d4446cc48717d1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "104720593"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Nagy teljesítményű számítástechnika a InfiniBand-t használó H-és N-sorozatú virtuális gépeken

Az Azure InfiniBand-kompatibilis H-sorozatú és N sorozatú virtuális gépeket úgy tervezték, hogy vezetői szintű teljesítményt, Message Passing Interface (MPI) skálázhatóságot és költséghatékonyságot nyújtsanak a különböző valós HPC-és AI-munkaterhelésekhez. Ezeknek a nagy teljesítményű számítástechnikai (HPC) optimalizált virtuális gépeknek a segítségével megoldhatja a legtöbb számítási feladattal kapcsolatos problémát a tudományok és a mérnöki tudományok terén, például: Fluid Dynamics, föld modellezés, időjárási szimulációk stb.

Ezek a cikkek ismertetik, hogyan kezdheti el a InfiniBand-kompatibilis H-sorozatú és N sorozatú virtuális gépeket az Azure-ban, valamint a HPC-és AI-munkaterhelések optimális konfigurációját a virtuális gépeken a méretezhetőség érdekében.

## <a name="features-and-capabilities"></a>Funkciók és képességek

A InfiniBand enabled H-sorozatú és N sorozatú virtuális gépek úgy vannak kialakítva, hogy a legjobb HPC-teljesítményt, az MPI skálázhatóságot és a költséghatékonyságot használják a HPC számítási feladatokhoz. A virtuális gépek funkcióinak és képességeinek megismeréséhez lásd a [H-sorozat](../../sizes-hpc.md) és az [N sorozatú](../../sizes-gpu.md) virtuális gépek című témakört.

### <a name="rdma-and-infiniband"></a>RDMA és InfiniBand

A [RDMA képes](../../sizes-hpc.md#rdma-capable-instances) [H-sorozatú](../../sizes-hpc.md) és [N sorozatú](../../sizes-gpu.md) virtuális gépek kommunikációja az alacsony késésű és a nagy sávszélességű InfiniBand-hálózaton keresztül történik. Az RDMA képesség kritikus fontosságú a elosztott csomópontok és az AI-munkaterhelések méretezhetőségének és teljesítményének növelése érdekében. Az InfiniBand enabled H-sorozatú és N sorozatú virtuális gépek egy nem blokkoló FAT-fában vannak csatlakoztatva, amely alacsony átmérőjű kialakítást biztosít az optimalizált és konzisztens RDMA teljesítményhez.
A InfiniBand-kompatibilis virtuális gépek InfiniBand beállításával kapcsolatos további tudnivalókért tekintse meg a [InfiniBand engedélyezése](enable-infiniband.md) című témakört.

### <a name="message-passing-interface"></a>Üzenet átadása illesztőfelület

Az SR-IOV-kompatibilis H-sorozat és az N-sorozat szinte minden MPI-könyvtárat és-verziót támogat. A leggyakrabban használt MPI-kódtárak a következők: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, platform MPI. A távoli közvetlen memória-hozzáférés (RDMA) összes művelete támogatott.
A különböző támogatott MPI-könyvtárak telepítésével és az optimális konfigurációval kapcsolatos további tudnivalókért tekintse meg az [MPI beállítása](setup-mpi.md) című témakört.

## <a name="get-started"></a>Bevezetés

Első lépésként válassza a [H-sorozat](../../sizes-hpc.md) és az [N sorozatú](../../sizes-gpu.md) virtuális gép típusát a számítási feladatokhoz a virtuálisgép-specifikációk és a [RDMA képesség](../../sizes-hpc.md#rdma-capable-instances)alapján.
Másodszor konfigurálja a virtuális gépet a InfiniBand engedélyezésével. Ezt többféleképpen is elvégezheti, beleértve az optimalizált virtuálisgép-rendszerképek használatát a besütött illesztőprogramokkal; a részletekért lásd: [a Linux optimalizálása](configure.md) és a [InfiniBand engedélyezése](enable-infiniband.md) .
Harmadszor, az elosztott csomópontok számítási feladataihoz elengedhetetlen az MPI megfelelő kiválasztása és konfigurálása. További részleteket az [MPI beállítása](setup-mpi.md) című témakörben talál.
Negyedszer, a teljesítmény és a méretezhetőség érdekében optimálisan konfigurálja a munkaterheléseket a virtuálisgép-családra jellemző útmutatást követve, például a [HBv3-sorozat áttekintéséhez](hbv3-series-overview.md) és a [HC-sorozatok áttekintéséhez](hc-series-overview.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan konfigurálhatja és optimalizálhatja](configure.md) a [H-sorozatú](../../sizes-hpc.md) és az [N sorozatú](../../sizes-gpu.md) virtuális gépek InfiniBand.
- Tekintse át az [HBv3-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését](hc-series-overview.md) , amelyből megismerheti a számítási feladatok optimális konfigurálását a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb bejelentéseket, a HPC számítási feladatait és a teljesítmény eredményeit az [Azure számítási technikai közösségi blogokban](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
