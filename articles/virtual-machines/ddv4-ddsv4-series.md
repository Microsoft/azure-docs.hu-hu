---
title: Ddv4 és Ddsv4 sorozat
description: A DV4, a Ddv4, a Dsv4 és a Ddsv4 sorozatú virtuális gépek specifikációi.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: feabdcef9298c93f0cba93d3eeb9ebb0a32d6ef2
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560344"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4 és Ddsv4 sorozat

A Ddv4 és a Ddsv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, és jobb ár-arányt biztosít a legtöbb általános célú munkaterheléshez. A szolgáltatás a 3,4 GHz-es és az Intel [ &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), az [Intel &reg; Hyper-Threading technológia](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) és az [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html)minden alapvető Turbo órajelét tartalmazza. Emellett támogatják az [Intel &reg; Deep learning fellendítését](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html)is. Ezek az új virtuálisgép-méretek 50%-kal nagyobb helyi tárterülettel rendelkeznek, valamint jobb helyi lemez-IOPS is használhatók, mint a [Dv3-/Dsv3](./dv3-dsv3-series.md) -méretek és a Gen2-alapú virtuális [gépek](./generation-2.md).

A D sorozat használati esetei többek között nagyvállalati szintű alkalmazások, a kapcsolódó adatbázisok, a memórián belüli gyorsítótárazás és az elemzések.

## <a name="ddv4-series"></a>Ddv4 sorozat

A Ddv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) futnak. A Ddv4 sorozat vCPU, memória és ideiglenes lemez kombinációját kínálja a legtöbb éles számítási feladathoz.

Az új Ddv4 virtuálisgép-méretek közé tartozik a gyors, nagyobb helyi SSD-tároló (akár 2 400 GiB is), és olyan alkalmazások számára készült, amelyek alacsony késésű, nagy sebességű helyi tárterületet igényelnek, például olyan alkalmazásokhoz, amelyeknél gyors olvasási/írási műveletek szükségesek a temp Storage szolgáltatáshoz, vagy a gyorsítótárak vagy ideiglenes fájlok átmeneti tárolására van szükségük. A Ddv4 virtuális gépekhez szabványos SSD-ket és standard HDD-tárolót is csatolhat. A távoli adatlemezek tárterületét a rendszer külön számlázza a virtuális gépektől.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): nem támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott (*legalább 4 vCPU szükséges*)<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
<br> 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | <sup>**</sup> Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>**</sup>Ezek a IOPs-értékek a [Gen2 virtuális gépek](generation-2.md) használatával garantálva lehetnek

## <a name="ddsv4-series"></a>Ddsv4 sorozat

A Ddsv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) fut. A Ddsv4 sorozat vCPU, memória és ideiglenes lemez kombinációját kínálja a legtöbb éles számítási feladathoz.

Az új Ddsv4 virtuálisgép-méretek közé tartozik a gyors, nagyobb helyi SSD-tároló (akár 2 400 GiB is), és olyan alkalmazások számára készült, amelyek alacsony késésű, nagy sebességű helyi tárterületet igényelnek, például olyan alkalmazásokhoz, amelyeknél gyors olvasási/írási műveletek szükségesek a temp Storage szolgáltatáshoz, vagy a gyorsítótárak vagy ideiglenes fájlok átmeneti tárolására van szükségük. 

 > [!NOTE]
 >A Ddsv4-méretek díjszabása és számlázási mérőszámai azonosak a Ddv4 sorozattal.

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
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>**</sup>Ezek a IOPs-értékek a [Gen2 virtuális gépek](generation-2.md) használatával garantálva lehetnek

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
