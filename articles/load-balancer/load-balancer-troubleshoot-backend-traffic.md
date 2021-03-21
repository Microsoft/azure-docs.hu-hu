---
title: Az Azure Load Balancer hibaelhárítása
description: Ismerje meg, hogy miként lehet elhárítani a Azure Load Balancer ismert problémáit.
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
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: bfe2b21a86f2ce4b4630ba69cde87796fd367f4b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98029199"
---
# <a name="troubleshoot-azure-load-balancer-backend-traffic-responses"></a>Azure Load Balancer háttérbeli forgalmi válaszok hibáinak megoldása

Ez az oldal Azure Load Balancer kérdésekkel kapcsolatos hibaelhárítási információkat tartalmaz.

## <a name="vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Load Balancer mögötti virtuális gépek nem válaszolnak a konfigurált adatporton tárolt forgalomra

Ha a háttérrendszer-készlet virtuális gépe kifogástalan állapotú, és válaszol az állapotfigyelő tesztekre, de továbbra sem vesz részt a terheléselosztásban, vagy nem válaszol az adatforgalomra, az a következő okok bármelyike miatt előfordulhat:
* Load Balancer háttérbeli készlet virtuális gépe nem figyeli az adatportot
* A hálózati biztonsági csoport blokkolja a portot a Load Balancer háttérrendszer-készlet virtuális gépén.  
* A Load Balancer elérése ugyanarról a virtuális gépről és hálózati adapterről
* Az Internet Load Balancer frontend elérése a résztvevő Load Balancer háttérbeli készletből származó virtuális gépről

## <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>1. ok: Load Balancer háttérbeli készlet virtuális gépe nem figyeli az adatportot

Ha egy virtuális gép nem válaszol az adatforgalomra, annak oka az lehet, hogy a célként megadott port nincs megnyitva a résztvevő virtuális gépen, vagy a virtuális gép nem figyeli a portot. 

**Ellenőrzés és megoldás**

1. Jelentkezzen be a háttérbeli virtuális gépre. 
2. Nyisson meg egy parancssort, és futtassa a következő parancsot annak ellenőrzéséhez, hogy az alkalmazás figyeli-e az adatportot:  
            netstat – a 
3. Ha a port nem szerepel a "LISTENing" állapotú állapotban, konfigurálja a megfelelő figyelő portot. 
4. Ha a port figyelt van megjelölve, akkor bármely lehetséges probléma esetén ellenőrizze az adott porton található célalkalmazás használatát.

## <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>2. ok: a hálózati biztonsági csoport blokkolja a portot a Load Balancer háttérrendszer-készlet virtuális gépén.  

Ha az alhálózaton vagy a virtuális gépen konfigurált egy vagy több hálózati biztonsági csoport blokkolja a forrás IP-címet vagy portot, akkor a virtuális gép nem tud válaszolni.

A nyilvános terheléselosztó esetében az internetes ügyfelek IP-címe az ügyfelek és a terheléselosztó háttérbeli virtuális gépek közötti kommunikációhoz lesz használva. Győződjön meg arról, hogy az ügyfelek IP-címe engedélyezett a háttérbeli virtuális gép hálózati biztonsági csoportjában.

1. A háttérbeli virtuális gépen konfigurált hálózati biztonsági csoportok listázása. További információ: [hálózati biztonsági csoportok kezelése](../virtual-network/manage-network-security-group.md)
1. A hálózati biztonsági csoportok listájából ellenőrizze, hogy:
    - az adatporton bejövő vagy kimenő forgalom interferenciát okoz. 
    - az összes hálózati biztonsági csoport **megtagadása** a virtuális gép hálózati adapterén vagy az alhálózaton, amelynek nagyobb a prioritása, hogy az Load Balancer-mintavételt és-forgalmat engedélyező alapértelmezett szabály (a hálózati biztonsági csoportoknak engedélyezniük kell a 168.63.129.16 Load Balancer IP-címét, azaz a mintavételi portot)
1. Ha a szabályok bármelyike blokkolja a forgalmat, távolítsa el és konfigurálja újra ezeket a szabályokat az adatforgalom engedélyezéséhez.  
1. Tesztelje, hogy a virtuális gép most már elindult-e az állapotra.

## <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>3. ok: a Load Balancer elérése ugyanarról a virtuális gépről és hálózati adapterről 

Ha egy Load Balancer háttérbeli virtuális gépén lévő alkalmazása megpróbál elérni egy másik, ugyanazon a háttér-virtuális gépen futó alkalmazást ugyanazon a hálózati adapteren keresztül, akkor ez nem támogatott forgatókönyv, és sikertelen lesz. 

**Megoldás** A probléma a következő módszerek egyikével oldható meg:
* Különálló háttérrendszer-készlet virtuális gépek konfigurálása egy alkalmazásban. 
* Konfigurálja az alkalmazást kettős NIC virtuális gépeken, hogy minden alkalmazás a saját hálózati adapterét és IP-címét használja. 

## <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>4. ok: a belső Load Balancer előtér elérésének elérése a résztvevő Load Balancer backend Pool virtuális gépről

Ha egy belső Load Balancer konfigurálva van egy VNet belül, és a résztvevői háttérbe tartozó virtuális gépek egyike megpróbál hozzáférni a belső Load Balancer előtérhöz, a hibák akkor fordulhatnak elő, ha a folyamat a kezdeményező virtuális gépre van leképezve. Ez a forgatókönyv nem támogatott.

**Megoldás** A forgatókönyv feloldásának számos módja van, beleértve a proxy használatát is. Értékelje ki Application Gateway vagy más harmadik féltől származó proxyt (például Nginx vagy HAProxy). További információ a Application Gatewayról: [Application Gateway áttekintése](../application-gateway/overview.md)

**Részletek** A belső terheléselosztó nem fordítja le a kimenő kapcsolatokat egy belső Load Balancer elülső végére, mert mindkettő magánhálózati IP-címtartomány részét képezi. A nyilvános terheléselosztó [kimenő kapcsolatokat](load-balancer-outbound-connections.md) biztosít a virtuális hálózaton belüli magánhálózati IP-címekről a nyilvános IP-címekre. A belső terheléselosztó esetében ez a módszer elkerüli a lehetséges SNAT-portok kimerülését egy egyedi belső IP-címtartomány belsejében, ahol a fordítás nem szükséges.

Ennek egyik mellékhatása, hogy ha a háttér-készletben lévő virtuális gépről érkező kimenő forgalom a készlet belső Load Balancerának végére kísérli meg a folyamatot, _és_ az önmagához van rendelve, a folyamat két lába nem egyezik. Mivel nem egyeznek, a folyamat meghiúsul. A folyamat abban az esetben sikeres, ha a folyamat nem képezi vissza ugyanazt a virtuális gépet a háttér-készletben, amely létrehozta a folyamatot az előtér-végpontnak.

Ha a folyamat önmagára mutat, a kimenő folyamat úgy tűnik, hogy a virtuális gépről származik, és a megfelelő bejövő folyamat úgy tűnik, hogy a virtuális gépről származik. A vendég operációs rendszer szempontjából az ugyanazon folyamat bejövő és kimenő részei nem egyeznek meg a virtuális géppel. A TCP-verem nem ismeri fel ugyanezen folyamat azon felét, mint ugyanazon folyamat részeként. A forrás és a cél nem egyezik. Ha a folyamat a háttérbeli készlet bármely más virtuális gépe felé mutat, a folyamat felei megegyeznek, és a virtuális gép válaszolni tud a folyamatra.

Ennek a forgatókönyvnek a tünete átmeneti kapcsolati időtúllépés, amikor a folyamat visszatér ugyanarra a háttérre, amely a folyamatból származik. Gyakori megkerülő megoldások közé tartozik a belső Load Balancer mögötti proxy réteg beszúrása, valamint a közvetlen kiszolgáló Return (DSR) stílusú szabályok használata. További információ: [Azure Load Balancer több előtérbeli felülete](load-balancer-multivip-overview.md).

A belső Load Balancer bármely harmadik féltől származó proxyval egyesítheti, vagy használhat belső [Application Gateway](../application-gateway/overview.md) a proxy forgatókönyvekhez HTTP/HTTPS használatával. Habár a probléma megoldásához nyilvános Load Balancer is használható, az eredményül kapott forgatókönyv a [SNAT kimerülését](load-balancer-outbound-connections.md)eredményezi. Kerülje ezt a második megközelítést, ha körültekintően nem felügyelt.

## <a name="next-steps"></a>Következő lépések

Ha a fenti lépések nem oldják meg a problémát, nyisson meg egy [támogatási jegyet](https://azure.microsoft.com/support/options/).