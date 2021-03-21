---
title: DPDK egy Azure-beli linuxos virtuális gépen | Microsoft Docs
description: Ismerje meg az adatközpont-fejlesztési csomag (DPDK) előnyeit, valamint azt, hogyan állíthatja be a DPDK Linux rendszerű virtuális gépen.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: labattul
ms.openlocfilehash: 3b4d66525ec52ef2382dfbe97bc09278e35b31fb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124669"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>DPDK beállítása Linux rendszerű virtuális gépen

Az Azure-beli adatközpont-fejlesztési csomag (DPDK) gyorsabb, felhasználói adatátviteli keretrendszert biztosít a nagy teljesítményű alkalmazások számára. Ez a keretrendszer megkerüli a virtuális gép kernel-hálózati veremét.

A kernel hálózati veremét használó tipikus csomagméret esetén a folyamat megszakításon alapul. Ha a hálózati adapter fogad bejövő csomagokat, a rendszer egy kernel-megszakítással dolgozza fel a csomagot és egy környezeti kapcsolót a rendszermag területéről a felhasználói helyre. A DPDK kiküszöböli a környezetek közötti váltást és a megszakítás-vezérelt módszert egy olyan felhasználói felületi implementáció mellett, amely lekérdezési módú illesztőprogramokat használ a gyors csomagok feldolgozásához.

A DPDK olyan felhasználói tárhelyek készleteit tartalmazza, amelyek hozzáférést biztosítanak az alacsonyabb szintű erőforrásokhoz. Ezek az erőforrások lehetnek a hardverek, a logikai magok, a memória-kezelés és a lekérdezési mód illesztőprogramjai a hálózati adapterekhez.

A DPDK olyan Azure-beli virtuális gépeken is futtathatók, amelyek több operációs rendszer terjesztését támogatják. A DPDK kulcsfontosságú teljesítménybeli különbséget biztosít a hálózati függvények virtualizációs implementációinak vezetésében. Ezek a megvalósítások hálózati virtuális berendezések (NVA) formáját képezik, például virtuális útválasztók, tűzfalak, VPN-EK, terheléselosztó, Evolved csomagok magjai és szolgáltatásmegtagadási (DDoS) alkalmazások formájában.

## <a name="benefit"></a>Előny

**Nagyobb csomagok másodpercenként (PPS)**: a kernel kihagyása és a felhasználói terület csomagjainak szabályozása csökkenti a ciklusok számát a környezeti kapcsolók kiiktatásával. Emellett javítja az Azure-beli linuxos virtuális gépeken másodpercenként feldolgozott csomagok sebességét is.


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az Azure Marketplace-ről a következő disztribúciók támogatottak:

| Linux operációs rendszer     | Kernel verziója               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014 – Azure +           | 
| Ubuntu 18.04 | 4.15.0-1014 – Azure +           |
| SLES 15 SP1  | 4.12.14-8.19 – Azure +          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64 +  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64 +  | 

**Egyéni kernel-támogatás**

A nem felsorolt Linux kernel-verziók esetében lásd: az [Azure-ban hangolt Linux-kernel létrehozásához szükséges javítások](https://github.com/microsoft/azure-linux-kernel). További információért vegye fel a kapcsolatot a következővel: [aznetdpdk@microsoft.com](mailto:aznetdpdk@microsoft.com) . 

## <a name="region-support"></a>Régiós támogatás

Az összes Azure-régió támogatja a DPDK-t.

## <a name="prerequisites"></a>Előfeltételek

A gyorsított hálózatkezelést engedélyezni kell egy linuxos virtuális gépen. A virtuális gépnek legalább két hálózati adapterrel kell rendelkeznie, egyetlen kezelőfelülettel a felügyelethez. A gyorsított hálózatkezelés engedélyezése a felügyeleti felületen nem ajánlott. Megtudhatja, hogyan [hozhat létre Linux rendszerű virtuális gépet, ha engedélyezve van a gyorsított hálózatkezelés](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>DPDK-függőségek telepítése

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL 7.5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Alapértelmezett kernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>A virtuális gép környezetének beállítása (egyszer)

1. [Töltse le a legújabb DPDK](https://core.dpdk.org/download). Az Azure-hoz a 18,11 LTS vagy az 19,11 LTS verzió szükséges.
2. Hozza létre az alapértelmezett konfigurációt a paranccsal `make config T=x86_64-native-linuxapp-gcc` .
3. Engedélyezze a Mellanox PMDs a generált konfigurációban a paranccsal `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config` .
4. Fordítás a-val `make` .
5. Telepítés a-val `make install DESTDIR=<output folder>` .

## <a name="configure-the-runtime-environment"></a>A futásidejű környezet konfigurálása

Az újraindítás után futtassa a következő parancsokat egyszer:

1. Hugepages

   * Konfigurálja a hugepage a következő parancs futtatásával minden NUMA-csomópont esetében:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Hozzon létre egy könyvtárat a csatlakoztatáshoz `mkdir /mnt/huge` .
   * Hugepages csatlakoztatása a rel `mount -t hugetlbfs nodev /mnt/huge` .
   * Győződjön meg arról, hogy a hugepages vannak fenntartva `grep Huge /proc/meminfo` .

     > Megjegyzés A grub-fájl úgy módosítható, hogy a hugepages a DPDK [vonatkozó utasítások](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) követésével legyenek lefoglalva a rendszerindításhoz. Az utasítások az oldal alján találhatók. Ha Azure Linux rendszerű virtuális gépet használ, módosítsa a fájlokat a **/etc/config/grub.d** területen az újraindítások közötti hugepages foglalásához.

2. MAC & IP-címek: a használatával `ifconfig –a` megtekintheti a hálózati adapterek Mac és IP-címét. A *VF* hálózati adapter és a *NETVSC* hálózati adapter ugyanazzal a Mac-címmel rendelkezik, de csak az *NETVSC* hálózati adapter IP-címmel rendelkezik. A *VF* felületek a *NETVSC* felületek alárendelt felületeinek formájában futnak.

3. PCI-címek

   * `ethtool -i <vf interface name>`A használatával megtudhatja, melyik PCI-címeket kell használni a *VF*-hez.
   * Ha a *ETH0* gyorsított hálózatkezelést engedélyez, győződjön meg arról, hogy a testpmd nem veszi át véletlenül a *VF* PCI-eszközt a *ETH0*-hez. Ha a DPDK alkalmazás véletlenül átveszi a felügyeleti hálózati adaptert, és elveszíti az SSH-kapcsolatot, a soros konzollal állíthatja le a DPDK alkalmazást. A virtuális gép leállításához vagy elindításához a soros konzolt is használhatja.

4. Töltse be a *ibuverbs* minden újraindításkor `modprobe -a ib_uverbs` . A SLES csak 15 esetében is betölti a *mlx4_ibt* a következővel: `modprobe -a mlx4_ib` .

## <a name="failsafe-pmd"></a>Failsafe PMD

A DPDK-alkalmazásoknak az Azure-ban elérhető Failsafe-PMD kell futniuk. Ha az alkalmazás közvetlenül a *VF* PMD fut, akkor nem kapja meg a virtuális géphez tartozó **összes** csomagot, mivel egyes csomagok a szintetikus felületen jelennek meg. 

Ha DPDK-alkalmazást futtat a Failsafe-PMD keresztül, az garantálja, hogy az alkalmazás megkapja az összes olyan csomagot, amely erre a célra szolgál. Azt is ellenőrzi, hogy az alkalmazás DPDK módban fut-e, még akkor is, ha a VF-t visszavonják a gazdagép kiszolgálása során. A Failsafe-PMD kapcsolatos további információkért lásd: a [nem biztonságos lekérdezési mód illesztőprogram-könyvtára](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Testpmd futtatása

A testpmd gyökérszintű módban való futtatásához használja `sudo` a *testpmd* parancs előtt.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Alapszintű: józan ész-ellenőrzési, Failsafe-adapter inicializálása

1. Futtassa az alábbi parancsokat egy portos testpmd-alkalmazás elindításához:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Futtassa a következő parancsokat egy kettős portos testpmd-alkalmazás indításához:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Ha a testpmd-t több mint két hálózati adapterrel futtatja, az `--vdev` argumentum a következő mintát követi: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` .

3.  Az indítás után futtassa a parancsot `show port info all` a port adatainak vizsgálatához. Egy vagy két DPDK-portot kell látnia net_failsafe (nem *net_mlx4*).
4.  `start <port> /stop <port>`A forgalom elindítására használható.

Az előző parancsok interaktív módban kezdenek *testpmd* , ami a testpmd parancsok kipróbálásához ajánlott.

### <a name="basic-single-sendersingle-receiver"></a>Alapszintű: egyetlen feladó/egy fogadó

A következő parancsok időnként kinyomtatják a csomagokat másodpercenkénti statisztikában:

1. A TX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Az RX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

Ha az előző parancsokat egy virtuális gépen futtatja, a fordítás előtt módosítsa *IP_SRC_ADDR* és *IP_DST_ADDR* , `app/test-pmd/txonly.c` hogy az megfeleljen a virtuális gépek tényleges IP-címének. Ellenkező esetben a rendszer eldobja a csomagokat, mielőtt elérné a fogadót.

### <a name="advanced-single-sendersingle-forwarder"></a>Speciális: egyetlen feladó/egyetlen továbbító
A következő parancsok időnként kinyomtatják a csomagokat másodpercenkénti statisztikában:

1. A TX oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. A FWD oldalon futtassa a következő parancsot:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

Ha az előző parancsokat egy virtuális gépen futtatja, a fordítás előtt módosítsa *IP_SRC_ADDR* és *IP_DST_ADDR* , `app/test-pmd/txonly.c` hogy az megfeleljen a virtuális gépek tényleges IP-címének. Ellenkező esetben a rendszer eldobja a csomagokat, mielőtt elérné a továbbítót. Nem lesz lehetősége arra, hogy egy harmadik gép kapjon továbbított forgalmat, mert a *testpmd* -továbbító nem módosítja a 3. rétegbeli címeket, hacsak nem módosít valamilyen programkódot.

## <a name="references"></a>Hivatkozások

* [EAL-beállítások](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd parancsok](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
