---
title: A DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a DHCPv6-t Linux rendszerű virtuális gépekhez.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure Load Balancer, Dual stack, nyilvános IP-cím, natív IPv6, mobil, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: d8bd62bab627beb70a8fcba276bf8c2eca309c45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96006746"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>A DHCPv6 konfigurálása Linux rendszerű virtuális gépekhez


Az Azure Marketplace-en található linuxos virtuálisgép-rendszerképek közül néhány nem rendelkezik alapértelmezettként konfigurált Dynamic Host Configuration Protocol 6-os verzióval (DHCPv6). Az IPv6 támogatásához a DHCPv6-t az Ön által használt Linux operációs rendszer-eloszlásban kell konfigurálni. A különböző Linux-disztribúciók többféle módon konfigurálhatják a DHCPv6-t, mert különböző csomagokat használnak.

> [!NOTE]
> Az Azure Marketplace-en a legújabb SUSE Linux-és CoreOS-lemezképek előre konfigurálva lettek a DHCPv6-sel. A rendszerképek használatakor nincs szükség további módosításokra.

Ez a dokumentum leírja, hogyan engedélyezhető a DHCPv6, hogy a linuxos virtuális gép IPv6-címeket szerezzen.

> [!WARNING]
> A hálózati konfigurációs fájlok nem megfelelő szerkesztésével elveszítheti a virtuális géphez való hálózati hozzáférést. Javasoljuk, hogy tesztelje a konfigurációs módosításokat a nem éles rendszereken. A cikkben szereplő utasítások az Azure Marketplace-en található Linux-rendszerképek legújabb verzióin lettek tesztelve. Részletesebb útmutatásért olvassa el a Linux saját verziójának dokumentációját.

## <a name="ubuntu"></a>Ubuntu

1. Szerkessze a */etc/DHCP/dhclient6.conf* fájlt, és adja hozzá a következő sort:

    ```config
    timeout 10;
    ```

2. Szerkessze az ETH0-interfész hálózati konfigurációját a következő konfigurációval:

   * **Ubuntu 12,04 és 14,04** esetén szerkessze a */etc/network/interfaces.d/eth0.cfg* fájlt. 
   * Az **Ubuntu 16,04**-ben szerkessze a */etc/network/interfaces.d/50-Cloud-init.cfg* fájlt.

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

Az Ubuntu 17,10-es verziójától kezdve az alapértelmezett hálózati konfigurációs mechanizmus a [NETPLAN]( https://netplan.io).  A telepítés/létrehozás ideje alatt a NETPLAN beolvassa a hálózati konfigurációt a YAML konfigurációs fájljairól ezen a helyen:/{lib, etc, Run}/netplan/*. YAML.

Adja meg a *dhcp6: true* utasítást a konfigurációban található minden Ethernet-adapterhez.  Például:

```config
network:
  version: 2
  ethernets:
    eno1:
      dhcp6: true
```

A korai rendszerindítás során a "hálózati leképező" netplan úgy írja be a konfigurációt, hogy/Run az eszközök vezérlését a megadott hálózati démonnak a NETPLAN vonatkozó hivatkozási információkkal kapcsolatban: https://netplan.io/reference .
 
## <a name="debian"></a>Debian

1. Szerkessze a */etc/DHCP/dhclient6.conf* fájlt, és adja hozzá a következő sort:

    ```config
    timeout 10;
    ```

2. Szerkessze a */etc/network/interfaces* fájlt, és adja hozzá a következő konfigurációt:

    ```config
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS és Oracle Linux

1. Szerkessze a */etc/sysconfig/network* fájlt, és adja hozzá a következő paramétert:

    ```config
    NETWORKING_IPV6=yes
    ```

2. Szerkessze a */etc/sysconfig/network-scripts/ifcfg-eth0* fájlt, és adja hozzá a következő két paramétert:

    ```config
    IPV6INIT=yes
    DHCPV6C=yes
    ```

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 és openSUSE 13

Az Azure-ban a legutóbbi SUSE Linux Enterprise Server (SLES) és az openSUSE-lemezképek előre konfigurálva lettek a DHCPv6-vel. A rendszerképek használatakor nincs szükség további módosításokra. Ha olyan virtuális géppel rendelkezik, amely egy régebbi vagy egyéni SUSE-rendszerképen alapul, tegye a következőket:

1. `dhcp-client`Szükség esetén telepítse a csomagot:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Szerkessze a */etc/sysconfig/network/ifcfg-eth0* fájlt, és adja hozzá a következő paramétert:

    ```config
    DHCLIENT6_MODE='managed'
    

3. Renew the IPv6 address:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 és openSUSE ugrás

Az Azure-ban a legutóbbi SLES-és openSUSE-lemezképek előre konfigurálva lettek a DHCPv6-sel. A rendszerképek használatakor nincs szükség további módosításokra. Ha olyan virtuális géppel rendelkezik, amely egy régebbi vagy egyéni SUSE-rendszerképen alapul, tegye a következőket:

1. Szerkessze a */etc/sysconfig/network/ifcfg-eth0* fájlt, és cserélje le a `#BOOTPROTO='dhcp4'` paramétert a következő értékre:

    ```config
    BOOTPROTO='dhcp'
    ```

2. A */etc/sysconfig/network/ifcfg-eth0* -fájlhoz adja hozzá a következő paramétert:

    ```config
    DHCLIENT6_MODE='managed'
    ```

3. Újítsa meg az IPv6-cím:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Az Azure-beli legújabb CoreOS-lemezképek előre konfigurálva lettek a DHCPv6-sel. A rendszerképek használatakor nincs szükség további módosításokra. Ha a virtuális gép egy régebbi vagy egyéni CoreOS-rendszerkép alapján rendelkezik, tegye a következőket:

1. Szerkessze a */etc/systemd/network/10_dhcp. Network* fájlt:

    ```config
    [Match]
    eth0

    [Network]
    DHCP=ipv6
    ```

2. Újítsa meg az IPv6-cím:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
