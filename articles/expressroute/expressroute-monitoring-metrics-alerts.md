---
title: 'Azure ExpressRoute: figyelés, mérőszámok és riasztások'
description: Ismerje meg az Azure ExpressRoute-figyelést, metrikákat és riasztásokat a Azure Monitor használatával, amely az Azure-ban az összes mérőszámot, riasztást és diagnosztikai naplót használja.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 01/11/2020
ms.author: duau
ms.openlocfilehash: 7a5da35da35b2f447256bc742681ccd7a7d403da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99091566"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Az ExpressRoute monitorozása, mérőszámai és riasztásai

Ez a cikk segít megérteni a ExpressRoute-figyelést, metrikákat és riasztásokat a Azure Monitor használatával. Azure Monitor az összes Azure-beli mérőszámok, riasztások és diagnosztikai naplók egyikének leállítása.
 
>[!NOTE]
>A **klasszikus metrikák** használata nem ajánlott.
>

## <a name="expressroute-metrics"></a>ExpressRoute metrikák

A **metrikák** megtekintéséhez navigáljon a *Azure monitor* lapra, és kattintson a *metrikák* elemre. Az **ExpressRoute** -metrikák megtekintéséhez a szűrés erőforrás típusú *ExpressRoute-áramkörök* alapján. **Global REACH** mérőszámok megtekintéséhez válassza az erőforrás típusa ExpressRoute- *áramkörök* lehetőséget, és válasszon ki egy Global REACH engedélyezett ExpressRoute-áramköri erőforrást. A **közvetlen ExpressRoute** -metrikák megtekintéséhez *ExpressRoute-portok* alapján szűrheti az erőforrás típusát. 

Ha kijelöl egy mérőszámot, a rendszer az alapértelmezett összesítést alkalmazza. Igény szerint a felosztást is alkalmazhatja, amely a metrikát különböző dimenziókkal jeleníti meg.

### <a name="available-metrics"></a>Elérhető metrikák

|**Metrika**|**Kategória**|**Dimenzió (k)**|**Szolgáltatás (ok)**|
| --- | --- | --- | --- |
|ARP rendelkezésre állása|Rendelkezésre állás|<ui><li>Társ (elsődleges/másodlagos ExpressRoute útválasztó)</ui></li><ui><li> Egyenrangú típus (privát/nyilvános/Microsoft)</ui></li>|ExpressRoute|
|BGP rendelkezésre állása|Rendelkezésre állás|<ui><li> Társ (elsődleges/másodlagos ExpressRoute útválasztó)</ui></li><ui><li> Egyenrangú típus</ui></li>|ExpressRoute|
|BitsInPerSecond|Adatforgalom|<ui><li> Egyenrangúság típusa (ExpressRoute)</ui></li><ui><li>Hivatkozás (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute-átjáró kapcsolatai</ui></li>|
|BitsOutPerSecond|Adatforgalom| <ui><li>Egyenrangúság típusa (ExpressRoute)</ui></li><ui><li> Hivatkozás (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>Közvetlen ExpressRoute</ui></li><ui><li>ExpressRoute-átjáró kapcsolatai</ui></li>|
|Processzor kihasználtsága|Teljesítmény| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network átjáró|
|Másodpercenkénti csomagok száma|Teljesítmény| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network átjáró|
|A társnak hirdetett útvonalak száma |Rendelkezésre állás| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network átjáró|
|A társ által megszerzett útvonalak száma |Rendelkezésre állás| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network átjáró|
|Útvonalak változásának gyakorisága |Rendelkezésre állás| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network átjáró|
|A Virtual Networkban lévő virtuális gépek száma |Rendelkezésre állás| N/A |ExpressRoute Virtual Network átjáró|
|GlobalReachBitsInPerSecond|Adatforgalom|<ui><li>Egyenrangú áramköri Sgomb (szolgáltatás kulcsa)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Adatforgalom|<ui><li>Egyenrangú áramköri Sgomb (szolgáltatás kulcsa)</ui></li>|Global Reach|
|AdminState|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|LineProtocol|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|RxLightLevel|Fizikai kapcsolat|<ui><li>Hivatkozás</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fizikai kapcsolat|<ui><li>Hivatkozás</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>A *GlobalGlobalReachBitsInPerSecond* és a *GlobalGlobalReachBitsOutPerSecond* használata csak akkor látható, ha legalább egy Global REACH-kapcsolatok létrejöttek.
>

## <a name="circuits-metrics"></a>Áramkörök metrikái

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>BITS be-és kifelé – mérőszámok az összes társon belül

A mérőszámokat megtekintheti egy adott ExpressRoute-áramkörön található összes társon.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="áramköri metrikák":::

### <a name="bits-in-and-out---metrics-per-peering"></a>BITS be-és kimenő metrikák

A privát, nyilvános és Microsoft-partnerek mérőszámait BITS/másodpercben tekintheti meg.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metrikák felhasználónként":::

### <a name="bgp-availability---split-by-peer"></a>BGP rendelkezésre állása – megosztás társ szerint  

Megtekintheti közel valós idejű rendelkezésre állását a BGP és a társai között (elsődleges és másodlagos ExpressRoute útválasztók). Ez az irányítópult az elsődleges BGP-munkamenetet jeleníti meg a privát partnereknél, a második BGP-munkamenetet pedig a privát társak számára. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP rendelkezésre állása csoportonként":::

### <a name="arp-availability---split-by-peering"></a>ARP rendelkezésre állása – megosztás társítás szerint  

Megtekintheti a közel valós idejű rendelkezésre állást az [ARP](./expressroute-troubleshooting-arp-resource-manager.md) -ben a különböző társítások és társaik (elsődleges és másodlagos ExpressRoute útválasztók) között. Ez az irányítópult a privát társ-összevonási ARP-munkamenetet jeleníti meg mindkét társon, de a Microsoft társközi szolgáltatásban való kitöltését. A rendszer az alapértelmezett összesítést (átlag) használta mindkét társban.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP rendelkezésre állása egyenrangúként":::

## <a name="expressroute-direct-metrics"></a>Közvetlen ExpressRoute metrikák

### <a name="admin-state---split-by-link"></a>Rendszergazdai állapot – felosztás hivatkozás alapján

Megtekintheti a ExpressRoute Direct port pár hivatkozásának rendszergazdai állapotát.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct rendszergazdai állapot":::

### <a name="bits-in-per-second---split-by-link"></a>Bitek másodpercenként – felosztás hivatkozás alapján

A biteket másodpercenként megtekintheti a ExpressRoute Direct port pár hivatkozásán keresztül.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Közvetlen bitek másodpercenkénti száma":::

### <a name="bits-out-per-second---split-by-link"></a>Bitek másodpercenkénti felosztása hivatkozás alapján

A másodpercenkénti biteket is megtekintheti a ExpressRoute Direct port pár hivatkozásán keresztül.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="ER Direct bitek másodpercenként":::

### <a name="line-protocol---split-by-link"></a>Sor protokoll – felosztás hivatkozás alapján

A ExpressRoute Direct port pár hivatkozásán keresztül megtekintheti a sor protokollját.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct Line protokoll":::

### <a name="rx-light-level---split-by-link"></a>Rx fényszint-felosztás hivatkozás alapján

Megtekintheti az RX-fény szintjét (a ExpressRoute közvetlen portot **fogadó** fény szintjét) az egyes portokhoz. Az egészséges Rx-fény szintje általában a-10 és 0 km közötti tartományba esik

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx fény szintje":::

### <a name="tx-light-level---split-by-link"></a>TX Light Level-felosztás hivatkozás alapján

Megtekintheti a TX-fény szintjét (a ExpressRoute közvetlen port által **közvetített** fény szintjét) az egyes portokhoz. Az egészséges TX-fény szintje általában a-10 és 0 km közötti tartományba esik

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line TX fény szintje":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute Virtual Network átjáró Metrikái

### <a name="cpu-utilization---split-instance"></a>CPU-kihasználtság – példány felosztása

Megtekintheti az átjáró-példányok CPU-kihasználtságát.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="PROCESSZOR felosztása":::

### <a name="packets-per-second---split-by-instance"></a>Csomagok/másodperc – példány szerint felosztva

Az átjárón másodpercenként megtekintheti a csomagokat.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Csomagok másodpercenként – felosztás":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>A társ-felosztással meghirdetett útvonalak száma példány szerint

Megtekintheti a ExpressRoute áramkörnek hirdetett útvonalak számát.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="A társnak hirdetett útvonalak száma":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>A partner által felosztott útvonalak száma példány szerint

Megtekintheti a ExpressRoute áramkörtől kapott útvonalak számát.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="A társ által megszerzett útvonalak száma":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Útvonalak változásának gyakorisága – felosztás példány szerint

Megtekintheti az útvonal változásának gyakoriságát az átjárón.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Útvonalak változásának gyakorisága":::

### <a name="number-of-vms-in-the-virtual-network"></a>A Virtual Networkban lévő virtuális gépek száma

Megtekintheti a virtuális hálózatban lévő virtuális gépek számát.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Virtuális gépek száma a virtuális hálózaton":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute bit/másodpercben

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="átjáró kapcsolatai":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Riasztások a ExpressRoute-átjáró kapcsolataihoz

1. A riasztások konfigurálásához navigáljon **Azure monitor**, majd válassza a **riasztások** lehetőséget.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="riasztások":::
2. Kattintson a **+ cél kiválasztása** elemre, és válassza ki a ExpressRoute-átjáróhoz tartozó kapcsolatok erőforrását.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="cél":::
3. Adja meg a riasztás részleteit.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="műveleti csoport":::
4. Adja meg és adja hozzá a műveleti csoportot.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="műveleti csoport hozzáadása":::

## <a name="alerts-based-on-each-peering"></a>Riasztások az egyes társak alapján

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="minden társ":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Riasztások konfigurálása a tevékenységi naplókhoz az áramkörön

A **riasztási feltételek** között kiválaszthatja a jel típusa **tevékenység naplóját** , és kiválaszthatja a jelet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="Tevékenységnaplók":::

## <a name="additional-metrics-in-log-analytics"></a>További metrikák a Log Analytics

Az ExpressRoute mérőszámait úgy is megtekintheti, hogy a ExpressRoute-áramköri erőforráshoz navigál, és kiválasztja a *naplók* lapot. A lekérdezett mérőszámok esetében a kimenet az alábbi oszlopokat fogja tartalmazni.

|**Oszlop**|**Típus**|**Leírás**|
| --- | --- | --- |
|TimeGrain|sztring|PT1M (a metrikus értékeket percenként küldi el a rendszer)|
|Darabszám|valós szám|Általában egyenlő, mint 2 (minden MSEE percenként egyetlen metrikai értéket küld el)|
|Minimális|valós szám|A két Msee által leküldett két metrikai érték minimuma|
|Maximum|valós szám|A két Msee által leküldett két metrikai érték maximális értéke|
|Átlag|valós szám|Egyenlő (minimum + maximum)/2|
|Összesen|valós szám|Mindkét Msee két metrikai értékének összege (a fő érték, amely a lekérdezett metrikára koncentrál)|
  
## <a name="next-steps"></a>Következő lépések

Az ExpressRoute-kapcsolat konfigurálása.
  
* [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
* [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
* [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
