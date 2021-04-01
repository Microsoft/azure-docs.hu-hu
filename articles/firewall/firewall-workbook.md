---
title: Naplók figyelése Azure Firewall munkafüzet használatával
description: Azure Firewall munkafüzetek rugalmas vásznon biztosítanak a Azure Firewall adatelemzéshez és a részletes vizualizációs jelentések létrehozásához a Azure Portalon belül.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831841"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Naplók figyelése Azure Firewall munkafüzet használatával

Azure Firewall munkafüzet rugalmas vásznon biztosít Azure Firewall adatelemzéshez. A szolgáltatással gazdag vizuális jelentéseket hozhat létre a Azure Portalon belül. Több, az Azure-ban üzembe helyezett tűzfalra koppinthat, és egységesített interaktív élményekben egyesítheti azokat.

Betekintést nyerhet Azure Firewall eseményekbe, megismerheti az alkalmazás-és hálózati szabályokat, és megtekintheti a tűzfalak statisztikáit az URL-címek, portok és címek között. Azure Firewall a munkafüzet lehetővé teszi a tűzfalak és erőforráscsoportok szűrését, és kategóriánként dinamikusan szűrheti az adatkészleteket, ha a naplókban problémát vizsgál. 

## <a name="prerequisites"></a>Előfeltételek

A Kezdés előtt engedélyeznie kell a [diagnosztikai naplózást](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) a Azure Portalon. Emellett olvassa el [Azure Firewall naplókat és mérőszámokat](logs-and-metrics.md) a Azure Firewall számára elérhető diagnosztikai naplók és metrikák áttekintéséhez.

## <a name="get-started"></a>Bevezetés

A munkafüzet üzembe helyezéséhez nyissa meg a [Azure monitor munkafüzetet Azure Firewall](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) és kövesse az oldalon megjelenő utasításokat. Azure Firewall a munkafüzet több-bérlős, többszörös előfizetések közötti együttműködésre lett tervezve, és több tűzfalon szűrhető.

## <a name="overview-page"></a>Áttekintő oldal

Az Áttekintés oldalon a munkaterületek, az idő és a tűzfalak közötti szűrésre van lehetőség. Események időpontját jeleníti meg a tűzfalak és a naplózási típusok (alkalmazás, hálózatok, fenyegetés Intel, DNS-proxy) között.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Azure Firewall munkafüzet áttekintése":::

## <a name="application-rule-log-statistics"></a>Az alkalmazási szabály naplójának statisztikája

Ez az oldal az IP-címek egyedi forrásait jeleníti meg az idő múlásával, az alkalmazási szabály a használatot, a megtagadott/engedélyezett teljes tartománynevet és a szűrt Az Adatszűrés az IP-cím alapján végezhető el.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Azure Firewall munkafüzet-alkalmazási szabály naplója":::

## <a name="network-rule-log-statistics"></a>Hálózati szabály naplójának statisztikája

Ezen az oldalon megtekinthető a szabály szerinti művelet – engedélyezés/megtagadás, a célként megadott port IP-címe és a DNAT idő szerint. Szűrés a művelet, a port és a cél típusa szerint is végezhető.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Azure Firewall munkafüzet hálózati szabályának naplója":::

A naplókat a Time-ablak alapján is szűrheti:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Azure Firewall munkafüzet hálózati szabályának naplózási ideje ablak":::

## <a name="investigations"></a>Vizsgálatokhoz

Megtekintheti a naplókat, és megismerheti az erőforrást a forrás IP-címe alapján. Olyan információkat kaphat, mint például a virtuális gép neve és a hálózati adapter neve. A naplókból egyszerűen szűrheti az erőforrást.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure Firewall munkafüzet vizsgálata":::

## <a name="next-steps"></a>Következő lépések

- További információ a [Azure Firewall Diagnostics](firewall-diagnostics.md) szolgáltatásról
