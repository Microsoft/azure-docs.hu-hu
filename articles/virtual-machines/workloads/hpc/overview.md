---
title: Nagy teljesítményű számítás infiniBand-kompatibilis H- és N-sorozatú virtuális gépeken – Azure Virtual Machines
description: Ismerje meg a HPC-re optimalizált InfiniBand-kompatibilis H- és N-sorozatú virtuális gépek funkcióit és képességeit.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 554764b89e5da4cd6777ec89fcb2f2d5ad104ebf
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600267"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Nagy teljesítményű számítás infiniBand-kompatibilis H- és N-sorozatú virtuális gépeken

Az Azure InfiniBand-kompatibilis H- és N-sorozatú virtuális gépeket arra tervezték, hogy vezető osztályú teljesítményt, Message Passing Interface (MPI) méretezhetőséget és költséghatékonyságot nyújtsunk számos valós HPC- és AI-számítási feladathoz. Ezek a nagy teljesítményű számítástechnikára (HPC) optimalizált virtuális gépek a tudományos és mérnöki tevékenység legnagyobb számítási igényű problémáinak (például folyadékdinamika, földmodellezés, időjárás-szimulációk stb.) megoldására szolgálnak.

Ezek a cikkek ismertetik az Azure InfiniBand-kompatibilis H- és N-sorozatú virtuális gépekkel kapcsolatos első lépések, valamint a HPC- és AI-számítási feladatok optimális konfigurációját a virtuális gépeken a méretezhetőség érdekében.

## <a name="features-and-capabilities"></a>Funkciók és képességek

Az InfiniBand-kompatibilis H- és N-sorozatú virtuális gépek úgy vannak kialakítva, hogy a legjobb HPC-teljesítményt, MPI-méretezhetőséget és költséghatékonyságot biztosítják a HPC számítási feladatokhoz. A [H- és](../../sizes-hpc.md) [N-sorozatú](../../sizes-gpu.md) virtuális gépekről további információt a virtuális gépek funkcióiról és képességeiről itt olvashat.

### <a name="rdma-and-infiniband"></a>RDMA és InfiniBand

[Az RDMA-kompatibilis](../../sizes-hpc.md#rdma-capable-instances) [H-](../../sizes-hpc.md) és [N-sorozatú](../../sizes-gpu.md) virtuális gépek kis késésű és nagy sávszélességű InfiniBand-hálózaton keresztül kommunikálnak. Az ilyen összekapcsoláson keresztüli RDMA-képesség kritikus fontosságú az elosztott csomópontos HPC- és AI-számítási feladatok skálázhatóságának és teljesítményének növelése érdekében. Az InfiniBand-kompatibilis H- és N-sorozatú virtuális gépek egy nem blokkoló, fat fában vannak csatlakoztatva, alacsony szintű kialakítással az optimalizált és konzisztens RDMA-teljesítmény érdekében.
Az InfiniBand beállításával kapcsolatos további információkért lásd: Enable [InfiniBand](enable-infiniband.md) (InfiniBand engedélyezése).

### <a name="message-passing-interface"></a>Üzenetát átadási felület

Az SR-IOV-kompatibilis H- és N-sorozat szinte minden MPI-kódtárat és -verziót támogat. Néhány a leggyakrabban használt MPI-kódtárak közül: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Platform MPI. Minden távoli közvetlen memóriaelérési (RDMA) művelet támogatott.
A [különböző támogatott MPI-kódtárak](setup-mpi.md) telepítésével és azok optimális konfigurációjának telepítésével kapcsolatos további információkért lásd az MPI beállítását.

## <a name="get-started"></a>Bevezetés

Az első lépés a [virtuálisgép-specifikációk](../../sizes-hpc.md) és az RDMA-képesség alapján optimális H- és [N-sorozatú virtuálisgép-típus](../../sizes-gpu.md) kiválasztása a számítási [feladathoz.](../../sizes-hpc.md#rdma-capable-instances)
Másodszor konfigurálja a virtuális gépet az InfiniBand engedélyezésével. Ennek többféle módja is van, beleértve az optimalizált virtuálisgép-rendszerképek és a be vannak ékelt illesztőprogramok használatával; Részletekért [lásd: Optimalizálás Linuxra](configure.md) és [InfiniBand](enable-infiniband.md) engedélyezése.
Harmadszor, az elosztott csomópontok számítási feladatai esetében az MPI megfelelő kiválasztása és konfigurálása kritikus fontosságú. A [részletekért lásd az MPI](setup-mpi.md) beállítását.
Negyedszer, a teljesítmény és a méretezhetőség érdekében optimálisan konfigurálja a számítási feladatokat a virtuálisgép-családra vonatkozó útmutatás alapján, például a [HBv3-sorozat](hbv3-series-overview.md) áttekintéséhez és a [HC-sorozat áttekintéséhez.](hc-series-overview.md)

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [az](configure.md) InfiniBand-kompatibilis [H- és N-sorozatú](../../sizes-hpc.md) virtuális gépek konfigurálását és optimalizálását. [](../../sizes-gpu.md)
- Tekintse át [a HBv3-sorozat](hb-series-overview.md) áttekintését és a [HC-sorozat](hc-series-overview.md) áttekintését, amelyből megtudhatja, hogyan konfigurálható a számítási feladatok optimális konfigurálása a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb közleményeket, HPC számítási feladatok példáit és a teljesítményeredményeket a [Azure Compute Tech Community Blogon.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Tesztelje tudását egy tanulási modullal, amely a HPC-alkalmazások azure-beli [optimalizálását használja.](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/)
- A HPC számítási feladatok futtatásának magasabb szintű architekturális nézetét lásd: Nagy teljesítményű [számítástechnika (HPC) az Azure-ban.](/azure/architecture/topics/high-performance-computing/)
