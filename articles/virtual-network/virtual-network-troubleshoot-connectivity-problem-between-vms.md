---
title: Az Azure-beli virtuális gépek közötti kapcsolódási problémák elhárítása | Microsoft Docs
description: Útmutató az Azure-beli virtuális gépek közötti kapcsolódási problémák elhárításához és megoldásához.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5e602f8bbdf6ad92acacac6688326a555a0420b6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105042866"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure-beli virtuális gépek közötti kapcsolatok hibaelhárítása

Az Azure-beli virtuális gépek (VM-EK) közötti kapcsolódási problémák merülhetnek fel. Ez a cikk a probléma megoldásához szükséges hibaelhárítási lépéseket ismerteti. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Hibajelenség

Egy Azure-beli virtuális gép nem tud csatlakozni egy másik Azure-beli virtuális géphez.

## <a name="troubleshooting-guidance"></a>Hibaelhárítási útmutató 

1. [Győződjön meg arról, hogy a NIC helytelenül van-e konfigurálva](#step-1-check-whether-nic-is-misconfigured)
2. [Annak ellenőrzéséhez, hogy a NSG vagy a UDR blokkolja-e a hálózati forgalmat](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Annak ellenőrzéséhez, hogy a virtuális gép tűzfala blokkolta-e a hálózati forgalmat](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Annak megállapítása, hogy a virtuális gép alkalmazás vagy szolgáltatás figyeli-e a portot](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Annak ellenőrzéséhez, hogy a problémát a SNAT okozza-e](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Annak ellenőrzéséhez, hogy a rendszer letiltja-e a forgalmat a klasszikus virtuális gép ACL-jei](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Annak megállapítása, hogy a végpont létrejött-e a klasszikus virtuális gép számára](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Próbáljon meg csatlakozni a virtuális gép hálózati megosztásához](#step-8-try-to-connect-to-a-vm-network-share)
9. [Inter-Vnet kapcsolat ellenőrzése](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A probléma elhárításához kövesse az alábbi lépéseket. Az egyes lépések befejezését követően győződjön meg arról, hogy a probléma megoldódott-e. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>1. lépés: Győződjön meg arról, hogy a hálózati adapter helytelenül van-e konfigurálva

Kövesse az [Azure-beli Windows rendszerű virtuális gép hálózati adapterének alaphelyzetbe állítása](/troubleshoot/azure/virtual-machines/reset-network-interface)című témakör lépéseit. 

Ha a probléma a hálózati adapter (NIC) módosítása után következik be, kövesse az alábbi lépéseket:

**Több hálózati adapteres virtuális gépek**

1. Adjon hozzá egy hálózati adaptert.
2. Javítsa ki a hibákat a hibás hálózati adapteren, vagy távolítsa el a rossz hálózati adaptert.  Ezután adja hozzá újra a hálózati adaptert.

További információ: [hálózati adapterek hozzáadása vagy eltávolítása a virtuális gépekről](virtual-network-network-interface-vm.md).

**Egyetlen hálózati adapteres virtuális gép** 

- [Windows rendszerű virtuális gép újbóli üzembe helyezése](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-windows)
- [Linux rendszerű virtuális gép újbóli üzembe helyezése](/troubleshoot/azure/virtual-machines/redeploy-to-new-node-linux)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>2. lépés: Győződjön meg arról, hogy a NSG vagy a UDR blokkolja-e a hálózati forgalmat

[Network Watcher IP-flow ellenőrzése](../network-watcher/network-watcher-ip-flow-verify-overview.md) és [NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) használatával állapítsa meg, hogy van-e olyan hálózati biztonsági csoport (NSG) vagy User-Defined útvonal (UDR), amely zavarja a forgalmat.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>3. lépés: annak ellenőrzése, hogy a virtuálisgép-tűzfal blokkolja-e a hálózati forgalmat

Tiltsa le a tűzfalat, majd tesztelje az eredményt. Ha a probléma megoldódott, ellenőrizze a tűzfal beállításait, majd engedélyezze újra a tűzfalat.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>4. lépés: annak meghatározása, hogy a virtuális gép alkalmazás vagy szolgáltatás figyeli-e a portot

Az alábbi módszerek egyikével ellenőrizhető, hogy a virtuálisgép-alkalmazás vagy-szolgáltatás figyeli-e a portot.

- A következő parancsok futtatásával győződjön meg arról, hogy a kiszolgáló figyeli-e a portot.

**Windows rendszerű virtuális gép**

```console
netstat –ano
```

**Linux rendszerű virtuális gép**

```console
netstat -l
```

- Futtassa a **Telnet** parancsot a virtuális gépen, hogy tesztelje a portot. Ha a teszt sikertelen, az alkalmazás vagy szolgáltatás nincs konfigurálva a port figyelésére.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>5. lépés: annak ellenőrzéséhez, hogy a problémát a SNAT okozza-e

Bizonyos helyzetekben a virtuális gép olyan terheléselosztási megoldás mögé kerül, amely az Azure-on kívüli erőforrásokkal függ. Ezekben az esetekben, ha időszakos kapcsolódási problémákat tapasztal, a problémát a SNAT- [port kimerülése](../load-balancer/load-balancer-outbound-connections.md)okozhatja. A probléma megoldásához hozzon létre egy virtuális IP-címet (vagy a klasszikus ILPIP) a terheléselosztó mögött található minden egyes virtuális gép számára, és gondoskodjon arról, hogy a NSG vagy az ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>6. lépés: annak ellenőrzéséhez, hogy a rendszer letiltja-e a forgalmat a klasszikus virtuális gép ACL-jei

A hozzáférés-vezérlési lista (ACL) lehetővé teszi a virtuális gépek végpontjának forgalmának szelektív engedélyezését vagy megtagadását. További információ: [a hozzáférés-vezérlési lista kezelése egy végponton](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>7. lépés: annak meghatározása, hogy a végpont létrejött-e a klasszikus virtuális géphez

Az Azure-ban a klasszikus üzemi modell használatával létrehozott összes virtuális gép automatikusan képes kommunikálni egy magán hálózati csatornán keresztül más, azonos felhőalapú szolgáltatásban vagy virtuális hálózaton lévő virtuális gépekkel. A más virtuális hálózatokon található számítógépek azonban végpontokat igényelnek a virtuális gépek bejövő hálózati forgalmának irányításához. További információ: [a végpontok beállítása](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>8. lépés: próbáljon meg csatlakozni egy virtuálisgép-hálózati megosztáshoz

Ha nem tud kapcsolódni egy virtuálisgép-hálózati megosztáshoz, a problémát a virtuális gép nem elérhető hálózati adapterei okozzák. A nem elérhető hálózati adapterek törlését lásd: [a nem elérhető hálózati adapterek törlése](/troubleshoot/azure/virtual-machines/reset-network-interface#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>9. lépés: Inter-Vnet kapcsolat ellenőrzése

[Network Watcher IP-flow ellenőrzése](../network-watcher/network-watcher-ip-flow-verify-overview.md) és [NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) használatával ellenőrizze, hogy van-e olyan NSG vagy UDR, amely zavarja a forgalom áramlását. [Itt](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)ellenőrizheti a Inter-Vnet konfigurációját is.

### <a name="need-help-contact-support"></a>Segítségre van szüksége? Vegye fel a kapcsolatot az ügyfélszolgálattal.
Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.