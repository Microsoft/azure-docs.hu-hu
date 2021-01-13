---
title: NVv4 sorozat
description: A NVv4 sorozatú virtuális gépek specifikációi.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 67780c725466707f347e9009a77099786cc64939
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133293"
---
# <a name="nvv4-series"></a>NVv4 sorozat 

A NVv4 sorozatú virtuális gépeket az [AMD Radeon ösztön MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-k és az AMD EPYC 7V12 (Róma) CPU-k működtetik. A NVv4 sorozatú Azure a részleges GPU-val rendelkező virtuális gépek bevezetését ismerteti. Válassza ki a megfelelő méretű virtuális gépet GPU-gyorsított grafikus alkalmazások és virtuális asztalok számára a GPU-k 1/8-étől kezdődően 2 GiB frame-pufferrel egy teljes GPU-val, 16 GiB frame-pufferrel. A NVv4 Virtual Machines jelenleg csak a Windows vendég operációs rendszert támogatja.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
Gyorsított hálózatkezelés: támogatott<br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma/várt hálózati sávszélesség (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> a NVv4 sorozatú virtuális gépek AMD szimultán többszálú technológiával rendelkeznek

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

A Windows rendszerű Azure NVv4-sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat.

Az AMD GPU-illesztőprogramok manuális telepítéséhez lásd: [N-sorozatú AMD GPU illesztőprogram-telepítő a Windows](./windows/n-series-amd-driver-setup.md) rendszerhez támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
