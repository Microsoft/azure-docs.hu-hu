---
title: Egy Azure Linux VM DPDK |} A Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a Linux rendszerű virtuális gép DPDK.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c5cb840035c5d0d5694982324c7237c58001e689
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60731600"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>A Linux rendszerű virtuális gép DPDK beállítása

Adatok Adatsík Development Kit (DPDK) az Azure-ban egy gyorsabb felhasználói térben csomag feldolgozást végző keretrendszer, teljesítmény-igényes alkalmazásokhoz kínál. Ez a keretrendszer megkerüli a virtuális gép kernel hálózati vermet.

A tipikus csomagfeldolgozáshoz, amely a kernel hálózati vermet használja, a folyamat nem megszakítási adatvezérelt. A hálózati adapter bejövő csomagok kap, ha nincs a kernel megszakítási folyamat a csomag és a egy helyi váltson a kernel területről a felhasználói felület. DPDK kiküszöböli a környezet közötti váltás és a megszakítási adatvezérelt metódust, hogy használja lekérdezik a gyors csomagfeldolgozáshoz illesztőprogram és a felhasználói térben megvalósítás értéke.

Csoportok, amelyek alacsonyabb szintű erőforrásokhoz való hozzáférést biztosítanak a felhasználói térben könyvtárak DPDK áll. Ezeket az erőforrásokat is tartalmazhat, hardver, logikai magok, memória-kezelési és hálózati kártyák a lekérdezési mód illesztőprogramjait.

Az Azure virtuális gépek, amelyek támogatják a több operációs rendszer disztribúciók DPDK futtathatja. DPDK fő differenciálás a hálózati függvény virtualizálási megvalósításokhoz vezetői biztosít. Rendszer ezeket is igénybe vehet az űrlap hálózati virtuális készülékek (nva-k), például virtuális útválasztók, tűzfalak, VPN-eket, terheléselosztók, fejlődött csomag magok és szolgáltatásmegtagadásos (DDoS-) alkalmazások.

## <a name="benefit"></a>Előny

**Magasabb csomagok másodpercenként (PPS)** : A ciklus száma megkerüli a kernel és a felhasználói térben csomagok véve irányítását csökkenti a környezet kapcsolók kiküszöbölése révén. Ez növeli a csomagok Azure-beli Linuxos virtuális gépek a másodpercenként feldolgozott is.


## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

Az Azure katalógusából a következő disztribúciók támogatottak:

| Linux OS     | Kernel verziója        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Egyéni kernel támogatása**

Minden Linux kernel verziója, amely nem szerepel a listában, lásd: [javítások létrehozásához egy beállított Azure Linux-kernelt](https://github.com/microsoft/azure-linux-kernel). További információ is fordulhat [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Régió támogatása

Az összes Azure-régiók támogatják a DPDK.

## <a name="prerequisites"></a>Előfeltételek

Linux rendszerű virtuális gép gyorsított hálózatkezelés engedélyezni kell. A virtuális gépnek rendelkeznie kell legalább két hálózati adapterek, a felügyeleti felülethez. Ismerje meg, hogyan [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelés engedélyezett](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>DPDK függőségek telepítése

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Az Azure kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Az alapértelmezett kernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>A virtuális gép környezetet (egyszer)

1. [Töltse le a legújabb DPDK](https://core.dpdk.org/download). 18\.02 vagy újabb verziójú Azure szükség.
2. Az alapértelmezett konfigurációt hozhat létre `make config T=x86_64-native-linuxapp-gcc`.
3. A generált konfigurációt Mellanox PMDs engedélyezése `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. A fordítási `make`.
5. Telepítés a `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>A futtatókörnyezet konfigurálása

Az újraindítás után futtassa egyszer a következő parancsokat:

1. Hugepages

   * A következő parancsot, és egyszer az összes numanodes hugepage konfigurálása:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Hozzon létre egy könyvtárat a csatlakoztatáshoz szükséges `mkdir /mnt/huge`.
   * A csatlakoztatási hugepages `mount -t hugetlbfs nodev /mnt/huge`.
   * Ellenőrizze, hogy hugepages lefoglalták `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Egy lehetséges, hogy módosítsa a grub-fájlt, hogy a hugepages vannak fenntartva az rendszerindításkor az alábbi a [utasításokat](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) a DPDK számára. Az utasítások vannak a lap alján. Ha egy Azure-beli Linuxos virtuális gép használja, módosíthatja a fájlokat a **/etc/config/grub.d** ehelyett hugepages lefoglalása újraindítások között.

2. MAC és IP-címek: Használat `ifconfig –a` megtekintéséhez a hálózati adapterek MAC- és IP-címét. A *VF* hálózati adapter és *NETVSC* hálózati adapter van az azonos MAC-címet, de csak a *NETVSC* hálózati illesztőnek egy IP-címet. Futtatja a VF felületek, alárendelt felületek NETVSC felületek.

3. PCI-címek

   * Használjon `ethtool -i <vf interface name>` melyik PCI cím használandó *VF*.
   * Ha *eth0* van a gyorsított hálózatkezelés engedélyezve van, győződjön meg arról, hogy testpmd véletlenül nem átveszi a VF pci eszközt *eth0*. Ha a DPDK alkalmazás véletlenül veszi át a felügyeleti hálózati adapter és okozza, hogy az SSH-kapcsolat megszakadhat, a soros konzol használatával állítsa le a DPDK alkalmazást. A soros konzol használatával a virtuális gép indítása vagy leállítása.

4. Betöltés *ibuverbs* minden egyes újraindításkor a `modprobe -a ib_uverbs`. Csak a SLES 15., is terheléselosztást *mlx4_ib* a `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>FailSafe PMD

DPDK alkalmazások kell futtatnia a failsafe PMD, amely az Azure-ban van közzétéve. Ha az alkalmazás közvetlenül a VF PMD keresztül futtat, akkor nem kap **összes** a virtuális géphez, mivel az egyes csomagok mutatja a szintetikus adapter küldött csomagokat. 

A failsafe PMD keresztül futtatja egy DPDK alkalmazást, azt garantálja, hogy az alkalmazás megkapja az összes csomagokat, amelyek végül azt. Azt is gondoskodik arról, hogy az alkalmazás továbbra is működik DPDK módban, még akkor is, amennyiben a VF vissza lett vonva, ha a gazdagép javítás alatt áll. Failsafe PMD kapcsolatos további információkért lásd: [hibamentes lekérdezési mód illesztőprogram könyvtárából](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Testpmd futtatása

Legfelső szintű módban testpmd futtatásához használja `sudo` előtt a *testpmd* parancsot.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Alapszintű: Ellenőrzés megerősítést failsafe adapter inicializálása

1. Futtassa a következő parancsokat egy egyetlen port testpmd alkalmazás elindításához:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Futtassa a következő parancsokat egy kettős port testpmd alkalmazás elindításához:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Ha több mint két hálózati adapterrel, testpmd futtatja a `--vdev` argumentum ezt a mintát követi: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  Miután elindult, futtassa `show port info all` portadatokat ellenőrzéséhez. Megtekintheti az egy vagy két DPDK-portok net_failsafe (nem *net_mlx4*).
4.  Használat `start <port> /stop <port>` forgalom elindításához.

Az előző parancsok start *testpmd* interaktív módban, amely ajánlott testpmd parancsok kipróbálásához.

### <a name="basic-single-sendersingle-receiver"></a>Alapszintű: Egyetlen feladó/egyetlen fogadó

A következő parancsok rendszeres időközönként nyomtatása a csomagok másodpercenkénti statisztika:

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

2. A RX oldalon futtassa a következő parancsot:

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

Az előző parancs egy virtuális gépen futtatja, amikor módosítani *IP_SRC_ADDR* és *IP_DST_ADDR* a `app/test-pmd/txonly.c` a konkrét IP-címet a virtuális gépek megfelelően, mielőtt fordítása. Ellenkező esetben a csomagok megszakadnak a fogadó elérése előtt.

### <a name="advanced-single-sendersingle-forwarder"></a>Speciális: Egyetlen feladó/egyetlen továbbító
A következő parancsok rendszeres időközönként nyomtatása a csomagok másodpercenkénti statisztika:

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

Az előző parancs egy virtuális gépen futtatja, amikor módosítani *IP_SRC_ADDR* és *IP_DST_ADDR* a `app/test-pmd/txonly.c` a konkrét IP-címet a virtuális gépek megfelelően, mielőtt fordítása. Ellenkező esetben a csomagok megszakadnak a továbbító elérése előtt. Nem lehet szeretné, hogy egy harmadik gép továbbított forgalom fogadásához, mivel a *testpmd* továbbító nem módosítja a 3. rétegbeli címeket, kivéve, ha néhány kódot módosít.

## <a name="references"></a>Referencia

* [EAL beállításai](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd parancsok](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
