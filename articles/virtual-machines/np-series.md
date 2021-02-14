---
title: NP-Series – Azure Virtual Machines
description: Az NP-sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 2606881cec5527084c3c2788d40f8bdfd8abf5a3
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417594"
---
# <a name="np-series-preview"></a>NP-Series (előzetes verzió)

Az NP-sorozatú virtuális gépeket a [xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA, amely felgyorsítja a számítási feladatokat, beleértve a gépi tanulási következtetéseket, a videó-átkódolást és az adatbázis-keresési & elemzéseket. Az NP-sorozatú virtuális gépeket az Intel Xeon 8171M (Skylake) processzorok is használják, amelyek az összes Core Turbo órajeltel rendelkeznek, 3,2 GHz-es sebességgel.


[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
VM-generáció támogatása: 1. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | FPGA | FPGA memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma/várt hálózati sávszélesség (MBps) | 
|---|---|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok
A támogatott operációs rendszerek teljes listáját a [xilinx Runtime (XRT) kibocsátási megjegyzései](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) között érheti el.

Az előzetes verzió program Microsoft Azure mérnöki csapatai megosztják az illesztőprogramok telepítésére vonatkozó konkrét utasításokat.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
