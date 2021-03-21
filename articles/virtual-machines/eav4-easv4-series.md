---
title: Eav4 és Easv4 sorozat
description: A Eav4 és a Easv4 sorozatú virtuális gépek specifikációi.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ec320d7eeebbe5eab14061cd5c194f15acf5638a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557930"
---
# <a name="eav4-and-easv4-series"></a>Eav4 és Easv4 sorozat

A Eav4 sorozat és a Easv4 sorozat az AMD 2.35 GHz EPYC<sup>TM</sup> 7452 processzorát egy többszálas konfigurációban, akár 256mb L3 gyorsítótárral, a legtöbb memóriára optimalizált számítási feladatok futtatására szolgáló lehetőségek növelésével teszi elérhetővé. A Eav4-sorozat és a Easv4-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a Ev3 & Esv3 sorozat.

## <a name="eav4-series"></a>Eav4 sorozat

[ACU](acu.md): 230 – 260<br>
[Premium Storage](premium-storage-performance.md): nem támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-generáció támogatása](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott (*legalább 4 vCPU szükséges*)<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
<br>

A Eav4-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely növelheti a 3.35 GHz-es maximális gyakoriságot. A Eav4-sorozat méretei ideálisak a nagy mennyiségű, nagyvállalati alkalmazások számára. Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. A prémium SSD használatához használja a Easv4-sorozat méretét. A Easv4-méretek díjszabása és számlázási mérőszámai megegyeznek a Eav3 sorozattal.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard \_ E2a \_ v4|2|16|50|4|3000 / 46 / 23|2 | 800 |
| Standard \_ E4a \_ v4|4|32|100|8|6000 / 93 / 46|2 | 1600 |
| Standard \_ E8a \_ v4|8|64|200|16|12000 / 187 / 93|4 | 3200 |
| Standard \_ E16a \_ v4|16|128|400|32|24000 / 375 / 187|8 | 6400 |
| Standard \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 8000 |
| Standard \_ E32a \_ v4|32|256|800|32|48000 / 750 / 375|8 | 12800 |
| Standard \_ E48a \_ v4|48|384|1200|32|96000/1000 (500)|8 | 19200 |
| Standard \_ E64a \_ v4|64|512|1600|32|96000/1000 (500)|8 | 25600 |
| Standard \_ E96a \_ v4|96|672|2400|32|96000/1000 (500)|8 | 32000 |

## <a name="easv4-series"></a>Easv4 sorozat

[ACU](acu.md): 230 – 260<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-generáció támogatása](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott (*legalább 4 vCPU szükséges*)<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
<br>

A Easv4-sorozat méretei a 2.35 GHz-es AMD EPYC<sup>TM</sup> 7452 processzoron alapulnak, amely növelheti a 3.35 GHz-es maximális gyakoriságot, és prémium SSD-t használhat. A Easv4-sorozat méretei ideálisak a nagy mennyiségű, nagyvállalati alkalmazások számára.

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 32000 |

<sup>1</sup> [korlátozott méretű alapméret érhető el](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

Árképzési kalkulátor: [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)

További információ a lemezek típusairól: [lemezek típusai](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
