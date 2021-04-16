---
title: Dv2 és DSv2 sorozat – Azure Virtual Machines
description: A Dv2 és a Dsv2 sorozatú virtuális gépek specifikációi.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 71a4cebcc11657566f65f53508df18efe822c409
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514784"
---
# <a name="dv2-and-dsv2-series"></a>Dv2 és DSv2 sorozat

A Dv2 és a DSv2 sorozat, amely az eredeti D-sorozat követője, nagyobb teljesítményű processzorral és optimális processzor-memória konfigurációval rendelkezik, így a legtöbb éles számítási feladathoz megfelelő. A Dv2-sorozat körülbelül 35%-kal gyorsabb a D-sorozatnál. A Dv2-sorozat Intel® Xeon® Fog 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz -es (Broadwell) vagy Intel® Xeon® E5-2673 v3 2.4 GHz -es (Haswell) processzorok az Intel Turbo Boost Technology 2.0-val. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

## <a name="dv2-series"></a>Dv2-sorozat

A Dv2-sorozat méretei Intel® Xeon® Majd 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake) vagy az Intel® Xeon platformon futnak®® <2> <2> E5-2673 v4 2,3 GHz (Broadwell) vagy intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorok Intel Turbo Boost Technology 2.0-val.

[ACU:](acu.md)210-250<br>
[Premium Storage:](premium-storage-performance.md)Nem támogatott<br>
[Premium Storage:](premium-storage-performance.md)Nem támogatott<br>
[Élő áttelepítés:](maintenance-and-updates.md)Támogatott<br>
[Memóriamegőrzési frissítések:](maintenance-and-updates.md)Támogatott<br>
[Virtuális gépek generációtámogatása:](generation-2.md)1. generáció<br>
[Gyorsított hálózat:](../virtual-network/create-vm-accelerated-networking-cli.md)Támogatott (*legalább 2 vCPU-t igényel*)<br>
[Aphemeral operációsrendszer-lemezek:](ephemeral-os-disks.md)Nem támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális átviteli sebessége: IOPS/Olvasási MBps/Írási MBps | Adatlemezek max. száma | Átviteli sebesség: IOPS | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3000/46/23    | 4  | 4x500  | 2|750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6000/93/46    | 8  | 8x500  | 2|1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12000/187/93  | 16 | 16x500 | 4|3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24000/375/187 | 32 | 32x500 | 8|6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>DSv2-sorozat

A DSv2-sorozat méretei Intel® Xeon® Majd 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake) vagy az Intel® Xeon platformon futnak® ® E5-2673 v4 2,3 GHz-es (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz -es (Haswell) processzorok Intel Turbo Boost Technology 2.0-val, és prémium szintű tárolást használhatnak.

[ACU:](acu.md)210-250<br>
[Premium Storage:](premium-storage-performance.md)Támogatott<br>
[Premium Storage:](premium-storage-performance.md)Támogatott<br>
[Élő áttelepítés:](maintenance-and-updates.md)Támogatott<br>
[Memóriamegőrzési frissítések:](maintenance-and-updates.md)Támogatott<br>
[Virtuális gépek generációtámogatása:](generation-2.md)1. és 2. generáció<br>
[Gyorsított hálózat:](../virtual-network/create-vm-accelerated-networking-cli.md)Támogatott (*legalább 2 vCPU-t igényel*)<br>
[Aphemeral operációsrendszer-lemezek:](ephemeral-os-disks.md)Támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tároló maximális átviteli sebessége: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótárazás nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2|1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4|3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8|12000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

Díjkalkulátor: [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

További információ a lemeztípusokról: [Lemeztípusok](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan hasonlíthatja össze az Azure-beli számítási egységek [(ACU)](acu.md) a számítási teljesítményt az Azure-beli SKU-k között.
