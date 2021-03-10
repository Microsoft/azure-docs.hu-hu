---
title: Msv2 sorozat (előzetes verzió) – Azure Virtual Machines
description: A Msv2 sorozatú virtuális gépek specifikációi.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562571"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 és Mdsv2 sorozatú közepes memória (előzetes verzió)


> [!IMPORTANT]
> Csatlakozzon az előzetes verzióhoz, és töltse ki az űrlapot a következő címen: **https://aka.ms/Mv2MedMemoryPreview** .  

A Msv2 és a Mdsv2 közepes memória virtuálisgép-sorozata Intel® Xeon® Platinum 8280 (Cascade Lake) processzort tartalmaz, amely a 2,7 GHz-es és a 4,0 GHz-es, egyetlen Core Turbo-gyakorisággal rendelkezik. Ezekkel a virtuális gépekkel az ügyfelek nagyobb rugalmasságot érnek el a helyi lemezekkel és lemez nélküli lehetőségekkel. Az ügyfelek az új elkülönített virtuálisgép-méretekhez is hozzáférhetnek, több PROCESSZORral és memóriával, amelyek akár 192 vCPU is elérhetők 4 TiB memóriával. 


A Msv2 és a Mdsv2 sorozatú virtuális gépek csak 2. generációsak, és támogatják a 2. generációs támogatott lemezképek egy részhalmazát. A Msv2 és a Mdsv2 sorozat által támogatott lemezképek teljes listáját alább találja.  

- Windows Server 2019 vagy újabb
- SUSE Linux Enterprise Server 12 SP4 és újabb, vagy SUSE Linux Enterprise Server 15 SP1 és újabb verziók
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 vagy újabb 
- Oracle Enterprise Linux 7,7 vagy újabb verzió

A 2. generációs virtuális gépekkel kapcsolatos további információkért lásd: a 2. generációs virtuális gépek [támogatása az Azure](./generation-2.md)-ban.



[Premium Storage](premium-storage-performance.md): támogatott<br>
[Premium Storage gyorsítótárazás](premium-storage-performance.md): támogatott<br>
[Élő áttelepítés](maintenance-and-updates.md): nem támogatott<br>
[Memória-megőrzési frissítések](maintenance-and-updates.md): nem támogatott<br>
[VM-létrehozási támogatás](generation-2.md): 2. generáció<br>
[Írásgyorsító](./how-to-enable-write-accelerator.md): támogatott<br>
[Gyorsított hálózatkezelés](../virtual-network/create-vm-accelerated-networking-cli.md): támogatott<br>
[Ideiglenes operációsrendszer-lemezek](ephemeral-os-disks.md): nem támogatott <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 közepes memória lemez nélküli 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 közepes memória lemezzel  

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemez maximális száma | Maximális gyorsítótárazott és ideiglenes tárolási sebesség: IOPS/MBps | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

Árképzési kalkulátor: [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
