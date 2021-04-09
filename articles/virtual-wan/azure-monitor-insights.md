---
title: Virtuális WAN figyelése Azure Monitor-adatvizsgálatok használatával
description: Ebből a cikkből megtudhatja, hogyan figyelheti az Azure Virtual WAN-t Azure Monitor információk használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6ead00a0979d81ef11ac81fb13a1abe31317691d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100571325"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Azure Monitor a virtuális WAN-ra vonatkozó bepillantást

Az Azure Virtual WAN [Azure monitor](../azure-monitor/insights/network-insights-overview.md) elemzése lehetővé teszi a felhasználók és a kezelők számára, hogy megtekintsék a virtuális WAN állapotát és állapotát, amely egy automatikusan felderített topológiai Térkép használatával jelenik meg. A Térkép erőforrás állapota és állapota átfedésben van a virtuális WAN általános állapotának pillanatkép-nézetével. Az erőforrásokat a térképen a virtuális WAN-portál erőforrás-konfigurációs oldalain egy kattintással érheti el.

A virtuális WAN erőforrás-szintű metrikák gyűjtése és bemutatása egy előre csomagolt virtuális WAN-metrikai munkafüzettel történik. A munkafüzet a virtuális WAN, a hub, az átjáró és a kapcsolat szintjein jeleníti meg a metrikákat. Ebből a cikkből megtudhatja, hogyan használhatja a virtuális WAN Azure Monitor-bepillantást a virtuális WAN-topológiák és-mérőszámok egyetlen helyen történő megtekintéséhez.

> [!NOTE]
> Az **Áttekintés** menüpont a virtuális WAN-portál **figyelés** területén található. A virtuális WAN-topológia és a metrikák munkafüzetét a hálózatok Azure Monitor használatával is elérheti. További információ: [Azure monitor hálózatok számára](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Előkészületek

A cikkben szereplő lépések végrehajtásához egy vagy több hubhoz kell rendelkeznie egy virtuális WAN-kapcsolattal. Virtuális WAN és hub létrehozásához kövesse az alábbi cikkekben ismertetett lépéseket:

* [Virtuális WAN létrehozása](virtual-wan-site-to-site-portal.md#openvwan)
* [Elosztó létrehozása](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN-topológia megtekintése

Nyissa meg **Azure Portal**  >  **virtuális WAN**-t. A bal oldali ablaktábla **figyelés** menüjében válassza az eredmények **(előzetes verzió)** lehetőséget. Megjelenik **az Áttekintés** nézet. Itt látható a virtuális WAN-függőségi Térkép és a magas szintű **mérőszámok** mini-munkafüzet.

**1. ábra: >-adatellenőrzési menü figyelése**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Képernyőkép, amely az eredmények (előzetes verzió) nézetét jeleníti meg." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

Az Áttekintés **nézetben megtekintheti az automatikusan** felderített virtuális WAN-erőforrásokat. Ezen erőforrások közé tartoznak a hubok, az átjárók, a tűzfalak, a kapcsolatok és a küllős virtuális hálózatok, a harmadik féltől származó NVA és a végpontok közötti virtuális WAN-fiókok. Példa: **2. ábra**.

Az erőforrás állapota és állapota színkódolt, és a Térkép erőforrás-ikonjain látható. A magas szintű virtuális WAN-metrikák, például a hub kapacitása és az átjáró kihasználtsága az ablak jobb oldalán jelenik meg egy mini-munkafüzetben.

**2. ábra: az áttekintések nézete**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Képernyőkép, amely az eredmények nézetét jeleníti meg." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Függőség nézet

A virtuális WAN **függőségi** nézete segít megjeleníteni az összes virtuális WAN-erőforrás összekapcsolt nézetét, amely széles körben egy sugaras architektúrára van szervezve.

**3. ábra: a VWAN függőségi nézete**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="A függőségi nézetet bemutató képernyőkép." lightbox="./media/azure-monitor-insights/dependency-map.png":::

A **függőség** nézet Térkép a következő erőforrásokat jeleníti meg csatlakoztatott gráfként:

* Virtuális WAN-hubok a különböző Azure-régiók között.
* Küllős virtuális hálózatok, amelyek közvetlenül csatlakoznak az elosztóhoz.
* VPN-és Azure ExpressRoute-fiókirodák és P2S-felhasználók, amelyek a megfelelő ExpressRoute, S2S és P2S kapcsolaton keresztül csatlakoznak az egyes központokhoz, valamint virtuális hálózati átjárókkal.
* Az Azure tűzfalak (beleértve a harmadik féltől származó tűzfalakat is) központilag üzembe helyezhetők (biztonságos központ).
* A küllős virtuális hálózatokban üzembe helyezett, harmadik féltől származó NVA (hálózati virtuális berendezések).

A függőségi Térkép az indirekt módon csatlakoztatott virtuális hálózatokat is megjeleníti (a virtuális WAN küllős virtuális hálózatokkal összekapcsolt virtuális hálózatokat).

A függőségi Térkép egyszerű navigálást tesz lehetővé az egyes erőforrások konfigurációs beállításaiban. Például a hub-erőforrás fölé húzva megtekintheti az alapszintű erőforrás-konfigurációt, például a hub-régiót és a hub-előtagot. Kattintson a jobb gombbal a hub-erőforrás Azure Portal oldalának eléréséhez.

**4. ábra: az erőforrás-specifikus információk megkeresése**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Képernyőkép, amely bemutatja, hogyan lehet navigálni az erőforrás-specifikus információkhoz.":::

A **függőség** nézetben található keresési és szűrési sáv egyszerű módszert kínál a diagramon való keresésre. A különböző szűrők segítségével leszűkítheti a keresést egy adott elérési útra és állapotba.

**5. ábra: keresés és szűrés**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="A keresési és a szűrő sávot megjelenítő képernyőkép." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Részletes mérőszámok

A **részletes mérőszámok oldal** megnyitásához válassza a **részletes mérőszámok megtekintése** lehetőséget. A **metrikák** lap egy külön lapokkal előre konfigurált irányítópult. Ezek a lapok betekintést nyújtanak a virtuális WAN-erőforrások kapacitására, teljesítményére és kihasználtságára a virtuális WAN szintjén és a hub szintjén, valamint az egyes kapcsolatok szintjén.

**6. ábra: részletes mérőszámok irányítópultja**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="A részletes mérőszámok irányítópultját bemutató képernyőkép." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Következő lépések

* További információ: [mérőszámok a Azure monitorban](../azure-monitor/essentials/data-platform-metrics.md).
* Az összes virtuális WAN-metrika részletes ismertetését lásd: [virtuális WAN-naplók és-metrikák](logs-metrics.md).
