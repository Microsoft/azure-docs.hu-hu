---
title: Msv2/Mdsv2 közepes memória sorozat – Azure Virtual Machines
description: Az Msv2 sorozatú virtuális gépek specifikációi.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: d85623184ad52fb0d4acd4c49d08badfaf886b30
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728229"
---
# <a name="msv2-and-mdsv2-series-medium-memory"></a>Msv2 és Mdsv2 sorozatú közepes memória

Az Msv2 és Mdsv2 közepes memóriaű virtuálisgép-sorozat Intel® Xeon® Valamint Egymagos Turbo 8280 (Cascade Lake) processzort tartalmaz, és az alapkésése 2,7 GHz-es és 4,0 GHz-es egymagos turbo-gyakoriságú. Ezekkel a virtuális gépekkel az ügyfelek nagyobb rugalmasságot érhetnek el a helyi lemezekkel és lemez nélküli beállításokkal. Az ügyfelek új, elkülönített virtuálisgép-méretek készletét is elérhetik, amelyek processzora és memóriája akár 192 virtuális processzorral és 4 TiB memóriával is elérhető. 


Az Msv2 és Mdsv2 sorozatú virtuális gépek csak a 2. generációs virtuális gépek, és a 2. generációs támogatott rendszerképek egy részkészletét támogatják. Az Msv2 és Mdsv2 sorozat támogatott rendszerképei teljes listáját alább láthatja.  

- Windows Server 2019 vagy újabb
- SUSE Linux Enterprise Server 12 SP4 vagy újabb, vagy SUSE Linux Enterprise Server 15 SP1 vagy újabb
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 vagy újabb 
- Oracle Enterprise Linux 7.7 vagy újabb verzió

További információ a 2. generációs virtuális gépekről: 2. generációs virtuális gépek [támogatása az Azure-ban.](./generation-2.md)



[Premium Storage:](premium-storage-performance.md)Támogatott<br>
[Premium Storage gyorsítótárazás:](premium-storage-performance.md)Támogatott<br>
[Élő áttelepítés:](maintenance-and-updates.md)Nem támogatott<br>
[Memóriamegőrzési frissítések:](maintenance-and-updates.md)Nem támogatott<br>
[Virtuális gépek generációtámogatása:](generation-2.md)2. generáció<br>
[írásgyorsító:](./how-to-enable-write-accelerator.md)Támogatott<br>
[Gyorsított hálózat:](../virtual-network/create-vm-accelerated-networking-cli.md)Támogatott<br>
[Aphemeral operációsrendszer-lemezek:](ephemeral-os-disks.md)Nem támogatott <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 Közepes memória lemez nélküli 

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótárazás nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 közepes memória lemezzel  

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemez max. | Gyorsítótárazott és ideiglenes tárterület maximális átviteli sebessége: IOPS /MBps | Gyorsítótárazás nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma | Várt hálózati sávszélesség (Mbps) | 
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

Díjkalkulátor: [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan hasonlíthatja össze az Azure-beli SKU-k számítási teljesítményét az Azure számítási egységei [(ACU)](acu.md) segítségével.
