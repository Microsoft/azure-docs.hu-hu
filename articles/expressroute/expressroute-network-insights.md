---
title: Az Azure ExpressRoute bepillantást nyerhet a hálózati eredmények használatával
description: Ismerje meg az Azure ExpressRoute bepillantást a hálózati információk használatával.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/23/2021
ms.author: duau
ms.openlocfilehash: 7033ea6a1ba6d85f9aa15e14bb9577b2439c59a8
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050490"
---
# <a name="azure-expressroute-insights-using-network-insights"></a>Az Azure ExpressRoute bepillantást nyerhet a hálózati eredmények használatával

Ez a cikk azt ismerteti, hogyan segítheti a hálózati adatok egy helyen történő megtekintését a ExpressRoute metrikáinak és konfigurációinak megtekintéséhez. A hálózati információk használatával megtekintheti a fontos ExpressRoute adatokat tartalmazó topológiai térképeket és állapot-irányítópultokat anélkül, hogy a további telepítést el kellene végeznie.

:::image type="content" source="./media/expressroute-network-insights/monitor-landing-page.png" alt-text="Képernyőkép a ExpressRoute-figyelő kezdőlapján." lightbox="./media/expressroute-network-insights/monitor-landing-page-expanded.png":::

## <a name="visualize-functional-dependencies"></a>Funkcionális függőségek megjelenítése

A megoldás megtekintéséhez navigáljon a *Azure monitor* lapra, válassza a *hálózatok* lehetőséget, majd válassza ki a *ExpressRoute áramköri* kártyát. Ezután válassza ki a megtekinteni kívánt áramkör topológia gombját.

A funkcionális függőségi nézet tiszta képet nyújt a ExpressRoute-telepítőről, amely a különböző ExpressRoute-összetevők (társítások, kapcsolatok, átjárók) közötti kapcsolatot ismerteti.

:::image type="content" source="./media/expressroute-network-insights/topology-view.png" alt-text="A hálózati adatmennyiségek topológiájának nézetét bemutató képernyőkép." lightbox="./media/expressroute-network-insights/topology-view-expanded.png":::

Vigye a kurzort a topológiai Térkép bármelyik összetevőjére a konfigurációs adatok megtekintéséhez. Például vigye az egérmutatót egy ExpressRoute-társítási összetevő fölé, és tekintse meg a részleteket, például az áramköri sávszélességet és a Global Reach engedélyezést.

:::image type="content" source="./media/expressroute-network-insights/topology-hovered.png" alt-text="Képernyőkép az egérmutatóval a topológia nézet erőforrásairól." lightbox="./media/expressroute-network-insights/topology-hovered-expanded.png":::

## <a name="view-a-detailed-and-pre-loaded-metrics-dashboard"></a>Részletes és előre betöltött mérőszámok irányítópultjának megtekintése

Miután áttekintette a ExpressRoute-telepítő topológiáját a funkcionális függőségi nézet használatával, válassza a részletes **mérőszámok megtekintése** lehetőséget a részletes mérőszámok nézetre való átjáráshoz, hogy megértse az áramkör teljesítményét. Ez a nézet a társított erőforrások rendezett listáját, valamint a fontos ExpressRoute-metrikák gazdag irányítópultját kínálja.

A **társított erőforrások** szakasz felsorolja a csatlakoztatott ExpressRoute-átjárókat és a konfigurált társításokat, amelyek kiválasztásával megnyithatja a megfelelő erőforrás-lapot.

:::image type="content" source="./media/expressroute-network-insights/linked-resources.png" alt-text="Képernyőkép a kapcsolódó erőforrásról a figyelő lapon.":::


Az **ExpressRoute mérőszámok** szakasz a **rendelkezésre állás**, az **átviteli sebesség**, a **csomagok** és az **átjáró metrikái** kategóriáinak fontos áramköri mérőszámait tartalmazza.

### <a name="availability"></a>Rendelkezésre állás

A *rendelkezésre állás* lapon nyomon követheti az ARP és a BGP rendelkezésre állását, valamint az áramkör teljes és egyéni kapcsolatának (elsődleges és másodlagos) összegyűjtését. 

:::image type="content" source="./media/expressroute-network-insights/arp-bgp-availability.png" alt-text="Képernyőfelvétel a rendelkezésre állási metrikák diagramjairól." lightbox="./media/expressroute-network-insights/arp-bgp-availability-expanded.png":::

### <a name="throughput"></a>Teljesítmény

Hasonlóképpen, az *átviteli sebesség* lap az áramkör bejövő és kimenő adatforgalmának teljes átviteli sebességét ábrázolja bit/másodpercben. Megtekintheti az egyes kapcsolatok átviteli sebességét és az egyes konfigurált társítási típusokat is.

:::image type="content" source="./media/expressroute-network-insights/throughput.png" alt-text="Képernyőfelvétel az átviteli sebesség mérőszámait bemutató ábráról." lightbox="./media/expressroute-network-insights/throughput-expanded.png":::

### <a name="packet-drops"></a>Csomagvesztések

A *csomagok* eldobása lap az eldobott biteket/másodperceket ábrázolja a bejövő és a kimenő forgalomnak az áramkörön keresztül. Ezen a lapon egyszerűen figyelheti a teljesítménnyel kapcsolatos problémákat, amelyek akkor fordulhatnak elő, ha rendszeresen szüksége van vagy túllépi az áramköri sávszélességet.

:::image type="content" source="./media/expressroute-network-insights/dropped-packets.png" alt-text="Képernyőkép az eldobott csomagok diagramjairól." lightbox="./media/expressroute-network-insights/dropped-packets-expanded.png":::

### <a name="gateway-metrics"></a>Átjárómetrikák

Végül az átjáró Metrikái lap a kijelölt ExpressRoute-átjáró (a társított erőforrások szakaszból) fő mérőszámok diagramjaival töltődik fel. Akkor használja ezt a lapot, ha figyelni szeretné az adott virtuális hálózatokhoz való kapcsolódást.

:::image type="content" source="./media/expressroute-network-insights/gateway-metrics.png" alt-text="Képernyőkép az átjáró teljesítményéről és a CPU-metrikáról." lightbox="./media/expressroute-network-insights/gateway-metrics-expanded.png":::

## <a name="next-steps"></a>Következő lépések

Az ExpressRoute-kapcsolat konfigurálása.
  
* További információ az [Azure ExpressRoute](expressroute-introduction.md), a [hálózati](../azure-monitor/insights/network-insights-overview.md)információkról és a [Network Watcher](../network-watcher/network-watcher-monitoring-overview.md)
* [Kapcsolatcsoport létrehozása és módosítása](expressroute-howto-circuit-arm.md)
* [Társhálózati konfiguráció létrehozása és módosítása](expressroute-howto-routing-arm.md)
* [VNet csatlakoztatása egy ExpressRoute-kapcsolatcsoporthoz](expressroute-howto-linkvnet-arm.md)
* [A metrikák testreszabása](expressroute-monitoring-metrics-alerts.md) és a [kapcsolatok figyelő](../network-watcher/connection-monitor-overview.md) létrehozása
