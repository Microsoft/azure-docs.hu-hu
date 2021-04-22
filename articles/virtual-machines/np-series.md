---
title: NP-sorozat – Azure Virtual Machines
description: Az NP sorozatú virtuális gépek specifikációi.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 61488b88b00206cb78beed4fe773bf9377848701
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861048"
---
# <a name="np-series"></a>NP-sorozat 
Az NP-sorozat virtuális gépeit [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) FPGA-k működtetik a számítási feladatok felgyorsítására, beleértve a gépi tanulási következtetést, a videóátkódolást és az adatbázis-& elemzéseket. Az NP-sorozat virtuális gépei szintén Intel Xeon 8171M (Skylake) processzorokkal működnek, és az órajel összes magja 3,2 GHz-es.

[Premium Storage:](premium-storage-performance.md)Támogatott<br>
[Premium Storage gyorsítótárazás:](premium-storage-performance.md)Támogatott<br>
[Élő áttelepítés:](maintenance-and-updates.md)Nem támogatott<br>
[Memóriamegőrzési frissítések:](maintenance-and-updates.md)Nem támogatott<br>
Virtuális gépek generációtámogatása: 1. generáció<br>
[Gyorsított hálózat:](../virtual-network/create-vm-accelerated-networking-cli.md)Támogatott<br>
[Aphemeral operációsrendszer-lemezek:](ephemeral-os-disks.md)Nem támogatott <br>
<br>

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Fpga | FPGA-memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális/várt hálózati sávszélessége (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]


##  <a name="frequently-asked-questions"></a>Gyakori kérdések

**K:** A Vfül melyik verzióját használjam? 

**A:** Az Xilinx a [Vtha 2020.2-t javasolja](https://www.xilinx.com/products/design-tools/vitis/vitis-platform.html)


**K:** Np virtuális gépeket kell használnom a megoldásom fejlesztéshez? 

**A:** Nem, fejleszthet a saját környezetében, és üzembe helyezheti a felhőben! Kövesse az igazolási dokumentációt az NP virtuális gépeken való üzembe helyezéshez. 

**K:** Az XRT melyik verzióját használjam?

**A:** xrt_202020.2.8.832 

**K:** Mi a cél üzembe helyezési platform?

**A:** Használja a következő platformokat.
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1 

**K:** Melyik platformot érdemes megcélnom a fejlesztéshez?

**A:** xilinx-u250-gen3x16-xdma-2.1-202010-1-dev_1-2954688_all 

**K:** Mi a támogatott operációs rendszer (operációs rendszer)? 

**A:** Az Xilinx és a Microsoft érvényesíti az Ubuntu 18.04 LTS és a CentOS 7.8 operációs rendszereket.

 Az Xilinx a következő marketplace-rendszerképeket hozta létre a virtuális gépek üzembe helyezésének egyszerűsítése érdekében. 

[Xilinx Alveo U250 üzembe helyezési virtuális gép – Ubuntu18.04](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_ubuntu1804_032321)

[Xilinx Alveo U250 üzembe helyezési virtuális gép – CentOS7.8](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryItemDetailsBladeNopdl/id/xilinx.xilinx_alveo_u250_deployment_vm_centos78_032321)

**K:** Üzembe helyezhetem a saját Ubuntu-/CentOS-alapú virtuális gépemet, és telepítem az XRT/Deployment Target Platformot? 

**V:** Igen.

**K:** Ha saját Ubuntu18.04-es virtuális gépet helyezek üzembe, mik a szükséges csomagok és lépések?

**A:** A Kernel 4.1X használata [Xilinx XRT-dokumentációnként](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf)
       
Telepítse az alábbi csomagokat.
- xrt_202020.2.8.832_18.04-amd64-xrt.deb
       
- xrt_202020.2.8.832_18.04-amd64-azure.deb
       
- xilinx-u250-gen3x16-xdma-platform-2.1-3_all_18.04.deb.tar.gz
       
- xilinx-u250-gen3x16-xdma-validate_2.1-3005608.1_all.deb  

**K:** Ubuntun a virtuális gép újraindítása után nem találom az FPGA-(k)t: 

**A:** Ellenőrizze, hogy a kernel nem lett-e frissítve (uname -a). Ha igen, lépjen vissza a 4.1X kernelre. 

**K:** Ha saját CentOS7.8 virtuális gépet helyezek üzembe, mik a szükséges csomagok és lépések?

**A:** Kernelverzió használata: 3.10.0-1160.15.2.el7.x86_64

 Telepítse az alábbi csomagokat.
   
 - xrt_202020.2.8.832_7.4.1708-x86_64-xrt.rpm 
      
 - xrt_202020.2.8.832_7.4.1708-x86_64-azure.rpm 
     
 - xilinx-u250-gen3x16-xdma-platform-2.1-3.noarch.rpm.tar.gz 
      
 - xilinx-u250-gen3x16-xdma-validate-2.1-3005608.1.noarch.rpm  

**K:** Az xbutil validate CentOS-on való futtatásakor a következő figyelmeztetés jelenik meg: "WARNING: Kernel version 3.10.0-1160.15.2.el7.x86_64 is officially supported. A 4.18.0-193 a legújabb támogatott verzió." 

**A:** Ez nyugodtan figyelmen kívül hagyható. 

**K:** Mi a különbség a onPrem és az NP virtuális gépek között az XRT-re vonatkozóan? 

**A:** Az Azure-ban az XDMA 2.1 platform csak a Host_Mem (SB) és DDR adatmegőrzési funkciókat támogatja. 

A Host_Mem (SB) (1 Gb RAM) engedélyezéséhez: sudo xbutil host_mem --enable --size 1g 

A Host_Mem (SB) letiltása: sudo xbutil host_mem --disable 

**K:** Futtatok xbmgmt-parancsokat? 

**A:** Nem, az Azure-beli virtuális gépeken nem támogatott közvetlenül az Azure-beli virtuális gép kezelése. 

 **K:** Be kell töltenem egy PLP-t? 

**A:** Nem, a PLP-t a rendszer automatikusan betölti, így nem kell xbmgmt-parancsokkal betöltenie. 

 
**K:** Különböző Azure-támogatás plp-eket? 

**A:** Jelenleg nem. Csak az üzembe helyezési platform csomagjaiban biztosított PLP-t támogatjuk. 

**K:** Hogyan lehet lekérdezni a PLP-adatokat? 

**A:** Futtatnia kell az xbutil lekérdezést, és meg kell néznie az alsó részt. 

**K:** Ha saját virtuális gépet hozok létre, és manuálisan telepítem az XRT-t, milyen további módosításokat kell tennem? 

**A:** Az /opt/xilinx/xrt/setup.sh-ban adjon hozzá egy bejegyzést a XRT_INI_PATH-hoz, amely a /opt/xilinx/xrt/xrt.ini

 
Az /opt/xilinx/xrt/xrt.ini tartalma a következőket tartalmazza: <br>
[Runtime]<br>
ert=false <br>

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan hasonlíthatja össze az Azure-beli számítási egységek [(ACU)](acu.md) a számítási teljesítményt az Azure-beli SKU-k között.
