---
title: Edv4 és Edsv4 sorozat
description: A Ev4, a Edv4, a Esv4 és a Edsv4 sorozatú virtuális gépek specifikációi.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: d6edee1f349b37c274f71c9db7abac35f9c88c4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557114"
---
# <a name="edv4-and-edsv4-series"></a>Edv4 és Edsv4 sorozat

A Edv4 és a Edsv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, és ideális a különböző, nagy mennyiségű Nagyvállalati alkalmazások számára, és akár 504 GIB RAM, az [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), az [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) és az [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Emellett támogatják az [Intel &reg; Deep learning fellendítését](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)is. Ezek az új virtuálisgép-méretek 50%-kal nagyobb helyi tárterülettel rendelkeznek, valamint jobb helyi lemez-IOPS is használhatók, mint a [Ev3-/Esv3](./ev3-esv3-series.md) -méretek és a Gen2-alapú virtuális [gépek](./generation-2.md). Ez az összes Core Turbo órajel 3,4 GHz-es sebességgel rendelkezik. 

## <a name="edv4-series"></a>Edv4 sorozat

A Edv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) processzorokon futnak. A Edv4 virtuálisgép-méretek akár 504 GiB RAM-mal is rendelkeznek, a gyors és nagyméretű helyi SSD-tárolók mellett (legfeljebb 2 400 GiB). Ezek a virtuális gépek ideálisak a nagy teljesítményű, kis késésű, nagy sebességű helyi tárterületet használó, nagy teljesítményű vállalati alkalmazásokhoz és alkalmazásokhoz. A standard SSD-ket és a standard HDD lemezes tárolást a Edv4 virtuális gépekhez is csatlakoztathatja. 

[ACU](acu.md): 195 – 210<br>
[Premium Storage](premium-storage-performance.md): nem támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott (*legalább 4 vCPU szükséges*)<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | <sup>**</sup> Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


<sup>**</sup>Ezek a IOPs-értékek a [Gen2 virtuális gépek](generation-2.md) használatával garantálva lehetnek

## <a name="edsv4-series"></a>Edsv4 sorozat

A Edsv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) processzorokon futnak. A Edsv4 virtuálisgép-méretek akár 504 GiB RAM-mal is rendelkeznek, a gyors és nagyméretű helyi SSD-tárolók mellett (legfeljebb 2 400 GiB). Ezek a virtuális gépek ideálisak a nagy teljesítményű, kis késésű, nagy sebességű helyi tárterületet használó, nagy teljesítményű vállalati alkalmazásokhoz és alkalmazásokhoz.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott (*legalább 4 vCPU szükséges*)<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | <sup>**</sup> Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |
| Standard_E80ids_v4 <sup>2</sup> | 80 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>1</sup> [korlátozott méretű alapméret érhető el)](./constrained-vcpu.md).

<sup>2</sup> a példány egyetlen ügyfél számára dedikált hardveren van elkülönítve.

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
