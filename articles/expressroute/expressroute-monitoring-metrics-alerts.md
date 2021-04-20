---
title: 'Azure ExpressRoute: Monitorozás, metrikák és riasztások'
description: Ismerje meg Azure ExpressRoute, metrikákat és riasztásokat a Azure Monitor használatával, amely egyetlen üzlet az Azure összes metrikához, riasztáshoz és diagnosztikai naplóhoz.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725781"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Az ExpressRoute monitorozása, mérőszámai és riasztásai

Ez a cikk segítséget nyújt az ExpressRoute-monitorozás, a metrikák és a riasztások Azure Monitor. Azure Monitor azure-beli összes metrikát, riasztást és diagnosztikai naplót egyetlen állásban lehet leállítani.
 
>[!NOTE]
>A **klasszikus metrikák** használata nem ajánlott.
>

## <a name="expressroute-metrics"></a>ExpressRoute-metrikák

A **Metrikák megtekintéséhez** lépjen a Azure Monitor lapra, és válassza *a* *Metrikák lehetőséget.* Az **ExpressRoute-metrikák** megtekintéséhez szűrhet az *ExpressRoute-kapcsolatcsoportok erőforrástípusa alapján.* A **metrikák Global Reach** szűrjön az *ExpressRoute-kapcsolatcsoportok* erőforrástípus szerint, és válasszon ki egy olyan ExpressRoute-kapcsolatcsoport-erőforrást, amely Global Reach engedélyezett. Az **erőforrás-ExpressRoute Direct** megtekintéséhez szűrje az *Erőforrástípust ExpressRoute-portok szerint.* 

A metrika kiválasztása után az alapértelmezett összesítés lesz alkalmazva. Másik lehetőségként alkalmazhat felosztást is, amely különböző dimenziókkal mutatja meg a metrikát.

### <a name="aggregation-types"></a>Összesítési típusok:

A Metrikátorkezelő a SUM, MAX, MIN, AVG és COUNT összesítési [típusokat támogatja.](../azure-monitor/essentials/metrics-charts.md#aggregation) Az egyes ExpressRoute-metrikák elemzésének áttekintésekor az ajánlott összesítési típust kell használnia.

* Összeg: Az összesítési intervallum során rögzített összes érték összege. 
* Darabszám: Az összesítési időköz alatt rögzített mérések száma. 
* Átlag: Az összesítési időköz során rögzített metrikaértékek átlaga. 
* Min: Az összesítési időköz alatt rögzített legkisebb érték. 
* Max: Az összesítési időköz során rögzített legnagyobb érték. 

### <a name="available-metrics"></a>Elérhető metrikák

|**Metrika**|**Kategória**|**Dimenzió(k)**|**Funkció(k)**|
| --- | --- | --- | --- |
|ARP rendelkezésre állása|Rendelkezésre állás|<ui><li>Társ (elsődleges/másodlagos ExpressRoute-útválasztó)</ui></li><ui><li> Társviszony-létesítés típusa (privát/nyilvános/Microsoft)</ui></li>|ExpressRoute|
|BGP rendelkezésre állása|Rendelkezésre állás|<ui><li> Társ (elsődleges/másodlagos ExpressRoute-útválasztó)</ui></li><ui><li> Társviszony-létesítés típusa</ui></li>|ExpressRoute|
|BitsInPerSecond (BitsInPerSecond)|Adatforgalom|<ui><li> Társviszony-létesítés típusa (ExpressRoute)</ui></li><ui><li>Hivatkozás (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>ExpressRoute Direct</li><ui><li>ExpressRoute-átjárókapcsolat</ui></li>|
|BitsOutPerSecond|Adatforgalom| <ui><li>Társviszony-létesítés típusa (ExpressRoute)</ui></li><ui><li> Hivatkozás (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>ExpressRoute-átjárókapcsolat</ui></li>|
|Processzor kihasználtsága|Teljesítmény| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network Átjáró|
|Csomagok száma másodpercenként|Teljesítmény| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network Gateway|
|Társviszonyba meghirdetett útvonalak száma |Rendelkezésre állás| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network Gateway|
|A társtól megtanult útvonalak száma |Rendelkezésre állás| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network Gateway|
|Útvonalak gyakoriságának módosítása |Rendelkezésre állás| <ui><li>Példány</ui></li>|ExpressRoute Virtual Network Gateway|
|A virtuális gépek száma a Virtual Network |Rendelkezésre állás| N/A |ExpressRoute Virtual Network Gateway|
|GlobalReachBitsInPerSecond|Adatforgalom|<ui><li>Társviszony-kapcsolat kapcsolatkulcsa (szolgáltatáskulcs)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Adatforgalom|<ui><li>Társviszony-kapcsolat kapcsolatkulcsa (szolgáltatáskulcs)</ui></li>|Global Reach|
|AdminState (Rendszergazdai állam)|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|LineProtocol (Sorösszeg)|Fizikai kapcsolat|Hivatkozás|ExpressRoute Direct|
|RxLightLevel|Fizikai kapcsolat|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
|TxLightLevel|Fizikai kapcsolat|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|ExpressRoute Direct|
>[!NOTE]
>A *GlobalGlobalReachBitsInPerSecond* és *a GlobalGlobalReachBitsOutPerSecond* használata csak akkor lesz látható, ha legalább Global Reach kapcsolat létrejött.
>

## <a name="circuits-metrics"></a>Kapcsolati körök metrikai

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Be- és kifelé mutató bitek – Metrikák az összes társviszonyban

Összesítés típusa: *Átlag*

Megtekintheti egy adott ExpressRoute-kapcsolathálózat összes társviszony-létesítésének metrikákat.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="kapcsolati metrikák":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Be- és kifelé mutató bitek – Társviszonyonkénti metrikák

Összesítés típusa: *Átlag*

A privát, nyilvános és Microsoft társviszony-létesítés metrikákat bit/másodpercben megtekintheti.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metrikák társviszonyonként":::

### <a name="bgp-availability---split-by-peer"></a>BGP rendelkezésre állása – Felosztás társ alapján  

Összesítés típusa: *Átlag*

Megtekintheti a BGP (3. rétegben létesített kapcsolat) közel valós idejű rendelkezésre állását a társviszonyok és társviszonyok (elsődleges és másodlagos ExpressRoute-útválasztók) között. Ezen az irányítópulton az látható, hogy az elsődleges BGP-munkamenet állapota a privát társviszony-létesítéskor be van felé, a második BGP-munkamenet állapota pedig a privát társviszony-létesítésben. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="BGP rendelkezésre állása társonként":::

### <a name="arp-availability---split-by-peering"></a>ARP rendelkezésre állása – Felosztás társviszony-létesítés alapján  

Összesítés típusa: *Átlag*

Megtekintheti az [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (3. réteghez való csatlakozás) közel valós idejű rendelkezésre állását a társviszonyok és társviszonyok (elsődleges és másodlagos ExpressRoute-útválasztók) között. Ezen az irányítópulton látható, hogy a privát társviszony-létesítés ARP-munkamenetének állapota mindkét társon fel van felé, de a Microsoft-társviszony-létesítés mindkét társviszonynál nem. Az alapértelmezett összesítés (átlag) mindkét társnál használatban volt.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="ARP rendelkezésre állása társonként":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct metrikák

### <a name="admin-state---split-by-link"></a>Rendszergazdai állapot – Felosztás hivatkozás alapján

Összesítés típusa: *Átlag*

A portpárok minden hivatkozásának rendszergazdai állapotát ExpressRoute Direct megtekintheti. A Rendszergazdai állapot azt jelzi, hogy a fizikai port be van-e kapcsolva vagy ki van-e kapcsolva. Ez az állapot szükséges ahhoz, hogy a forgalom áthaladjon a ExpressRoute Direct kapcsolaton.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="ER Direct rendszergazdai állapota":::

### <a name="bits-in-per-second---split-by-link"></a>Bitek száma másodpercben – Felosztás hivatkozás alapján

Összesítés típusa: *Átlag*

A bitek másodpercenkénti száma a portpár mindkét ExpressRoute Direct megtekinthető. Figyelje ezt az irányítópultot mindkét hivatkozás bejövő sávszélességének összehasonlításához.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="ER Direct bitek másodpercenként":::

### <a name="bits-out-per-second---split-by-link"></a>Bitek száma másodpercenként – Felosztás hivatkozás alapján

Összesítés típusa: *Átlag*

A bitek másodpercenkénti száma a portpár mindkét ExpressRoute Direct megtekinthető. Figyelje ezt az irányítópultot mindkét hivatkozás kimenő sávszélességének összehasonlításához.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Er Direct bitek száma másodpercenként":::

### <a name="line-protocol---split-by-link"></a>Line Protocol – Felosztás hivatkozás alapján

Összesítés típusa: *Átlag*

A vonal protokollját a portpárok minden egyes hivatkozásán ExpressRoute Direct megtekintheti. A Line Protocol jelzi, hogy a fizikai kapcsolat működik-e a ExpressRoute Direct. Figyelje ezt az irányítópultot, és állítson be riasztásokat, hogy tudja, mikor lépett le a fizikai kapcsolat.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="ER Direct Line protokoll":::

### <a name="rx-light-level---split-by-link"></a>Rx világos szintje – Felosztás hivatkozás alapján

Összesítés típusa: *Átlag*

Az egyes portok Rx-fényszintje (a port által ExpressRoute Direct világos **szintje)** megtekinthető. A kifogástalan RX-fényszintek általában -10 dBm és 0 dBm között esnek. Állítson be riasztásokat, amelyek értesítést küldnek, ha az Rx fényszintje a kifogástalan tartományon kívülre esik.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx Light Level":::

### <a name="tx-light-level---split-by-link"></a>Tx világosszint – Felosztás hivatkozás alapján

Összesítés típusa: *Átlag*

Az egyes portok Tx-fényszintje (az a fényszint, ExpressRoute Direct port **továbbítva** van). A kifogástalan Tx-fényszintek általában -10 dBm és 0 dBm között esnek. Állítson be riasztásokat, amelyek értesítést küldnek, ha a Tx-fényszint a kifogástalan tartományon kívülre esik.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line Tx Light Level":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>ExpressRoute Virtual Network átjáró metrika

Összesítés típusa: *Átlag*

ExpressRoute-átjáró üzembe helyezésekor az Azure kezeli az átjáró számítási feladatait és funkcióit. Hat átjárómetrika érhető el az átjáró teljesítményének jobb átfogyása érdekében:

* Processzor kihasználtsága
* Csomagok másodpercenként
* A társviszonyok számára meghirdetett útvonalak száma
* A társviszonyok által megtanult útvonalak száma
* Útvonalak változásának gyakorisága
* Virtuális gépek száma a virtuális hálózaton  

Javasoljuk, hogy állítson be riasztásokat az egyes metrikákhoz, hogy tisztában legyen azzal, hogy az átjáró mikor láthat teljesítménybeli problémákat.

### <a name="cpu-utilization---split-instance"></a>CPU-kihasználtság – Példány felosztása

Összesítés típusa: *Átlag*

Megtekintheti az egyes átjárópéldányok CPU-kihasználtságát. A cpu-kihasználtság rövid időre megugrhat a gazdagép rutinszerű karbantartása során, de a magas cpu-kihasználtság hosszabb ideig azt jelezheti, hogy az átjáró teljesítménybeli szűk keresztmetszetet ér el. Az ExpressRoute-átjáró méretének növelése megoldhatja a problémát. Állítson be egy riasztást arról, hogy milyen gyakran lépi túl a CPU-kihasználtság egy adott küszöbértéket.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Képernyőkép a CPU-kihasználtságról – metrikák felosztása.":::

### <a name="packets-per-second---split-by-instance"></a>Csomagok másodpercenként – Felosztás példányok szerint

Összesítés típusa: *Átlag*

Ez a metrika az ExpressRoute-átjárón áthaladó bejövő csomagok számát rögzíti. Ha az átjáró forgalmat fogad a helyszíni hálózatról, itt egy konzisztens adatstreamet kell látnia. Állítson be egy riasztást, ha a csomagok másodpercenkénti száma egy küszöbérték alá csökken, ami azt jelzi, hogy az átjáró már nem fogad forgalmat.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Csomagok másodpercenkénti képernyőképe – metrikák felosztása.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Társviszonyba meghirdetett útvonalak száma – Felosztás példány szerint

Összesítés típusa: *Darabszám*

Ez a metrika azon útvonalak száma, amelyekre az ExpressRoute-átjáró a kapcsolatkörnek hirdet. A címterek olyan virtuális hálózatokat tartalmazhatnak, amelyek virtuális társhálózat-létesítés használatával kapcsolódnak, és távoli ExpressRoute-átjárót használ. Az útvonalak számának konzisztensnek kell maradnia, kivéve, ha a virtuális hálózati címterek gyakran módosulnak. Állítson be egy riasztást, ha a meghirdetett útvonalak száma a megadott virtuális hálózat címterének küszöbértéke alá csökken.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Képernyőkép a társviszonyba meghirdetett útvonalak számról.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Társviszonyból megtanult útvonalak száma – Felosztás példány szerint

Összesítés típusa: *Maximum*

Ez a metrika azt mutatja, hogy az ExpressRoute-átjáró hány útvonalat tanul az ExpressRoute-kapcsolathálózathoz csatlakoztatott társtól. Ezek az útvonalak egy másik, ugyanhoz a kapcsolathoz csatlakoztatott vagy a helyszínen megtanult virtuális hálózatról is lekért útvonalak. Állítson be egy riasztást, ha a megtanult útvonalak száma egy bizonyos küszöbérték alá csökken. Ez azt jelezheti, hogy az átjáró teljesítménybeli problémát lát, vagy a távoli társviszonyok már nem hirdetnek útvonalakat az ExpressRoute-kapcsolathálózathoz. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Képernyőkép a társtól megtanult útvonalak számról.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Útvonalak változásának gyakorisága – Felosztás példány szerint

Összesítés típusa: *Összeg*

Ez a metrika a távoli társviszonyok számára megtanult vagy meghirdetett útvonalak gyakoriságát mutatja. Először vizsgálja meg a helyszíni eszközöket, hogy megértse, miért változik ilyen gyakran a hálózat. Az útvonalak nagy gyakorisággal való módosítása teljesítménybeli problémát jelezhet az ExpressRoute-átjárón, ahol az átjáró termékváltozatának felméretezése megoldhatja a problémát. Állítson be egy riasztást egy gyakorisági küszöbértékhez, amely figyelembe vegye, ha az ExpressRoute-átjáró rendellenes útvonal-módosításokat észlel.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Képernyőkép az útvonalak módosított metrika gyakoriságáról.":::

### <a name="number-of-vms-in-the-virtual-network"></a>A virtuális gépek száma a Virtual Network

Összesítés típusa: *Maximum*

Ez a metrika az ExpressRoute-átjárót használó virtuális gépek számát mutatja. A virtuális gépek száma olyan virtuális társhálózatok virtuális gépeit is tartalmazhatja, amelyek ugyanazt az ExpressRoute-átjárót használják. Állítson be riasztást ehhez a metrikhoz, ha a virtuális gépek száma egy bizonyos küszöbérték fölé kerül, ami hatással lehet az átjáró teljesítményére. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Képernyőkép a virtuális hálózat metrikában lévő virtuális gépek számról.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>ExpressRoute-átjárókapcsolatok bit/másodpercben

Összesítés típusa: *Átlag*

Ez a metrika az ExpressRoute-kapcsolatokkal létesített adott kapcsolat sávszélesség-használatát mutatja.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Az átjárókapcsolat sávszélesség-használati metrikának képernyőképe.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>ExpressRoute-átjárókapcsolatokkal kapcsolatos riasztások

1. A riasztások konfiguráláshoz lépjen a **Azure Monitor,** majd válassza a **Riasztások lehetőséget.**

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="riasztások":::
2. Válassza **a +Cél kiválasztása lehetőséget,** majd válassza ki az ExpressRoute-átjárókapcsolat erőforrást.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Cél":::
3. Adja meg a riasztás részleteit.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="műveletcsoport":::
4. Definiálja és adja hozzá a műveletcsoportot.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="műveletcsoport hozzáadása":::

## <a name="alerts-based-on-each-peering"></a>Riasztások az egyes társviszony-létesítés alapján

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="minden társviszony-létesítés":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Tevékenységnaplók riasztásának konfigurálása kapcsolati körökön

A **Riasztási feltételek között** kiválaszthatja **a** Tevékenységnapló lehetőséget a Jel típusa mezőben, majd a Jelet.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="tevékenységnaplók":::

## <a name="more-metrics-in-log-analytics"></a>További metrikák a Log Analyticsben

Az ExpressRoute-metrikák megtekintéséhez navigálhat az ExpressRoute-kapcsolatcsoport erőforrására, majd a Naplók *lapra.* A lekérdezhető metrikák kimenete az alábbi oszlopokat fogja tartalmazni.

|**Oszlop**|**Típus**|**Leírás**|
| --- | --- | --- |
|TimeGrain|sztring|PT1M (a metrikaértékek percenként lekért értékek)|
|Darabszám|valós szám|Általában 2-nek kell lennie (minden MSEE percenként egyetlen metrikaértéket ad le)|
|Minimális|valós szám|A két MSEE által lekért két metrikaérték minimuma|
|Maximum|valós szám|A két MSEE által lekért két metrikaérték maximuma|
|Átlag|valós szám|Egyenlő (minimum + maximum)/2|
|Összesen|valós szám|A két MSEE két metrikaértékének összege (a lekérdezett metrika fő értéke)|
  
## <a name="next-steps"></a>Következő lépések

Az ExpressRoute-kapcsolat konfigurálása.
  
* [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
* [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
* [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
