---
title: Azure-beli virtuálisgép-méretek – optimalizált számítás | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző számítási optimalizált méreteket. A vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózat sávszélességével kapcsolatos információkat sorolja fel ebben a sorozatban.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: 01eba7bff571a8db25591b8bfa5c5e712511588f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557692"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Számítási optimalizált virtuálisgép-méretek

A számítási optimalizált virtuálisgép-méretek magas CPU-memória aránnyal rendelkeznek. Ezek a méretek kiválóan alkalmasak a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazások kiszolgálói számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számáról nyújt információt. Emellett a tárolási teljesítményre és a hálózati sávszélességre vonatkozó információkat is tartalmaz ebben a csoportosításban.

A [Fsv2 sorozat](fsv2-series.md) 2. generációs Intel® Xeon® Platinum 8272CL (Cascade Lake) processzorokat és Intel® Xeon® Platinum 8168 (Skylake) processzorokat futtat. A szolgáltatás egy tartós, 3,4 GHz-es, a Turbo órajelét és a 3,7 GHz-es max. Intel® AVX-512 utasítások az Intel skálázható processzorok újdonságai. Ezek az utasítások akár kétszeres teljesítmény-növekedést is biztosítanak a vektoros feldolgozási feladatokhoz egy-és dupla pontosságú lebegőpontos műveletekben. Más szóval a számítási feladatokhoz nagyon gyorsak.

A Fsv2 sorozat a legjobb ár-teljesítmény az Azure-portfólióban, amely az Azure-beli számítási egység (ACU) alapján vCPU.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.

További információ arról, hogy az Azure Hogyan nevezi el a virtuális gépeket: az [Azure virtuálisgép-méretek elnevezési konvenciói](./vm-naming-conventions.md).