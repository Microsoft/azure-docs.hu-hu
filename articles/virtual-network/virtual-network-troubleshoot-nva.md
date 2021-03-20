---
title: A hálózati virtuális berendezésekkel kapcsolatos problémák elhárítása az Azure-ban | Microsoft Docs
description: A hálózati virtuális berendezés (NVA) hibáinak elhárítása az Azure-ban és a NVA-konfigurációk alapszintű Azure platformra vonatkozó követelményeinek ellenőrzése.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: fe4c17b74cd786d03bd19257dea190a21ecaa9f5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99095641"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Hálózati virtuális berendezésekkel kapcsolatos problémák az Azure-ban

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Előfordulhat, hogy a virtuális gép vagy a VPN-kapcsolattal kapcsolatos problémákat és hibákat tapasztal, amikor egy harmadik féltől származó hálózati virtuális berendezést (NVA) használ a Microsoft Azure. Ez a cikk alapvető lépéseket biztosít a NVA-konfigurációk alapszintű Azure-platformra vonatkozó követelményeinek érvényesítéséhez.

A harmadik féltől származó NVA és az Azure platformmal való integrációjának technikai támogatását a NVA gyártója biztosítja.

> [!NOTE]
> Ha van olyan kapcsolat vagy útválasztási probléma, amely egy NVA tartalmaz, forduljon közvetlenül [a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Ellenőrzőlista a NVA-gyártóval való hibaelhárításhoz

- Szoftverfrissítések a NVA VM-szoftverekhez
- Szolgáltatásfiók beállítása és funkciója
- Felhasználó által megadott útvonalak (UDR-EK) a virtuális hálózati alhálózatokon, amelyek a NVA irányítják a forgalmat
- UDR a NVA-ből közvetlen forgalomban lévő virtuális hálózati alhálózatokon
- Útválasztási táblák és szabályok a NVA belül (például a NIC1 és a NIC2 között)
- A hálózati forgalom fogadásának és küldésének ellenőrzésére szolgáló NVA hálózati adapterek nyomkövetése
- Szabványos SKU és nyilvános IP-címek használatakor létre kell hozni egy NSG és egy explicit szabályt, amely lehetővé teszi a forgalom átirányítását a NVA.

## <a name="basic-troubleshooting-steps"></a>Alapvető hibaelhárítási lépések

- Az alapszintű konfiguráció keresése
- NVA teljesítményének megtekintése
- Speciális hálózati hibaelhárítás

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Az Azure-beli NVA minimális konfigurációs követelményeinek ellenõrzése

Minden NVA rendelkezik az Azure-ban megfelelő működéséhez szükséges alapszintű konfigurációs követelményekkel. A következő szakasz az alapvető konfigurációk ellenőrzésének lépéseit ismerteti. További információért [forduljon a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655).

**Győződjön meg arról, hogy engedélyezve van-e az IP-továbbítás a NVA**

Az Azure Portal használata

1. Keresse meg a NVA erőforrást a [Azure Portalban](https://portal.azure.com), válassza a hálózatkezelés lehetőséget, majd válassza ki a hálózati adaptert.
2. A hálózati adapter lapon válassza az IP-konfiguráció lehetőséget.
3. Győződjön meg arról, hogy az IP-továbbítás engedélyezve van.

A PowerShell használata

1. Nyissa meg a PowerShellt, majd jelentkezzen be az Azure-fiókjába.
2. Futtassa a következő parancsot (a zárójeles értékeket cserélje le az adataira):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Keresse meg a **EnableIPForwarding** tulajdonságot.
4. Ha az IP-továbbítás nincs engedélyezve, futtassa a következő parancsokat az engedélyezéséhez:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**NSG keresése standard SKU Pubilc IP-cím használata esetén** A standard SKU és a nyilvános IP-címek használatakor létre kell hozni egy NSG és egy explicit szabályt, amely engedélyezi a forgalmat a NVA.

**Győződjön meg arról, hogy a forgalom átirányítható-e a NVA**

1. [Azure Portalon](https://portal.azure.com)nyissa meg a **Network Watcher**, majd válassza a **következő ugrás** lehetőséget.
2. Olyan virtuális gépet válasszon, amely átirányítja a forgalmat a NVA, valamint egy cél IP-címet, amelyen a következő ugrás megtekinthető. 
3. Ha a NVA nem szerepel a **következő ugrásként**, ellenőrizze és frissítse az Azure Route-táblákat.

**Annak megkeresése, hogy a forgalom elérheti-e a NVA**

1. [Azure Portal](https://portal.azure.com)nyissa meg a **Network Watcher**, majd válassza az **IP-folyamat ellenőrzése** lehetőséget. 
2. Adja meg a virtuális gépet és a NVA IP-címét, majd győződjön meg arról, hogy bármely hálózati biztonsági csoport (NSG) blokkolja-e a forgalmat.
3. Ha van olyan NSG-szabály, amely blokkolja a forgalmat, keresse meg a NSG a **hatályos biztonsági** szabályokban, majd frissítse azt a forgalom továbbításának engedélyezéséhez. Ezután futtassa újra az **IP-folyamatot** , és használja a **kapcsolati HIBÁit** a virtuális gépekről a belső vagy külső IP-CÍMÉRE irányuló TCP-kommunikáció teszteléséhez.

**Győződjön meg arról, hogy a NVA és a virtuális gépek figyelik-e a várható forgalmat**

1. Csatlakozzon a NVA RDP vagy SSH használatával, majd futtassa a következő parancsot:

    Windows esetén:

    ```console
   netstat -an
    ```

    Linux esetén:

    ```console
   netstat -an | grep -i listen
    ```
2. Ha nem látja azt a TCP-portot, amelyet az eredmények listájában szereplő NVA szoftver használ, konfigurálnia kell az alkalmazást a NVA és a virtuális gépen, hogy figyelje és reagáljon a portokat elérő forgalomra. [Szükség esetén forduljon a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655).

## <a name="check-nva-performance"></a>NVA teljesítményének megtekintése

### <a name="validate-vm-cpu"></a>Virtuális gép PROCESSZORának ellenőrzése

Ha a CPU-használat a 100 százalékhoz közeledik, akkor előfordulhat, hogy problémák lépnek fel a hálózati csomagok elejtésére. A virtuális gép átlagos CPU-jelentést küld egy adott időtartományhoz a Azure Portalban. A CPU-tüske során vizsgálja meg, hogy a vendég virtuális gép melyik folyamata okozza a nagy CPU-t, és ha lehetséges, csökkentse a problémát. Előfordulhat, hogy a virtuális gépet át kell méreteznie egy nagyobb SKU-méretre, vagy a virtuálisgép-méretezési csoportnál növelje a példányszámot, vagy állítsa a CPU-használat automatikus méretezésére. A problémák bármelyike esetén [segítségért forduljon a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655), igény szerint.

### <a name="validate-vm-network-statistics"></a>Virtuálisgép-hálózati statisztika ellenőrzése

Ha a virtuálisgép-hálózat tüskéket használ, vagy magas kihasználtságú időszakokat jelenít meg, akkor előfordulhat, hogy a virtuális gép SKU-os méretének növelésére van szüksége a magasabb átviteli sebesség érdekében. A virtuális gépet a gyorsított hálózatkezelés engedélyezésével is újra üzembe helyezheti. Annak ellenőrzéséhez, hogy a NVA támogatja-e a gyorsított hálózatkezelési funkciót, szükség esetén [forduljon a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655).

## <a name="advanced-network-administrator-troubleshooting"></a>A hálózati rendszergazda speciális hibaelhárítása

### <a name="capture-network-trace"></a>Hálózati nyomkövetés rögzítése
Rögzítse egyidejű hálózati nyomkövetést a forrásoldali virtuális gépen, a NVA és a cél virtuális gépen a **[PsPing](/sysinternals/downloads/psping)** vagy az **nmap** futtatása közben, majd állítsa le a nyomkövetést.

1. Egyidejű hálózati nyomkövetés rögzítéséhez futtassa a következő parancsot:

   **Windows rendszerhez**

   netsh Trace Start Capture = Yes tracefile = c:\ server_IP. etl forgatókönyv = NETCONNECTION

   **Linux rendszerhez**

   sudo tcpdump-S0-i ETH0-X-w vmtrace. Cap

2. Használjon **PsPing** vagy **nmap** -t a forrás virtuális gépről a cél virtuális gépre (például: `PsPing 10.0.0.4:80` vagy `Nmap -p 80 10.0.0.4` ).
3. Nyissa meg a hálózati nyomkövetést a cél virtuális gépről [Hálózatfigyelő](https://download.cnet.com/s/network-monitor) vagy tcpdump használatával. Alkalmazzon megjelenítési szűrőt a **PsPing** vagy **nmap** -t futtató forrás virtuális gép IP-címére, például `IPv4.address==10.0.0.4 (Windows netmon)` vagy `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Nyomkövetések elemzése

Ha nem látja a háttérbeli virtuális gép nyomkövetéséhez bejövő csomagokat, valószínűleg egy NSG vagy egy UDR zavarja, vagy a NVA útválasztási táblái helytelenek.

Ha a csomagok beérkeznek, de nincs válasz, akkor a virtuálisgép-alkalmazáshoz vagy a tűzfalhoz kapcsolódó problémáról lehet szó. A problémák bármelyike esetén [a szükséges segítségért forduljon a NVA gyártójához](https://mskb.pkisolutions.com/kb/2984655).