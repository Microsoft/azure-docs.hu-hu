---
title: NP-Series – Azure Virtual Machines
description: Az NP-sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: aa67a858d0396badc25a625b23dc2f2fdf1bdff9
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551373"
---
# <a name="np-series"></a>NP sorozat 
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
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** A Vitis milyen verzióját érdemes használni? 

**A:** A xilinx a [Vitis 2020,2](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html) -et ajánlja


**K:** Kell-e NP virtuális gépeket használni a megoldás fejlesztéséhez? 

**A:** Nem, a helyszínen fejlesztheti és üzembe helyezheti a felhőben. Győződjön meg arról, hogy kövesse az igazolási dokumentációt az NP virtuális gépeken való üzembe helyezéshez. 

**K:** Milyen XRT-verziót használok?

**A:** xrt_202020.2.8.832 

**K:** Mi a cél telepítési platform?

**A:** Használja az alábbi platformokat.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1 – 3005608.1 

**K:** Milyen platformra van szükségem a fejlesztéshez?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**K:** Mik a támogatott operációs rendszerek? 

**A:** A xilinx és a Microsoft érvényesített Ubuntu 18,04 LTS és CentOS 7,8.

 A xilinx a következő Piactéri rendszerképeket hozta létre a virtuális gépek üzembe helyezésének egyszerűsítése érdekében. 

[Xilinx Alveo U250 üzembe helyezési VM – Ubuntu 18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 üzembe helyezési VM – CentOS 7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**K:** Telepíthetem saját Ubuntu/CentOS virtuális gépeket, és telepíthetem a XRT/Deployment Target platformot? 

**V:** Igen.

**K:** Ha saját Ubuntu 18.04 virtuális gépet telepítek, akkor Mik a szükséges csomagok és lépések?

**A:** Kernel 4.1 X/ [xilinx XRT dokumentáció](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) használata
       
Telepítse a következő csomagokat.
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-XRT. deb
       
- xrt_202020 xrt_202020.2.8.832_18.04-amd64-Azure. deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04. deb. tar. gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1 – 3005608.1_all. deb  

**K:** Ubuntu rendszeren a virtuális gép újraindítása után nem találom a FPGA (ka) t: 

**A:** Győződjön meg arról, hogy a kernel nem lett frissítve (uname-a). Ha igen, térjen vissza a kernel 4.1 X verzióra. 

**K:** Ha üzembe helyezem a saját CentOS 7.8 virtuális gépet, akkor mi a szükséges csomagok és lépések?

**A:** Kernel verziójának használata: 3.10.0-1160.15.2.el7.x86_64

 Telepítse a következő csomagokat.
   
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-XRT. rpm 
      
 - xrt_202020 xrt_202020.2.8.832_7.4.1708-x86_64-Azure. rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1 -3. a. 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1 – 3005608.1...  

**K:** A xbutil-ellenőrzés futtatásakor a CentOS-ben a következő figyelmeztetés jelenik meg: "Figyelmeztetés: a kernel verziója 3.10.0-1160.15.2.el7.x86_64 nem hivatalosan támogatott. a 4.18.0-193 a legújabb támogatott verzió. " 

**A:** Ezt nyugodtan figyelmen kívül hagyhatja. 

**K:** Mi a különbség a helyszíni és az NP virtuális gépek között a XRT kapcsolatban? 

**A:** Az Azure-ban a XDMA 2,1 platform csak a Host_Mem (SB) és a DDR adatmegőrzési funkciókat támogatja. 

Host_Mem (SB) engedélyezése (1 GB RAM): sudo xbutil host_mem--Enable--size 1g 

Host_Mem letiltása (SB): sudo xbutil host_mem--Disable 

**K:** Futtathatok xbmgmt parancsokat? 

**A:** Nem, az Azure-beli virtuális gépeken az Azure-beli virtuális gépen nincs felügyeleti támogatás. 

 **K:** Be kell tölteni egy PLP? 

**A:** Nem, a PLP automatikusan betöltődik, így nem kell betölteni a xbmgmt-parancsokon keresztül. 

 
**K:** Támogatja az Azure a különböző PLPs? 

**A:** Jelenleg nem. Csak a telepítési platform csomagjaiban megadott PLP támogatjuk. 

**K:** Hogyan lehet lekérdezni a PLP adatait? 

**A:** Xbutil-lekérdezést kell futtatnia, és meg kell vizsgálnia az alsó részt. 

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
