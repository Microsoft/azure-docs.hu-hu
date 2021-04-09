---
title: Azure Load Balancer állapot mintavételi állapotával kapcsolatban felmerülő hibák
description: Ismerje meg, hogy miként lehet elhárítani a Azure Load Balancer állapot-mintavételi állapottal kapcsolatos ismert problémákat.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/02/2020
ms.author: allensu
ms.openlocfilehash: 28823c997cd974d5061829df88680ed52075caa0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98029209"
---
# <a name="troubleshoot-azure-load-balancer-health-probe-status"></a>Azure Load Balancer állapot mintavételi állapotával kapcsolatban felmerülő hibák

Ez az oldal a gyakori Azure Load Balancer állapot-mintavételi problémákkal kapcsolatos hibaelhárítási információkat tartalmaz.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Tünet: a Load Balancer mögötti virtuális gépek nem válaszolnak az állapot-mintavételre
Ahhoz, hogy a háttér-kiszolgálók részt vegyenek a terheléselosztó készletében, át kell adni a mintavétel-ellenőrzést. További információ az állapot-mintavételekről: Load Balancer mintavételek [ismertetése](load-balancer-custom-probe-overview.md). 

Előfordulhat, hogy a Load Balancer backend-készlet virtuális gépei nem válaszolnak a mintavételre a következő okok miatt: 
- Load Balancer háttérbeli készlet virtuális gépe nem kifogástalan állapotú 
- Load Balancer háttérbeli készlet virtuális gépe nem figyeli a mintavételi portot 
- A tűzfal vagy egy hálózati biztonsági csoport blokkolja a portot a Load Balancer backend-készletben lévő virtuális gépeken 
- Egyéb helytelen konfigurációk Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>1. ok: Load Balancer háttérbeli készlet virtuális gépe sérült

**Ellenőrzés és megoldás**

A probléma megoldásához jelentkezzen be a részt vevő virtuális gépekre, és ellenőrizze, hogy a virtuális gép állapota Kifogástalan-e, és tud-e válaszolni a **PsPing** vagy a **TCPING** egy másik virtuális gépről a készletben. Ha a virtuális gép nem kifogástalan állapotú, vagy nem tud válaszolni a mintavételre, ki kell javítania a problémát, és vissza kell állítania a virtuális gépet egy Kifogástalan állapotba, mielőtt részt tudjon venni a terheléselosztásban.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>2. ok: Load Balancer háttérbeli készlet virtuális gépe nem figyeli a mintavételi portot
Ha a virtuális gép kifogástalan állapotban van, de nem válaszol a mintavételre, az egyik lehetséges ok az lehet, hogy a mintavételi port nincs megnyitva a résztvevő virtuális gépen, vagy a virtuális gép nem figyeli a portot.

**Ellenőrzés és megoldás**

1. Jelentkezzen be a háttérbeli virtuális gépre.
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy van-e olyan alkalmazás, amely figyeli a mintavételi portot: netstat-an
3. Ha a port állapota nem **figyelt** van felsorolva, konfigurálja a megfelelő portot. 
4. Másik lehetőségként kiválaszthat egy másik portot, amely **Figyelésként** jelenik meg, és ennek megfelelően frissíti a terheléselosztó konfigurációját.

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>3. ok: a tűzfal vagy egy hálózati biztonsági csoport blokkolja a terheléselosztó háttér-készletének virtuális gépei portját.
Ha a virtuális gépen lévő tűzfal blokkolja a mintavételi portot, vagy egy vagy több, az alhálózaton vagy a virtuális gépen konfigurált hálózati biztonsági csoport nem engedélyezi a mintavételt a port eléréséhez, a virtuális gép nem tud válaszolni az állapot-mintavételre.

**Ellenőrzés és megoldás**

1. Ha a tűzfal engedélyezve van, ellenőrizze, hogy be van-e állítva a mintavételi port engedélyezéséhez. Ha nem, konfigurálja úgy a tűzfalat, hogy engedélyezze a forgalmat a mintavételi porton, majd tesztelje újra.
2. A hálózati biztonsági csoportok listájából ellenőrizze, hogy a mintavételi port bejövő vagy kimenő forgalma interferencia-e.
3. Azt is ellenőrizze, hogy az összes hálózati biztonsági csoport **megtagadva** szabály a virtuális gép hálózati adapterén vagy az alhálózaton, amely magasabb prioritású, mint az LB-szondák & forgalmat engedélyező alapértelmezett szabálynál (a hálózati biztonsági csoportoknak engedélyezniük kell Load Balancer IP-168.63.129.16).
4. Ha a szabályok bármelyike blokkolja a mintavételi forgalmat, távolítsa el és konfigurálja újra a szabályokat a mintavételi forgalom engedélyezéséhez.  
5. Tesztelje, hogy a virtuális gép most már megkezdte-e a válaszadást az állapotra.

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>4. ok: egyéb helytelen konfigurációk Load Balancer
Ha az összes korábbi ok úgy tűnik, hogy a rendszer megfelelően érvényesíti és orvosolja az összes okot, és a háttérbeli virtuális gép továbbra sem válaszol az állapot-mintavételre, akkor manuálisan tesztelje a kapcsolatot, és gyűjtsön néhány nyomkövetést a kapcsolat megismeréséhez.

**Ellenőrzés és megoldás**

1. Használja a **Psping** a VNet belüli egyik másik virtuális gépről a mintavételi port válaszának teszteléséhez (például: .\psping.exe-t 10.0.0.4:3389), és jegyezze fel az eredményeket. 
2. Használja a **TCPing** a VNet belüli egyik másik virtuális gépről a mintavételi port válaszának teszteléséhez (például: .\tcping.exe 10.0.0.4 3389), és jegyezze fel az eredményeket. 
3. Ha nem érkezik válasz a ping tesztek során, akkor
    - Futtasson egyidejű netsh-nyomkövetést a cél háttérrendszer-készlet virtuális gépén és egy másik, ugyanabból a VNet származó teszt virtuális gépről. Most futtasson egy PsPing-tesztet egy ideig, gyűjtsön néhány hálózati nyomkövetést, majd állítsa le a tesztet. 
    - Elemezze a hálózati rögzítést, és ellenőrizze, hogy vannak-e a ping lekérdezéshez kapcsolódó bejövő és kimenő csomagok is. 
        - Ha nem figyelhetők meg bejövő csomagok a háttérrendszer-készlet virtuális gépen, akkor potenciálisan hálózati biztonsági csoportok vagy UDR hibás konfiguráció blokkolja a forgalmat. 
        - Ha nem figyelhetők meg kimenő csomagok a háttérrendszer-készlet virtuális gépen, a virtuális gépet ellenőrizni kell a nem kapcsolódó problémák miatt (például az alkalmazás blokkolja a mintavételi portot). 
    - Ellenőrizze, hogy a mintavételi csomagok kényszerítve vannak-e egy másik célra (valószínűleg UDR-beállításokon keresztül) a terheléselosztó elérése előtt. Ez azt eredményezheti, hogy a forgalom soha nem éri el a háttérbeli virtuális gépet. 
4. Módosítsa a mintavételi típust (például HTTP-ről TCP-re), és konfigurálja a megfelelő portot a hálózati biztonsági csoportok ACL-jei és a tűzfalon annak ellenőrzéséhez, hogy a probléma a mintavételi válasz konfigurációjának megfelelően van-e kiválasztva. További információ az állapot-mintavételezési konfigurációról: a végpont terheléselosztási [állapotának mintavételi beállítása](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).