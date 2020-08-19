---
title: Az Azure számítási egység áttekintése
description: Az Azure számítási egységek koncepciójának áttekintése. A ACU lehetővé teszi a CPU-teljesítmény összehasonlítását az Azure SKU-ban.
author: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.reviewer: davberg
ms.openlocfilehash: 07d222139f48e91637405660ae0e0c65ef3acdb6
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88605024"
---
# <a name="azure-compute-unit-acu"></a>Azure számítási egység (ACU)

Az Azure számítási egység (ACU) koncepciója lehetővé teszi a számítási (CPU-) teljesítmény összehasonlítását az Azure SKU-ban. Így könnyebben választhatja ki a teljesítményigényeinek leginkább megfelelő termékváltozatot. Az ACU jelenleg a 100-es kis (Standard_A1) virtuális gépen van szabványosítva, és az összes többi SKU is körülbelül annyit jelent, hogy az SKU képes a szabványos teljesítményteszt futtatására.

* A ACUs az Intel® Turbo technológiát használja a CPU-gyakoriság növelésére és a teljesítmény növelésére.  A teljesítmény növelésének mértéke a virtuális gép méretétől, a számítási feladatoktól és az ugyanazon a gazdagépen futó egyéb számítási feladatoktől függően változhat.

* * A ACUs az AMD® fokozza a technológiát a CPU-gyakoriság növelése és a teljesítmény növelése érdekében.  A teljesítmény növelésének mértéke a virtuális gép méretétől, a számítási feladatoktól és az ugyanazon a gazdagépen futó egyéb számítási feladatoktől függően változhat.

Hyper-threaded és képes a beágyazott virtualizálás futtatására

> [!IMPORTANT]
> Az ACU csupán iránymutatóként szolgál. Az egyes számítási terhelések eredményei ettől eltérhetnek.
<br>

| Termékváltozat-család | ACU \ vCPU | vCPU: mag |
| --- | --- |---|
| [A0](sizes-previous-gen.md) |50 | 1:1 |
| [A1 – A4](sizes-previous-gen.md) |100 | 1:1 |
| [A5 – A7](sizes-previous-gen.md) |100 | 1:1 |
| [A1_v2 – A8_v2](sizes-general.md) |100 | 1:1 |
| [A2m_v2 – A8m_v2](sizes-general.md) |100 | 1:1 |
| [A8 – A11](sizes-previous-gen.md) |225* | 1:1 |
| [D1 – D14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [D1_v2 – D15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [DS1 – DS14](sizes-previous-gen.md) |160 – 250 | 1:1 |
| [DS1_v2 – DS15_v2](dv2-dsv2-series.md) |210 - 250* | 1:1 |
| [D_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Ds_v3](dv3-dsv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Dav4](dav4-dasv4-series.md) |230 – 260 * * | 2:1 |
| [Dasv4](dav4-dasv4-series.md) |230 – 260 * * | 2:1 |
| [DV4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Dsv4](dv4-dsv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Ddv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Ddsv4](ddv4-ddsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [E_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\*|
| [Es_v3](ev3-esv3-series.md) |160 – 190 * | 2:1\*\*\* |
| [Eav4](eav4-easv4-series.md) |230 – 260 * * | 2:1 |
| [Easv4](eav4-easv4-series.md) | 230 – 260 * * | 2:1 |
| [Ev4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Esv4](ev4-esv4-series.md) | 195 – 210 | 2:1\*\*\* |
| [Edv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [Edsv4](edv4-edsv4-series.md) | 195 – 210 * | 2:1\*\*\* |
| [F2s_v2 – F72s_v2](fsv2-series.md) |195 – 210 * | 2:1\*\*\* |
| [F1 – F16](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [F1s – F16s](sizes-previous-gen.md) |210 - 250* | 1:1 |
| [G1 – G5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [GS1 – GS5](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [H](h-series.md) |290 - 300* | 1:1 |
| [HB](hb-series.md) |199 – 216 * * | 1:1 |
| [HIBRID kapcsolat](hc-series.md) |297 – 315 * | 1:1 |
| [L4s - L32s](sizes-previous-gen.md) |180 - 240* | 1:1 |
| [L8s_v2 – L80s_v2](lsv2-series.md) |150 – 175 * * | 2:1 |
| [M](m-series.md) | 160 – 180 | 2:1\*\*\* |
| [NVv4](nvv4-series.md) |230 – 260 * * | 2:1 |

Az alábbiakban további információkra mutató hivatkozásokat talál a különböző méretekről:

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Számításoptimalizált](sizes-compute.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Tároptimalizált](sizes-storage.md)
