---
title: Tudnivalók az Azure ExpressRoute Directről
description: Ismerje meg az Azure ExpressRoute Direct főbb funkcióit, valamint azokat az információkat, amelyek a közvetlen ExpressRoute, például az elérhető SKU-hoz és a technikai követelményekhez szükségesek.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: duau
ms.openlocfilehash: 4b129a218f0fe90f632adef1325288b3f8d97d16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585961"
---
# <a name="about-expressroute-direct"></a>Az ExpressRoute Direct ismertetése

A ExpressRoute Direct lehetővé teszi a közvetlen kapcsolódást a Microsoft globális hálózatához a világ különböző pontjain stratégiailag elosztott, egymásra épülő helyeken. A ExpressRoute Direct kettős 100 GB/s-os vagy 10 GB/s közötti kapcsolatot biztosít, amely támogatja az aktív/aktív kapcsolatot a skálán.

A közvetlen ExpressRoute által biztosított főbb funkciók közé tartoznak a következők:

* Nagy mennyiségű adat betöltése az olyan szolgáltatásokba, mint például a Storage és az Cosmos DB
* Fizikai elkülönítés olyan iparágak esetében, amelyek szabályozott és elkülönített kapcsolatot igényelnek, például: banki, kormányzati és kereskedelmi
* A kapcsolatcsoportok elosztásának üzleti egységen alapuló, részletes szabályzása

## <a name="onboard-to-expressroute-direct"></a>Közvetlen ExpressRoute

A ExpressRoute Direct használata előtt először regisztrálnia kell az előfizetését. A regisztráláshoz futtassa a következő parancsokat a Azure PowerShell használatával:

1.  Jelentkezzen be az Azure-ba, és válassza ki a regisztrálni kívánt előfizetést.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

1. Regisztrálja az előfizetését a nyilvános előzetes verzióra az alábbi paranccsal:
1. 
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

A regisztráció után ellenőrizze, hogy a **Microsoft. Network** erőforrás-szolgáltató regisztrálva van-e az előfizetésében. Az erőforrás-szolgáltató regisztrálása konfigurálja az előfizetést az erőforrás-szolgáltatóval való együttműködésre.

1. Nyissa meg az előfizetési beállításokat az [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md)című témakörben leírtak szerint.

1. Az előfizetésben az **erőforrás-szolgáltatóknál** ellenőrizze, hogy a **Microsoft. Network** Provider **regisztrált** állapotot jelenít meg. Ha a Microsoft. Network erőforrás-szolgáltató nem szerepel a regisztrált szolgáltatók listáján, adja hozzá.

Ha megkezdi a ExpressRoute közvetlen használatát, és megfigyelheti, hogy a kiválasztott összevonási helyen nincsenek elérhető portok, a rendszer e-mailt ExpressRouteDirect@microsoft.com küld a további leltár igényléséhez.

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute-szolgáltató és ExpressRoute közvetlen használata

| **ExpressRoute szolgáltató használatával** | **ExpressRoute Direct** | 
| --- | --- |
| Szolgáltatók használatával lehetővé teszi a gyors bevezetést és a meglévő infrastruktúrához való csatlakozást | 100 Gbps/10 GB/s infrastruktúra és az összes réteg teljes felügyeletét igényli
| Integrálható több száz szolgáltatóval, többek között az Ethernettel és a MPLS | Közvetlen/dedikált kapacitás a szabályozott iparágak és a nagy adatfeldolgozás számára |
| Áramköri SKU-i 50 Mbps és 10 GB/s között | Az ügyfél a következő áramköri SKU kombinációját választhatja ki a 100-Gbps ExpressRoute Direct-on: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Az ügyfél a következő áramköri SKU kombinációját választhatja 10 GB/s ExpressRoute Direct-on:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Egyetlen bérlőre optimalizált | Egyetlen bérlőre optimalizált több üzleti egységgel és több munkahelyi környezettel

## <a name="expressroute-direct-circuits"></a>Közvetlen ExpressRoute áramkörök

A Microsoft Azure ExpressRoute lehetővé teszi a helyszíni hálózat kibővítését a Microsoft-felhőbe egy olyan magánhálózati kapcsolaton keresztül, amely egy kapcsolati szolgáltató által könnyebben elérhető. A ExpressRoute használatával kapcsolatokat létesíthet a Microsoft Cloud Services, például a Microsoft Azure és a Microsoft 365 között.

Minden egyes társítási hely rendelkezik hozzáféréssel a Microsoft globális hálózatához, és alapértelmezés szerint bármely régióhoz hozzáférhet egy geopolitikai zónában. Az összes globális régiót prémium szintű áramkörrel érheti el.  

A legtöbb forgatókönyvben szereplő funkciók egyenértékűek azon áramkörökkel, amelyek ExpressRoute-szolgáltatót használnak a működéséhez. A ExpressRoute Direct szolgáltatással elérhető további részletesség és új képességek támogatásához bizonyos kulcsfontosságú képességek állnak rendelkezésre a ExpressRoute Direct-áramkörökben.

## <a name="circuit-skus"></a>Áramköri SKU-i

A ExpressRoute Direct a nagyméretű adatfeldolgozási forgatókönyveket támogatja az Azure Storage-ban és más big data szolgáltatásokban. A ExpressRoute-áramkörök a 100-es ExpressRoute Direct-on mostantól támogatják az **40 Gbps** és a * * 100-Gbps áramköri SKU-t is. A fizikai port pár **100 GB/s vagy 10 GB/** s, és több virtuális áramkör is lehet. Áramköri méretek:

| **100 – Gbps ExpressRoute Direct** | **10 GB/s ExpressRoute Direct** | 
| --- | --- |
| **Előfizetett sávszélesség**: 200 GB/s | **Előfizetett sávszélesség**: 20 GB/s |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Technikai követelmények

* Microsoft Enterprise Edge router (MSEE) interfészek:
    * Kettős 10 Gigabit vagy 100 – Gigabit Ethernet-portok csak az útválasztó párok között
    * Single Mode LR Fiber-kapcsolat
    * IPv4 és IPv6
    * IP-MTU 1500 bájt

* Switch/router 2. réteg/3. rétegbeli kapcsolat:
    * 1 802.1 Q (Dot1Q) vagy két tag 802.1 Q (QinQ) címke beágyazását kell támogatnia
    * EtherType = 0x8100
    * Hozzá kell adnia a külső VLAN-címkét (STAG) a Microsoft által megadott VLAN-azonosító alapján – *csak a QinQ*
    * Egy porton és eszközön több BGP-munkamenetet (VLAN) kell támogatnia
    * IPv4-és IPv6-kapcsolat. *Az IPv6 esetében nem jön létre további alhálózati kapcsolat. Az IPv6-cím hozzá lesz adva a meglévő alkapcsolathoz*. 
    * Opcionális: [kétirányú továbbítási észlelési (BFD)](./expressroute-bfd.md) támogatás, amely alapértelmezés szerint a ExpressRoute-áramkörök összes privát társán konfigurálva van

## <a name="vlan-tagging"></a>VLAN-címkézés

A ExpressRoute Direct a QinQ és a Dot1Q VLAN-címkézést is támogatja.

* A **QINQ VLAN-címkézés** lehetővé teszi az elkülönített útválasztási tartományok ExpressRoute áramköri alapon történő használatát. Az Azure dinamikusan ad egy S-címkét az áramkör létrehozásakor, és nem módosítható. Az áramkörön lévő minden egyes Peer (Private és Microsoft) egy egyedi C-címkét használ a VLAN-hoz. A C-címkének nem kell egyedinek lennie a ExpressRoute közvetlen portjain lévő áramkörökben.

* A **DOT1Q VLAN-címkézés** lehetővé teszi, hogy egyetlen címkézett VLAN legyen a ExpressRoute Direct port pár alapján. A ExpressRoute használt C-címkének egyedinek kell lennie az összes áramkörben és a közvetlen porton belül.

## <a name="workflow"></a>Munkafolyamat

[![munkafolyamat](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>SLA

A ExpressRoute Direct a Microsoft globális hálózatának aktív/aktív redundáns kapcsolataival azonos nagyvállalati szintű SLA-t biztosít. A ExpressRoute-infrastruktúra redundáns és a Microsoft globális hálózattal való kapcsolat redundáns és sokrétű, és megfelelően méretezhető az ügyfelek igényeivel. 

## <a name="next-steps"></a>Következő lépések

[Az ExpressRoute Direct konfigurálása](expressroute-howto-erdirect.md)
