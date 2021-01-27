---
title: Fsv2 sorozat
description: A Fsv2 sorozatú virtuális gépek specifikációi.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: d633d631a8d905532601ec15700ff80eed644efe
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920156"
---
# <a name="fsv2-series"></a>Fsv2 sorozat

A Fsv2 sorozat az Intel® Xeon® Platinum 8272CL (Cascade Lake) processzorokon és az Intel® Xeon® Platinum 8168 (Skylake) processzorokon fut. A szolgáltatás egy tartós, 3,4 GHz-es, a Turbo órajelét és a 3,7 GHz-es max. Intel® AVX-512 utasítások az Intel skálázható processzorok újdonságai. Ezek az utasítások akár kétszeres teljesítmény-növekedést is biztosítanak a vektoros feldolgozási feladatokhoz egy-és dupla pontosságú lebegőpontos műveletekben. Más szóval a számítási feladatokhoz nagyon gyorsak.

Az Fsv2 sorozatú virtuális gépek Intel® Hyper-Threading technológiával rendelkeznek.

[ACU](acu.md): 195 – 210<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30000 |

<sup>1</sup> a 64-nál több vCPU használata a következő támogatott vendég operációs rendszerek egyikét igényli:

- Windows Server 2016 vagy újabb
- Ubuntu 16,04 LTS vagy újabb, az Azure-ban hangolt kernel (4,15 kernel vagy újabb)
- SLES 12 SP2 vagy újabb
- RHEL vagy CentOS 6,7-es verzió – 6,10, a Microsoft által biztosított LIS-csomag 4.3.1-es (vagy újabb) verziójával
- RHEL vagy CentOS 7,3-es verzió, a Microsoft által biztosított LIS csomag 4.2.1 (vagy újabb) telepítésével
- RHEL vagy CentOS 7,6-es vagy újabb verzió
- Oracle Linux UEK4 vagy újabb verzióval
- Debian 9 a backports rendszermaggal, Debian 10 vagy újabb verzióval
- CoreOS 4,14 kernelsel vagy újabb verzióval

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