---
title: Nyilvános IP-címek az Azure-ban
titlesuffix: Azure Virtual Network
description: Információk a nyilvános IP-címekről az Azure-ban.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: d1d412774dc68cba0c24709c7fc8a7999e0aeab8
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106094761"
---
# <a name="public-ip-addresses"></a>Nyilvános IP-címek

A nyilvános IP-címek lehetővé teszik az internetes erőforrások bejövő kommunikációját az Azure-erőforrásokkal. A nyilvános IP-címek lehetővé teszik az Azure-erőforrások számára az internettel és a nyilvános Azure-szolgáltatásokkal való kommunikációt. A címeket a rendszer az erőforráshoz rendeli, egészen addig, amíg nincs hozzárendelve. Egy nyilvános IP-címmel nem rendelkező erőforrás képes a kimenő kommunikációra. Az Azure dinamikusan rendel hozzá olyan elérhető IP-címet, amely nincs hozzárendelve az erőforráshoz. További információt a [kimenő kapcsolatokat ismertető](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cikkben talál az Azure kimenő kapcsolatairól.

Az Azure Resource Manager szolgáltatásban a [nyilvános IP-cím](virtual-network-public-ip-address.md) olyan erőforrás, amely saját tulajdonságokkal rendelkezik. A nyilvános IP-cím erőforrásait az alábbiak szerint társíthatja:

* Virtuális gépek hálózati adapterei
* Internetkapcsolattal rendelkező terheléselosztók
* VPN-átjárók
* Alkalmazásátjárók
* Azure Firewall

## <a name="ip-address-version"></a>IP-cím verziója

A nyilvános IP-címek IPv4- vagy IPv6-címekkel jönnek létre. 

## <a name="sku"></a>Termékváltozat

Az SKU frissítésével kapcsolatos további tudnivalókért tekintse meg a [nyilvános IP-cím frissítését](../virtual-network/virtual-network-public-ip-address-upgrade.md)ismertető témakört.

A nyilvános IP-címek a következő termékváltozatok valamelyikével jönnek létre:

>[!IMPORTANT]
> A Load Balancerhez és a nyilvános IP-erőforrásokhoz megfelelő SKU-ket kell megadni. Az alapszintű és a standard SKU-erőforrások nem kombinálhatók. Nem csatolhat mindkét SKU-hoz egyszerre önálló virtuális gépeket, rendelkezésre állási csoportban lévő virtuális gépeket vagy virtuálisgép-méretezési csoport típusú erőforrásokat.  Új kialakítások esetén a standard SKU-erőforrások használata ajánlott.  Részletekért tekintse át a [Standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című részt.

### <a name="standard"></a>Standard

Standard SKU nyilvános IP-címei:

- Mindig használjon statikus kiosztási módszert.
- Egy állítható bejövő, 4-30 perces üresjárati időkorláttal rendelkezik, amely alapértéke 4 perc, a rögzített kimenő folyamat pedig 4 perces üresjárati időkorlátot tartalmaz.
- Alapértelmezés szerint biztonságos és a bejövő forgalomhoz zárva van. Engedélyezi a bejövő forgalom listázását egy [hálózati biztonsági csoporttal](./network-security-groups-overview.md#network-security-groups).
- A hálózati adapterekhez, a standard nyilvános terheléselosztóhoz vagy az Application Gatewayhez van rendelve. További információ a standard Load balancerről: [Azure standard Load Balancer](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- A zóna redundáns lehet (az összes 3 zónából meghirdetve), a (megadott előre kiválasztott rendelkezésre állási zónában garantált), vagy nem zónában (egy adott előre kiválasztott rendelkezésre állási zónához nem társítva). További információ a rendelkezésre állási zónákról: [A rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [A Standard Load Balancer és a rendelkezésre állási zónák](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **A zóna redundáns IP-címei csak olyan régiókban hozhatók létre, [amelyekben 3 rendelkezésre állási zóna](../availability-zones/az-region.md) él.** A zónák élő létrehozása előtt létrehozott IP-címek nem lesznek redundáns zónában.
- A többrégiós terheléselosztó (előzetes verzió) számára is használható a [többhelyes terheléselosztási](../load-balancer/cross-region-overview.md) IP-címekhez.
 
> [!NOTE]
> A standard SKU-erőforrással való bejövő kommunikáció meghiúsul, amíg létre nem hoz egy [hálózati biztonsági csoportot](./network-security-groups-overview.md#network-security-groups) , és kifejezetten engedélyezi a kívánt bejövő forgalmat.

> [!NOTE]
> A [példány metaadatainak szolgáltatás IMDS](../virtual-machines/windows/instance-metadata-service.md)használata esetén csak az alapszintű SKU-val rendelkező nyilvános IP-címek érhetők el. A standard SKU nem támogatott.

> [!NOTE]
> Szabványos SKU nyilvános IP-cím használata esetén a diagnosztikai beállítások nem jelennek meg a erőforrás panelen. Ha engedélyezni szeretné a naplózást a standard nyilvános IP-cím erőforráson, navigáljon a Azure Monitor panelen található diagnosztikai beállításokhoz, és válassza ki az IP-cím erőforrását.

### <a name="basic"></a>Alapszintű

A termékváltozatok bevezetése előtt létrehozott minden nyilvános IP-cím alapszintű termékváltozatú nyilvános IP-cím. 

A SKU bevezetésével határozza meg, hogy melyik SKU-t szeretné a nyilvános IP-címhez adni. 

Alapszintű SKU-címek:

- A statikus vagy a dinamikus kiosztási módszerrel oszthatók ki.
- Egy állítható bejövő, 4-30 perces üresjárati időkorláttal rendelkezik, amely alapértéke 4 perc, a rögzített kimenő folyamat pedig 4 perces üresjárati időkorlátot tartalmaz.
- Alapértelmezés szerint nyitva vannak.  A hálózati biztonsági csoportok használata ajánlott, de nem kötelező a bejövő és kimenő forgalom korlátozásához.
- Hozzárendelve bármely olyan Azure-erőforráshoz, amely nyilvános IP-címet rendelhet hozzá, például:
    * Hálózati adapterek
    * VPN-átjárók
    * Application Gateway-átjárók
    * Nyilvános terheléselosztó
- Nem támogatja a rendelkezésre állási zónák forgatókönyveit. Használja a standard SKU nyilvános IP-címet a rendelkezésre állási zónák forgatókönyvei esetében. További információ a rendelkezésre állási zónákról: [A rendelkezésre állási zónák áttekintése](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) és [A Standard Load Balancer és a rendelkezésre állási zónák](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Lefoglalási módszer

Az alapszintű és a standard nyilvános IP-címek támogatják a **statikus** hozzárendelést.  Az erőforrás egy IP-címet kap a létrehozásuk időpontjában. Az IP-cím az erőforrás törlésekor jelenik meg.

Az alapszintű SKU nyilvános IP-címei támogatják a **dinamikus** hozzárendelést. A dinamikus az alapértelmezett hozzárendelési módszer. Az IP-cím **nincs** megadva az erőforráshoz a létrehozáskor a dinamikus kiválasztásakor.

Az IP-címet a rendszer a nyilvános IP-cím erőforráshoz való hozzárendelésekor rendeli hozzá:

* Virtuális gép 
* Az első virtuális gép a terheléselosztó háttér-készletéből van társítva.

Az IP-cím akkor szabadul fel, ha leállítja (vagy törli) az erőforrást.  

Például egy nyilvános IP-erőforrást az **a erőforrás egy** nevű erőforrása bocsát ki. Az **a erőforrás** egy másik IP-címet kap az indításkor, ha a nyilvános IP-erőforrás újra van rendelve.

Az IP-cím akkor jelenik meg, ha a kiosztási módszer **statikusról** **dinamikusra** változik. Annak biztosítása érdekében, hogy a kapcsolódó erőforrás IP-címe azonos maradjon, állítsa a kiosztási módszert explicit módon **statikusra**. A rendszer azonnal kioszt egy statikus IP-címet.

> [!NOTE]
> Akkor sem adhatja meg a nyilvános IP-cím erőforráshoz rendelt konkrét IP-címet, ha a kiosztási módszert **statikusra** állítja. Ehelyett az Azure az elérhető IP-címek készletéből foglalja le az IP-címet azon az Azure-helyen, ahol az erőforrást létrehozták.
>

Statikus nyilvános IP-címeket általában a következő esetekben szoktak használni:

* Amikor frissíteni kell a tűzfalszabályokat, hogy kommunikálhassanak az Azure-erőforrásaival.
* DNS-névfeloldás, ahol az IP-cím változása miatt frissíteni kellene az A-rekordokat.
* Az Ön Azure-erőforrásai más alkalmazásokkal és szolgáltatásokkal is kommunikálnak, amelyek IP-címalapú biztonsági modellt használnak.
* Egy IP-címhez társított TLS/SSL-tanúsítványokat használ.

> [!NOTE]
> Az Azure a nyilvános IP-címeket az egyes régiókhoz tartozó egyedi tartományokból foglalja le a különböző Azure-felhőkben. Letöltheti a tartományok (előtagok) listáját az Azure [nyilvános](https://www.microsoft.com/download/details.aspx?id=56519), valamint [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) és [Germany](https://www.microsoft.com/download/details.aspx?id=57064) felhője esetében.
>

## <a name="dns-hostname-resolution"></a>DNS-állomásnév feloldása

Válassza a nyilvános IP-erőforráshoz tartozó DNS-tartománynév címke megadásának lehetőségét. 

Ez a kijelölés létrehoz egy leképezést a **domainnamelabel értékkel**. **Location**. cloudapp.Azure.com a nyilvános IP-címhez az Azure által felügyelt DNS-ben. 

Például egy nyilvános IP-cím létrehozása a következővel:

* **contoso** **domainnamelabel értékkel**
* **USA nyugati** régiója Azure-beli **hely**

A teljes tartománynév (FQDN) **contoso.westus.cloudapp.Azure.com** az erőforrás nyilvános IP-címére oldja fel.

> [!IMPORTANT]
> Minden egyes létrehozott tartománynév-címkének egyedinek kell lennie az Azure-helyen.  
>

## <a name="dns-recommendations"></a>DNS-javaslatok

Ha szükség van egy régió áthelyezésére, akkor nem tudja áttelepíteni a nyilvános IP-cím teljes tartománynevét. A teljes tartománynév használatával hozzon létre egy egyéni CNAME rekordot, amely a nyilvános IP-címhez mutat. 

Ha egy másik nyilvános IP-re való áttérés szükséges, frissítse a CNAME rekordot a teljes tartománynév frissítése helyett.

A DNS-rekordhoz [Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) vagy külső DNS-szolgáltatót is használhat. 

## <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

A nyilvános IP-címet társíthatja [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rendszerű virtuális géphez, ha hozzárendeli a **hálózati adapteréhez**. 

Válassza a **dinamikus** vagy a **statikus** lehetőséget a nyilvános IP-címhez. Tudjon meg többet [az IP-címek hálózati adapterekhez való hozzárendeléséről](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Internetkapcsolattal rendelkező terheléselosztók

Az [SKU](#sku) -hoz tartozó nyilvános IP-címet társíthatja egy [Azure Load Balancerhoz](../load-balancer/load-balancer-overview.md), ha hozzárendeli a terheléselosztó előtér- **konfigurációjához.** A nyilvános IP-cím elosztott terhelésű IP-cím. 

Hozzárendelhet egy dinamikus vagy egy statikus nyilvános IP-címet egy terheléselosztó előtérrendszerhez. Több nyilvános IP-címet is hozzárendelhet egy terheléselosztó kezelőfelületéhez. Ez a konfiguráció lehetővé teszi a [több-VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) forgatókönyvek, például a több-bérlős környezetek számára a TLS-alapú webhelyeket. 

Az Azure Load Balancer termékváltozataival kapcsolatos további információkért tekintse meg [az Azure Load Balancer standard termékváltozatáról](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szóló cikket.

## <a name="vpn-gateways"></a>VPN-átjárók

Az [azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) egy Azure-beli virtuális hálózatot csatlakoztat a következőhöz:

* Azure-beli virtuális hálózatok
* Helyszíni hálózat (ok). 

A távoli hálózattal való kommunikáció engedélyezéséhez egy nyilvános IP-cím van hozzárendelve a VPN Gatewayhoz. 

* Rendeljen egy **dinamikus** alapszintű nyilvános IP-címet egy VPNGW 1-5 SKU előtér-konfigurációhoz.
* Rendeljen **statikus** szabványos nyilvános IP-címet egy VPNGWAZ 1-5 SKU előtér-konfigurációhoz.

## <a name="application-gateways"></a>Alkalmazásátjárók

A nyilvános IP-címet társíthatja egy [Azure Application Gateway átjáróval](../application-gateway/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ha hozzárendeli az átjáró **előtér**-konfigurációjához. 

* Rendeljen egy **dinamikus** alapszintű nyilvános IP-címet egy Application Gateway v1 kezelőfelületi konfigurációhoz. 
* Rendeljen **statikus** szabványos nyilvános IP-címet egy v2 előtér-konfigurációhoz.

## <a name="azure-firewall"></a>Azure Firewall

[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lehetővé teszi az alkalmazás-és hálózati kapcsolati házirendek létrehozását, betartatását és naplózását az előfizetések és a virtuális hálózatok között.

Csak **statikus** szabványos nyilvános IP-címeket rendelhet hozzá tűzfallal. Ez lehetővé teszi, hogy a tűzfalon kívüli adatok azonosíthassák a virtuális hálózatról származó forgalmat. 


## <a name="at-a-glance"></a>Egy pillantásra

A következő táblázat azt a tulajdonságot mutatja be, amelyen keresztül a nyilvános IP-cím a legfelső szintű erőforráshoz és a lehetséges kiosztási módszerekhez társítható.

| Legfelső szintű erőforrás | IP-cím társítása | Dinamikus | Statikus |
| --- | --- | --- | --- |
| Virtuális gép |Hálózati adapter |Igen |Yes |
| Internetkapcsolattal rendelkező terheléselosztó |Előtér-konfiguráció |Igen |Yes |
| VPN-átjáró |Átjáró IP-konfigurációja |Igen |Nem |
| Alkalmazásátjáró |Előtér-konfiguráció |Igen (csak v1) |Igen (csak v2) |
| Azure Firewall | Előtér-konfiguráció | Nem | Igen|

## <a name="limits"></a>Korlátok

Az IP-címzés korlátai a [hálózatkezelési korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) teljes készletében vannak felsorolva az Azure-ban. 

A korlátok régiónként és előfizetésenként értendőek. [Vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és növelje az alapértelmezett korlátokat az üzleti igényeknek megfelelő maximális korlátig.

## <a name="pricing"></a>Díjszabás

A nyilvános IP-címek kapcsán névleges díjak merülhetnek fel. Ha többet szeretne megtudni az Azure-ban használt IP-címek díjszabásáról, tekintse át az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt.

## <a name="next-steps"></a>Következő lépések
* További információ [Az Azure-beli magánhálózati IP-címekről](private-ip-addresses.md)
* [Statikus nyilvános IP-címmel rendelkező virtuális gép telepítése az Azure Portal használatával](virtual-network-deploy-static-pip-arm-portal.md)
