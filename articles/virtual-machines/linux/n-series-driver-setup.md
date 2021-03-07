---
title: Azure N-Series GPU illesztőprogram-telepítő Linux rendszerhez
description: NVIDIA GPU-illesztőprogramok beállítása az Azure-ban Linux rendszerű N sorozatú virtuális gépekhez
services: virtual-machines-linux
author: vikancha-MSFT
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: 766f6cb0515f45fa11ee26ba23e79ae51fff5ce3
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436090"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>NVIDIA GPU-illesztőprogramok telepítése a Linuxon futó N sorozatú virtuális gépeken

Az NVIDIA GPU-k által támogatott Azure N-sorozatú virtuális gépek GPU-képességeinek kihasználásához az NVIDIA GPU-illesztőprogramokat kell telepítenie. Az [NVIDIA GPU illesztőprogram-bővítmény](../extensions/hpccompute-gpu-linux.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. Telepítse vagy kezelje a bővítményt a Azure Portal vagy az eszközök, például az Azure CLI vagy a Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](../extensions/hpccompute-gpu-linux.md) a támogatott disztribúciók és telepítés lépéseihez.

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, ez a cikk a támogatott disztribúciókat, illesztőprogramokat és telepítési és ellenőrzési lépéseket tartalmazza. A manuális illesztőprogram-telepítési információk a Windows rendszerű [virtuális gépek](../windows/n-series-driver-setup.md)esetében is elérhetők.

Az N sorozatú virtuális gépekhez tartozó specifikációk, a tárolókapacitások és a lemezek részleteiért lásd: [GPU LINUXOS VM-méretek](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>A CUDA-illesztőprogramok telepítése N sorozatú virtuális gépeken

Az alábbi lépésekkel telepítheti a CUDA-illesztőprogramokat az NVIDIA CUDA Toolkit-ből az N sorozatú virtuális gépeken. 

A C és C++ fejlesztők igény szerint telepíthetik a teljes eszközkészletet a GPU-gyorsított alkalmazások létrehozásához. További információ: a [CUDA telepítési útmutatója](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

A CUDA-illesztőprogramok telepítéséhez létesítsen SSH-kapcsolatokat az egyes virtuális gépekhez. A következő parancs futtatásával ellenőrizheti, hogy a rendszer rendelkezik-e CUDA-kompatibilis GPU-val:

```bash
lspci | grep -i NVIDIA
```
A következő példához hasonló kimenet jelenik meg (NVIDIA Tesla K80 kártya):

![lspci parancs kimenete](./media/n-series-driver-setup/lspci.png)

a lspci felsorolja a virtuális gépen található PCIe-eszközöket, beleértve a InfiniBand NIC-t és a GPU-t, ha van ilyen. Ha a lspci nem ad vissza sikeres értéket, lehet, hogy telepítenie kell a következőt: LIS/RHEL (utasítások alább).
Ezután futtassa az adott disztribúcióhoz tartozó telepítési parancsokat.

### <a name="ubuntu"></a>Ubuntu 

1. Töltse le és telepítse a CUDA-illesztőprogramokat az NVIDIA webhelyről. Például Ubuntu 16,04 LTS esetén:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   A telepítés több percet is igénybe vehet.

2. A teljes CUDA-eszközkészlet telepítéséhez írja be a következőt:

   ```bash
   sudo apt-get install cuda
   ```

3. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.

#### <a name="cuda-driver-updates"></a>A CUDA-illesztőprogram frissítései

Javasoljuk, hogy a telepítés után rendszeresen frissítse a CUDA-illesztőprogramokat.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux

1. Frissítse a kernelt (ajánlott). Ha úgy dönt, hogy nem frissíti a kernelt, győződjön meg arról, hogy a `kernel-devel` és a `dkms` rendszermagja megfelelő-e.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Telepítse a [Hyper-V és az Azure legújabb linuxos integrációs szolgáltatásait](https://www.microsoft.com/download/details.aspx?id=55106). Ellenőrizze, hogy szükséges-e a LIS a lspci eredményeinek ellenőrzéséhez. Ha az összes GPU-eszköz a várt módon szerepel (és dokumentálva van), a LIS telepítése nem szükséges.

   Vegye figyelembe, hogy a LIS a Red Hat Enterprise Linux, a CentOS és a Oracle Linux Red hat-kompatibilis kernel 5.2-5.11, 6.0-6.10 és 7.0-7.7 rendszerre vonatkozik. Tekintse át a [Linux Integration Services dokumentációját] ( https://www.microsoft.com/en-us/download/details.aspx?id=55106) További részletekért. 
   Ugorja át ezt a lépést, ha a CentOS/RHEL 7,8 (vagy újabb verzió) használatát tervezi, mert az nem szükséges a következő verziókhoz: LIS.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Kapcsolódjon újra a virtuális géphez, és folytassa a telepítést a következő parancsokkal:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A telepítés több percet is igénybe vehet. 

4. A teljes CUDA-eszközkészlet telepítéséhez írja be a következőt:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Ha a hiányzó csomagokhoz (például a Vulkan-fájlrendszerhez) kapcsolódó hibaüzenet jelenik meg, akkor előfordulhat, hogy szerkesztenie kell a/etc/yum.Repos.d/RH-Cloud-t, meg kell keresnie az opcionális-RPMs és az 1 értékre van állítva
   >  

5. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.

### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése

Ha le szeretné kérdezni a GPU-eszköz állapotát, SSH-t a virtuális gépre, és futtassa az illesztőprogrammal telepített [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet jelenik meg. Vegye figyelembe, hogy a **GPU-util** 0%-ot mutat, ha jelenleg nem fut GPU-s munkaterhelés a virtuális gépen. Az illesztőprogram verziószáma és a GPU adatai eltérhetnek a megjelenített adatoktól.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

A RDMA hálózati kapcsolat engedélyezhető a RDMA-kompatibilis N sorozatú virtuális gépeken, például az azonos rendelkezésre állási csoporton vagy egy virtuálisgép-méretezési csoportban üzembe helyezett egyetlen elhelyezési csoportban is. A RDMA-hálózat támogatja a Message Passing Interface (MPI) forgalmat az Intel MPI 5. x vagy újabb verzióját futtató alkalmazások esetében. További követelmények:

### <a name="distributions"></a>Disztribúciók

Telepítsen RDMA-kompatibilis N sorozatú virtuális gépeket az Azure Marketplace egyik rendszerképéből, amely támogatja az N sorozatú virtuális gépek RDMA-kapcsolatait:
  
* **Ubuntu 16,04 LTS** – RDMA-illesztőprogramok konfigurálása a virtuális gépen, és az Intel MPI letöltése az Intel használatával:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* A **CentOS-alapú 7,4 HPC** -RDMA illesztőprogramok és az Intel MPI 5,1 telepítve vannak a virtuális gépen.

* **CentOS-alapú HPC** -CentOS-HPC 7,6 és újabb (olyan SKU-ra, ahol a INFINIBAND támogatott SR-IOV-en keresztül). Ezek a lemezképek előre telepített Mellanox-OFED és MPI-könyvtárakkal rendelkeznek.

> [!NOTE]
> CX3-Pro kártyákat csak a Mellanox OFED LTS-verziói támogatják. ConnectX3-Pro kártyával rendelkező N sorozatú virtuális gépeken használja az LTS Mellanox OFED verzióját (4.9-0.1.7.0). További információ: Linux- [illesztőprogramok](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed).
>
> Emellett a legújabb Azure Marketplace HPC-lemezképek a Mellanox OFED 5,1-as és újabb verzióival rendelkeznek, amelyek nem támogatják ConnectX3-Pro kártyákat. A ConnectX3-Pro kártyával rendelkező virtuális gépeken való használat előtt keresse meg a HPC-rendszerkép Mellanox-OFED verzióját.
>
> Az alábbi képek a ConnectX3-Pro kártyákat támogató legújabb CentOS-HPC-rendszerképek:
>
> - OpenLogic: CentOS-HPC: 7.6:7.6.2020062900
> - OpenLogic: CentOS-HPC: 7_6gen2:7.6.2020062901
> - OpenLogic: CentOS-HPC: 7.7:7.7.2020062600
> - OpenLogic: CentOS-HPC: 7_7-Gen2:7.7.2020062601
> - OpenLogic: CentOS-HPC: 8_1:8.1.2020062400
> - OpenLogic: CentOS-HPC: 8_1-Gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-illesztőprogramok telepítése NV-vagy NVv3-sorozatú virtuális gépeken

Az NVIDIA GRID-illesztőprogramok NV-vagy NVv3-sorozatú virtuális gépekre való telepítéséhez létesítsen SSH-kapcsolatokat minden virtuális géphez, és kövesse a Linux-disztribúció lépéseit. 

### <a name="ubuntu"></a>Ubuntu 

1. Futtassa a következő parancsot: `lspci`. Győződjön meg arról, hogy az NVIDIA M60 kártya vagy kártya PCI-eszközként jelenik meg.

2. Telepítse a frissítéseket.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Tiltsa le a Nouveau kernel illesztőprogramját, amely nem kompatibilis az NVIDIA-illesztőprogrammal. (Csak az NVIDIA-illesztőprogramot használja az NV-vagy NVv2-alapú virtuális gépeken.) Ehhez hozzon létre egy `/etc/modprobe.d` nevű fájlt `nouveau.conf` a következő tartalommal:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Indítsa újra a virtuális gépet, és kapcsolódjon újra. Kilépési X kiszolgáló:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Töltse le és telepítse a GRID-illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Ha a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen** lehetőséget.

7. A telepítés befejezése után másolja a/etc/NVIDIA/GRIDD.conf.Template egy új fájlba. conf fájlt a következő helyen:/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adja hozzá a következőket a következőhöz `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Ha megtalálható, távolítsa el a következőket `/etc/nvidia/gridd.conf` :
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux 

1. Frissítse a kernelt és a DKMS (ajánlott). Ha úgy dönt, hogy nem frissíti a kernelt, győződjön meg arról, hogy a `kernel-devel` és a `dkms` rendszermagja megfelelő-e.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Tiltsa le a Nouveau kernel illesztőprogramját, amely nem kompatibilis az NVIDIA-illesztőprogrammal. (Csak az NVIDIA-illesztőprogramot használja az NV-vagy NV3-alapú virtuális gépeken.) Ehhez hozzon létre egy `/etc/modprobe.d` nevű fájlt `nouveau.conf` a következő tartalommal:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Indítsa újra a virtuális gépet, kapcsolódjon újra, és telepítse a [Hyper-V és az Azure legújabb linuxos integrációs szolgáltatásait](https://www.microsoft.com/download/details.aspx?id=55106). Ellenőrizze, hogy szükséges-e a LIS a lspci eredményeinek ellenőrzéséhez. Ha az összes GPU-eszköz a várt módon szerepel (és dokumentálva van), a LIS telepítése nem szükséges. 

   Ugorja át ezt a lépést, ha a CentOS/RHEL 7,8 (vagy újabb verzió) használatát tervezi, mert az nem szükséges a következő verziókhoz: LIS.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Kapcsolódjon újra a virtuális géphez, és futtassa a `lspci` parancsot. Győződjön meg arról, hogy az NVIDIA M60 kártya vagy kártya PCI-eszközként jelenik meg.
 
5. Töltse le és telepítse a GRID-illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Ha a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen** lehetőséget.

7. A telepítés befejezése után másolja a/etc/NVIDIA/GRIDD.conf.Template egy új fájlba. conf fájlt a következő helyen:/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adja hozzá a következőket a következőhöz `/etc/nvidia/gridd.conf` :
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Ha megtalálható, távolítsa el a következőket `/etc/nvidia/gridd.conf` :
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.


### <a name="verify-driver-installation"></a>Illesztőprogram telepítésének ellenőrzése


Ha le szeretné kérdezni a GPU-eszköz állapotát, SSH-t a virtuális gépre, és futtassa az illesztőprogrammal telepített [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet jelenik meg. Vegye figyelembe, hogy a **GPU-util** 0%-ot mutat, ha jelenleg nem fut GPU-s munkaterhelés a virtuális gépen. Az illesztőprogram verziószáma és a GPU adatai eltérhetnek a megjelenített adatoktól.

![Képernyőkép, amely a GPU-eszköz állapotának lekérdezése után megjeleníti a kimenetet.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-kiszolgáló
Ha egy NV-vagy NVv2-alapú virtuális géphez távoli kapcsolatokhoz X11-kiszolgálóra van szüksége, akkor a [x11vnc](http://www.karlrunge.com/x11vnc/) használata javasolt, mivel lehetővé teszi a grafikus processzorok hardveres gyorsítását. A M60 eszközt manuálisan kell hozzáadni az X11 konfigurációs fájljához (általában: `etc/X11/xorg.conf` ). Vegyen fel egy `"Device"` , az alábbihoz hasonló szakaszt:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Emellett frissítse a `"Screen"` szakaszt az eszköz használatához.
 
A megtalált tizedes tört a futtatásával

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
A teendő megváltozhat, ha egy virtuális gép újra le lesz foglalva vagy újraindul. Ezért előfordulhat, hogy létre kell hoznia egy parancsfájlt az X11 konfigurációjának frissítéséhez, amikor egy virtuális gép újraindul. Hozzon létre például egy nevű szkriptet `busidupdate.sh` (vagy egy másik nevet) a következőhöz hasonló tartalommal:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Ezután hozzon létre egy bejegyzést a frissítési parancsfájlhoz, `/etc/rc.d/rc3.d` hogy a parancsfájl gyökérként legyen meghívva a rendszerindításhoz.

## <a name="troubleshooting"></a>Hibaelhárítás

* Az adatmegőrzési módot úgy állíthatja be, hogy a `nvidia-smi` parancs kimenete gyorsabb legyen, ha kártyákat kell lekérdezni. Az adatmegőrzési mód beállításához futtassa a következőt: `nvidia-smi -pm 1` . Vegye figyelembe, hogy ha a virtuális gép újraindul, a Mode (mód) beállítás eltűnik. A mód beállítását bármikor végrehajthatja indításkor.
* Ha a legújabb verzióra frissítette az NVIDIA CUDA-illesztőprogramokat, és megkeresi az RDMA-kapcsolatot, akkor [a kapcsolat újbóli létrehozásához telepítse újra a RDMA-illesztőprogramokat](#rdma-network-connectivity) . 
* A LIS telepítésekor, ha egy bizonyos CentOS/RHEL operációsrendszer-verzió (vagy kernel) nem támogatott a LIS esetében, a rendszer a "nem támogatott kernel-verzió" hibát dobta. Jelentse ezt a hibát az operációs rendszer és a kernel verzióival együtt.

## <a name="next-steps"></a>Következő lépések

* Linux rendszerű virtuálisgép-lemezképnek a telepített NVIDIA-illesztőprogramokkal való rögzítéséről lásd: [Linux rendszerű virtuális gép általánosítása és rögzítése](capture-image.md).
