---
title: Az Azure GPU N-sorozat illesztőinek Linux |} A Microsoft Docs
description: Hogyan állítható be NVIDIA GPU-illesztőprogramokat N-sorozatú virtuális gépek Azure-ban futó Linux
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c80b821d6bd0263473ba0178eea148f7a2d5773
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123953"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Az N sorozatú virtuális gépeken Linux rendszert futtató NVIDIA GPU-illesztők telepítése

Linux rendszerű Azure N-sorozatú virtuális gépek a GPU-funkcióinak kihasználásához, NVIDIA GPU-illesztőprogramokat kell telepíteni. A [NVIDIA GPU illesztőprogramjának bővítmény](../extensions/hpccompute-gpu-linux.md) telepíti a megfelelő NVIDIA CUDA vagy GRID illesztőprogramok-N-sorozatú virtuális gépen. Telepítse, vagy a bővítmény az Azure portal vagy az eszközök, például az Azure CLI vagy az Azure Resource Manager-sablonok használata kezelheti. Tekintse meg a [NVIDIA GPU illesztőprogramjának bővítmény dokumentáció](../extensions/hpccompute-gpu-linux.md) a támogatott disztribúcióiról, valamint a telepítés lépéseit.

Ha manuálisan telepíti a GPU-illesztőprogramokat, a cikk ismerteti a telepítés és ellenőrzés lépésein, támogatott disztribúciók és illesztőprogramok. Telepítési információk manuális illesztőprogram érhető el is [Windows virtuális gépek](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Az N sorozatú virtuális gépek adatait tartalmazza, tárolókapacitást és lemez adatai: [GPU Linux Virtuálisgép-méretek](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>CUDA-illesztőprogramokat N-sorozatú virtuális gépek telepítése

Az alábbiakban a CUDA az NVIDIA CUDA-eszközkészlet az N sorozatú virtuális gépeken való telepítésének lépéseit. 


C és C++ fejlesztők is telepíthet a teljes eszközkészlet GPU-gyorsított alkalmazásokat hozhat létre. További információkért lásd: a [CUDA telepítési útmutató](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

CUDA illesztőprogramok telepíteni, győződjön meg arról, az SSH-kapcsolatot minden egyes virtuális géphez. Győződjön meg arról, hogy a rendszer a CUDA-kompatibilis GPU rendelkezik-e, futtassa a következő parancsot:

```bash
lspci | grep -i NVIDIA
```
(Ahol az NVIDIA Tesla K80-kártya) az alábbi példához hasonló kimenetet fog látni:

![lspci parancs kimenete](./media/n-series-driver-setup/lspci.png)

Ezután a disztribúció adott futtatási telepítési parancsokat.

### <a name="ubuntu"></a>Ubuntu 

1. Töltse le és telepítse a CUDA-illesztőprogramokat a NVIDIA webhelyről. Ha például az Ubuntu 16.04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   A telepítés több percig is eltarthat.

2. Szükség esetén telepíti a teljes CUDA-eszközkészlet, írja be:

   ```bash
   sudo apt-get install cuda
   ```

3. A virtuális gépet, és ellenőrizheti a telepítést.

#### <a name="cuda-driver-updates"></a>CUDA-illesztőprogramok frissítései

Azt javasoljuk, hogy rendszeres időközönként frissíti CUDA illesztőprogramok telepítése után.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS és Red Hat Enterprise Linux

1. Frissítse a kernel (ajánlott). Ha nem szeretné frissíteni a kernel, ellenőrizze, hogy a verziói `kernel-devel` és `dkms` a kernel megfelelő.

   ```
   sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
   sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

   ```bash
   wget https://aka.ms/lis
 
   tar xvzf lis
 
   cd LISISO
 
   sudo ./install.sh
 
   sudo reboot
   ```
 
3. A virtuális Géphez csatlakozni, és folytathatja a telepítést a következő parancsokat:

   ```bash
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

   sudo yum install dkms

   CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

   wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

   sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo yum install cuda-drivers
   ```

   A telepítés több percig is eltarthat. 

4. Szükség esetén telepíti a teljes CUDA-eszközkészlet, írja be:

   ```bash
   sudo yum install cuda
   ```

5. A virtuális gépet, és ellenőrizheti a telepítést.

### <a name="verify-driver-installation"></a>Ellenőrizze az illesztőprogram telepítése

Lekérdezni a GPU Eszközállapot SSH, a virtuális gép, és futtassa a [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogram az illesztőprogram telepítve. 

Ha az illesztőprogram telepítve van, látni fogja a következőhöz hasonló kimenetet. Vegye figyelembe, hogy **GPU-Util** jeleníti meg a 0 %-os, kivéve, ha a virtuális gép jelenleg fut a GPU számítási feladatok. Az illesztőprogram verziója és a GPU-részletek látható eltérő lehet.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA hálózati kapcsolat

RDMA hálózati kapcsolat RDMA-kompatibilis az N sorozatú virtuális gépek például NC24r helyezze üzembe, ugyanazon rendelkezésre állási csoportban vagy egy Virtuálisgép-méretezési csoport egyetlen elhelyezési csoport engedélyezhető. Az RDMA hálózati futtatott az Intel MPI-alkalmazások esetében támogatja a Message Passing Interface (MPI) forgalmat 5.x vagy újabb verziója. Kövesse a további követelmények:

### <a name="distributions"></a>Felosztások

RDMA-kompatibilis az N sorozatú virtuális gépeket a rendszerképeket az Azure Marketplace-en, amely támogatja az RDMA-kapcsolattal az N sorozatú virtuális gépek üzembe helyezése:
  
* **Ubuntu 16.04 LTS** – RDMA-illesztőprogramok a virtuális gép konfigurálása és regisztrálása az Intel MPI letöltéséhez Intel:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **7.4-es HPC centOS-alapú** -RDMA-illesztőprogramok és az Intel MPI 5.1 telepítve a virtuális gépen.

## <a name="install-grid-drivers-on-nv-or-nvv2-series-vms"></a>NV vagy NVv2-sorozat virtuális gépei GRID illesztőprogramok telepítése

Telepítheti az NVIDIA GRID illesztőprogramok NV vagy NVv2-sorozat virtuális gépei, létesítsen SSH-kapcsolatot minden egyes virtuális géphez, és kövesse a lépéseket, a Linux-disztribúció. 

### <a name="ubuntu"></a>Ubuntu 

1. Futtassa a következő parancsot: `lspci`. Győződjön meg arról, hogy az NVIDIA M60 kártya vagy kártyák láthatók, a PCI-eszközök.

2. Telepítse a frissítéseket.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Tiltsa le a Nouveau kernel-illesztőprogram, ami nem kompatibilis az NVIDIA illesztőprogram. (Csak a NVIDIA illesztőprogram használata NV vagy NVv2 virtuális gépeken.) Ehhez hozzon létre egy fájlt a `/etc/modprobe.d` nevű `nouveau.conf` a következő tartalommal:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. A virtuális gépet, és csatlakoztassa újra. Kilépés X-kiszolgáló:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Töltse le és telepítse a rács illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. Amikor megkérdezi, hogy szeretné-e frissíteni a X-konfigurációs fájlt, jelölje be a nvidia-xconfig segédprogram futtatásához **Igen**.

7. A telepítés befejezése után egy új fájl gridd.conf, helye/etc/nvidia/másolja /etc/nvidia/gridd.conf.template

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Adja hozzá a következőt `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   ```
9. A virtuális gépet, és ellenőrizheti a telepítést.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS és Red Hat Enterprise Linux 

1. Frissítse a kernel és DKMS (ajánlott). Ha nem szeretné frissíteni a kernel, ellenőrizze, hogy a verziói `kernel-devel` és `dkms` a kernel megfelelő.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   ```

2. Tiltsa le a Nouveau kernel-illesztőprogram, ami nem kompatibilis az NVIDIA illesztőprogram. (Csak a NVIDIA illesztőprogram használata NV vagy NV2 virtuális gépeken.) Ehhez hozzon létre egy fájlt a `/etc/modprobe.d` nevű `nouveau.conf` a következő tartalommal:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. A virtuális gépet, csatlakoztassa újra, és telepítse a legújabb [a Hyper-V és az Azure Linux Integration Services](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. A virtuális gép, és futtassa újra csatlakozni a `lspci` parancsot. Győződjön meg arról, hogy az NVIDIA M60 kártya vagy kártyák láthatók, a PCI-eszközök.
 
5. Töltse le és telepítse a rács illesztőprogramot:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. Amikor megkérdezi, hogy szeretné-e frissíteni a X-konfigurációs fájlt, jelölje be a nvidia-xconfig segédprogram futtatásához **Igen**.

7. A telepítés befejezése után egy új fájl gridd.conf, helye/etc/nvidia/másolja /etc/nvidia/gridd.conf.template
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Adja hozzá a következőt `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   ```
9. A virtuális gépet, és ellenőrizheti a telepítést.

### <a name="verify-driver-installation"></a>Ellenőrizze az illesztőprogram telepítése


Lekérdezni a GPU Eszközállapot SSH, a virtuális gép, és futtassa a [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) parancssori segédprogram az illesztőprogram telepítve. 

Ha az illesztőprogram telepítve van, látni fogja a következőhöz hasonló kimenetet. Vegye figyelembe, hogy **GPU-Util** jeleníti meg a 0 %-os, kivéve, ha a virtuális gép jelenleg fut a GPU számítási feladatok. Az illesztőprogram verziója és a GPU-részletek látható eltérő lehet.

![NVIDIA eszköz állapota](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 kiszolgáló
Ha egy X11 kell kiszolgáló NV vagy NVv2 virtuális Gépet, a távoli kapcsolatok [x11vnc](http://www.karlrunge.com/x11vnc/) ajánlott, mert lehetővé teszi a képek hardveres gyorsítás. Az eszköz M60 BusID manuálisan kell hozzáadni a X11 konfigurációs fájlt (általában `etc/X11/xorg.conf`). Adjon hozzá egy `"Device"` szakasz a következőhöz hasonló:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Emellett frissítse az `"Screen"` szakaszban az eszköz használatára.
 
A tizedes tört BusID található futtatásával

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
A BusID is módosítani, ha a virtuális gép beolvasása újra kiosztja, vagy újraindítása. Ezért érdemes lehet frissíteni a BusID a X11 a parancsfájl létrehozása, ha egy virtuális gép újraindul. Hozzon létre például egy nevű szkriptet `busidupdate.sh` (vagy egy másik, a választott név) tartalma az alábbihoz hasonló:

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

Ezután hozzon létre egy bejegyzés a frissítési parancsfájlt a `/etc/rc.d/rc3.d` , a szkript meghívása rendszerindító a legfelső szintű.

## <a name="troubleshooting"></a>Hibaelhárítás

* Beállíthatja, hogy adatmegőrzési mód használatával `nvidia-smi` , a parancs végrehajtása gyorsabb, amikor a lekérdezés kártyák kell. Adatmegőrzési módja beállításához hajtsa végre a `nvidia-smi -pm 1`. Vegye figyelembe, hogy ha a virtuális gép újraindul, a üzemmódját ami újbóli próbálkozással megszűnik. A üzemmódját indításkor végrehajtásához parancsfájllal mindig is végrehajthatja.

## <a name="next-steps"></a>További lépések

* Tekintse meg a telepített NVIDIA illesztőprogram egy Linux rendszerű Virtuálisgép-lemezkép rögzítéséhez [generalize és a egy Linuxos virtuális gép rögzítése](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
