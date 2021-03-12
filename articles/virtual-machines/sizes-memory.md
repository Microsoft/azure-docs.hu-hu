---
title: Azure-beli virtuális gépek méretei – memória | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző memória-optimalizált méreteket. A vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózat sávszélességével kapcsolatos információkat sorolja fel ebben a sorozatban.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM-elkülönítés, elkülönített virtuális gép, elkülönítés, elkülönített
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 08a14a1c97a1eae9c07c50334929fb16304e635a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556519"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Memória-optimalizált virtuális gépek méretei

A memóriára optimalizált virtuálisgép-méretek magas memória-CPU arányt kínálnak, amely kiválóan használható a kapcsolódó adatbázis-kiszolgálók, a közepes és a nagy gyorsítótárak, valamint a memóriabeli elemzések számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózati sávszélességgel kapcsolatos információkat tartalmaz ebben a csoportosításban.

- A [Dv2 és a DSv2 sorozat](dv2-dsv2-series-memory.md), amely az eredeti D sorozatra mutat, hatékonyabb processzorral rendelkezik. A Dv2 sorozat körülbelül 35%-kal gyorsabb a D sorozatnál. Az Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) vagy Intel &reg; Xeon &reg; e5-2673 v4 2,3 GHz (Broadwell) vagy Intel &reg; xeon &reg; E5-2673 v3 2,4 GHz (Haswell) processzorokkal, valamint az Intel Turbo Boost Technology 2,0-mel fut. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

    A Dv2 és a DSv2 sorozat ideális olyan alkalmazások számára, amelyek gyorsabb vCPU, jobb ideiglenes tárolási teljesítményt vagy nagyobb memóriát igényelnek. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.

- A [Eav4 és a Easv4 sorozat](eav4-easv4-series.md) az AMD 2.35 GHz EPYC<sup>TM</sup> 7452 processzorát egy többszálas konfigurációban, akár 256mb L3 gyorsítótárral, a legtöbb memóriára optimalizált számítási feladatok futtatására szolgáló lehetőségek növelésével teszi elérhetővé. A Eav4-sorozat és a Easv4-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a Ev3 & Esv3 sorozat.

- A [Ev3 és a Esv3 sorozatú](ev3-esv3-series.md) Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) vagy Intel &reg; Xeon &reg; E5-2673 v4 2,3 GHz (Broadwell) processzor egy Hyper-threaded konfigurációban, jobb értékű kiépítés a legtöbb általános célú számítási feladathoz, és a Ev3 integrálása a legtöbb más felhőben található általános célú virtuális gépekkel. A memória kibontása megtörtént (7 GiB/vCPU – 8 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer a Hyper-Threading szolgáltatáshoz való áttéréssel összehangolva kiigazította. A Ev3 a D/Dv2 családok nagy memóriabeli virtuálisgép-méretének követése.

- A [Ev4 és a Esv4 sorozat](ev4-esv4-series.md) a 2. generációs Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokat futtatja egy Hyper-threaded konfigurációban, ideális választás a különböző, nagy mennyiségű, nagyvállalati alkalmazások számára, és akár 504 GIB RAM-ot is tartalmaz. Az [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), az [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) és az [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)funkciókkal rendelkezik. A Ev4 és a Esv4 sorozat nem tartalmaz helyi Temp lemezt. További információkért tekintse meg a  [helyi Temp Disk nélküli Azure](azure-vms-no-temp-disk.md)-beli virtuális gépek méretét.

- A [Edv4 és a Edsv4 sorozat](edv4-edsv4-series.md) a 2. generációs Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut, ideális a rendkívül nagy méretű adatbázisokhoz vagy más alkalmazásokhoz, amelyek nagy vCPU számítanak és nagy mennyiségű memóriát foglalnak magukban. Emellett ezek a virtuálisgép-méretek gyors és nagyobb helyi SSD-tárolót tartalmaznak olyan alkalmazások esetében, amelyek alacsony késésű, nagy sebességű helyi tárterületet foglalnak magukban. A szolgáltatás a 3,4 GHz-es és az Intel [ &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), az [Intel &reg; Hyper-Threading technológia](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) és az [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)minden alapvető Turbo órajelét tartalmazza.

- Az [M sorozat](m-series.md) magas vCPU (akár 128 vCPU) és nagy mennyiségű memóriát (legfeljebb 3,8 TiB) kínál. Emellett ideális a rendkívül nagy méretű adatbázisok vagy más alkalmazások számára, amelyek nagy vCPU és nagy mennyiségű memóriát foglalnak magukban.

- A [Mv2 sorozat](mv2-series.md) a legmagasabb vCPU (legfeljebb 416 vCPU) és a legnagyobb (legfeljebb 11,4 TiB) memóriával rendelkezik a felhőben. Ideális megoldás a rendkívül nagy méretű adatbázisok vagy más alkalmazások számára, akik nagy vCPU és nagy mennyiségű memóriát foglalnak magukban.

Az Azure-beli számítások olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve. Ezek a virtuálisgép-méretek olyan számítási feladatokhoz ideálisak, amelyek nagy fokú elkülönítést igényelnek más ügyfelektől olyan munkaterhelések esetén, mint például a megfelelőségi és szabályozási követelmények. Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait. Az elkülönített virtuálisgép-lehetőségekért tekintse meg az alábbi virtuális gépeket tartalmazó lapokat.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításoptimalizált](sizes-compute.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

További információ arról, hogy az Azure Hogyan nevezi el a virtuális gépeket: az [Azure virtuálisgép-méretek elnevezési konvenciói](./vm-naming-conventions.md).
