---
title: Azure N sorozatú GPU-illesztő beállítása Linuxhoz
description: NVIDIA GPU-illesztőprogramok beállítása Linuxot futtató N-sorozatú virtuális gépekhez az Azure-ban
services: virtual-machines
author: vikancha-MSFT
ms.service: virtual-machines
ms.subervice: vm-sizes-gpu
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2019
ms.author: vikancha
ms.openlocfilehash: dd9461e30138ee1a59a93db45aa5f739bfe88f94
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565304"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>NVIDIA GPU-illesztőprogramok telepítése Linuxot futtató N sorozatú virtuális gépeken

Az NVIDIA GPU-k által háttérbeli Azure N-sorozatú virtuális gépek GPU-képességeinek kihasználása érdekében NVIDIA GPU-illesztőprogramokat kell telepítenie. Az [NVIDIA GPU-illesztőbővítmény](../extensions/hpccompute-gpu-linux.md) telepíti a megfelelő NVIDIA CUDA- vagy GRID-illesztőprogramokat egy N sorozatú virtuális gépre. Telepítse vagy kezelje a bővítményt a Azure Portal vagy eszközökkel, például az Azure CLI-vel vagy Azure Resource Manager sablonokkal. A támogatott [disztribúciókért](../extensions/hpccompute-gpu-linux.md) és üzembe helyezési lépésekért tekintse meg az NVIDIA GPU-illesztőbővítmény dokumentációját.

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, ez a cikk a támogatott disztribúciókat, illesztőprogramokat, valamint telepítési és ellenőrzési lépéseket tartalmaz. A Manuális illesztőprogram-telepítési információk Windows rendszerű virtuális [gépekhez is elérhetők.](../windows/n-series-driver-setup.md)

Az N sorozatú virtuális gépek specifikációiért, a tárolási kapacitásokért és a lemez részleteiért lásd: [GPU Linux virtuálisgép-méretek.](../sizes-gpu.md?toc=/azure/virtual-machines/linux/toc.json) 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>CUDA-illesztőprogramok telepítése N sorozatú virtuális gépeken

Az alábbi lépésekkel telepíthet CUDA-illesztőprogramokat az NVIDIA CUDA-eszközkészletből N sorozatú virtuális gépekre. 

A C- és C++-fejlesztők igény szerint telepítheti a teljes eszközkészletet a GPU-gyorsítással rendelkező alkalmazások felépítéséhez. További információkért lásd a [CUDA telepítési útmutatóját.](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)

A CUDA-illesztőprogramok telepítéséhez létesítsen SSH-kapcsolatot minden virtuális géphez. Annak ellenőrzéséhez, hogy a rendszer rendelkezik-e CUDA-kompatibilis GPU-val, futtassa a következő parancsot:

```bash
lspci | grep -i NVIDIA
```
Az alábbi példához hasonló kimenetet fog látni (nvidia Tesla K80 kártyát mutat):

![lspci-parancs kimenete](./media/n-series-driver-setup/lspci.png)

Az lspci felsorolja a virtuális gépen található PCIe-eszközöket, beleértve az InfiniBand hálózati adaptert és GPU-kat, ha van ilyen. Ha az lspci nem tér vissza sikeresen, előfordulhat, hogy telepítenie kell a LIS-t CentOS/RHEL-re (az alábbi utasításokat követve).
Ezután futtassa a disztribúcióra vonatkozó telepítési parancsokat.

### <a name="ubuntu"></a>Ubuntu 

1. Töltse le és telepítse a CUDA-illesztőprogramokat az NVIDIA webhelyéről. 
    > [!NOTE]
   >  Az alábbi példában az Ubuntu 16.04 CUDA-csomag elérési útja látható. Cserélje le a használni tervben megadott verzió elérési útját. 
   >  
   >  Az egyes verziókra vonatkozó teljes elérési útért látogasson el az [Nvidia https://developer.download.nvidia.com/compute/cuda/repos/) letöltőközpontba] ( 
   > 
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb
   wget -O /tmp/${CUDA_REPO_PKG} https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}
   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update
   sudo apt-get install cuda-drivers
   ```

   A telepítés több percig is eltarthat.
 

2. A teljes CUDA-eszközkészlet opcionális telepítéséhez írja be a következőt:

   ```bash
   sudo apt-get install cuda
   ```

3. Indítsa újra a virtuális gépet, és folytassa a telepítés ellenőrzésével.

#### <a name="cuda-driver-updates"></a>CUDA-illesztőprogramok frissítései

Javasoljuk, hogy az üzembe helyezés után rendszeresen frissítse a CUDA-illesztőprogramokat.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get dist-upgrade -y
sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux

1. Frissítse a kernelt (ajánlott). Ha úgy dönt, hogy nem frissíti a kernelt, győződjön meg arról, hogy a és a verziói `kernel-devel` `dkms` megfelelőek a kernelhez.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
   sudo reboot
   ```

2. Telepítse a [Legújabb Linux integrációs szolgáltatásokat a Hyper-V-hez és az Azure-hoz.](https://www.microsoft.com/download/details.aspx?id=55106) Ellenőrizze az lspci eredményeinek ellenőrzéséhez, hogy szükség van-e a LIS-re. Ha az összes GPU-eszköz a várt módon jelenik meg (és fent van dokumentálva), a LIS telepítése nem szükséges.

   Vegye figyelembe, hogy a LIS az Red Hat Enterprise Linux, a CentOS és a Oracle Linux Red Hat-kompatibilis kernel 5.2-5.11, 6.0-6.10 és 7.0-7.7 esetén alkalmazható. További részletekért tekintse meg a [Linux Integration Services dokumentációját] https://www.microsoft.com/en-us/download/details.aspx?id=55106) ( 
   Hagyja ki ezt a lépést, ha a CentOS/RHEL 7.8-as (vagy újabb) verzióját tervezi használni, mivel ezekhez a verziókhoz már nincs szükség LIS-re.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot
      ```

3. Csatlakoztassa újra a virtuális gépet, és folytassa a telepítést a következő parancsokkal:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A telepítés több percig is eltarthat. 
   
    > [!NOTE]
   >  Keresse [fel a Fedora](https://dl.fedoraproject.org/pub/epel/) és [az Nvidia CUDA-t,](https://developer.download.nvidia.com/compute/cuda/repos/) és válassza ki a használni kívánt CentOS- vagy RHEL-verzióhoz megfelelő csomagot.
   >  

A CentOS 8-nak és az RHEL 8-nak például a következő lépésekre lesz szüksége.

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
   sudo yum install dkms
   
   CUDA_REPO_PKG=cuda-repo-rhel8-10.2.89-1.x86_64.rpm
   wget https://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}
   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

4. A teljes CUDA-eszközkészlet opcionális telepítéséhez írja be a következőt:

   ```bash
   sudo yum install cuda
   ```
   > [!NOTE]
   >  Ha hiányzó csomagokkal, például a vulkan-filesystemtel kapcsolatos hibaüzenet jelenik meg, akkor előfordulhat, hogy szerkesztenie kell az /etc/yum.repos.d/ft-cloud fájlt, és meg kell keresnie az optional-rpms paramétert, és az enabled (engedélyezve) beállítást 1-re kell állítania
   >  

5. Indítsa újra a virtuális gépet, és folytassa a telepítés ellenőrzésével.

### <a name="verify-driver-installation"></a>Az illesztő telepítésének ellenőrzése

A GPU-eszköz állapotának lekérdezéséhez SSH-val csatlakozik a virtuális géphez, és futtassa az illesztővel együtt telepített [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet fog látni. Vegye figyelembe, hogy a **GPU-Util** 0%-ot mutat, kivéve, ha jelenleg GPU számítási feladatot futtat a virtuális gépen. Előfordulhat, hogy az illesztő verziója és a GPU adatai eltérnek a bemutatottaktól.

![NVIDIA-eszköz állapota](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

Az RDMA hálózati kapcsolat engedélyezhető olyan RDMA-kompatibilis N-sorozatú virtuális gépeken, mint például az NC24r, amely ugyanabban a rendelkezésre állási csoportban vagy egy virtuálisgép-méretezési csoport egyetlen elhelyezési csoportjában van üzembe állítva. Az RDMA-hálózat Message Passing Interface (MPI) forgalmat támogat az Intel MPI 5.x vagy újabb verzióját futtató alkalmazások esetében. További követelmények a következők:

### <a name="distributions"></a>Disztribúciók

Telepítsen RDMA-kompatibilis N sorozatú virtuális gépeket a Azure Marketplace egyik rendszerképből, amely támogatja az RDMA-kapcsolatot az N-sorozatú virtuális gépeken:
  
* **Ubuntu 16.04 LTS** – RDMA-illesztőprogramok konfigurálása a virtuális gépen, és regisztrálás az Intel-ben az Intel MPI letöltéséhez:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **A CentOS-alapú 7.4 HPC** – RDMA-illesztőprogramok és az Intel MPI 5.1 telepítve vannak a virtuális gépen.

* **CentOS-alapú HPC** – CentOS-HPC 7.6 és újabb verziók (az SR-IOV-val támogatott SFINIBandet támogató termékkódok esetén). Ezek a lemezképek előre telepítve vannak a Mellanox OFED- és MPI-kódtárakkal.

> [!NOTE]
> CX3-Pro kártyák csak a Mellanox OFED LTS-verzióin keresztül támogatottak. Használja az LTS Mellanox OFED verziót (4.9-0.1.7.0) az N sorozatú virtuális gépeken ConnectX3-Pro kártyákkal. További információ: [Linux-illesztőprogramok.](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)
>
> Emellett a HPC-Azure Marketplace a Mellanox OFED 5.1-es vagy újabb verzióját, amelyek nem támogatják a ConnectX3-Pro kártyákat. Ellenőrizze a Mellanox OFED verzióját a HPC-rendszerképben, mielőtt virtuális gépeken használ ConnectX3-Pro kártyákkal.
>
> A következő képek a legújabb CentOS-HPC-lemezképek, amelyek támogatják a ConnectX3-Pro kártyákat:
>
> - OpenLogic:CentOS-HPC:7.6:7.6.2020062900
> - OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
> - OpenLogic:CentOS-HPC:7.7:7.7.2020062600
> - OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
> - OpenLogic:CentOS-HPC:8_1:8.1.2020062400
> - OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401
>

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>GRID-illesztőprogramok telepítése NV vagy NVv3 sorozatú virtuális gépekre

Az NVIDIA GRID-illesztőprogramok NV vagy NVv3 sorozatú virtuális gépekre való telepítéséhez létesítsen SSH-kapcsolatot mindegyik virtuális géphez, és kövesse a Linux-disztribúció lépéseit. 

### <a name="ubuntu"></a>Ubuntu 

1. Futtassa a következő parancsot: `lspci`. Ellenőrizze, hogy az NVIDIA M60-kártya vagy -kártyák PCI-eszközként láthatók-e.

2. Frissítések telepítése.

   ```bash
   sudo apt-get update
   sudo apt-get upgrade -y
   sudo apt-get dist-upgrade -y
   sudo apt-get install build-essential ubuntu-desktop -y
   sudo apt-get install linux-azure -y
   ```
3. Tiltsa le a Nouv kernelillesztőt, amely nem kompatibilis az NVIDIA-illesztővel. (Csak NV vagy NVv2 virtuális gépeken használja az NVIDIA-illesztőt.) Ehhez hozzon létre egy nevű fájlt a `/etc/modprobe.d` `nouveau.conf` fájlban a következő tartalommal:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```


4. Indítsa újra a virtuális gépet, és csatlakoztassa újra. Kilépés az X kiszolgálóból:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Töltse le és telepítse a GRID-illesztőt:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run
   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Amikor a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen lehetőséget.**

7. A telepítés befejezése után másolja az /etc/nvidia/gridd.conf.template fájlt egy új gridd.conf fájlba az /etc/nvidia/ helyen

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adja hozzá a következőket a következő `/etc/nvidia/gridd.conf` hez:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Ha jelen van, távolítsa el `/etc/nvidia/gridd.conf` a következőket a-ból:
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS vagy Red Hat Enterprise Linux 

1. Frissítse a kernelt és a DKMS-t (ajánlott). Ha úgy dönt, hogy nem frissíti a kernelt, győződjön meg arról, hogy a és a verziói `kernel-devel` `dkms` megfelelőek a kernelhez.
 
   ```bash  
   sudo yum update
   sudo yum install kernel-devel
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo yum install dkms
   sudo yum install hyperv-daemons
   ```

2. Tiltsa le a Nouv kernelillesztőt, amely nem kompatibilis az NVIDIA-illesztővel. (Csak NV vagy NV3 virtuális gépeken használja az NVIDIA-illesztőt.) Ehhez hozzon létre egy nevű fájlt a `/etc/modprobe.d` `nouveau.conf` fájlban a következő tartalommal:

   ```
   blacklist nouveau
   blacklist lbm-nouveau
   ```

3. Indítsa újra a virtuális gépet, csatlakoztassa újra, és telepítse a Hyper-V-hez és az Azure-hoz elérhető legújabb [Linux integrációs szolgáltatásokat.](https://www.microsoft.com/download/details.aspx?id=55106) Ellenőrizze az lspci eredményeinek ellenőrzéséhez, hogy szükség van-e a LIS-re. Ha minden GPU-eszköz a várt módon jelenik meg (és a fentiekben dokumentált), a LIS telepítése nem szükséges. 

   Hagyja ki ezt a lépést, ha a CentOS/RHEL 7.8-as (vagy újabb) verzióját tervezi használni, mivel ezekhez a verziókhoz már nincs szükség LIS-re.

      ```bash
      wget https://aka.ms/lis
      tar xvzf lis
      cd LISISO

      sudo ./install.sh
      sudo reboot

      ```
 
4. Csatlakoztassa újra a virtuális gépet, és futtassa a `lspci` parancsot. Ellenőrizze, hogy az NVIDIA M60-kártya vagy -kártyák PCI-eszközként láthatók-e.
 
5. Töltse le és telepítse a GRID-illesztőt:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  
   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Amikor a rendszer megkérdezi, hogy szeretné-e futtatni az nvidia-xconfig segédprogramot az X konfigurációs fájl frissítéséhez, válassza az **Igen lehetőséget.**

7. A telepítés befejezése után másolja az /etc/nvidia/gridd.conf.template fájlt egy új gridd.conf fájlba az /etc/nvidia/ helyen
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adja hozzá a következőket a következő `/etc/nvidia/gridd.conf` hez:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Ha jelen van, távolítsa el `/etc/nvidia/gridd.conf` a következőket a-ból:
 
   ```
   FeatureType=0
   ```
10. Indítsa újra a virtuális gépet, és ellenőrizze a telepítést.


### <a name="verify-driver-installation"></a>Az illesztő telepítésének ellenőrzése


A GPU-eszköz állapotának lekérdezéséhez SSH-val a virtuális géphez, és futtassa az illesztővel együtt telepített [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogramot. 

Ha az illesztőprogram telepítve van, az alábbihoz hasonló kimenet fog látni. Vegye figyelembe, hogy a **GPU-Util** 0%-ot mutat, kivéve, ha jelenleg GPU számítási feladatot futtat a virtuális gépen. Az illesztő verziója és a GPU részletei eltérőek lehetnek a bemutatottaktól.

![Képernyőkép a GPU-eszköz állapotának lekérdezésekor látható kimenetről.](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-kiszolgáló
Ha egy NV vagy NVv2 virtuális gép távoli kapcsolataihoz X11-kiszolgálóra van szüksége, [az x11vnc](http://www.karlrunge.com/x11vnc/) használata ajánlott, mert lehetővé teszi a grafikus elemek hardvergyorsítását. Az M60-eszköz BusID-ját manuálisan kell hozzáadni az X11 konfigurációs fájlhoz `etc/X11/xorg.conf` (általában). Adjon hozzá egy, az `"Device"` alábbihoz hasonló szakaszt:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Emellett frissítse a `"Screen"` szakaszt az eszköz használatára.
 
A decimális buszazonosító a következő futtatásával található meg:

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
A BusID megváltoztatható, ha egy virtuális gép újra lesz locálva vagy újraindul. Ezért előfordulhat, hogy létre szeretne hozni egy szkriptet, amely frissíti a BusID-t az X11 konfigurációban a virtuális gép újraindításakor. Hozzon létre például egy nevű szkriptet (vagy egy másik választott `busidupdate.sh` nevet) a következőhöz hasonló tartalommal:

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

Ezután hozzon létre egy bejegyzést a frissítési szkripthez a -ban, hogy a rendszer indításkor gyökérként hívja meg `/etc/rc.d/rc3.d` a szkriptet.

## <a name="troubleshooting"></a>Hibaelhárítás

* Az adatmegőrzési módot a paranccsal állíthatja be, így a parancs kimenete gyorsabb `nvidia-smi` lesz, ha kártyákat kell lekérdezni. Az adatmegőrzési mód beállítását a parancs végrehajtásával állíthatja `nvidia-smi -pm 1` be. Vegye figyelembe, hogy ha a virtuális gép újraindul, a módbeállítás el fog menni. Az indításkor futtatott módbeállítást mindig megszkentálhatja.
* Ha frissítette az NVIDIA CUDA-illesztőprogramokat a legújabb verzióra, és úgy találja, hogy az RDMA-kapcsolat már nem működik, telepítse újra az [RDMA-illesztőprogramokat](#rdma-network-connectivity) a kapcsolat újratelepítéséhez. 
* Ha a LIS telepítése során a CentOS/RHEL operációs rendszer egy bizonyos verziója (vagy kernele) nem támogatott a LIS esetében, a rendszer "Nem támogatott kernelverzió" hibaüzenetet ad vissza. Jelentse ezt a hibát az operációs rendszer és a kernelverziók mellett.

## <a name="next-steps"></a>Következő lépések

* Linux rendszerű virtuális gép rendszerképének a telepített NVIDIA-illesztőprogramokkal való rögzítéséhez [lásd: Linux rendszerű](capture-image.md)virtuális gép általánosizálása és rögzítése.
