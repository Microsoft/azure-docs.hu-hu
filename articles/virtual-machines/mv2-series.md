---
title: Mv2 sorozat – Azure Virtual Machines
description: A Mv2 sorozatú virtuális gépek specifikációi.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: b15fdc3826a72e9cfb039b6b2994179ab9565404
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562520"
---
# <a name="mv2-series"></a>Mv2 sorozat

A Mv2 sorozat nagy átviteli sebességű, kis késleltetésű platformot használ egy Hyper-threaded Intel® Xeon® Platinum 8180M 2.5 GHz-es (Skylake) processzoron, amely a 2,5 GHz-es és a max. 3,8 GHz-es maximális Turbo-gyakorisággal rendelkezik. A Mv2 sorozatú virtuálisgép-méretek standard és prémium szintű állandó lemezeket is használhatnak. A Mv2 sorozat példányai a memóriára optimalizált virtuálisgép-méretek, amelyek páratlan számítási teljesítményt biztosítanak a nagy memóriában lévő adatbázisok és munkaterhelések támogatásához, és magas memória-CPU aránnyal rendelkeznek, amely ideális a kapcsolódó adatbázis-kiszolgálók, a nagyméretű gyorsítótárak és a memóriabeli elemzések számára.

Az Mv2 sorozatú virtuális gépek funkciójának Intel® Hyper-Threading technológiája

[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 2. generáció<br>
[Írásgyorsító](./how-to-enable-write-accelerator.md): támogatott<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

|Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS/MBps (gyorsítótár mérete GiB-ban) | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_M208ms_v2<sup>1</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M208s_v2<sup>1</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 | 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 | 32000 |

<sup>1</sup> a Mv2 sorozatú virtuális gépek csak 2. generációsak, és támogatják a 2. generációs támogatott lemezképek egy részhalmazát. A Mv2 sorozat által támogatott lemezképek teljes listáját alább találja. Ha Linuxot használ, tekintse meg a [2. generációs virtuális gépek támogatása az Azure](./generation-2.md) -ban című témakört, amely útmutatást nyújt a rendszerképek megkereséséhez és kiválasztásához. Ha Windows rendszert használ, tekintse meg a [2. generációs virtuális gépek támogatása az Azure](./generation-2.md) -ban című témakört, amely útmutatást nyújt a képek megkereséséhez és kiválasztásához. 

- Windows Server 2019 vagy újabb
- SUSE Linux Enterprise Server 12 SP4 és újabb, vagy SUSE Linux Enterprise Server 15 SP1 és újabb verziók
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 vagy újabb 
- Oracle Enterprise Linux 7,7 vagy újabb verzió



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
