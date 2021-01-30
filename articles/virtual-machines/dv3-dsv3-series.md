---
title: Dv3 és DSv3 sorozat
description: A Dv3 és a Dsv3 sorozatú virtuális gépek specifikációi.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 215f907edb01cc35ffa4e9bb683964f8eda11d94
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089325"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 és DSv3 sorozat

A Dv3 sorozat Intel® Xeon® Platinum 8272CL (Cascade Lake) fut, Intel® Xeon® 8171M 2.1 GHz-es (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell), vagy az Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorok egy Hyper-threaded konfigurációban, ami jobb értéket biztosít a legtöbb általános célú munkaterheléshez. A memória ki lett bontva (~ 3,5 GiB/vCPU – 4 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer alapszinten igazította ki, hogy az a feleznie-re legyen igazítva. A Dv3-sorozat már nem rendelkezik a D/Dv2 sorozat nagy memóriabeli virtuálisgép-méretével, ezeket a rendszer áthelyezte a memória-optimalizált [Ev3 és Esv3-sorozatba](ev3-esv3-series.md).

Példa: a D sorozat használati esetei nagyvállalati szintű alkalmazások, a kapcsolódó adatbázisok, a memórián belüli gyorsítótárazás és az elemzések.

## <a name="dv3-series"></a>Dv3-sorozat

Az Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) vagy Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorok [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html)-Dv3 sorozatos méretei. A Dv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

Az adatlemezes tárolást a virtuális gépektől függetlenül számlázzuk. Prémium szintű tárolólemezek használatához Dsv3-méreteket vegyen igénybe. A Dsv3-méretek díjszabása és számlázási mérőszámai megegyeznek a Dv3-sorozatéval.

Az Dv3 sorozatú virtuális gépek Intel® Hyper-Threading technológiával rendelkeznek.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): nem támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-generáció támogatása](generation-2.md): 1. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális Temp Storage átviteli sebessége: IOPS/olvasási MBps/írási MBps | Hálózati adapterek maximális száma/sávszélesség |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-sorozat

Az Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) vagy Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorok az [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) használatával futnak, és a Premium Storage-t használják a Dsv3 sorozatokra. A Dsv3-sorozat méretei vCPU, memória és ideiglenes tárterület kombinációját nyújtják a legtöbb éles számítási feladathoz.

Az Dsv3 sorozatú virtuális gépek Intel® Hyper-Threading technológiával rendelkeznek.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): támogatott<br>
[Memória megőrzésének frissítései](maintenance-and-updates.md): támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps (gyorsítótár mérete GiB-ban) | A burst gyorsítótárban tárolt és az ideiglenes tárolási sebesség maximális száma: IOPS/MBps<sup>1</sup> | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Nem gyorsítótárazott lemez maximális adatátviteli sebessége: IOPS/MBps<sup>1</sup> | Hálózati adapterek maximális száma/várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  a Dsv3 sorozatú virtuális [gépek a lemezük teljesítményét felhasználhatják](./disk-bursting.md) , és akár 30 percet is igénybe vehetnek.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)
- [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
- További információ a lemezek típusairól: [milyen típusú lemezek érhetők el az Azure-ban?](disks-types.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.