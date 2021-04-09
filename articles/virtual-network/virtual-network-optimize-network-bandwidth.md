---
title: VM-hálózat teljesítményének optimalizálása | Microsoft Docs
description: Optimalizálja a hálózat átviteli sebességét Microsoft Azure Windows és Linux rendszerű virtuális gépekhez, beleértve a nagyobb disztribúciókat, mint például az Ubuntu, a CentOS és a Red Hat.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/06/2020
ms.author: steveesp
ms.openlocfilehash: bb9235f4d1190bf7f71ddc007f09c9666c353234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98216801"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Az Azure-beli virtuális gépek hálózati teljesítményének optimalizálása

Az Azure Virtual Machines (VM) olyan alapértelmezett hálózati beállításokat tartalmaz, amelyek továbbra is optimalizálva lesznek a hálózati átviteli sebességre. Ez a cikk azt ismerteti, hogyan optimalizálható a hálózati átviteli sebesség Microsoft Azure Windows és Linux rendszerű virtuális gépek esetében, beleértve a nagyobb disztribúciókat, mint például az Ubuntu, a CentOS és a Red Hat.

## <a name="windows-vm"></a>Windows rendszerű virtuális gép

Ha a Windows rendszerű virtuális gép támogatja a [gyorsított hálózatkezelést](create-vm-accelerated-networking-powershell.md), a funkció engedélyezése az átviteli sebesség optimális beállítása lenne. A fogadó oldali skálázás (RSS) használatával minden más Windows rendszerű virtuális gép esetében magasabb maximális átviteli sebességet érhet el, mint az RSS nélküli virtuális gépek. Előfordulhat, hogy az RSS alapértelmezés szerint le van tiltva egy Windows rendszerű virtuális gépen. Az alábbi lépéseket követve megállapíthatja, hogy engedélyezve van-e az RSS, és engedélyezheti, ha jelenleg le van tiltva.

1. Ellenőrizze, hogy engedélyezve van-e az RSS egy hálózati adapterhez a `Get-NetAdapterRss` PowerShell-paranccsal. A következő példában szereplő kimenetben az `Get-NetAdapterRss` RSS nincs engedélyezve.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Az RSS engedélyezéséhez írja be a következő parancsot:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Az előző parancsnak nincs kimenete. A parancs módosította a hálózati adapter beállításait, ami egy percen keresztül ideiglenes kapcsolat elvesztését okozta. A kapcsolat elvesztésekor megjelenik egy újracsatlakozás párbeszédpanel. A kapcsolódást általában a harmadik kísérlet után állítják vissza.
3. Ellenőrizze, hogy az RSS engedélyezve van-e a virtuális gépen a parancs ismételt beírásával `Get-NetAdapterRss` . Ha a művelet sikeres, a következő példa kimenetet adja vissza:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux rendszerű virtuális gép

Az RSS-t az Azure Linux virtuális gépek alapértelmezés szerint mindig engedélyezik. Az október 2017 óta kiadott linuxos kernelek olyan új hálózati optimalizálási lehetőségekkel rendelkeznek, amelyek lehetővé teszik a linuxos virtuális gépek számára a nagyobb hálózati átviteli sebesség elérését.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu az új üzemelő példányokhoz

Az Ubuntu Azure kernel a leginkább optimalizált hálózati teljesítmény az Azure-ban. A legújabb optimalizálások beszerzéséhez először telepítse az 18,04-LTS legújabb támogatott verzióját, a következőképpen:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "18.04-LTS",
"Version": "latest"
```

A létrehozás befejezését követően adja meg a következő parancsokat a legújabb frissítések beszerzéséhez. Ezek a lépések az Ubuntu Azure kernelt jelenleg futtató virtuális gépeken is működnek.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

A következő opcionális parancssori beállítás hasznos lehet az olyan meglévő Ubuntu-telepítések esetében, amelyek már rendelkeznek az Azure kernelével, de a hibákkal kapcsolatos további frissítések nem sikerültek.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel frissítése meglévő virtuális gépekhez

Az Azure Linux kernelre való frissítéssel jelentős átviteli teljesítmény érhető el. Annak ellenőrzéséhez, hogy van-e ilyen kernel, ellenőrizze a kernel verzióját. A példánál vagy annál későbbinek kell lennie.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Ha a virtuális gép nem rendelkezik Azure kernelrel, a verziószám általában a "4,4" előtaggal kezdődik. Ha a virtuális gépnek nincs Azure kernele, futtassa a következő parancsokat root-ként:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

A legújabb optimalizáláshoz a következő paraméterek megadásával érdemes létrehozni egy virtuális gépet a legújabb támogatott verzióval:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.7",
"Version": "latest"
```

Az új és a meglévő virtuális gépek kihasználhatják a legújabb Linux Integration Services (LIS) telepítését. Az átviteli sebesség optimalizálása a 4.2.2-2-es verziójától kezdődően, a későbbi verziók azonban további fejlesztéseket is tartalmaz. A legújabb LIS telepítéséhez adja meg a következő parancsokat:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

Az optimalizáláshoz érdemes létrehozni egy virtuális gépet a legújabb támogatott verzióval a következő paraméterek megadásával:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Az új és a meglévő virtuális gépek kihasználhatják a legújabb Linux Integration Services (LIS) telepítését. Az átviteli sebesség optimalizálása: LIS, 4,2-tól kezdődően. A LIS letöltéséhez és telepítéséhez adja meg a következő parancsokat:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

További információ a Hyper-V-hez készült Linux Integration Services 4,2-es verziójáról a [letöltési oldal](https://www.microsoft.com/download/details.aspx?id=55106)megtekintésével.

## <a name="next-steps"></a>Következő lépések
* Virtuális gépek üzembe helyezése közel egymáshoz a [közelségi elhelyezési csoporttal](../virtual-machines/co-location.md) való alacsony késés érdekében
* Tekintse meg az optimalizált eredményt a [sávszélesség/átviteli sebesség tesztelése Azure virtuális gépen](virtual-network-bandwidth-testing.md) a forgatókönyvhöz.
* További információ arról [, hogyan vannak lefoglalva a sávszélesség a virtuális gépekhez](virtual-machine-network-throughput.md)
* További információ az [Azure Virtual Network gyakori kérdéseiről (GYIK)](virtual-networks-faq.md)