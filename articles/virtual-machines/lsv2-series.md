---
title: Lsv2 sorozat – Azure Virtual Machines
description: A Lsv2 sorozatú virtuális gépek specifikációi.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 787f9ada921e1580a34431568e87c41496b37f6a
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563200"
---
# <a name="lsv2-series"></a>Lsv2 sorozat

A Lsv2 sorozat nagy átviteli sebességű, kis késleltetésű, közvetlenül leképezett helyi NVMe-tárolót biztosít az [AMD EPYC<sup>TM</sup> 7551 processzoron](https://www.amd.com/en/products/epyc-7000-series) , amely a 2.55 GHz-es és a 3,0 GHz-es maximális lendülettel rendelkezik. A Lsv2 sorozatú virtuális gépek mérete 8 – 80 vCPU, egyidejű többszálú konfigurációban.  VCPU 8 GiB memória, valamint egy 1.92 TB NVMe SSD M. 2 eszköz/8 vCPU, amely akár 19.2 TB (10x 1.92 TB) érhető el a L80s v2-ben.

> [!NOTE]
> A Lsv2 sorozatú virtuális gépek úgy vannak optimalizálva, hogy az állandó adatlemezek használata helyett közvetlenül a virtuális géphez csatolt helyi lemezt használják. Ez nagyobb IOPs/átviteli sebességet tesz lehetővé a munkaterhelések számára. A Lsv2 és az ls-sorozat nem támogatja a helyi gyorsítótár létrehozását, hogy növelje a tartós adatlemezekkel elérhető IOPs.
>
> A helyi lemez nagy átviteli sebessége és IOPs révén a Lsv2 sorozatú virtuális gépek ideálisak olyan NoSQL-tárolók számára, mint például az Apache Cassandra és a MongoDB, amelyek több virtuális gépen replikálják az adatmegőrzést, hogy egyetlen virtuális gép meghibásodása esetén is megmaradjon az adatok.
>
> További információ: a teljesítmény optimalizálása a Lsv2-sorozatú virtuális gépeken [Windows](../virtual-machines/windows/storage-performance.md) vagy [Linux](../virtual-machines/linux/storage-performance.md)rendszeren.  

[ACU](acu.md): 150-175<br>
[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): nem támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 1. és 2. generáció<br>
Feltört: támogatott<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>

| Méret | vCPU | Memória (GiB) | <sup>1</sup> . ideiglenes lemez (GIB) | NVMe-lemezek<sup>2</sup> | NVMe lemez átviteli sebessége<sup>3</sup> (olvasási IOPS/Mbps) | Nem gyorsítótárazott adatlemez átviteli sebessége (IOPs/MBps)<sup>4</sup> | A nem gyorsítótárazott adatlemezek maximális adatátviteli sebessége (IOPs/MBps)<sup>5</sup>| Adatlemezek maximális száma | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x 1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 m/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2.2 m/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x 1.92 TB  | 2,9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10x 1.92 TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> a Lsv2 sorozatú virtuális gépek szabványos SCSI-alapú ideiglenes erőforrás-lemezzel rendelkeznek az operációs rendszer lapozófájljának/swap-fájljának használatához (D: Windows rendszeren, Linuxon/dev/sdb). Ez a lemez 80 GiB tárterületet, 4 000 IOPS és 80 MBps átviteli sebességet biztosít minden 8 vCPU (például Standard_L80s_v2 biztosítja a 800 GiB-t az 40 000 IOPS és a 800 MBPS-ban). Ez biztosítja, hogy a NVMe-meghajtók teljes mértékben kiállíthatók legyenek az alkalmazások általi használatra. Ez a lemez elmúló, és az összes adatvesztés a Leállítás/felszabadítás során elvész.

<sup>2</sup> a helyi NVMe-lemezek elmúlóak, a virtuális gép leállítása/felszabadítása után az adatvesztés történik.

<sup>3</sup> a Hyper-V NVMe Direct technológiája nem szabályozott hozzáférést biztosít a helyi NVMe-meghajtókhoz, amelyek biztonságosan vannak leképezve a vendég virtuális gép területére.  A maximális teljesítmény eléréséhez a legújabb WS2019 Build vagy Ubuntu 18,04 vagy 16,04 használatával kell használnia az Azure Marketplace-en.  Az írási teljesítmény az IO-méret, a meghajtó terhelése és a kapacitás kihasználtsága alapján változhat.

<sup>4</sup> a Lsv2 sorozatú virtuális gépek nem biztosítanak gazdagép-gyorsítótárat az adatlemez számára, mivel az nem használja ki a Lsv2 számítási feladatait.

<sup>5</sup> a Lsv2 sorozatú virtuális gépek egyszerre akár 30 [percig is elérhetik a lemez](./disk-bursting.md) teljesítményét. 

<sup>6</sup> több mint 64 vCPU rendelkező virtuális gépekhez a következő támogatott vendég operációs rendszerek egyike szükséges:

- Windows Server 2016 vagy újabb
- Ubuntu 16,04 LTS vagy újabb, az Azure-ban hangolt kernel (4,15 kernel vagy újabb)
- SLES 12 SP2 vagy újabb
- RHEL vagy CentOS 6,7-es verzió – 6,10, a Microsoft által biztosított LIS-csomag 4.3.1-es (vagy újabb) verziójával
- RHEL vagy CentOS 7,3-es verzió, a Microsoft által biztosított LIS csomag 4.2.1 (vagy újabb) telepítésével
- RHEL vagy CentOS 7,6-es vagy újabb verzió
- Oracle Linux UEK4 vagy újabb verzióval
- Debian 9 a backports rendszermaggal, Debian 10 vagy újabb verzióval
- CoreOS 4,14 kernelsel vagy újabb verzióval

## <a name="size-table-definitions"></a>Mérettábla definíciói

- A tárolókapacitás mértékegysége GiB (gibibájt = 1024^3 bájt). A gigabájtban (1000^3 bájt) és a gibibájtban (1024^3 bájt) mért meghajtók összehasonlításakor tartsa észben, hogy a GiB-ban kifejezett kapacitások kisebbnek tűnhetnek. Például: 1023 GiB = 1098,4 GB
- A lemezteljesítmény másodpercenkénti bemeneti/kimeneti műveletek (IOPS) mennyiségeként van kifejezve, valamint MBps-ben, ahol 1 MBps = 10^6 bájt/másodperc.
- Ha a legjobb teljesítményt szeretné használni a virtuális gépek számára, az adatlemezek számát a vCPU 2 lemezre kell korlátoznia.
- A **várt hálózati sávszélesség** a virtuálisgép- [típusokban lefoglalt](../virtual-network/virtual-machine-network-throughput.md) maximális összesített sávszélesség az összes hálózati adapteren, minden célhelynél. A felső határértékek nem garantáltak, csak útmutatóul szolgálnak a kívánt alkalmazásra megfelelő VM-típus kiválasztásához. A tényleges hálózati teljesítmény számos tényezőtől függ, többek között a hálózat túlterhelésétől, az alkalmazás terhelésétől, valamint az alkalmazás hálózati beállításaitól. A hálózati átviteli sebesség optimalizálásával kapcsolatos információkért lásd: [A hálózati átviteli sebesség optimalizálása Windows és Linux rendszeren](../virtual-network/virtual-network-optimize-network-bandwidth.md). Linux vagy Windows rendszeren a várt hálózati teljesítmény eléréséhez egy adott verzió kiválasztására vagy a virtuális gép optimalizálására lehet szükség. További információkért lásd: [Virtuális gépek átviteli sebességének megbízható tesztelése](../virtual-network/virtual-network-bandwidth-testing.md).


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